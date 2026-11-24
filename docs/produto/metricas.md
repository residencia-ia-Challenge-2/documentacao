# Métricas de sucesso

## KPIs de produto

| Indicador | Meta |
| --- | --- |
| Tempo médio de registro | Jornada completa de registro de uma refeição em **menos de 8 segundos**. |
| Retenção em 30 dias (D30) | Retenção de usuários ativos **≥ 25%**. |
| Taxa de intervenção manual | **< 15%** das refeições exigindo correção manual. |
| Net Promoter Score (NPS) | **> 50** pontos. |

## Metas técnicas ligadas aos requisitos

| Referência | Meta |
| --- | --- |
| RNF01 | Backend + IA em menos de 3 s. |
| RNF02 | Fluxo principal em até 3 toques. |
| RNF03 | Acurácia ≥ 85% na identificação de alimentos comuns. |
| RNF05 | Disponibilidade da API ≥ 99,5%. |

## Métricas de avaliação do pipeline

As métricas usadas para comparar as etapas do Mira (mapeamento, MAPE de kcal, Recall@3 e latência p95) estão em [Etapas e avaliação](../architecture/etapas.md).

!!! warning "TODO"
    Definir **como** cada KPI de produto será medido (instrumentação, ferramenta de analytics, período de coleta) e se eles se aplicam ao MVP em Streamlit.

**Fontes:** [Definições do Projeto v2](../assets/pdfs/definicoes-projeto-v2.pdf), seções 5 e 8.
