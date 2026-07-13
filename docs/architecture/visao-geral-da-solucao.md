# Visão Geral da Solução

## Objetivo

Descrever a arquitetura proposta para a primeira versão do `Projeto Lago Verde`, alinhada à decisão de conduzir uma `PoC` com `Django + Wagtail + PostgreSQL`.

## Arquitetura proposta

O sistema será construído inicialmente como um `monólito`.

Isso significa que os seguintes elementos viverão dentro da mesma aplicação principal:

- autenticação;
- autorização;
- catálogo;
- wiki/blog;
- painel administrativo;
- renderização das páginas;
- regras de negócio do portal.

## Stack arquitetural

- `Aplicação principal`: Django
- `CMS e painel administrativo`: Wagtail
- `Banco de dados`: PostgreSQL
- `Frontend`: Django Templates + Tailwind CSS + HTMX
- `Servidor web / proxy reverso`: Nginx
- `Empacotamento e deploy`: Docker + Docker Compose
- `Sistema operacional alvo`: Linux

## Como o frontend se encaixa

O frontend não será um projeto separado nesta primeira arquitetura.

Ele será construído dentro do próprio backend com:

- `templates` do Django para renderização server-side;
- `Tailwind CSS` para estilização responsiva;
- `HTMX` para interações leves, como filtros e atualizações parciais;
- `JavaScript` apenas onde realmente for necessário.

Essa abordagem reduz:

- complexidade de integração;
- custo inicial de desenvolvimento;
- esforço de deploy em infraestrutura própria.

## Componentes principais

### Camada de apresentação

- `home` pública;
- páginas privadas autenticadas;
- listagem de itens;
- páginas individuais de item;
- páginas de categorias;
- páginas de artigos/orientações;
- interface administrativa do Wagtail.

### Camada de domínio e regras

- gestão de usuários e perfis;
- controle de acesso;
- possível expiração de usuários convidados;
- modelagem do catálogo;
- filtros e taxonomias;
- publicação e edição de conteúdo.

### Camada de dados

- banco relacional `PostgreSQL`;
- arquivos de mídia armazenados em volume persistente;
- possibilidade de uso futuro de `Redis`, caso seja necessário.

## Infraestrutura alvo

A primeira versão deve considerar operação em `infra própria` com:

- servidor Linux;
- Docker;
- Docker Compose;
- Nginx;
- PostgreSQL;
- backup de banco e arquivos;
- HTTPS;
- monitoramento e logs.

## Integrações possíveis

- `Nextcloud` para documentos complementares;
- provedor de e-mail/SMTP;
- autenticação centralizada, se houver necessidade futura.

## Decisões de escopo para a primeira fase

- priorizar arquitetura simples;
- evitar frontend desacoplado nesta etapa;
- evitar microserviços;
- introduzir `Redis` e `Celery` apenas se houver necessidade real.

## Referências relacionadas

- [Levantamento de Tecnologias](../discovery/levantamento-tecnologias.md)
- [ADR-0001](../adr/0001-adotar-django-wagtail-postgresql-para-poc.md)
- [Plano de Execução](../process/plano-de-execucao.md)
