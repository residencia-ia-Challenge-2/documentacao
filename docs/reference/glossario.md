# Glossário

ADR
:   *Architecture Decision Record*. Registro de uma decisão de arquitetura; as regras fixas só mudam com um ADR.

BM25
:   Algoritmo de ranqueamento por palavras-chave (busca full-text).

Bronze, Silver, Gold
:   Camadas dos dados no `mira-dados`: fontes brutas (`data/raw/`), dados limpos (`data/interim/`) e base de serving (`data/processed/`).

Curadoria
:   Decisões humanas sobre os dados (ligações, sinônimos, grupos do Guia), versionadas no Git em `data/curadoria/`, cada linha com um `status`.

Data Quality Gate
:   Conjunto de regras do `mira-dados` que valida a Gold antes da publicação. Uma regra bloqueante reprovada impede a carga no banco.

DVC
:   *Data Version Control*. Versiona dados e reproduz o ETL com `dvc repro`.

Embeddings
:   Representações vetoriais de texto usadas na busca semântica.

Etapa
:   Pipeline completo e comparável, avaliado no mesmo conjunto de teste (v1 a v5).

Guia Alimentar
:   Guia Alimentar para a População Brasileira, do Ministério da Saúde; base da orientação qualitativa.

IBGE POF
:   Pesquisa de Orçamentos Familiares do IBGE; fonte das medidas caseiras.

LGPD
:   Lei Geral de Proteção de Dados Pessoais.

LLM-juiz
:   LLM usada para avaliar a qualidade da orientação gerada.

MAPE
:   *Mean Absolute Percentage Error*. Erro percentual absoluto médio.

mira-dados
:   Nome do pacote do repositório `data`, dono do ciclo de vida dos dados do Mira.

Open Food Facts
:   Base colaborativa de produtos industrializados (licença ODbL). O Mira usa o recorte Brasil, com busca por código de barras.

Ollama
:   Ferramenta para executar LLMs localmente.

p95
:   Percentil 95 da latência.

pgvector
:   Extensão do PostgreSQL para busca vetorial.

RAG
:   *Retrieval-Augmented Generation*. Geração aumentada por recuperação.

Recall@3
:   Proporção de casos em que o item correto está entre os 3 primeiros resultados da busca.

RRF
:   *Reciprocal Rank Fusion*. Combina rankings de buscas diferentes (full-text e vetorial).

TACO
:   Tabela Brasileira de Composição de Alimentos (NEPA/Unicamp).

TBCA
:   Tabela Brasileira de Composição de Alimentos (USP/FoRC).

YOLOv8
:   Família de modelos de detecção e segmentação de objetos; previsto para a fase 2.
