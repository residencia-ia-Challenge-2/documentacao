# Pipeline de uma refeição (v5)

O pipeline v5 (`v5_rag_completo`) transforma um texto livre em resultado nutricional com orientação.

```mermaid
flowchart LR
    T(["Texto livre<br/>'2 conchas de feijão…'"]) --> E
    E["1. Extração (LLM)<br/>nome · qtd · unidade<br/>→ ItemExtraido"]:::llm --> B
    B["2. Busca híbrida<br/>full-text + vetores + RRF<br/>→ 5 candidatos"]:::det --> C
    C["3. Escolha (LLM)<br/>só ids da lista<br/>(Literal ou null)"]:::llm --> K
    K["4. Cálculo (código)<br/>medida IBGE → gramas<br/>× TACO por 100 g"]:::det --> G
    G["5. Busca no Guia<br/>por grupo e processamento<br/>→ até 5 trechos"]:::det --> O
    O["6. Orientação (LLM)<br/>≤ 2 frases, sem números<br/>cada uma cita trecho_id"]:::llm --> R
    R(["Resultado<br/>kcal · macros · grupos<br/>orientação + fontes"])
    DB[("PostgreSQL + pgvector<br/>alimentos · medidas · sinônimos · trechos")]
    B -.-> DB
    K -.-> DB
    G -.-> DB

    classDef llm fill:#dbeafe,stroke:#3b82f6,color:#1e3a8a
    classDef det fill:#dcfce7,stroke:#16a34a,color:#14532d
```

**Azul:** passos em que a LLM entra, sempre com saída validada por Pydantic. **Verde:** passos determinísticos e auditáveis.

## Passo a passo

| # | Passo | Tipo | Entrada → saída | Garantia |
| --- | --- | --- | --- | --- |
| 1 | Extração | LLM | texto → lista de `ItemExtraido` (nome, quantidade, unidade) | Saída validada por Pydantic. |
| 2 | Busca híbrida | Código | nome do item → 5 candidatos | Full-text + vetores combinados por RRF. |
| 3 | Escolha | LLM | item + candidatos → id escolhido ou `null` | Só ids da lista (`Literal`); id fora da lista rejeita a resposta. |
| 4 | Cálculo | Código | id + quantidade + unidade → gramas, kcal, macros | Medida caseira IBGE → gramas; gramas × valor TACO por 100 g. |
| 5 | Busca no Guia | Código | grupos e grau de processamento → até 5 trechos | Trechos do Guia Alimentar / MS. |
| 6 | Orientação | LLM | itens + trechos → até 2 frases | Sem números; cada frase cita um `trecho_id`. |

!!! tip "Quando a medida caseira não existe"
    Se a medida caseira informada não existe na base, o item aparece como **"informe as gramas"** e **nenhum número é inventado**.

## Exemplo de resultado

!!! warning "TODO"
    Incluir um exemplo real de entrada e do JSON de `ResultadoRefeicao` retornado (campos, unidades e formato das fontes). Os documentos de origem não trazem o schema.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seção 1.
