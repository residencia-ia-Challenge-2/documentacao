# Documentação inicial — Grupo 13

Documentação do projeto de controle nutricional com IA, construída com **MkDocs + Material** a partir de [Definições do Projeto](docs/referencias/definicoes-projeto.pdf) e [YOLOv8 Nutrition Analysis Pipeline](docs/referencias/pipeline-yolov8.pdf).

O conteúdo apresenta a ideia, o escopo, o público, os requisitos, a arquitetura, os riscos e as metas do projeto.

O projeto utiliza **YOLOv8 para visão computacional** e **`qwen3:8b` como único modelo de linguagem**, com testes locais do LLM.

## GitHub Pages

Endereço da documentação: <https://residencia-ia-challenge-2.github.io/documentacao/>.

O workflow `.github/workflows/pages.yml` gera e valida o site e publica no GitHub Pages a cada push em `main`. Também pode ser executado manualmente em **Actions → Publicar documentação → Run workflow**.

Para habilitar a hospedagem no repositório, selecione **Settings → Pages → Build and deployment → Source → GitHub Actions**. Acompanhe a execução em **Actions**; o endereço fica disponível após a publicação bem-sucedida.

## Estrutura

```text
docs/
  index.md          Visão geral e ideia do projeto
  projeto.md        Público, personas e questões norteadoras
  requisitos.md     Requisitos funcionais e não funcionais
  arquitetura.md    Componentes e fluxos de processamento
  riscos.md         Riscos, mitigações e premissas
  metricas.md       Metas e indicadores de sucesso
  fontes.md         Referências documentais
  referencias/      PDFs de referência
mkdocs.yml          Configuração e navegação do site
requirements-docs.txt  Dependência da documentação
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
- As referências estão em `docs/fontes.md`.
- Os PDFs de referência ficam em `docs/referencias/`.

Referências da ferramenta: [MkDocs](https://www.mkdocs.org/getting-started/) e [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).
