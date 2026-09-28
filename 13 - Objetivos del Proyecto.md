# 13 — Objetivos del Proyecto

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** 📝 Borrador para revisión del equipo · actualizado con las tecnologías del documento 14
> **Fecha:** 25 de septiembre de 2026
> **Documento anterior:** 12 — Casos de Uso
> **Siguiente paso:** 14 — Tecnologías y Arquitectura → Plan de trabajo → Prompts para IA

---

## Índice

1. [Objetivo general](#1-objetivo-general)
2. [Cómo leer los objetivos](#2-cómo-leer-los-objetivos)
3. [Mapa de objetivos](#3-mapa-de-objetivos)
4. [Fase 0 — Fundaciones](#4-fase-0--fundaciones)
5. [Fase 1 — Lógica de negocio](#5-fase-1--lógica-de-negocio)
6. [Fase 2 — Módulos](#6-fase-2--módulos)
7. [Fase 3 — Cierre](#7-fase-3--cierre)
8. [Paquetes sugeridos por persona](#8-paquetes-sugeridos-por-persona)
9. [Orden y dependencias](#9-orden-y-dependencias)
10. [Carga estimada y riesgo](#10-carga-estimada-y-riesgo)

---

## 1. Objetivo general

Desarrollar e implementar, antes de finalizar el semestre, el PMS de Villa Serena:

- Un **motor de reservas web**.
- Un **panel privado** para Recepción, Room Service, Mantenimiento/Limpieza y Administración.
- Una **app Android para el huésped**.

Todo integrado sobre **una sola base de datos en la nube**, con seguridad por roles y despliegue continuo, según el alcance aprobado (documento 01).

---

## 2. Cómo leer los objetivos

Cada objetivo es una **unidad de trabajo** que una persona puede tomar y que se puede convertir en un prompt para la IA.

| Campo | Significado |
|---|---|
| **Qué es** | Resumen en una frase |
| **Incluye** | Lista concreta de lo que hay que construir |
| **Terminado cuando** | Condiciones verificables; si no se cumplen todas, el objetivo no está terminado |
| **Tecnología** | Herramientas con las que se construye (documento 14) |
| **Documentos** | Documentos de referencia que la IA debe leer antes de trabajar |
| **Depende de** | Objetivos que deben estar terminados (o avanzados) antes |
| **Carga** | Estimación en horas **sin** IA (sección 10) |

**Regla dentro de cada objetivo:** se construyen primero las historias de prioridad **Alta**, luego Media y al final Baja.

**Tecnologías:** cada objetivo indica en el campo **Tecnología** con qué se construye, según el documento 14 — Tecnologías y Arquitectura. Todo el código de aplicación es **TypeScript con Next.js**; la base de datos usa **SQL**.

**Frontend existente:** si el frontend de Kim ya tiene pantallas hechas, los objetivos de la web (OBJ-05, 08 a 13) cambian de "construir" a "**conectar y completar**" esas pantallas.

---

## 3. Mapa de objetivos

| ID | Objetivo | Fase | Carga |
|---|---|---|---|
| OBJ-01 | Levantar la base de datos del sistema | 0 · Fundaciones | ~25 h |
| OBJ-02 | Implementar autenticación, roles y permisos | 0 · Fundaciones | ~20 h |
| OBJ-03 | Levantar la infraestructura en la nube | 0 · Fundaciones | ~12 h |
| OBJ-04 | Configurar el repositorio y el CI/CD | 0 · Fundaciones | ~15 h |
| OBJ-05 | Construir la base del frontend web | 0 · Fundaciones | ~15 h |
| OBJ-06 | Levantar el backend de reservas, cuentas y estadía | 1 · Lógica | ~30 h |
| OBJ-07 | Integrar pagos en línea, correos y procesos automáticos | 1 · Lógica | ~20 h |
| OBJ-08 | Construir la web pública (motor de reservas) | 2 · Módulos | ~75 h |
| OBJ-09 | Construir el panel de Recepción | 2 · Módulos | ~115 h |
| OBJ-10 | Construir Administración: catálogos, tarifas y configuración | 2 · Módulos | ~45 h |
| OBJ-11 | Construir Administración: personal, turnos, inventario, supervisión e indicadores | 2 · Módulos | ~60 h |
| OBJ-12 | Construir el módulo de Room Service | 2 · Módulos | ~35 h |
| OBJ-13 | Construir el módulo de Mantenimiento y Limpieza | 2 · Módulos | ~55 h |
| OBJ-14 | Construir la app Android: base, acceso y estadía | 2 · Módulos | ~25 h |
| OBJ-15 | Construir la app Android: servicios, pago y check-out | 2 · Módulos | ~30 h |
| OBJ-16 | Construir la preparación para el Channel Manager | 2 · Módulos | ~40 h |
| OBJ-17 | Ejecutar pruebas integrales y asegurar la calidad | 3 · Cierre | ~25 h |
| OBJ-18 | Implementar respaldo y recuperación de datos | 3 · Cierre | ~6 h |
| OBJ-19 | Elaborar la documentación final y la demostración | 3 · Cierre | ~15 h |

---

## 4. Fase 0 — Fundaciones

Estos objetivos **desbloquean a todo el equipo**. Deben empezar primero.

### OBJ-01 — Levantar la base de datos del sistema

**Qué es:** crear el modelo de datos completo en la nube, con todas las tablas, estados, relaciones y datos de prueba.

**Incluye:**
- Diagrama entidad-relación (ER) aprobado por el equipo.
- Tablas para: configuración del hotel, tipos de habitación, habitaciones, huéspedes, reservas, huéspedes adicionales, cuentas, cargos, pagos, reembolsos, temporadas, ajuste de fin de semana, categorías e ítems del menú, pedidos y sus ítems, solicitudes y sus ítems, catálogo de artículos para huésped, incidencias/órdenes, repuestos usados, limpiezas, observaciones, objetos olvidados, productos, movimientos de inventario, reportes de faltante, empleados, turnos, asignaciones de turno, canales, registro de peticiones de la API e **historial de estados**.
- Tipos enumerados con **los códigos exactos del documento 07** (`PENDIENTE_PAGO`, `EN_ESTADIA`, `FUERA_DE_SERVICIO`…).
- Restricciones en la base de datos:
  - Una habitación no puede tener reservas activas que se traslapen.
  - El stock nunca puede ser negativo.
  - Unicidad de correo, número de habitación, código de reserva y (canal + identificador externo).
- Índices para las búsquedas frecuentes: fechas, estado, documento, código y canal.
- Migraciones versionadas en el repositorio.
- **Datos de prueba (seed):** 1 hotel, 3 tipos de habitación, 12 habitaciones, menú, amenidades, catálogo de artículos, productos y 1 usuario por rol.

**Terminado cuando:**
- [ ] Las migraciones crean la base desde cero en un ambiente vacío sin errores.
- [ ] El diagrama ER está en el repositorio y fue aprobado.
- [ ] El seed carga los datos de prueba y se pueden consultar.
- [ ] Insertar dos reservas activas traslapadas en la misma habitación es rechazado por la base de datos.

**Tecnología:** Supabase (PostgreSQL) · migraciones con Supabase CLI · extensiones `btree_gist` y `pgcrypto` · `supabase/seed.sql` · diagrama ER en Mermaid dentro de `docs/`
**Documentos:** 07 (Estados), 08 (Inventario, turnos y personal), 10 (Reglas de negocio y parámetros), 11 (Requisitos funcionales).
**Depende de:** —
**Carga:** ~25 h

---

### OBJ-02 — Implementar autenticación, roles y permisos

**Qué es:** controlar quién entra al sistema y qué puede ver o hacer cada rol, aplicado directamente en la base de datos.

**Incluye:**
- Acceso del **personal** con correo y contraseña. No hay registro público; las cuentas las crea el Admin.
- Acceso del **huésped** con correo y código OTP: 6 dígitos, vence en 10 minutos, con límite de intentos por IP configurado en Supabase (RN-APP-002). Se envía con Resend.
- Perfiles de empleado (rol, área, estado) y de huésped, vinculados al usuario.
- Funciones auxiliares "rol del usuario actual" y "área del usuario actual".
- Políticas de acceso **por tabla**, según la matriz del documento 09.
- Tres contenedores de archivos: público (fotos), privado de documentos (identificaciones) y privado de operación (fotos de incidencias y objetos).
- Consulta pública de disponibilidad y precios **sin** exponer la tabla de reservas.

**Terminado cuando:**
- [ ] Cada rol puede hacer lo marcado ✅ en la matriz y se le **rechaza** lo marcado "—", con al menos una prueba automática por módulo.
- [ ] Un huésped no puede ver datos de otro huésped.
- [ ] Un empleado `INACTIVO` no puede entrar.
- [ ] El OTP llega por correo y respeta el vencimiento y el límite de intentos.

**Tecnología:** Supabase Auth (contraseña y OTP por correo con SMTP de Resend) · Row Level Security · Supabase Storage · pruebas con pgTAP
**Documentos:** 02 (Roles), 09 (Matriz de permisos), 10 (RN-SEG, RN-APP-001 a 004, RN-PER).
**Depende de:** OBJ-01
**Carga:** ~20 h

---

### OBJ-03 — Levantar la infraestructura en la nube

**Qué es:** preparar todos los servicios en la nube donde vivirá el sistema.

**Incluye:**
- Ambientes separados de **desarrollo** y **producción**.
- Hosting de la web con URL pública.
- Gestión de secretos (claves de Stripe, correo y base de datos). Nunca se guardan en Git.
- **Dominio propio** comprado y configurado (DNS para Vercel, Resend y el archivo `assetlinks.json` del APK).
- Proveedor de correo transaccional con remitente del dominio.
- Cuenta de Stripe en **modo prueba**, con su webhook apuntando al ambiente.
- Zona horaria America/Guatemala en los procesos del servidor (PAR-21).
- Monitoreo básico de errores y caídas, con alerta por correo.
- Diagrama de arquitectura en el repositorio.

**Terminado cuando:**
- [ ] Una página de prueba está publicada en una URL pública en ambos ambientes.
- [ ] Un correo de prueba llega a una bandeja real.
- [ ] Un evento de prueba de Stripe llega al ambiente de desarrollo.
- [ ] Se probó que una caída genera una alerta.

**Tecnología:** Supabase (2 proyectos gratuitos) · Vercel Hobby con dominio propio · Resend con dominio verificado · Stripe sandbox · Sentry · UptimeRobot
**Documentos:** 01 (ALC-TRA-05, 06 y 09), 10 (RN-SEG-007, RN-SEG-008, PAR-21).
**Depende de:** —
**Carga:** ~12 h

---

### OBJ-04 — Configurar el repositorio y el CI/CD

**Qué es:** organizar el código del equipo y automatizar pruebas y despliegues.

**Incluye:**
- Estructura del repositorio según el documento 14 (sección 6): app Next.js, `supabase/`, `twa/`, `tests/` y `docs/`.
- Estrategia de ramas: `main` (producción), `develop` (desarrollo) y una rama por historia de usuario.
- Protección de ramas: pull request obligatorio, al menos 1 aprobación y verificaciones en verde.
- Pipeline automático que:
  - Revisa estilo y tipos, ejecuta pruebas, compila y aplica migraciones.
  - Despliega a desarrollo al hacer merge en `develop` y a producción al hacer merge en `main`.
  - Genera el **APK** de la app como artefacto descargable.
- Plantilla de pull request con el ID de la historia y la "Definición de Hecho" del documento 03.
- Tablero de proyecto con las **96 historias como tareas**, etiquetadas por épica, prioridad y plataforma.

**Terminado cuando:**
- [ ] Un pull request de prueba pasa por todo el pipeline y se despliega solo.
- [ ] Un pull request con una prueba fallida **no** se puede fusionar.
- [ ] El tablero tiene las 96 historias.

**Tecnología:** GitHub Actions · GitHub Projects · Supabase CLI (`db push`) · Vercel CLI · Bubblewrap (APK) · ESLint + Prettier · Vitest · pgTAP
**Documentos:** 03 (Plantilla y Definición de Hecho), 04 (índice de historias).
**Depende de:** OBJ-03
**Carga:** ~15 h

---

### OBJ-05 — Construir la base del frontend web

**Qué es:** dejar lista la estructura de la web sobre la que se construyen todos los módulos.

**Incluye:**
- Organizar el proyecto Next.js con su sistema de rutas oficial (App Router): una zona **pública** y una zona **privada** (panel) con una sección por rol.
- Layout del panel con menú según el rol del usuario.
- Pantalla de login del personal y cierre de sesión.
- Protección de rutas: un rol no puede abrir las pantallas de otro.
- Conexión a los datos y manejo de la sesión.
- Componentes compartidos: tabla con filtros, formulario con validación, modal, avisos (toast), **etiqueta de estado** con los textos y colores del documento 07, y selector de fechas.
- Suscripción reutilizable a actualizaciones en tiempo real.
- Formato de moneda (Q) y fechas en la zona horaria de Guatemala.

**Terminado cuando:**
- [ ] Cada rol entra y ve solo su menú.
- [ ] Intentar abrir la ruta de otro rol redirige o muestra "sin permiso".
- [ ] La compilación pasa en el pipeline.

**Tecnología:** Next.js 15 (App Router) · `@supabase/ssr` · Tailwind CSS 4 · shadcn/ui · TanStack Query y Table · React Hook Form + Zod · date-fns + `@date-fns/tz` · Supabase Realtime
**Documentos:** 02 (Roles), 07 (Estados, etiquetas), 09 (Permisos).
**Depende de:** OBJ-02, OBJ-04
**Carga:** ~15 h

---

## 5. Fase 1 — Lógica de negocio

Es el **backend**: las reglas que usan la web, la app y el Channel Manager. Deben estar listas antes de que las pantallas las necesiten.

### OBJ-06 — Levantar el backend de reservas, cuentas y estadía

**Qué es:** programar en el servidor todas las reglas de reservas, precios, cuentas, check-in y check-out.

**Incluye:**
- **Disponibilidad** por tipo y noche (RN-RES-008).
- **Cálculo de precio** en el servidor: base × temporada × fin de semana, con su excepción para canales (RN-TAR-001 a 010).
- **Crear reserva** con revalidación de disponibilidad y protección contra dos reservas simultáneas de la última habitación.
- **Modificar reserva:** revalidar, recalcular, ajustar el cargo y manejar el saldo a favor.
- **Cancelar reserva:** política de 48 horas, penalidad y cálculo del reembolso (RN-CAN).
- **Cuenta:** cargos, anulación de cargos, pagos en recepción y cálculo del saldo.
- **Asignar habitación.**
- **Check-in y check-out**, con **todos** los efectos automáticos (documento 07, sección 11).
- **Validación de transiciones** de estado (lista cerrada del documento 07) y registro en el **historial de estados**.

**Terminado cuando:**
- [ ] Hay pruebas automáticas para cada regla RN-RES, RN-TAR, RN-PAG y RN-CAN, y para cada transición R1 a R9.
- [ ] La prueba de concurrencia (dos reservas simultáneas de la última habitación) confirma solo una.
- [ ] Una transición no permitida se rechaza.

**Tecnología:** Funciones SQL (RPC) y triggers en Supabase · restricciones de PostgreSQL · pruebas con pgTAP y Vitest
**Documentos:** 07, 10, 12 (UC-01 a 04, UC-09, UC-10).
**Depende de:** OBJ-01, OBJ-02
**Carga:** ~30 h

---

### OBJ-07 — Integrar pagos en línea, correos y procesos automáticos

**Qué es:** conectar el sistema con Stripe y el correo, y programar las tareas que corren solas.

**Incluye:**
- **Pago en línea** con Stripe (modo prueba) desde la web y la app.
- **Webhook** con verificación de firma e idempotencia: un evento se procesa una sola vez.
- **Reembolsos** totales y parciales.
- **Correos:** confirmación de reserva (con código y enlace a la app), código OTP, cancelación, resumen de check-out y comprobante.
- **Comprobante de pago en PDF** con número correlativo.
- **Procesos programados:**
  - Cancelar reservas no pagadas en 30 minutos.
  - Marcar no-show a las 23:59 (hora de Guatemala).
- Alerta de stock mínimo al Admin.

**Terminado cuando:**
- [ ] Un pago de prueba confirma la reserva **solo** al llegar el webhook.
- [ ] Un evento de Stripe repetido no duplica el pago.
- [ ] Una reserva no pagada se cancela sola a los 30 minutos.
- [ ] El no-show se marca y aplica la penalidad.
- [ ] Todos los correos llegan con el contenido correcto.

**Tecnología:** Route Handlers de Next.js · Stripe SDK para Node (sandbox) · Resend + React Email · `@react-pdf/renderer` · pg_cron
**Documentos:** 10 (RN-PAG, RN-CAN, PAR-06, 09 y 21), 12 (UC-05, UC-15).
**Depende de:** OBJ-06, OBJ-03
**Carga:** ~20 h

---

## 6. Fase 2 — Módulos

Cada objetivo es un **módulo completo** (pantallas y su conexión con el backend). Las pantallas pueden empezarse con datos simulados mientras se terminan OBJ-06 y OBJ-07.

### OBJ-08 — Construir la web pública (motor de reservas)

**Qué es:** el sitio donde el cliente conoce el hotel, reserva, paga y gestiona su reserva.

**Incluye:** HU-HUE-01 a HU-HUE-11.
- Inicio, catálogo de habitaciones y búsqueda con calendario.
- Precio con desglose por noche.
- Formulario de reserva, pago y página de resultado.
- "Mi reserva" con acceso OTP y cancelación.
- Check-in anticipado con carga de documento.

**Terminado cuando:**
- [ ] UC-01, UC-02, UC-05, UC-10, UC-11 y UC-12 funcionan de extremo a extremo en la URL pública.
- [ ] Se ve bien en computadora y en teléfono.

**Tecnología:** Next.js (grupo de rutas `(publico)`) · shadcn/ui · React Hook Form + Zod · Stripe Checkout · Supabase Auth (OTP) · Supabase Storage
**Documentos:** 04 (HU - Cliente y Huésped), 10, 12.
**Depende de:** OBJ-05, OBJ-06, OBJ-07
**Carga:** ~75 h

---

### OBJ-09 — Construir el panel de Recepción

**Qué es:** la herramienta diaria de Recepción, incluido el calendario Gantt.

**Incluye:** HU-REC-01 a HU-REC-23.
- Huéspedes, reservas, disponibilidad y asignación de habitación.
- Vista del día y **calendario Gantt** (ver y crear reservas).
- Estado de habitaciones, check-in y check-out.
- Cargos, pagos, cuenta y comprobante.
- Solicitudes, reporte de daños y avisos en tiempo real.

**Terminado cuando:**
- [ ] UC-02 (A4), UC-03, UC-04, UC-09, UC-10 y UC-14 funcionan.
- [ ] El Gantt se actualiza sin recargar cuando otra persona crea o modifica una reserva.

**Tecnología:** Next.js (`/panel/recepcion`) · componente Gantt propio con Tailwind · TanStack Table · Supabase Realtime · `@react-pdf/renderer`
**Documentos:** 04 (HU - Recepcionista), 07, 09, 12.
**Depende de:** OBJ-05, OBJ-06
**Carga:** ~115 h

---

### OBJ-10 — Construir Administración: catálogos, tarifas y configuración

**Qué es:** las pantallas con que el Admin define el hotel y sus precios.

**Incluye:** HU-ADM-05 a HU-ADM-11 y HU-ADM-19.
- Tipos de habitación (con fotos) y habitaciones.
- Menú de Room Service y amenidades.
- Temporadas, ajuste de fin de semana y **vista previa de tarifas**.
- Datos generales del hotel (horarios, Wi-Fi, política).

**Terminado cuando:**
- [ ] UC-17 funciona.
- [ ] Los precios de la vista previa coinciden exactamente con los de la web pública.
- [ ] Un Admin configura el hotel desde cero **sin tocar la base de datos**.

**Tecnología:** Next.js (`/panel/admin`) · Supabase Storage (fotos) · función SQL de cálculo de precios (misma que usa la web pública)
**Documentos:** 04 (HU - Administrador), 10 (RN-TAR), 12.
**Depende de:** OBJ-05, OBJ-01
**Carga:** ~45 h

---

### OBJ-11 — Construir Administración: personal, turnos, inventario, supervisión e indicadores

**Qué es:** las pantallas con que el Admin gestiona personas, insumos, mantenimiento y resultados.

**Incluye:** HU-ADM-01 a HU-ADM-04, HU-ADM-12 a HU-ADM-18 y HU-ADM-20.
- Empleados (crear, editar, desactivar), turnos y panel "personal en turno".
- Productos, catálogo de artículos para el huésped, entradas, ajustes, alertas y reportes de faltante.
- Revisión, asignación, cierre y cancelación de incidencias.
- Reasignación de trabajo en curso.
- Indicadores (ocupación, ingresos, reservas por canal).

**Terminado cuando:**
- [ ] UC-18 y UC-19 funcionan, y UC-08 del lado del Admin.
- [ ] No se puede desactivar a un empleado con trabajo en curso.

**Tecnología:** Next.js (`/panel/admin`) · TanStack Table · Recharts · Route Handler `/api/empleados` (crear cuentas con la clave de servidor)
**Documentos:** 04 (HU - Administrador), 08, 10 (RN-INV, RN-TUR, RN-PER), 12.
**Depende de:** OBJ-05, OBJ-02
**Carga:** ~60 h

---

### OBJ-12 — Construir el módulo de Room Service

**Qué es:** la gestión de pedidos de comida, de la cocina a la habitación.

**Incluye:**
- HU-RS-01 a HU-RS-10.
- La lógica de pedidos: transiciones S1 a S5 y **un solo cargo** al entregar.

**Terminado cuando:**
- [ ] UC-06 funciona desde la web.
- [ ] Los pedidos nuevos llegan en tiempo real con aviso sonoro.
- [ ] Un pedido cancelado no genera cargo.

**Tecnología:** Next.js (`/panel/room-service`) · triggers y funciones SQL · Supabase Realtime (aviso sonoro)
**Documentos:** 04 (HU - Room Service), 07 (sección 6), 10 (RN-RS), 12.
**Depende de:** OBJ-05, OBJ-06
**Carga:** ~35 h

---

### OBJ-13 — Construir el módulo de Mantenimiento y Limpieza

**Qué es:** las herramientas del personal de piso, pensadas para usarse desde el teléfono (web responsive).

**Incluye:**
- HU-MYL-01 a HU-MYL-17.
- La lógica de:
  - Limpieza (transiciones C1 a C8).
  - Solicitudes (Q1 a Q5).
  - Incidencias (M1 a M8).
  - Consumos de inventario.

**Terminado cuando:**
- [ ] UC-07, UC-13 y UC-08 (lado del técnico) funcionan.
- [ ] Los consumos descuentan stock.
- [ ] Un consumo mayor al stock se rechaza.

**Tecnología:** Next.js (`/panel/piso`, diseño para teléfono) · triggers y funciones SQL · Supabase Realtime · Supabase Storage (fotos)
**Documentos:** 04 (HU - Mantenimiento y Limpieza), 07, 08, 10 (RN-LIM, RN-MAN, RN-INV), 12.
**Depende de:** OBJ-05, OBJ-06 y los productos de OBJ-11
**Carga:** ~55 h

---

### OBJ-14 — Construir la app Android: base, acceso y estadía

**Qué es:** la app del huésped (PWA de Next.js empaquetada como APK): entrada y consulta de su estadía.

**Incluye:**
- Sección `/app` del proyecto Next.js configurada como **PWA instalable** y empaquetada como **APK** (Trusted Web Activity).
- Acceso con OTP (HU-HUE-08) y "Mis reservas" (HU-HUE-09).
- Check-in anticipado (HU-HUE-11).
- Detalle de la estadía (HU-HUE-12).
- Amenidades y Wi-Fi (HU-HUE-18).
- "Mi cuenta" en vivo (HU-HUE-19).
- Historial en solo lectura (HU-HUE-21).

**Terminado cuando:**
- [ ] El APK se instala en un teléfono Android real y abre sin barra del navegador.
- [ ] UC-11 funciona.
- [ ] El huésped ve su estadía y su cuenta actualizadas en vivo.

**Tecnología:** Next.js (grupo de rutas `(app)`) como PWA con Serwist y `app/manifest.ts` · APK con Bubblewrap (Trusted Web Activity) y `assetlinks.json` · Supabase Auth (OTP) · Supabase Realtime
**Documentos:** 04 (HU - Cliente y Huésped), 09, 10 (RN-APP), 12.
**Depende de:** OBJ-02, OBJ-06
**Carga:** ~25 h

---

### OBJ-15 — Construir la app Android: servicios, pago y check-out

**Qué es:** lo que el huésped hace durante su estadía desde la app.

**Incluye:**
- Pedir room service y seguirlo en vivo (HU-HUE-13, 14).
- Solicitar limpieza y artículos y ver su estado (HU-HUE-15, 16, 17).
- Pagar el saldo y hacer check-out (HU-HUE-20).

**Terminado cuando:**
- [ ] UC-06 y UC-13 funcionan desde la app.
- [ ] UC-04 (A1, check-out desde la app) funciona con pago de prueba y respeta la ventana horaria.

**Tecnología:** Next.js (grupo `(app)`) · Stripe Checkout · Supabase Realtime
**Documentos:** 04 (HU - Cliente y Huésped), 07, 10, 12.
**Depende de:** OBJ-14, OBJ-07, OBJ-12, OBJ-13
**Carga:** ~30 h

---

### OBJ-16 — Construir la preparación para el Channel Manager

**Qué es:** dejar el sistema listo para recibir reservas de Booking y Expedia, demostrado con un canal simulado.

**Incluye:**
- HU-CM-01 a HU-CM-05.
- **Documento de diseño** de la integración (ALC-CM-01): adaptadores y sincronización de disponibilidad, tarifas e inventario.
- API documentada con **OpenAPI**.
- Registro de todas las peticiones.

**Terminado cuando:**
- [ ] UC-16 funciona con el canal simulado.
- [ ] Reenviar la misma reserva no la duplica.
- [ ] La reserva externa aparece en el Gantt con el ícono de su canal.

**Tecnología:** Route Handlers de Next.js (`/api/canal/v1`) · OpenAPI 3 + Scalar · `pgcrypto` para el hash de claves · funciones SQL de reservas compartidas
**Documentos:** 04 (HU - Channel Manager), 10 (RN-CM, RN-TAR-010, RN-PAG-008), 12.
**Depende de:** OBJ-06
**Carga:** ~40 h

---

## 7. Fase 3 — Cierre

### OBJ-17 — Ejecutar pruebas integrales y asegurar la calidad

**Qué es:** comprobar que todo el sistema funciona junto antes de la entrega.

**Incluye:**
- Pruebas de extremo a extremo de los casos de uso principales: UC-01 a UC-06, UC-13, UC-15 y UC-16.
- Pruebas de autorización de toda la matriz.
- Prueba de concurrencia de reservas.
- Registro de errores y seguimiento de su corrección.
- Datos de demostración realistas.

**Terminado cuando:**
- [ ] Las pruebas corren en el pipeline y pasan.
- [ ] No quedan errores críticos abiertos.

**Tecnología:** Playwright · pgTAP · Vitest · ejecución en GitHub Actions
**Documentos:** 09, 10, 12.
**Depende de:** todos los módulos
**Carga:** ~25 h

---

### OBJ-18 — Implementar respaldo y recuperación de datos

**Qué es:** asegurar que los datos se pueden recuperar si algo falla.

**Incluye:**
- Política de backups: frecuencia y retención.
- Pérdida máxima aceptable (RPO) y tiempo máximo de recuperación (RTO).
- **Restauración probada** en un ambiente separado.
- Guía paso a paso de recuperación.

**Terminado cuando:**
- [ ] Se restauró un backup con éxito y quedó documentado, con fecha y resultado.

**Tecnología:** GitHub Actions programado con `pg_dump` · restauración en el ambiente local o de desarrollo
**Documentos:** 01 (ALC-TRA-08).
**Depende de:** OBJ-01, OBJ-03
**Carga:** ~6 h

---

### OBJ-19 — Elaborar la documentación final y la demostración

**Qué es:** dejar el proyecto listo para entregar y presentar.

**Incluye:**
- README con instrucciones de instalación.
- Manual de uso por rol.
- API publicada (OpenAPI).
- Documentación definitiva actualizada si hubo cambios.
- Guion de la demostración y presentación.

**Terminado cuando:**
- [ ] Una persona externa puede instalar y usar el sistema con el README.
- [ ] La demostración se ensayó al menos una vez.

**Tecnología:** Markdown en `docs/` · Scalar para el API
**Documentos:** todos.
**Depende de:** todos
**Carga:** ~15 h (compartido por todo el equipo)

---

## 8. Paquetes sugeridos por persona

Cada paquete agrupa objetivos que **comparten contexto**. Así una persona y su IA trabajan sobre las mismas tablas, reglas y pantallas. Ustedes deciden a quién le toca cada paquete y en qué fechas.

| Paquete | Enfoque | Objetivos | Carga aprox. |
|---|---|---|---|
| **P1** | Datos y seguridad | OBJ-01, OBJ-02, OBJ-18, OBJ-10 | ~96 h |
| **P2** | Lógica de negocio (backend) | OBJ-06, OBJ-07, OBJ-16 | ~90 h |
| **P3** | Plataforma y calidad | OBJ-03, OBJ-04, OBJ-17, OBJ-11 | ~112 h |
| **P4** | Web base y Recepción | OBJ-05, OBJ-09 | ~130 h |
| **P5** | Web pública y Room Service | OBJ-08, OBJ-12 | ~110 h |
| **P6** | App móvil y personal de piso | OBJ-14, OBJ-15, OBJ-13 | ~110 h |
| **Todos** | Cierre | OBJ-19 | ~15 h |

**Por qué se agrupan así:**

| Paquete | Razón |
|---|---|
| P1 | Quien diseña las tablas también construye las pantallas de catálogos, que son las que más dependen del modelo de datos |
| P2 | El Channel Manager reutiliza las mismas reglas de disponibilidad y precio del backend |
| P3 | Quien arma el pipeline también arma las pruebas integrales; Admin de personal e inventario equilibra la carga |
| P4 | Recepción es el módulo más grande y el que más usa los componentes base |
| P5 | Web pública y Room Service comparten el menú, los pedidos y el flujo del huésped |
| P6 | La app y el módulo de piso están pensados para el teléfono, y los servicios de la app dependen de ese módulo |

---

## 9. Orden y dependencias

```
FASE 0  OBJ-01 ──► OBJ-02 ──► OBJ-05
        OBJ-03 ──► OBJ-04 ──┘
            │
FASE 1      └──► OBJ-06 ──► OBJ-07
                   │
FASE 2             ├──► OBJ-08 (web pública)     ◄── OBJ-07
                   ├──► OBJ-09 (recepción)
                   ├──► OBJ-12 (room service) ──┐
                   ├──► OBJ-13 (piso) ◄── OBJ-11┤
                   ├──► OBJ-16 (channel mgr)    │
                   └──► OBJ-14 (app base) ──► OBJ-15 (app servicios)
        OBJ-10, OBJ-11 (admin) ◄── OBJ-05

FASE 3  OBJ-17 (pruebas) · OBJ-18 (backups) · OBJ-19 (documentación)
```

**Recomendaciones de orden:**

1. **OBJ-01, OBJ-03 y OBJ-04 empiezan el primer día**, en paralelo.
2. Mientras se termina la Fase 0, quienes construyen pantallas (P4, P5, P6) pueden **diseñarlas con datos simulados** para no quedarse detenidos.
3. **OBJ-06 es el cuello de botella:** casi todo depende de él. Conviene que sea lo primero que se termine después de la base de datos.

---

## 10. Carga estimada y riesgo

| Dato | Valor |
|---|---|
| Carga total estimada **sin** IA | **~665 horas** |
| Capacidad del equipo (6 personas × 1–2 h × ~4 días × 6 semanas) | **~145–290 horas** |
| Carga estimada **con** IA (reducción aproximada del 50 %) | **~330 horas** |

**Conclusión:** incluso con IA, el trabajo supera la capacidad estimada. Para cerrar la brecha:

1. **Priorizar dentro de cada objetivo:** primero las historias de prioridad Alta (59 de 96).
2. **Reducir el backend propio:** Supabase resuelve autenticación, permisos, tiempo real, almacenamiento y tareas programadas (documento 14), y la app del huésped reutiliza el proyecto Next.js.
3. **Reutilizar al máximo** el frontend existente de Kim.
4. **Revisar el avance cada semana** y mover historias de prioridad Media o Baja si hace falta.

Las horas son **estimaciones de referencia** para repartir la carga de forma pareja, no un compromiso.
