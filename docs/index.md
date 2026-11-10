# Mira (FoodScan)

**Grupo 13 · Challenge II · Residência em IA · Saúde, nutrição e bem-estar digital**

O **Mira** (nome técnico do projeto **FoodScan**) automatiza o diário alimentar. Na versão atual (MVP), a pessoa descreve a refeição em texto livre — por exemplo, *"2 conchas de feijão e 1 filé de frango"* — e o Mira devolve itens, gramas, calorias, macronutrientes e uma orientação qualitativa do prato, baseada no Guia Alimentar e em outras publicações do Ministério da Saúde.

A análise por **foto do prato** (visão computacional) é a **fase 2** do projeto.

## Ideia central

!!! abstract "A LLM entende e escolhe; os números vêm da base"
    A LLM interpreta o texto e **escolhe** entre alimentos candidatos. Os **números** vêm sempre da base de dados (TACO + medidas caseiras do IBGE) e são calculados em código determinístico:

    ```text
    nutriente = gramas × valor por 100 g
    ```

O repositório de código guarda a evolução do projeto em **etapas comparáveis**: cada etapa é um pipeline completo, avaliado no mesmo conjunto de teste. Veja [Etapas e avaliação](architecture/etapas.md).

## Questão central

> Como podemos usar a inteligência artificial para tornar o controle nutricional diário uma experiência simples, precisa e sem atritos, substituindo a digitação manual de alimentos?

## Estado atual

| Item | Situação |
| --- | --- |
| Etapas 0 a 6 (fundação, baseline, RAG, banco, orientação, MVP) | Concluídas |
| Etapa 7 (relatório comparativo) | Pendente |
| LLM utilizada | Qwen 2.5 7B local, via Ollama (ADR-014) |
| Visão computacional | Fase 2, em outro repositório |
| CI/CD do código | A criar |

!!! warning "Divergências entre documentos"
    Os documentos de origem descrevem duas visões do produto: a de **produto** (foto do prato e código de barras como entrada principal) e a **técnica atual** (texto livre no MVP, foto na fase 2). As divergências estão listadas em [Fontes e histórico](sobre/fontes.md#divergencias-entre-documentos) e precisam ser resolvidas pela equipe.

## Por onde começar

<div class="grid cards" markdown>

- :material-target: **[Produto](produto/index.md)** — problema, personas, requisitos, riscos e métricas.
- :material-rocket-launch: **[Primeiros passos](getting-started/index.md)** — subir o MVP localmente e entender o repositório.
- :material-sitemap: **[Arquitetura](architecture/index.md)** — camadas, pipeline v5, etapas e regras fixas.
- :material-book-open-variant: **[Guias](guides/index.md)** — contribuir, testar, avaliar e criar etapas.
- :material-code-json: **[Referência](reference/index.md)** — API, contratos Pydantic, banco e glossário.
- :material-account-group: **[Sobre](sobre/index.md)** — equipe, fontes e plano de migração da documentação.

</div>

**Fontes:** [Estrutura do projeto Mira](assets/pdfs/estrutura-mira.pdf) (10/11/2026); [Definições do Projeto v2](assets/pdfs/definicoes-projeto-v2.pdf), seção 10.
