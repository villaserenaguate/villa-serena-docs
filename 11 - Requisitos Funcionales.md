# 11 — Requisitos Funcionales

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** 📝 Para revisión del equipo
> **Fecha:** 24 de septiembre de 2026
> **Documento anterior:** 10 — Reglas de Negocio
> **Siguiente documento:** 12 — Casos de Uso
> **Reemplaza a:** "Requisitos Funcionales" (documentación inicial, borrador generado por IA)

---

## Índice

1. [Propósito y cómo leer este documento](#1-propósito-y-cómo-leer-este-documento)
2. [Reservas — RF-RES](#2-reservas--rf-res)
3. [Huéspedes — RF-HUE](#3-huéspedes--rf-hue)
4. [Habitaciones — RF-HAB](#4-habitaciones--rf-hab)
5. [Recepción: check-in y check-out — RF-REC](#5-recepción-check-in-y-check-out--rf-rec)
6. [Pagos y cuenta — RF-PAG](#6-pagos-y-cuenta--rf-pag)
7. [Tarifas — RF-TAR](#7-tarifas--rf-tar)
8. [Room Service — RF-RS](#8-room-service--rf-rs)
9. [Limpieza — RF-LIM](#9-limpieza--rf-lim)
10. [Solicitudes del huésped — RF-SOL](#10-solicitudes-del-huésped--rf-sol)
11. [Mantenimiento — RF-MAN](#11-mantenimiento--rf-man)
12. [Administración — RF-ADM](#12-administración--rf-adm)
13. [App del huésped — RF-APP](#13-app-del-huésped--rf-app)
14. [Channel Manager — RF-CM](#14-channel-manager--rf-cm)
15. [Notificaciones — RF-NOT](#15-notificaciones--rf-not)
16. [Seguridad y trazabilidad — RF-SEG](#16-seguridad-y-trazabilidad--rf-seg)
17. [Resumen](#17-resumen)

---

## 1. Propósito y cómo leer este documento

Un **requisito funcional** describe **qué debe hacer el sistema**. Las historias de usuario (documento 04) describen lo mismo desde el punto de vista de cada usuario; este documento lo agrupa **por módulo del sistema**, que es como lo construyen el backend y la base de datos.

**Columnas:**

| Columna | Significado |
|---|---|
| ID | Se conservan los IDs originales; los nuevos continúan la numeración |
| Estado | `V1` = se construye en la versión 1 · `Fase 2` = fuera de alcance (documento 01, sección 5) |
| Cambio | `=` sin cambios · `MOD` modificado · `NUEVO` agregado en esta revisión |
| HU | Historias de usuario que lo implementan (documento 04) |
| RN | Reglas de negocio que debe cumplir (documento 10) |

Todos los requisitos originales que decían "Propuesto" o no tenían estado quedaron resueltos: ahora son `V1` o `Fase 2`.

---

## 2. Reservas — RF-RES

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-RES-001 | El sistema debe permitir consultar la disponibilidad por fechas y número de huéspedes, excluyendo habitaciones con reservas que se traslapen o bloqueadas por mantenimiento. | V1 | = | HUE-03, REC-04 | RES-001, 002, 008 |
| RF-RES-002 | El sistema debe permitir crear reservas indicando huésped principal, fechas, número de huéspedes y tipo o habitación, revalidando la disponibilidad antes de guardar. | V1 | = | HUE-05, REC-05, REC-12 | RES-001, 005, 006, 007, 011 |
| RF-RES-003 | El sistema debe impedir que una habitación o cupo de un tipo quede asignado a reservas que se traslapen. | V1 | = | HUE-05, REC-05, REC-09 | RES-002, 008 |
| RF-RES-004 | El sistema debe permitir a Recepción modificar fechas, número de huéspedes o habitación, revalidando disponibilidad y recalculando el precio. | V1 | MOD | REC-06 | RES-003, 016, 017, TAR-007 |
| RF-RES-005 | El sistema debe permitir cancelar reservas con motivo, liberar la disponibilidad y aplicar la política de cancelación. | V1 | MOD | HUE-10, REC-07 | CAN-001 a 010 |
| RF-RES-006 | El sistema debe permitir buscar reservas por nombre, documento, código, fecha, estado y canal. | V1 | MOD | REC-08 | — |
| RF-RES-007 | El sistema debe mostrar a Recepción las llegadas, salidas, reservas pendientes de pago y el resumen de habitaciones del día. | V1 | = | REC-10 | — |
| RF-RES-008 | Un visitante debe poder reservar desde la web pública sin crear una cuenta. | V1 | = | HUE-05 | RES-011 |
| RF-RES-009 | El sistema debe cancelar automáticamente las reservas web no pagadas dentro del tiempo límite. | V1 | NUEVO | HUE-06 | RES-012 |
| RF-RES-010 | Toda reserva debe tener un código único y registrar su canal de origen. | V1 | NUEVO | HUE-05, REC-05, CM-04 | RES-009, 010 |
| RF-RES-011 | El sistema debe marcar automáticamente como no-show las reservas sin check-in al terminar el día de llegada. | V1 | NUEVO | — (proceso del sistema) | CAN-004, 005 |
| RF-RES-012 | El sistema debe mostrar un calendario Gantt de habitaciones por días con las reservas, su estado y su canal, y permitir crear reservas desde él. | V1 | NUEVO | REC-11, REC-12 | — |
| RF-RES-013 | El huésped debe poder consultar sus reservas y cancelar las que estén permitidas. | V1 | NUEVO | HUE-09, HUE-10 | RES-016, CAN-001 |
| RF-RES-014 | El sistema debe enviar un correo de confirmación con el código de reserva y el enlace de la app. | V1 | NUEVO | HUE-07 | — |

---

## 3. Huéspedes — RF-HUE

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-HUE-001 | El sistema debe registrar al huésped con nombre completo, tipo y número de documento, teléfono, correo y nacionalidad. | V1 | = | REC-01, HUE-05 | PER-006 |
| RF-HUE-002 | Una reserva debe poder asociarse a varios huéspedes (principal y adicionales), sin superar la capacidad. | V1 | = | REC-02, HUE-11 | RES-007, APP-004 |
| RF-HUE-003 | El sistema debe permitir consultar el historial de estadías, habitaciones, pagos y servicios de un huésped. | V1 | = | REC-03, HUE-21 | — |

---

## 4. Habitaciones — RF-HAB

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-HAB-001 | Los roles autorizados deben poder consultar la ocupación y la condición actual de cada habitación. | V1 | MOD | REC-13, MYL-04 | — |
| RF-HAB-002 | Recepción debe poder asignar una habitación disponible del tipo reservado a una reserva. | V1 | = | REC-09 | RES-013, HAB-001 |
| RF-HAB-003 | El sistema debe mantener dos dimensiones de estado por habitación: ocupación y condición (documento 07, sección 4). | V1 | MOD | REC-13 | HAB-004 |
| RF-HAB-004 | Los cambios de estado deben respetar las transiciones del documento 07 y los permisos del documento 09. | V1 | MOD | REC-14, MYL-02, MYL-03 | HAB-004, 005, 006 |
| RF-HAB-005 | Una habitación con una incidencia que impide su uso debe excluirse de la disponibilidad. | V1 | = | REC-22, MYL-10 | HAB-001, 002, 006 |
| RF-HAB-006 | La web pública debe mostrar el catálogo de tipos de habitación activos con fotos, descripción, capacidad y precio base. | V1 | NUEVO | HUE-02 | — |

---

## 5. Recepción: check-in y check-out — RF-REC

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-REC-001 | El sistema debe permitir el check-in: localizar la reserva, verificar datos, confirmar habitación, registrar fecha, hora y responsable, y actualizar estados. | V1 | = | REC-15 | RES-014 |
| RF-REC-002 | El sistema debe permitir el check-out verificando cargos, pagos y saldo, y ejecutar los efectos automáticos (cuenta, habitación, solicitudes). | V1 | MOD | REC-16, HUE-20 | RES-015, HAB-002, LIM-009 |
| RF-REC-003 | El huésped debe poder hacer un check-in anticipado: confirmar datos, registrar acompañantes, subir foto del documento y registrar peticiones especiales. Firma digital y llave digital quedan en Fase 2. | V1 | MOD (antes "Propuesto") | HUE-11 | APP-007, APP-009 |

---

## 6. Pagos y cuenta — RF-PAG

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-PAG-001 | Todo pago debe asociarse a una cuenta e incluir monto, método, fecha, referencia y responsable. | V1 | = | REC-18 | PAG-013, 014 |
| RF-PAG-002 | El sistema debe mostrar alojamiento, cargos adicionales, pagos, reembolsos y saldo de la cuenta. Los descuentos no existen en la versión 1. | V1 | MOD | REC-19, HUE-19 | PAG-012, 018 |
| RF-PAG-003 | El sistema debe integrarse con Stripe en modo prueba para pagos en línea. | V1 | MOD (se descarta PayPal) | HUE-06, HUE-20 | PAG-004, 005 |
| RF-PAG-004 | El estado definitivo de un pago en línea debe confirmarse mediante el webhook de Stripe, de forma idempotente. | V1 | = | HUE-06, HUE-20 | PAG-001, 002, 003 |
| RF-PAG-005 | El sistema debe generar comprobantes de pago en PDF con número correlativo. | V1 | = | REC-20 | PAG-015 |
| RF-PAG-006 | Facturación electrónica FEL. | Fase 2 | MOD (antes "Propuesto") | — | F2-08 |
| RF-PAG-007 | La cuenta del huésped debe crearse junto con la reserva, con el cargo por alojamiento. | V1 | NUEVO | HUE-05, REC-05 | PAG-009 |
| RF-PAG-008 | Recepción debe poder agregar cargos por servicios adicionales y anularlos con motivo. | V1 | NUEVO | REC-17 | PAG-010, 011 |
| RF-PAG-009 | El sistema debe procesar reembolsos totales y parciales según la política de cancelación, y reembolsar los saldos a favor antes del check-out. | V1 | NUEVO | HUE-10, REC-07, REC-06, REC-16 | CAN-002 a 008, PAG-016 |
| RF-PAG-010 | El huésped debe poder pagar su saldo desde la app antes del check-out. | V1 | NUEVO | HUE-20 | APP-008 |

---

## 7. Tarifas — RF-TAR

Detalla el antiguo RF-ADM-001.

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-TAR-001 | El Administrador debe poder definir el precio base por noche de cada tipo de habitación. | V1 | NUEVO | ADM-05 | TAR-004, 009 |
| RF-TAR-002 | El Administrador debe poder definir temporadas con rango de fechas y porcentaje de ajuste. | V1 | NUEVO | ADM-09 | TAR-003 |
| RF-TAR-003 | El Administrador debe poder definir un ajuste de fin de semana por tipo de habitación. | V1 | NUEVO | ADM-10 | TAR-002 |
| RF-TAR-004 | El sistema debe calcular en el servidor el precio de cada noche y el total, y fijarlo al crear la reserva. | V1 | NUEVO | HUE-04, REC-05 | TAR-001, 005, 006, 007, 008 |
| RF-TAR-005 | El Administrador debe poder ver una vista previa mensual de los precios calculados. | V1 | NUEVO | ADM-11 | TAR-001 |
| RF-TAR-006 | Tarifas por nivel de ocupación. | Fase 2 | NUEVO | — | F2-13 |

---

## 8. Room Service — RF-RS

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-RS-001 | El sistema debe mostrar la cola de pedidos activos ordenada por antigüedad. | V1 | = | RS-01 | — |
| RF-RS-002 | El sistema debe mostrar el detalle del pedido: habitación, piso, huésped, ítems, cantidades y notas. | V1 | = | RS-02 | SEG-002 |
| RF-RS-003 | Room Service debe poder registrar pedidos telefónicos para habitaciones ocupadas. | V1 | = | RS-03 | RS-006 |
| RF-RS-004 | Flujo del pedido `NUEVO → EN_PREPARACION → EN_CAMINO → ENTREGADO`, sin saltos. | V1 | = | RS-04 | RS-001, 002 |
| RF-RS-005 | La cancelación de un pedido exige motivo y no permite reactivarlo. | V1 | = | RS-05 | RS-003, 004 |
| RF-RS-006 | Debe poder consultarse el menú y marcarse ítems como agotados; solo el Admin los reactiva. | V1 | = | RS-06, RS-07, ADM-07 | RS-005, 008 |
| RF-RS-007 | El importe del pedido se carga a la cuenta al entregarse, una sola vez. | V1 | MOD | RS-08 | RS-007 |
| RF-RS-008 | Debe poder consultarse el historial por fecha, habitación, estado y turno. | V1 | = | RS-09 | — |
| RF-RS-009 | El personal debe recibir un aviso en tiempo real de cada pedido nuevo. | V1 | MOD (tecnología definida: tiempo real) | RS-10 | — |
| RF-RS-010 | El huésped debe poder hacer pedidos desde la app. | V1 | NUEVO | HUE-13 | RS-005, 006 |
| RF-RS-011 | El huésped debe poder seguir el estado de su pedido en tiempo real. | V1 | NUEVO | HUE-14 | RG-EST-07 |

---

## 9. Limpieza — RF-LIM

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-LIM-001 | Debe existir un listado de habitaciones que requieren limpieza. | V1 | = | MYL-01 | LIM-002 |
| RF-LIM-002 | Flujo de limpieza `SUCIA → EN_LIMPIEZA → LIMPIA`, con posibilidad de interrupción. | V1 | MOD | MYL-02, MYL-03 | LIM-001 |
| RF-LIM-003 | La finalización debe registrar fecha, hora y responsable. | V1 | = | MYL-03 | MAN-007 |
| RF-LIM-004 | El personal debe poder gestionar solicitudes de limpieza y de artículos. | V1 | = | MYL-06, MYL-07 | LIM-011 |
| RF-LIM-005 | Flujo de solicitudes `PENDIENTE → EN_PROCESO → ATENDIDA`. | V1 | = | MYL-07 | LIM-003 |
| RF-LIM-006 | El personal debe poder reportar daños, que generan una incidencia. | V1 | MOD | MYL-10 | MAN-001 |
| RF-LIM-007 | Debe poder registrarse objeto, habitación, fecha y hora de los objetos olvidados. | V1 | = | MYL-11 | LIM-012 |
| RF-LIM-008 | Debe poder asociarse el consumo de insumos a la limpieza, descontándolo del inventario. | V1 | MOD | MYL-08 | INV-001, 002, 006 |
| RF-LIM-009 | Debe poder reportarse la necesidad de reposición de insumos. | V1 | = | MYL-09 | INV-009 |
| RF-LIM-010 | Las habitaciones prioritarias deben distinguirse y ordenarse primero. | V1 | = | MYL-01 | LIM-010 |
| RF-LIM-011 | Las observaciones deben asociarse a la habitación con fecha y responsable. | V1 | = | MYL-05 | — |
| RF-LIM-012 | Debe poder consultarse el historial de servicios realizados. | V1 | = | MYL-17 | — |
| RF-LIM-013 | Debe poder registrarse la devolución o desecho de un objeto olvidado. | V1 | NUEVO | MYL-12 | LIM-012 |
| RF-LIM-014 | Al atender una solicitud de artículos consumibles se debe descontar el inventario. | V1 | NUEVO | MYL-07 | INV-007, 008 |

---

## 10. Solicitudes del huésped — RF-SOL

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-SOL-001 | Recepción y el huésped (app) deben poder crear solicitudes de limpieza o artículos asociadas a una reserva en estadía. | V1 | MOD | REC-21, HUE-15, HUE-16 | LIM-005, 006, 007 |
| RF-SOL-002 | Las solicitudes deben llegar al área de Limpieza; los daños se reportan como incidencias. | V1 | MOD | REC-21, REC-22 | LIM-011 |
| RF-SOL-003 | Debe existir seguimiento del estado de cada solicitud hasta su atención, visible para Recepción y el huésped. | V1 | = | REC-21, HUE-17 | LIM-008, 009 |

---

## 11. Mantenimiento — RF-MAN

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-MAN-001 | Debe existir una bandeja de incidencias reportadas para el Administrador. | V1 | MOD | ADM-16 | MAN-001 |
| RF-MAN-002 | Una incidencia se convierte en orden de trabajo una sola vez, al ser asignada. | V1 | MOD | ADM-16 | MAN-002 |
| RF-MAN-003 | Cualquier empleado de Mantenimiento/Limpieza, Recepción o el Admin puede reportar una avería. | V1 | MOD | MYL-10, REC-22 | MAN-001 |
| RF-MAN-004 | El **Administrador** asigna técnico, prioridad y fecha compromiso. | V1 | MOD ("encargado" → Admin) | ADM-16 | MAN-003 |
| RF-MAN-005 | Cada técnico debe poder consultar sus órdenes asignadas. | V1 | = | MYL-13 | MAN-009 |
| RF-MAN-006 | Ciclo `REPORTADA → ASIGNADA → EN_PROCESO → RESUELTA → CERRADA`. | V1 | MOD | MYL-14, MYL-15, ADM-17 | MAN-002 |
| RF-MAN-007 | Toda cancelación, reasignación o devolución registra justificación. | V1 | MOD | ADM-16, ADM-17 | MAN-004, 011 |
| RF-MAN-008 | Una habitación no puede volver a venderse mientras exista una orden abierta que impida su uso. | V1 | = | ADM-17 | MAN-005, 006 |
| RF-MAN-009 | Mantenimiento preventivo recurrente. | Fase 2 | — | — | F2-16 |
| RF-MAN-010 | Registro de activos e historial de intervenciones. | Fase 2 | — | — | F2-16 |
| RF-MAN-011 | Los repuestos usados se descuentan del inventario y se asocian a la orden. | V1 | = | MYL-16 | MAN-008, INV-006 |
| RF-MAN-012 | Indicadores operativos de mantenimiento. | Fase 2 | — | — | F2-16 |

---

## 12. Administración — RF-ADM

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-ADM-001 | Debe permitirse modificar precios según temporadas y fin de semana. Detallado en RF-TAR. | V1 | MOD | ADM-09, ADM-10 | TAR-001 a 009 |
| RF-ADM-002 | Debe permitirse consultar ocupación, ingresos y reservas por canal. RevPAR y ADR quedan en Fase 2. | V1 | MOD | ADM-18 | PAG-017 |
| RF-ADM-003 | Debe poder gestionarse empleados, roles, áreas y turnos. | V1 | MOD | ADM-01 a 04 | PER-001 a 007, TUR-001 a 004 |
| RF-ADM-004 | Debe existir control de productos, stock, movimientos, alertas y reportes de faltante. | V1 | MOD | ADM-12 a 15 | INV-001 a 009 |
| RF-ADM-005 | El Admin debe gestionar los catálogos: tipos de habitación, habitaciones, menú, amenidades y artículos para huésped. | V1 | NUEVO | ADM-05 a 08, ADM-12 | HAB-007 |
| RF-ADM-006 | El Admin debe configurar los datos generales del hotel: contacto, horarios, Wi-Fi y texto de la política de cancelación. | V1 | NUEVO | ADM-19 | PAR-01, PAR-02 |
| RF-ADM-007 | La web pública debe mostrar la información general del hotel configurada por el Admin. | V1 | NUEVO | HUE-01 | PAR-01, PAR-02 |
| RF-ADM-008 | El Admin debe poder reasignar el trabajo en curso (solicitudes, limpiezas y órdenes) a otro empleado. | V1 | NUEVO | ADM-20 | PER-003 |

---

## 13. App del huésped — RF-APP

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-APP-001 | El huésped debe acceder con correo y código OTP, sin contraseña. | V1 | NUEVO | HUE-08 | APP-001, 002, 003 |
| RF-APP-002 | El huésped debe ver los detalles de su estadía. | V1 | NUEVO | HUE-12 | — |
| RF-APP-003 | El huésped debe consultar el directorio de amenidades y los datos del Wi-Fi. | V1 | NUEVO | HUE-18 | — |
| RF-APP-004 | Las funciones de estadía solo deben estar disponibles con la reserva `EN_ESTADIA`. | V1 | NUEVO | HUE-13 a 20 | APP-005 |
| RF-APP-005 | Las estadías finalizadas deben quedar en solo lectura. | V1 | NUEVO | HUE-21 | APP-006 |

---

## 14. Channel Manager — RF-CM

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-CM-001 | El Admin debe registrar canales y generar sus claves de API. | V1 | NUEVO | CM-01 | CM-001, 002 |
| RF-CM-002 | El sistema debe exponer una API documentada (OpenAPI) para recibir reservas de canales externos. | V1 | NUEVO | CM-02 | CM-003, 004, 006, TAR-010, PAG-008 |
| RF-CM-003 | La API debe permitir cancelar reservas del propio canal. | V1 | NUEVO | CM-03 | CM-005 |
| RF-CM-004 | El canal de origen debe ser visible en reservas, búsquedas y el Gantt. | V1 | NUEVO | CM-04 | RES-010 |
| RF-CM-005 | Debe existir un canal simulado que use la API real. | V1 | NUEVO | CM-05 | CM-007 |
| RF-CM-006 | Debe existir un documento de diseño de la integración (adaptadores y sincronización de disponibilidad, tarifas e inventario). | V1 | NUEVO | — (entregable de Arquitectura) | — |
| RF-CM-007 | Conexión real con Booking y Expedia. | Fase 2 | NUEVO | — | F2-10 |

---

## 15. Notificaciones — RF-NOT

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-NOT-001 | Los cambios en pedidos, solicitudes, habitaciones y reservas deben reflejarse en tiempo real en las pantallas que los muestran. | V1 | NUEVO | REC-23, RS-10, HUE-14, HUE-17, MYL-06 | RG-EST-07 |
| RF-NOT-002 | El sistema debe enviar correos de confirmación, OTP, cancelación, check-out y comprobantes. | V1 | NUEVO | HUE-07, HUE-08, REC-07, REC-16 | CAN-010 |
| RF-NOT-003 | Notificaciones push en la app. | Fase 2 | NUEVO | — | F2-05 |

---

## 16. Seguridad y trazabilidad — RF-SEG

| ID | Requisito | Estado | Cambio | HU | RN |
|---|---|---|---|---|---|
| RF-SEG-001 | El personal debe autenticarse con correo y contraseña; las cuentas las crea el Admin. | V1 | NUEVO | ADM-01 | PER-001, R-ROL-03 |
| RF-SEG-002 | Cada operación debe autorizarse según la matriz de permisos (documento 09), en la base de datos y el backend. | V1 | NUEVO | Todas | SEG-001 a 008 |
| RF-SEG-003 | Todo cambio de estado debe quedar en un historial con actor, fecha, hora y motivo. | V1 | NUEVO | RS-04, MYL-03, MYL-07, MYL-14, MYL-15, REC-14, ADM-17 | RG-EST-02 |

---

## 17. Resumen

| Grupo | V1 | Fase 2 | Total |
|---|---|---|---|
| RF-RES | 14 | 0 | 14 |
| RF-HUE | 3 | 0 | 3 |
| RF-HAB | 6 | 0 | 6 |
| RF-REC | 3 | 0 | 3 |
| RF-PAG | 9 | 1 | 10 |
| RF-TAR | 5 | 1 | 6 |
| RF-RS | 11 | 0 | 11 |
| RF-LIM | 14 | 0 | 14 |
| RF-SOL | 3 | 0 | 3 |
| RF-MAN | 9 | 3 | 12 |
| RF-ADM | 8 | 0 | 8 |
| RF-APP | 5 | 0 | 5 |
| RF-CM | 6 | 1 | 7 |
| RF-NOT | 2 | 1 | 3 |
| RF-SEG | 3 | 0 | 3 |
| **Total** | **101** | **7** | **108** |

**Cambios respecto al borrador original (70 requisitos):**

- Se conservan todos los IDs originales.
- Todos los requisitos tienen estado; los "Propuesto" se resolvieron (RF-REC-003 → V1 recortado; RF-PAG-006 → Fase 2).
- "Encargado" se reemplaza por Administrador (RF-MAN-004).
- Se agregan 38 requisitos nuevos, sobre todo en tarifas, app, Channel Manager, notificaciones y seguridad (3 de ellos en la revisión de consistencia: RF-HAB-006, RF-ADM-007 y RF-ADM-008).
- Cada requisito indica qué historias lo implementan y qué reglas debe cumplir.
