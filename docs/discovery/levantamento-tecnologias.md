# Levantamento de Tecnologias

- `Data`: 19/07/2026
- `Responsável`: Lucas de Andrade - Engenheiro de Software
- `Status`: Em análise
- `Escopo`: avaliação inicial da stack para a PoC e para a primeira fase do Projeto Lago Verde, considerando as premissas operacionais da infraestrutura Maadix

## Objetivo

Este documento consolida a avaliação das tecnologias consideradas para o `Projeto Lago Verde`, com foco em aderência aos requisitos, facilidade operacional, experiência para usuários não técnicos e sustentabilidade técnica no médio e longo prazo.

## Contexto

Após o levantamento de requisitos, a solução proposta se aproxima de um `portal privado em formato de catálogo/wiki`, contendo:

- `home` pública;
- áreas privadas com autenticação e autorização;
- perfis distintos de usuário;
- catálogo estruturado;
- filtros simples e avançados de busca;
- artigos orientativos;
- painel administrativo para conteudistas;
- operação em infraestrutura própria.

Esse contexto exige equilíbrio entre:

- rapidez de implementação;
- facilidade de manutenção;
- interface leve e responsiva;
- facilidade de uso do CMS;
- clareza para implementação das regras de negócio atuais e futuras;
- simplicidade do fluxo de desenvolvimento até o deploy;
- segurança da aplicação;
- apoio à conformidade com a `LGPD`.

## Stack principal sugerida

### Sugestão de tecnologias alinhadas aos requisitos do projeto

- `Linguagem`: Python
- `Framework backend`: Django
- `CMS`: Wagtail
- `Banco de dados`: PostgreSQL
- `Frontend`: Django Templates + Tailwind CSS + HTMX
- `Servidor de aplicação`: Gunicorn
- `Infraestrutura`: Linux + Docker + Docker Compose
- `Publicação / proxy reverso`: Apache da Maadix com `VirtualHost` dedicado

## Ferramentas de apoio

- `Versionamento e repositório`: GitHub, GitLab ou repositório Git interno
- `Design e prototipação`: Figma

## Justificativa da escolha

- `Python + Django` oferecem uma base sólida para autenticação, permissões, regras de negócio e desenvolvimento de um sistema web robusto. Python contribui com simplicidade e produtividade, enquanto Django entrega uma base madura, segura e bem estruturada para aplicações web.
- `Wagtail` complementa o Django com um CMS amigável, permitindo que usuários não técnicos gerenciem conteúdo do catálogo, páginas e artigos com mais autonomia.
- `PostgreSQL` é um banco relacional estável e adequado para dados estruturados, como itens do catálogo, categorias, usuários, perfis e permissões.
- `Django Templates + Tailwind + HTMX` permitem construir uma interface responsiva, moderna e com boa usabilidade, sem exigir a complexidade de um frontend separado.
- `Gunicorn` é um servidor de aplicação simples e adequado para publicar o monólito Django/Wagtail dentro dos containers.
- `Linux + Docker` facilitam a padronização do ambiente, o deploy em infraestrutura própria e a manutenção do sistema.
- `Apache` já faz parte do modelo operacional informado pela infraestrutura da Maadix e pode publicar o novo portal por `VirtualHost`, sem exigir alteração estrutural no ecossistema atual.

## Premissas de infraestrutura da Maadix

Com base no feedback recebido da equipe de infraestrutura, a proposta passa a considerar as seguintes premissas:

- a aplicação poderá ser hospedada na infraestrutura atual da Maadix, desde que permaneça isolada dos demais serviços;
- o portal deverá operar em ambiente Docker próprio;
- o banco de dados será dedicado ao projeto;
- rede e volumes serão independentes;
- a arquitetura atual da Maadix não deve sofrer alterações estruturais por causa do novo portal;
- a publicação será feita por `VirtualHost` próprio no `Apache`, em linha com o modelo já utilizado para outros serviços;
- o novo serviço será tratado como aplicação customizada, com rotina operacional própria de deploy, atualização, backup e manutenção.

## Estratégia de ambientes

- `desenvolvimento`: execução local pelos desenvolvedores, usando `Docker Compose`;
- `homologação / staging`: VM Linux dedicada para validação da PoC, testes de integração e homologação com stakeholders;
- `produção`: VM Linux dedicada, isolada dos demais serviços e separada do ambiente de homologação.

Essa abordagem reduz risco operacional e facilita a validação do mesmo desenho técnico antes da entrada em produção.

## Publicação, domínio e controle de acesso

- o domínio ou subdomínio do portal será publicado pela infraestrutura da Maadix;
- o `Apache` será responsável por `VirtualHost`, `HTTPS` e proxy reverso;
- a aplicação `Django + Wagtail` será responsável por autenticação, autorização, sessão e controle de acesso às páginas privadas;
- a `home` permanecerá pública;
- as demais áreas do portal exigirão login e senha;
- o suporte será responsável por prover credenciais aos usuários;
- a aplicação deverá suportar perfis e validade de acesso, conforme os requisitos do projeto.

## Serviços previstos na fase inicial

### Obrigatórios

- `Django + Wagtail`
- `PostgreSQL`
- `Gunicorn`
- `Docker / Docker Compose`

### Não previstos na fase inicial

- `Redis`
- `Celery`

Esses componentes podem ser adicionados depois, caso surja necessidade real de cache compartilhado, filas, tarefas assíncronas ou processamento adicional.

## Fluxo de deploy resumido

- desenvolvimento local com `Docker`;
- versionamento do código em `Git`;
- publicação em `staging` para validação técnica e funcional;
- aprovação da PoC e, depois, do MVP;
- publicação em `produção` com o mesmo desenho de containerização.

Na fase inicial, a recomendação é trabalhar com deploy controlado a partir do repositório, priorizando rastreabilidade e simplicidade operacional.

## Alternativas consideradas

### WordPress clássico

#### Vantagens

- implantação inicial rápida;
- alta familiaridade para usuários não técnicos;
- boa escolha para sites, blogs e MVPs simples;
- grande ecossistema de plugins.

#### Limitações no contexto do projeto

- maior risco de dependência de plugins;
- implementação menos confortável para regras de negócio mais específicas;
- modelagem do catálogo tende a exigir mais customização;
- menor aderência a um portal privado com regras de acesso mais finas.

### WordPress headless + Next.js

#### Vantagens

- frontend muito forte e flexível;
- bom potencial de SEO e performance;
- mantém WordPress como ferramenta de conteúdo.

#### Limitações no contexto do projeto

- separa o sistema em duas camadas principais;
- aumenta a complexidade de autenticação, integração e deploy;
- adiciona custo estrutural que não parece proporcional ao cenário atual do projeto.

### Strapi + Next.js + PostgreSQL

#### Vantagens

- boa modelagem para conteúdo estruturado;
- API limpa e abordagem headless;
- alinhamento natural para times orientados a `JavaScript/TypeScript`.

#### Limitações no contexto do projeto

- mais serviços para operar;
- maior complexidade de integração;
- frontend obrigatoriamente separado;
- complexidade superior ao necessário para a fase atual do projeto.

### NestJS + Next.js + PostgreSQL

#### Vantagens

- alto controle técnico;
- forte padronização para times focados em `TypeScript`;
- boa base para regras de negócio e integrações futuras.

#### Limitações no contexto do projeto

- exige construir ou integrar um CMS e um backoffice;
- aumenta escopo, prazo e custo inicial;
- perde competitividade quando a gestão de conteúdo é parte central da solução.

## Painel comparativo

| Critério | WordPress | Django + Wagtail |
|---|---|---|
| Facilidade inicial | Muito alta | Alta |
| Velocidade para MVP | Muito alta | Alta |
| Facilidade para conteudistas | Muito alta | Alta |
| Regras de negócio específicas | Média | Alta |
| Catálogo estruturado | Média | Alta |
| Controle de acesso | Média | Alta |
| Dependência de plugins | Alta | Baixa |
| Manutenção no longo prazo | Média | Alta |
| Flexibilidade para crescer | Média | Alta |
| Aderência ao Projeto Lago Verde | Boa | Muito boa |

### Leitura do comparativo

- `WordPress` leva vantagem em familiaridade, velocidade inicial e menor atrito para um MVP simples.
- `Django + Wagtail` leva vantagem quando o foco passa a ser catálogo estruturado, regras de acesso, evolução funcional e sustentabilidade técnica.
- Para o contexto do `Projeto Lago Verde`, a avaliação geral favorece `Django + Wagtail`.

## Painel comparativo ampliado

| Stack | Aderência ao projeto | Pontos fortes | Riscos / quando perde | Complexidade operacional |
|---|---|---|---|---|
| `WordPress clássico` | Boa para MVP rápido | CMS maduro, fácil para conteudista, deploy simples, ecossistema grande | Expiração de acesso por prazo controlado pelo suporte, filtros mais ricos, modelagem do catálogo e regras finas tendem a virar plugin + código customizado | Baixa |
| `WordPress headless + Next.js` | Média | Front muito forte, bom SEO/performance, mantém WordPress para conteúdo | Complexidade de `2 sistemas`; para este projeto parece custo extra sem ganho proporcional | Média/alta |
| `Strapi + Next.js + PostgreSQL` | Boa | Modelagem estruturada, RBAC, API limpa, bom para catálogo relacional | Mais `moving parts`, mais DevOps, frontend obrigatório separado, pode ser mais do que o projeto precisa no início | Alta |
| `NestJS + Next.js + PostgreSQL` | Média/boa | Alto controle técnico, forte padronização em `TypeScript`, boa base para regras de negócio | Exige construir ou integrar CMS/admin; sobe escopo, prazo e custo inicial | Alta |
| `Django + Wagtail + PostgreSQL` | Muito boa | Monólito forte, autenticação e expiração mais fáceis de controlar, bom CMS, busca e modelagem mais coerentes no longo prazo | Sobe um pouco o custo inicial de desenvolvimento e exige equipe confortável com Python | Média |

### Leitura do painel ampliado

- `WordPress clássico` é competitivo quando o objetivo principal é entregar rápido com baixa barreira para editores.
- `WordPress headless + Next.js` aumenta a sofisticação do frontend, mas adiciona separação de sistemas sem resolver o principal desafio do projeto.
- `Strapi + Next.js + PostgreSQL` é tecnicamente sólido, especialmente para times de `JavaScript/TypeScript`, mas tende a introduzir mais complexidade operacional.
- `NestJS + Next.js + PostgreSQL` faz mais sentido quando o projeto é guiado por backend de aplicação e controle total do time técnico, não quando o CMS é o centro da experiência.
- `Django + Wagtail + PostgreSQL` oferece o melhor equilíbrio entre CMS, regras de negócio, operação em infraestrutura própria e evolução sustentável.

### Resumo executivo do painel

- Se a prioridade máxima for `velocidade e familiaridade`, `WordPress clássico` é a opção mais simples.
- Se a prioridade máxima for `frontend desacoplado`, `WordPress headless + Next.js` ou `Strapi + Next.js` entram na conversa, mas com custo estrutural maior.
- Se a prioridade máxima for `controle técnico total em TypeScript`, `NestJS + Next.js` é forte, mas exige resolver CMS e backoffice.
- Se a prioridade for `equilíbrio entre CMS, regras de negócio, manutenção e operação on-premise`, a recomendação continua sendo `Django + Wagtail + PostgreSQL`.

## Recomendação final

A stack recomendada para a `PoC` e para a primeira fase do projeto é:

`Python + Django + Wagtail + PostgreSQL + Gunicorn + Tailwind + HTMX + Linux + Docker`, com publicação via `Apache / VirtualHost` da Maadix

Ela oferece um bom equilíbrio entre:

- facilidade de uso para conteudistas;
- controle técnico para a equipe de desenvolvimento;
- simplicidade de deploy em infraestrutura própria;
- capacidade de evolução no médio e longo prazo.

## Referências relacionadas

- [ADR-0001 - Adotar Django + Wagtail + PostgreSQL como stack candidata para a PoC](../adr/0001-adotar-django-wagtail-postgresql-para-poc.md)
- [Visão Geral da Solução](../architecture/visao-geral-da-solucao.md)
- [Plano de Execução](../process/plano-de-execucao.md)
