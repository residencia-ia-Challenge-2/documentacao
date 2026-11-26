# Equipe e responsabilidades

## Integrantes (Grupo 13)

- Artur Braz Lopes
- George Filipe Rodrigues de Lacerda
- Jessika Cardoso do Nascimento
- Pedro Henrique Silva de Sousa

## Áreas (4 pessoas, 5 áreas)

Desenvolvimento e CI/CD ficam com a mesma pessoa: o CI ainda não existe, e o que ele testa e publica é justamente a API, a interface e o Docker. As fronteiras entre as áreas seguem os [contratos](../reference/contratos.md).

| Área | É dona de | Próximas tarefas |
| --- | --- | --- |
| 1. Engenharia de dados | Repositório [`data`](https://github.com/residencia-ia-Challenge-2/data) (`mira-dados`): fontes, ETL, curadoria, Data Quality, schema e publicação no Postgres | Revisão da curadoria pela equipe (472 linhas pendentes) · Ver a CI rodando no GitHub · `ia` lendo tudo do PostgreSQL em vez dos Parquets do disco · Cobertura de medidas caseiras (26,8 %) · TBCA (verificar termos de uso) · MWAA no Terraform |
| 2. Ciência de dados | `eval/`, `llm/prompts`, `pipelines/`, `rag/`; Etapa 7 | Completar o conjunto (15 → 40) e criar conjunto de validação sem curadoria · Erros de escolha: `qwen2.5:14b` e sensibilidade à ordem dos candidatos · Validar o LLM-juiz (kappa) e melhorar a fidelidade da orientação · Relatório comparativo da Etapa 7 |
| 3. Desenvolvimento + CI/CD | `api/`, `app/`, Docker, `.github/` | GitHub Actions: ruff + pytest + dvc status + build da imagem; `main` protegida · Concorrência na API · Latência (~18 s): streaming e cache · Dockerfiles e deploy automático a cada tag `etapa-N` |
| 4. Visão computacional | Fase 2 (outro repositório) e a rota `/v1/analisar-foto` | Contrato foto → `list[ItemExtraido]` · Dataset de pratos brasileiros com os 29 alimentos do MVP (LGPD) · Baseline de detecção e estimativa de porção |

!!! warning "TODO"
    Indicar qual integrante responde por cada área.

## Tarefas de todos

- [ ] Anotar as 25 refeições novas: cada pessoa anota e outra revisa.
- [ ] Revisão cruzada nos PRs: dados ↔ ciência e dev ↔ visão.
- [ ] Quem fecha uma etapa atualiza `DESENVOLVIMENTO.md` (seção, métricas, ADRs, questões) e cria a tag.
- [ ] Uma pessoa fica responsável por conseguir um nutricionista para validar medidas e densidades.

!!! note "Atualizado com o `mira-dados`"
    As tarefas antigas da Engenharia de dados "remoto do DVC" e "filtro por público na base ampliada" (lado dos dados) foram concluídas no `mira-dados`. O filtro na busca ainda falta no código do `ia`.

**Fontes:** [Definições do Projeto v2](../assets/pdfs/definicoes-projeto-v2.pdf), capa; [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seção 6; [Roadmap dos dados](../dados/roadmap.md).
