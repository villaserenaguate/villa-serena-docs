# OBJ-03 — Levantar la infraestructura en la nube

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P3 — Plataforma y calidad | — | `feat/obj-03-infraestructura` | ~12 h |

Este objetivo es **mayormente manual** (crear cuentas en paneles web). La IA ayuda con la configuración en código y la documentación.

## Pasos manuales (antes del prompt)

Crear las cuentas con un **correo del equipo** y guardar las credenciales en un gestor de contraseñas compartido (no en el chat ni en Git).

- [ ] **Dominio:** comprarlo (Cloudflare, Porkbun u otro) y gestionar su DNS.
- [ ] **Supabase:** crear 2 proyectos gratuitos, `villaserena-dev` y `villaserena-prod` (región más cercana: us-east o similar).
- [ ] **Vercel:** crear el proyecto de `apps/web` (plan Hobby). Conectar el dominio: principal → producción, `dev.<dominio>` → desarrollo. Generar un token para GitHub Actions.
- [ ] **Resend:** verificar el dominio (registros DNS) y crear una API key.
- [ ] **Stripe:** crear la cuenta y usar solo el sandbox. Obtener las claves de prueba.
- [ ] **Sentry:** crear la organización y dos proyectos (web y mobile).
- [ ] **UptimeRobot:** crear la cuenta (los monitores se crean al final del prompt).
- [ ] **Expo:** crear la cuenta del equipo (se usa en OBJ-14).
- [ ] **Supabase Auth en dev y prod:** configurar el SMTP con Resend (host, puerto, usuario y API key), remitente `reservas@<dominio>`, y las URL permitidas del sitio.
- [ ] **Stripe webhook:** crear el endpoint `https://dev.<dominio>/api/stripe/webhook` (y el de producción) con los eventos de checkout y reembolso. Guardar el secreto de firma.

---

## Prompt 03-A — Configuración, variables y monitoreo

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es la parte en código del OBJ-03: preparar la configuración de ambientes, variables de entorno y monitoreo. Las cuentas ya fueron creadas manualmente.

Lee: docs/14 - Tecnologias y Arquitectura.md (secciones 2, 7, 9, 10 y 11) y docs/13 - Objetivos del Proyecto.md (OBJ-03).

1. Variables de entorno:
   - Crea apps/web/.env.example y apps/mobile/.env.example con TODAS las variables necesarias (sin valores reales), comentadas: URL y anon key de Supabase, SUPABASE_SERVICE_ROLE_KEY (solo web servidor), STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET, NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY, RESEND_API_KEY, CORREO_REMITENTE, SENTRY_DSN, NEXT_PUBLIC_SITE_URL, EXPO_PUBLIC_SUPABASE_URL, EXPO_PUBLIC_SUPABASE_ANON_KEY, EXPO_PUBLIC_API_URL, zona horaria, etc.
   - Crea un módulo de configuración tipado (apps/web/src/lib/env.ts) que valide las variables con Zod y falle con un mensaje claro si falta alguna. Separa variables de servidor y públicas; las de servidor nunca deben importarse desde componentes de cliente.
   - Verifica que .gitignore excluye todos los .env excepto .env.example.

2. Endpoint de salud: GET apps/web/src/app/api/health → { estado: "ok", hora } y prueba que Supabase responde (consulta ligera). Lo usará UptimeRobot.

3. Sentry:
   - Configura @sentry/nextjs en apps/web (cliente, servidor y edge) con el DSN por variable de entorno y sin enviar datos personales.
   - Deja preparado el paquete para la app (@sentry/react-native) y documenta que se activa en OBJ-14.

4. Página de prueba: la página de inicio muestra "Villa Serena — en construcción" y el ambiente (dev/prod) para verificar el despliegue.

5. Documentación docs/infraestructura.md:
   - Diagrama de arquitectura (Mermaid) según el documento 14.
   - Tabla de servicios, ambientes y dónde se configura cada variable (Vercel, GitHub Secrets, Supabase, Expo).
   - Cómo configurar el SMTP de Resend en Supabase Auth, el webhook de Stripe y los monitores de UptimeRobot (sitio y /api/health, alerta por correo).
   - Nota de zona horaria: PAR-21 (America/Guatemala; pg_cron en UTC).

Criterios de terminado:
- La app web arranca en local con .env.local y falla con mensaje claro si falta una variable.
- /api/health responde.
- docs/infraestructura.md completo.
- (Después del despliegue de OBJ-04 B) la página se ve en dev.<dominio> y <dominio>, llega un correo de prueba, un evento de prueba de Stripe llega al webhook y UptimeRobot alerta al detener el sitio.
````

## Revisión humana

- [ ] Ninguna clave real en el repositorio (`git log -p | grep -i "sk_"` no debe mostrar nada).
- [ ] Las credenciales están en el gestor de contraseñas del equipo.
