# Fontes e histórico

## Documentos de origem

| Documento | Data | Conteúdo | Situação | Arquivo |
| --- | --- | --- | --- | --- |
| Estrutura do projeto Mira | 10/11/2026 | Pipeline v5, camadas, contratos, pastas, etapas e métricas, regras fixas, divisão da equipe, contribuição e execução local. | **Vigente** (técnico) | [PDF](../assets/pdfs/estrutura-mira.pdf) |
| Definições do Projeto — v2 | 2026 | Capa com equipe, cenário, problema, stakeholders, personas, RF01–RF08, RNF01–RNF05, riscos 1–5, premissas, KPIs e questões. | **Vigente** (produto) | [PDF](../assets/pdfs/definicoes-projeto-v2.pdf) |
| Definições do Projeto — v1 | 2026 | Primeira versão: RF01–RF05, RNF01–RNF04, riscos 1–3. | Substituída pela v2 | [PDF](../assets/pdfs/definicoes-projeto-v1.pdf) |
| YOLOv8 Nutrition Analysis Pipeline | 29/10/2026 | Diagrama da arquitetura original: prato (YOLOv8), produto (Open Food Facts/OCR), LLM, validação JSON, MLOps. | Histórico — substituído pela Estrutura do Mira | [PDF](../assets/pdfs/pipeline-yolov8.pdf) |

Além dos PDFs, a [documentação do `mira-dados`](https://github.com/residencia-ia-Challenge-2/data/tree/main/docs) é a fonte **vigente para dados**: fontes, ETL, curadoria, schema do banco e publicação. As páginas daqui resumem e linkam para ela, sem duplicar os detalhes.

O conteúdo dos PDFs foi migrado para Markdown; os arquivos originais ficam em `docs/assets/pdfs/` apenas como histórico. **A fonte da verdade passa a ser esta documentação.**

## Divergências entre documentos

Pontos em que os documentos se contradizem e que a equipe precisa resolver.

| # | Tema | Versão A | Versão B | Onde aparece |
| --- | --- | --- | --- | --- |
| 1 | Nome | FoodScan (Definições v2) | Mira (FoodScan) (Estrutura) | [Início](../index.md) |
| 2 | Entrada do MVP | Foto do prato e código de barras (RF01) | Texto livre; foto na fase 2 | [Requisitos](../produto/requisitos.md#rastreabilidade-com-o-mvp) |
| 3 | Quem calcula kcal/macros | LLM (RF03 v2) | Código determinístico; "números só da base" | [Requisitos](../produto/requisitos.md), [Regras fixas](../architecture/decisoes.md) |
| 4 | LLM | GPT-4o-mini / Claude (Pipeline YOLOv8); `qwen3:8b` (docs anteriores, sem PDF de origem) | Qwen 2.5 7B via Ollama (ADR-014) | [Visão computacional](../architecture/visao-computacional.md) |
| 5 | Endpoints | `/analisar-prato/`, `/analisar-produto/` | `/analisar-refeicao`, `/orientar`, `/recalcular`, `/v1/analisar-foto` | [API](../reference/api.md) |
| 6 | Bases nutricionais | TACO, TBCA, Open Food Facts (v2); USDA (v1) | TACO + medidas IBGE + Open Food Facts (na base, sem uso no MVP); TBCA futura | [Banco e fontes](../reference/dados.md) |
| 7 | Desempenho | RNF01 < 3 s | p95 medido de 17,5 s a 45,1 s | [Etapas](../architecture/etapas.md) |
| 8 | Personas | Lucas (24, < 5 s) e Camila (36) — v1 | Pedro (24, < 8 s) e Ana (30) — v2 | [Contexto](../produto/contexto.md#personas) |
| 9 | Front-end | App móvel, 3 toques (RNF02) | Streamlit (MVP) | [Arquitetura](../architecture/index.md) |
| 10 | MLOps | Data lake, Roboflow/CVAT, Evidently, MLflow | Dados: DVC + remote S3, Airflow, Alembic, CI e Terraform no `mira-dados`. Aplicação: CI/CD do `ia` a criar | [Visão computacional](../architecture/visao-computacional.md) |

!!! warning "TODO"
    Para cada divergência, registrar a decisão (de preferência como ADR) e atualizar a página indicada.
