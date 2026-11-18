# upload.ai

Aplicação que transforma um vídeo em texto e usa IA para gerar conteúdo a partir dele, como títulos e descrições para o YouTube.

Feito durante o NLW IA da Rocketseat (setembro de 2023). Este repositório junta a API e a interface web, que antes ficavam em `upload-ai-api` e `upload-ai-web`. O histórico de commits dos dois foi preservado.

## Como funciona

1. O usuário escolhe um vídeo `.mp4` na interface web.
2. O **ffmpeg.wasm** converte o vídeo em MP3 **no próprio navegador**, sem enviar o vídeo inteiro para o servidor.
3. A API recebe o áudio e gera a transcrição com o **Whisper** da OpenAI. Palavras-chave informadas pelo usuário ajudam na transcrição.
4. O usuário escolhe um prompt pronto (título ou descrição para o YouTube) ou escreve o seu, usando a variável `{transcription}`, e ajusta a temperatura.
5. A API envia o prompt ao GPT-3.5 e devolve a resposta **em streaming**, que aparece na tela enquanto é gerada.

## Estrutura

```
upload-ai
├── api/    # Node.js, Fastify, Prisma (SQLite), OpenAI
└── web/    # React, Vite, Tailwind, shadcn/ui, ffmpeg.wasm
```

### API (`api/`)

| Rota | O que faz |
|---|---|
| `GET /prompts` | Lista os prompts prontos |
| `POST /videos` | Recebe o MP3 (até 25 MB) e registra o vídeo |
| `POST /videos/:videoId/transcription` | Gera a transcrição com o Whisper |
| `POST /ai/complete` | Gera o texto com o GPT-3.5 em streaming |

Stack: Node.js, TypeScript, Fastify, Prisma com SQLite, Zod, SDK da OpenAI e Vercel AI SDK.

### Web (`web/`)

Stack: React 18, TypeScript, Vite, Tailwind CSS, shadcn/ui (Radix UI), ffmpeg.wasm, Vercel AI SDK (`useCompletion`) e Axios.

## Como rodar

Pré-requisitos: Node.js 18+, pnpm e uma chave da API da OpenAI.

### 1. API

```bash
cd api
pnpm install
```

Crie o arquivo `api/.env`:

```
DATABASE_URL="file:./dev.db"
OPENAI_KEY="sua-chave-da-openai"
```

Prepare o banco, crie a pasta dos uploads e suba o servidor:

```bash
pnpm prisma migrate dev
pnpm prisma db seed
mkdir -p tmp
pnpm dev
```

A API fica em `http://localhost:3333`.

### 2. Web

Em outro terminal:

```bash
cd web
pnpm install
pnpm dev
```

Abra o endereço mostrado pelo Vite (normalmente `http://localhost:5173`).
