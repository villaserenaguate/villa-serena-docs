# 10 — Reglas de Negocio

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** 09 — Matriz de Permisos
> **Siguiente documento:** 11 — Requisitos Funcionales
> **Reemplaza a:** "Reglas de Negocio" (documentación inicial)

---

## Índice

1. [Propósito y cómo leer este documento](#1-propósito-y-cómo-leer-este-documento)
2. [Parámetros del sistema](#2-parámetros-del-sistema)
3. [Reservas — RN-RES](#3-reservas--rn-res)
4. [Habitaciones — RN-HAB](#4-habitaciones--rn-hab)
5. [Tarifas — RN-TAR](#5-tarifas--rn-tar)
6. [Pagos y cuenta — RN-PAG](#6-pagos-y-cuenta--rn-pag)
7. [Cancelación y no-show — RN-CAN](#7-cancelación-y-no-show--rn-can)
8. [Room Service — RN-RS](#8-room-service--rn-rs)
9. [Limpieza y solicitudes — RN-LIM](#9-limpieza-y-solicitudes--rn-lim)
10. [Mantenimiento — RN-MAN](#10-mantenimiento--rn-man)
11. [Inventario — RN-INV](#11-inventario--rn-inv)
12. [Turnos — RN-TUR](#12-turnos--rn-tur)
13. [Personal y roles — RN-PER y R-ROL](#13-personal-y-roles--rn-per-y-r-rol)
14. [Seguridad — RN-SEG](#14-seguridad--rn-seg)
15. [App y acceso del huésped — RN-APP](#15-app-y-acceso-del-huésped--rn-app)
16. [Channel Manager — RN-CM](#16-channel-manager--rn-cm)
17. [Reglas generales de estados — RG-EST](#17-reglas-generales-de-estados--rg-est)
18. [Cambios respecto a las reglas originales](#18-cambios-respecto-a-las-reglas-originales)

---

## 1. Propósito y cómo leer este documento

Este documento **consolida todas las reglas de negocio** del PMS Villa Serena en un solo lugar. Reúne:

- Las **25 reglas originales**, corregidas y con los códigos de estado del documento 07.
- Las reglas aprobadas en los documentos **02** (roles), **07** (estados), **08** (inventario, turnos y personal) y **09** (permisos).
- Las **reglas nuevas** con los 20 valores aprobados por el equipo.

**Columna "Origen":**

| Valor | Significado |
|---|---|
| `ORIG` | Regla de la documentación inicial (se conserva su ID) |
| `ORIG*` | Regla original modificada |
| `D01`, `D02`, `D04`, `D07`, `D08`, `D09` | Aprobada en ese documento (D04 = historias de usuario) |
| `REV` | Agregada en la revisión de consistencia |
| `NUEVA` | Agregada en este documento |

Una regla de negocio es una **condición que el sistema debe cumplir siempre**. Cada regla debe tener al menos una **prueba automática** (CI/CD).

---

## 2. Parámetros del sistema

Valores aprobados por el equipo. Los marcados como **configurables** los puede cambiar el Administrador (HU-ADM-19); los demás se definen como **constantes** en el código o en la configuración del servidor.

| # | Parámetro | Valor | Configurable |
|---|---|---|---|
| PAR-01 | Hora de check-in | 15:00 | ✅ Admin |
| PAR-02 | Hora de check-out | 12:00 | ✅ Admin |
| PAR-03 | Estadía mínima | 1 noche | — |
| PAR-04 | Estadía máxima | 30 noches | — |
| PAR-05 | Anticipación máxima para reservar | 365 días | — |
| PAR-06 | Tiempo límite de pago de reservas web | 30 minutos | — |
| PAR-07 | Anticipación para cancelar sin costo | 48 horas antes de la hora de check-in | — |
| PAR-08 | Penalidad por cancelación tardía o no-show | Primera noche | — |
| PAR-09 | Momento del no-show | 23:59 del día de llegada | — |
| PAR-10 | Moneda | Quetzales (GTQ) | — |
| PAR-11 | Impuestos | Incluidos en el precio (IVA 12 % + INGUAT 10 %), sin desglose | — |
| PAR-12 | Decimales de precios | 2 | — |
| PAR-13 | Noches de fin de semana | Viernes y sábado | — |
| PAR-14 | Código OTP | 6 dígitos, vence en 10 minutos | — |
| PAR-15 | Límite de intentos de verificación del OTP | Límite por dirección IP configurado en Supabase Auth (por defecto 30 cada 5 minutos) | — |
| PAR-16 | Apertura del check-in anticipado | 24 horas antes de la llegada | — |
| PAR-17 | Ventana del check-out desde la app | Desde las 00:00 del día de salida hasta la hora de check-out | — |
| PAR-18 | Horario de Room Service | 24 horas | — |
| PAR-19 | Solicitudes de limpieza activas por habitación | Máximo 1 | — |
| PAR-20 | Archivos subidos | Máximo 5 MB; JPG, PNG o PDF | — |
| PAR-21 | Zona horaria del hotel | America/Guatemala (UTC-6). La base de datos guarda las fechas en UTC y las convierte a esta zona para mostrar y para calcular "hoy", el no-show, las 48 horas de cancelación y la ventana de check-out | — |

---

## 3. Reservas — RN-RES

| ID | Regla | Origen |
|---|---|---|
| RN-RES-001 | No se confirma una reserva sin disponibilidad válida. | ORIG |
| RN-RES-002 | Una habitación (o cupo de un tipo de habitación) no puede tener reservas que se traslapen. Ocupan disponibilidad las reservas `PENDIENTE_PAGO`, `CONFIRMADA` y `EN_ESTADIA`. | ORIG* |
| RN-RES-003 | Cambiar fechas, tipo o habitación obliga a revalidar la disponibilidad. | ORIG |
| RN-RES-004 | Una cancelación, un no-show o el vencimiento del tiempo de pago liberan la disponibilidad. | ORIG* |
| RN-RES-005 | La estadía mínima es de 1 noche y la máxima de 30 noches (PAR-03, PAR-04). | NUEVA |
| RN-RES-006 | No se permiten fechas pasadas ni reservas con más de 365 días de anticipación (PAR-05). | NUEVA |
| RN-RES-007 | El número de huéspedes no puede superar la capacidad del tipo de habitación. | NUEVA |
| RN-RES-008 | La disponibilidad de un tipo en una noche = habitaciones activas del tipo que no estén bloqueadas por mantenimiento − reservas de ese tipo que ocupan esa noche (con o sin habitación asignada). | NUEVA |
| RN-RES-009 | Toda reserva tiene un código único, no secuencial, que se comunica al huésped. | NUEVA |
| RN-RES-010 | Toda reserva registra su canal de origen: `DIRECTO_WEB`, `RECEPCION`, `BOOKING` o `EXPEDIA`. | D07 |
| RN-RES-011 | Las reservas web nacen en `PENDIENTE_PAGO`; las de Recepción y de canales, en `CONFIRMADA`. | D07 |
| RN-RES-012 | Una reserva `PENDIENTE_PAGO` se cancela automáticamente si el pago no se confirma en 30 minutos (PAR-06). | NUEVA |
| RN-RES-013 | La habitación asignada debe ser del tipo reservado y no tener reservas que se traslapen. | D04 (HU-REC-09) |
| RN-RES-014 | El check-in solo se hace el día de llegada, con habitación asignada en estado `LIBRE` + `LIMPIA`. La hora de check-in es referencial: se permite antes si la habitación ya está limpia. | D07 |
| RN-RES-015 | El check-out requiere saldo igual a cero. | D07 |
| RN-RES-016 | El huésped no modifica su reserva; solo puede cancelarla. Las modificaciones las hace Recepción. | D09 |
| RN-RES-017 | En una reserva `EN_ESTADIA` solo se pueden modificar la fecha de salida y la habitación. | D04 (HU-REC-06) |

---

## 4. Habitaciones — RN-HAB

| ID | Regla | Origen |
|---|---|---|
| RN-HAB-001 | Una habitación `FUERA_DE_SERVICIO`, o con una orden abierta que impide su uso, no se ofrece ni se asigna. | ORIG* |
| RN-HAB-002 | Una habitación ocupada no pasa a `FUERA_DE_SERVICIO` mientras el huésped esté alojado; queda con el indicador "Incidencia pendiente" y pasa a `FUERA_DE_SERVICIO` al hacer el check-out. | ORIG* |
| RN-HAB-003 | Una habitación liberada por mantenimiento pasa a `SUCIA` y debe limpiarse antes de estar disponible. | ORIG |
| RN-HAB-004 | La ocupación (`LIBRE` / `OCUPADA`) solo cambia con el check-in y el check-out; nunca a mano. | D07 |
| RN-HAB-005 | Solo el personal de Limpieza puede marcar una habitación como `LIMPIA`. | D07 |
| RN-HAB-006 | Una habitación solo pasa a `FUERA_DE_SERVICIO` por una incidencia que impide su uso. | D07 |
| RN-HAB-007 | El número de habitación es único. Una habitación no se desactiva ni cambia de tipo si tiene reservas futuras o está ocupada. | D04 (HU-ADM-06) |
| RN-HAB-008 | Al hacer check-out, la condición de la habitación pasa a `SUCIA` desde cualquier condición, salvo que tenga una incidencia que impida su uso, en cuyo caso pasa a `FUERA_DE_SERVICIO`. | REV |

---

## 5. Tarifas — RN-TAR

| ID | Regla | Origen |
|---|---|---|
| RN-TAR-001 | Precio de una noche = precio base del tipo × (1 + ajuste de temporada vigente) × (1 + ajuste de fin de semana, si aplica). | D04 (HU-ADM) |
| RN-TAR-002 | Las noches de fin de semana son las de viernes y sábado (PAR-13). | D04 |
| RN-TAR-003 | No pueden existir dos temporadas que se traslapen para un mismo tipo de habitación. | D04 |
| RN-TAR-004 | El precio final de una noche siempre es mayor a cero. | D04 |
| RN-TAR-005 | Los precios incluyen impuestos (IVA 12 % + INGUAT 10 %) y no se desglosan (PAR-11). | NUEVA |
| RN-TAR-006 | El precio de cada noche se redondea a 2 decimales; el total de la estadía es la suma de las noches. | NUEVA |
| RN-TAR-007 | El precio queda fijo al crear la reserva. Los cambios de tarifa no afectan reservas existentes. Si se modifican fechas o tipo, la estadía se recalcula con las tarifas vigentes y se muestra la diferencia. | D04 |
| RN-TAR-008 | El precio siempre se calcula en el servidor, nunca solo en el navegador o la app. Excepción: reservas de canal (RN-TAR-010). | D04 (HU-HUE-04) |
| RN-TAR-009 | Todos los montos se manejan en quetzales (PAR-10). | D01 |
| RN-TAR-010 | En las reservas de canales, el cargo por alojamiento es el monto enviado por el canal; no se aplican las tarifas del hotel (RN-TAR-001 a 007). | REV |

---

## 6. Pagos y cuenta — RN-PAG

| ID | Regla | Origen |
|---|---|---|
| RN-PAG-001 | La confirmación de un pago en línea depende del evento confiable del proveedor (webhook), no de la respuesta del navegador. | ORIG |
| RN-PAG-002 | Los webhooks son idempotentes: un mismo evento de Stripe se procesa una sola vez. | ORIG |
| RN-PAG-003 | Los reintentos no deben duplicar pagos. | ORIG |
| RN-PAG-004 | El sistema no recibe ni almacena datos de tarjeta; el pago se hace en la página segura de Stripe. | ORIG (RNF-SEC-007) |
| RN-PAG-005 | Stripe funciona en modo prueba; no se procesa dinero real. | D01 |
| RN-PAG-006 | Las reservas web se pagan al 100 % por adelantado. | NUEVA |
| RN-PAG-007 | Las reservas de Recepción no requieren pago por adelantado; se cobran antes del check-out. | NUEVA |
| RN-PAG-008 | Las reservas de canales las cobra el canal; al recibirlas se registra un pago `APROBADO` con método `CANAL` por el total. | NUEVA |
| RN-PAG-009 | Existe una sola cuenta por reserva; se crea junto con la reserva e incluye el cargo por alojamiento. | D07 |
| RN-PAG-010 | Los cargos adicionales (room service, servicios) solo se agregan con la reserva `EN_ESTADIA` y la cuenta `ABIERTA`. | D07 |
| RN-PAG-011 | Los cargos no se eliminan; se anulan con motivo obligatorio. | D07 |
| RN-PAG-012 | Saldo = suma de cargos `VIGENTE` − (suma de pagos `APROBADO` − reembolsos parciales). | D07* |
| RN-PAG-013 | Un pago registrado en recepción debe ser mayor a cero y no superar el saldo pendiente. | D04 (HU-REC-18) |
| RN-PAG-014 | Métodos de pago: `EFECTIVO`, `TARJETA`, `STRIPE`, `CANAL`, `OTRO`. | NUEVA |
| RN-PAG-015 | Cada comprobante de pago tiene un número correlativo único e indica que no es una factura electrónica. | D04 (HU-REC-20) |
| RN-PAG-016 | Si el saldo queda a favor del huésped (negativo), se reembolsa antes del check-out: por Stripe si pagó en línea o en efectivo si pagó en recepción. El check-out exige saldo exactamente 0. | REV |
| RN-PAG-017 | En los indicadores, los ingresos son la suma de los cargos `VIGENTE` según su fecha, incluidas las penalidades. | REV |
| RN-PAG-018 | En la versión 1 no existen descuentos; un cobro incorrecto se corrige anulando el cargo. | REV |

---

## 7. Cancelación y no-show — RN-CAN

| ID | Regla | Origen |
|---|---|---|
| RN-CAN-001 | Solo se cancelan reservas `PENDIENTE_PAGO` o `CONFIRMADA`, con motivo obligatorio. | D07 |
| RN-CAN-002 | Si se cancela **48 horas o más** antes de la hora de check-in del día de llegada, se reembolsa el 100 % de lo pagado (PAR-07). | NUEVA |
| RN-CAN-003 | Si se cancela **con menos de 48 horas**, se cobra la primera noche como penalidad y se reembolsa el resto de lo pagado (PAR-08). | NUEVA |
| RN-CAN-004 | A las 23:59 del día de llegada, el sistema marca como `NO_SHOW` toda reserva `CONFIRMADA` sin check-in (PAR-09). | NUEVA |
| RN-CAN-005 | En un no-show se cobra la primera noche como penalidad y se reembolsa el resto de lo pagado. | NUEVA |
| RN-CAN-006 | Si la reserva no tiene pagos (ej. reserva de Recepción), la cancelación o el no-show no generan cargo. | NUEVA |
| RN-CAN-007 | Las reservas de canales siguen la política de reembolso del canal; el hotel no procesa reembolsos de esas reservas. | NUEVA |
| RN-CAN-008 | Los reembolsos se solicitan a Stripe (modo prueba). Un reembolso total pasa el pago a `REEMBOLSADO`; un reembolso parcial se registra asociado al pago original, que sigue `APROBADO`. | NUEVA (complementa D07-P5) |
| RN-CAN-009 | Al cancelar o marcar no-show, el cargo por alojamiento se anula y, si aplica, se registra el cargo de penalidad; luego la cuenta se cierra. | NUEVA |
| RN-CAN-010 | Toda cancelación envía un correo al huésped con el detalle del reembolso. | D04 |

**Ejemplo:** reserva de 3 noches a Q600 = Q1,800 pagados en la web. Se cancela 20 horas antes de la llegada → penalidad Q600 (primera noche) → reembolso Q1,200.

---

## 8. Room Service — RN-RS

| ID | Regla | Origen |
|---|---|---|
| RN-RS-001 | Flujo del pedido: `NUEVO → EN_PREPARACION → EN_CAMINO → ENTREGADO`. | ORIG |
| RN-RS-002 | No hay saltos ni retrocesos de estado. | ORIG |
| RN-RS-003 | Cancelar un pedido exige motivo. | ORIG |
| RN-RS-004 | Un pedido cancelado no se reactiva. | ORIG |
| RN-RS-005 | Los ítems agotados no pueden agregarse a nuevos pedidos. | ORIG |
| RN-RS-006 | Solo se crean pedidos para reservas `EN_ESTADIA`. | D07 |
| RN-RS-007 | El cargo se genera una sola vez, cuando el pedido pasa a `ENTREGADO`, con los precios vigentes al momento del pedido. | D07 |
| RN-RS-008 | Solo el Administrador puede reactivar un ítem agotado. | ORIG (HU-RS-07) |
| RN-RS-009 | Room Service funciona 24 horas (PAR-18). | NUEVA |
| RN-RS-010 | El huésped no puede cancelar pedidos. | D07 |
| RN-RS-011 | No se permite el check-out desde la app con pedidos `NUEVO`, `EN_PREPARACION` o `EN_CAMINO`. | D04 (HU-HUE-20) |

---

## 9. Limpieza y solicitudes — RN-LIM

| ID | Regla | Origen |
|---|---|---|
| RN-LIM-001 | Flujo de limpieza: `SUCIA → EN_LIMPIEZA → LIMPIA`; se permite volver de `EN_LIMPIEZA` a `SUCIA` si se interrumpe. | ORIG* |
| RN-LIM-002 | Una habitación `LIMPIA` sale de la lista de pendientes. | ORIG |
| RN-LIM-003 | Las solicitudes atendidas permanecen en el historial. | ORIG |
| RN-LIM-004 | Las habitaciones ocupadas solo se limpian cuando el huésped lo solicita; no hay limpieza diaria automática. | D07 |
| RN-LIM-005 | Solo puede haber 1 solicitud de limpieza activa (`PENDIENTE` o `EN_PROCESO`) por habitación (PAR-19). | D04 |
| RN-LIM-006 | Solo se crean solicitudes para reservas `EN_ESTADIA`. | D07 |
| RN-LIM-007 | La cantidad solicitada de cada artículo no puede superar el máximo definido en el catálogo. | D08 |
| RN-LIM-008 | El huésped solo puede cancelar solicitudes `PENDIENTE`. | D07 |
| RN-LIM-009 | Al hacer check-out, las solicitudes `PENDIENTE` se cancelan automáticamente. | D07 |
| RN-LIM-010 | Una habitación con llegada programada para hoy es prioritaria en la lista de limpieza. | D04 (HU-MYL-01) |
| RN-LIM-011 | Solo los empleados con área `LIMPIEZA` o `AMBAS` realizan limpiezas y atienden solicitudes. | D02 |
| RN-LIM-012 | Un objeto olvidado se asocia a la última reserva de la habitación y termina como `DEVUELTO` o `DESECHADO`. | D07 |
| RN-LIM-013 | La limpieza pedida por un huésped alojado se gestiona como solicitud y no cambia la condición de la habitación. | REV |

---

## 10. Mantenimiento — RN-MAN

| ID | Regla | Origen |
|---|---|---|
| RN-MAN-001 | Toda orden de mantenimiento nace de una incidencia reportada por Mantenimiento/Limpieza, Recepción o el Administrador. | ORIG* (sin preventivo: Fase 2) |
| RN-MAN-002 | Flujo: `REPORTADA → ASIGNADA → EN_PROCESO → RESUELTA → CERRADA`. | ORIG* ("Abierta" → `REPORTADA`) |
| RN-MAN-003 | Solo el **Administrador** asigna, reasigna, devuelve, cierra y cancela órdenes. | ORIG* ("encargado" → Administrador) |
| RN-MAN-004 | Cancelar una orden exige motivo. | ORIG |
| RN-MAN-005 | Una habitación con una orden abierta que impide su uso no se libera. | ORIG |
| RN-MAN-006 | Al liberarse por mantenimiento, la habitación pasa a `SUCIA`. | ORIG |
| RN-MAN-007 | Todo cambio de estado registra fecha, hora y responsable. | ORIG |
| RN-MAN-008 | El consumo de repuestos no puede exceder el stock. | ORIG |
| RN-MAN-009 | Solo el técnico asignado (área `MANTENIMIENTO` o `AMBAS`) inicia y resuelve la orden. | D07 |
| RN-MAN-010 | La descripción de la solución es obligatoria para marcar una orden como `RESUELTA`. | D04 |
| RN-MAN-011 | Reasignar exige motivo; devolver una orden resuelta a `EN_PROCESO` exige comentario. | D07 |

---

## 11. Inventario — RN-INV

| ID | Regla | Origen |
|---|---|---|
| RN-INV-001 | El stock solo cambia mediante movimientos de inventario. | D08 |
| RN-INV-002 | El stock nunca queda negativo; el movimiento que lo haría se rechaza. | D08 |
| RN-INV-003 | Todo movimiento registra producto, tipo, cantidad, stock resultante, responsable, fecha y origen. | D08 |
| RN-INV-004 | Un `AJUSTE` requiere motivo y solo lo hace el Administrador. | D08 |
| RN-INV-005 | Cuando el stock queda igual o por debajo del mínimo, se alerta al Administrador. | D08 |
| RN-INV-006 | Limpieza solo consume `INSUMO_LIMPIEZA` o `AMENIDAD_HABITACION`; Mantenimiento solo `REPUESTO`. | D08 |
| RN-INV-007 | Al atender una solicitud de artículos, cada artículo vinculado a un producto descuenta stock (`CONSUMO_ENTREGA`). | D08 |
| RN-INV-008 | La lencería no se controla en el inventario en la versión 1. | D08 |
| RN-INV-009 | No puede haber más de un reporte de faltante `PENDIENTE` por producto. | D08 |

---

## 12. Turnos — RN-TUR

| ID | Regla | Origen |
|---|---|---|
| RN-TUR-001 | Un empleado no puede tener dos turnos que se traslapen. | D08 |
| RN-TUR-002 | No se asignan turnos al rol `ADMIN` ni a empleados `INACTIVO`. | D08 |
| RN-TUR-003 | Los turnos son informativos; no restringen el acceso al sistema. | D08 |
| RN-TUR-004 | No se desactiva un turno con asignaciones futuras. | D08 |

---

## 13. Personal y roles — RN-PER y R-ROL

### Roles (documento 02, se conservan sus IDs)

| ID | Regla |
|---|---|
| R-ROL-01 | Un empleado tiene un solo rol. |
| R-ROL-02 | Los empleados no se eliminan, se desactivan. |
| R-ROL-03 | Solo el Administrador crea cuentas de personal. |
| R-ROL-04 | Todo el personal, excepto el Administrador, trabaja en turnos. |
| R-ROL-05 | El rol Mantenimiento/Limpieza requiere un área. |
| R-ROL-06 | El huésped solo accede a su propia información. |
| R-ROL-07 | Los permisos se aplican en la base de datos, no solo en la interfaz. |

### Personal

| ID | Regla | Origen |
|---|---|---|
| RN-PER-001 | Solo el Administrador crea, edita y desactiva empleados. | D08 |
| RN-PER-002 | Los empleados no se eliminan; se desactivan y su nombre se conserva en el historial. | D08 |
| RN-PER-003 | No se desactiva a un empleado con órdenes `ASIGNADA` o `EN_PROCESO`, solicitudes `EN_PROCESO` o habitaciones `EN_LIMPIEZA` a su cargo; primero se reasignan (HU-ADM-20). | D08* |
| RN-PER-004 | Al desactivar a un empleado se eliminan sus turnos futuros y se cierra su sesión. | D08 |
| RN-PER-005 | El Administrador no puede desactivarse a sí mismo. | D08 |
| RN-PER-006 | Un mismo correo no puede pertenecer a un empleado y a un huésped. | D08 |
| RN-PER-007 | El rol `MANTENIMIENTO_LIMPIEZA` requiere área `LIMPIEZA`, `MANTENIMIENTO` o `AMBAS`. | D08 |

---

## 14. Seguridad — RN-SEG

| ID | Regla | Origen |
|---|---|---|
| RN-SEG-001 | El huésped solo ve sus propios registros. | D09 |
| RN-SEG-002 | Room Service y Mantenimiento/Limpieza no ven datos personales de los huéspedes. | D09 |
| RN-SEG-003 | Las fotos de documentos se guardan en almacenamiento privado (Recepción, Admin y el propio huésped). | D09 |
| RN-SEG-004 | La información pública no expone reservas ni datos de otros huéspedes. | D09 |
| RN-SEG-005 | Un empleado `INACTIVO` no tiene permisos. | D09 |
| RN-SEG-006 | Los permisos se aplican en la base de datos (RLS) y en el backend. | D09 |
| RN-SEG-007 | Las claves con privilegios totales solo existen en el servidor. | D09 |
| RN-SEG-008 | Los secretos no se guardan en el repositorio Git. | ORIG (RNF-SEC-006) |

---

## 15. App y acceso del huésped — RN-APP

| ID | Regla | Origen |
|---|---|---|
| RN-APP-001 | El huésped entra con su correo y un código OTP de 6 dígitos, de un solo uso, que vence en 10 minutos (PAR-14). | NUEVA |
| RN-APP-002 | Los intentos de verificación del OTP están limitados por dirección IP según la configuración de Supabase Auth (PAR-15). | NUEVA* (ajustada en el documento 14) |
| RN-APP-003 | Si el correo no tiene reservas, se muestra un mensaje genérico que no revela si el correo existe. | D04 (HU-HUE-08) |
| RN-APP-004 | Solo el huésped principal de la reserva tiene acceso; los huéspedes adicionales no son usuarios. | D02 |
| RN-APP-005 | Las funciones de estadía (room service, solicitudes, pago y check-out) solo están disponibles con la reserva `EN_ESTADIA`. | D07 |
| RN-APP-006 | Una estadía `FINALIZADA` queda en solo lectura. | D07 |
| RN-APP-007 | El check-in anticipado está disponible desde 24 horas antes de la llegada, solo para reservas `CONFIRMADA`, y no reemplaza el check-in en recepción (PAR-16). | NUEVA |
| RN-APP-008 | El check-out desde la app está disponible desde las 00:00 del día de salida hasta la hora de check-out; después, solo en Recepción (PAR-17). | NUEVA |
| RN-APP-009 | Los archivos subidos tienen un máximo de 5 MB y formato JPG, PNG o PDF (PAR-20). | NUEVA |

---

## 16. Channel Manager — RN-CM

| ID | Regla | Origen |
|---|---|---|
| RN-CM-001 | Cada canal tiene una clave de API única, guardada cifrada (hash) y mostrada una sola vez. | D04 (HU-CM-01) |
| RN-CM-002 | Un canal `INACTIVO` no puede enviar reservas ni cancelaciones. | D07 |
| RN-CM-003 | Las reservas de canales se validan con las mismas reglas de disponibilidad que las reservas directas. | D04 |
| RN-CM-004 | Una reserva con el mismo identificador externo y canal no se duplica. | D04 |
| RN-CM-005 | Un canal solo puede cancelar sus propias reservas, y solo si están `CONFIRMADA`. | D04 |
| RN-CM-006 | Toda petición recibida por la API (aceptada o rechazada) queda registrada. | D04 |
| RN-CM-007 | El canal simulado usa la API real; la conexión con Booking o Expedia reales es Fase 2. | D01 |

---

## 17. Reglas generales de estados — RG-EST

Definidas en el documento 07, sección 10. Se listan aquí como referencia.

| ID | Regla |
|---|---|
| RG-EST-01 | Solo se permiten las transiciones documentadas en el documento 07. |
| RG-EST-02 | Todo cambio de estado se registra en un historial. |
| RG-EST-03 | Las transiciones se validan en el servidor o la base de datos. |
| RG-EST-04 | Los estados finales no se pueden modificar. |
| RG-EST-05 | Los códigos de estado son únicos en todo el proyecto. |
| RG-EST-06 | Toda cancelación, anulación, reasignación o devolución exige motivo. |
| RG-EST-07 | Los cambios de estado se reflejan en tiempo real. |

---

## 18. Cambios respecto a las reglas originales

| Regla original | Cambio |
|---|---|
| RN-RES-002, 004 | Se precisan qué estados ocupan disponibilidad y qué eventos la liberan |
| RN-HAB-001, 002 | Se usan los estados del documento 07 y se define qué pasa con habitaciones ocupadas |
| RN-LIM-001 | Estados con códigos (`SUCIA`, `EN_LIMPIEZA`, `LIMPIA`) y posibilidad de interrupción |
| RN-MAN-001 | Se elimina "tarea preventiva" (Fase 2, F2-16) |
| RN-MAN-002 | "Abierta" se renombra `REPORTADA` |
| RN-MAN-003 | "Encargado" se reemplaza por Administrador |
| Resto de reglas originales | Se conservan sin cambios |

**Documento 14 (tecnologías):** RN-APP-002 y PAR-15 se ajustaron al límite de intentos por IP de Supabase Auth.

**Revisión de consistencia:** se agregaron PAR-21 (zona horaria), RN-HAB-008, RN-TAR-010, RN-PAG-016, 017, 018 y RN-LIM-013, y se amplió RN-PER-003.

**Total de reglas:** 142 (+ 7 reglas de roles R-ROL + 7 reglas generales RG-EST) · **Parámetros:** 21
