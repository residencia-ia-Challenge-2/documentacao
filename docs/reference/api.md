# API HTTP

API em FastAPI (`src/mira/api/app.py`), servida localmente com:

```bash
uvicorn mira.api.app:app --port 8000
```

## Endpoints

| Método | Rota | Descrição | Situação |
| --- | --- | --- | --- |
| TODO | `/analisar-refeicao` | Analisa uma refeição em texto livre e devolve itens, gramas, kcal, macros e grupos. | MVP |
| TODO | `/orientar` | Gera a orientação qualitativa baseada no Guia Alimentar. | MVP |
| TODO | `/recalcular` | TODO: finalidade não documentada nos PDFs. | MVP |
| TODO | `/v1/analisar-foto` | Recebe uma foto e a converte em `list[ItemExtraido]`. | Fase 2 |

!!! warning "TODO"
    Para cada endpoint, documentar: método HTTP, corpo da requisição, resposta de sucesso, códigos de erro e um exemplo com `curl`. Confirmar a finalidade de `/recalcular`. Avaliar se a documentação automática do FastAPI (`/docs`, `/openapi.json`) pode ser exportada para esta página.

??? example "Modelo para documentar um endpoint"

    ```markdown
    ### POST /analisar-refeicao

    **Requisição**

        { "texto": "2 conchas de feijão e 1 filé de frango" }

    **Resposta 200**

        { ... ResultadoRefeicao ... }

    **Erros**

    | Código | Quando |
    | --- | --- |
    | 422 | Corpo inválido |
    ```

    Os campos acima são ilustrativos e precisam ser confirmados no código.

## Limitações conhecidas

- Concorrência: hoje um *lock* serializa os pedidos.
- Latência de cerca de 18 s por pedido.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 2, 6 e 7.
