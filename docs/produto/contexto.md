# Contexto e público

## Cenário

O projeto insere-se no contexto de **saúde, nutrição e bem-estar digital**. Com a rotina cada vez mais dinâmica, manter uma alimentação saudável e acompanhar a nutrição tornaram-se desafios diários. O cenário escolhido foca na **automação do diário alimentar**, substituindo a busca e a digitação manual de itens por uma experiência fluida baseada no registro fotográfico do prato ou no escaneamento de produtos industrializados.

## Problema

Alta taxa de desistência e abandono de aplicativos tradicionais de controle calórico e nutricional, motivada por:

- elevada fricção na digitação manual de alimentos;
- imprecisão na estimativa visual de porções;
- falta de tempo dos usuários no dia a dia.

## Público-alvo

Praticantes de atividades físicas, pessoas em reeducação alimentar ou com dietas restritivas que buscam praticidade no acompanhamento diário.

## Mapa de stakeholders

| Categoria | Papel e responsabilidades |
| --- | --- |
| Desenvolvedores e engenheiros de IA/ML | Implementação dos modelos YOLOv8, integração com APIs de modelos de linguagem (LLM) e arquitetura de backend. |
| Designers de UX/UI | Interface intuitiva e jornada do usuário com a mínima quantidade de toques. |
| Usuários finais (público-alvo) | Ver [Público-alvo](#publico-alvo). |
| Nutricionistas e profissionais de saúde | Consultores técnicos para validação das bases nutricionais (TACO/TBCA) e parceiros estratégicos de divulgação.[^nutri] |
| Provedores de infraestrutura | Plataformas de nuvem (GCP/AWS) e serviços especializados de visão computacional e IA. |
| Órgãos reguladores / LGPD | Garantia de conformidade com a privacidade de dados e o tratamento de imagens dos usuários. |
| Corpo docente e mentores da Residência em IA | Acompanhamento pedagógico, orientação e avaliação técnica dos entregáveis. |

[^nutri]: Na v2 das Definições esta linha está **sem descrição**; o texto acima vem da v1. Na Estrutura do Mira, a equipe registra que uma pessoa ficará responsável por conseguir um nutricionista para validar medidas e densidades.

!!! warning "TODO"
    Confirmar a descrição do papel de **Nutricionistas e profissionais de saúde** (vazia na v2 das Definições).

!!! note "Mudou na v2"
    Foram adicionados os stakeholders **Órgãos reguladores / LGPD** e **Corpo docente e mentores**.

## Personas

=== "Pedro, 24 anos"

    **Estudante de tecnologia.** Treina musculação regularmente e precisa bater metas diárias de macronutrientes.

    **Necessidade:** registro completo em **menos de 8 segundos**, sem pesar ou digitar cada item consumido individualmente.

=== "Ana, 30 anos"

    **Arquiteta.** Está em reeducação alimentar por orientação médica.

    **Necessidade:** ferramenta simples, sem burocracia, que dê uma visão clara da **qualidade do prato** sem gerar ansiedade ou obsessão por contagem numérica.

!!! note "Mudou na v2"
    Na v1, as personas eram **Lucas, 24 anos** (meta de registro em menos de **5 segundos**) e **Camila, 36 anos**. A v2 renomeou as personas, alterou a idade da segunda e alinhou a meta da primeira ao KPI de 8 segundos.

## Questões norteadoras

1. De que maneira é viável garantir a acurácia na estimativa de volume em pratos misturados e heterogêneos utilizando modelos de visão computacional?
2. Quais estratégias de interface e experiência do usuário (UI/UX) devem ser adotadas para tratar incorreções de identificação sem gerar atrito ou frustração?
3. Qual é a arquitetura de telas estritamente necessária para viabilizar o fluxo completo de registro nutricional em até 3 toques?
4. Quais mecanismos de engajamento contínuo são eficazes para sustentar a retenção do usuário após o período de atração pela novidade tecnológica?

## Questão central

> Como podemos usar a inteligência artificial para tornar o controle nutricional diário uma experiência simples, precisa e sem atritos, substituindo a digitação manual de alimentos?

**Fonte:** [Definições do Projeto v2](../assets/pdfs/definicoes-projeto-v2.pdf), seções 1 a 4, 9 e 10.
