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
    DADOS["<b>Dados</b><br/>src/mira/dados/ + DVC<br/>ETL: data/raw → data/interim → data/processed"]

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
| Banco | PostgreSQL + pgvector | Alimentos, medidas, sinônimos e trechos do Guia. Ver [Banco de dados](../reference/dados.md). |
| Dados | DVC | Versionamento e reprodução do ETL. |
| Busca | BM25 + embeddings + RRF (manual, LangChain ou pgvector) | Recuperação de candidatos e trechos. |

## Nesta seção

- [Pipeline de uma refeição](pipeline-refeicao.md) — os 6 passos do pipeline v5.
- [Etapas e avaliação](etapas.md) — evolução v1 → v5 e métricas comparativas.
- [Regras fixas e ADRs](decisoes.md) — o que só muda com um ADR.
- [Visão computacional (fase 2)](visao-computacional.md) — arquitetura de referência para foto e código de barras.

!!! warning "TODO"
    Documentar o modelo de embeddings usado na busca vetorial e a dimensão dos vetores no pgvector.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 1 e 2.
