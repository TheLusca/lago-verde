# Perguntas para Reunião de Acesso Operacional

## Objetivo

Consolidar as perguntas que precisam ser respondidas na reunião com a infraestrutura para fechar o desenho operacional do `Projeto Lago Verde` dentro do ambiente da Maadix.

## Resultado esperado da reunião

Ao final da reunião, o time deve sair com definição clara sobre:

- estrutura de ambientes;
- forma de acesso operacional;
- fluxo de deploy e rollback;
- persistência e backups;
- responsabilidades entre desenvolvimento, administração do portal e infraestrutura.

## Perguntas sobre ambientes

- Teremos `hom` e `prod` simultaneamente no mesmo host desde o início, ou começaremos apenas com um ambiente remoto?
- Caso existam dois ambientes, quais serão os subdomínios previstos para `hom` e `prod`?
- Haverá limites de recurso ou restrições práticas para manter os dois ambientes ativos ao mesmo tempo?
- O ambiente de homologação poderá ter banco e mídia independentes de produção desde o início?

## Perguntas sobre diretórios e estrutura no host

- Qual será o diretório base do portal no servidor?
- A infraestrutura prefere diretórios separados por ambiente, por exemplo `lago-verde-hom` e `lago-verde-prod`?
- Onde ficarão os arquivos de configuração operacional, como `.env`, scripts e backups locais?
- Existe alguma convenção da Maadix para organização de aplicações customizadas no host?

## Perguntas sobre Docker e permissões operacionais

- A equipe de desenvolvimento poderá executar `docker compose` diretamente, ou as operações serão feitas por scripts preparados pela infraestrutura?
- Quais comandos poderemos executar com a conta restrita?
- Haverá uso de `sudo` limitado para scripts específicos?
- Como será controlado o acesso ao contexto Docker do portal sem ampliar privilégios sobre os demais serviços?
- Haverá separação clara entre permissões de `hom` e `prod`?

## Perguntas sobre deploy

- O deploy será feito com `git pull` no servidor, ou a infraestrutura prefere outro modelo de entrega?
- A aplicação será publicada diretamente a partir do repositório clonado no host?
- Quem executa os comandos de deploy no ambiente remoto: desenvolvimento ou infraestrutura?
- Qual será o procedimento padrão de publicação em `hom`?
- Qual será o procedimento padrão de publicação em `prod`?
- Existe alguma janela operacional ou regra para deploy em produção?

## Perguntas sobre migrations

- A equipe de desenvolvimento poderá executar migrations diretamente?
- As migrations serão parte do script de deploy ou uma etapa separada?
- Como será tratado rollback em caso de migration problemática?
- Existe alguma exigência da infraestrutura para registrar as mudanças de schema aplicadas?

## Perguntas sobre logs e troubleshooting

- Quais logs estarão disponíveis para a equipe de desenvolvimento?
- O acesso aos logs será direto, por script, por arquivo ou por comando pré-definido?
- Haverá alguma ferramenta de centralização ou retenção de logs?
- Como será o fluxo de troubleshooting em caso de falha após deploy?

## Perguntas sobre domínio e publicação

- Quais subdomínios serão reservados para `hom` e `prod`?
- Quem ficará responsável por criar e manter os `VirtualHosts` no Apache?
- Como será feita a validação do `HTTPS` em homologação?
- Existe alguma restrição para publicação de múltiplos subdomínios do portal?

## Perguntas sobre variáveis de ambiente e segredos

- Onde ficarão armazenadas as variáveis de ambiente da aplicação?
- Quem ficará responsável por manter credenciais sensíveis no servidor?
- A equipe de desenvolvimento poderá atualizar variáveis de ambiente diretamente?
- Como será feito o versionamento ou controle das mudanças de configuração operacional?

## Perguntas sobre persistência e dados

- Quais diretórios de mídia e dados precisam ser persistidos em `hom` e `prod`?
- O banco PostgreSQL do portal será um container próprio, uma instância separada ou um banco dedicado dentro de um serviço existente?
- A infraestrutura prefere volumes Docker nomeados ou bind mounts?
- Como será evitado compartilhamento acidental de dados entre `hom` e `prod`?

## Perguntas sobre backup e restauração

- Qual será a rotina de backup do banco do portal?
- Qual será a rotina de backup dos arquivos de mídia?
- Onde os backups serão armazenados?
- Qual será a retenção aplicada aos backups?
- A equipe de desenvolvimento participará de testes de restore?
- Haverá um procedimento documentado de recuperação do portal em caso de falha?

## Perguntas sobre rollback

- Qual será o procedimento formal de rollback da aplicação?
- O rollback será feito por versionamento de código, por imagem ou por outro mecanismo?
- Quem autoriza e quem executa rollback em produção?
- Como o rollback será tratado quando houver migration de banco?

## Perguntas sobre divisão de responsabilidades

- Quais atividades ficam explicitamente com a infraestrutura?
- Quais atividades ficam explicitamente com a equipe de desenvolvimento?
- Quais atividades ficam com a administração funcional do portal?
- Em incidentes de indisponibilidade, qual será o ponto inicial de acionamento?
- Em incidentes funcionais da aplicação, qual será o fluxo esperado entre administração do portal e desenvolvimento?

## Perguntas finais de fechamento

- Há algum requisito operacional da Maadix que ainda não apareceu e que deveria entrar no desenho técnico?
- Há alguma limitação de segurança ou compliance que precise ser considerada antes da implantação?
- Existe alguma recomendação da infraestrutura para simplificar o primeiro deploy do portal?
- Quais decisões precisam sair desta reunião para permitir o início da implementação com segurança?

## Checklist de decisões para sair da reunião

- ambiente remoto terá `hom` e `prod` ou apenas um ambiente inicial;
- subdomínios definidos;
- diretórios base definidos;
- modelo de acesso operacional definido;
- forma de deploy definida;
- forma de rollback definida;
- persistência definida;
- rotina de backup definida;
- responsabilidades formalizadas.

## Referências relacionadas

- [Desenho Técnico de Ambientes e Deploy](../architecture/desenho-tecnico-ambientes-e-deploy.md)
- [Plano de Execução](./plano-de-execucao.md)
- [Visão Geral da Solução](../architecture/visao-geral-da-solucao.md)
