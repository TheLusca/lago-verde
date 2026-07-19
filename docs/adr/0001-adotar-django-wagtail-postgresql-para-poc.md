# ADR-0001: Adotar Django + Wagtail + PostgreSQL como stack candidata para a PoC do Projeto Lago Verde

- `Status`: Proposto
- `Data`: 2026-07-19
- `Decisores`: Equipe técnica do Projeto Lago Verde
- `Impacta`: arquitetura da aplicação, CMS, modelagem de conteúdo, autenticação, deploy e operação em infraestrutura própria

## Contexto

O `Projeto Lago Verde` não se comporta como um site institucional simples. Pelos requisitos levantados até o momento, o sistema precisa atender simultaneamente a demandas de conteúdo, catálogo estruturado, controle de acesso e operação em infraestrutura própria.

Entre os requisitos e condicionantes identificados estão:

- `home` pública e demais áreas protegidas por autenticação;
- perfis distintos de acesso: `admin`, `conteudista`, `visitante` e `convidado`;
- acesso temporário para usuários convidados, com validade aproximada de `1 mês`;
- catálogo/wiki com páginas próprias para cada item;
- filtros por nome, categoria, atividade e nível;
- área de artigos/orientações com perfil de `wiki/blog`;
- painel administrativo amigável para pessoas não técnicas;
- design responsivo para desktop e celular;
- hospedagem em `infraestrutura própria` do cliente.

Durante a avaliação inicial, surgiu uma divergência legítima entre:

- adotar `WordPress`, pela familiaridade de parte dos stakeholders e pela velocidade de entrega inicial;
- adotar uma stack mais controlada pela equipe técnica, reduzindo dependência de plugins e facilitando a evolução do sistema no longo prazo.

Também foi observado que a solução precisa equilibrar:

- facilidade de uso para editores de conteúdo;
- capacidade de implementar regras de negócio específicas;
- simplicidade de operação em ambiente `on-premise`;
- base técnica sustentável para evoluções futuras.

Após o retorno da infraestrutura da Maadix, ficou claro que o principal ponto de atenção não está na capacidade inicial do ambiente, mas sim no desenho da operação do novo serviço dentro da infraestrutura existente.

## Decisão

Será conduzida uma `PoC (Prova de Conceito)` usando a stack:

- `Django` como framework principal da aplicação;
- `Wagtail` como CMS e painel administrativo;
- `PostgreSQL` como banco de dados relacional;
- frontend renderizado no próprio backend, com `templates server-side`;
- abordagem inicial de aplicação `monolítica`;
- empacotamento e deploy com `Docker` e `Docker Compose`;
- publicação via `Apache / VirtualHost` da infraestrutura da Maadix;
- isolamento do serviço em relação aos demais componentes já existentes no ambiente;
- ausência inicial de `Redis` e `Celery`, salvo necessidade validada posteriormente.

Para a PoC, a implementação deve priorizar:

- `home` pública;
- autenticação básica;
- modelagem inicial de itens do catálogo;
- cadastro e edição de conteúdo no CMS;
- uma listagem com filtros principais;
- uma página individual de item;
- validação do fluxo de uso por perfis não técnicos.

## Justificativa

### 1. O projeto exige mais do que publicação de conteúdo genérico

Apesar de o sistema possuir características de site e blog, ele também precisa lidar com:

- regras de acesso;
- validade de usuários;
- catálogo estruturado;
- filtros e taxonomias;
- crescimento funcional previsível.

Esse conjunto aproxima mais o produto de um `portal privado com regras de negócio` do que de um site institucional tradicional.

### 2. O Wagtail atende a necessidade de CMS sem abrir mão de arquitetura mais controlada

O `Wagtail` oferece experiência de CMS para cadastro e edição de conteúdo, mantendo a flexibilidade do ecossistema `Django` para implementar lógica de autenticação, permissões, filtros e fluxos de publicação.

### 3. O Django oferece melhor base para regras de negócio e manutenção

Com `Django`, a equipe técnica tende a ter:

- mais clareza na implementação das regras do sistema;
- menor dependência de plugins de terceiros;
- melhor previsibilidade para manutenção e evolução;
- integração natural entre autenticação, painel, modelos de dados e renderização do site.

### 4. O PostgreSQL é adequado para dados estruturados e relacionamentos

O catálogo proposto possui natureza relacional e tende a exigir boa modelagem de:

- itens;
- categorias;
- atividades;
- usuários e perfis;
- artigos e relacionamentos de conteúdo.

`PostgreSQL` é uma escolha consistente para esse perfil.

### 5. A abordagem monolítica simplifica a operação inicial

Como o projeto será operado em `infraestrutura própria`, a adoção de um `monólito` reduz a complexidade operacional inicial em comparação com arquiteturas distribuídas ou `headless` com múltiplos serviços independentes.

### 6. O retorno da infraestrutura da Maadix validou a viabilidade do desenho proposto

O feedback recebido da infraestrutura indicou que:

- não há impedimento inicial de capacidade para hospedar o novo serviço;
- o principal cuidado está no desenho da operação;
- a aplicação deve permanecer isolada dos demais serviços existentes;
- a publicação pode ser feita por `VirtualHost` dedicado no `Apache`;
- a solução deve entrar como aplicação customizada, com fluxo próprio de deploy, manutenção, backup e monitoramento.

## Alternativas consideradas

### Alternativa A: WordPress

### Pontos favoráveis

- implantação inicial mais rápida;
- interface familiar para parte dos stakeholders;
- forte apelo para sites, blogs e MVPs rápidos;
- ecossistema maduro e grande oferta de plugins.

### Pontos desfavoráveis

- maior risco de dependência de plugins;
- regras específicas do projeto tendem a exigir customizações adicionais;
- possibilidade de crescimento de dívida técnica ao misturar plugins e código customizado;
- menor conforto para evoluir o produto como sistema com regras próprias.

### Avaliação

`WordPress` continua sendo uma alternativa válida caso a prioridade máxima seja reduzir atrito inicial e acelerar uma primeira entrega. Ainda assim, foi avaliado como menos aderente ao perfil estrutural do projeto.

### Alternativa B: Strapi + Next.js + PostgreSQL

### Pontos favoráveis

- boa experiência para times orientados a `JavaScript/TypeScript`;
- CMS com abordagem `headless`;
- frontend moderno e desacoplado.

### Pontos desfavoráveis

- maior complexidade operacional;
- necessidade de manter pelo menos dois sistemas principais;
- maior custo de integração entre autenticação, CMS e frontend;
- complexidade possivelmente acima do necessário para o estágio atual do projeto.

### Avaliação

Foi considerada tecnicamente viável, porém menos adequada ao objetivo de simplificar a primeira arquitetura em ambiente próprio.

### Alternativa C: NestJS + Next.js + PostgreSQL

### Pontos favoráveis

- alto controle técnico da solução;
- boa padronização para times focados em `TypeScript`;
- base flexível para regras de negócio.

### Pontos desfavoráveis

- necessidade de construir ou integrar um CMS/painel administrativo;
- maior escopo de implementação;
- tempo de entrega inicial superior;
- menor adequação quando o problema principal é gestão de conteúdo com boa experiência editorial.

### Avaliação

Foi considerada forte do ponto de vista de backend de aplicação, mas inadequada como primeira escolha para um projeto em que `CMS`, edição de conteúdo e operação editorial são centrais.

## Consequências

### Consequências positivas esperadas

- melhor aderência ao tipo de sistema descrito para o Projeto Lago Verde;
- painel administrativo utilizável por pessoas não técnicas;
- arquitetura mais clara para regras de acesso e catálogo;
- menor dependência estrutural de plugins de terceiros;
- base mais sustentável para evolução futura.

### Consequências negativas e custos assumidos

- curva de familiarização maior para stakeholders acostumados com WordPress;
- desenvolvimento inicial potencialmente mais lento que uma montagem rápida em WordPress;
- necessidade de demonstrar o valor da stack por meio de uma PoC;
- dependência de conhecimento da equipe técnica em `Python`, `Django` e `Wagtail`.

### Consequências operacionais

- será necessário planejar deploy em `infra própria`;
- será necessário preparar ambiente com `Docker`, aplicação, banco e backup;
- será necessário alinhar homologação e produção em ambientes separados dos demais serviços;
- a publicação dependerá de integração com o `Apache` da Maadix;
- a equipe deverá definir política de autenticação, gestão de usuários e rotina de manutenção.

## Critérios de sucesso da PoC

A PoC será considerada bem-sucedida se demonstrar, com clareza:

- viabilidade da stack escolhida;
- facilidade de uso do CMS por usuários não técnicos;
- modelagem adequada de pelo menos um tipo de item do catálogo;
- autenticação funcional para áreas privadas;
- listagem e filtragem coerentes com os requisitos iniciais;
- capacidade de o time sustentar a solução em ambiente próprio.

## Dúvidas em aberto

- O login será totalmente interno ao sistema ou integrado a algum provedor já existente?
- O `Nextcloud` será usado apenas para documentos complementares ou também para identidade/SSO?
- O controle de expiração de acesso será automático desde a primeira versão?
- Haverá fluxo editorial com revisão/aprovação ou publicação direta por conteudistas?
- Qual será o domínio ou subdomínio oficial do portal em cada ambiente?

## Próximos passos

1. Validar este ADR com a equipe técnica e stakeholders.
2. Confirmar o escopo da PoC.
3. Confirmar o desenho de ambientes com a infraestrutura da Maadix.
4. Implementar a PoC com foco no fluxo principal do sistema.
5. Revisar este ADR após a PoC, alterando o status para `Aceito`, `Substituído` ou `Descartado`.

## Referências

- J. Walker, [Guia completo sobre Architecture Decision Records (ADR): definição e melhores práticas](https://medium.com/@jhonywalkeer/guia-completo-sobre-architecture-decision-records-adr-defini%C3%A7%C3%A3o-e-melhores-pr%C3%A1ticas-f63e66d33e6)
- Projeto Lago Verde, [Levantamento de Tecnologias](../discovery/levantamento-tecnologias.md)
- Projeto Lago Verde, [Visão Geral da Solução](../architecture/visao-geral-da-solucao.md)
- Projeto Lago Verde, [Plano de Execução](../process/plano-de-execucao.md)
- Django Software Foundation, [Django deployment checklist](https://docs.djangoproject.com/en/6.0/howto/deployment/checklist/)
- Wagtail, [Wagtail documentation](https://docs.wagtail.org/en/stable/)
- PostgreSQL Global Development Group, [PostgreSQL documentation](https://www.postgresql.org/docs/)
