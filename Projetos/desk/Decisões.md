---
projeto: desk
tags:
  - projeto/desk
  - decisoes
---

# Decisões — desk

Escolhas técnicas importantes, da mais recente para a mais antiga. Contexto geral em [[Projetos/desk/Visão geral|Visão geral]].

## 2026-10-27 — Pausar `feat/planos-assinaturas`
- Branch parada em `19aeb0f` (tudo commitado) para voltar à `feat/pagina-inicial`.
- O banco local ficou com as 18 migrações dessa branch (todas aditivas); a página inicial roda sobre ele.
- **Para retomar:** checkout, `prisma generate`, `prisma migrate deploy`.

## 2026-10-27 — Plano Free não vincula máquinas
- Máquina sem grupo = solicitante Free; pode pedir conexão a qualquer alvo vinculado, mas só com aprovação manual.
- Limites de sessão (30 min, 1 por vez, 60 min de espera, reconexão em 2 min) valem só para o **solicitante**.
- Pendente: alvo sem grupo ainda não recebe conexão.

## 2026-10-27 — Convites e contas de técnico
- Convite por link: token SHA-256, válido por 72 h, uso único. Limite: Básico 2 / Pro 5 usuários (conta o Master e os pendentes).
- Contas `kind=TECNICO` são exclusivas do grupo; o nível 3 continua podendo cadastrar e desabilitar máquinas.
- Sem SMTP configurado, a API devolve o link ao Master.

## 2026-10-27 — Retenção de registros
- Tentativas de conexão: limpeza diária após 180 dias.
- Sessões: 3/6/12 meses conforme a configuração do Master (o usuário escolheu manter assim).
- Histórico em `/painel/historico` (substituiu a ConnectionsPage), com relatório PDF via pdfkit.

## 2026-10-26 — Agente desktop orientado à máquina, sem login
- Identidade por máquina: UUID v4 + chave Ed25519 em `C:\ProgramData\SuporteRemoto\` (cifrada e amarrada ao MachineGuid).
- Toda requisição do agente é assinada (cabeçalhos `X-Machine-*`, protocolo `SR1`).
- Planos Free / Básico / Pro definidos no servidor; contas antigas migradas para PRO; só ADMIN troca de plano.
- O login e o ID de 9 dígitos antigos deixam de existir. **Não reintroduzir login no agente.**
- Instalador passou a ser `perMachine` (pede UAC) e roda `--init-identity`.

## 2026-10-26 — Segurança das conexões
- Senha de acesso com Argon2id; bloqueio após 5 falhas por 15 min (por alvo + solicitante).
- Cofre de credenciais AES-256-GCM (chave obrigatória em produção).
- Autorização manual com token HMAC de uso único (60 s) e limite de 5 pedidos/min por origem.
- Whitelist avaliada antes de status/plano/senha; bloqueio e máquina inexistente respondem igual
  (`404 MACHINE_UNAVAILABLE`), para não revelar se a máquina existe.

## Início — Stack e acesso remoto próprio
- Vue 3 + Quasar para web e desktop (Electron) com o mesmo código; Fastify + Prisma + MySQL na API.
- Acesso remoto feito em casa via WebRTC, sem depender de software de terceiros.
