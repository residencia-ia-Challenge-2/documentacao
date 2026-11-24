# Avaliar um pipeline

```bash
python eval/run_eval.py --pipeline vN
```

Substitua `vN` pelo pipeline a avaliar (ex.: `v5`).

## Componentes

| Item | Descrição |
| --- | --- |
| `eval/queries.jsonl` | Conjunto anotado de refeições (15 hoje; meta de 40). |
| `eval/run_eval.py` | Avaliador comum a todas as etapas. |
| Juiz da orientação | LLM-juiz que avalia a orientação qualitativa. |
| Resultados | Gravados em JSON. |

## Comparação justa

Para que os números sejam comparáveis com as outras etapas: **mesmo conjunto, mesmo modelo, mesma base**.

## Anotar novas refeições

Tarefa de todos: anotar as **25 refeições novas** — cada pessoa anota e outra revisa.

!!! warning "TODO"
    Documentar o formato de uma linha de `queries.jsonl`, onde os resultados JSON são gravados, o nome exato dos argumentos aceitos por `--pipeline` e como rodar o juiz da orientação.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 3, 4, 6 e 7.
