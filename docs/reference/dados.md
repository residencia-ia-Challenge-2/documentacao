# Banco de dados e fontes

## PostgreSQL + pgvector

Conteúdo da base:

| Conjunto | Uso no pipeline |
| --- | --- |
| Alimentos | Candidatos da busca e valores nutricionais por 100 g (TACO). |
| Medidas | Conversão de medida caseira em gramas (IBGE). |
| Sinônimos | Normalização e busca dos nomes de alimentos. |
| Trechos | Trechos do Guia Alimentar e publicações do MS usados na orientação. |

!!! warning "TODO"
    Documentar o schema: tabelas, colunas, tipos, chaves, índices full-text e vetoriais (dimensão dos embeddings). Um diagrama `erDiagram` em Mermaid é recomendado.

## Fontes de dados

| Fonte | Uso | Situação |
| --- | --- | --- |
| TACO | Valores nutricionais por 100 g. | Em uso |
| IBGE POF | Medidas caseiras → gramas. | Em uso |
| Guia Alimentar e publicações do Ministério da Saúde | Orientação qualitativa e grupos. | Em uso |
| TBCA | Alimentos fora da TACO. | Em avaliação (termos de uso) |
| Open Food Facts | Produtos industrializados por código de barras (RF03, RF06, P03). | Previsto nas Definições; não citado na Estrutura |
| USDA | Citado na P03 da v1 das Definições. | Removido na v2 |

O MVP cobre **29 alimentos**.

!!! warning "TODO"
    Listar os 29 alimentos do MVP, as versões/edições de cada fonte e as licenças de uso.

**Fontes:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 1, 3 e 6; [Definições do Projeto v2](../assets/pdfs/definicoes-projeto-v2.pdf), seções 5 e 7.
