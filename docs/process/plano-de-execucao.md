# Plano de Execução

- `Data`: 13/07/2026
- `Autor`: Lucas de Andrade - Engenheiro de Software
- `LinkedIn`: https://www.linkedin.com/in/lucasdeandrade01/
- `Status`: Em análise
- `Escopo`: plano de execução da fase inicial do projeto, da descoberta à validação da PoC, com preparação para o MVP

## Objetivo

Descrever um fluxo de trabalho para transformar os requisitos do website em uma `PoC` validável com stakeholders e equipe técnica, além de orientar a transição para a fase de `MVP`.

## Fluxo de trabalho sugerido

### 1. Descoberta e alinhamento

- consolidar requisitos funcionais e não funcionais;
- validar perfis de usuário;
- confirmar restrições da infraestrutura própria;
- alinhar critérios de sucesso da PoC.

### 2. Levantamento de tecnologias e decisão arquitetural

- comparar alternativas de stack;
- registrar a recomendação técnica;
- formalizar a decisão em ADR;
- alinhar a proposta com a equipe interna.

### 3. Design e estruturação da experiência

- criar wireframes no Figma;
- validar navegação das principais páginas;
- validar fluxo do usuário visitante;
- validar fluxo do usuário autenticado;
- validar o comportamento esperado do catálogo e dos filtros.

### 4. Modelagem funcional e técnica

- definir entidades principais do catálogo;
- modelar categorias, atividades e níveis;
- definir regras de autenticação e perfis;
- definir páginas e componentes principais do sistema.

### 5. Implementação da PoC

- configurar o monólito `Django + Wagtail + PostgreSQL`;
- construir a `home` pública;
- construir autenticação básica;
- implementar cadastro inicial de itens;
- implementar listagem com filtros principais;
- implementar página individual de item;
- configurar o CMS para edição de conteúdo.

### 6. Validação interna

- revisar usabilidade com a equipe técnica;
- revisar fluxo de conteúdo com possíveis conteudistas;
- verificar aderência da stack à infraestrutura própria;
- ajustar pontos críticos identificados na PoC.

### 7. Apresentação e validação com stakeholders

- apresentar wireframes e PoC;
- demonstrar fluxo administrativo;
- demonstrar navegação do catálogo;
- recolher feedback;
- validar aderência da solução às expectativas do projeto.

### 8. Decisão de continuidade

- consolidar feedback técnico e funcional;
- avaliar se a PoC atende aos critérios definidos;
- decidir `go / no-go` para continuidade;
- aprovar eventuais ajustes antes da evolução para o MVP.

### 9. Planejamento do MVP

- revisar escopo;
- transformar a PoC em backlog do MVP;
- atualizar ADRs e documentação de arquitetura;
- definir os próximos passos da implementação.

## Sequência resumida

1. Requisitos
2. Levantamento tecnológico
3. ADR
4. Wireframe no Figma
5. Modelagem
6. Implementação da PoC
7. Validação
8. Decisão de continuidade
9. Evolução para MVP

## Observações importantes

- o wireframe no Figma entra antes da implementação para reduzir retrabalho;
- o frontend será implementado dentro do próprio monólito nesta fase;
- o backend e o frontend não serão separados em aplicações distintas na PoC;
- o fluxo pode ser iterativo, mas a ordem acima ajuda a organizar a apresentação e a execução.

## Entregáveis sugeridos por etapa

- descoberta: requisitos consolidados;
- levantamento: documento de stack;
- decisão: ADR;
- design: wireframes;
- arquitetura: visão geral da solução;
- implementação: PoC funcional;
- validação: feedback consolidado;
- continuidade: decisão formal sobre a próxima fase;
- próxima fase: backlog priorizado do MVP.

## Referências relacionadas

- [Levantamento de Tecnologias](../discovery/levantamento-tecnologias.md)
- [Visão Geral da Solução](../architecture/visao-geral-da-solucao.md)
- [ADR-0001](../adr/0001-adotar-django-wagtail-postgresql-para-poc.md)
