# Registro de datasets

Inventário das fontes de dados do Mira e histórico de **aumento da base**. Toda fonte nova passa pelo [protocolo de admissão](fontes.md#protocolo-de-admissao) **antes** de ter ETL e entra aqui com a decisão registrada. Hashes e URLs exatos ficam nos manifestos do repositório `data` (`data/curadoria/nutricional/fontes_tabelas.csv` e `data/curadoria/especialista/documentos.csv`); detalhes de parsing ficam em `data/raw/FONTES.md`.

!!! tip "Ao admitir ou atualizar uma fonte"
    Registre a decisão nesta página: uma linha no [Inventário](#inventario), uma linha no [Histórico](#historico-de-aumento) e, para fontes novas, uma seção de admissão como a do [Open Food Facts](#admissao-open-food-facts-recorte-brasil).

## Inventário

| # | Dataset | Domínio | Chave canônica | Licença | Bronze | Gold | Status | Admitido em |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | TACO 4ª ed. (NEPA/Unicamp, 2011) | Composição de alimentos genéricos | `taco_<id>` | Reprodução com citação | `taco_4a_edicao_2011.xls` | `alimentos.parquet` | Em uso | 05/11/2026 (no `ia`) |
| 2 | IBGE Medidas Referidas POF 2008-2009 | Medidas caseiras → gramas | `(ibge_codigo, ibge_alimento, ibge_preparacao)` | Pública, citar fonte | `ibge_medidas_referidas_pof2008_2009.zip` | `medidas_caseiras.parquet` | Em uso | 05/11/2026 (no `ia`) |
| 3 | Guia Alimentar 2ª ed. (MS, 2014) | Orientação qualitativa (RAG) | `guia_<nnn>` | Pública, citar fonte | `guia_alimentar_2ed.pdf` | `guia_trechos.parquet` | Em uso | 05/11/2026 (no `ia`) |
| 4 | 18 publicações do Ministério da Saúde | Orientação qualitativa ampliada | `<documento_id>_<nnnn>` | Pública; CC BY-NC nas recentes | `publicacoes_ms/` | `documentos_trechos.parquet` | Em uso | 05/11/2026 (no `ia`, ADR-028) |
| 5 | Open Food Facts, recorte Brasil | Industrializados por código de barras | `ean_<gtin>` | ODbL (base) + DbCL (conteúdo) | `off_brasil.parquet` | `produtos_ean.parquet` | Na base; ainda não consumido pelo `ia` | 17/11/2026 |
| 6 | Tokenizer do `granite-embedding:278m` | Corte dos trechos por tokens reais | — | Do modelo | `granite_embedding_278m_tokenizer.json` | — | Em uso | 24/11/2026 |
| 7 | Bluesoft Cosmos API | Marca e NCM quando faltarem no Open Food Facts | `ean_<gtin>` | A verificar (termos comerciais, exige token) | — | — | Planejado | — |
| 8 | OCR de rótulo (RDC 429/2020 + IN 75/2020) | Captura guiada no app | `ean_<gtin>` | Dado do usuário (LGPD) | — | — | Planejado | — |
| 9 | TBCA (USP) | Mais alimentos e nutrientes | `tbca_<id>` | A verificar antes de ingerir | — | — | Planejado | — |
| 10 | Datasets de imagens brasileiros (MyFood, Brazilian Food Images…) | Visão computacional | `classe_visual → alimento_id` | Por dataset | — | — | Planejado | — |

## Histórico de aumento

| Data | Mudança | Volume | Decisão ou motivo |
| --- | --- | --- | --- |
| 05/11/2026 | Base inicial do MVP (no `ia`): TACO + IBGE + Guia | 597 alimentos, 11.801 medidas, 147 trechos | Etapas 0 a 5 do `ia` |
| 05/11/2026 | Base ampliada: + 18 publicações do MS | 2.394 trechos de 19 documentos | ADR-028 do `ia` (20 fontes ou mais na sprint) |
| 17/11/2026 | Migração do ETL para o `mira-dados` (Fase 1) | Mesmas fontes, Parquets idênticos aos do `ia` | Separação entre ETL e aplicação |
| 17/11/2026 | + Open Food Facts, recorte Brasil | 38.425 produtos no Bronze → 26.389 na Gold (68,7 %) | Suprir a falta de industrializados e da leitura de código de barras |
| 24/11/2026 | Corte por tokens reais, público e condição de saúde, regras oficiais | 2.424 trechos (nenhum acima de 512 tokens), 15 regras | Prontidão para RAG e agentes |

## Admissão: Open Food Facts (recorte Brasil)

### Protocolo de admissão

- [x] **Licença e termos de uso:** base sob **ODbL 1.0** e conteúdo individual sob **DbCL 1.0** (README do dataset na revisão fixada). Consequências registradas:
    - **Atribuição obrigatória** ("Open Food Facts — openfoodfacts.org") onde os dados forem exibidos.
    - ***Share-alike*:** uma base derivada do Open Food Facts que for *disponibilizada publicamente* precisa ser oferecida sob ODbL. Por isso o Open Food Facts fica em tabela própria (`produtos_ean`), sem se misturar com TACO e IBGE.
    - Imagens de produto (CC BY-SA) **não** são ingeridas.
- [x] **Chave canônica e rastreabilidade:** `produto_id = ean_<gtin>`, só GTIN-8/12/13/14 com dígito verificador válido. A origem é o `food.parquet` do dataset `openfoodfacts/product-database` no Hugging Face, no **commit fixo** `09cc74034e7cb41b2f84c348e0dd5f4096aafa53`, conferido pelo SHA-256 publicado (`sha256_origem` no manifesto). O recorte gerado tem SHA-256 próprio e é versionado pelo DVC.
- [x] **Contrato e Quality Gate:** `OffSilverSchema` e `ProdutoEanGoldSchema` (Pandera) e regras físicas de bloqueio em `silver/off.py`, com parâmetros em `params.yaml` (`off.*`). Só produtos **sem nenhum motivo bloqueante** chegam à Gold.
- [x] **Impacto na avaliação:** nenhum. Não há regra derivada das consultas de avaliação; as regras são físicas (Atwater, faixas, kJ/kcal) e valem igualmente para todo produto.
- [x] **LGPD:** o dump traz identificadores de contribuidores (`creator`, `editors`, `last_editor`, `last_modified_by`, `photographers`, `informers_tags`, `checkers_tags`, `correctors_tags`, `owner`, `owner_fields`). O recorte usa **lista de permissão** de colunas, e nenhum desses campos entra no Bronze (testado em `tests/test_off.py`).

### Decisões de engenharia

1. **Recorte em vez do dump inteiro:** o dump mundial tem cerca de 8 GB (Parquet) ou 14 GB (JSONL). O Bronze do Mira é o recorte `countries_tags ∋ en:brazil`, com as colunas como vieram da fonte. É reprodutível porque a origem (commit + SHA-256) e a consulta são fixas (`ingerir_off.py`).
2. **Parquet do Hugging Face em vez do JSONL de `static.openfoodfacts.org`:** o JSONL é regerado diariamente na mesma URL e não dá para fixar a versão. O Parquet do Hugging Face tem commit imutável e hash publicado. O processamento usa DuckDB.
3. **Nada é corrigido por chute:** produto com problema é rejeitado com motivo registrado (`dq_motivos`), nunca consertado. Nulo continua nulo, e kcal ausente **não** é preenchida por `4P + 4C + 9L`.

### Resultado da primeira carga (17/11/2026, revisão `09cc740`)

| Etapa | Produtos |
| --- | ---: |
| Origem mundial (`food.parquet`, 8,06 GB, SHA-256 `9de6c4f4…cada8` conferido) | — |
| Bronze `off_brasil.parquet` (4,3 MB, SHA-256 `cf0c20e4…308b41`; recorte determinístico, conferido em 3 execuções) | 38.425 |
| Gold `produtos_ean.parquet` (sem nenhum motivo bloqueante) | **26.389 (68,7 %)** |

As rejeições por motivo e a cobertura de cada campo estão em [Pipeline ETL](etl.md#open-food-facts) e em [Fontes de dados](fontes.md#open-food-facts-em-detalhe).

**Conferência por amostragem das rejeições:** `atwater_divergente` pega sobretudo gordura gravada como 0 (por exemplo, sorvetes com lipídios = 0) e kcal = 0 com carboidrato > 0. `fora_da_faixa` pega kJ gravado como kcal em alimentos energéticos (por exemplo, 1.420 "kcal") e valores por embalagem. A faixa de `kj_como_kcal` foi estreitada de 3,5–5 para 3,9–4,5 porque a faixa larga confundia gordura zerada com kJ; o produto continua rejeitado, com o motivo certo.

### Pendências do Open Food Facts

- [x] Tabela `mira.produtos_ean` (B-tree em `gtin`) na migração Alembic e carga em `publicar_postgres.py`.
- [ ] **Revisão da equipe:** o status da fonte no manifesto está `proposto_agente` até a equipe validar regras e amostras.
- [ ] **NOVA só em 25 % da Gold:** não inferir. Avaliar o uso de `ingredients_tags` e `additives_tags` numa regra documentada, ou manter nulo.
- [ ] **Atribuição ODbL** na interface quando um produto do Open Food Facts for exibido.
- [ ] **Consumo pelo app:** o `backend` ainda consulta a API do Open Food Facts ao vivo.

### Como atualizar o recorte

1. Pegue o novo commit do dataset no Hugging Face e o SHA-256 do `food.parquet`.
2. Atualize `revisao`, `url` e `sha256_origem` em `fontes_tabelas.csv`.
3. Rode `python -m mira_dados.ingerir_off`, depois `dvc add data/raw/off_brasil.parquet`, e atualize o `sha256` do recorte no manifesto.
4. Rode `dvc repro` e registre a nova carga no [Histórico](#historico-de-aumento).

**Fontes:** manifestos em `data/curadoria/`, `data/raw/FONTES.md` e `data/processed/relatorio_off.json`, no repositório `data`.
