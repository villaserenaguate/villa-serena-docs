# 01 — Alcance del Proyecto

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** Reporte de Revisión — Documentación Inicial
> **Siguiente documento:** 02 — Definición de Roles

---

## Índice

1. [Propósito del documento](#1-propósito-del-documento)
2. [Decisiones generales](#2-decisiones-generales)
3. [Modelo de acceso al sistema](#3-modelo-de-acceso-al-sistema)
4. [Alcance de la versión 1](#4-alcance-de-la-versión-1)
5. [Fuera de alcance — Fase 2](#5-fuera-de-alcance--fase-2)
6. [Resumen](#6-resumen)
7. [Pendientes](#7-pendientes)

---

## 1. Propósito del documento

Este documento define **qué se construye en la versión 1** del PMS Villa Serena y **qué queda fuera** como trabajo futuro (Fase 2). Todo el trabajo posterior (historias de usuario, base de datos, permisos, arquitectura y plan de trabajo) debe respetar este alcance.

**Regla del equipo:** si una funcionalidad no aparece en la sección 4, no se construye en la versión 1. Cualquier cambio de alcance se discute con el equipo y se actualiza en este documento.

Cada funcionalidad tiene un **ID de alcance** (ej. `ALC-PUB-03`) para poder referenciarla desde las historias de usuario, los requisitos y las tareas de GitHub.

---

## 2. Decisiones generales

| # | Decisión | Detalle |
|---|---|---|
| D-01 | **Un solo hotel** | El sistema administra únicamente Villa Serena. No es multi-propiedad. |
| D-02 | **Moneda única: quetzales (GTQ)** | Todos los precios, cargos, pagos y reportes se manejan en Q. |
| D-03 | **Idioma: español** | Toda la interfaz (web y app) en español. |
| D-04 | **Pagos con Stripe en modo prueba** | Se demuestra el flujo completo (pago + confirmación por webhook) sin cobrar dinero real. Se usa tanto en la web pública como en la app. |
| D-05 | **Channel Manager simulado** | El enunciado pide *"preparar el sistema para conectarse"*. Se diseña e implementa la integración contra un canal simulado; la conexión real con Booking/Expedia requiere certificación como socio y queda en Fase 2. |
| D-06 | **Personal en web, huésped en app** | Recepción, Room Service, Mantenimiento/Limpieza y Administrador trabajan en la **web privada**. La **app Android** es exclusiva del huésped. |
| D-07 | **Reserva sin cuenta** | El cliente reserva desde la web pública sin crear cuenta ni contraseña (confirma RF-RES-008). |
| D-08 | **Acceso del huésped por código al correo (OTP)** | El huésped se identifica con su correo y un código de 6 dígitos que recibe por email. Sin contraseñas. Ver sección 3. |
| D-09 | **Sin categoría intermedia** | Solo existen dos niveles: **Versión 1** (se construye) y **Fase 2** (no se construye). |
| D-10 | **App Android con React Native + Expo** | La app del huésped se construye en TypeScript con React Native + Expo (SDK 54), se prueba con Expo Go y se entrega como APK generado con EAS Build. Ver documento 14. |

---

## 3. Modelo de acceso al sistema

### 3.1 Personal del hotel (web privada)

| Rol | Cómo entra | Dónde trabaja |
|---|---|---|
| Administrador | Correo + contraseña | Web privada |
| Recepcionista | Correo + contraseña | Web privada |
| Room Service | Correo + contraseña | Web privada |
| Mantenimiento/Limpieza | Correo + contraseña | Web privada |

Las cuentas del personal **las crea el Administrador**. El personal no se registra por su cuenta.

### 3.2 Cliente / Huésped (web pública + app)

**Flujo completo:**

1. **Reserva (web pública, sin cuenta):** el cliente busca fechas, elige habitación, ingresa sus datos y paga (Stripe en modo prueba).
2. **Confirmación:** recibe un correo con su **código de reserva** y el **enlace de descarga de la app**.
3. **Acceso (app o web pública):** escribe su correo → recibe un **código de 6 dígitos (OTP)** → entra sin contraseña.
4. **Durante la estadía (app):** ve sus reservas, pide room service, solicita limpieza o artículos, consulta amenidades, ve su cuenta, paga y hace check-out.
5. **Después del check-out:** la estadía queda en **solo lectura** (historial). Ya no permite pedidos, solicitudes ni pagos.

**Ventajas del modelo OTP:**

- Sin contraseñas que olvidar ni pantallas de "recuperar contraseña".
- El correo queda verificado.
- Una sola identidad para la web pública y la app; el huésped ve **todas** sus reservas e historial de estadías.
- Supabase Auth lo incluye de forma nativa.

**Requisito técnico derivado:** configurar un proveedor de correo (SMTP) para enviar los códigos y confirmaciones sin los límites del correo por defecto de Supabase.

> Las reservas **solo se crean en la web pública** (o por Recepción). Reservar desde la app queda en Fase 2.

---

## 4. Alcance de la versión 1

La columna **Origen** indica de dónde viene la funcionalidad: historia de usuario existente (HU), enunciado del ingeniero (ENUN) o decisión del equipo durante esta revisión (NUEVO).

### A. Web pública — Motor de reservas (Cliente)

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-PUB-01 | Página de inicio con información del hotel | ENUN |
| ALC-PUB-02 | Catálogo de habitaciones: fotos, descripción, capacidad y precio | ENUN, HU-HUE-01 |
| ALC-PUB-03 | Calendario de disponibilidad y búsqueda por fechas y número de huéspedes | ENUN, HU-HUE-01 |
| ALC-PUB-04 | Flujo de reserva sin cuenta (datos del huésped principal + confirmación) | ENUN, HU-HUE-01 |
| ALC-PUB-05 | Cálculo del precio total con la tarifa dinámica vigente (temporada + fin de semana) | ENUN |
| ALC-PUB-06 | Pago en línea con Stripe (modo prueba), confirmado por webhook | HU-HUE-01, RF-PAG-003/004 |
| ALC-PUB-07 | Correo de confirmación con código de reserva y enlace de descarga de la app | HU-HUE-01 |
| ALC-PUB-08 | Consultar y cancelar "mi reserva" (acceso por OTP) | NUEVO |
| ALC-PUB-09 | Check-in anticipado simplificado: confirmar datos, subir foto del documento (JPG/PDF) y registrar peticiones especiales | HU-HUE-02 (recortada) |

### B. Web privada — Recepción

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-REC-01 | Registrar huéspedes y huéspedes adicionales de una reserva | HU-REC-01, 18 |
| ALC-REC-02 | Crear, modificar y cancelar reservas, con revalidación de disponibilidad | HU-REC-02, 07, 08 |
| ALC-REC-03 | Consultar disponibilidad por fechas | HU-REC-03 |
| ALC-REC-04 | Asignar habitación a una reserva, sin traslapes | HU-REC-04 |
| ALC-REC-05 | Realizar check-in y check-out | HU-REC-05, 06 |
| ALC-REC-06 | Registrar pagos, servicios adicionales y consultar la cuenta del huésped | HU-REC-09, 10, 11 |
| ALC-REC-07 | Generar comprobante de pago descargable (PDF) | HU-REC-12 |
| ALC-REC-08 | Buscar reservas (nombre, DPI/pasaporte, código, fecha) y consultar historial del huésped | HU-REC-13, 17 |
| ALC-REC-09 | Vista del día: check-ins, check-outs, reservas pendientes y habitaciones disponibles | HU-REC-19 |
| ALC-REC-10 | Consultar y actualizar el estado de las habitaciones | HU-REC-14, 20 |
| ALC-REC-11 | Registrar solicitudes de huéspedes y enviarlas al área responsable | HU-REC-15, 16 |
| ALC-REC-12 | **Calendario Gantt:** habitaciones × días, reservas con color por estado, ver detalle y crear reserva desde el calendario | ENUN |
| ALC-REC-13 | Ver la tarifa aplicada al crear o modificar una reserva | ENUN |
| ALC-REC-14 | Notificación en pantalla (tiempo real) de nuevas solicitudes | NUEVO |

### C. Web privada — Administrador

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-ADM-01 | Gestión de personal: crear, editar y desactivar empleados | RF-ADM-003 |
| ALC-ADM-02 | Asignar rol a cada empleado (los permisos por rol se definen en el documento 09 — Matriz de Permisos) | RF-ADM-003 |
| ALC-ADM-03 | Turnos: definir turnos y asignarlos a empleados | RF-ADM-003 |
| ALC-ADM-04 | Inventario: productos, stock, entradas y salidas, alerta de stock mínimo | RF-ADM-004 |
| ALC-ADM-05 | Atender reportes de faltantes y registrar reposición de insumos | HU-LIM-10 |
| ALC-ADM-06 | Catálogo de habitaciones y tipos de habitación (fotos, capacidad, precio base) | NUEVO |
| ALC-ADM-07 | Catálogo del menú de Room Service (incluye reactivar ítems agotados) | HU-RS-07 |
| ALC-ADM-08 | Catálogo de amenidades del hotel (para la app) | ENUN |
| ALC-ADM-09 | **Tarifas dinámicas:** precio base por tipo de habitación, temporadas por rango de fechas y ajuste de fin de semana | ENUN, RF-ADM-001 |
| ALC-ADM-10 | Indicadores: ocupación, ingresos y reservas por canal | RF-ADM-002 |
| ALC-ADM-11 | Gestión de órdenes de mantenimiento: asignar técnico, cerrar y cancelar (rol de "encargado") | RN-MAN-003, RF-MAN-004 |

### D. Channel Manager (preparación)

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-CM-01 | Documento de diseño de la integración: adaptador por canal y sincronización de disponibilidad, tarifas e inventario | ENUN |
| ALC-CM-02 | Campo "canal de origen" en cada reserva (Directo web, Recepción, Booking, Expedia) | ENUN |
| ALC-CM-03 | API para recibir reservas externas (con validación de disponibilidad y sin duplicados) | ENUN |
| ALC-CM-04 | Canal simulado que envía reservas de prueba a la API | ENUN |

### E. App móvil Android — Huésped

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-APP-01 | Acceso por correo + código OTP | NUEVO (D-08) |
| ALC-APP-02 | Ver detalles de la estadía: fechas, habitación, huéspedes, estado | ENUN |
| ALC-APP-03 | Pedir Room Service desde el menú, con notas y alergias | ENUN, HU-HUE-03 |
| ALC-APP-04 | Seguimiento en vivo del pedido (Nuevo → En preparación → En camino → Entregado) | HU-HUE-03 |
| ALC-APP-05 | Solicitar limpieza y artículos (toallas, almohadas, papel, etc.) | ENUN, HU-HUE-03 |
| ALC-APP-06 | Directorio de amenidades: descripción, horario, ubicación y datos del Wi-Fi | ENUN |
| ALC-APP-07 | Ver mi cuenta: consumos, pagos y saldo pendiente | HU-HUE-05 |
| ALC-APP-08 | Pagar el saldo (Stripe en modo prueba) y hacer check-out digital | HU-HUE-05 (recortada) |
| ALC-APP-09 | Estadía finalizada en modo solo lectura (historial) | NUEVO |

**Efectos del check-out digital (ALC-APP-08):**

1. La reserva pasa a `FINALIZADA` y la cuenta del huésped se cierra.
2. La habitación pasa a `LIBRE` + `SUCIA` (o `FUERA_DE_SERVICIO` si tiene una incidencia que impide su uso).
3. Las solicitudes pendientes se cancelan.
4. La estadía queda en solo lectura en la app.

El detalle completo está en el documento 07 — Estados, sección 11.

Recepción puede seguir haciendo el check-out de forma tradicional (ALC-REC-05).

### F. Web privada — Room Service

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-RS-01 | Cola de pedidos pendientes, ordenada por antigüedad | HU-RS-01 |
| ALC-RS-02 | Detalle del pedido (ítems, cantidades, notas, huésped, habitación y piso) | HU-RS-02 |
| ALC-RS-03 | Registrar pedido telefónico | HU-RS-03 |
| ALC-RS-04 | Actualizar estado del pedido sin saltos | HU-RS-04 |
| ALC-RS-05 | Cancelar pedido con motivo obligatorio | HU-RS-05 |
| ALC-RS-06 | Consultar menú y disponibilidad | HU-RS-06 |
| ALC-RS-07 | Marcar ítem como agotado | HU-RS-07 |
| ALC-RS-08 | Cargo automático del pedido a la cuenta del huésped | HU-RS-08 |
| ALC-RS-09 | Historial de pedidos con filtros y tiempo total de entrega | HU-RS-09 |
| ALC-RS-10 | Notificación en pantalla (tiempo real) de nuevo pedido | HU-RS-10 |

### G. Web privada — Mantenimiento/Limpieza

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-MYL-01 | Habitaciones pendientes de limpieza, con prioridad y motivo | HU-LIM-01, 11 |
| ALC-MYL-02 | Cambiar estado de limpieza y registrar finalización (fecha y hora) | HU-LIM-02, 03 |
| ALC-MYL-03 | Ver y atender solicitudes de limpieza y artículos; confirmar entrega | HU-LIM-04, 05, 06, 15 |
| ALC-MYL-04 | Consultar información de la habitación y registrar observaciones | HU-LIM-12, 13 |
| ALC-MYL-05 | Registrar insumos usados, descontándolos del inventario | HU-LIM-09 |
| ALC-MYL-06 | Reportar faltantes de insumos | HU-LIM-10 |
| ALC-MYL-07 | Reportar daños, que generan una incidencia de mantenimiento | HU-LIM-07 |
| ALC-MYL-08 | Registrar objetos olvidados y su devolución | HU-LIM-08 |
| ALC-MYL-09 | Ver órdenes de mantenimiento asignadas, iniciarlas y resolverlas con descripción de la solución | HU-MAN-01, 02, 03 |
| ALC-MYL-10 | Registrar repuestos usados, descontándolos del inventario | RF-MAN-011 |
| ALC-MYL-11 | Historial de servicios de limpieza y mantenimiento realizados | HU-LIM-14, HU-MAN-04 |

### H. Transversal — Infraestructura, datos y calidad

| ID | Funcionalidad | Origen |
|---|---|---|
| ALC-TRA-01 | Autenticación: personal con correo + contraseña; huésped con OTP | RNF-SEC-001, D-08 |
| ALC-TRA-02 | 5 roles con permisos aplicados en la base de datos | RNF-SEC-003 |
| ALC-TRA-03 | Historial de cambios de estado con fecha, hora y responsable | RN-MAN-007, HU-RS-04 |
| ALC-TRA-04 | Actualizaciones en tiempo real (pedidos, solicitudes, estados de habitación) | HU-HUE-03, HU-RS-10 |
| ALC-TRA-05 | Envío de correos transaccionales (confirmación de reserva, OTP, comprobantes) | HU-HUE-01 |
| ALC-TRA-06 | Despliegue en la nube: web con URL pública y app como APK instalable | ENUN |
| ALC-TRA-07 | CI/CD: pull requests con revisión, pruebas automáticas y despliegue automático | Reparto del equipo |
| ALC-TRA-08 | Backups de la base de datos con restauración probada | RNF-DATA-004 |
| ALC-TRA-09 | Monitoreo básico de errores y caídas | RNF-OBS-003 |

---

## 5. Fuera de alcance — Fase 2

Estas funcionalidades **no se construyen** en la versión 1. Se documentan como trabajo futuro en la entrega final.

| # | Funcionalidad | Origen | Motivo |
|---|---|---|---|
| F2-01 | Firma digital de términos, llave digital o QR de acceso a la habitación | HU-HUE-02 | Requiere integración con cerraduras y validez legal |
| F2-02 | Domótica (temperatura, luces, cortinas), apertura por NFC/Bluetooth, Wi-Fi automático | HU-HUE-04 | Requiere hardware IoT |
| F2-03 | Reservar turnos en spa, gimnasio o canchas con límite de aforo | HU-HUE-04 | Módulo adicional, no pedido por el enunciado |
| F2-04 | Chat en tiempo real con recepción | HU-HUE-03 | Complejidad alta; las solicitudes cubren la necesidad |
| F2-05 | Notificaciones push en la app | Propuesta anterior | Las actualizaciones en vivo dentro de la app cubren la necesidad |
| F2-06 | Crear reservas desde la app | NUEVO | El enunciado indica que la app se usa después de confirmar la reserva |
| F2-07 | Versión iOS de la app | ENUN | El enunciado pide solo Android |
| F2-08 | Facturación electrónica FEL (SAT Guatemala) | HU-HUE-05, RF-PAG-006 | Integración con el sistema fiscal |
| F2-09 | Programa de fidelidad y pago con puntos | HU-HUE-05 | Módulo completo adicional |
| F2-10 | Conexión real con Booking o Expedia | ENUN | Requiere certificación como socio |
| F2-11 | Mapeo de tipos de habitación del hotel ↔ tipos de cada canal | Propuesta anterior | Solo necesario con conexión real |
| F2-12 | Gantt: arrastrar para mover o extender reservas | Propuesta anterior | El Gantt de ver y crear cubre el enunciado |
| F2-13 | Tarifas por nivel de ocupación | Propuesta anterior | Temporadas + fin de semana cubren "tarifas dinámicas" |
| F2-14 | Indicadores avanzados: RevPAR, ADR, gráficas históricas | RF-ADM-002 | Los indicadores básicos cubren la necesidad |
| F2-15 | Bitácora de auditoría general | Propuesta anterior | El historial de cambios de estado (ALC-TRA-03) cubre lo requerido |
| F2-16 | Registro de activos, mantenimiento preventivo recurrente e indicadores técnicos | RF-MAN-009, 010, 012 | No es necesario para la operación básica |
| F2-17 | Monitoreo avanzado: dashboards y alertas de latencia, CPU y RAM | RNF-OBS-002 | El monitoreo básico es suficiente para la versión 1 |
| F2-18 | Multi-hotel, multi-idioma y multi-moneda | NUEVO | Ver decisiones D-01, D-02 y D-03 |

---

## 6. Resumen

| Componente | Funcionalidades en versión 1 |
|---|---|
| A. Web pública (Cliente) | 9 |
| B. Recepción | 14 |
| C. Administrador | 11 |
| D. Channel Manager | 4 |
| E. App Android (Huésped) | 9 |
| F. Room Service | 10 |
| G. Mantenimiento/Limpieza | 11 |
| H. Transversal | 9 |
| **Total versión 1** | **77** |
| **Fase 2 (fuera de alcance)** | **18** |

---

## 7. Pendientes

- [ ] **Confirmar la fecha de entrega final.** Afecta el plan de trabajo, no el alcance aprobado.
- [ ] **Ubicar el frontend ya terminado** (repositorio o carpeta) para verificar qué pantallas de este alcance ya existen y cuáles faltan.
