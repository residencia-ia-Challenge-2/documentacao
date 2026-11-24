# Rodar testes e lint

```bash
ruff check . && pytest
```

- A suíte tem **94 testes** (na data da Estrutura do Mira, 10/11/2026).
- Os testes rodam **sem rede** e com **LLM falsa** — não é preciso ter o Ollama nem o PostgreSQL ativos.
- O cálculo nutricional (`nutricao/`) é testado com valores conhecidos da TACO.

## No CI

O pipeline de CI ainda **não existe**. O planejado (GitHub Actions) é:

- [ ] `ruff`
- [ ] `pytest`
- [ ] `dvc status`
- [ ] build da imagem Docker
- [ ] `main` protegida

!!! warning "TODO"
    Documentar como ativar a LLM falsa fora dos testes, como rodar testes de integração (com Ollama e PostgreSQL reais), se existem marcadores do pytest e a meta de cobertura.

**Fonte:** [Estrutura do Mira](../assets/pdfs/estrutura-mira.pdf), seções 3, 6 e 7.
