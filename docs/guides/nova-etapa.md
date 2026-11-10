# Criar uma nova etapa

Uma etapa é um **pipeline completo** que implementa o contrato `Pipeline` e é avaliado no mesmo conjunto que as demais.

## Passos

1. Registrar a motivação como questão em aberto ou ADR em `DESENVOLVIMENTO.md`.
2. Criar a implementação em `src/mira/pipelines/` seguindo o padrão de nomes (`v1_baseline` … `v5_rag_completo`).
3. Implementar o contrato:

    ```python
    class Pipeline(Protocol):
        nome: str
        def analisar(self, texto: str) -> ResultadoRefeicao: ...
    ```

4. Se precisar de acesso à base, usar `RepositorioAlimentos` — não acessar o banco diretamente.
5. Garantir as [regras fixas](../architecture/decisoes.md): números só da base, escolha só entre candidatos, Pydantic em toda saída de LLM.
6. Adicionar testes (sem rede, LLM falsa).
7. Avaliar: `python eval/run_eval.py --pipeline vN`.
8. **Não apagar** as etapas anteriores.
9. Atualizar `DESENVOLVIMENTO.md` e criar a tag `etapa-N`.

!!! warning "TODO"
    Documentar como um novo pipeline é registrado para ser encontrado por `run_eval.py` e pela API ("configurável" na Etapa 6).

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 2, 4, 5 e 7.
