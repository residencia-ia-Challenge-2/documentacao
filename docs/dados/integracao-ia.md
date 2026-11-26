# Integração com o `ia`

O repositório `ia` só **lê** o schema `mira`. O contrato entre os dois repositórios tem quatro partes, e todas são verificadas.

| Contrato | Como o `mira-dados` cumpre | Verificação |
| --- | --- | --- |
| **Mesmos Parquets** | ETL portado linha a linha, com as mesmas versões de bibliotecas | Byte a byte idênticos com `base.ampliada` `false` e `true` (com `chunking.max_tokens: null`) |
| **Mesmo `base_sha256`** | `qualidade.hash_base()` usa o algoritmo do `ia` sobre os mesmos 6 Parquets | `RepositorioPostgres(verificar_base=True)` do `ia` aceitou o banco |
| **Mesmas tabelas e colunas** | Migração `0001` com o DDL que o `ia` criava, incluindo a tabela legada `trechos` | Busca, medidas, trechos e grupos lidos pelo código do `ia`, sem mudança |
| **Mesmos vetores** | Mesmo texto indexado, mesmo modelo e mesma chave de cache | Cache do `ia` acertado para os 147 trechos do Guia e os 2.394 da base ampliada |

## Prova executada

Código do `ia`, sem alteração, contra o banco publicado pelo `mira-dados`:

```text
base_sha256 aceito pelo ia: True
'macaxeira cozida'        → taco_129 Mandioca, cozida
'feijão'                  → taco_561 Feijão, carioca, cozido
'filé de frango grelhado' → taco_410 Frango, peito, sem pele, grelhado
guia(trechos)             → guia_067, guia_068
ampliada                  → cab36_0003, cab38_0105, cab36_0001, guia_069
```

## Suíte de integração do `ia`

`test_v4_banco.py`, `test_v5_orientacao.py` e `test_documentos.py` do `ia`, rodados com `DATABASE_URL` apontando para o PostgreSQL do `mira-dados` (nenhum arquivo do `ia` alterado): **24 de 24 passam**. Esses testes usam o carregador do próprio `ia` em schemas de teste, então provam que o banco (pgvector, `unaccent`, extensões) serve ao `ia`. A leitura do schema `mira` publicado pela DAG foi conferida à parte com o `RepositorioPostgres` do `ia`: `base_sha256` aceito, buscas e orientação ampliada funcionando.

## O que ainda acopla o `ia` ao disco

Hoje o `ia` confere o `base_sha256` lendo `data/processed/*.parquet` **do próprio disco**, e a API carrega a base do disco. Para separar de vez os repositórios:

- **Transição:** `dvc pull` no `ia`, apontando para o mesmo remote S3 do `mira-dados`.
- **Definitivo:** o `ia` lê tudo do PostgreSQL e valida só `dq_aprovado = true` e `versao_schema` em `mira.metadados`.

!!! warning "Parquets do `ia` desatualizados"
    O corte por tokens mudou `guia_trechos` e `documentos_trechos` (de 2.394 para 2.424 trechos), então o `base_sha256` mudou. O `ia` precisa dos Parquets novos (`dvc pull`) para o `RepositorioPostgres(verificar_base=True)` aceitar o banco. Com `chunking.max_tokens: null`, o `mira-dados` volta a produzir a base anterior, byte a byte.

## Mudanças só por acréscimo

- Nenhum `trecho_id` antigo mudou. Os 30 trechos longos agora começam igual e terminam antes; o resto foi para ids novos (`guia_148`, `cab36_0177`…). O `eval/queries.jsonl` do `ia` não cita trechos.
- Colunas e tabelas novas (`publico_alvo`, `condicao_saude`, `regras_oficiais`, `produtos_ean`) são só acréscimo: a leitura atual do `ia` continua funcionando, e os testes de publicação garantem isso.
- A tabela `produtos_ean` ainda não é consumida pelo `ia` nem pelo `backend`, que consulta a API do Open Food Facts ao vivo.

**Fontes:** `src/mira_dados/qualidade.py` e `scripts/comparar_invariancia.py` no repositório `data`; testes de integração do repositório `ia`.
