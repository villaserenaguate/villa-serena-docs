# 14 — Tecnologías y Arquitectura

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 25 de septiembre de 2026
> **Documento anterior:** 13 — Objetivos del Proyecto
> **Reemplaza a:** "Requisitos no funcionales" (documentación inicial)

---

## Índice

1. [Propósito](#1-propósito)
2. [Decisiones de arquitectura](#2-decisiones-de-arquitectura)
3. [Vista general](#3-vista-general)
4. [Tecnologías](#4-tecnologías)
5. [Dónde vive cada regla de negocio](#5-dónde-vive-cada-regla-de-negocio)
6. [Estructura del repositorio y rutas](#6-estructura-del-repositorio-y-rutas)
7. [Ambientes](#7-ambientes)
8. [Pipeline de CI/CD](#8-pipeline-de-cicd)
9. [Requisitos no funcionales](#9-requisitos-no-funcionales)
10. [Limitaciones conocidas y mitigaciones](#10-limitaciones-conocidas-y-mitigaciones)
11. [Cuentas y costos](#11-cuentas-y-costos)
12. [Cambios respecto a los requisitos no funcionales originales](#12-cambios-respecto-a-los-requisitos-no-funcionales-originales)
13. [Pendientes](#13-pendientes)

---

## 1. Propósito

Define **con qué** se construye el PMS Villa Serena y **cómo se conectan** sus partes. Es la referencia técnica para los prompts de IA de cada objetivo (documento 13) y reemplaza los requisitos no funcionales originales, que asumían Spring Boot, EC2 y Cloudflare.

**Restricción del equipo:** todo el código de aplicación se escribe en **TypeScript con Next.js**. No se usan otros lenguajes ni frameworks de aplicación (Flutter, Kotlin, Java, React Native, etc.). La única excepción es **SQL**, el lenguaje propio de la base de datos.

---

## 2. Decisiones de arquitectura

| # | Decisión | Motivo |
|---|---|---|
| AD-01 | **Supabase es el backend completo:** PostgreSQL, autenticación, permisos (RLS), tiempo real, archivos y tareas programadas | Evita programar y mantener un servidor propio con el tiempo disponible |
| AD-02 | **Un solo proyecto Next.js** contiene la web pública, el panel privado del personal y la app del huésped | Un solo lenguaje, un solo despliegue y componentes compartidos |
| AD-03 | **La app Android es una PWA de Next.js empaquetada como APK** con Trusted Web Activity (Bubblewrap) | Cumple "solo TypeScript/Next.js" y genera un APK instalable |
| AD-04 | **Las reglas críticas viven en la base de datos** (restricciones, triggers y funciones SQL). Lo que requiere servicios externos o claves secretas vive en **Route Handlers de Next.js** | La web y la app usan las mismas reglas; los procesos de varios pasos son atómicos |
| AD-05 | **Vercel** aloja la web y se despliega **desde GitHub Actions** con la CLI de Vercel | El plan gratuito de Vercel no conecta repositorios de organizaciones de GitHub |
| AD-06 | **pg_cron** ejecuta los procesos programados | El cron gratuito de Vercel solo corre una vez al día |
| AD-07 | **Stripe en sandbox** para todos los pagos | La empresa es ficticia; no se procesa dinero real |
| AD-08 | **Dominio propio + Resend** para los correos | Envío a cualquier destinatario sin los límites del correo por defecto de Supabase |
| AD-09 | **Repositorio único** (`villaserena-web`) con la app Next.js, las migraciones de Supabase y la configuración del APK | Un solo lugar para el código, las pruebas y el pipeline |

---

## 3. Vista general

```
                        ┌──────────────────────────────────────────────┐
  Cliente / Huésped ───►│            NEXT.JS (en Vercel)               │◄─── Personal del hotel
  (navegador o APK)     │                                              │     (navegador)
                        │  (publico)  Web pública / motor de reservas  │
                        │  (app)      App del huésped (PWA → APK)      │
                        │  (panel)    Recepción · Room Service ·       │
                        │             Piso · Administración            │
                        │  /api       Stripe · Canal · Correo · PDF    │
                        └───────────────┬───────────────┬──────────────┘
                                        │ supabase-js   │ claves secretas
                                        ▼               ▼
                        ┌──────────────────────────────────────────────┐
                        │                 SUPABASE                     │
                        │  PostgreSQL: tablas · restricciones ·        │
                        │    triggers · funciones (RPC) · pg_cron      │
                        │  RLS (permisos por rol) · Auth (contraseña / │
                        │    OTP) · Realtime · Storage                 │
                        └──────────────────────────────────────────────┘
                                        ▲
         Stripe (sandbox) ──webhook──► /api/stripe/webhook
         Canal simulado ───API──────► /api/canal/v1/...
         Resend ◄── correos · Sentry ◄── errores · UptimeRobot ──► alertas de caída
```

---

## 4. Tecnologías

### 4.1 Aplicación (TypeScript)

| Tecnología | Uso | Objetivos |
|---|---|---|
| **Next.js 15** (App Router) + **React 19** + **TypeScript** | Web pública, panel privado, app del huésped y API | OBJ-05, 07, 08–16 |
| **Tailwind CSS 4** | Estilos | Todos los de interfaz |
| **shadcn/ui** + **lucide-react** | Componentes base (botones, tablas, diálogos, formularios) e íconos | OBJ-05 y módulos |
| **React Hook Form** + **Zod** | Formularios y validación, compartida entre cliente y servidor | Módulos |
| **TanStack Query** | Carga y caché de datos | Módulos |
| **TanStack Table** | Tablas con filtros y paginación | Módulos |
| **Componente propio de Gantt** (cuadrícula con Tailwind) | Calendario habitaciones × días | OBJ-09 |
| **Recharts** (vía shadcn charts) | Gráficas de indicadores | OBJ-11 |
| **date-fns** + **@date-fns/tz** | Cálculo de noches y zona horaria America/Guatemala | OBJ-06, módulos |
| **@supabase/supabase-js** + **@supabase/ssr** | Conexión a Supabase y sesión con cookies seguras | OBJ-05 |
| **Serwist** (`@serwist/next`) + `app/manifest.ts` | PWA: instalación, ícono y pantalla completa | OBJ-14 |
| **Stripe** (`stripe` para Node) | Sesiones de pago, webhook y reembolsos | OBJ-07 |
| **Resend** + **React Email** | Envío de correos y plantillas | OBJ-02, 07 |
| **@react-pdf/renderer** | Comprobante de pago en PDF | OBJ-07 |
| **OpenAPI 3** + **Scalar** | Documentación interactiva del API del canal | OBJ-16 |
| **Sentry** (`@sentry/nextjs`) | Registro de errores | OBJ-03 |

### 4.2 Datos (Supabase)

| Tecnología | Uso | Objetivos |
|---|---|---|
| **PostgreSQL** | Todas las tablas, tipos enumerados con los códigos del documento 07 | OBJ-01 |
| **Restricciones** (`EXCLUDE` con `btree_gist`, `CHECK`, `UNIQUE`) | Reglas que nunca deben romperse | OBJ-01 |
| **Triggers** | Validación de transiciones de estado e historial | OBJ-06, 12, 13 |
| **Funciones SQL (RPC)** | Procesos atómicos: reservar, check-in, check-out, cancelar | OBJ-06, 12, 13 |
| **Row Level Security** | Matriz de permisos del documento 09 | OBJ-02 |
| **Supabase Auth** | Contraseña (personal) y OTP por correo (huésped) | OBJ-02 |
| **Supabase Realtime** | Actualizaciones en vivo | OBJ-05 y módulos |
| **Supabase Storage** | Contenedores público, documentos y operación | OBJ-02 |
| **pg_cron** | Vencimiento de pagos y no-show | OBJ-07 |
| **pgcrypto** | Hash de claves de API de los canales | OBJ-16 |
| **Supabase CLI** | Migraciones, datos de prueba, tipos TypeScript generados y pruebas | OBJ-01, 04 |

### 4.3 Plataforma y calidad

| Tecnología | Uso | Objetivos |
|---|---|---|
| **GitHub** (organización `villaserenagt`) + **GitHub Projects** | Código, pull requests y tablero de historias | OBJ-04 |
| **GitHub Actions** | CI/CD: pruebas, migraciones, despliegue, APK y backups | OBJ-04, 18 |
| **Vercel** (plan Hobby) | Hosting de la web con dominio propio | OBJ-03 |
| **Bubblewrap CLI** | Generar el APK (Trusted Web Activity) | OBJ-04, 14 |
| **Vitest** | Pruebas unitarias en TypeScript | OBJ-06, 17 |
| **Playwright** | Pruebas de extremo a extremo | OBJ-17 |
| **pgTAP** (`supabase test db`) | Pruebas de permisos, restricciones y funciones SQL | OBJ-02, 06 |
| **ESLint** + **Prettier** | Estilo de código | OBJ-04 |
| **UptimeRobot** | Alerta de caída | OBJ-03 |
| **pnpm** + **Node.js LTS** | Gestor de paquetes y entorno | Todos |

---

## 5. Dónde vive cada regla de negocio

| Mecanismo | Grupos de reglas (documento 10) |
|---|---|
| **Restricciones de BD** | RN-RES-002 (sin traslapes), RN-INV-002 (stock ≥ 0), RN-HAB-007 y RN-CM-004 (unicidades), RN-PER-006 |
| **Triggers** | RG-EST-01 a 04 (transiciones e historial), RN-RS-001 a 004, RN-LIM-001, RN-MAN-002, RN-INV-001, 003 y 005 |
| **Funciones SQL (RPC)** | RN-RES (reservar, modificar), RN-TAR (precio), RN-PAG (cuenta, saldo), RN-CAN (penalidad), check-in y check-out (documento 07, sección 11), RN-RS-007 (cargo único), RN-INV-007 |
| **RLS** | RN-SEG, R-ROL-06 y 07, matriz del documento 09 |
| **pg_cron** | RN-RES-012 (vencimiento de pago), RN-CAN-004 (no-show) |
| **Route Handlers de Next.js** | RN-PAG-001 a 005 (Stripe y webhook), RN-CAN-008 (reembolsos), RN-CM (API del canal), correos, PDF, creación de cuentas de empleados |
| **Supabase Auth** | RN-APP-001 a 003 (OTP), RN-PER-001 |

**Principio:** la interfaz valida para dar buena experiencia al usuario, pero **la base de datos es la que decide**. Una regla solo está implementada si se cumple aunque alguien intente saltarse la interfaz.

---

## 6. Estructura del repositorio y rutas

### 6.1 Carpetas

```
villaserena-web/
├── src/
│   ├── app/
│   │   ├── (publico)/          Web pública: inicio, habitaciones, reservar, mi-reserva
│   │   ├── (app)/app/          App del huésped (PWA → APK)
│   │   ├── (panel)/panel/      recepcion · room-service · piso · admin
│   │   ├── api/                stripe · pagos · canal/v1 · empleados · comprobantes
│   │   ├── login/              Acceso del personal
│   │   └── manifest.ts         Manifiesto de la PWA
│   ├── components/             ui (shadcn) · compartidos (EstadoBadge, Gantt, etc.)
│   ├── features/               Lógica de interfaz por módulo
│   ├── lib/                    supabase (cliente/servidor) · validaciones (Zod) · fechas · formato
│   └── types/                  Tipos generados desde Supabase
├── supabase/
│   ├── migrations/             Tablas, restricciones, triggers, funciones, RLS, pg_cron
│   ├── tests/                  Pruebas pgTAP
│   └── seed.sql                Datos de prueba
├── twa/                        Configuración de Bubblewrap para el APK
├── tests/                      Vitest y Playwright
├── docs/                       Documentación definitiva, OpenAPI, diagramas
└── .github/workflows/          Pipelines de CI/CD
```

El scaffold actual del repositorio (estructura pensada para Vite + React Router) se reorganiza según esta estructura en OBJ-05.

### 6.2 Rutas principales

| Zona | Rutas | Quién |
|---|---|---|
| Pública | `/`, `/habitaciones`, `/reservar`, `/reservar/resultado`, `/mi-reserva` | Cualquiera / huésped con OTP |
| App | `/app`, `/app/estadia`, `/app/room-service`, `/app/solicitudes`, `/app/amenidades`, `/app/cuenta` | Huésped con OTP |
| Panel | `/panel/recepcion/...`, `/panel/room-service/...`, `/panel/piso/...`, `/panel/admin/...` | Personal según rol |
| API | `/api/pagos/checkout`, `/api/stripe/webhook`, `/api/canal/v1/reservas`, `/api/canal/v1/docs`, `/api/empleados`, `/api/comprobantes/[id]` | Sistema, pasarela, canal y personal autorizado |

---

## 7. Ambientes

| Ambiente | Supabase | Web | Stripe | Se actualiza con |
|---|---|---|---|---|
| **Local** | Supabase CLI en Docker | `pnpm dev` | Sandbox + Stripe CLI | Cada desarrollador |
| **Desarrollo** | Proyecto gratuito `villaserena-dev` | Vercel (preview), subdominio `dev.` | Sandbox | Merge a `develop` |
| **Producción** | Proyecto gratuito `villaserena-prod` | Vercel (producción), dominio principal | Sandbox | Merge a `main` |

---

## 8. Pipeline de CI/CD

| Evento | Pasos |
|---|---|
| **Pull request** | Instalar → ESLint → verificación de tipos → Vitest → levantar Supabase local → aplicar migraciones → pgTAP → build de Next.js → Playwright (flujos críticos) |
| **Merge a `develop`** | Todo lo anterior → `supabase db push` a desarrollo → despliegue con la CLI de Vercel (preview/dev) |
| **Merge a `main`** | Todo lo anterior → `supabase db push` a producción → despliegue a producción → generar APK con Bubblewrap y publicarlo como artefacto o release |
| **Programado (diario)** | Backup con `pg_dump` de producción guardado como artefacto cifrado · ping para evitar la pausa de Supabase |

**Reglas del repositorio:** `main` y `develop` protegidas, pull request obligatorio con 1 aprobación y todas las verificaciones en verde.

---

## 9. Requisitos no funcionales

### Seguridad

| ID | Requisito |
|---|---|
| RNF-SEC-001 | Supabase Auth es el proveedor de identidad: contraseña para el personal y OTP por correo para el huésped. |
| RNF-SEC-002 | La sesión web usa cookies seguras compatibles con SSR (`@supabase/ssr`). |
| RNF-SEC-003 | Los permisos se aplican con RLS en la base de datos según el documento 09. |
| RNF-SEC-004 | Todo el tráfico usa HTTPS (Vercel y Supabase). |
| RNF-SEC-005 | La clave `service_role` de Supabase y las claves de Stripe y Resend solo existen en el servidor (variables de entorno) y nunca en Git. |
| RNF-SEC-006 | El sistema no recibe ni almacena datos de tarjeta; el pago ocurre en Stripe. |
| RNF-SEC-007 | El webhook de Stripe verifica la firma de cada evento. |
| RNF-SEC-008 | El API del canal exige una clave de API, guardada como hash. |
| RNF-SEC-009 | Los documentos de identidad se guardan en almacenamiento privado con URLs firmadas de corta duración. |

### Disponibilidad y datos

| ID | Requisito |
|---|---|
| RNF-DAT-001 | PostgreSQL de Supabase es la única base de datos del sistema. |
| RNF-DAT-002 | Backup diario de producción con restauración probada. Objetivos: RPO ≤ 24 horas, RTO ≤ 2 horas. |
| RNF-DAT-003 | Se evita la pausa por inactividad de Supabase con una tarea programada. |
| RNF-DAT-004 | Las fechas se guardan en UTC y se muestran en America/Guatemala (PAR-21). |

### Rendimiento

| ID | Requisito |
|---|---|
| RNF-REN-001 | Objetivo: hotel pequeño (≈ 12–30 habitaciones), tráfico bajo o medio. |
| RNF-REN-002 | La búsqueda de disponibilidad responde en menos de 2 segundos. |
| RNF-REN-003 | Los cambios en tiempo real se reflejan en menos de 3 segundos. |
| RNF-REN-004 | Índices en las columnas de búsqueda frecuente (fechas, estado, documento, código, canal). |

### Observabilidad

| ID | Requisito |
|---|---|
| RNF-OBS-001 | Los errores de la web, la app y el API se registran en Sentry. |
| RNF-OBS-002 | UptimeRobot alerta por correo si el sitio o el API dejan de responder. |
| RNF-OBS-003 | Todas las peticiones al API del canal quedan registradas (RN-CM-006). |

### Mantenibilidad

| ID | Requisito |
|---|---|
| RNF-MAN-001 | Todo cambio entra por pull request con revisión y pipeline en verde. |
| RNF-MAN-002 | Los cambios de base de datos se hacen solo con migraciones versionadas. |
| RNF-MAN-003 | Los tipos TypeScript se generan desde el esquema de la base de datos. |
| RNF-MAN-004 | Cada despliegue queda identificado por su commit y se puede revertir a uno anterior desde Vercel. |
| RNF-MAN-005 | El API del canal está documentado con OpenAPI. |

### Compatibilidad y usabilidad

| ID | Requisito |
|---|---|
| RNF-USA-001 | La web pública y el panel de Mantenimiento/Limpieza se ven correctamente en computadora y en teléfono (diseño responsive). |
| RNF-USA-002 | La app del huésped se instala como APK en Android con Google Chrome y también como PWA desde el navegador. |
| RNF-USA-003 | Toda la interfaz está en español y los montos en quetzales. |
| RNF-USA-004 | Los estados usan las etiquetas y colores del documento 07. |

---

## 10. Limitaciones conocidas y mitigaciones

| Limitación | Mitigación |
|---|---|
| Stripe no ofrece modo real en Guatemala | No afecta al proyecto: todo funciona en sandbox. Un cobro real necesitaría una pasarela local (Fase 2) |
| Vercel Hobby no conecta repositorios de organizaciones | Despliegue desde GitHub Actions con la CLI de Vercel (AD-05) |
| El cron de Vercel Hobby solo corre una vez al día | pg_cron en Supabase (AD-06) |
| Supabase gratuito: sin backups automáticos y pausa tras 1 semana sin uso | Backup diario y ping programado en GitHub Actions |
| Supabase gratuito: 2 proyectos, 500 MB de base de datos y 1 GB de archivos | Suficiente para desarrollo y producción de un hotel pequeño; imágenes optimizadas |
| El límite de intentos del OTP de Supabase es por IP, no por cuenta | RN-APP-002 y PAR-15 ajustados al límite por IP |
| Resend gratuito: 100 correos por día y 3,000 por mes | Suficiente para el proyecto y la demostración |
| El APK por Trusted Web Activity requiere Chrome en el teléfono y el dominio verificado (`assetlinks.json`) | Chrome viene en casi todos los Android; el archivo se publica desde `public/.well-known/` |
| Los logs de Vercel Hobby duran 1 hora | Los errores quedan guardados en Sentry |

---

## 11. Cuentas y costos

| Servicio | Plan | Costo | Responsable sugerido |
|---|---|---|---|
| GitHub (organización existente) | Free | $0 | Plataforma (P3) |
| Supabase (2 proyectos) | Free | $0 | Datos (P1) |
| Vercel | Hobby | $0 | Plataforma (P3) |
| Stripe | Sandbox | $0 | Backend (P2) |
| Resend | Free | $0 | Plataforma (P3) |
| Sentry | Free (Developer) | $0 | Plataforma (P3) |
| UptimeRobot | Free | $0 | Plataforma (P3) |
| **Dominio** | Anual | **~$10–15 al año** | Plataforma (P3) |

Las cuentas deben crearse con un correo del equipo y compartirse de forma segura (no por chat en texto plano).

---

## 12. Cambios respecto a los requisitos no funcionales originales

| Original | Nuevo | Motivo |
|---|---|---|
| Spring Boot (Java) en EC2 | Funciones SQL + Route Handlers de Next.js | Restricción de lenguaje (TypeScript) y tiempo disponible |
| Cloudflare Tunnel y WAF | HTTPS y protección de Vercel y Supabase | No hay servidor propio que proteger |
| Cloudflare R2 | Supabase Storage | Integrado con los permisos |
| CloudWatch + Grafana Cloud + Micrometer | Sentry + UptimeRobot | Suficiente y gratuito |
| Docker e imágenes por commit | Despliegues de Vercel por commit con rollback | Mismo objetivo sin administrar contenedores |
| "La primera versión puede operar con una sola EC2" | Servicios administrados (sin servidores propios) | Menos mantenimiento |
| **Se conservan:** Supabase Auth, PostgreSQL de Supabase, cookies seguras compatibles con SSR, secretos fuera de Git, no almacenar tarjetas, OpenAPI, rollback, RPO/RTO definidos | | |

---

## 13. Pendientes

- [ ] **Confirmar con el ingeniero** que la app Android como PWA empaquetada en APK (Trusted Web Activity) cumple el requisito del enunciado.
- [ ] **Comprar el dominio** y configurar el DNS (Vercel, Resend y `assetlinks.json`).
- [ ] **Crear las cuentas** de la sección 11.
- [ ] **Recibir el frontend de Kim** y adaptar OBJ-05 y los módulos a lo que ya existe.
