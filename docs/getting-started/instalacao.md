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
git clone git@github.com:residencia-ia-Challenge-2/ia.git
git clone git@github.com:residencia-ia-Challenge-2/data.git   # mira-dados: base de dados
# TODO: comando de instalação das dependências do ia
```

Os dados vêm do `mira-dados`: o remote do DVC (`s3://mira-dados/dvc`) já existe, e sem acesso a ele `python -m mira_dados.ingerir` baixa as fontes das URLs originais e confere o SHA-256. Ver [Dados e DVC](../guides/dados-dvc.md#regenerar-os-dados).

## 2. Preparar a LLM

```bash
ollama pull qwen2.5:7b
```

!!! warning "TODO"
    Confirmar a *tag* exata do modelo usada no projeto (ADR-014) e se há parâmetros de execução específicos.

## 3. Subir o banco e carregar a base

O banco é publicado pelo `mira-dados`. O `ia` só lê.

```bash
# no repositório mira-dados
make up            # PostgreSQL na porta 5433 + S3 local
dvc pull data/raw/*.dvc && dvc repro
make db-publish    # staging → smoke → swap (Ollama ligado)
```

No `.env` do `ia`, aponte para esse banco:

```bash
DATABASE_URL=postgresql://mira:mira@localhost:5433/mira_dados
```

O `mira-dados` usa a porta 5433 para conviver com o `mira-postgres` do `ia` (5432).

??? note "Caminho legado (só o `ia`)"
    O `ia` ainda tem o carregador antigo (`docker compose up -d` e `python -m mira.repositorios.postgres carregar`). Use-o só para reproduzir etapas antigas; a base oficial é a do `mira-dados`.

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
