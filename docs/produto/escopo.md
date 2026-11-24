# Escopo

O que o projeto entrega agora, o que fica para depois e o que não faz parte dele. Os requisitos que cada item atende estão em [Requisitos](requisitos.md); a situação de cada um no MVP está na [rastreabilidade](requisitos.md#rastreabilidade-com-o-mvp).

## Dentro do escopo: MVP (texto livre)

| Entrega | Situação | Onde |
| --- | --- | --- |
| Entrada da refeição em **texto livre** ("2 conchas de feijão e 1 filé de frango") | Implementado | `ia` |
| Extração de itens e escolha do alimento pela LLM, **só entre candidatos da base** | Implementado | `ia` |
| Cálculo determinístico de gramas, kcal e macros (`gramas × valor por 100 g`) | Implementado | `ia` |
| Orientação qualitativa baseada no Guia Alimentar e em publicações do MS | Implementado | `ia` |
| Base de serving: TACO, medidas caseiras do IBGE, sinônimos, grupos do Guia e trechos documentais | Publicada | `mira-dados` |
| Etapas comparáveis (v1 a v5) avaliadas no mesmo conjunto | Etapas 0 a 6 concluídas; Etapa 7 pendente | `ia` |
| Interface de demonstração (Streamlit) e API (FastAPI) | Implementado | `ia` |

## Fase 2

| Entrega | Situação | Onde |
| --- | --- | --- |
| Análise por **foto do prato** (segmentação, estimativa de porção) | Não iniciado no código | Repositório de visão computacional |
| Leitura de **código de barras** | Dados prontos (26.389 produtos do Open Food Facts em `produtos_ean`); o `ia` ainda não consome | `mira-dados` (dados) · `ia` (feature) |
| Filtro por público e condição de saúde na busca de trechos | Dados prontos; código não | `mira-dados` (dados) · `ia` (busca) |

## Futuro (sem fase definida)

- OCR da tabela nutricional como *fallback* do código de barras (RF06).
- TBCA para alimentos fora da TACO (depende dos termos de uso).
- Histórico de refeições por data e tipo (RF05).
- App móvel com fluxo de 3 toques (RNF02); o MVP usa Streamlit.
- Deploy em nuvem (AWS: S3, RDS, ECR e MWAA); hoje tudo roda em instâncias locais.
- Multiagentes sobre a base do `mira-dados`.

## Fora do escopo

- **A LLM gerar números nutricionais.** Calorias e macros vêm só da base, por cálculo em código (ver [Regras fixas](../architecture/decisoes.md)).
- **Regras clínicas próprias.** Sem nutricionista no time, toda classificação qualitativa aponta para o Guia Alimentar ou uma publicação do MS.
- **Prescrição de dieta ou diagnóstico.** O Mira orienta sobre a qualidade do prato; não substitui um profissional de saúde.
- **Versionar refeições reais de usuários** (LGPD).

## Repositórios

O projeto é dividido em repositórios com donos e fronteiras claras.

| Repositório | É dono de | Não contém |
| --- | --- | --- |
| [`documentacao`](https://github.com/residencia-ia-Challenge-2/documentacao) (este site) | Produto, arquitetura geral, guias e referência | Código |
| [`ia`](https://github.com/residencia-ia-Challenge-2/ia) | Pipelines RAG (v1 a v5), LLM e prompts, avaliação (`eval/`), API FastAPI e Streamlit | DDL do banco, carga destrutiva, download de fontes |
| [`data`](https://github.com/residencia-ia-Challenge-2/data) (`mira-dados`) | Fontes brutas, ETL, curadoria, Data Quality, migrações do banco e publicação | API, prompts, pipelines RAG, gabarito de avaliação |
| Visão computacional | Dataset de fotos, modelo e estimativa de porção (fase 2) | — |

```mermaid
flowchart LR
    D["<b>mira-dados</b><br/>fontes → ETL → Data Quality"] -->|publica| PG[("PostgreSQL + pgvector<br/>schema mira")]
    PG -->|só leitura| IA["<b>ia</b><br/>pipelines · API · avaliação"]
    VC["<b>visão computacional</b><br/>(fase 2)"] -.->|"list[ItemExtraido]"| IA
    IA --> UI["Streamlit"]
```

A separação vale nos dois sentidos: o `ia` **só lê** o banco, e o `mira-dados` **nunca** lê o gabarito de avaliação. Detalhes em [Banco de dados e fontes](../reference/dados.md).

!!! warning "TODO"
    Informar o endereço do repositório de visão computacional e confirmar com a equipe se o código de barras entra na fase 2 ou fica para o futuro.

**Fontes:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 1, 2 e 6; [`mira-dados`: Contexto e regras fixas](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/definicao/contexto.md) e [Roadmap](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/operacao/roadmap.md).
