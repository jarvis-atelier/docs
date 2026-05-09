# Starter Templates — Guía completa

> **Companion del catálogo.** El [catálogo](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md#templates-disponibles) te dice CUÁL elegir; este doc te explica POR QUÉ existen, QUÉ traen por dentro y CÓMO se extienden.
>
> Si solo querés saber qué template usar, no leas todo esto — andá al árbol de decisión del catálogo.

---

## Tabla de contenidos

- [Filosofía: por qué tres templates](#filosofía-por-qué-tres-templates)
- [El cimiento: `template-jarvis-base`](#el-cimiento-template-jarvis-base)
- [Templates de stack del atelier](#templates-de-stack-del-atelier)
- [Polyrepo vs monorepo: cuándo aplica la excepción](#polyrepo-vs-monorepo-cuándo-aplica-la-excepción)
- [Stacks sin template (Flask, Go, Rust, etc.)](#stacks-sin-template)
- [Cómo extender la base](#cómo-extender-la-base)
- [Cómo proponer un nuevo template](#cómo-proponer-un-nuevo-template)
- [FAQ](#faq)

---

## Filosofía: por qué tres templates

El atelier tiene **tres** starter templates oficiales, no uno y no diez. La razón es práctica:

### Uno solo (descartado)

Un template fullstack único que traiga front + back funcionaría para 60% de los proyectos, pero **rompe en los otros 40%**: proyectos que son solo backend (CLIs, integraciones B2B), solo frontend (paneles administrativos sobre APIs ajenas), o que usan stacks fuera del default (Python, Go, sitios estáticos). Para esos, un template monolítico es peor que arrancar de cero porque tenés que **borrar cosas**.

### Diez (también descartado)

Un template por cada combinación posible (`template-react-vite`, `template-react-nextjs`, `template-vue-nuxt`, `template-nestjs-typeorm`, `template-nestjs-prisma`, `template-flask`, `template-fastapi`, `template-django`, etc.) **se ensucia rápido**:

- Cada uno requiere mantenimiento (deps, lint configs, CI). Más templates = más superficie que actualizar.
- Muchos quedan obsoletos sin uso real. La regla #489 fue clara: *"no creamos templates anticipadamente"*. El primer proyecto de un stack se hace a mano; el segundo lo justifica.
- Decisiones por inundación: si hay 10 templates parecidos, el dev nuevo no sabe cuál elegir.

### Tres (la decisión)

```
template-jarvis-base       ← cimiento universal stack-agnóstico
        ↓ extiende
        ├── template-jarvis-web       ← React + TS + Vite
        └── template-jarvis-api       ← NestJS + Prisma + Postgres
```

- **Una base universal**: lo que TODO repo del atelier necesita, regardless del lenguaje.
- **Dos opinionados**: los stacks default del estudio según el [profile](https://github.com/jarvis-atelier/.github/blob/main/profile/README.md) (React+TS+Vite, NestJS+Prisma+Postgres). Esos son los que más se repiten — templatearlos baja fricción real.
- **Lo demás se hace a mano**: si arrancás un proyecto Python, Go, Rust o un sitio estático, partís de `template-jarvis-base` y armás el resto. Cuando aparezca el segundo proyecto del mismo stack, lo extraemos.

Esto **modela la convención polyrepo de la decisión #489**: los templates de stack están separados (`-web` / `-api`), no fusionados en un fullstack monorepo.

---

## El cimiento: `template-jarvis-base`

Stack-agnóstico. Sirve para CUALQUIER proyecto del atelier. Los otros dos templates lo extienden.

### Qué archivos incluye y por qué

| Archivo | Para qué |
|---|---|
| [`README.md`](#) | Skeleton con secciones obligatorias (Stack, Quickstart, Tests, Deploy, Owners, Estado, Convenciones, Licencia). Trae un banner inicial con instrucciones de "reemplazá este contenido". |
| [`LICENSE`](#) | MIT por default (template del estudio, libre forkeo entre teams). Para proyectos de cliente reemplazá por la licencia que el cliente requiera. |
| [`.gitignore`](#) | Universal: OS junk (`.DS_Store`, `Thumbs.db`), editores (`.vscode/*` con excepciones para shared settings, `.idea/`), env files (`.env*`), logs, build outputs (`dist/`, `build/`, `out/`), caches (`.cache/`, `.next/`, `.vite/`), cobertura (`coverage/`). Más Node.js (`node_modules/`) y Python (`__pycache__/`, `.venv/`) uncommitted porque son los stacks principales del atelier. Java/Go/Rust/Docker/Terraform vienen comentados — descomentás según tu stack. |
| [`.gitattributes`](#) | Fuerza LF en archivos de texto, CRLF en `.bat`/`.cmd`/`.ps1`, marca binarios (imágenes, fuentes, media). **Crítico en Windows mixto** — sin esto, los shell scripts se rompen al cruzar plataformas. |
| [`.editorconfig`](#) | UTF-8 + LF + 2 spaces default. Override a 4 spaces para Python (PEP 8), tabs para Go y Makefiles, sin trim de trailing whitespace en Markdown (porque MD lo usa para line breaks). |
| [`SECURITY.md`](#) | One-liner que apunta al `SECURITY.md` org-level (no duplicar política). Incluye recordatorio de no abrir issues públicos para vulnerabilidades. |
| [`CONTRIBUTING.md`](#) | One-liner que apunta al `CONTRIBUTING.md` org-level. Para setup específico del repo, redirige al README. |
| [`.github/CODEOWNERS`](#) | Template con `@jarvis-atelier/leads` por default. Comentarios con ejemplos de scoping por carpeta (`/apps/web/`, `/apps/api/`, etc.). |
| [`.github/dependabot.yml`](#) | `github-actions` activo siempre (toda repo con workflows lo necesita). `npm`, `pip`, `docker`, `gomod` comentados — descomentás según tu stack. |
| [`.github/workflows/ci.yml`](#) | Placeholder con `name: CI`, triggers en PR y push a `main`, `runs-on: ubuntu-latest`, timeout 15min, y un step "Placeholder" con `echo "configurar tu stack"`. **El header del archivo trae ejemplos completos** de Node, Python y static site para copy-paste. |

### Qué NO incluye (a propósito)

- **package.json / requirements.txt / Cargo.toml / go.mod**: stack-specific.
- **ESLint / Prettier / ruff / black configs**: stack-specific.
- **Dockerfile**: depende de qué deployás.
- **`.env.example`**: depende de qué variables usás.
- **CI con steps reales**: depende del stack.

Esos los meten los templates de stack (`web`, `api`) o vos a mano si tu stack no tiene template.

### Cuándo el dev cambia algo de la base directamente

`template-jarvis-base` está versionado como cualquier repo. Si hacés "Use this template" hoy, te llevás un snapshot — cambios futuros a la base **no se propagan automáticamente** a los repos derivados. Es la naturaleza de los templates de GitHub.

Esto significa:
- Si el atelier cambia una convención (ej: línea de imports, nueva regla de `.gitignore`), **hay que actualizar la base** y opcionalmente comunicar a los owners de repos vivos para que sincen.
- Si tu repo se desfasa mucho de la base, no hay drama — tu repo evoluciona con su realidad.

---

## Templates de stack del atelier

### `template-jarvis-web`

Default web/frontend del atelier. Extiende de `base` y agrega el stack:

**Stack:**
- React 18 + TypeScript strict
- Vite 5 (bundler + dev server)
- Vitest 2 + Testing Library + jsdom (unit tests con coverage threshold 80%)
- ESLint 9 (flat config con `react-hooks` + `react-refresh` + `typescript-eslint`)
- Prettier 3
- pnpm 9 + Node 20+

**Archivos extras sobre la base:**

| Archivo | Para qué |
|---|---|
| `package.json` | deps + scripts (`dev`, `build`, `preview`, `lint`, `format`, `test`, `test:coverage`, `typecheck`) |
| `tsconfig.json` | references a `tsconfig.app.json` y `tsconfig.node.json` |
| `tsconfig.app.json` | TS strict para el código de `src/`, JSX react-jsx, alias `@/*`, types de Vitest + jest-dom |
| `tsconfig.node.json` | TS para `vite.config.ts` (Node-side) |
| `vite.config.ts` | plugin React + alias `@/*` + Vitest config con coverage 80% threshold |
| `eslint.config.js` | flat config moderna con react-hooks y react-refresh |
| `.prettierrc` + `.prettierignore` | formateo |
| `index.html` | entry HTML con `<div id="root">`, `lang="es"` |
| `src/main.tsx` | bootstrap con `createRoot`, valida `#root` o tira |
| `src/App.tsx` | componente placeholder con link al doc |
| `src/App.test.tsx` | dos smoke tests (heading + link) |
| `src/index.css` | estilos globales con dark/light scheme |
| `src/vite-env.d.ts` | tipos de Vite |
| `src/test/setup.ts` | `import '@testing-library/jest-dom/vitest'` |
| `.env.example` | placeholder con comentario sobre `VITE_*` prefix |

**Customizaciones sobre los archivos heredados:**
- `.github/workflows/ci.yml` → steps reales: install + lint + format:check + typecheck + test:coverage + build
- `.github/dependabot.yml` → `npm` activo con grouping de minor/patch para no inundar PRs
- `README.md` → Quickstart específico con comandos de pnpm + estructura

**Cuándo elegirlo:**
- Proyecto frontend puro (sin backend propio).
- Mitad frontend de un proyecto polyrepo (sufijo `-web`).
- SPA contra una API ajena (panel administrativo de un servicio externo).

**Cuándo NO:**
- Necesitás SSR/SSG → considerá Next.js o Astro a mano. Cuando aparezca el segundo proyecto así, templateamos.
- Stack distinto (Vue, Svelte, vanilla JS, Solid) → base + manual.

### `template-jarvis-api`

Default backend del atelier. Extiende de `base` y agrega el stack completo:

**Stack:**
- NestJS 10 (arquitectura por módulos, decoradores, ValidationPipe global)
- Prisma 5 (PrismaService como `@Global()` provider con `OnModuleInit`/`OnModuleDestroy`)
- Postgres 16 (Docker compose para dev local)
- Jest + Supertest (unit + e2e tests con coverage threshold 80%)
- ESLint 9 + Prettier 3 + tsconfig-paths
- pnpm 9 + Node 20+
- Helmet (security headers) + CORS configurable por env

**Archivos extras sobre la base:**

| Archivo | Para qué |
|---|---|
| `package.json` | deps + scripts (`dev`, `build`, `start`, `start:prod`, `test`, `test:e2e`, `test:cov`, `lint`, `typecheck`, `db:generate`, `db:migrate`, `db:migrate:deploy`, `db:seed`, `db:studio`, `db:reset`, `format`) |
| `tsconfig.json` | TS strict + `emitDecoratorMetadata` + `experimentalDecorators` (Nest los necesita) + alias `@/*` |
| `tsconfig.build.json` | excluye specs y test/ del build |
| `nest-cli.json` | config del `nest` CLI |
| `eslint.config.js` | flat config con typescript-eslint + prettier |
| `.prettierrc` + `.prettierignore` | formateo (excluye `prisma/migrations/`) |
| `src/main.ts` | bootstrap: helmet + CORS configurable + ValidationPipe global con whitelist + transform |
| `src/app.module.ts` | root module con `ConfigModule.forRoot({ isGlobal: true, envFilePath: ['.env.local', '.env'] })` y `PrismaModule` |
| `src/app.controller.ts` + `.spec.ts` + `.service.ts` | endpoint `/health` con 3 tests (status, uptime, timestamp) |
| `src/common/prisma/prisma.module.ts` + `prisma.service.ts` | wrapper `@Global()` de PrismaClient |
| `prisma/schema.prisma` | datasource Postgres + generator client + ejemplo comentado de model |
| `prisma/seed.ts` | placeholder con comentario de upsert example |
| `test/jest-e2e.json` | config jest para e2e |
| `test/app.e2e-spec.ts` | e2e test del `/health` con Supertest contra app real |
| `docker-compose.yml` | Postgres 16-alpine en `:5432`, volume persistente, healthcheck |
| `.env.example` | `DATABASE_URL`, `PORT`, `NODE_ENV`, `CORS_ORIGIN` |

**Customizaciones sobre los archivos heredados:**
- `.github/workflows/ci.yml` → con `services.postgres` (16-alpine), env `DATABASE_URL` apuntando al service, steps: install + db:generate + lint + format:check + typecheck + db:migrate:deploy + test:cov + test:e2e + build. Timeout 20 min.
- `.github/dependabot.yml` → `npm` + `docker` activos, npm con grouping
- `README.md` → Quickstart con docker compose + Prisma flow + comandos completos

**Cuándo elegirlo:**
- Backend HTTP (REST o GraphQL) en Node.js con DB relacional.
- Mitad backend de un proyecto polyrepo (sufijo `-api`).
- Servicio nuevo del atelier que va a vivir mucho tiempo.

**Cuándo NO:**
- Workers / jobs en background sin HTTP → considerá un setup más liviano (BullMQ standalone, plain Node).
- Microservicio que no necesita ORM (cache, gateway, proxy) → base + manual.
- Stack distinto (Flask, FastAPI, Go, Rust, Java) → base + manual.

---

## Polyrepo vs monorepo: cuándo aplica la excepción

El default del atelier es **polyrepo SIEMPRE** ([decisión #489](https://github.com/jarvis-atelier/.github/blob/main/projects/README.md#convención-de-nombres-de-repos)). Pero hay una excepción válida: **frontend + backend del MISMO producto en monorepo con `apps/web/` + `apps/api/`**.

### Cuándo va polyrepo (default)

- **Trabajo de cliente.** Permisos granulares (Cliente A no debería ver código de Cliente B), ciclos de release independientes, CI rápido por repo.
- **Producto chico que crece.** Empezar polyrepo desde día 1 evita el dolor de splitear después.
- **Stacks distintos en front y back.** Si front es TS y back es Python, no hay valor en compartir lockfile o CI.
- **Más de un dev en cada lado.** PRs no se cruzan, code review se mantiene focalizado.

→ Crear DOS repos: `client-acme-portal-web` con `template-jarvis-web` + `client-acme-portal-api` con `template-jarvis-api`.

### Cuándo va monorepo (excepción)

- **Producto propio chiquito en MVP.** Solo vos o dos devs, deploy unificado, las dos partes evolucionan juntas y duele coordinar repos separados.
- **Tipos compartidos altamente acoplados.** Si front y back usan los mismos tipos generados (ej: schema OpenAPI, GraphQL codegen) y se rompen seguido, monorepo simplifica.
- **Deploy de un solo binario.** Si embebés el front compilado en el backend y deployás un solo container, no tiene sentido tener dos repos.

→ Crear UN repo: `product-foo` con `template-jarvis-base` y manualmente armar `apps/web/` + `apps/api/` adentro.

### Mini-receta para armar monorepo desde la base

```sh
# 1. Use this template → template-jarvis-base
# 2. Cloná tu nuevo repo
git clone https://github.com/jarvis-atelier/product-foo.git
cd product-foo

# 3. Creá la estructura
mkdir -p apps/web apps/api packages/shared

# 4. Inicializá el manager (pnpm workspaces es lo más usado en el atelier)
echo 'packages:\n  - "apps/*"\n  - "packages/*"' > pnpm-workspace.yaml
pnpm init  # genera el package.json raíz

# 5. Adentro de apps/web/ y apps/api/ podés:
#    - pnpm create vite (front)
#    - nest new . (back)
# y mover archivos para que cada app tenga su propio package.json + tsconfig.

# 6. Editar .github/workflows/ci.yml para correr ambos:
#    - pnpm --filter web lint && pnpm --filter web test
#    - pnpm --filter api lint && pnpm --filter api test
```

### Cuándo NO ir a monorepo aunque "parezca tentador"

- **Proyectos de clientes distintos.** Nunca. Confidencialidad por cliente lo prohíbe.
- **Cuando el "monorepo" es solo escaping del costo de aprender CI multi-repo.** El costo está bien invertido.
- **Cuando los stacks son radicalmente distintos** (TS + Python, JVM + Node). Las herramientas de monorepo (Turborepo, Nx, pnpm workspaces) están pensadas para JS/TS — fuera de eso, ganás complejidad sin valor.

---

## Stacks sin template

Para stacks que no tienen template (Python Flask, FastAPI, Go, Rust, sitios estáticos sin SSG, etc.):

1. **Use this template** → `template-jarvis-base`.
2. Cloneás el repo.
3. Adentro, corrés el comando del stack:
   - Python: `python -m venv .venv && source .venv/bin/activate && pip install flask`
   - Go: `go mod init github.com/jarvis-atelier/<repo>`
   - Rust: `cargo init`
   - Static: tirar `index.html`, `styles.css`, `script.js`.
4. Reemplazás el README con tu Quickstart real.
5. Editás `.github/workflows/ci.yml` con los comandos de lint/test/build de tu stack (ya hay ejemplos comentados al tope del archivo).
6. Descomentás en `.github/dependabot.yml` el ecosistema de tu stack.
7. Descomentás en `.gitignore` las patterns stack-specific si no estaban (ej: `target/` para Rust).

**Política de extracción**: cuando aparezca el SEGUNDO proyecto del mismo stack en el atelier, abrir un issue en `jarvis-atelier/.github` proponiendo extraer un template específico. No antes.

---

## Cómo extender la base

Si necesitás agregar archivos comunes a TODOS los repos del atelier (no a un stack específico), el flujo es:

1. **Abrir issue** en el repo `jarvis-atelier/template-jarvis-base` proponiendo el cambio. Justificar por qué es UNIVERSAL (no stack-specific).
2. **Esperar discusión** con leads — algunos cambios "universales" son en realidad stack-specific disfrazados.
3. **Si se aprueba**: PR contra `template-jarvis-base` con el archivo nuevo + actualización de esta guía mencionando el archivo.
4. **Comunicar a owners** de repos vivos: tienen que decidir si syncan el cambio a su repo manualmente. Los repos nuevos lo van a heredar automáticamente al usar el template.

**Ejemplos de cambios universales legítimos:**
- Nueva versión del `.editorconfig` con un override que falta.
- Patch a `.gitignore` por una herramienta nueva (ej: ignorar `.cursor/`, `.windsurf/`).
- Update a `dependabot.yml` con un nuevo ecosistema disponible en GitHub.

**Ejemplos de cambios que NO son universales** (van al template de stack o al repo específico):
- Nuevo lint rule de ESLint → va a `template-jarvis-web` o al repo donde aplique.
- Nuevo step de CI específico de Node → va a `template-jarvis-web` / `-api`.
- Configuración de Prisma → va a `template-jarvis-api`.

---

## Cómo proponer un nuevo template

Si creés que un nuevo stack merece template (porque ya hay 2+ proyectos en producción usándolo):

1. **Issue en `jarvis-atelier/.github`** con label `template-proposal`. Explicar:
   - Qué stack es.
   - Cuántos proyectos del atelier ya lo usan (linkear a los repos).
   - Qué traería el template (lista de archivos + configs).
2. **Discusión con leads** — pueden pedir que esperes un tercer proyecto, o aprobar el draft.
3. **Si aprueban**: crear el repo `template-jarvis-<stack>` extendiendo de `template-jarvis-base`, marcarlo como Template repository, agregar topics, y abrir PR contra `.github/projects/README.md` sumando la fila a la tabla de Templates disponibles + entrada en este doc.

---

## FAQ

### ¿Puedo modificar `template-jarvis-base` después de "Use this template" en mi repo?

Sí, totalmente. Lo que clonás es un snapshot — es tuyo, hacele lo que quieras. La base solo te da el punto de partida.

### ¿Y si la base cambia después de que yo cloné?

Tu repo no se entera. Es el comportamiento natural de los templates de GitHub. Si querés syncar un cambio de la base, lo hacés a mano (cherry-pick o copy-paste).

### ¿Por qué el `.gitignore` trae cosas de Node y Python uncommented si la base es stack-agnóstica?

Porque el atelier **tiene 95% de proyectos en Node o Python** según el [profile](https://github.com/jarvis-atelier/.github/blob/main/profile/README.md). Si tu proyecto es de otro stack, los patterns no te molestan (no hay archivos para ignorar) y vos descomentás el bloque que necesites para tu stack.

### ¿La base trae `.husky/` o pre-commit hooks?

No. Pre-commit hooks dependen del stack y de la opinión del owner. Los metés en el template de stack (`web`, `api`) o en tu repo. La base se queda neutra.

### ¿Por qué no incluir un `Dockerfile` en la base?

Porque el contenido de un Dockerfile depende 100% del stack y del deploy target. Un Dockerfile genérico es peor que ninguno — engaña al lector.

### ¿La base trae GitHub Actions para deploy a Railway / Render / Vercel?

No. El deploy depende del proveedor y de tu pipeline. Cada repo lo configura adentro de `.github/workflows/`. La base solo trae `ci.yml` placeholder para lint+test.

### ¿Puedo cambiar la licencia de un repo derivado?

Sí. La base trae MIT por default (porque el template ES MIT), pero tu repo es independiente — reemplazá `LICENSE` por la que corresponda al proyecto (proprietary para clientes, etc.).

### ¿Y si quiero arrancar un proyecto sin template en GitHub, directo en mi máquina?

Cloneás `template-jarvis-base` a un directorio, borrás el `.git`, hacés `git init` adentro y empujás a un repo nuevo en GitHub. Es lo mismo que "Use this template" pero a mano.
