# Dados e DVC

## Camadas de dados

| Pasta | Regra |
| --- | --- |
| `data/raw/` | Fontes originais (TACO, IBGE POF, Guia Alimentar, publicações do MS), versionadas com DVC e descritas em `FONTES.md`. |
| `data/curadoria/` | Decisões humanas versionadas no Git: alimentos do MVP, ligações TACO↔IBGE, sinônimos, grupos do Guia. |
| `data/interim/`, `data/processed/` | Gerados por `dvc repro`. **Nunca editar à mão.** |

## Regenerar os dados

```bash
dvc repro
```

Depois, recarregar o banco:

```bash
python -m mira.repositorios.postgres carregar
```

## LGPD

Nenhuma refeição real de usuário é versionada.

## Pendências da Engenharia de dados

- Remoto do DVC (Drive ou S3) para usar `dvc pull`.
- TBCA para os alimentos fora da TACO (verificar os termos de uso).
- Medidas que faltam (iogurte; "unidade" × "bife"/"filé").
- Filtro por público na base ampliada; pool de conexões.

!!! warning "TODO"
    Documentar os estágios do `dvc.yaml`, como adicionar um novo alimento à curadoria e como adicionar uma nova medida caseira.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 3, 5 e 6.
