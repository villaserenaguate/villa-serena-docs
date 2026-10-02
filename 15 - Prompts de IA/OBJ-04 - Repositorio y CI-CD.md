# OBJ-04 — Configurar el repositorio y el CI/CD

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P3 — Plataforma y calidad | A: ninguno · B: OBJ-03 | `feat/obj-04-monorepo`, `feat/obj-04-cicd`, `feat/obj-04-tablero` | ~15 h |

## Antes de empezar

- [ ] Acceso de administrador a la organización `villaserenagt` en GitHub.
- [ ] Copiar la carpeta **Documentación Definitiva** completa y los archivos `AGENTS.md` y `CLAUDE.md` de esta carpeta a una ubicación a mano, para que el prompt A los agregue al repo.
- [ ] **Si Kim ya tiene el frontend en otro lugar**, esperar a tenerlo antes del prompt A, para migrarlo al monorepo en vez del scaffold vacío.

---

## Prompt 04-A — Monorepo, documentación y contexto (día 1)

````text
Tu tarea es el OBJ-04, parte A: convertir este repositorio en el monorepo del PMS Villa Serena. Si existe AGENTS.md en la raíz, léelo; si no, lo vas a crear en el paso 3.

Situación actual: el repositorio es un scaffold de Next.js 15 en la raíz (package.json, src/, etc.) donde la mayoría de archivos están vacíos y la estructura src/app/router, layouts y features/*/pages viene de un proyecto Vite + React Router que Next.js no usa.

Pasos:

1. Crea la estructura de monorepo con pnpm workspaces:
   - apps/web: mueve aquí el proyecto Next.js actual (conserva package.json, dependencias, tsconfig, next.config.ts, postcss, src/app/layout.tsx, src/app/globals.css, src/app/providers y src/lib/utils.ts).
   - Elimina los archivos vacíos del scaffold (0 bytes) y las carpetas que Next.js no usa (src/App.tsx, src/app/router, src/app/layouts, features/*/pages vacías). Enumera en el resumen lo que eliminaste.
   - apps/mobile: por ahora solo un README que diga "Proyecto Expo — se crea en OBJ-14".
   - packages/shared: paquete TypeScript sin dependencias de React, con src/index.ts, src/estados.ts (vacío por ahora) y script de build o exportación directa de TS.
   - supabase/: ejecuta supabase init si no existe.
   - tests/ y docs/.
   - pnpm-workspace.yaml, package.json raíz con scripts: dev, build, lint, typecheck, test, gen:types (supabase gen types typescript --local > packages/shared/src/database.types.ts).
   - .npmrc con node-linker=hoisted (compatibilidad con Expo/Metro).
   - ESLint y Prettier compartidos en la raíz.

2. Copia la documentación definitiva a docs/ (te indicaré la ruta de origen) conservando los nombres de archivo, incluida la subcarpeta "04 - Historias de Usuario".

3. Coloca AGENTS.md y CLAUDE.md en la raíz (te indicaré la ruta de origen).

4. Crea .github/pull_request_template.md con: IDs de HU, descripción, cómo probar, capturas si hay interfaz, y la checklist de "Definición de Hecho" del documento docs/03 - Plantilla de Historias de Usuario.md (sección 5).

5. README.md raíz: qué es el proyecto, estructura, requisitos (Node LTS, pnpm, Docker, Supabase CLI) y comandos principales.

6. Verifica: pnpm install, pnpm --filter web dev arranca, pnpm lint y pnpm typecheck pasan.

Criterios de terminado:
- Monorepo funcional; la web arranca desde apps/web.
- docs/, AGENTS.md y CLAUDE.md presentes.
- Plantilla de pull request creada.
````

---

## Prompt 04-B — Pipelines de CI/CD

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-04, parte B: crear los pipelines de GitHub Actions.

Lee: docs/14 - Tecnologias y Arquitectura.md (secciones 7, 8 y 10) y docs/infraestructura.md.

1. .github/workflows/ci.yml (en cada pull request a develop y main):
   pnpm install con caché → lint → typecheck (web, mobile si existe, shared) → pruebas Vitest → supabase start (Docker) → supabase db reset → supabase test db (pgTAP) → build de apps/web → Playwright de flujos críticos (si existen pruebas; si no, déjalo preparado y omitido sin fallar).

2. .github/workflows/deploy-dev.yml (push a develop): repetir verificaciones → supabase db push al proyecto de desarrollo (SUPABASE_ACCESS_TOKEN, SUPABASE_DB_PASSWORD y project ref en GitHub Secrets) → despliegue con Vercel CLI (vercel pull, vercel build, vercel deploy --prebuilt) al ambiente de desarrollo. Recuerda: el plan Hobby de Vercel no conecta repositorios de organizaciones, por eso se despliega desde Actions con VERCEL_TOKEN, VERCEL_ORG_ID y VERCEL_PROJECT_ID.

3. .github/workflows/deploy-prod.yml (push a main): lo mismo contra producción (--prod).

4. .github/workflows/apk.yml (push a main o ejecución manual): si existe apps/mobile, genera el APK con EAS Build (perfil preview, buildType apk) usando EXPO_TOKEN, y adjunta el enlace o archivo como release. Si apps/mobile aún no existe, el workflow termina sin error.

5. .github/workflows/programados.yml (diario): ping a Supabase de producción y de desarrollo para evitar la pausa por inactividad (RNF-DAT-003). El backup se agrega en OBJ-18.

6. Documenta en docs/cicd.md: qué hace cada workflow, la lista de GitHub Secrets necesarios y cómo configurar la protección de ramas (main y develop: pull request obligatorio, 1 aprobación, checks de ci.yml obligatorios, sin push directo).

Criterios de terminado:
- Un pull request de prueba ejecuta ci.yml completo.
- Un merge a develop despliega en dev.<dominio>.
- Un pull request con una prueba fallida no se puede fusionar (después de configurar la protección de ramas).
````

---

## Prompt 04-C — Tablero con las 96 historias

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-04, parte C: cargar las 96 historias de usuario como issues de GitHub en un tablero de GitHub Projects.

1. Escribe un script en TypeScript (scripts/crear-issues.ts, ejecutable con tsx) que:
   - Lea los archivos de docs/04 - Historias de Usuario/HU - *.md.
   - Extraiga de cada historia: ID, título, plataforma, épica, alcance, prioridad, tamaño, la historia (Como/Quiero/Para), criterios de aceptación, reglas relacionadas y dependencias.
   - Cree un issue por historia con título "HU-XXX-00 — Título" y el contenido en el cuerpo, usando la CLI gh.
   - Asigne etiquetas: módulo (recepcion, room-service, piso, admin, huesped, channel-manager), prioridad (prioridad-alta/media/baja), plataforma (web-publica, web-privada, app, api), tamaño (S/M/L) y el objetivo (obj-08, obj-09…) según docs/13 - Objetivos del Proyecto.md.
   - Tenga modo --dry-run que solo muestra lo que haría.
   - No duplique issues si se ejecuta dos veces (buscar por ID en el título).

2. Documenta en docs/cicd.md cómo ejecutar el script y cómo crear el tablero de GitHub Projects con columnas: Pendiente, En desarrollo, En revisión, Hecha.

3. Ejecuta primero en --dry-run y muéstrame el resultado antes de crear los issues reales.

Criterios de terminado:
- 96 issues creados con etiquetas correctas y sin duplicados.
- Tablero creado con las 4 columnas.
````

## Revisión humana

- [ ] Confirmar la protección de ramas en la configuración del repositorio.
- [ ] Revisar que los GitHub Secrets están configurados y que ningún workflow imprime secretos en los logs.
