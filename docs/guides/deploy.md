# Deploy

## MVP (API + interface)

O deploy do MVP **ainda não existe**. O planejado pela área de Desenvolvimento + CI/CD:

- Dockerfiles para API e interface.
- Deploy automático a cada tag `etapa-N`.
- Melhorias de concorrência (hoje um *lock* serializa os pedidos) e de latência (cerca de 18 s: *streaming* e cache).

!!! warning "TODO"
    Definir ambiente de destino (nuvem, servidor da residência…), como a LLM é servida em produção e como os segredos são injetados.

## Esta documentação

O site é gerado com MkDocs + Material e publicado no GitHub Pages pelo workflow `.github/workflows/pages.yml` a cada push em `main` (ou manualmente em **Actions → Publicar documentação → Run workflow**).

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-docs.txt
python -m mkdocs serve            # http://127.0.0.1:8000
python -m mkdocs build --strict   # valida e gera site/
```

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seção 6; `README.md` deste repositório.
