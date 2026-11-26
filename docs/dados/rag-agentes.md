# Contrato para RAG e agentes

O que a base entrega para a avaliação do RAG e para os agentes do `ia`, e como consultar. Tudo vem do schema `mira` (ou de um `mira_exp_<nome>`), apenas para leitura.

## Checklist de prontidão

| Item | Estado |
| --- | --- |
| `relatorio_dq.json` aprovado e **nenhum** trecho acima da janela do embedding | Pronto: 0 de 2.424 (tokens reais; o Ollama aceita todos com `truncate: false`) |
| Índices GIN (`tsv_tokens`, `tsv_portugues`, `pg_trgm`) + HNSW, `base_sha256` em `metadados` | Pronto: migrações `0001` + `0002` |
| Metadados de filtro (`tema`, `relevancia`, `publico_alvo`, `condicao_saude`) nos trechos | Pronto, com curadoria `proposto_agente`; falta a revisão da equipe |
| Ablações isoladas (`embeddings.modelo`, `chunking.max_tokens`, `mira_exp_<nome>`) | Pronto |
| Cobertura de medidas além de 26,8 % e ligações TACO↔IBGE revisadas | Pendente: revisão da equipe |
| Conjunto de avaliação com 40 refeições ou mais, com parcela cega, e juiz validado | Pendente, no repositório `ia` (nunca no `mira-dados`: regra fixa 4) |

## Busca documental com filtro de público

`publico_alvo` e `condicao_saude` estão em cada linha de `trechos_ampliados`, então o filtro entra direto na busca vetorial ou híbrida, sem join. Exemplo para um adulto com diabetes, sem trechos infantis, de gestantes nem de manuais para profissionais:

```sql
SELECT trecho_id, documento_id, capitulo, secao, pagina_inicio, pagina_fim, texto
FROM mira.trechos_ampliados
WHERE publico_alvo IN ('geral', 'adultos')
  AND (condicao_saude IS NULL OR condicao_saude = ANY(%(condicoes)s))   -- ex.: ARRAY['diabetes']
ORDER BY embedding <=> %(vetor)s::vector
LIMIT 5;
```

A distribuição dos trechos por público e condição está em [Curadoria](curadoria.md#publico-alvo-dos-trechos).

!!! note "Filtro restritivo e HNSW"
    O HNSW filtra depois de achar os vizinhos (`ef_search` = 40 por padrão). Com filtro muito seletivo (por exemplo, `gestantes`, com 25 trechos), aumente `SET hnsw.ef_search` ou ligue `hnsw.iterative_scan` (pgvector 0.8 ou superior) para não voltar menos linhas que o `LIMIT`.

`publico_alvo = 'profissionais_saude'` marca manuais, políticas e instrumentos (PNAN, vigilância, marcadores, terapia hospitalar): úteis para análise, mas não são orientação direta ao usuário.

## Consultas para agentes

Cada agente lê só o seu domínio. As consultas abaixo foram conferidas contra um schema publicado.

=== "Alimento (autocomplete)"

    Trigramas em `alimentos.nome` e `sinonimos.termo`, sem LLM. Pega sinônimos regionais (`macaxeira` → mandioca):

    ```sql
    SELECT alimento_id, nome, max(sim) AS sim FROM (
        SELECT alimento_id, nome, similarity(nome, %(t)s) AS sim FROM mira.alimentos WHERE nome % %(t)s
        UNION ALL
        SELECT a.alimento_id, a.nome, similarity(s.termo, %(t)s)
        FROM mira.sinonimos s JOIN mira.alimentos a USING (alimento_id) WHERE s.termo % %(t)s
    ) x GROUP BY 1, 2 ORDER BY sim DESC LIMIT 5;
    ```

    Com 597 alimentos, o planejador prefere varredura sequencial; o índice GIN passa a valer com uma base maior (TBCA).

=== "Medidas e grupo do Guia"

    ```sql
    SELECT a.nome, m.medida, m.gramas, g.grupo_guia, g.processamento
    FROM mira.alimentos a
    LEFT JOIN mira.medidas m USING (alimento_id)
    LEFT JOIN mira.grupos_guia g USING (alimento_id)
    WHERE a.alimento_id = %(id)s
    ORDER BY m.id;
    ```

=== "Regras oficiais"

    15 regras do Guia (4 recomendações, regra de ouro e Dez passos), cada uma conferida **literalmente** contra o trecho de onde foi transcrita. O agente cita a regra e abre o trecho inteiro para contexto:

    ```sql
    SELECT r.regra_id, r.tipo, r.texto_oficial, r.evidencia_fonte, t.texto
    FROM mira.regras_oficiais r JOIN mira.trechos_ampliados t USING (trecho_id)
    WHERE r.tipo = 'passo'
    ORDER BY r.regra_id;
    ```

=== "Produto (código de barras)"

    ```sql
    SELECT nome, marca, nova_grupo, base_valores, kcal, acucares_g, sodio_mg, dq_alertas
    FROM mira.produtos_ean WHERE gtin = %(gtin)s;
    ```

    `nova_grupo` é nulo em 19.757 de 26.389 produtos: o Open Food Facts não classificou, e o ETL não infere.

## Governança dos campos novos

`publico_documentos.csv` e as 15 linhas de `regras_oficiais.csv` estão como `proposto_agente` e entram em desenvolvimento (`curadoria.status_permitidos` com os três status). No perfil de produção (`[aprovado_equipe]`), público e condição ficam nulos e as regras não revisadas não são publicadas, até a equipe revisar. O filtro do agente precisa tratar `publico_alvo IS NULL` como "sem informação", não como "geral".

## Ablação de corte ou de modelo

```bash
# params.yaml: chunking.max_tokens: 384   (ou outro embeddings.modelo + o tokenizer dele em chunking.tokenizer)
dvc repro
python -m mira_dados.publicar_postgres --fase all --schema mira_exp_chunk384   # sem swap: o mira fica intacto
```

`metadados.chunking_max_tokens` registra o teto usado em cada schema. Para outro modelo de embeddings com dimensão diferente, a migração aceita `-x embedding_dim=1024` (veja [Publicação](publicacao.md#migracoes-alembic)).

**Fontes:** `alembic/versions/0002_filtros_rag_regras_trgm.py`, `src/mira_dados/gold/catalogo.py` e `src/mira_dados/gold/regras_oficiais.py`, no repositório `data`.
