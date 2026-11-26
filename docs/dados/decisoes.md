# Decisões de engenharia de dados

Registro das decisões tomadas na construção do repositório `data` (`mira-dados`), com o motivo. As decisões herdadas do `ia` mantêm o número do ADR original; as decisões gerais do projeto estão em [Regras fixas e ADRs](../architecture/decisoes.md).

A coluna **Fase** indica quando a decisão foi tomada: Fases 1 a 3 do [Roadmap](roadmap.md), **Expansão** (entrada do Open Food Facts) e **RAG** (prontidão para RAG e agentes).

| # | Decisão | Motivo | Fase |
| --- | --- | --- | --- |
| D01 | Port linha a linha do ETL do `ia`, com as mesmas versões de pandas/pyarrow/pymupdf | Garantir Parquets byte a byte idênticos e provar a migração sem regressão | 1 |
| D02 | Curadoria unificada (um CSV por domínio) com `escopo` em vez de pares `*_mvp`/`*_ampliacao` | Uma fonte de verdade por tipo; `base.ampliada` vira só um filtro | 1 |
| D03 | Ordem das linhas (`mvp` antes de `ampliacao`) é contrato testado | O ETL deduplica com `keep="first"` | 1 |
| D04 | `status` novo derivado da observação (`a revisar` → `a_revisar`, senão `proposto_agente`); nenhum `aprovado_equipe` inventado | Sem nutricionista, aprovação só pela equipe | 1 |
| D05 | `evidencia_fonte` calculada do próprio ETL do Guia (capítulo, seção, páginas impressas) | Âncora verificável em vez de citação manual | 1 |
| D06 | Open Food Facts pelo Parquet do Hugging Face, num commit fixo, e não pelo JSONL diário | O JSONL é regerado na mesma URL; o commit é imutável e tem SHA-256 publicado | Expansão |
| D07 | Bronze do OFF = recorte Brasil determinístico, com lista de permissão de colunas | ~8 GB → 4,3 MB; LGPD (sem contribuidores) | Expansão |
| D08 | OFF em tabela própria (`produtos_ean`) | Isola a base ODbL (share-alike) da TACO/IBGE | Expansão |
| D09 | Produto ruim é rejeitado com motivo, nunca corrigido; kcal ausente não é recalculada | Regras fixas 1 e 2 | Expansão |
| D10 | "Tabela por porção" do OFF é alerta, não bloqueio; `base_valores` para 100 ml | O OFF já converte para 100 g; bebidas precisam da unidade certa | Expansão |
| D11 | `base_sha256` calculado sobre os mesmos 6 Parquets do `ia` | O `ia` aceita o banco sem mudança de código | 2 |
| D12 | Todo o DDL (inclusive GIN/HNSW) na migração Alembic; carga só insere | Uma fonte de verdade do schema; volume pequeno não justifica criar índice depois | 2 |
| D13 | `alembic_version` dentro de cada schema | A versão do DDL viaja junto no swap | 2 |
| D14 | `trechos` como tabela real, não view | O texto vetorizado difere do de `trechos_ampliados` | 2 |
| D15 | Relatório de DQ sem data/hora e amarrado aos Parquets por hash | Determinismo; publicação recusa dado alterado depois do DQ | 2 |
| D16 | Whitelist de nomes de schema (`mira`, `mira_staging`, `mira_anterior`, `mira_exp_*`) | O nome entra em SQL dinâmico (DDL não aceita parâmetro) | 2 |
| D17 | Swap e rollback com `pg_advisory_xact_lock` | Duas publicações simultâneas não se atropelam | 2 |
| D18 | Rollback é troca simétrica `mira` ↔ `mira_anterior` | Rodar de novo desfaz; nada é apagado no rollback | 2 |
| D19 | Experimentos em `mira_exp_*` sem swap | Ablação do Cientista nunca vira produção por engano | 2 |
| D20 | `exigido_mvp` fica no Parquet, mas não vai para o banco | O DDL do `ia` não tem a coluna, e ela nasce do conjunto de avaliação (regra fixa 4) | 2 |
| D21 | SeaweedFS 4.52 no lugar do MinIO como S3 local (decidido com o usuário) | As imagens do MinIO comunitário deixaram de ser publicadas; produção segue no Amazon S3 | 3 |
| D22 | Airflow 3.3.2, não 3.4.0 | A 3.4.0 tinha 11 dias; preferida a última 3.3.x estabilizada | 3 |
| D23 | ETL num venv separado dentro da imagem do Airflow, fixado por `requirements.lock` | O Airflow e o ETL exigem versões diferentes de SQLAlchemy/Alembic | 3 |
| D24 | Task `alembic_upgrade` = `publicar_postgres --fase migrar` (só o staging) | `alembic upgrade` direto no `mira` criaria um schema ativo vazio na primeira execução | 3 |
| D25 | Credenciais do remote DVC só em `.dvc/config.local`; `.dvc/config` igual em dev e prod | Nada de segredo no Git; prod usa IAM role | 3 |
| D26 | Postgres local na porta 5433 | Convive com o `mira-postgres` do repo `ia` na 5432 | 3 |
| D27 | Corte por tokens com o tokenizer do modelo como Bronze (SHA-256, commit fixado), não pelo Ollama | O ETL e a CI não dependem do Ollama; a contagem é igual ao `prompt_eval_count` dele | RAG |
| D28 | Partes do corte por tokens recebem números depois do último do documento | Nenhum `trecho_id` existente muda; o padrão `<doc>_<número>` do `ia` continua valendo | RAG |
| D29 | `publico_alvo`/`condicao_saude` em curadoria própria (`proposto_agente`), não em `documentos.csv` | As linhas do catálogo já são `aprovado_equipe`; um campo novo precisa da própria revisão | RAG |
| D30 | Filtros desnormalizados em `trechos_ampliados` | Entram direto no `WHERE` da busca híbrida, sem join | RAG |
| D31 | `pg_trgm` criado `WITH SCHEMA public` | O usuário é `mira`: sem schema explícito, a extensão iria para o `mira` ativo, que o swap descarta com CASCADE | RAG |

## Pendências de decisão

- Restringir `curadoria.status_permitidos` a `aprovado_equipe` em produção (depende da revisão da equipe).
- Remover `exigido_por` da curadoria ou mantê-lo só como metadado de desenvolvimento.
- Regra documentada para NOVA nos produtos do OFF, ou manter nulo.

**Fontes:** histórico de commits e pull requests do repositório `data`; [Blueprint de dados](../sobre/blueprint-dados.md).
