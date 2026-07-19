# Visão Geral da Solução

## Objetivo

Descrever a arquitetura proposta para a primeira versão do `Projeto Lago Verde`, alinhada à decisão de conduzir uma `PoC` com `Django + Wagtail + PostgreSQL` e compatível com o modelo operacional da infraestrutura da Maadix.

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
- `Servidor de aplicação`: Gunicorn
- `Publicação / proxy reverso`: Apache da Maadix com `VirtualHost` dedicado
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
- página de login pública;
- páginas privadas autenticadas;
- listagem de itens;
- páginas individuais de item;
- páginas de categorias;
- páginas de artigos/orientações;
- interface administrativa do Wagtail.

### Camada de domínio e regras

- gestão de usuários e perfis;
- controle de acesso;
- expiração de usuários convidados;
- modelagem do catálogo;
- filtros e taxonomias;
- publicação e edição de conteúdo.

### Camada de dados

- banco relacional `PostgreSQL`;
- arquivos de mídia armazenados em volume persistente;
- possibilidade de uso futuro de `Redis` e `Celery`, caso surja necessidade operacional real.

## Estratégia de ambientes

### Desenvolvimento local

- execução local pelos desenvolvedores;
- uso de `Docker Compose`;
- banco local dedicado ao ambiente de desenvolvimento;
- sem dependência de acesso contínuo ao servidor para o dia a dia de implementação.

### Homologação / staging

- VM Linux dedicada;
- ambiente isolado dos demais serviços da Maadix;
- stack equivalente à de produção;
- usada para validação da PoC, testes de integração e homologação com stakeholders.

### Produção

- VM Linux dedicada;
- isolamento em relação aos demais serviços existentes;
- banco, rede e volumes próprios;
- publicação via `Apache / VirtualHost` da Maadix.

## Infraestrutura alvo

A primeira versão deve considerar operação em `infra própria` com:

- servidor Linux dedicado por ambiente remoto;
- Docker;
- Docker Compose;
- Apache na camada de publicação da Maadix;
- PostgreSQL dedicado;
- backup de banco e arquivos;
- HTTPS;
- monitoramento e logs;
- acesso operacional restrito.

## Publicação, domínio e controle de acesso

### Publicação e domínio

- o domínio ou subdomínio do portal será publicado pela infraestrutura da Maadix;
- o `Apache` será responsável por `VirtualHost`, `SSL/TLS`, redirecionamento para HTTPS e proxy reverso;
- a aplicação será entregue containerizada para encaixe no fluxo operacional definido com a infraestrutura.

### Controle de acesso

- apenas a `home` e a página de `login` permanecerão públicas;
- as demais páginas do portal exigirão autenticação;
- o controle de acesso não ficará no Apache;
- a aplicação `Django + Wagtail` será responsável por autenticação, autorização, sessões, perfis e validade de acesso;
- o suporte será responsável por prover credenciais aos usuários.

## Serviços previstos na fase inicial

- `app`: Django + Wagtail + Gunicorn
- `db`: PostgreSQL
- `proxy externo`: Apache da Maadix

Não estão previstos na fase inicial:

- `Redis`
- `Celery`

## Fluxo de deploy

### Fase inicial

- desenvolvimento local;
- versionamento em `Git`;
- publicação em `staging`;
- validação interna e com stakeholders;
- publicação em `produção` após aprovação.

### Premissas operacionais

- a aplicação será entregue com `Dockerfile` e `Docker Compose`;
- o deploy deve ser rastreável a partir do repositório;
- o acesso ao servidor deve ser mínimo e controlado;
- operações esperadas em produção: deploy, migrations, leitura de logs, reinício de containers e troubleshooting pontual;
- desenvolvimento cotidiano não deve acontecer diretamente nos servidores remotos.

## Integrações possíveis

- `Nextcloud` para documentos complementares;
- provedor de e-mail/SMTP;
- autenticação centralizada, se houver necessidade futura.

## Decisões de escopo para a primeira fase

- priorizar arquitetura simples;
- evitar frontend desacoplado nesta etapa;
- evitar microserviços;
- manter o serviço isolado dos demais componentes da Maadix;
- usar publicação por `Apache / VirtualHost`, sem alterar estruturalmente a plataforma atual;
- introduzir `Redis` e `Celery` apenas se houver necessidade real.

## Referências relacionadas

- [Levantamento de Tecnologias](../discovery/levantamento-tecnologias.md)
- [ADR-0001](../adr/0001-adotar-django-wagtail-postgresql-para-poc.md)
- [Plano de Execução](../process/plano-de-execucao.md)
