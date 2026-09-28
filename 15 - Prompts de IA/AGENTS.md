# AGENTS.md — PMS Villa Serena

> Este archivo va en la **raíz del repositorio**. Lo leen automáticamente Codex y Antigravity; Claude Code lo lee mediante `CLAUDE.md`.
> Es el contexto permanente del proyecto: **léelo completo antes de cualquier tarea.**

---

## 1. Qué es el proyecto

Sistema de Administración de Propiedades (PMS) para **Villa Serena**, un hotel boutique **ficticio** (proyecto universitario). Tiene tres partes:

1. **Web pública** (motor de reservas): el cliente busca, reserva y paga sin crear cuenta.
2. **Panel privado** (web): Recepción, Room Service, Mantenimiento/Limpieza y Administración.
3. **App Android** para el huésped: estadía, room service, solicitudes, amenidades, cuenta, pago y check-out.

Un solo hotel · moneda **quetzales (GTQ)** · idioma **español** · zona horaria **America/Guatemala (UTC-6)** · pagos solo en **Stripe sandbox** (nunca dinero real).

---

## 2. Documentación (fuente de verdad)

Toda la documentación está en `docs/`. **Antes de programar, lee los documentos que indique la tarea.** Si el código y la documentación no coinciden, la documentación manda; si la documentación es ambigua o contradictoria, **detente y pregunta** en lugar de inventar.

| Documento | Para qué sirve |
|---|---|
| `docs/01 - Alcance del Proyecto.md` | Qué entra en la versión 1 y qué es **Fase 2 (no construir)** |
| `docs/02 - Definicion de Roles.md` | Los 5 roles, sus códigos y responsabilidades |
| `docs/04 - Historias de Usuario/` | Las 96 historias (HU) con criterios de aceptación |
| `docs/07 - Estados.md` | **Códigos exactos** de estado, transiciones permitidas y efectos automáticos |
| `docs/08 - Inventario Turnos y Personal.md` | Inventario, turnos y reglas de personal |
| `docs/09 - Matriz de Permisos.md` | Qué puede hacer cada rol (base de las políticas RLS) |
| `docs/10 - Reglas de Negocio.md` | 142 reglas (RN-…) y 21 parámetros (PAR-…) |
| `docs/11 - Requisitos Funcionales.md` | Requisitos por módulo |
| `docs/12 - Casos de Uso.md` | Flujos completos con casos alternativos |
| `docs/13 - Objetivos del Proyecto.md` | Los 19 objetivos de trabajo (OBJ-01 a OBJ-19) |
| `docs/14 - Tecnologias y Arquitectura.md` | Stack, estructura, ambientes y requisitos no funcionales |

**Prioridad si hay conflicto:** 10 (Reglas) > 07 (Estados) > 09 (Permisos) > 04 (HU) > resto.

---

## 3. Stack (no agregar otros lenguajes ni frameworks)

| Parte | Tecnología |
|---|---|
| Web (`apps/web`) | Next.js 15 (App Router) · React 19 · TypeScript · Tailwind CSS 4 · shadcn/ui · TanStack Query y Table · React Hook Form + Zod · date-fns + `@date-fns/tz` · `@supabase/ssr` |
| App (`apps/mobile`) | React Native + **Expo SDK 54** · Expo Router · NativeWind · `@supabase/supabase-js` + `expo-secure-store` · TanStack Query · React Hook Form + Zod · `expo-web-browser` + `expo-linking` |
| Compartido (`packages/shared`) | Tipos generados de Supabase · esquemas Zod · códigos y etiquetas de estado · **sin dependencias de React** |
| Backend | **Supabase**: PostgreSQL · RLS · Auth (contraseña para personal, OTP por correo para huésped) · Realtime · Storage · pg_cron · funciones SQL (RPC) y triggers |
| API con secretos | Route Handlers de Next.js en `apps/web/src/app/api/` (Stripe, correos, PDF, API del canal, creación de empleados) |
| Servicios | Stripe (sandbox) · Resend + React Email · `@react-pdf/renderer` · Sentry · Vercel |
| Calidad | Vitest · Playwright · pgTAP (`supabase test db`) · ESLint + Prettier |
| Monorepo | pnpm workspaces |

Lenguajes permitidos: **TypeScript** y **SQL**. Nada más.

---

## 4. Estructura del repositorio

```
apps/web/          Next.js: (publico), (panel)/panel/{recepcion,room-service,piso,admin}, api/, login/
apps/mobile/       Expo: app/ (Expo Router), components/, lib/, eas.json
packages/shared/   Tipos, Zod, estados
supabase/          migrations/, tests/ (pgTAP), seed.sql
tests/             Vitest y Playwright
docs/              Documentación definitiva, OpenAPI, diagramas
.github/workflows/ CI/CD
```

---

## 5. Reglas que nunca se rompen

1. **Códigos de estado exactos** del documento 07 (`PENDIENTE_PAGO`, `EN_ESTADIA`, `FUERA_DE_SERVICIO`…) en base de datos, backend, web, app y pruebas. En pantalla se muestran con las **etiquetas** del documento 07.
2. **La base de datos decide.** Las reglas críticas van en restricciones, triggers o funciones SQL (RPC). La interfaz también valida, pero solo para dar buena experiencia de usuario.
3. **RLS activado en todas las tablas.** Ninguna tabla queda sin políticas. Los permisos siguen el documento 09.
4. **Transiciones de estado:** solo las del documento 07; cualquier otra se rechaza. Todo cambio de estado se registra en el **historial de estados** con actor, fecha, hora y motivo.
5. **No se borra información de negocio:** los empleados se desactivan, los cargos se anulan con motivo, los catálogos se desactivan.
6. **Secretos:** nunca en Git. La clave `service_role`, Stripe y Resend solo en variables de entorno del servidor. Nunca en la web del navegador ni en la app.
7. **Dinero:** columnas `numeric(10,2)` en quetzales. Nada de `float`.
8. **Fechas:** se guardan en UTC (`timestamptz`); las fechas de estadía son `date`. Se muestran en America/Guatemala.
9. **Migraciones:** todo cambio de base de datos es una migración nueva en `supabase/migrations/`. **Nunca edites una migración ya fusionada en `develop`.** Después de cambiar el esquema, regenera los tipos en `packages/shared`.
10. **Fase 2 no se construye:** domótica, llaves digitales, firma digital, chat, notificaciones push, FEL, fidelidad, spa con aforo, conexión real con Booking/Expedia, arrastrar en el Gantt, tarifas por ocupación, RevPAR/ADR, activos y mantenimiento preventivo, reservar desde la app.
11. **App (Expo Go):** solo módulos incluidos en Expo SDK 54. No agregar código nativo propio.
12. **No agregues dependencias** fuera del stack sin justificarlo en el pull request.

---

## 6. Convenciones

- **Idioma del dominio: español.** Tablas y columnas en `snake_case` y plural (`reservas`, `tipos_habitacion`, `fecha_entrada`); funciones SQL en verbo (`crear_reserva`); componentes y funciones TS en español (`FormularioReserva`, `crearReserva`). Los términos técnicos de React pueden quedar en inglés (`useQuery`, `props`).
- **Textos de interfaz:** en español, montos como `Q 1,250.00`.
- **Ramas:** `feat/HU-REC-05-crear-reserva`, `feat/obj-01-base-datos`, `fix/…`.
- **Commits:** Conventional Commits en español: `feat(recepcion): crear reserva desde el Gantt (HU-REC-12)`.
- **Pull requests:** uno por historia o grupo pequeño de historias; incluye los IDs de HU y la checklist de "Definición de Hecho" (documento 03).
- **Pruebas:** toda regla de negocio nueva lleva prueba (pgTAP para SQL, Vitest para TypeScript). Los flujos críticos llevan Playwright.

---

## 7. Comandos (disponibles después de OBJ-04)

```bash
pnpm install                     # instalar todo
pnpm --filter web dev            # web en http://localhost:3000
pnpm --filter mobile start       # app con Expo Go (escanear QR)
supabase start                   # Supabase local (Docker)
supabase db reset                # recrear la BD local con migraciones y seed
supabase test db                 # pruebas pgTAP
pnpm gen:types                   # regenerar tipos de Supabase en packages/shared
pnpm lint && pnpm typecheck && pnpm test
```

---

## 8. Protocolo de trabajo del agente

1. **Lee** `AGENTS.md`, los documentos indicados en la tarea y las HU involucradas.
2. **Presenta un plan** breve (archivos a crear o modificar, migraciones, pruebas) y espera confirmación si la tarea es grande.
3. **Trabaja en pasos pequeños**, con commits frecuentes en la rama indicada.
4. **Si algo es ambiguo o contradictorio en la documentación, pregunta.** No inventes reglas.
5. **Antes de terminar:** ejecuta lint, typecheck y pruebas; corrige lo que falle.
6. **Resumen final:** qué hiciste, qué HU/criterios quedaron cubiertos, cómo probarlo manualmente, qué quedó pendiente y si agregaste migraciones o dependencias.
7. **No hagas merge** ni push a `main` o `develop`: eso se hace por pull request.
