# Variáveis de ambiente

Os segredos ficam no arquivo `.env`, que **não é versionado** (regra fixa: *"Segredos no `.env` e LGPD"*).

## Variáveis

| Variável | Obrigatória | Descrição | Exemplo |
| --- | --- | --- | --- |
| TODO | — | — | — |

!!! warning "TODO"
    Os documentos de origem não listam as variáveis. Preencher a tabela com, no mínimo:

    - conexão com o PostgreSQL (host, porta, usuário, senha, banco);
    - seleção do cliente de LLM (Ollama, Anthropic ou falso) e endereço do Ollama;
    - chave de API, caso o cliente Anthropic seja usado;
    - URL da API consumida pela interface Streamlit;
    - qualquer configuração do remoto do DVC.

    Recomenda-se versionar um `.env.example` sem valores reais no repositório de código.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 3 e 5.
