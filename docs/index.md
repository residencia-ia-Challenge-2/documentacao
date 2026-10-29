# Controle nutricional com inteligência artificial

**Grupo 13 · Saúde, nutrição e bem-estar digital**

O projeto propõe simplificar o diário alimentar por meio de fotografias de refeições e leitura de códigos de barras de produtos industrializados. O objetivo é reduzir o esforço de registro e apoiar o acompanhamento nutricional diário.

## Questão central

> Como podemos usar a inteligência artificial para tornar o controle nutricional diário uma experiência simples, precisa e sem atritos, substituindo a digitação manual de alimentos?

## Objetivo e limites

Automatizar o diário alimentar para reduzir a digitação manual e apoiar o acompanhamento nutricional. O problema, as personas e as necessidades estão em [Projeto e público](projeto.md).

A estimativa de porções e a identificação de ingredientes invisíveis são limitações reconhecidas do projeto.

## O que a solução prevê

1. Registrar uma refeição por foto ou um produto por código de barras.
2. Identificar os alimentos e estimar calorias e macronutrientes.
3. Permitir correções manuais dos resultados.
4. Organizar o histórico por data e tipo de refeição.

## Inteligência artificial

- **YOLOv8:** visão computacional para identificar e segmentar alimentos nas fotos dos pratos e detectar o garfo usado como referência de escala.
- **`qwen3:8b`:** único LLM do projeto, responsável por processar os dados textuais e estruturados e compor a resposta em JSON. Os testes do modelo de linguagem serão executados localmente.

Os fluxos de processamento estão descritos em [Arquitetura](arquitetura.md).

## Navegação

- [Projeto e público](projeto.md): problema, personas e stakeholders.
- [Requisitos](requisitos.md): funcionalidades e metas de qualidade.
- [Arquitetura](arquitetura.md): componentes do diagrama, fluxos e dependências.
- [Riscos e premissas](riscos.md): limitações previstas e mitigações propostas.
- [Metas e indicadores](metricas.md): desempenho, registro e satisfação dos usuários.
- [Referências](fontes.md): documentos que fundamentam o projeto.

**Fontes:** Definições do Projeto, seções 1, 2, 5 e 10; diagrama técnico, página 1. Consulte os [documentos de referência](fontes.md).
