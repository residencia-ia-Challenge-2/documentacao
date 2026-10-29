# Arquitetura e funcionamento

A arquitetura proposta organiza a aplicação em cliente, gateway e orquestração, processamento de imagens e produtos, inteligência e MLOps.

## Componentes

| Camada | Componentes | Responsabilidade |
| --- | --- | --- |
| Aplicação cliente | Câmera, leitura de código de barras e interface de resultados. | Registrar a entrada conforme o cenário e exibir o resultado nutricional. |
| Gateway e orquestração | FastAPI; endpoints `/analisar-prato/` e `/analisar-produto/`. | Receber a imagem ou o código e direcionar o processamento. |
| Visão computacional | YOLOv8-Seg, YOLOv8-Box e cálculo de escala. | Segmentar alimentos, detectar o garfo e produzir nomes e áreas em cm². |
| Produtos | Open Food Facts e motor OCR. | Recuperar metadados do produto ou extrair texto da tabela nutricional. |
| Modelo de linguagem | Prompt estruturado e `qwen3:8b`. | Receber os dados processados e gerar uma resposta textual estruturada em JSON. |
| Validação e retorno | Resposta JSON e validação de schema. | Validar a estrutura do resultado e retornar calorias e macronutrientes ao cliente. |
| MLOps e governança | Data lake, Roboflow/CVAT, Evidently AI/MLflow e CI/CD. | Armazenar imagens e logs, rotular dados, monitorar desvio de dados e executar testes de regressão. |

## Como funciona a análise de um prato

1. O usuário abre o aplicativo e seleciona o cenário de prato.
2. Captura uma foto do prato com um garfo como referência visual.
3. A imagem é enviada ao endpoint `/analisar-prato/`.
4. YOLOv8-Seg segmenta os alimentos da imagem.
5. YOLOv8-Box detecta o garfo.
6. O módulo de escala combina essas informações para converter pixels em medidas e produzir nomes de alimentos e áreas em cm².
7. A saída segue para a montagem do prompt estruturado na camada de inteligência.

### Porções e dados nutricionais

O requisito RF03 prevê cálculo de calorias e macronutrientes com base no volume estimado e nas tabelas TACO/TBCA. A mitigação do Risco 1 utiliza referência visual para escala pixel–centímetro e ajuste rápido por fração de prato.

O diagrama descreve a saída em nomes e áreas em cm², sem detalhar a conversão de área em volume e peso.

## Como funciona a análise de um produto

1. O usuário seleciona o cenário de produto e escaneia o código de barras.
2. O código é enviado como string ao endpoint `/analisar-produto/`.
3. O sistema consulta Open Food Facts.
4. Se o produto for encontrado, os metadados seguem para a camada de inteligência.
5. Se não for encontrado, o aplicativo solicita uma foto da tabela nutricional.
6. O motor OCR extrai o texto bruto da tabela e o encaminha para a montagem do prompt.

## LLM, RAG e resposta estruturada

O projeto utiliza **`qwen3:8b` como único modelo de linguagem**, com execução local para testes. Ele recebe dados textuais e estruturados: nomes e áreas dos alimentos identificados pelo YOLOv8, metadados dos produtos ou texto extraído por OCR.

A captura da foto é feita pelo aplicativo. A análise visual do prato, a segmentação dos alimentos e a detecção do garfo ficam com **YOLOv8**. O OCR extrai o texto das tabelas nutricionais. O `qwen3:8b` processa as informações produzidas por essas etapas para compor a resposta estruturada.

O processamento previsto segue esta sequência:

1. Montagem de um prompt estruturado.
2. Chamada ao `qwen3:8b` pelo backend.
3. Produção de resposta em JSON.
4. Validação do schema JSON pelo backend.
5. Retorno de JSON validado com calorias e macronutrientes, via HTTP 200.
6. Exibição dos resultados na interface do aplicativo.

A recuperação de conhecimento RAG não é detalhada no diagrama.

## Fontes nutricionais e armazenamento

| Fonte ou armazenamento | Uso descrito |
| --- | --- |
| TACO/TBCA | Base para o cálculo de calorias e macronutrientes previsto em RF03 e validação por profissionais de nutrição. |
| USDA | Citada junto de TACO/TBCA na premissa P03 sobre dados nutricionais. |
| Open Food Facts | Consulta de metadados de produtos pelo código de barras. |
| Data lake | Imagens e logs no contexto de MLOps e governança. |
| Histórico de refeições | Organização por data e tipo de refeição, conforme RF05. |

O requisito RNF04 estabelece proteção dos dados de saúde e das fotografias em conformidade com a LGPD.

## MLOps e monitoramento

O desenho técnico inclui:

- **Roboflow / CVAT:** rotulagem de erros e novos pratos.
- **Evidently AI / MLflow:** monitoramento de desvio de dados (data drift).
- **Data lake:** imagens e logs.
- **Pipeline CI/CD:** testes de regressão do YOLOv8 e da integração com `qwen3:8b`.

Os [riscos e premissas](riscos.md) contextualizam as limitações de porções, ingredientes não visíveis e consulta de produtos. As [metas e indicadores](metricas.md) apresentam os objetivos de qualidade e uso.

**Fontes:** YOLOv8 Nutrition Analysis Pipeline, página 1; Definições do Projeto, seções 5, 6 e 7. Consulte as [referências](fontes.md).
