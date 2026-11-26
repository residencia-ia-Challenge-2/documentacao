# Fontes de dados

Toda fonte do Mira tem URL e SHA-256 registrados num manifesto, um arquivo `.dvc` no Git e uma entrada em `data/raw/FONTES.md`. Nenhuma fonte entra no Bronze sem passar antes pelo [protocolo de admissão](#protocolo-de-admissao).

## Inventário

| Fonte | Para que serve | Chave canônica | Licença | Situação |
| --- | --- | --- | --- | --- |
| **TACO 4ª ed.** (NEPA/Unicamp, 2011) | Composição de alimentos genéricos: kcal e macronutrientes por 100 g | `taco_<id>` | Reprodução permitida com citação | Em uso |
| **IBGE Medidas Referidas** (POF 2008-2009) | Conversão de medida caseira (concha, colher) em gramas | `(ibge_codigo, ibge_alimento, ibge_preparacao)` | Dado público, citar a fonte | Em uso |
| **Guia Alimentar** 2ª ed. (MS, 2014) | Orientação qualitativa (RAG), grupos do Guia e regras oficiais | `guia_<nnn>` | Publicação pública, citar a fonte | Em uso |
| **18 publicações do Ministério da Saúde** | Orientação qualitativa ampliada, com filtro por público e condição de saúde | `<documento_id>_<nnnn>` | Públicas; CC BY-NC nas recentes | Em uso |
| **Open Food Facts** (recorte Brasil) | Produtos industrializados por código de barras | `ean_<gtin>` | ODbL 1.0 (base) + DbCL 1.0 (conteúdo) | Na base (`produtos_ean`); o `backend` ainda usa a API ao vivo |
| Tokenizer do `granite-embedding:278m` | Corte dos trechos por tokens reais do modelo de embeddings | — | Do modelo | Em uso (Bronze) |
| Bluesoft Cosmos API | Marca e NCM quando faltarem no Open Food Facts | `ean_<gtin>` | A verificar (termos comerciais, exige token) | Planejada |
| OCR de rótulo (RDC 429/2020 + IN 75/2020) | Captura guiada da tabela nutricional no app | `ean_<gtin>` | Dado do usuário (LGPD) | Planejada |
| TBCA (USP) | Mais alimentos e nutrientes | `tbca_<id>` | A verificar antes de ingerir | Planejada |
| Datasets de imagens brasileiros (MyFood, Brazilian Food Images) | Visão computacional | `classe_visual → alimento_id` | Por dataset | Planejada |

O ingestor confere hoje **23 arquivos**: os 4 de `fontes_tabelas.csv` (TACO, IBGE, Open Food Facts e tokenizer) e os 19 documentos de `documentos.csv` (Guia + 18 publicações).

## O que cada fonte entrega

| Fonte | Arquivo no Bronze | Volume | Particularidades tratadas no ETL |
| --- | --- | --- | --- |
| TACO | `taco_4a_edicao_2011.xls` | 597 alimentos em 15 grupos | Cabeçalho de 3 linhas repetido a cada página; linha de grupo antes dos itens; marcadores `Tr`, `NA` e `*` |
| IBGE | `ibge_medidas_referidas_pof2008_2009.zip` | 11.801 medidas de 1.124 nomes | Cabeçalho na linha 4; o mesmo código IBGE é usado por nomes diferentes (exige chave composta); preparação `CROZIDO(A)`; `g` e `kg` ignorados |
| Guia Alimentar | `guia_alimentar_2ed.pdf` | 147 trechos (pp. 11–129) + 1 parte do corte por tokens | Estrutura lida pela tipografia do PDF (PyMuPDF); um trecho nunca cruza seção |
| 18 publicações do MS | `publicacoes_ms/` | 2.276 trechos | Tipografia relativa ao corpo do texto; cabeçalhos e rodapés repetidos em 25 % ou mais das páginas são removidos; Referências e Sumário ignorados |
| Open Food Facts | `off_brasil.parquet` | 38.425 produtos → 26.389 aprovados (68,7 %) | Origem fixada por commit e SHA-256; sem campos de contribuidor; validação física (Atwater, kJ/kcal, faixas) |

??? info "As 19 publicações da base documental"
    `guia` (Guia Alimentar 2ª ed.), `prot1` a `prot5` (Protocolos de uso do Guia, fascículos 1 a 5), `regionais` (Alimentos regionais brasileiros), `crianca2` (Guia alimentar para crianças, versão resumida), `cab35`, `cab36`, `cab37` e `cab38` (Cadernos de Atenção Básica: doença crônica, diabetes, hipertensão e obesidade), `cardio` (Alimentação cardioprotetora), `atividade` (Guia de atividade física), `marcadores` (Marcadores de consumo alimentar), `vigilancia` (Marco de referência da vigilância alimentar), `instrutivo` (Instrutivo de alimentação e nutrição na atenção básica), `pnan` (Política Nacional de Alimentação e Nutrição) e `terapia` (Manual de terapia nutricional). A lista com URL e hash está em `data/curadoria/especialista/documentos.csv`.

## Manifestos

Os manifestos são a fonte da verdade sobre de onde vem cada arquivo.

| Manifesto | Fontes | Colunas principais |
| --- | --- | --- |
| `data/curadoria/nutricional/fontes_tabelas.csv` | TACO, IBGE, Open Food Facts, tokenizer | `fonte_id`, `arquivo`, `url`, `sha256`, `licenca`, `metodo` (`download` ou `recorte_off`), `revisao`, `sha256_origem` |
| `data/curadoria/especialista/documentos.csv` | Guia + 18 publicações do MS | `documento_id`, `arquivo`, `url`, `sha256`, `tema`, `relevancia`, `status` |

`python -m mira_dados.ingerir` lê os dois manifestos, baixa só o que falta e **falha** se algum hash divergir. Um arquivo já presente com hash diferente não é sobrescrito.

## Open Food Facts em detalhe

O Open Food Facts é a fonte mais nova e a única colaborativa, por isso tem tratamento próprio.

**Recorte em vez do dump inteiro.** O dump mundial tem cerca de 8 GB. O Bronze do Mira é só o recorte Brasil (4,3 MB), gerado de forma determinística por `python -m mira_dados.ingerir_off`:

1. A origem é o `food.parquet` do dataset `openfoodfacts/product-database` no Hugging Face, num **commit fixo** (`09cc740…`), conferido pelo SHA-256 publicado.
2. Uma consulta DuckDB fixa seleciona `countries_tags ∋ 'en:brazil'`, ordenada por `code` e `last_modified_t`.
3. Só entram colunas de uma **lista de permissão**. Nenhum campo de contribuidor (`creator`, `editors`, `photographers` etc.) chega ao Bronze, por causa da LGPD.
4. O recorte tem SHA-256 próprio no manifesto, conferido em 3 execuções idênticas.

O Parquet do Hugging Face foi escolhido no lugar do JSONL de `static.openfoodfacts.org` porque o JSONL é regerado todo dia na mesma URL, então não dá para fixar a versão.

**Licença.** A base está sob ODbL 1.0 e o conteúdo sob DbCL 1.0. Isso traz duas obrigações:

- **Atribuição** ("Open Food Facts — openfoodfacts.org") onde os dados forem exibidos.
- ***Share-alike*:** uma base derivada que for disponibilizada publicamente precisa ser oferecida sob ODbL. Por isso o Open Food Facts fica numa tabela própria (`produtos_ean`), sem se misturar com TACO e IBGE.

Imagens de produto (CC BY-SA) não são ingeridas.

**Nada é corrigido por chute.** Um produto com problema é rejeitado com o motivo registrado (`dq_motivos`), nunca consertado. Nulo continua nulo, e kcal ausente não é preenchida por `4P + 4C + 9L`. As regras de rejeição estão em [Pipeline ETL](etl.md#open-food-facts).

**Cobertura na Gold** (26.389 produtos): kcal 26.356 · carboidrato 25.610 · proteína 24.975 · lipídios 24.491 · porção em gramas 25.115 · sódio 17.260 · açúcares adicionados 9.999 · **grupo NOVA 6.632 (25 %)**. O grupo NOVA não é inferido quando falta.

## Protocolo de admissão

Toda fonte nova passa por este checklist **antes** de ter ETL, e a decisão fica registrada no [Registro de datasets](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/REGISTRO_DATASETS.md) do `mira-dados`:

- [ ] **Licença e termos de uso:** permissão explícita para uso no projeto, com as consequências registradas.
- [ ] **Chave canônica e rastreabilidade:** prefixo único de id (`taco_*`, `tbca_*`, `ean_*`) e URL + SHA-256 no manifesto.
- [ ] **Contrato e Quality Gate:** schema Pandera da Silver e regras físicas de bloqueio escritos antes de promover para o banco.
- [ ] **Impacto na avaliação:** nenhuma regra pode ser derivada das consultas de teste do `ia`.
- [ ] **LGPD:** nenhum campo identificável entra no Bronze.

## Histórico de aumento da base

| Data | Mudança | Volume |
| --- | --- | --- |
| 05/11/2026 | Base inicial do MVP (no `ia`): TACO + IBGE + Guia | 597 alimentos, 11.801 medidas, 147 trechos |
| 05/11/2026 | Base ampliada: + 18 publicações do MS (ADR-028) | 2.394 trechos de 19 documentos |
| 17/11/2026 | Migração do ETL para o `mira-dados` (Fase 1) | Mesmas fontes, Parquets idênticos aos do `ia` |
| 17/11/2026 | + Open Food Facts, recorte Brasil | 38.425 produtos no Bronze → 26.389 na Gold |
| 24/11/2026 | Corte por tokens reais, público e condição de saúde, regras oficiais | 2.424 trechos (nenhum acima de 512 tokens), 15 regras |

**Fontes:** documentação do `mira-dados`: [Fontes de dados](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/definicao/fontes.md), [Registro de datasets](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/REGISTRO_DATASETS.md) e [Bronze](https://github.com/residencia-ia-Challenge-2/data/blob/main/docs/pipelines/bronze.md); manifestos em `data/curadoria/`; `data/processed/relatorio_off.json`.
