# Plano de migração (PDF → Markdown)

Registro da migração dos PDFs e da documentação plana anterior para este portal (*Docs-as-Code*).

## 1. Inventário

### Documentos analisados

| Arquivo original | Páginas | Observação |
| --- | --- | --- |
| `Estrutura_Mira.pdf` | 4 | Documento técnico mais recente (10/11/2026). |
| `Grupo 13 - Definições do Projeto Challenge II (1).pdf` | 7 | Definições v2. |
| `definicoes-projeto.pdf` | 5 | Definições v1 (cópia idêntica em `docs/referencias/` e na raiz). |
| `pipeline-yolov8.pdf` | 1 | Diagrama de arquitetura original (cópia idêntica em `docs/referencias/` e na raiz). |
| `docs/*.md` (7 páginas) | — | Documentação plana anterior, baseada em v1 + diagrama. |

### Temas centrais

| Tema | Fontes | Seção do portal |
| --- | --- | --- |
| Visão geral e propósito | Estrutura §intro; Definições §1, §2, §10 | `index.md` |
| Contexto, público, stakeholders, personas | Definições §1–4, §9 | `produto/contexto.md` |
| Requisitos | Definições §5 | `produto/requisitos.md` |
| Riscos e premissas | Definições §6–7 | `produto/riscos-premissas.md` |
| KPIs | Definições §8 | `produto/metricas.md` |
| Arquitetura em camadas e pipeline | Estrutura §1–2 | `architecture/` |
| Evolução por etapas e métricas de avaliação | Estrutura §4 | `architecture/etapas.md` |
| Regras de negócio / regras fixas | Estrutura §5 | `architecture/decisoes.md` |
| Visão computacional e arquitetura original | Pipeline YOLOv8; Estrutura §6 | `architecture/visao-computacional.md` |
| Estrutura de pastas e responsáveis | Estrutura §3 | `getting-started/estrutura-repositorio.md` |
| Execução local | Estrutura §7 | `getting-started/instalacao.md` |
| Fluxo de contribuição | Estrutura §6–7 | `guides/` |
| Contratos e API | Estrutura §2 | `reference/` |
| Equipe | Definições v2 capa; Estrutura §6 | `sobre/equipe.md` |

### Lacunas críticas (para um desenvolvedor novo)

| # | Lacuna | Página com TODO |
| --- | --- | --- |
| 1 | URL e acesso aos repositórios de código e de visão computacional. | `getting-started/index.md` |
| 2 | Versões de Python, Docker, Ollama e DVC; gerenciador de dependências. | `getting-started/instalacao.md` |
| 3 | Lista de variáveis de ambiente / `.env.example`. | `getting-started/variaveis-ambiente.md` |
| 4 | Como obter `data/raw/` sem remoto do DVC. | `getting-started/instalacao.md` |
| 5 | Payloads, métodos e erros da API. | `reference/api.md` |
| 6 | Campos dos modelos Pydantic e exemplo de `ResultadoRefeicao`. | `reference/contratos.md`, `architecture/pipeline-refeicao.md` |
| 7 | Schema do banco e modelo de embeddings. | `reference/dados.md`, `architecture/index.md` |
| 8 | Conteúdo dos ADRs (só o ADR-014 é citado). | `architecture/decisoes.md` |
| 9 | Definição formal da métrica "Mapeamento" e formato de `queries.jsonl`. | `architecture/etapas.md`, `guides/avaliar-pipeline.md` |
| 10 | Como registrar um novo pipeline. | `guides/nova-etapa.md` |
| 11 | Deploy do MVP (inexistente) e CI (inexistente). | `guides/deploy.md`, `guides/testes.md` |
| 12 | Tratamento de LGPD/consentimento na prática (RNF04). | `produto/requisitos.md` |
| 13 | Como medir os KPIs de produto. | `produto/metricas.md` |
| 14 | Quem é responsável por cada área. | `sobre/equipe.md` |

!!! success "Resolvidas depois, com o `mira-dados`"
    As lacunas **4** (obter `data/raw/`: remote S3 e `mira_dados.ingerir`) e **7** (schema do banco e modelo de embeddings) foram fechadas com a documentação do repositório [`data`](https://github.com/residencia-ia-Challenge-2/data). A lacuna **1** ficou parcial: faltam o endereço do repositório de visão computacional e o processo de acesso.

### Redundâncias

| Redundância | Tratamento |
| --- | --- |
| `definicoes-projeto.pdf` e `pipeline-yolov8.pdf` existiam na raiz **e** em `docs/referencias/` (bytes idênticos). | Mantida uma única cópia em `docs/assets/pdfs/`. |
| Definições v1 e v2 repetem ~80% do conteúdo. | Migrada só a v2; diferenças viraram blocos "Mudou na v2". |
| Stakeholder "Desenvolvedores e Engenheiros de IA/ML" duplicado na tabela das Definições (v1 e v2, quebra de página). | Linha única. |
| Metas de desempenho repetidas em personas, RNF e KPIs (`metricas.md` antigo). | Centralizadas em `produto/metricas.md`, com link a partir dos requisitos. |
| Estrutura de pastas (§3) e camadas (§2) da Estrutura descrevem os mesmos módulos. | Camadas em `architecture/index.md`; pastas e responsáveis em `getting-started/estrutura-repositorio.md`. |
| Tarefas da Eng. de dados e Ciência repetidas em vários contextos. | Fonte única em `sobre/equipe.md`; demais páginas citam ou linkam. |

## 2. Mapa de migração por documento

### Estrutura do projeto Mira

| Seção do PDF | Destino |
| --- | --- |
| Introdução ("O que é", "Ideia central") | `index.md` |
| §1 Fluxo de uma refeição (pipeline v5) | `architecture/pipeline-refeicao.md` |
| §2 Arquitetura em camadas | `architecture/index.md` |
| §2 Os dois contratos | `reference/contratos.md` |
| §3 Estrutura de pastas | `getting-started/estrutura-repositorio.md` |
| §4 Etapas | `architecture/etapas.md` |
| §5 Regras fixas | `architecture/decisoes.md` |
| §6 Divisão da equipe | `sobre/equipe.md`; trechos em `guides/dados-dvc.md`, `guides/deploy.md`, `architecture/visao-computacional.md` |
| §7 Como contribuir | `guides/contribuir.md`, `guides/testes.md`, `guides/avaliar-pipeline.md` |
| §7 Subir o MVP localmente | `getting-started/instalacao.md` |

**Convertidos em código:**

- Diagrama do pipeline v5 → Mermaid `flowchart` com classes LLM/determinístico.
- Diagrama ASCII de camadas → Mermaid `flowchart TB`.
- Tabela de pastas, etapas e divisão da equipe → tabelas Markdown.
- Métricas das etapas → tabela + 2 gráficos Mermaid `xychart-beta`.
- Cartões de regras fixas → tabela Markdown.
- Fluxo de contribuição → Mermaid `flowchart` + abas de comandos.
- Novo: fluxo de dados do DVC (Mermaid) e diagrama de classes dos modelos de domínio.

### Definições do Projeto v2 (e v1)

| Seção do PDF | Destino |
| --- | --- |
| Capa (equipe) | `sobre/equipe.md` |
| §1 Cenário, §2 Problema, §3 Stakeholders, §4 Personas, §9 Questões | `produto/contexto.md` |
| §5 Requisitos | `produto/requisitos.md` |
| §6 Riscos, §7 Premissas | `produto/riscos-premissas.md` |
| §8 KPIs | `produto/metricas.md` |
| §10 Questão central | `index.md` e `produto/contexto.md` |

**Convertidos em código:**

- Listas de RF/RNF → tabelas Markdown com ID.
- Tabela de riscos → tabela Markdown + matriz Mermaid `quadrantChart` (posição derivada de Alta/Média/Baixa).
- Personas → abas (`pymdownx.tabbed`).

### YOLOv8 Nutrition Analysis Pipeline

| Conteúdo | Destino |
| --- | --- |
| Diagrama completo (cliente, gateway, visão, produtos, LLM, MLOps) | `architecture/visao-computacional.md` (seção histórica) |
| Diferenças em relação à arquitetura atual | `architecture/visao-computacional.md` e `sobre/fontes.md` |

**Convertido em código:** diagrama raster → Mermaid `flowchart` com `subgraph` por camada (o rótulo corrompido `|s40| R T[...]` do original foi interpretado como a aresta Data lake → Evidently/MLflow → Roboflow/CVAT).

### Documentação Markdown anterior

| Página antiga | Nova página | Redirecionamento |
| --- | --- | --- |
| `index.md` | `index.md` | — |
| `projeto.md` | `produto/contexto.md` | Sim |
| `requisitos.md` | `produto/requisitos.md` | Sim |
| `arquitetura.md` | `architecture/index.md` + `architecture/visao-computacional.md` | Sim |
| `riscos.md` | `produto/riscos-premissas.md` | Sim |
| `metricas.md` | `produto/metricas.md` | Sim |
| `fontes.md` | `sobre/fontes.md` | Sim |

Os redirecionamentos são feitos pelo plugin `mkdocs-redirects`, para não quebrar links já publicados.

## 3. Convenções adotadas

- Diagramas sempre em Mermaid (nunca imagem exportada), para serem editáveis via PR.
- Pendências marcadas com `!!! warning "TODO"`; conflitos entre fontes com `!!! danger`.
- Cada página termina com a linha **Fonte(s)**, citando o PDF e a seção.
- Imagens futuras em `docs/assets/images/`; PDFs históricos em `docs/assets/pdfs/`.
