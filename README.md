# QR Admin Platform

Base operacional para um único administrador gerenciar clientes e QR Codes estáticos e dinâmicos. Não há contas de clientes finais.

## O que funciona nesta versão

- Clientes, dashboard, criação e listagem de QR Codes.
- QR dinâmico: URL permanente `/q/<código>`, status no servidor (ativo, agendado, expirado e desativado), redirecionamento e scan mínimo.
- QR estático: conteúdo inserido diretamente no código, sem URL própria e sem estatísticas.
- Exportação PNG/SVG, paleta básica, margem e correção de erro alta.
- Autenticação de administrador, sessão, CSRF, limitação de login, logs de auditoria e backup manual SQLite.

## Instalação

1. Instale Node.js LTS com npm (Node 22.5+).
2. Copie `.env.example` para `.env`; defina `APP_ORIGIN` com HTTPS e um `SESSION_SECRET` aleatório de ao menos 48 caracteres.
3. Rode `npm install` e então `npm run start`.
4. Crie o administrador uma única vez: `node src/create-admin.js seu-email senha-forte-com-14-ou-mais`.
5. Acesse `https://seu-dominio/admin`.

Não use o comando de criação de administrador com uma senha que fique gravada no histórico de um terminal compartilhado. Em produção, prefira secret manager ou entrada protegida.

## Operação e backup

O botão de backup cria cópia consistente em `BACKUP_DIRECTORY`, com checksum e registro no banco. Copie essa pasta regularmente para armazenamento externo criptografado e execute restaurações de teste. Para restaurar: pare o serviço, faça uma cópia do banco atual, substitua `DATABASE_PATH` pelo backup escolhido, confirme permissões do arquivo e inicie o serviço. Nunca restaure sobre o banco ativo sem cópia prévia.

O backup automático depende do agendador do sistema operacional: execute um job autenticado ou uma rotina local que gere e copie um backup. A política recomendada é diária + retenção de 30 dias, ajustada à obrigação contratual e à LGPD.

## Produção

Coloque a aplicação atrás de proxy reverso HTTPS, defina `NODE_ENV=production` e `TRUST_PROXY=true` somente se o proxy for confiável. O processo deve usar usuário sem privilégio, diretórios de banco/backup fora da raiz pública e permissões restritas. Monitore espaço em disco, erros e integridade dos backups. Execute `npm audit --omit=dev` a cada deploy.

Consulte [arquitetura](docs/ARCHITECTURE.md), [auditoria](docs/SECURITY_AUDIT.md) e [licenças](THIRD_PARTY_NOTICES.md).
