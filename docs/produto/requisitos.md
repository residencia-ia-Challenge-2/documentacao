# Requisitos

## Requisitos funcionais

| ID | Requisito |
| --- | --- |
| RF01 | Permitir o registro de refeições por captura fotográfica do prato ou escaneamento de código de barras de produtos industrializados. |
| RF02 | Realizar segmentação de instâncias para mapear contornos e áreas de múltiplos alimentos em uma única imagem, utilizando YOLOv8-Seg. |
| RF03 | Empregar LLM para calcular, estruturar e integrar os dados visuais de volume ao cruzamento automatizado com as bases TACO, TBCA e Open Food Facts, determinando calorias e macronutrientes. |
| RF04 | Permitir ajuste e edição manual dos itens identificados pela IA. |
| RF05 | Manter histórico das refeições agrupadas por data e classificação (café da manhã, almoço, lanche e jantar). |
| RF06 | Extrair dados via OCR a partir da foto da tabela nutricional caso o código de barras não conste nas bases integradas (TACO, TBCA e Open Food Facts), como mecanismo de *fallback*. |
| RF07 | Identificar talheres (ex.: garfo) na imagem como referência visual de escala para converter a área identificada em volume e peso estimado. |
| RF08 | Oferecer visualização qualitativa da refeição baseada na distribuição por grupos alimentares e equilíbrio do prato, sem dependência de contagem numérica rígida. |

!!! danger "Conflito: quem calcula os números (RF03)"
    O RF03 diz que a **LLM calcula** calorias e macronutrientes. A regra fixa da arquitetura atual diz o contrário: **"Números só da base. A partir da Etapa 2, a LLM não gera calorias nem macros"** — o cálculo é feito em código (`gramas × TACO por 100 g`). Ver [Regras fixas](../architecture/decisoes.md).

    !!! warning "TODO"
        A equipe precisa reescrever o RF03 para refletir a decisão vigente (ou registrar um ADR revertendo a regra).

## Requisitos não funcionais

| ID | Categoria | Requisito |
| --- | --- | --- |
| RNF01 | Desempenho | Tempo de processamento exclusivo do backend e da IA inferior a **3 segundos** entre o envio da imagem e a entrega dos dados processados. |
| RNF02 | Usabilidade | Fluxo principal concluído em até **3 toques** na tela do dispositivo móvel. |
| RNF03 | Acurácia | Modelo de visão computacional com acurácia mínima de **85%** na identificação de alimentos comuns. |
| RNF04 | Segurança e privacidade | Por serem dados sensíveis de saúde e imagens pessoais, armazenamento e processamento exigem **consentimento explícito** do usuário, em cumprimento à LGPD. |
| RNF05 | Escalabilidade e disponibilidade | API do backend com disponibilidade mínima de **99,5%** e suporte a requisições concorrentes. |

!!! note "Mudou na v2"
    - **RF02** passou de "segmentar e identificar com YOLOv8" para "segmentação de instâncias com YOLOv8-Seg".
    - **RF03** passou de "calcular com base no volume e nas tabelas TACO/TBCA" para "empregar LLM … TACO, TBCA e Open Food Facts".
    - **RF06, RF07 e RF08** são novos.
    - **RNF01** passou a medir só backend + IA (antes: da captura à exibição).
    - **RNF04** passou a exigir consentimento explícito.
    - **RNF05** é novo.

## Rastreabilidade com o MVP

Correspondência entre os requisitos e o que a [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf) descreve como implementado.

| Requisito | Situação no MVP (texto livre) | Observação |
| --- | --- | --- |
| RF01 | Parcial | Entrada atual é texto livre; foto na fase 2 (`/v1/analisar-foto`); código de barras não aparece na Estrutura. |
| RF02, RF07 | Fase 2 | Responsabilidade da área de visão computacional. |
| RF03 | Implementado com outra abordagem | Cálculo determinístico em código, não pela LLM. |
| RF04 | Indício | Endpoint `/recalcular` existe; comportamento não documentado. |
| RF05 | Não documentado | — |
| RF06 | Não documentado | — |
| RF08 | Implementado (parcial) | Orientação qualitativa baseada no Guia Alimentar e resultado com grupos. |
| RNF01 | Não atendido | Latência p95 medida entre 17,5 s e 45,1 s (ver [Etapas](../architecture/etapas.md)). |
| RNF05 | Não atendido | Hoje um *lock* serializa os pedidos na API. |

!!! warning "TODO"
    Confirmar a situação de RF04, RF05, RF06 e do código de barras no MVP, e definir se RNF01/RNF05 se aplicam ao MVP de texto ou apenas ao produto final.

**Fontes:** [Definições do Projeto v2](../assets/pdfs/definicoes-projeto-v2.pdf), seção 5; [v1](../assets/pdfs/definicoes-projeto-v1.pdf), seção 5; [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 1, 2, 4 e 6.
