# Riscos e premissas

## Riscos identificados

O projeto identifica os seguintes riscos e estratégias de mitigação.

| Identificação | Risco | Impacto | Mitigação |
| --- | --- | --- | --- |
| Risco 1 | Imprecisão de volume e peso em imagens bidimensionais. | Alto | Usar referência visual, como um talher, para escala pixel–centímetro e oferecer ajuste rápido por fração do prato. |
| Risco 2 | Dificuldade de identificar ingredientes não visíveis, como óleos, manteiga e temperos. | Médio | Disponibilizar formulário simples e opcional após a captura para indicar o modo de preparo. |
| Risco 3 | Falha na leitura de código de barras ou produto ausente na base. | Baixo | Oferecer OCR da tabela nutricional da embalagem como alternativa. |

## Premissas registradas

| ID | Premissa |
| --- | --- |
| P01 | Os usuários possuem smartphone com câmera funcional e conexão contínua à internet durante o uso. |
| P02 | YOLOv8 permite segmentar múltiplos alimentos com latência aceitável para aplicações móveis. |
| P03 | A integração de TACO, TBCA e USDA assegura validade e precisão dos dados exibidos. |

As premissas representam as condições consideradas no planejamento do projeto.

## Limitações já reconhecidas

O projeto reconhece que uma foto pode não revelar ingredientes e que pratos misturados ou heterogêneos dificultam a estimativa de volume. Por isso, o ajuste manual e a investigação dos métodos de preparo fazem parte do planejamento.

Consulte o [funcionamento da arquitetura](arquitetura.md) e as [metas e indicadores](metricas.md).

**Fonte:** Definições do Projeto, seções 6, 7 e 9. Consulte os [documentos de referência](fontes.md).
