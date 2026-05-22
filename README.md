# 🎰 AureusBet — Enterprise iGaming Platform

> Plataforma de cassino online completa, segura e pronta para produção, integrada com a blockchain Polygon e o token ERC-20 **AUR** (`0x7B7A787a84D1d9Bf10f5089Ad84dD1A6dBCA5f90`).

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Node](https://img.shields.io/badge/node-20+-green.svg)](https://nodejs.org)
[![TypeScript](https://img.shields.io/badge/typescript-5.4-blue.svg)](https://typescriptlang.org)
[![NestJS](https://img.shields.io/badge/nestjs-10-red.svg)](https://nestjs.com)
[![Next.js](https://img.shields.io/badge/next.js-15-black.svg)](https://nextjs.org)

---

## 📋 Índice

- [Visão Geral](#visão-geral)
- [Stack Tecnológico](#stack-tecnológico)
- [Arquitetura](#arquitetura)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Variáveis de Ambiente](#variáveis-de-ambiente)
- [Desenvolvimento](#desenvolvimento)
- [Deploy em Produção](#deploy-em-produção)
- [Jogos](#jogos)
- [Sistema de Colecionáveis](#sistema-de-colecionáveis)
- [Token AUR & Blockchain](#token-aur--blockchain)
- [Segurança](#segurança)
- [Compliance](#compliance)
- [API Documentation](#api-documentation)
- [Estrutura de Pastas](#estrutura-de-pastas)

---

## 🎯 Visão Geral

AureusBet é uma plataforma de apostas online enterprise com:

- **Jogos Provably Fair** — resultados verificáveis via HMAC-SHA256
- **Token Próprio AUR** — ERC-20 na rede Polygon
- **Colecionáveis NFT-like** — drops raros nos jogos, mercado P2P
- **KYC + AML** — compliance completo com verificação de identidade
- **Responsible Gambling** — auto-exclusão, limites de depósito, sessão
- **Admin Completo** — dashboard, analytics, gerenciamento total

---

## 🛠 Stack Tecnológico

### Backend
| Tecnologia | Versão | Função |
|---|---|---|
| Node.js | 20+ | Runtime |
| NestJS | 10 | Framework API |
| PostgreSQL | 16 | Banco de dados principal |
| Prisma | 5 | ORM + Migrations |
| Redis | 7 | Cache + Sessions + PubSub |
| Socket.io | 4 | WebSocket real-time |
| ethers.js | 6 | Integração Blockchain |
| Passport JWT | — | Autenticação |
| Bull | 4 | Filas de trabalho |

### Frontend
| Tecnologia | Versão | Função |
|---|---|---|
| Next.js | 15 | Framework React |
| TypeScript | 5.4 | Tipagem |
| TailwindCSS | 3.4 | Estilização |
| Zustand | 5 | Gerenciamento de estado |
| TanStack Query | 5 | Cache/fetching |
| Framer Motion | 11 | Animações |
| wagmi + Web3Modal | 2/5 | Integração Web3 |
| Socket.io Client | 4 | WebSocket |

### Infraestrutura
| Componente | Tecnologia |
|---|---|
| Containerização | Docker + Docker Compose |
| Proxy Reverso | Nginx |
| CDN / DDoS | Cloudflare |
| Storage | S3-compatible (MinIO) |
| SSL | Let's Encrypt |
| Monitoramento | Grafana + Prometheus |

---

## 🏗 Arquitetura

```
                    Cloudflare CDN/WAF
                           │
                    Nginx Proxy Reverso
                    ┌──────┼──────┐
                    │      │      │
               app.* admin.* api.*
                    │      │      │
              Next.js 15  Next.js  NestJS 10
                              │
                    ┌─────────┼─────────┐
                    │         │         │
               PostgreSQL  Redis    S3/MinIO
                    │
                Prisma ORM
                    │
            ┌───────┼───────┐
            │       │       │
         Users   Games  Payments
            │       │       │
         Auth   Crash+RNG  Stripe/PIX
            │       │       │
         2FA   Roulette  Blockchain
            │   Blackjack  (Polygon)
         KYC    Dice/Mines    │
            │   Slots/Tower  AUR
         AML   CoinFlip    Token
```

---

## 📦 Pré-requisitos

- Node.js 20+
- Docker & Docker Compose v2
- PostgreSQL 16 (ou via Docker)
- Redis 7 (ou via Docker)

---

## 🚀 Instalação

### 1. Clonar e configurar

```bash
git clone https://github.com/matheuscoopscel-max/que1ss0m3uf1c4lm4a
cd aureusbet

# Configurar variáveis de ambiente
cp .env.example .env
# Edite .env com seus valores reais
nano .env
```

### 2. Instalar dependências

```bash
# Backend
cd apps/backend && npm install && cd ../..

# Frontend
cd apps/frontend && npm install && cd ../..
```

### 3. Subir infraestrutura com Docker

```bash
cd infra/docker
docker compose up -d postgres redis minio
cd ../..
```

### 4. Configurar banco de dados

```bash
cd apps/backend

# Executar migrations
npx prisma migrate dev --name init

# Gerar Prisma Client
npx prisma generate

# Executar seed inicial
npm run db:seed
```

### 5. Iniciar em desenvolvimento

```bash
# Terminal 1 — Backend
cd apps/backend && npm run start:dev

# Terminal 2 — Frontend
cd apps/frontend && npm run dev
```

Acesse:
- **Frontend:** http://localhost:3000
- **API:**       http://localhost:3001/api/v1
- **Swagger:**   http://localhost:3001/api/docs
- **MinIO:**     http://localhost:9001

---

## 🔑 Variáveis de Ambiente

Copie `.env.example` para `.env` e preencha **TODOS** os valores. Campos críticos:

| Variável | Descrição | Exemplo |
|---|---|---|
| `JWT_SECRET` | Segredo JWT (min 64 chars) | `openssl rand -hex 64` |
| `ENCRYPTION_KEY` | Chave AES-256 | `openssl rand -hex 32` |
| `DATABASE_URL` | Connection string PostgreSQL | `postgresql://...` |
| `REDIS_PASSWORD` | Senha Redis forte | — |
| `STRIPE_SECRET_KEY` | Chave secreta Stripe | `sk_live_...` |
| `PIX_API_KEY` | Chave do gateway PIX | — |
| `PLATFORM_WALLET_PRIVATE_KEY` | Chave privada da carteira | **NUNCA exponha** |
| `POLYGON_RPC_URL` | RPC Polygon (Alchemy/Infura) | `https://...` |

> ⚠️ **NUNCA** commite o arquivo `.env` real. Use secrets manager em produção.

---

## 💻 Desenvolvimento

### Comandos backend

```bash
# Iniciar em watch mode
npm run start:dev

# Build
npm run build

# Migrations
npm run db:migrate       # Dev (cria migration)
npm run db:deploy        # Produção (aplica migrations)
npm run db:studio        # Prisma Studio GUI
npm run db:seed          # Seed inicial

# Lint
npm run lint
```

### Comandos frontend

```bash
npm run dev       # Iniciar dev server
npm run build     # Build produção
npm run start     # Servir build produção
npm run lint      # ESLint
```

---

## 🌐 Deploy em Produção

### Passo a passo

```bash
# 1. Preparar servidor (Ubuntu 22.04/24.04)
chmod +x infra/scripts/setup-server.sh
sudo ./infra/scripts/setup-server.sh

# 2. Configurar SSL
chmod +x infra/scripts/ssl-setup.sh
./infra/scripts/ssl-setup.sh aureusbet.com admin@aureusbet.com

# 3. Preencher .env com valores de produção
cp .env.example .env
nano .env

# 4. Deploy
chmod +x infra/scripts/deploy.sh
./infra/scripts/deploy.sh v1.0.0
```

### CI/CD (GitHub Actions)

Adicione os secrets no repositório e use o workflow em `.github/workflows/deploy.yml` (a ser criado conforme sua pipeline).

### Backup automático

```bash
# Configurar backup diário às 2h
echo "0 2 * * * /opt/aureusbet/infra/scripts/backup.sh >> /var/log/aureusbet-backup.log 2>&1" | crontab -
```

---

## 🎮 Jogos

Todos os jogos usam **Provably Fair** baseado em HMAC-SHA256:

```
resultado = HMAC-SHA256(serverSeed, clientSeed:nonce:cursor)
```

| Jogo | Descrição | Multiplicador Máx |
|---|---|---|
| **Crash** | Multiplicador em alta até crashar | ∞ |
| **Roulette** | Roleta europeia (0-36) | 35x |
| **Blackjack** | 21 clássico | 2x |
| **Slots** | Caça-níqueis 5 linhas | 1000x |
| **Dice** | Dado 0.00-99.99 over/under | até 99x |
| **Mines** | Grade 5x5 com minas ocultas | até 24x |
| **Tower** | Escada crescente | até 200x |
| **CoinFlip** | Cara ou coroa | 2x |

### Verificar resultado (Provably Fair)

```bash
curl https://api.aureusbet.com/api/v1/games/verify/{roundId}
```

---

## 🏆 Sistema de Colecionáveis

Colecionáveis são imagens únicas que os jogadores podem ganhar durante as partidas:

- **Drop Rate** configurável por jogo no Admin
- **Raridades:** Comum, Raro, Épico, Lendário, Mítico
- **Supply limitado** — cada colecionável tem um número máximo de exemplares
- **Transferível** — pode ser enviado para outros jogadores
- **Mercado P2P** — venda e compra usando AUR (5% de taxa da plataforma)

### Raridades e probabilidades (exemplo)

| Raridade | Drop Weight | Cor |
|---|---|---|
| Comum | 15 | Cinza |
| Raro | 5 | Azul |
| Épico | 2 | Roxo |
| Lendário | 0.5 | Dourado |
| Mítico | 0.1 | Vermelho |

---

## ⛓ Token AUR & Blockchain

### Informações do Token

- **Nome:** Aureus (AUR)
- **Padrão:** ERC-20
- **Rede:** Polygon (MATIC)
- **Endereço:** `0x7B7A787a84D1d9Bf10f5089Ad84dD1A6dBCA5f90`

### Funcionalidades

| Funcionalidade | Descrição |
|---|---|
| Depósito interno | PIX ou cartão → AUR creditado internamente |
| Depósito on-chain | Envie AUR direto da sua carteira via Polygon |
| Saque on-chain | Saque AUR para sua carteira Polygon |
| Saque via PIX | AUR → BRL via PIX |
| Vincular carteira | Conecte MetaMask ou WalletConnect |
| Histórico | Todas as transações internas e on-chain |

### Fluxo de depósito on-chain

1. Usuário vincula carteira → assina mensagem (ECDSA)
2. Envia AUR para endereço da plataforma
3. Fornece TxHash → sistema verifica on-chain
4. Saldo interno creditado automaticamente

---

## 🔒 Segurança

### Autenticação
- JWT com refresh token rotation (família de tokens)
- Detecção de reuso de refresh token (revogar família inteira)
- 2FA TOTP (Google Authenticator) com backup codes criptografados
- Lockout após 5 tentativas falhas (30 min)

### Proteções
- **Helmet.js** — Headers de segurança (CSP, HSTS, etc.)
- **Rate Limiting** — Por IP, por rota (NestJS Throttler)
- **CORS** estrito — apenas origens autorizadas
- **Validação** — class-validator em todos os DTOs
- **Sanitização** — whitelist de campos (forbidNonWhitelisted)
- **SQL Injection** — Prisma com queries parametrizadas
- **XSS** — Next.js + CSP headers
- **CSRF** — Cookies SameSite=Strict

### Dados sensíveis
- Senhas: bcrypt rounds=12
- 2FA secrets: AES-256-CBC
- Chaves privadas: variáveis de ambiente (nunca no código)
- KYC docs: S3 privado com URLs assinadas (5min)
- Dados bancários: criptografados em repouso

---

## 📋 Compliance

### KYC (Know Your Customer)
- Upload de documentos (CPF, RG, CNH, Selfie, Comprovante)
- Review manual pelo admin com aprovação/rejeição
- Documentos armazenados em bucket privado S3

### AML (Anti Money Laundering)
- Score de risco automático por transação
- Flags: valor alto, alta frequência, structuring, conta nova
- Relatórios de atividade suspeita
- Revisão obrigatória antes de processar

### Responsible Gambling
- Limites de depósito (diário/semanal/mensal)
- Auto-exclusão (7/30/90/180 dias ou permanente)
- Limite de tempo de sessão
- Bloqueio de menores de 18 anos

### LGPD / GDPR
- Consentimento explícito no cadastro
- Direito de exclusão de dados
- Logs de auditoria completos
- Política de privacidade clara

---

## 📚 API Documentation

Com o ambiente de desenvolvimento rodando, acesse:

```
http://localhost:3001/api/docs
```

### Endpoints principais

```
POST /api/v1/auth/register           Cadastro
POST /api/v1/auth/login              Login
POST /api/v1/auth/refresh            Renovar token
POST /api/v1/auth/2fa/setup          Configurar 2FA
POST /api/v1/auth/2fa/enable         Ativar 2FA

GET  /api/v1/users/me                Perfil atual
PATCH /api/v1/users/me               Atualizar perfil
POST /api/v1/users/me/kyc            Enviar KYC

GET  /api/v1/payments/balance        Saldo
POST /api/v1/payments/deposit/pix    Depositar via PIX
POST /api/v1/payments/deposit/stripe Depositar via cartão
POST /api/v1/payments/withdraw       Solicitar saque

GET  /api/v1/games                   Listar jogos
POST /api/v1/games/crash/bet         Apostar no Crash
POST /api/v1/games/crash/cashout     Sacar do Crash
GET  /api/v1/games/verify/:roundId   Verificar provably fair

GET  /api/v1/collectibles            Listar colecionáveis
GET  /api/v1/collectibles/inventory  Meu inventário
POST /api/v1/collectibles/transfer   Transferir colecionável

GET  /api/v1/market/orders           Ordens abertas
POST /api/v1/market/list             Listar para venda
POST /api/v1/market/buy/:orderId     Comprar

GET  /api/v1/blockchain/token-info   Info do token AUR
POST /api/v1/blockchain/link-wallet  Vincular carteira
POST /api/v1/blockchain/verify-deposit Verificar depósito on-chain
```

---

## 📁 Estrutura de Pastas

```
aureusbet/
├── apps/
│   ├── backend/                    # NestJS API
│   │   ├── src/
│   │   │   ├── modules/
│   │   │   │   ├── auth/           # Autenticação, JWT, 2FA
│   │   │   │   ├── users/          # Perfil, KYC, limites
│   │   │   │   ├── games/          # Crash, Roulette, etc.
│   │   │   │   │   ├── crash/      # Crash game completo
│   │   │   │   │   ├── roulette/   # Roleta europeia
│   │   │   │   │   ├── dice/       # Dado provably fair
│   │   │   │   │   ├── mines/      # Campo minado
│   │   │   │   │   └── rng.service.ts # RNG provably fair
│   │   │   │   ├── payments/       # Carteira, Stripe, PIX, AML
│   │   │   │   ├── blockchain/     # Token AUR, Polygon
│   │   │   │   ├── collectibles/   # Sistema de colecionáveis
│   │   │   │   ├── market/         # Mercado P2P
│   │   │   │   ├── admin/          # Painel admin completo
│   │   │   │   ├── websocket/      # Gateways Socket.io
│   │   │   │   ├── notifications/  # E-mails transacionais
│   │   │   │   ├── audit/          # Logs de auditoria
│   │   │   │   ├── storage/        # S3 upload service
│   │   │   │   └── health/         # Health checks
│   │   │   ├── common/
│   │   │   │   ├── decorators/     # @CurrentUser, @Roles, etc.
│   │   │   │   ├── filters/        # Exception filter global
│   │   │   │   ├── guards/         # RolesGuard
│   │   │   │   ├── interceptors/   # Transform, Logging
│   │   │   │   └── services/       # PrismaService
│   │   │   └── config/             # Validação de env vars
│   │   └── prisma/
│   │       ├── schema.prisma       # Schema completo (30+ models)
│   │       └── seed/seed.ts        # Seed inicial
│   │
│   └── frontend/                   # Next.js 15
│       └── src/
│           ├── app/                # App Router
│           │   ├── page.tsx        # Home
│           │   ├── crash/          # Jogo Crash
│           │   └── auth/           # Login, Register
│           ├── components/
│           │   ├── layout/         # Navbar, Sidebar, etc.
│           │   ├── games/          # Componentes de jogos
│           │   │   └── crash/      # CrashGame completo
│           │   ├── wallet/         # WalletMenu
│           │   ├── ui/             # UserMenu, NotificationBell
│           │   └── collectibles/   # Inventário, colecionáveis
│           ├── store/              # Zustand stores
│           ├── hooks/              # useCrashSocket, etc.
│           └── lib/
│               ├── api/            # Axios client + React Query
│               ├── web3/           # wagmi + WalletConnect
│               └── utils/          # format, helpers
│
├── infra/
│   ├── docker/
│   │   ├── docker-compose.yml      # Desenvolvimento
│   │   └── docker-compose.prod.yml # Produção
│   ├── nginx/
│   │   ├── nginx.conf              # Config global Nginx
│   │   └── conf.d/app.conf         # Vhosts e proxy
│   └── scripts/
│       ├── deploy.sh               # Deploy em produção
│       ├── setup-server.sh         # Setup servidor Ubuntu
│       ├── ssl-setup.sh            # Let's Encrypt SSL
│       ├── backup.sh               # Backup PostgreSQL
│       └── restore.sh              # Restaurar backup
│
├── packages/
│   └── shared/                     # Types e utils compartilhados
│
├── .env.example                    # Template de variáveis
├── .gitignore
└── README.md                       # Este arquivo
```

---

## 📄 Licença

MIT License — veja [LICENSE](LICENSE) para detalhes.

---

## ⚠️ Aviso Legal

Esta plataforma é para uso em jurisdições onde jogos de azar online são regulamentados e permitidos. O operador é responsável por obter todas as licenças necessárias e garantir compliance com as leis locais. Jogue com responsabilidade.

---

*Desenvolvido com ♥ pela equipe AureusBet*
