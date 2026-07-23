# Desenho Técnico de Ambientes e Deploy

## Objetivo

Descrever o desenho técnico recomendado para ambientes e deploy do `Projeto Lago Verde`, considerando a restrição operacional confirmada pela infraestrutura da Maadix:

- não haverá novas `VMs`;
- o portal será hospedado na `mesma instância GNU/Linux` já existente;
- o isolamento será feito por `compartimentação Docker`;
- a publicação externa continuará sendo feita pelo `Apache` via `VirtualHost`.

## Premissas

- o desenvolvimento cotidiano acontecerá localmente;
- a infraestrutura disponibilizará um ambiente Docker compartimentado para o portal;
- o portal terá rede, volumes, banco PostgreSQL, diretórios e credenciais próprios;
- o `Apache` continuará sendo a camada de publicação e proxy reverso;
- o banco de dados e os containers não serão expostos diretamente à internet;
- o suporte funcional às pessoas usuárias não será responsabilidade da infraestrutura.

## Visão geral da topologia

```text
Desenvolvimento local
-> Git
-> Host Linux Maadix
   -> Apache / VirtualHosts / HTTPS
   -> Ambiente HOM
      -> app Django + Wagtail + Gunicorn
      -> PostgreSQL
      -> rede, volumes e credenciais próprios
   -> Ambiente PROD
      -> app Django + Wagtail + Gunicorn
      -> PostgreSQL
      -> rede, volumes e credenciais próprios
```

## Estratégia de ambientes

### Desenvolvimento local

- cada desenvolvedor roda a aplicação localmente com `Docker Compose`;
- o ambiente local contém, no mínimo:
  - `app`
  - `db`
- o objetivo é permitir desenvolvimento, testes funcionais e validação rápida sem depender do servidor.

### Homologação / staging

- ambiente remoto dedicado logicamente dentro da instância Maadix;
- utilizado para:
  - validação da PoC;
  - testes de integração;
  - homologação com stakeholders;
  - verificação do fluxo real de publicação.

### Produção

- ambiente remoto separado logicamente do `hom`;
- publicado em subdomínio próprio;
- com banco, volumes, rede e credenciais próprios;
- só recebe atualizações após validação em homologação.

## Isolamento lógico no host Maadix

Mesmo sem VMs separadas, o isolamento do portal deve existir em vários níveis:

- `diretório` próprio por ambiente;
- `compose project` próprio por ambiente;
- `rede Docker` própria por ambiente;
- `volumes` próprios por ambiente;
- `banco PostgreSQL` próprio por ambiente;
- `credenciais` próprias por ambiente;
- `subdomínio` próprio por ambiente.

## Estrutura sugerida no host

Exemplo de organização:

```text
/opt/lago-verde-hom/
  compose.yml
  .env
  app/
  backups/

/opt/lago-verde-prod/
  compose.yml
  .env
  app/
  backups/
```

Essa estrutura pode ser ajustada pela infraestrutura, mas a separação entre `hom` e `prod` deve ser preservada.

## Publicação via Apache

O `Apache` não gerencia os containers. Ele apenas:

- recebe as requisições externas;
- publica os subdomínios;
- aplica `HTTPS`;
- encaminha o tráfego para portas locais dos serviços do portal.

Exemplo conceitual:

```text
staging.portal.exemplo.org -> Apache -> localhost:8001 -> ambiente HOM
portal.exemplo.org         -> Apache -> localhost:8002 -> ambiente PROD
```

## Composição mínima por ambiente

### Ambiente HOM

- `app`: Django + Wagtail + Gunicorn
- `db`: PostgreSQL
- `.env` próprio
- porta local própria
- rede e volumes próprios

### Ambiente PROD

- `app`: Django + Wagtail + Gunicorn
- `db`: PostgreSQL
- `.env` próprio
- porta local própria
- rede e volumes próprios

## Fluxo sugerido de branches

- `feature/*`: desenvolvimento
- `develop`: homologação
- `main`: produção

Esse modelo ajuda a manter rastreabilidade entre o que está em `hom` e o que está em `prod`.

## Fluxo de deploy recomendado

### Homologação

1. desenvolver localmente;
2. enviar alterações ao repositório;
3. integrar em `develop`;
4. publicar no ambiente `hom`;
5. executar migrations;
6. validar logs, acesso e funcionamento.

### Produção

1. aprovar a versão em `hom`;
2. integrar a versão final em `main`;
3. publicar em `prod`;
4. executar migrations;
5. realizar smoke test;
6. monitorar o serviço após a publicação.

## Modelo de operação remota

O mais seguro é evitar acesso amplo ao Docker e ao host.

Modelo preferido:

- conta individual para a equipe de desenvolvimento;
- acesso restrito ao contexto do portal;
- sem `root`;
- sem acesso aos demais serviços da Maadix;
- operações permitidas via scripts ou comandos previamente autorizados.

Exemplos de scripts esperados:

- `deploy-hom`
- `deploy-prod`
- `migrate-hom`
- `migrate-prod`
- `logs-hom`
- `logs-prod`
- `restart-hom`
- `restart-prod`
- `rollback-prod`

## Persistência de dados

Os dados que precisam persistir por ambiente são:

- banco PostgreSQL;
- arquivos de mídia;
- variáveis de ambiente;
- artefatos operacionais necessários para deploy.

Cada ambiente deve ter persistência própria, sem compartilhamento de dados com o outro.

## Backup e recuperação

Estratégia mínima recomendada:

- backup lógico diário do PostgreSQL;
- backup diário dos volumes de mídia;
- cópia dos backups para fora do diretório da aplicação;
- retenção definida pela infraestrutura;
- teste periódico de restauração.

## Rollback

O rollback precisa ser definido antes da entrada em produção.

Modelo mínimo recomendado:

- manter a versão anterior conhecida da aplicação;
- permitir voltar ao release anterior;
- avaliar impacto de migrations irreversíveis;
- registrar o procedimento operacional com a infraestrutura.

## Healthcheck e validação pós-deploy

Após cada publicação, deve haver uma validação mínima:

- aplicação sobe corretamente;
- conexão com banco está funcional;
- `home` responde;
- login responde;
- área autenticada abre corretamente;
- logs não apresentam erro crítico imediatamente após o deploy.

## Riscos operacionais que precisam ser controlados

- consumo de recurso por `hom` e `prod` no mesmo host;
- confusão entre variáveis de ambiente dos dois ambientes;
- publicação acidental do ambiente errado;
- rollback insuficiente;
- excesso de privilégio operacional para a equipe de desenvolvimento;
- ausência de visibilidade sobre logs e falhas de deploy.

## Decisão recomendada para a reunião

Se a instância suportar os dois ambientes, o desenho recomendado é:

- `desenvolvimento local`;
- `homologação` e `produção` no mesmo host Maadix;
- separação lógica forte entre `hom` e `prod`;
- publicação dos dois ambientes via `Apache / VirtualHost`;
- deploy da aplicação pela equipe de desenvolvimento, com acesso controlado e scripts operacionais.

## Referências relacionadas

- [Visão Geral da Solução](./visao-geral-da-solucao.md)
- [Levantamento de Tecnologias](../discovery/levantamento-tecnologias.md)
- [Plano de Execução](../process/plano-de-execucao.md)
- [Perguntas para Reunião de Acesso Operacional](../process/perguntas-reuniao-acesso-operacional.md)
