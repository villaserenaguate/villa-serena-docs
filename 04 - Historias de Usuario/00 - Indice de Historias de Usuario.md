# 04 — Historias de Usuario: Índice (versión 3)

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** 📝 Para revisión del equipo
> **Fecha:** 30 de septiembre de 2026
> **Basado en:** Documentación V3 / 01 — Alcance del Proyecto (versión 3)
> **Reemplaza a:** 04 — Historias de Usuario, versión 2 (101 historias, carpeta "Documentación Definitiva")

---

## 1. Archivos

| Archivo | Rol / actor | Prefijo | Historias | Nivel 1 | Nivel 2 |
|---|---|---|---|---|---|
| HU - Cliente y Huesped.md | Cliente/Huésped | HU-HUE | 18 | 17 | 1 |
| HU - Recepcionista.md | Recepcionista | HU-REC | 17 | 16 | 1 |
| HU - Room Service.md | Room Service | HU-RS | 7 | 7 | 0 |
| HU - Mantenimiento y Limpieza.md | Mantenimiento/Limpieza | HU-MYL | 8 | 8 | 0 |
| HU - Administrador.md | Administrador | HU-ADM | 13 | 10 | 3 |
| HU - Channel Manager.md | Canal externo, Administrador, Recepción | HU-CM | 3 | 3 | 0 |
| HU - Personal del Hotel.md | Todo el personal | HU-EMP | 2 | 2 | 0 |
| **Total** | | | **68** | **63** | **5** |

**Comparación:** la versión 2 tenía 101 historias. La versión 3 tiene 68: se descartaron 23, se unieron varias historias en otras más completas (12 menos) y se agregaron 2 nuevas (acceso del personal).

**Numeración:** es nueva en la versión 3. Cada historia indica a qué historia(s) de la versión 2 reemplaza ("Reemplaza a"), y la tabla de la sección 4 lo resume. Dentro de la versión 3, un ID nunca se reutiliza.

**Cambios en la plantilla (documento 03):**
- El campo **Prioridad** se reemplaza por **Nivel** (1 = obligatorio para el 10 de octubre; 2 = si da tiempo).
- El campo **Reglas relacionadas** se agregó el 1 de octubre a partir del documento 10 — Reglas de Negocio (versión 3), sección 20. Los valores concretos siguen escritos en los criterios.
- Se agrega el campo **Reemplaza a**, para rastrear la versión 2.

---

## 2. Historias por plataforma

| Plataforma | Historias |
|---|---|
| Web pública | HU-HUE-01 a 07 |
| Web privada | HU-REC, HU-RS, HU-MYL, HU-ADM, HU-EMP, HU-CM-02 y 03 |
| App Android | HU-HUE-08 a 18 |
| API | HU-CM-01 |

---

## 3. Estados usados en las historias

Las historias usan las **etiquetas**. Los códigos y transiciones están en el documento 07 — Estados (versión 3).

| Elemento | Estados |
|---|---|
| Reserva | `Pendiente de pago` · `Confirmada` · `En estadía` · `Finalizada` · `Cancelada` (ya no existe `No-show`: el huésped que no llega se cancela) |
| Habitación: ocupación | `Libre` · `Ocupada` |
| Habitación: condición | `Limpia` · `Sucia` · `En limpieza` · `Fuera de servicio` |
| Pedido de Room Service | `Nuevo` · `En preparación` · `En camino` · `Entregado` · `Cancelado` |
| Solicitud del huésped | `Pendiente` · `En proceso` · `Atendida` · `Cancelada` |
| Incidencia de mantenimiento | `Reportada` · `En proceso` · `Resuelta` (antes tenía 6 estados) |
| Cuenta | `Abierta` · `Cerrada` |
| Cargo de la cuenta | `Vigente` · `Anulado` |
| Pago | `Pendiente` · `Aprobado` · `Fallido` · `Reembolsado` |
| Método de pago (no es un estado) | `Stripe` (web y app) · `Canal` (reservas de Booking o Expedia) · `Efectivo`, `Tarjeta` u `Otro` (registrados en Recepción) |
| Factura | `Emitida` (ya no existe `Anulada`) |
| Ítem del menú | `Disponible` · `Agotado` |
| Empleado | `Activo` · `Inactivo` |
| Tipo de habitación, habitación, ítem del menú y amenidad (y, en Nivel 2, turno y producto) | `Activo` · `Inactivo` (lo desactiva el Administrador; es aparte de los estados anteriores) |

---

## 4. Lista completa

| ID | Historia | Plataforma | Alcance | Nivel | Tamaño | Reemplaza a (v2) |
|---|---|---|---|---|---|---|
| HU-HUE-01 | Ver la información del hotel | Web pública | ALC-PUB-01 | 1 | S | HU-HUE-01 |
| HU-HUE-02 | Ver el catálogo de habitaciones | Web pública | ALC-PUB-02 | 1 | S | HU-HUE-02 |
| HU-HUE-03 | Buscar disponibilidad por fechas y huéspedes | Web pública | ALC-PUB-03 | 1 | M | HU-HUE-03 |
| HU-HUE-04 | Ver el precio total de mi estadía | Web pública | ALC-PUB-05 | 1 | M | HU-HUE-04 |
| HU-HUE-05 | Ingresar mis datos y confirmar la reserva | Web pública | ALC-PUB-04 | 1 | M | HU-HUE-05 |
| HU-HUE-06 | Pagar mi reserva en línea | Web pública | ALC-PUB-06 | 1 | L | HU-HUE-06 |
| HU-HUE-07 | Recibir la confirmación por correo | Web pública | ALC-PUB-07, ALC-TRA-05 | 1 | S | HU-HUE-07 |
| HU-HUE-08 | Entrar a la app con mi correo y un código | App | ALC-APP-01, ALC-TRA-01, ALC-TRA-05 | 1 | M | HU-HUE-08 |
| HU-HUE-09 | Ver mis reservas y el detalle de mi estadía | App | ALC-APP-02 | 1 | M | HU-HUE-09, HU-HUE-12 |
| HU-HUE-10 | Pedir room service | App | ALC-APP-03 | 1 | M | HU-HUE-13 |
| HU-HUE-11 | Seguir mi pedido en vivo | App | ALC-APP-04 | 1 | M | HU-HUE-14 |
| HU-HUE-12 | Solicitar limpieza | App | ALC-APP-05 | 1 | S | HU-HUE-15 |
| HU-HUE-13 | Solicitar artículos | App | ALC-APP-05 | 1 | S | HU-HUE-16 |
| HU-HUE-14 | Ver el estado de mis solicitudes | App | ALC-APP-05 | 1 | S | HU-HUE-17 |
| HU-HUE-15 | Ver mi cuenta | App | ALC-APP-07 | 1 | S | HU-HUE-19 |
| HU-HUE-16 | Pagar mi saldo y hacer check-out desde la app | App | ALC-APP-08 | 1 | L | HU-HUE-20, HU-HUE-21 |
| HU-HUE-17 | Recibir notificaciones en mi teléfono | App | ALC-APP-10 | 1 | M | HU-HUE-22 |
| HU-HUE-18 | Ver las amenidades y el Wi-Fi | App | ALC-APP-06 | 2 | S | HU-HUE-18 |
| HU-REC-01 | Registrar un huésped | Web privada | ALC-REC-01 | 1 | S | HU-REC-01 |
| HU-REC-02 | Registrar huéspedes adicionales | Web privada | ALC-REC-01 | 1 | S | HU-REC-02 |
| HU-REC-03 | Consultar disponibilidad | Web privada | ALC-REC-03 | 1 | S | HU-REC-04 |
| HU-REC-04 | Crear una reserva | Web privada | ALC-REC-02, ALC-REC-13 | 1 | M | HU-REC-05 |
| HU-REC-05 | Cancelar una reserva | Web privada | ALC-REC-02 | 1 | M | HU-REC-07 |
| HU-REC-06 | Buscar reservas | Web privada | ALC-REC-08 | 1 | M | HU-REC-08, HU-REC-10 |
| HU-REC-07 | Asignar o cambiar la habitación antes del check-in | Web privada | ALC-REC-04 | 1 | S | HU-REC-09 |
| HU-REC-08 | Ver el calendario Gantt | Web privada | ALC-REC-12 | 1 | L | HU-REC-11 |
| HU-REC-09 | Crear una reserva seleccionando días en el Gantt | Web privada | ALC-REC-12b | 2 | M | HU-REC-12 |
| HU-REC-10 | Ver el estado de las habitaciones | Web privada | ALC-REC-10 | 1 | M | HU-REC-13 |
| HU-REC-11 | Marcar una habitación libre como sucia | Web privada | ALC-REC-10 | 1 | S | HU-REC-14 |
| HU-REC-12 | Realizar el check-in | Web privada | ALC-REC-05 | 1 | M | HU-REC-15 |
| HU-REC-13 | Consultar la cuenta y agregar cargos | Web privada | ALC-REC-06 | 1 | M | HU-REC-17, HU-REC-19 |
| HU-REC-14 | Realizar el check-out con pago único | Web privada | ALC-REC-05, ALC-REC-06 | 1 | M | HU-REC-16, HU-REC-18 |
| HU-REC-15 | Emitir la factura | Web privada | ALC-REC-15, ALC-TRA-05 | 1 | M | HU-REC-24 |
| HU-REC-16 | Imprimir la factura | Web privada | ALC-REC-16 | 1 | S | HU-REC-25 |
| HU-REC-17 | Reportar un daño en una habitación | Web privada | ALC-MYL-07 | 1 | S | HU-REC-22 |
| HU-RS-01 | Ver la cola de pedidos activos | Web privada | ALC-RS-01 | 1 | M | HU-RS-01 |
| HU-RS-02 | Ver el detalle de un pedido | Web privada | ALC-RS-02 | 1 | S | HU-RS-02 |
| HU-RS-03 | Avanzar el estado de un pedido | Web privada | ALC-RS-04 | 1 | S | HU-RS-04 |
| HU-RS-04 | Cancelar un pedido | Web privada | ALC-RS-05 | 1 | S | HU-RS-05 |
| HU-RS-05 | Consultar el menú y marcar ítems agotados | Web privada | ALC-RS-06 | 1 | S | HU-RS-06, HU-RS-07 |
| HU-RS-06 | Generar el cargo del pedido entregado | Web privada | ALC-RS-08 | 1 | S | HU-RS-08 |
| HU-RS-07 | Recibir aviso de pedido nuevo | Web privada | ALC-RS-10, ALC-TRA-04 | 1 | S | HU-RS-10 |
| HU-MYL-01 | Ver las habitaciones pendientes de limpieza | Web privada | ALC-MYL-01 | 1 | M | HU-MYL-01 |
| HU-MYL-02 | Iniciar o interrumpir la limpieza de una habitación | Web privada | ALC-MYL-02 | 1 | S | HU-MYL-02 |
| HU-MYL-03 | Marcar la limpieza como terminada | Web privada | ALC-MYL-02 | 1 | S | HU-MYL-03 |
| HU-MYL-04 | Ver y tomar solicitudes de huéspedes | Web privada | ALC-MYL-03, ALC-TRA-04 | 1 | M | HU-MYL-06 |
| HU-MYL-05 | Atender una solicitud | Web privada | ALC-MYL-03 | 1 | S | HU-MYL-07 |
| HU-MYL-06 | Reportar un daño | Web privada | ALC-MYL-07 | 1 | M | HU-MYL-10 |
| HU-MYL-07 | Ver las incidencias y tomar una | Web privada | ALC-MYL-09 | 1 | S | HU-MYL-13, HU-MYL-14 |
| HU-MYL-08 | Resolver una incidencia | Web privada | ALC-MYL-09 | 1 | S | HU-MYL-15 |
| HU-ADM-01 | Crear un empleado | Web privada | ALC-ADM-01, ALC-ADM-02 | 1 | M | HU-ADM-01 |
| HU-ADM-02 | Editar, desactivar o restablecer la contraseña de un empleado | Web privada | ALC-ADM-01 | 1 | M | HU-ADM-02 |
| HU-ADM-03 | Gestionar tipos de habitación | Web privada | ALC-ADM-06 | 1 | M | HU-ADM-05 |
| HU-ADM-04 | Gestionar habitaciones | Web privada | ALC-ADM-06 | 1 | S | HU-ADM-06 |
| HU-ADM-05 | Gestionar el menú de Room Service | Web privada | ALC-ADM-07 | 1 | M | HU-ADM-07 |
| HU-ADM-06 | Gestionar temporadas | Web privada | ALC-ADM-09 | 1 | M | HU-ADM-09 |
| HU-ADM-07 | Configurar el ajuste de fin de semana | Web privada | ALC-ADM-09 | 1 | S | HU-ADM-10 |
| HU-ADM-08 | Configurar los datos del hotel y de facturación | Web privada | ALC-ADM-12 | 1 | M | HU-ADM-19, HU-ADM-21 |
| HU-ADM-09 | Ver los indicadores básicos | Web privada | ALC-ADM-10 | 1 | M | HU-ADM-18 |
| HU-ADM-10 | Consultar las incidencias de mantenimiento | Web privada | ALC-ADM-11 | 1 | S | HU-ADM-16, HU-ADM-17 |
| HU-ADM-11 | Gestionar amenidades y el Wi-Fi | Web privada | ALC-ADM-08 | 2 | S | HU-ADM-08 |
| HU-ADM-12 | Definir y asignar turnos | Web privada | ALC-ADM-03 | 2 | M | HU-ADM-03, HU-ADM-04 |
| HU-ADM-13 | Gestionar el inventario | Web privada | ALC-ADM-04 | 2 | M | HU-ADM-12, HU-ADM-13, HU-ADM-14 |
| HU-CM-01 | Recibir una reserva de un canal externo | API | ALC-CM-03 | 1 | M | HU-CM-02 |
| HU-CM-02 | Ver el canal de origen de las reservas | Web privada | ALC-CM-02 | 1 | S | HU-CM-04 |
| HU-CM-03 | Enviar reservas de prueba con el canal simulado | Web privada | ALC-CM-04 | 1 | M | HU-CM-05 |
| HU-EMP-01 | Iniciar y cerrar sesión en la web privada | Web privada | ALC-TRA-01 | 1 | M | Nueva |
| HU-EMP-02 | Cambiar mi contraseña temporal | Web privada | ALC-TRA-01, ALC-ADM-01 | 1 | S | Nueva |

---

## 5. Trazabilidad: alcance → historias

Toda funcionalidad de Nivel 1 y Nivel 2 del alcance está cubierta por una historia o por una tarea técnica.

| Alcance | Historias |
|---|---|
| ALC-ADM-01 | HU-ADM-01, HU-ADM-02, HU-EMP-02 |
| ALC-ADM-02 | HU-ADM-01 |
| ALC-ADM-03 | HU-ADM-12 |
| ALC-ADM-04 | HU-ADM-13 |
| ALC-ADM-06 | HU-ADM-03, HU-ADM-04 |
| ALC-ADM-07 | HU-ADM-05 |
| ALC-ADM-08 | HU-ADM-11 |
| ALC-ADM-09 | HU-ADM-06, HU-ADM-07 |
| ALC-ADM-10 | HU-ADM-09 |
| ALC-ADM-11 | HU-ADM-10 |
| ALC-ADM-12 | HU-ADM-08 |
| ALC-APP-01 | HU-HUE-08 |
| ALC-APP-02 | HU-HUE-09 |
| ALC-APP-03 | HU-HUE-10 |
| ALC-APP-04 | HU-HUE-11 |
| ALC-APP-05 | HU-HUE-12, HU-HUE-13, HU-HUE-14 |
| ALC-APP-06 | HU-HUE-18 |
| ALC-APP-07 | HU-HUE-15 |
| ALC-APP-08 | HU-HUE-16 |
| ALC-APP-10 | HU-HUE-17 |
| ALC-CM-02 | HU-CM-02 |
| ALC-CM-03 | HU-CM-01 |
| ALC-CM-04 | HU-CM-03 |
| ALC-MYL-01 | HU-MYL-01 |
| ALC-MYL-02 | HU-MYL-02, HU-MYL-03 |
| ALC-MYL-03 | HU-MYL-04, HU-MYL-05 |
| ALC-MYL-07 | HU-REC-17, HU-MYL-06 |
| ALC-MYL-09 | HU-MYL-07, HU-MYL-08 |
| ALC-PUB-01 | HU-HUE-01 |
| ALC-PUB-02 | HU-HUE-02 |
| ALC-PUB-03 | HU-HUE-03 |
| ALC-PUB-04 | HU-HUE-05 |
| ALC-PUB-05 | HU-HUE-04 |
| ALC-PUB-06 | HU-HUE-06 |
| ALC-PUB-07 | HU-HUE-07 |
| ALC-REC-01 | HU-REC-01, HU-REC-02 |
| ALC-REC-02 | HU-REC-04, HU-REC-05 |
| ALC-REC-03 | HU-REC-03 |
| ALC-REC-04 | HU-REC-07 |
| ALC-REC-05 | HU-REC-12, HU-REC-14 |
| ALC-REC-06 | HU-REC-13, HU-REC-14 |
| ALC-REC-08 | HU-REC-06 |
| ALC-REC-10 | HU-REC-10, HU-REC-11 |
| ALC-REC-12 | HU-REC-08 |
| ALC-REC-12b | HU-REC-09 |
| ALC-REC-13 | HU-REC-04 |
| ALC-REC-15 | HU-REC-15 |
| ALC-REC-16 | HU-REC-16 |
| ALC-RS-01 | HU-RS-01 |
| ALC-RS-02 | HU-RS-02 |
| ALC-RS-04 | HU-RS-03 |
| ALC-RS-05 | HU-RS-04 |
| ALC-RS-06 | HU-RS-05 |
| ALC-RS-08 | HU-RS-06 |
| ALC-RS-10 | HU-RS-07 |
| ALC-TRA-01 | HU-HUE-08, HU-EMP-01, HU-EMP-02 |
| ALC-TRA-04 | HU-RS-07, HU-MYL-04 |
| ALC-TRA-05 | HU-HUE-07, HU-HUE-08, HU-REC-15 |

**Funcionalidades cubiertas por tareas técnicas (no son historias):**

| Alcance | Tarea técnica |
|---|---|
| ALC-CM-01 | Documento de diseño breve de la integración con canales |
| ALC-TRA-02 | Permisos por rol en Spring Security (documento 09, versión 3) |
| ALC-TRA-03 | Historial de cambios de estado |
| ALC-TRA-04 | Configuración del tiempo real (WebSocket) para los 4 eventos |
| ALC-TRA-09 / 09b | Grafana en local (y logs y alerta, si da tiempo) |
| ALC-TRA-10 | Entorno local con Docker |
| ALC-TRA-01 (parte) | Creación del primer Administrador al arrancar, con variables de entorno |
| Varias | **Datos iniciales (Flyway):** canales y sus claves (HU-CM-03), catálogo de artículos con su cantidad máxima (HU-HUE-13), datos del hotel, datos fiscales, serie y número inicial del correlativo (HU-ADM-08), y usuarios de prueba de cada rol, con **dos Administradores** para no perder el acceso si uno olvida su contraseña (V-04, V-06) |

---

## 6. Historias de la versión 2 que se descartaron

| Historia v2 | Tema | Motivo (alcance v3) |
|---|---|---|
| HU-HUE-10 | Cancelar mi reserva desde la web | ALC-PUB-08 descartado: cancela Recepción |
| HU-HUE-11 | Check-in anticipado | ALC-PUB-09 descartado |
| HU-REC-03 | Historial del huésped | Fuera del alcance de ALC-REC-08 |
| HU-REC-06 | Modificar una reserva | D-15: no se modifican reservas |
| HU-REC-20 | Comprobante de pago en PDF | ALC-REC-07 descartado: la factura lo cubre |
| HU-REC-21 | Registrar solicitudes desde Recepción | ALC-REC-11 descartado |
| HU-REC-23 | Avisos a Recepción en tiempo real | ALC-REC-14 descartado; el estado de habitaciones sí se actualiza en vivo |
| HU-REC-26 | Anular una factura | D-18: sin anulación |
| HU-RS-03 | Pedido telefónico | ALC-RS-03 descartado |
| HU-RS-09 | Historial de pedidos | ALC-RS-09 descartado |
| HU-MYL-04 | Información de la habitación | ALC-MYL-04 descartado |
| HU-MYL-05 | Observaciones | ALC-MYL-04 descartado |
| HU-MYL-08 | Insumos usados | ALC-MYL-05 descartado |
| HU-MYL-09 | Reportar faltantes | ALC-MYL-06 descartado |
| HU-MYL-11, HU-MYL-12 | Objetos olvidados y su devolución | ALC-MYL-08 descartado |
| HU-MYL-16 | Repuestos | ALC-MYL-10 descartado |
| HU-MYL-17 | Historial de servicios | ALC-MYL-11 descartado |
| HU-ADM-11 | Vista previa de tarifas | Simplificación de ALC-ADM-09 |
| HU-ADM-15 | Atender faltantes | ALC-ADM-05 descartado |
| HU-ADM-20 | Reasignar trabajo en curso | D-19: sin asignación ni reasignación |
| HU-CM-01 | Administrar canales y claves | Las claves se cargan en la configuración inicial |
| HU-CM-03 | Cancelación desde el canal | El canal solo envía reservas |

---

## 7. Decisiones tomadas al escribir las historias

Detalles que no estaban definidos en el alcance y se resolvieron así. **Revísenlos**; cualquiera se puede cambiar.

| Historia | Decisión |
|---|---|
| HU-HUE-13 | El catálogo de artículos para el huésped (toallas, almohadas…) y su cantidad máxima se cargan en los **datos iniciales**; no hay pantalla para administrarlo |
| HU-HUE-14, HU-HUE-15, HU-MYL-07 | No están entre los 4 eventos en tiempo real: se actualizan al abrir la pantalla o con un botón "Actualizar". El huésped se entera de la solicitud atendida por la notificación push |
| HU-REC-02 | El tope de huéspedes adicionales es el número de huéspedes de la reserva |
| HU-REC-05 | Si Stripe rechaza el reembolso, la reserva no se cancela (Recepción puede volver a intentarlo) |
| HU-REC-13 | Solo se anulan cargos adicionales; el cargo por alojamiento no se anula |
| HU-REC-14 | El check-out es una sola operación: si falla la factura, no se registra el pago ni cambia ningún estado |
| HU-REC-05 | No existe `No-show`: el huésped que no llega se cancela con motivo "No se presentó" (sin reembolso). No se envía correo de cancelación |
| HU-REC-12 | El check-in se permite desde la fecha de entrada hasta el día anterior a la salida (llegadas después de medianoche) |
| HU-REC-14 | Salida tarde: sin proceso automático ni cargo extra; Recepción hace el check-out cuando el huésped baje |
| HU-REC-15 | La factura muestra el total con "IVA incluido", sin desglose de impuestos (cierra RN-FAC-003) |
| HU-ADM-02 | El Administrador no puede desactivarse ni cambiarse el rol a sí mismo (con eso siempre queda uno activo) |
| HU-ADM-08 | Las horas de check-in (15:00) y check-out (12:00) son fijas, no configurables |
| Todas | El Administrador usa solo sus pantallas y el canal simulado; no usa las de Recepción ni las de piso |
| HU-ADM-08 | Los datos fiscales, la serie y el número inicial vienen en los datos iniciales; la serie y el número inicial no se editan en pantalla. No hay bloqueo del check-out por "faltan datos de facturación". La política de cancelación se muestra a partir de la regla de 48 horas, no se edita libremente |
| HU-ADM-09 | Ingresos = suma de los pagos `Aprobado` del rango (un pago reembolsado deja de sumar); las reservas por canal cuentan todas las creadas en el rango, incluidas las canceladas; la ocupación es la de hoy |
| HU-REC-01, HU-HUE-05, HU-CM-01 | El huésped principal tiene los mismos 6 datos obligatorios en todos los orígenes (nombre, correo, teléfono, nacionalidad, tipo y número de documento) y se identifica por su correo: si el correo ya existe, se usa ese perfil sin cambiar sus datos |
| HU-REC-06 | "Llegan hoy" solo muestra las entradas de hoy; quien llega después de medianoche se busca por nombre o código (limitación aceptada) |
| HU-HUE-08 | La sesión de la app dura mientras se use: refresh token de 7 días que se renueva en cada uso |
| HU-REC-05 | Recepción solo cancela reservas `Confirmada`; las `Pendiente de pago` se cancelan solas a los 30 minutos |
| HU-REC-05 | Las reservas de canal no se cancelan, ni siquiera si el huésped no llega: quedan `Confirmada` (limitación aceptada) |
| HU-ADM-02 | Un empleado desactivado con sesión abierta puede seguir hasta que venza su token de acceso (máx. 15 min) |
| HU-HUE-06 | Un intento de pago rechazado no cambia el pago (sigue `Pendiente`); pasa a `Fallido` solo cuando vence la sesión de Stripe (documento 07, observación O-01) |
| HU-HUE-16 | Limitación aceptada: no se controla que el huésped abra dos sesiones de pago del saldo a la vez (documento 07, sección 5.3) |
| HU-REC-12, HU-HUE-15 | El huésped puede ver su cuenta en la app en cualquier estado de la reserva; el check-in solo habilita room service y solicitudes (documento 09, OP-01) |
| HU-RS-06, HU-REC-17 | Cargo de room service: se ve al abrir o recargar la cuenta (no es tiempo real). Incidencia con habitación ocupada: solo si el daño impide el uso (documento 10, OBS-04 y OBS-05) |
| Room Service | No hay horario: el servicio está siempre disponible (documento 10, OBS-01) |
| Fotos | Solo JPG o PNG de hasta 5 MB, como validación técnica (documento 14) |
| HU-EMP-02 | Contraseña de al menos 8 caracteres, con una letra y un número |

**Descartado:** qué pasa con el trabajo en curso de un empleado que se desactiva. No es necesario para el funcionamiento ni para la presentación; no se construye nada para ese caso.

**Quitado tras el reporte de vacíos (30 sep):** validación de cupo al desactivar una habitación, validación del "último Administrador", registro de desactivaciones y restablecimientos de contraseña, aviso de reservas asignadas al quedar `Fuera de servicio`, indicador "Sin conexión" y sonido del aviso de pedido nuevo (pasa a Nivel 2), bloqueo del check-out por "faltan datos de facturación" y edición de la serie y el número inicial de la factura.

**Quitado tras la revisión de inconsistencias (30 sep):** recalcular el precio si la tarifa cambia durante la reserva (HU-HUE-04), información fija si la configuración no carga (HU-HUE-01), bloqueo del correo de un empleado como huésped (HU-REC-01), revisión del estado del empleado en cada acción (HU-ADM-02) y cancelación manual de reservas `Pendiente de pago` (HU-REC-05).
