# Infraestrutura de dados

Todo componente do `mira-dados` roda localmente com Docker e Python, e tem um equivalente direto na AWS, sem reescrever o pipeline.

## Stack local e AWS

| Camada | Local (dev) | AWS (prod) | Situação |
| --- | --- | --- | --- |
| Object storage (data lake) | SeaweedFS (Docker, API S3) | Amazon S3 | Pronto |
| Versionamento de dados | DVC 3 (`*.dvc`, `dvc.lock`), remote `s3://mira-dados/dvc` | DVC → S3 | Pronto |
| ETL | Python 3.11+, pandas, PyArrow, DuckDB | ECS Task ou AWS Batch | Pronto |
| Contratos e Data Quality | Pydantic v2 + Pandera | Mesmo container | Pronto |
| Leitura de PDFs | PyMuPDF | Mesmo container | Pronto |
| Serving | PostgreSQL 17 + pgvector (Docker) | RDS ou Aurora PostgreSQL | Pronto |
| Migrações | Alembic (schema parametrizável) | Mesmo container contra o RDS | Pronto |
| Embeddings | Ollama `granite-embedding:278m` | Container Ollama ou Bedrock | Pronto |
| Orquestração | Apache Airflow 3.3.2 (LocalExecutor) | Amazon MWAA | Local pronto; MWAA fora do Terraform |
| CI e IaC | GitHub Actions + Terraform | ECR + S3 + RDS + MWAA | Workflow validado localmente; ainda não executado no GitHub |
| Documentação | MkDocs Material | GitHub Pages | Pronto |

!!! info "Por que SeaweedFS e não MinIO"
    O projeto previa MinIO, mas as imagens da edição comunitária deixaram de ser publicadas. O SeaweedFS (Apache-2.0) cumpre o mesmo papel com API S3. Em produção o destino continua sendo o Amazon S3.

### Versões fixadas

Onde a saída depende da biblioteca, as versões são as mesmas do `ia`, o que garante Parquets idênticos byte a byte: `pandas 3.1.1`, `pyarrow 26.0.1`, `pymupdf 2.1`, `pydantic 2.14.3`, `rapidfuzz 3.14.6`, `xlrd 2.0.2` e `dvc 3.67.1`. Também ficam fixados DuckDB 2.0, Pandera 0.36 e Alembic 1.20. Todas estão em `requirements.lock`, usado pela imagem do Airflow e pela CI.

## Ambiente local (`docker-compose.yml`)

| Serviço | Imagem | Porta | Papel |
| --- | --- | --- | --- |
| `postgres` | `pgvector/pgvector:pg17` | 5433 | Bancos de serving (`mira_dados`), testes (`mira_dados_teste`) e metadados do Airflow (`airflow`) |
| `seaweedfs` | `chrislusf/seaweedfs:4.52` | 8333 (S3) | Data lake e remote do DVC |
| `seaweedfs-init` | idem | — | Cria o bucket `mira-dados` |
| `airflow-*` (init, api-server, scheduler, dag-processor) | `mira-dados-airflow:3.3.2` (build local) | 8080 | Orquestração (perfil `airflow`) |

O PostgreSQL usa a porta 5433 para conviver com o `mira-postgres` do `ia`, na 5432.

```bash
make up            # postgres + seaweedfs + bucket
make up-airflow    # + Airflow em http://localhost:8080
make down          # para tudo (volumes preservados)
make help          # lista todos os alvos
```

## Remote do DVC

| Arquivo | Vai para o Git? | Conteúdo |
| --- | --- | --- |
| `.dvc/config` | Sim | `remote = lake`, `url = s3://mira-dados/dvc` (igual em dev e prod) |
| `.dvc/config.local` | **Não** | `endpointurl = http://localhost:8333` + credenciais locais |

Em produção não existe `config.local`: o DVC usa o S3 real com as credenciais do ambiente (IAM role).

### O que vai para o Git

| Vai | Não vai |
| --- | --- |
| Código, testes, `dvc.yaml`, `dvc.lock`, `params.yaml` | Binários de `data/raw/` (só os `.dvc`) |
| `data/curadoria/**` e `data/raw/FONTES.md` | Parquets de `interim/` e `processed/` (ficam no cache do DVC) |
| Relatórios `relatorio_base.json`, `relatorio_off.json`, `relatorio_dq.json` | `cache/`, `.env`, `.venv/`, `site/` |

## CI (`.github/workflows/ci.yml`)

| Job | O que valida |
| --- | --- |
| `qualidade` | `ruff`, `pytest` com PostgreSQL + pgvector de serviço (migrações e publicação), `dvc dag` e `dvc status` |
| `dag` | Airflow 3.3.2 + `DagBag`: import, tasks, dependências e retries |
| `terraform` | `terraform fmt -check`, `init -backend=false` e `validate` |
| `docs` | `mkdocs build --strict` |

A CI não acessa o data lake: os testes que precisam de `data/` são pulados. Num checkout limpo, 67 passam, 32 são pulados e nenhum falha.

## Terraform (`infra/terraform/`)

| Módulo | Recursos | Destaques |
| --- | --- | --- |
| `s3_data_lake` | Bucket com versionamento, SSE-KMS, bloqueio público e lifecycle | Prefixos `bronze/`, `silver/`, `gold/` e `dvc/` |
| `rds_postgres` | RDS PostgreSQL 17, subnet group, security group e parameter group | Senha no Secrets Manager, SSL obrigatório, Multi-AZ e proteção contra exclusão em prod |
| `ecr` | Repositório da imagem do Airflow e do ETL | Tags imutáveis, scan no push, mantém as 20 últimas |

```bash
make tf-validate                                                  # sem credenciais AWS
cp infra/terraform/dev.tfvars.example infra/terraform/dev.tfvars  # preencher VPC e subnets
terraform -chdir=infra/terraform plan -var-file=dev.tfvars
```

O pgvector não precisa de instalação no RDS: a migração Alembic `0001` cria as extensões `vector` e `unaccent`, como no ambiente local.

## Pendências

| Pendência | Impacto |
| --- | --- |
| CI executando no GitHub | O workflow só foi validado localmente, job a job |
| MWAA no Terraform e troca do `BashOperator` por `EcsRunTaskOperator` | Orquestração em produção |
| `ia` lendo tudo do PostgreSQL em vez de conferir os Parquets do disco | Acoplamento entre os repositórios |
| `ia` com os Parquets novos (`dvc pull`) | O corte por tokens mudou o `base_sha256` |
| Revisão da curadoria (472 linhas) | Bloqueia o perfil de produção (`aprovado_equipe`) |
| Medidas caseiras em só 26,8 % dos alimentos | Itens sem gramas para o usuário |
| Grupo NOVA em só 25 % dos produtos | Orientação sobre ultraprocessados incompleta |

**Fontes:** documentação do `mira-dados`: [Stack local ↔ AWS](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/arquitetura/stack.md), [Infraestrutura, CI/CD e IaC](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/arquitetura/infra.md), [Estrutura do repositório](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/arquitetura/repositorio.md) e [Roadmap](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/operacao/roadmap.md).
