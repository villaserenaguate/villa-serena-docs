# 04 — Historias de Usuario: Índice

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo (con ajustes del documento 07 — Estados)
> **Fecha:** 24 de septiembre de 2026
> **Documento anterior:** 03 — Plantilla de Historias de Usuario
> **Siguiente documento:** 07 — Estados

Este conjunto de archivos resuelve los puntos **4** (historias del Administrador), **5** (historias de los diferenciadores del enunciado) y **6** (corrección de las historias existentes) de la lista de problemas.

---

## 1. Archivos

| Archivo | Rol / actor | Prefijo | Historias | Tipo de cambio |
|---|---|---|---|---|
| HU - Cliente y Huesped.md | Cliente/Huésped | HU-HUE | 21 | Reescrito: separado en web pública y app; recortado al alcance |
| HU - Recepcionista.md | Recepcionista | HU-REC | 23 | Corregido: criterios formales; agregados Gantt, daños y avisos |
| HU - Room Service.md | Room Service | HU-RS | 10 | Corregido: se conserva la numeración; criterios completados |
| HU - Mantenimiento y Limpieza.md | Mantenimiento/Limpieza | HU-MYL | 17 | Unificado: antiguos archivos de Limpieza (15) y Mantenimiento (4) |
| HU - Administrador.md | Administrador | HU-ADM | 20 | **Nuevo** |
| HU - Channel Manager.md | Administrador, Recepción, Canal | HU-CM | 5 | **Nuevo** |
| **Total** | | | **96** | |

---

## 2. Historias por plataforma

| Plataforma | Responsable principal | Historias |
|---|---|---|
| Web pública | Kim (frontend) | HU-HUE-01 a 11 |
| Web privada | Kim (frontend) | HU-REC, HU-RS, HU-MYL, HU-ADM, HU-CM-01, 04, 05 |
| App Android | Carlos (móvil) | HU-HUE-08, 09, 11 a 21 |
| API | Backend | HU-CM-02, 03 |

HU-HUE-08, 09 y 11 aplican a la web pública **y** a la app.

---

## 3. Estados usados en las historias

Los nombres de estado usados en todas las historias son los siguientes. Sus códigos, transiciones permitidas, responsables y efectos están definidos en el documento **07 — Estados**.

| Elemento | Estados |
|---|---|
| Reserva | `Pendiente de pago` · `Confirmada` · `En estadía` · `Finalizada` · `Cancelada` · `No-show` |
| Habitación: ocupación | `Libre` · `Ocupada` |
| Habitación: condición | `Limpia` · `Sucia` · `En limpieza` · `Fuera de servicio` |
| Pedido de Room Service | `Nuevo` · `En preparación` · `En camino` · `Entregado` · `Cancelado` |
| Solicitud de huésped | `Pendiente` · `En proceso` · `Atendida` · `Cancelada` |
| Incidencia / orden de mantenimiento | `Reportada` · `Asignada` · `En proceso` · `Resuelta` · `Cerrada` · `Cancelada` |
| Reporte de faltante | `Pendiente` · `Atendido` |
| Objeto olvidado | `Registrado` · `Devuelto` · `Desechado` |
| Empleado | `Activo` · `Inactivo` |

---

## 4. Correspondencia con las historias antiguas

### HU - Huésped (antiguo)

| Antigua | Nueva | Qué pasó |
|---|---|---|
| HU-01 Reserva en línea | HU-HUE-03, 04, 05, 06, 07 | Dividida en búsqueda, precio, datos, pago y confirmación |
| HU-02 Check-in web anticipado | HU-HUE-11 | Recortada. Firma digital y QR → Fase 2 (F2-01) |
| HU-03 Portal de servicios | HU-HUE-13 a 17 | Dividida. Chat → Fase 2 (F2-04) |
| HU-04 Confort, IoT y reservas de amenidades | HU-HUE-18 | Solo se conserva el directorio de amenidades. IoT, NFC, Wi-Fi automático y turnos de spa → Fase 2 (F2-02, F2-03) |
| HU-05 Gastos, facturación y check-out | HU-HUE-19, 20 | Facturación electrónica y puntos → Fase 2 (F2-08, F2-09) |

### HU - Recepcionista (antiguo)

| Antigua | Nueva | | Antigua | Nueva |
|---|---|---|---|---|
| 1 Registrar huésped | HU-REC-01 | | 11 Consultar cuenta | HU-REC-19 |
| 2 Realizar reserva | HU-REC-05 | | 12 Generar comprobante | HU-REC-20 |
| 3 Consultar disponibilidad | HU-REC-04 | | 13 Consultar reservas | HU-REC-08 |
| 4 Asignar habitación | HU-REC-09 | | 14 Estado de habitaciones | HU-REC-13 |
| 5 Check-in | HU-REC-15 | | 15 Registrar solicitudes | HU-REC-21 |
| 6 Check-out | HU-REC-16 | | 16 Notificar a empleados | HU-REC-21 (unida) |
| 7 Modificar reserva | HU-REC-06 | | 17 Historial del huésped | HU-REC-03 |
| 8 Cancelar reserva | HU-REC-07 | | 18 Huéspedes adicionales | HU-REC-02 |
| 9 Registrar pagos | HU-REC-18 | | 19 Reservas del día | HU-REC-10 |
| 10 Servicios adicionales | HU-REC-17 | | 20 Actualizar estado | HU-REC-14 |

**Nuevas:** HU-REC-11 (Gantt), HU-REC-12 (reservar desde el Gantt), HU-REC-22 (reportar daño), HU-REC-23 (avisos en tiempo real).

### HU - Room Service (antiguo)

HU-01 a HU-10 → HU-RS-01 a HU-RS-10 (misma numeración).

### HU - Limpieza y HU - Mantenimiento (antiguos)

| Antigua | Nueva | | Antigua | Nueva |
|---|---|---|---|---|
| LIM-01 Pendientes | HU-MYL-01 | | LIM-10 Falta de insumos | HU-MYL-09 |
| LIM-02 Cambiar estado | HU-MYL-02 | | LIM-11 Prioritarias | HU-MYL-01 (unida) |
| LIM-03 Limpieza terminada | HU-MYL-03 | | LIM-12 Info de habitación | HU-MYL-04 |
| LIM-04 Solicitudes de limpieza | HU-MYL-06 | | LIM-13 Observaciones | HU-MYL-05 |
| LIM-05 Solicitudes de artículos | HU-MYL-06 (unida) | | LIM-14 Historial | HU-MYL-17 |
| LIM-06 Estado de solicitudes | HU-MYL-07 | | LIM-15 Confirmar entrega | HU-MYL-07 (unida) |
| LIM-07 Reportar daños | HU-MYL-10 | | MAN-01 Ver incidencias | HU-MYL-13 |
| LIM-08 Objetos olvidados | HU-MYL-11 | | MAN-02 Atender incidencia | HU-MYL-14 |
| LIM-09 Productos utilizados | HU-MYL-08 | | MAN-03 Finalizar | HU-MYL-15 |
| | | | MAN-04 Historial | HU-MYL-17 (unida) |

**Nuevas:** HU-MYL-12 (devolución de objetos olvidados), HU-MYL-16 (repuestos).

---

## 5. Trazabilidad: alcance → historias

Toda funcionalidad del documento 01 está cubierta por al menos una historia o una tarea técnica.

| Alcance | Historias | | Alcance | Historias |
|---|---|---|---|---|
| ALC-PUB-01 | HUE-01, ADM-19 | | ALC-APP-01 | HUE-08 |
| ALC-PUB-02 | HUE-02 | | ALC-APP-02 | HUE-12 |
| ALC-PUB-03 | HUE-03 | | ALC-APP-03 | HUE-13 |
| ALC-PUB-04 | HUE-05 | | ALC-APP-04 | HUE-14 |
| ALC-PUB-05 | HUE-04 | | ALC-APP-05 | HUE-15, 16, 17 |
| ALC-PUB-06 | HUE-06 | | ALC-APP-06 | HUE-18 |
| ALC-PUB-07 | HUE-07 | | ALC-APP-07 | HUE-19 |
| ALC-PUB-08 | HUE-08, 09, 10 | | ALC-APP-08 | HUE-20 |
| ALC-PUB-09 | HUE-11 | | ALC-APP-09 | HUE-21 |
| ALC-REC-01 | REC-01, 02 | | ALC-RS-01 a 10 | RS-01 a 10 |
| ALC-REC-02 | REC-05, 06, 07 | | ALC-MYL-01 | MYL-01 |
| ALC-REC-03 | REC-04 | | ALC-MYL-02 | MYL-02, 03 |
| ALC-REC-04 | REC-09 | | ALC-MYL-03 | MYL-06, 07 |
| ALC-REC-05 | REC-15, 16 | | ALC-MYL-04 | MYL-04, 05 |
| ALC-REC-06 | REC-17, 18, 19 | | ALC-MYL-05 | MYL-08 |
| ALC-REC-07 | REC-20 | | ALC-MYL-06 | MYL-09 |
| ALC-REC-08 | REC-03, 08 | | ALC-MYL-07 | MYL-10 |
| ALC-REC-09 | REC-10 | | ALC-MYL-08 | MYL-11, 12 |
| ALC-REC-10 | REC-13, 14 | | ALC-MYL-09 | MYL-13, 14, 15 |
| ALC-REC-11 | REC-21, 22 | | ALC-MYL-10 | MYL-16 |
| ALC-REC-12 | REC-11, 12 | | ALC-MYL-11 | MYL-17 |
| ALC-REC-13 | REC-05 | | ALC-CM-01 | Entregable técnico de Arquitectura |
| ALC-REC-14 | REC-23 | | ALC-CM-02 | CM-04 |
| ALC-ADM-01, 02 | ADM-01, 02, 20 | | ALC-CM-03 | CM-01, 02, 03 |
| ALC-ADM-03 | ADM-03, 04 | | ALC-CM-04 | CM-05 |
| ALC-ADM-04 | ADM-12, 13, 14 | | ALC-TRA-01 | HUE-08, ADM-01 |
| ALC-ADM-05 | ADM-15 | | ALC-TRA-02 | Documento 09 — Matriz de Permisos |
| ALC-ADM-06 | ADM-05, 06 | | ALC-TRA-03 | Criterios en RS-04, MYL-03, 07, 14, 15, REC-14, ADM-17 |
| ALC-ADM-07 | ADM-07 | | ALC-TRA-04 | HUE-14, 17, RS-10, REC-23 |
| ALC-ADM-08 | ADM-08 | | ALC-TRA-05 | HUE-07 |
| ALC-ADM-09 | ADM-09, 10, 11 | | ALC-TRA-06 | Tarea técnica: Infraestructura (Josué) |
| ALC-ADM-10 | ADM-18 | | ALC-TRA-07 | Tarea técnica: CI/CD (Alex) |
| ALC-ADM-11 | ADM-16, 17 | | ALC-TRA-08 | Tarea técnica: Base de datos (Pablo y Hugo) |
| | | | ALC-TRA-09 | Tarea técnica: Infraestructura (Josué) |

---

## 6. Documentos relacionados

| Tema | Documento |
|---|---|
| Estados, transiciones y efectos automáticos | 07 — Estados |
| Inventario, turnos y personal | 08 — Inventario, Turnos y Personal |
| Qué acción exacta puede hacer cada rol | 09 — Matriz de Permisos |
| Reglas de negocio y parámetros (valores concretos) | 10 — Reglas de Negocio |
