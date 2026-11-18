---
projeto: desk
nome-produto: BridgeGate
repo: https://github.com/Pedro-fairtek/desk
pasta-local: C:\Users\nosli\OneDrive\Documentos\desk
status: em desenvolvimento
atualizado: 2026-11-18
tags:
  - projeto/desk
---

# desk — Suporte Remoto (BridgeGate)

Software interno de suporte técnico e **acesso remoto próprio via WebRTC** (tela, mouse/teclado,
bloqueio de entrada local, transferência de arquivos), com inventário de dispositivos, chamados e
sessões auditadas. Ver também [[Projetos/desk/Decisões|Decisões]] e o [[Projetos/desk/Diário/2026-11-18|diário]].

## Stack

- **Painel**: Vue 3 + TypeScript + Quasar (`apps/web`) — roda como SPA **e** como app desktop Electron
- **API**: Node.js + TypeScript + Fastify (`services/api`)
- **Banco**: MySQL 8 + Prisma ORM
- **Monorepo**: pnpm workspaces, Node ≥ 20
- **Infra**: Docker Compose (`infrastructure/docker` local, `infrastructure/vps` produção de teste)

## Como rodar localmente

```bash
pnpm install
pnpm db:up            # MySQL em localhost:3308 + Adminer em :8081
pnpm prisma:generate
pnpm --filter @suporte-remoto/api exec prisma migrate deploy
pnpm prisma:seed      # usuários admin/tecnico/solicitante@suporte.local
pnpm dev:api          # http://localhost:3333
pnpm dev:web          # http://localhost:9000
pnpm dev:desktop      # alternativa: janela Electron
```

> [!tip] Atalho
> `teste-local.bat` (branch `feat/pagina-inicial`) sobe Docker/MySQL, migrações, API, painel e gera
> um instalador apontando para o IP da rede.

> [!warning] Detalhes do ambiente Windows
> - Docker Desktop precisa estar aberto; no Git Bash o `docker` não está no PATH
>   (`/c/Program Files/Docker/Docker/resources/bin/docker.exe`).
> - `apps/web/.env` aponta para a API da VPS — **não alterar**. Para usar a API local, criar
>   `apps/web/.env.local` com `QCLI_API_BASE_URL=http://localhost:3333/api` e apagá-lo depois.

## Build e deploy

- Instaladores: `pnpm build:desktop:win` (NSIS `.exe`) e `pnpm build:desktop:linux` (`.AppImage`/`.deb`).
- GitHub Actions: `build-desktop.yml` e `deploy-vps.yml`.
- **VPS de teste** (HostGator, Ubuntu 22.04): projeto em `/root/desk`, SSH na porta 22022, painel na porta 8080
  (a porta 80 é do app imobrasil).

> [!bug] Pendente
> Depois da reinstalação da VPS em 2026-10-23, o workflow "Deploy VPS" nunca mais conectou.
> Conferir o status antes de confiar nele e não misturar `git pull` na VPS com o deploy por tar do Actions.

## Funcionalidades

- Login JWT com RBAC (ADMIN / TECHNICIAN / REQUESTER) e permissões por dispositivo/equipe checadas no backend
- Inventário de dispositivos, chamados (tickets) e ciclo de vida de sessão remota auditado (`AuditEvent`)
- Acesso remoto WebRTC próprio, multi-conexão com abas, cards de conexões recentes

## Branches relevantes

- `main`: linha principal (PRs vindos de `features`)
- `feat/pagina-inicial`: trabalho em andamento em 2026-10-27
- `feat/planos-assinaturas`: **pausada** em `19aeb0f` — agente por máquina, planos e convites (ver [[Projetos/desk/Decisões|Decisões]])

## Pendências conhecidas

- OIDC corporativo e MFA (hoje a autenticação é local por e-mail/senha)
- HTTPS (o agente só avisa; `QCLI_REQUIRE_TLS=true` bloqueia HTTP)
- Deploy automático na VPS quebrado
- Fazer backup do banco antes de aplicar as migrações da branch de planos na VPS
