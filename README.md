# Mira (FoodScan) — Documentação · Grupo 13

Documentação do **Mira (FoodScan)**, análise nutricional de refeições com LLM, RAG e base TACO/IBGE (visão computacional na fase 2), construída com **MkDocs + Material**.

O conteúdo foi migrado dos PDFs do projeto, preservados em [`docs/assets/pdfs/`](docs/assets/pdfs/). O mapa da migração, as lacunas e as divergências entre documentos estão em [`docs/sobre/plano-migracao.md`](docs/sobre/plano-migracao.md).

## GitHub Pages

Endereço da documentação: <https://residencia-ia-challenge-2.github.io/documentacao/>.

O workflow `.github/workflows/pages.yml` gera e valida o site e publica no GitHub Pages a cada push em `main`. Também pode ser executado manualmente em **Actions → Publicar documentação → Run workflow**.

Para habilitar a hospedagem no repositório, selecione **Settings → Pages → Build and deployment → Source → GitHub Actions**. Acompanhe a execução em **Actions**; o endereço fica disponível após a publicação bem-sucedida.

## Estrutura

```text
docs/
  index.md            Visão geral, ideia central e estado atual
  produto/            Contexto, personas, requisitos, riscos e métricas
  getting-started/    Ambiente local, variáveis de ambiente, estrutura do repositório
  architecture/       Camadas, pipeline v5, etapas, regras fixas/ADRs, visão computacional
  guides/             Contribuir, testes, DVC, avaliação, nova etapa, deploy
  reference/          API, contratos Pydantic, banco de dados, glossário
  sobre/              Equipe, fontes e histórico, plano de migração
  assets/pdfs/        PDFs originais (somente histórico)
mkdocs.yml            Configuração, plugins e navegação do site
requirements-docs.txt Dependências da documentação
```

## Executar a documentação localmente

Na raiz deste repositório, com Python 3 e suporte a `venv` instalados:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-docs.txt
python -m mkdocs serve
```

Abra <http://127.0.0.1:8000>. Para encerrar, pressione `Ctrl+C`.

## Validar e gerar o site

```bash
source .venv/bin/activate
python -m mkdocs build --strict
```

O HTML será gerado em `site/`. Esse comando gera o site localmente; não o publica na internet.

## Editar

- O conteúdo fica em `docs/`, em Markdown.
- O menu e o tema ficam em `mkdocs.yml`.
- Diagramas são escritos em Mermaid (blocos ` ```mermaid `).
- Pendências usam `!!! warning "TODO"`.
- Os PDFs históricos ficam em `docs/assets/pdfs/`; fontes e divergências em `docs/sobre/fontes.md`.

Referências da ferramenta: [MkDocs](https://www.mkdocs.org/getting-started/) e [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).
