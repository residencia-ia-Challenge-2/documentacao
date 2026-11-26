# Roadmap e status dos dados

Evolução do repositório `data` (`mira-dados`), fase a fase, e o que falta.

## Fase 1: bootstrap e migração do `ia` (concluída)

- [x] Esqueleto do repositório e dependências
- [x] `FONTES.md` e arquivos `.dvc` com os mesmos hashes do `ia`
- [x] Curadoria em `nutricional/` e `especialista/` com `escopo`, `status` e `evidencia_fonte`
- [x] ETL portado (`silver/`, `gold/`) e `ingerir.py` lendo os manifestos
- [x] Prova de invariância (`base.ampliada` `false` e `true`, byte a byte)

## Expansão: Open Food Facts (concluída)

- [x] Admissão (licença ODbL, chave `ean_<gtin>`, LGPD, impacto na avaliação)
- [x] Recorte Brasil determinístico e validação física → 26.389 produtos
- [x] Entrada no [Registro de datasets](registro-datasets.md)

## Fase 2: qualidade, Alembic e publicação atômica (concluída)

- [x] Contratos Pandera e `qualidade.py` no `dvc.yaml` (27 regras bloqueantes nesta fase; hoje são 35)
- [x] Alembic `0001_schema_inicial` com schema alvo parametrizável
- [x] `publicar_postgres.py`: `staging`, `smoke`, `swap`, `rollback` e `status`
- [x] Testes de ETL, qualidade e publicação, incluindo smoke reprovado preservando o schema ativo
- [x] Compatibilidade verificada com o código do `ia` lendo o banco publicado

## Fase 3: Docker, Airflow e CI/CD (concluída)

- [x] `docker-compose.yml` com `postgres` (pgvector:pg17), `seaweedfs` + `seaweedfs-init` (no lugar do MinIO, decisão D21) e Airflow (perfil opcional)
- [x] `.dvc/config` com remote S3 (`s3://mira-dados/dvc`) e `dvc push` feito
- [x] DAG `mira_ciclo_dados` + `tests/test_dag_integridade.py`; primeira execução real com as 15 tasks de então em `success` (hoje são 17)
- [x] `Makefile`, `.github/workflows/ci.yml` e `infra/terraform/` (S3, RDS, ECR; `terraform validate` ok)
- [x] Testes de integração do `ia` contra o banco do `mira-dados`: 24 de 24

## Prontidão para RAG e multiagentes (concluída)

- [x] Corte por tokens reais do modelo: 0 de 2.424 trechos acima de 512, com regra bloqueante no DQ
- [x] `chunking.max_tokens` no `params.yaml` para ablações; `null` mantém a paridade com o `ia`
- [x] `publico_alvo` e `condicao_saude` em `documentos` e `trechos_ampliados`, com `tema` e `relevancia` (migração `0002`)
- [x] `pg_trgm` e índices de trigramas em `alimentos.nome` e `sinonimos.termo`
- [x] `regras_oficiais` (15) na Gold e no banco, conferidas literalmente contra o trecho de origem
- [x] [Contrato para RAG e agentes](rag-agentes.md)

## Próximos passos

- [ ] Ver a CI rodando no GitHub (o workflow foi validado localmente, job a job)
- [ ] MWAA no Terraform e troca do `BashOperator` por `EcsRunTaskOperator`
- [ ] `ia` lendo do PostgreSQL em vez de `data/processed/` local (veja [Integração com o `ia`](integracao-ia.md))
- [ ] `ia` com os Parquets novos (`dvc pull`), porque o corte por tokens mudou o `base_sha256`

## Pendências de dados

| Pendência | Impacto | Referência |
| --- | --- | --- |
| Revisão da curadoria pela equipe (472 linhas pendentes, incluindo público e regras) | Bloqueia o perfil de produção (`aprovado_equipe`) | [Curadoria](curadoria.md) |
| Cobertura de medidas caseiras em 26,8 % dos alimentos | Itens sem gramas para o usuário | [Fontes](fontes.md) |
| NOVA em 25 % dos produtos EAN | Orientação sobre ultraprocessados incompleta | [Registro de datasets](registro-datasets.md) |
| Cosmos, OCR de rótulo, TBCA, imagens | Features futuras | [Registro de datasets](registro-datasets.md) |

As decisões ainda em aberto estão em [Decisões de engenharia de dados](decisoes.md#pendencias-de-decisao).

**Fontes:** histórico de commits e pull requests do repositório `data`; [Decisões de engenharia de dados](decisoes.md).
