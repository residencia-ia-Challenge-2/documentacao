# Arquitetura

## Visão em camadas

Cada camada só conhece a de baixo, por meio de uma interface. É isso que permite trocar implementação em memória por framework ou banco, ou texto por foto, sem mexer no resto.

```mermaid
flowchart TB
    UI["<b>Interface</b><br/>app/streamlit_app.py<br/><i>só fala HTTP com a API, sem regra de negócio</i>"]
    API["<b>API</b><br/>src/mira/api/app.py<br/>FastAPI: /analisar-refeicao · /orientar · /recalcular"]
    PIPE["<b>Pipelines</b><br/>src/mira/pipelines/<br/>Pipeline.analisar(texto) → ResultadoRefeicao<br/>v1 · v2 · v3 · v4 · v5 (todos continuam rodando)"]
    LLM["<b>LLM</b><br/>src/mira/llm/<br/>cliente + prompts versionados<br/>(falso nos testes)"]
    RAG["<b>RAG</b><br/>src/mira/rag/<br/>BM25 · embeddings · normalização · RRF"]
    REPO["<b>Repositórios</b><br/>repositorios/<br/>RepositorioAlimentos:<br/>local · framework · postgres"]
    NUT["<b>Nutrição</b><br/>nutricao/<br/>gramas × TACO"]
    DOM["<b>Domínio</b><br/>src/mira/dominio/<br/>modelos Pydantic: ItemExtraido, Candidato, Alimento…"]
    DADOS["<b>Dados</b><br/>repositório mira-dados<br/>Bronze → Silver → Gold → Data Quality → PostgreSQL"]

    UI --> API --> PIPE
    PIPE --> LLM & RAG & REPO & NUT
    LLM & RAG & REPO & NUT --> DOM
    DOM --> DADOS
```

## Componentes de infraestrutura

| Componente | Tecnologia | Papel |
| --- | --- | --- |
| Interface | Streamlit | Demonstração do MVP. |
| API | FastAPI (servida com Uvicorn) | Expõe os pipelines via HTTP. Ver [API HTTP](../reference/api.md). |
| LLM | Qwen 2.5 7B local via Ollama (ADR-014) | Extração, escolha e orientação. Clientes alternativos: Anthropic e falso. |
| Banco | PostgreSQL 17 + pgvector | Alimentos, medidas, sinônimos, grupos, trechos e produtos. Publicado pelo `mira-dados`; o `ia` só lê. Ver [Banco de dados](../reference/dados.md). |
| Dados | DVC, Alembic, Airflow (repositório `mira-dados`) | Versionamento das fontes, ETL, Data Quality, migrações e publicação. |
| Busca | BM25 + embeddings + RRF (manual, LangChain ou pgvector) | Recuperação de candidatos e trechos. Embeddings `granite-embedding:278m` (768 dimensões). |

## Nesta seção

- [Pipeline de uma refeição](pipeline-refeicao.md) — os 6 passos do pipeline v5.
- [Etapas e avaliação](etapas.md) — evolução v1 → v5 e métricas comparativas.
- [Regras fixas e ADRs](decisoes.md) — o que só muda com um ADR.
- [Visão computacional](visao-computacional.md) — detector atual (`ModeloVisao`), limites e arquitetura de referência original.
- [Sistema multiagentes multimodal (proposta)](multiagentes.md) — texto e foto, orquestrador em código, agentes especialistas e roteiro da Etapa 8.

**Fontes:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 1 e 2; [`mira-dados`: Modelo de dados](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/definicao/modelo_dados.md).
