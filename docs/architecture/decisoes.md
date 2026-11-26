# Regras fixas e ADRs

## Regras fixas

Estas regras **só mudam com um ADR** (Architecture Decision Record).

| Regra | Descrição |
| --- | --- |
| Números só da base | A partir da Etapa 2, a LLM não gera calorias nem macros. |
| Escolha só entre candidatos | Um id fora da lista rejeita a resposta. |
| Toda saída de LLM passa por Pydantic | Antes de ser usada. |
| Não inventar dados | O que falta vira questão em aberto. |
| Etapas não se apagam | Todas continuam rodando, para que a comparação possa ser refeita. |
| Comparação justa | Mesmo conjunto, mesmo modelo, mesma base. |
| Reprodutível | `dvc repro` e um comando por etapa. |
| Segredos no `.env` e LGPD | Nenhuma refeição real de usuário é versionada. |

Mudanças em `dominio/` ou `nutricao/` afetam todas as etapas e também exigem ADR.

O `mira-dados` tem as próprias regras fixas para a base (nenhum número vem de LLM, nulo nunca vira zero, texto indexado sem números, avaliação fora da base, LGPD), cada uma com verificação automática. Ver [Banco de dados e fontes](../reference/dados.md#regras-fixas-dos-dados).

## Registro de decisões

Os ADRs ficam no diário técnico `docs/DESENVOLVIMENTO.md`, no repositório `ia`. As decisões de engenharia de dados (D01 a D31) ficam em [Decisões de engenharia de dados](../dados/decisoes.md).

| ADR | Decisão | Situação |
| --- | --- | --- |
| ADR-014 | LLM de todas as etapas: Qwen 2.5 7B local (Ollama). | Em vigor |

!!! warning "TODO"
    Migrar para cá (ou linkar) a lista completa de ADRs (ADR-001 a ADR-014 e seguintes), com contexto, decisão e consequências. Hoje apenas o ADR-014 é citado nos documentos de origem.

??? example "Modelo de ADR"

    ```markdown
    # ADR-NNN: <título>

    - **Data:** AAAA-MM-DD
    - **Situação:** proposto | aceito | substituído por ADR-XXX
    - **Autores:** <nomes>

    ## Contexto
    <problema e restrições>

    ## Decisão
    <o que foi decidido>

    ## Consequências
    <impacto nas etapas, na comparação e no código>
    ```

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 4, 5 e 7.
