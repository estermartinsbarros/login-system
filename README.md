# Sistema Web de Autenticação e Gerenciamento de Acessos com JWT

Aplicação web de autenticação e gerenciamento de acessos desenvolvida com **Next.js, TypeScript, PostgreSQL e Prisma ORM**. O projeto utiliza JSON Web Tokens (JWT) para autenticação, bcrypt para proteção de senhas e controle de acesso baseado em papéis (RBAC).

A aplicação é executada em containers Docker e possui uma imagem publicada no Docker Hub.

## Tecnologias utilizadas

- **Next.js 16** — framework para desenvolvimento web.
- **React 19** — construção da interface.
- **TypeScript** — tipagem estática.
- **PostgreSQL 18** — banco de dados relacional.
- **Prisma ORM 7** — modelagem e comunicação com o banco de dados.
- **JWT (jose)** — geração e validação de tokens.
- **bcryptjs** — hash e verificação de senhas.
- **Tailwind CSS 4** — estilização da interface.
- **Docker** — conteinerização da aplicação e do banco de dados.
- **Docker Compose** — gerenciamento dos serviços.
- **Docker Hub** — armazenamento e distribuição da imagem da aplicação.

## Funcionalidades

### Autenticação
- Cadastro de usuários.
- Login com e-mail e senha.
- Proteção de senhas utilizando hash bcrypt.
- Autenticação baseada em JWT.
- Gerenciamento de sessões no banco de dados.
- Logout com revogação de sessão.

### Controle de acesso
- Controle de permissões baseado em papéis (RBAC).
- Dois níveis de acesso: `ADMIN` e `USER`.
- Proteção de rotas do dashboard.
- Acesso ao dashboard restrito a usuários administradores.

### Registro de atividades
- Registro de tentativas de login.
- Registro de cadastros.
- Registro de logouts.
- Armazenamento do status das operações de autenticação.

## Estrutura do projeto

```text
login-system/
├── app/
│   ├── (auth)/
│   │   └── login/
│   │       ├── cadastro/
│   │       └── page.tsx
│   ├── api/
│   │   ├── auth/
│   │   │   ├── login/
│   │   │   ├── logout/
│   │   │   └── register/
│   │   └── admin/
│   ├── dashboard/
│   │   ├── atividades/
│   │   ├── configuracoes/
│   │   ├── sessoes/
│   │   ├── usuarios/
│   │   ├── layout.tsx
│   │   └── page.tsx
│   └── services/
├── components/
│   └── dashboard/
├── lib/
│   ├── jwt.ts
│   ├── prisma.ts
│   └── admin-guard.ts
├── prisma/
│   ├── migrations/
│   └── schema.prisma
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
├── prisma7.config.ts
├── .env.example
└── package.json
```

## Requisitos

Antes de começar, você precisará ter instalado:

- Git.
- Docker.
- Docker Compose.

Para executar em ambiente de desenvolvimento local, também serão necessários:

- Node.js.
- npm.

## Execução com Docker

A aplicação possui uma imagem publicada no Docker Hub:

**Imagem:** `estermsb/login-system-web`

**Tag:** `v1`

[Visualizar imagem no Docker Hub](https://hub.docker.com/r/estermsb/login-system-web)

### 1. Clonar o repositório

```bash
git clone URL_DO_REPOSITORIO
cd login-system
```

### 2. Configurar as variáveis de ambiente

Crie o arquivo `.env`:

```bash
cp .env.example .env
```

Configure as variáveis necessárias:

```env
DATABASE_URL="postgresql://authsystem_user:SENHA_DO_POSTGRES@localhost:5432/authsystem?schema=public"
JWT_SECRET="SEU_SEGREDO_JWT"
```

A senha da conexão deve corresponder à senha configurada para o PostgreSQL no `docker-compose.yml`.

Para gerar um segredo JWT aleatório:

```bash
openssl rand -hex 32
```

**Importante:** nunca envie o arquivo `.env` para o GitHub ou para o Docker Hub.

### 3. Iniciar o banco de dados

```bash
docker compose up -d db
```

O comando inicia o PostgreSQL em um container.

### 4. Aplicar as migrations

Com o banco iniciado, execute:

```bash
npx prisma migrate deploy
```

Esse comando aplica as migrations existentes e prepara as tabelas necessárias para a aplicação.

### 5. Baixar a imagem do Docker Hub

```bash
docker compose pull web
```

O Docker baixa a imagem publicada:

```text
estermsb/login-system-web:v1
```

### 6. Iniciar a aplicação

```bash
docker compose up -d
```

O Docker Compose inicia a aplicação Next.js e o PostgreSQL.

A aplicação estará disponível em:

```text
http://localhost:9000
```

### 7. Verificar os containers

```bash
docker compose ps
```

Para consultar os logs da aplicação:

```bash
docker compose logs web
```

Para interromper os serviços:

```bash
docker compose down
```

Esse comando preserva o volume do banco de dados. Não utilize `docker compose down -v` se quiser preservar os dados armazenados.

## Execução em desenvolvimento local

Para desenvolver e modificar o código-fonte, utilize os seguintes procedimentos.

### 1. Instalar as dependências

```bash
npm install
```

### 2. Configurar o ambiente

Crie o arquivo `.env` conforme as instruções anteriores.

### 3. Iniciar o PostgreSQL

```bash
docker compose up -d db
```

### 4. Aplicar as migrations e gerar o Prisma Client

```bash
npx prisma migrate deploy
npx prisma generate
```

### 5. Executar a aplicação

```bash
npm run dev
```

A aplicação estará disponível em:

```text
http://localhost:3000
```

## Rotas da aplicação

| Rota | Descrição |
|---|---|
| `/login` | Página de autenticação |
| `/login/cadastro` | Cadastro de usuários |
| `/perfil` | Perfil do usuário |
| `/dashboard` | Painel administrativo |
| `/dashboard/usuarios` | Gerenciamento de usuários |
| `/dashboard/atividades` | Registro de atividades |
| `/dashboard/sessoes` | Gerenciamento de sessões |
| `/dashboard/configuracoes` | Configurações administrativas |

O dashboard é protegido por autenticação e autorização: somente usuários com o papel `ADMIN` podem acessá-lo.

## Endpoints da API

### Autenticação

| Método | Endpoint | Descrição |
|---|---|---|
| POST | `/api/auth/register` | Cadastro de usuário |
| POST | `/api/auth/login` | Autenticação |
| POST | `/api/auth/logout` | Encerramento da sessão |

### Administração

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/api/admin/dashboard` | Dados do dashboard |
| GET | `/api/admin/users` | Listagem de usuários |
| GET | `/api/admin/logs` | Consulta de registros |
| GET | `/api/admin/sessions` | Consulta de sessões |
| PATCH | `/api/admin/users/[id]` | Gerenciamento de usuário |
| POST | `/api/admin/sessions/[id]/revoke` | Revogação de sessão |
| PATCH | `/api/admin/settings/password` | Alteração de senha administrativa |

## Banco de dados

O sistema utiliza PostgreSQL com Prisma ORM.

O banco possui três modelos principais:

- **User:** armazena os dados dos usuários, incluindo e-mail, hash da senha e papel de acesso.
- **Session:** armazena as sessões, identificadores dos tokens, datas de expiração e revogação.
- **AccessLog:** registra eventos de autenticação, seus respectivos status e informações disponíveis.

Os papéis de acesso são definidos pelo enum `Role`:

- `ADMIN`: usuário com permissão para acessar o dashboard administrativo.
- `USER`: usuário padrão, sem acesso ao dashboard administrativo.

Novos usuários recebem automaticamente o papel `USER`. A promoção para `ADMIN` é realizada diretamente no banco de dados.

## Docker Hub

A imagem da aplicação está disponível publicamente no Docker Hub.

**Repositório:** [estermsb/login-system-web](https://hub.docker.com/r/estermsb/login-system-web)

Para baixar diretamente a imagem:

```bash
docker pull estermsb/login-system-web:v1
```

Para construir uma nova versão localmente:

```bash
docker build -t estermsb/login-system-web:v2 .
```

Para publicar a nova versão:

```bash
docker push estermsb/login-system-web:v2
```

As variáveis de ambiente e as credenciais do banco de dados são configuradas durante a execução dos containers, não devendo ser armazenadas na imagem.

## Desenvolvimento

Para verificar os tipos TypeScript:

```bash
npx tsc --noEmit
```

Para executar a verificação de código:

```bash
npm run lint
```

Para gerar a versão de produção:

```bash
npm run build
```

## Segurança

O projeto implementa algumas medidas de segurança:

- Senhas armazenadas por meio de hash bcrypt.
- Tokens JWT assinados com segredo definido por variável de ambiente.
- Cookie de autenticação configurado como `HttpOnly`.
- Controle de expiração e revogação de sessões.
- Verificação de autenticação e autorização nas rotas protegidas.
- Separação entre aplicação e banco de dados em containers distintos.
- Configuração de segredos por variáveis de ambiente.

**Observação:** este projeto tem finalidade educacional e de demonstração. Antes de utilizá-lo em produção, são necessários testes adicionais e uma revisão de segurança.

## Integrantes

- Ester Martins
- Bruno Vital
