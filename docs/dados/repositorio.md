# Estrutura do repositório `data`

Organização do repositório [`data`](https://github.com/residencia-ia-Challenge-2/data) (`mira-dados`). A documentação dele fica neste portal, não no próprio repositório.

```text
mira-dados/
├── .github/workflows/ci.yml       # ruff + pytest (Postgres de serviço) + DagBag + terraform
├── alembic/                       # DDL versionado do serving
│   ├── env.py                     # schema alvo parametrizável (mira | mira_staging | mira_exp_*)
│   └── versions/0001_schema_inicial.py, 0002_filtros_rag_regras_trgm.py
├── cache/embeddings/              # vetores por hash (fora do Git; semeado do repo ia)
├── data/
│   ├── raw/                       # BRONZE: *.dvc + FONTES.md (binários nunca no Git)
│   ├── interim/                   # SILVER: taco, ibge_medidas, ligacoes_sugeridas, off_produtos
│   ├── curadoria/                 # HUMANO (Git): nutricional/ e especialista/
│   └── processed/                 # GOLD: Parquets + relatorio_base/off/dq.json
├── infra/terraform/               # S3 (data lake), RDS PostgreSQL 17, ECR
├── orchestration/
│   ├── Dockerfile                 # Airflow 3.3.2 + venv do ETL (requirements.lock)
│   ├── dags/mira_ciclo_dados.py   # só orquestra: python -m mira_dados.*
│   └── postgres/init.sql          # bancos auxiliares: mira_dados_teste, airflow
├── scripts/
│   ├── migrar_curadoria_ia.py     # migração única da curadoria do ia (Fase 1)
│   └── comparar_invariancia.py    # prova byte a byte contra os Parquets do ia
├── src/mira_dados/
│   ├── caminhos.py · params.py · banco.py
│   ├── contratos.py               # Pydantic + Pandera
│   ├── curadoria.py               # leitura com filtro de escopo/status
│   ├── unidades.py · texto.py     # normalização e texto indexado (idênticos ao ia)
│   ├── embeddings.py              # Ollama + falso, cache compatível com o ia
│   ├── ingerir.py · ingerir_off.py
│   ├── silver/                    # taco · medidas · ligacoes · off
│   ├── tokens.py                  # contagem de tokens reais (tokenizer do modelo)
│   ├── gold/                      # base_nutricional · grupos_guia · guia · documentos · catalogo · regras_oficiais · produtos_ean
│   ├── qualidade.py               # Data Quality Gate
│   └── publicar_postgres.py       # staging → smoke → swap → rollback
├── tests/                         # sem rede; Postgres e Airflow opcionais
├── alembic.ini · dvc.yaml · dvc.lock · params.yaml · pyproject.toml
├── docker-compose.yml · Makefile · requirements.lock
└── .env.example
```

## O que vai para o Git

| Vai | Não vai |
| --- | --- |
| Código, testes, `dvc.yaml`, `dvc.lock`, `params.yaml` | Binários de `data/raw/` (só os `.dvc`) |
| `data/curadoria/**` e `data/raw/FONTES.md` | Parquets de `interim/` e `processed/` (cache do DVC) |
| Métricas `relatorio_base.json`, `relatorio_off.json`, `relatorio_dq.json` | `cache/`, `.env`, `.venv/` |

Os comandos do dia a dia estão no guia [Dados e DVC](../guides/dados-dvc.md).
