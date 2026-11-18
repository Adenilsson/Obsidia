---
data: 2026-11-18
tags:
  - pesquisa
  - ia
aliases:
  - Ferramenta de IA
---

# Como construir uma ferramenta de IA

> [!abstract] Resumo
> Construir uma ferramenta de IA quase nunca é treinar um modelo do zero. É **usar um modelo pronto** e escrever um programa em volta dele: modelo + instruções + ferramentas + interface.

## As 4 peças

### 1. O modelo (o "cérebro")
- **Na nuvem:** API como a da Claude (Anthropic). É o mais inteligente e rápido, e você paga por uso.
  Linha atual (nov/2026): Haiku 5.5 (barato e rápido), Sonnet 5.5 (equilíbrio), Opus 5.5 (o mais capaz).
- **Local:** Colibri + Qwen3.6-35B, já instalado nesta máquina.
  - API compatível com OpenAI e Anthropic em `http://127.0.0.1:8000` (modelo `qwen3.6-colibri`).
  - Iniciar com `D:\colibri\iniciar-cuda.bat`.
  - É de graça e privado, mas lento (~5 tokens/s com CUDA na RTX 3050) e erra mais.

### 2. As instruções (o prompt)
Um texto fixo que diz quem a IA é e como responder.
Ex.: *"Você é um assistente que lê contratos e devolve os prazos em uma tabela."*
Isso define a maior parte do comportamento da ferramenta.

### 3. As ferramentas (o que a IA pode fazer)
São funções que o seu programa oferece à IA, como ler arquivo, consultar banco, mandar e-mail ou buscar na web. O ciclo:
1. a IA pede para usar uma função;
2. o seu código executa;
3. o resultado volta para a IA, que decide o próximo passo.

É assim que o aider cria e edita arquivos usando o Colibri.

### 4. A interface
Terminal, página web, bot (WhatsApp/Telegram) ou um botão dentro de um sistema que já existe.

## Qual nível de complexidade usar

> [!tip] Comece sempre pelo mais simples

| Tipo | Exemplo | Como fazer |
|---|---|---|
| Uma pergunta, uma resposta | Resumir, classificar ou extrair dados de um texto | Uma chamada à API |
| Fluxo com etapas fixas | Ler PDF → extrair dados → salvar no banco | O seu código controla as etapas e chama a IA em cada uma |
| Agente | "Organize esta pasta", "corrija este bug" | A IA decide sozinha quais ferramentas usar e em que ordem |

## Caminho mais curto para começar
1. Escolher uma tarefa pequena e concreta que você repete muito.
2. Testar o prompt na mão, no chat, até a resposta ficar boa.
3. Escrever um script que manda esse prompt para a API (Python ou TypeScript).
4. Só depois acrescentar ferramentas, interface e o resto.

## Próximo passo
- [ ] Definir **o que** a ferramenta vai fazer e **onde** será usada (terminal, web, dentro do [[Projetos/desk/Visão geral|desk]] ou outro lugar).
- [ ] Escolher o modelo: API da Claude ou Colibri local.
