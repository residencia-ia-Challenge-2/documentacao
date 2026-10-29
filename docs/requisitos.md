# Requisitos

Os requisitos definem as funcionalidades e as metas de qualidade do sistema.

## Requisitos funcionais

| ID | Requisito |
| --- | --- |
| RF01 | Permitir registro por captura fotográfica do prato ou leitura do código de barras de produtos industrializados. |
| RF02 | Segmentar e identificar múltiplos alimentos na mesma imagem utilizando YOLOv8. |
| RF03 | Calcular automaticamente calorias, proteínas, carboidratos e gorduras com base no volume estimado e nas tabelas TACO/TBCA. |
| RF04 | Permitir ajuste e edição manual dos itens identificados pela IA. |
| RF05 | Manter histórico das refeições por data e classificação: café da manhã, almoço, lanche e jantar. |

## Requisitos não funcionais

| ID | Categoria | Meta registrada |
| --- | --- | --- |
| RNF01 | Desempenho | Menos de 3 segundos entre a captura da foto e a exibição do resultado nutricional processado. |
| RNF02 | Usabilidade | Conclusão do fluxo principal em até 3 toques. |
| RNF03 | Acurácia | Pelo menos 85% na identificação de alimentos comuns. |
| RNF04 | Segurança e privacidade | Armazenamento e processamento dos dados de saúde e das fotos em conformidade com a LGPD. |

**Fonte:** Definições do Projeto, seção 5. Consulte as [referências](fontes.md).
