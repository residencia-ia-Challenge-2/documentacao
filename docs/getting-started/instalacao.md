# Ambiente local

## Pré-requisitos

| Ferramenta | Para quê | Versão |
| --- | --- | --- |
| Python | API, pipelines, ETL, testes | TODO |
| Docker e Docker Compose | PostgreSQL + pgvector | TODO |
| Ollama | Executar a LLM local (Qwen 2.5 7B) | TODO |
| DVC | Versionamento e reprodução dos dados | TODO |
| Git | Fluxo de contribuição | — |

!!! warning "TODO"
    Preencher as versões mínimas, o gerenciador de dependências do código (pip, uv, poetry…) e o comando de instalação do pacote `mira`.

## 1. Obter o código e os dados

```bash
git clone <URL-DO-REPOSITORIO-DE-CODIGO>  # TODO: URL
cd <repositorio>
# TODO: comando de instalação das dependências
dvc pull   # requer o remoto do DVC configurado
```

!!! warning "Remoto do DVC ainda não existe"
    A configuração de um remoto (Drive ou S3) para `dvc pull` é uma tarefa pendente da Engenharia de dados. Até lá, os dados processados precisam ser gerados localmente com `dvc repro` a partir de `data/raw/`.

    !!! warning "TODO"
        Explicar como um desenvolvedor novo obtém `data/raw/` enquanto o remoto não existe.

## 2. Preparar a LLM

```bash
ollama pull qwen2.5:7b
```

!!! warning "TODO"
    Confirmar a *tag* exata do modelo usada no projeto (ADR-014) e se há parâmetros de execução específicos.

## 3. Subir o banco e carregar a base

```bash
docker compose up -d
python -m mira.repositorios.postgres carregar
```

## 4. Subir a API e a interface

=== "API (FastAPI)"

    ```bash
    uvicorn mira.api.app:app --port 8000
    ```

=== "Interface (Streamlit)"

    ```bash
    streamlit run app/streamlit_app.py
    ```

A interface só conversa com a API via HTTP — não contém regra de negócio.

!!! warning "`docker-compose.yml` marcado como \"a criar\""
    A Estrutura do Mira lista `docker-compose.yml` como **a criar**, mas o roteiro de execução local já usa `docker compose up -d`. Confirmar se o arquivo já existe no repositório.

## 5. Verificar a instalação

```bash
ruff check . && pytest
```

Os testes rodam **sem rede** e com **LLM falsa**. Ver [Rodar testes e lint](../guides/testes.md).

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 3, 6 e 7.
