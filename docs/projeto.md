# Projeto e público

## Cenário e problema

O projeto está inserido em saúde, nutrição e bem-estar digital. A proposta é automatizar o diário alimentar, substituindo a busca e a digitação manual por registros fotográficos e escaneamento de produtos.

O documento da equipe identifica como problema o abandono de aplicativos tradicionais de controle calórico e nutricional. Os fatores apontados são o esforço de digitação, a imprecisão na estimativa visual de porções e a falta de tempo no cotidiano.

Essa caracterização constitui a justificativa do projeto.

## Público-alvo

Praticantes de atividades físicas, pessoas em reeducação alimentar e pessoas com dietas restritivas que buscam praticidade no acompanhamento diário.

## Personas

| Persona | Contexto | Necessidade registrada |
| --- | --- | --- |
| Lucas, 24 anos, estudante de tecnologia | Pratica musculação e acompanha metas de macronutrientes. | Registrar a refeição em menos de 5 segundos, sem pesar ou digitar cada alimento individualmente. |
| Camila, 36 anos, arquiteta | Está em reeducação alimentar por orientação médica. | Entender a qualidade do prato em uma experiência simples, sem gerar ansiedade ou obsessão pela contagem numérica. |

## Stakeholders

| Categoria | Papel previsto |
| --- | --- |
| Desenvolvedores e engenheiros de IA/ML | Implementação do YOLOv8 para visão computacional, integração do backend com `qwen3:8b` e arquitetura de backend. |
| Designers de UX/UI | Interface intuitiva e jornada com poucos toques. |
| Usuários finais | Utilização do acompanhamento alimentar diário. |
| Nutricionistas e profissionais de saúde | Consultoria para validar bases nutricionais e parceria de divulgação. |
| Provedores de infraestrutura | Serviços de nuvem, visão computacional e IA; o documento cita GCP/AWS como exemplos. |

## Escopo funcional registrado

O escopo inclui captura fotográfica, leitura de código de barras, identificação de múltiplos alimentos, estimativa nutricional, edição manual e histórico das refeições.

O desenho técnico utiliza FastAPI e os fluxos de processamento descritos em [Arquitetura](arquitetura.md).

## Questões norteadoras

1. De que maneira é viável garantir a acurácia na estimativa de volume volumétrico em pratos misturados e heterogêneos utilizando modelos de visão computacional?
2. Quais estratégias de interface e experiência de usuário (UI/UX) devem ser adotadas para tratar incorreções de identificação sem gerar atrito ou frustração?
3. Qual é a arquitetura de telas estritamente necessária para viabilizar o fluxo completo de registro nutricional em até 3 toques?
4. Quais mecanismos de engajamento contínuo são eficazes para sustentar a retenção do usuário após o período de atração pela novidade tecnológica?

**Fontes:** Definições do Projeto, seções 1 a 5 e 9; diagrama técnico, página 1, para a referência arquitetural. Consulte os [documentos de referência](fontes.md).
