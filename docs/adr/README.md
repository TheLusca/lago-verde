# ADRs do Projeto Lago Verde

Este diretório registra as principais decisões arquiteturais do projeto em formato de `Architecture Decision Record (ADR)`.

## Objetivo

Os ADRs servem para:

- registrar decisões técnicas importantes;
- explicar o contexto e os motivos da decisão;
- documentar alternativas avaliadas;
- deixar explícitas as consequências e trade-offs;
- facilitar alinhamento entre equipe técnica, gestão e stakeholders.

## Convenções adotadas

- Um arquivo por decisão.
- Numeração sequencial no nome do arquivo: `0001`, `0002`, `0003`, etc.
- Título curto e objetivo no nome do arquivo.
- Status explícito em cada ADR, por exemplo:
  - `Proposto`
  - `Aceito`
  - `Substituído`
  - `Descartado`

## Estrutura recomendada

Cada ADR deve conter, sempre que possível:

1. Título
2. Status
3. Data
4. Contexto
5. Decisão
6. Justificativa
7. Alternativas consideradas
8. Consequências
9. Próximos passos ou dúvidas em aberto
10. Referências

## ADRs existentes

- [ADR-0001 - Adotar Django + Wagtail + PostgreSQL como stack candidata para a PoC](./0001-adotar-django-wagtail-postgresql-para-poc.md)
