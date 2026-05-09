# Onboarding — Jarvis Atelier

> Bienvenido. Esta guía te lleva del *"recién entré al atelier"* al *"abrí mi primer PR"*. Si ya estás laburando hace rato y la abriste para confirmar algo puntual, andá directo al [Índice](#índice).

---

## Índice

- [Bienvenida](#bienvenida)
- [Lo que necesitás antes de empezar](#lo-que-necesitás-antes-de-empezar)
- [Día 1: setup inicial](#día-1-setup-inicial)
- [Tu primer task](#tu-primer-task)
  - [Caso A: te asignan un proyecto que ya existe](#caso-a-te-asignan-un-proyecto-que-ya-existe)
  - [Caso B: te toca crear un proyecto nuevo](#caso-b-te-toca-crear-un-proyecto-nuevo)
- [El día a día](#el-día-a-día)
- [Cómo darle contexto del atelier a tu asistente de IA](#cómo-darle-contexto-del-atelier-a-tu-asistente-de-ia)
- [Recursos y a quién preguntarle](#recursos-y-a-quién-preguntarle)

---

## Bienvenida

El atelier es un equipo chico que construye software a medida para clientes y productos propios. Trabajamos con **conceptos antes que código**: entender el problema antes de tocar el editor. **Tests reales** (unit + integration + e2e). **PRs revisados**. **Documentación viva** que refleja lo que hay, no lo que había.

Las decisiones grandes del atelier (cómo organizamos los repos, qué stacks usamos, cómo trabajamos) están documentadas. Esta guía te apunta a esos documentos en el orden correcto, así no tenés que adivinar dónde leer qué.

---

## Lo que necesitás antes de empezar

### Hardware
Una máquina con Linux, macOS, o Windows 11. Cualquiera anda. El atelier no fuerza un sistema operativo.

### Software base (instalar UNA vez por máquina)
| Tool | Para qué | Cómo |
|---|---|---|
| **Git** | Control de versiones | [git-scm.com](https://git-scm.com) |
| **Node.js LTS (≥20)** | Runtime para todo lo TS/JS del atelier | [nodejs.org](https://nodejs.org) o vía `nvm` / `fnm` |
| **pnpm** | Package manager default del atelier | `npm install -g pnpm` |
| **Docker Desktop** | Para correr Postgres / Redis / etc. localmente | [docker.com](https://www.docker.com/products/docker-desktop) |
| **Editor** | Lo que prefieras: VS Code, Cursor, Neovim, JetBrains | Tu elección |
| **Asistente de IA (opcional)** | Claude Code, Cursor, Copilot, lo que uses | Tu elección |

### Si vas a tocar Python
- **Python 3.12+** ([python.org](https://python.org) o vía `pyenv`)
- **`uv` o `pip`** para deps

### Si vas a tocar otros stacks
- **Go**: 1.22+
- **Rust**: vía `rustup`
- Otros: el README del repo te lo va a decir

### Cuentas que necesitás
- **GitHub** — con autenticación funcionando (HTTPS con Personal Access Token o SSH key)
- **Acceso a la org `jarvis-atelier`** — pediselo a un lead vía mensaje directo o en el canal del equipo

---

## Día 1: setup inicial

### 1. Pedí acceso a la org
Sin acceso a `github.com/jarvis-atelier` no ves los repos privados (que son la mayoría — clientes). Hablá con un lead, te van a invitar.

### 2. Autenticate con GitHub
**Opción HTTPS (recomendado para arrancar):**
```sh
# Generá un Personal Access Token en https://github.com/settings/tokens
# Cuando hagas tu primer git push, te va a pedir usuario+token
# Git lo cachea automáticamente
```

**Opción SSH (más cómodo a la larga):**
```sh
ssh-keygen -t ed25519 -C "tu-email@ejemplo.com"
cat ~/.ssh/id_ed25519.pub
# Copiá la output y pegala en https://github.com/settings/keys
```

### 3. Configurá Git con tu identidad
```sh
git config --global user.name "Tu Nombre"
git config --global user.email "tu-email@ejemplo.com"
git config --global init.defaultBranch main
git config --global pull.rebase false  # o true si preferís rebase
```

### 4. Creá tu workspace local

> ⚠️ **Concepto importante**: la org `jarvis-atelier` en GitHub es una **lista plana de repos**, no una carpeta. **Tu carpeta local NO refleja la estructura de la org**. Vos clonás solo los repos que vas a tocar, donde te quede cómodo.

Elegí UNA carpeta donde vivan tus repos del atelier. Ejemplos:

```
C:\dev\jarvis\         (Windows)
~/Code/atelier/         (macOS / Linux)
~/projects/jarvis/      (otra opción)
```

No hay convención oficial para la carpeta local — es tu organización personal.

### 5. Leé las convenciones del estudio
Antes de tocar código, leé estos cuatro documentos:

1. **[`profile/README.md`](https://github.com/jarvis-atelier/.github/blob/main/profile/README.md)** — qué hace el atelier, stack default
2. **[`projects/README.md`](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md)** — cómo se organizan los repos en la org, naming, topics, ciclo de vida, **árbol de decisión de templates**
3. **[`CONTRIBUTING.md`](https://github.com/jarvis-atelier/.github/blob/main/CONTRIBUTING.md)** — flujo de PRs, conventional commits, code review
4. **[`SECURITY.md`](https://github.com/jarvis-atelier/.github/blob/main/SECURITY.md)** — política de seguridad y reporte de vulnerabilidades

Tiempo total: ~30 minutos. **No te saltes este paso**, te ahorra preguntas tontas durante el primer mes.

---

## Tu primer task

Hay dos casos posibles según lo que te asignen.

### Caso A: te asignan un proyecto que YA existe

Lo más común. El proyecto vive en `github.com/jarvis-atelier/<nombre-del-repo>`. Ejemplo: te asignan `client-casa-salco`.

#### 1. Cloná el repo
```sh
cd C:\dev\jarvis\          # o tu workspace
git clone https://github.com/jarvis-atelier/client-casa-salco.git
cd client-casa-salco
```

#### 2. Leé el README del repo
Cada repo del atelier tiene un README con:
- **Stack** — qué tecnologías usa
- **Quickstart** — cómo levantar el proyecto local
- **Tests** — cómo correr los tests
- **Deploy** — cómo se deploya
- **Owners** — a quién preguntarle si te trabás

Si el README está incompleto o desactualizado, **abrí un issue o un PR pequeño arreglándolo**. Es la mejor forma de devolver valor al equipo desde tu primer día.

#### 3. Levantá el entorno local
Seguí las instrucciones del Quickstart del README. Si necesita Postgres / Redis / etc., normalmente vas a correr:
```sh
docker compose up -d
pnpm install
# o pip install, según el stack
```

#### 4. Corré los tests
```sh
pnpm test
# o pytest, según el stack
```

Si los tests no pasan en la rama `main`, **es bug del repo, no tuyo** — abrí un issue antes de empezar a hacer cambios.

#### 5. Creá tu rama y abrí el PR
```sh
git checkout -b feat/lo-que-vas-a-hacer
# trabajás
git add -A
git commit -m "feat(area): descripción concisa"
git push -u origin feat/lo-que-vas-a-hacer
gh pr create  # o desde la UI de GitHub
```

Lee `CONTRIBUTING.md` para el formato exacto del commit message y del PR title.

---

### Caso B: te toca crear un proyecto NUEVO

Acá es donde tenés que ser disciplinado. **Antes de tocar nada, hacete las 4 preguntas del catálogo**:

1. **¿Qué tipo de cosa es?** → `client-`, `product-`, `internal-`, `template-`, `lib-`, `config-`
2. **¿Polyrepo o monorepo?** → polyrepo es el default; monorepo es excepción para front+back de un mismo producto chico
3. **¿Qué stack?** → seguí el [árbol de decisión de templates](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md#cómo-elegir-cuál-usar)
4. **¿Quién es el owner?** → vos + un backup, definidos antes de crear el repo

#### Pasos para crear el proyecto

> Esto sigue el flujo oficial de [`projects/README.md` → "Cómo registrar un proyecto nuevo"](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md#cómo-registrar-un-proyecto-nuevo). Acá te lo expando con comandos concretos.

1. **Pedí aprobación al lead.** Sin OK, no creás el repo. El lead valida cliente, owner, stack tentativo.

2. **Creá el repo desde un template** — recomendado vía la UI de GitHub:
   - Vas a `github.com/jarvis-atelier`
   - Click en **"New repository"**
   - En **"Repository template"** elegís: `template-jarvis-base`, `template-jarvis-web`, o `template-jarvis-api`
   - **Owner**: `jarvis-atelier`
   - **Name**: seguí la [convención](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md#convención-de-nombres-de-repos) (`client-acme-portal-web`, `product-foo`, etc.)
   - **Visibilidad**: **privado** por default (cliente o producto pre-launch); público si es OSS o producto público
   - Click **Create repository**

   **Alternativa con `gh` CLI:**
   ```sh
   gh repo create jarvis-atelier/<nombre> \
     --private \
     --template jarvis-atelier/template-jarvis-web \
     --description "Una línea descriptiva" \
     --clone
   cd <nombre>
   ```

3. **Aplicá topics**. Desde la UI del repo (Settings → General → Topics) o vía `gh`:
   ```sh
   gh repo edit jarvis-atelier/<nombre> \
     --add-topic type-frontend \
     --add-topic lang-typescript \
     --add-topic stack-react \
     --add-topic stack-vite \
     --add-topic client-acme \
     --add-topic status-active
   ```

4. **Cloná localmente** (si no lo hiciste con `--clone`):
   ```sh
   cd C:\dev\jarvis\
   git clone https://github.com/jarvis-atelier/<nombre>.git
   cd <nombre>
   ```

5. **Adaptá los archivos heredados del template**:
   - **`README.md`** — borrá el banner inicial, reemplazá los placeholders con los datos reales del proyecto (Stack, Quickstart, Owners, etc.)
   - **`.github/CODEOWNERS`** — reemplazá `@jarvis-atelier/leads` por los handles reales del owner principal y backup
   - **`LICENSE`** — si es proyecto de cliente, reemplazá MIT por la licencia que corresponda
   - **`.github/dependabot.yml`** — el template ya viene con npm activo (web/api). Si tu stack es otro, descomenta el bloque correspondiente
   - **`.gitignore`** — descomentá los stack-specific que necesites
   - **`CLAUDE.md`** (si existe) — extendelo con instrucciones específicas del proyecto si vas a usar asistentes de IA

6. **Configurá branch protection** en `main`:
   - Settings → Branches → Add rule
   - Branch name pattern: `main`
   - ✅ Require pull request before merging (1 approve)
   - ✅ Require status checks to pass before merging (CI)
   - ✅ Require conversation resolution before merging

7. **Instalá deps + smoke test**:
   ```sh
   pnpm install
   pnpm test
   pnpm lint
   pnpm typecheck
   pnpm build
   ```
   Si algo falla acá, fijalo antes de seguir.

8. **Configurá variables de entorno** copiando `.env.example` a `.env` y llenándolas.

9. **REGISTRA el proyecto en el catálogo** — esto es crítico, no lo dejes para después. Abrí un PR contra `jarvis-atelier/.github` agregando una fila a [`projects/README.md` → Portfolio activo](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md#portfolio-activo).

   Sin paso 9, **el proyecto no existe oficialmente para el atelier**. Hubo casos en los que esto se saltó y después tuvimos que hacer back-fill en otra sesión — evitalo desde el día 1.

---

## El día a día

Una vez seteado, tu loop diario va a ser parecido a esto:

### Ramas y commits

- **Una rama por feature/fix**: `feat/<area>-<resumen-corto>`, `fix/<area>-<bug>`, `chore/<resumen>`, `docs/<tema>`
- **Conventional commits**: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`, `perf:`, `ci:`
  ```
  feat(auth): add password reset flow
  fix(api): handle empty responses from external service
  ```
- **Commits chicos y enfocados** — un cambio lógico por commit. Si vas a hacer 3 cosas, son 3 commits.

### Pull Requests

- Abrí PRs **chicos y revisables**. Si tu PR tiene >500 líneas de diff, probablemente debería ser dos.
- **El PR title** sigue conventional commits (lo mismo que el commit message). Esto facilita generar changelogs después.
- **El PR body** describe el "por qué" del cambio, links a issues si los hay, screenshots si es UI, test plan si es backend.
- **CI tiene que estar verde** antes de pedir review.
- **Conseguí 1 approve** (o 2 si el cambio es grande / sensible).

### Tests

- **Coverage mínimo del atelier: 80%** en código de negocio. Los templates ya vienen con threshold configurado.
- **Test-Driven Development cuando aplica** — escribís el test primero, después la implementación.
- **No mockees lo que podés testear contra realidad**: por ejemplo, en api template testeamos contra Postgres real (Docker compose) en vez de mockear Prisma.

### Code review

- **Review todo PR como si fuera tuyo** — vas a tener que mantenerlo después.
- **Comentarios concretos**, no "creo que esto se podría mejorar". Decí qué cambiarías y por qué.
- **No bloquees por preferencias personales**. Bloqueá por correctness, security, performance, mantenibilidad.

### Deploys

- Cada repo documenta su deploy en el README. Pipelines comunes:
  - **Railway / Render / Fly.io** para backends
  - **Vercel / Cloudflare Pages** para frontends estáticos / SPAs
  - **Docker** cuando hay infra propia
- **Migraciones de DB**: siempre `db:migrate:deploy` antes del start del nuevo container.

---

## Cómo darle contexto del atelier a tu asistente de IA

Si usás Claude Code, Cursor, Copilot, Continue, o cualquier asistente de IA, **necesitás darle contexto del atelier al principio de cada nueva sesión** — si no, te va a sugerir patterns genéricos que rompen las convenciones del estudio.

Hay dos formas, complementarias:

### Forma 1 — Pasiva (cargar contexto manual al arrancar)

Antes de tu primer prompt en un proyecto, decile al asistente:

```
Antes de tocar nada en este repo, leé estos URLs y entendé las convenciones del atelier Jarvis:

1. https://github.com/jarvis-atelier/.github/blob/main/profile/README.md
2. https://github.com/jarvis-atelier/.github/blob/main/projects/README.md
3. https://github.com/jarvis-atelier/.github/blob/main/CONTRIBUTING.md
4. https://github.com/jarvis-atelier/docs/blob/main/onboarding.md
5. https://github.com/jarvis-atelier/docs/blob/main/starter-templates.md

Esperá mi confirmación de que entendiste antes de hacer cambios.
```

Esto fuerza al asistente a hacer un fetch de los docs antes de actuar.

### Forma 2 — Activa (CLAUDE.md en el repo)

Los repos creados desde nuestros templates **vienen con un archivo `CLAUDE.md`** que ya le dice al asistente dónde mirar. Así, cuando arrancás Claude Code en la carpeta del repo, automáticamente carga ese contexto.

Si tu repo no lo tiene (proyecto viejo, no creado desde template), copialo de [`template-jarvis-base/CLAUDE.md`](https://github.com/jarvis-atelier/template-jarvis-base/blob/main/CLAUDE.md) y adaptalo.

### Patrones útiles

- **Ante decisiones arquitecturales grandes**: pedile al asistente *"antes de proponer una solución, fijate si el atelier ya tiene una convención para esto"* — evita reinventar la rueda.
- **Ante código de cliente**: *"este es código de cliente, no lo expongas en logs ni en commits ni en respuestas que vayan a un servicio externo"*. Importante en proyectos con NDA.
- **Ante PRs**: *"este PR tiene que cumplir con CONTRIBUTING.md del atelier — conventional commits, tests con 80% coverage, no Co-Authored-By"*.

---

## Recursos y a quién preguntarle

### Documentación oficial del atelier
- **[Catálogo de proyectos](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md)** — qué proyectos hay, owners, stacks
- **[Convenciones de contribución](https://github.com/jarvis-atelier/.github/blob/main/CONTRIBUTING.md)** — workflow, commits, PRs
- **[Política de seguridad](https://github.com/jarvis-atelier/.github/blob/main/SECURITY.md)** — cómo reportar vulnerabilidades
- **[Guía de starter templates](./starter-templates.md)** — qué incluye cada template, cómo extender, monorepo vs polyrepo

### Templates de la org
- **[`template-jarvis-base`](https://github.com/jarvis-atelier/template-jarvis-base)** — esqueleto universal stack-agnóstico
- **[`template-jarvis-web`](https://github.com/jarvis-atelier/template-jarvis-web)** — React + TS + Vite + Vitest
- **[`template-jarvis-api`](https://github.com/jarvis-atelier/template-jarvis-api)** — NestJS + Prisma + Postgres + Jest

### A quién preguntarle
- **Acceso a repos / permisos**: lead del estudio
- **Decisiones arquitecturales**: lead técnico del proyecto (figura en CODEOWNERS del repo)
- **Bugs / features de un proyecto específico**: owner del proyecto (figura en el catálogo y en `.github/CODEOWNERS`)
- **Cómo arrancar / dudas de onboarding**: cualquier dev senior del atelier; o leer esta guía dos veces, está casi todo acá

---

> ¿Encontraste algo que falta o está mal en esta guía? **Abrí un PR contra este archivo**. La onboarding de los próximos devs te lo va a agradecer.
