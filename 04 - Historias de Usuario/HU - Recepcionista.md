# HU — Recepcionista

> **Rol:** Recepcionista (`RECEPCION`). El Administrador también puede realizar todas estas operaciones.
> **Plataforma:** Web privada
> **Prefijo:** `HU-REC`
> **Total de historias:** 23
> **Referencias:** 01 — Alcance (sección B) · 02 — Definición de Roles (3.2)

---

## Épica 1: Huéspedes

### HU-REC-01 — Registrar huésped

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Huéspedes | ALC-REC-01 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** registrar los datos personales de un huésped
- **Para** crear su perfil dentro del sistema

**Criterios de aceptación**
1. Se registran nombre completo, tipo de documento (DPI o pasaporte), número de documento, teléfono, correo y nacionalidad.
2. Todos los campos son obligatorios; el correo debe tener un formato válido.
3. Si ya existe un huésped con el mismo documento o correo, se avisa y se ofrece usar el perfil existente.
4. El huésped queda disponible para asociarlo a reservas.
5. No se permite registrar como huésped un correo que pertenezca a un empleado.

**Reglas relacionadas:** RN-PER-006

---

### HU-REC-02 — Registrar huéspedes adicionales

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Huéspedes | ALC-REC-01 | Media | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** registrar a todas las personas que ocuparán una habitación
- **Para** mantener un registro completo de los huéspedes alojados

**Criterios de aceptación**
1. Desde la reserva se pueden agregar huéspedes adicionales con nombre, documento y nacionalidad.
2. El total de huéspedes no puede superar la capacidad de la habitación.
3. Los huéspedes adicionales quedan asociados a la reserva, pero no tienen acceso a la app.
4. Se muestran los adicionales registrados por el huésped en su check-in anticipado.

**Depende de:** HU-REC-05

---

### HU-REC-03 — Consultar el historial de un huésped

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Huéspedes | ALC-REC-08 | Baja | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** consultar las estadías anteriores de un huésped
- **Para** conocer sus visitas y preferencias

**Criterios de aceptación**
1. Se busca al huésped por nombre, documento o correo.
2. Se listan sus reservas con fechas, habitación, estado y total pagado.
3. Se puede ver el detalle de la cuenta de cada estadía (servicios consumidos y pagos).

**Depende de:** HU-REC-01

---

## Épica 2: Reservas

### HU-REC-04 — Consultar disponibilidad

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reservas | ALC-REC-03 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** consultar las habitaciones disponibles para un rango de fechas
- **Para** ofrecer opciones al huésped

**Criterios de aceptación**
1. Se ingresan fecha de entrada, fecha de salida y número de huéspedes.
2. Se muestran las habitaciones disponibles con número, tipo, capacidad y precio total del rango.
3. Se excluyen habitaciones con reservas que se traslapen o que estén fuera de servicio.
4. Si no hay disponibilidad, se muestra un mensaje claro.

**Reglas relacionadas:** RN-RES-001, RN-RES-002, RN-HAB-001
**Notas técnicas:** usa el mismo cálculo de disponibilidad y precio que la web pública (HU-HUE-03, HU-HUE-04).

---

### HU-REC-05 — Crear reserva

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reservas | ALC-REC-02, ALC-REC-13 | Alta | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** crear una reserva para un huésped
- **Para** garantizarle una habitación durante su estadía

**Criterios de aceptación**
1. Se selecciona un huésped existente o se registra uno nuevo.
2. Se indican fechas, número de huéspedes y tipo de habitación (opcionalmente una habitación específica).
3. Se muestra la tarifa aplicada por noche y el total antes de confirmar.
4. Antes de guardar se **revalida la disponibilidad**; si no hay, no se crea la reserva.
5. La reserva se crea en estado `Confirmada`, con canal de origen `Recepción` y un código de reserva único.
6. Se registra el recepcionista que creó la reserva.
7. Junto con la reserva se crea la cuenta del huésped con el cargo por alojamiento.

**Reglas relacionadas:** RN-RES-001, RN-RES-002
**Depende de:** HU-REC-01, HU-REC-04

---

### HU-REC-06 — Modificar reserva

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reservas | ALC-REC-02 | Alta | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** modificar una reserva existente
- **Para** atender los cambios que solicite el huésped

**Criterios de aceptación**
1. Se pueden modificar fechas, número de huéspedes, tipo de habitación y habitación de reservas `Confirmada` o `En estadía`.
2. En una reserva `En estadía` solo se puede modificar la fecha de salida y la habitación.
3. Todo cambio que afecte disponibilidad se revalida antes de guardar.
4. Si cambia el precio, se muestra la diferencia y el nuevo total antes de confirmar; al guardar, el cargo por alojamiento de la cuenta se ajusta.
5. El cambio queda registrado con fecha, hora y responsable.
6. Si la modificación deja un saldo a favor del huésped, ese saldo se reembolsa antes del check-out.

**Reglas relacionadas:** RN-RES-003, RN-RES-017, RN-TAR-007, RN-PAG-016
**Depende de:** HU-REC-05

---

### HU-REC-07 — Cancelar reserva

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reservas | ALC-REC-02 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** cancelar una reserva
- **Para** mantener actualizada la disponibilidad del hotel

**Criterios de aceptación**
1. Solo se pueden cancelar reservas `Pendiente de pago` o `Confirmada`.
2. Se debe seleccionar un motivo de cancelación (obligatorio).
3. Se muestra la penalidad y el reembolso que aplican según la política (RN-CAN-002 a RN-CAN-007).
4. Se solicita confirmación antes de cancelar.
5. La reserva pasa a `Cancelada` y la habitación se libera.
6. El huésped recibe un correo notificando la cancelación.

**Reglas relacionadas:** RN-RES-004
**Depende de:** HU-REC-05

---

### HU-REC-08 — Buscar reservas

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reservas | ALC-REC-08 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** buscar reservas por distintos criterios
- **Para** encontrar rápidamente la información de un huésped

**Criterios de aceptación**
1. Se puede buscar por nombre del huésped, documento, código de reserva o fecha.
2. Se puede filtrar por estado y por canal de origen.
3. Los resultados muestran código, huésped, fechas, habitación, estado y canal.
4. Al seleccionar un resultado se abre el detalle de la reserva.

---

### HU-REC-09 — Asignar habitación a una reserva

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reservas | ALC-REC-04 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** asignar una habitación específica a una reserva
- **Para** que el huésped tenga su habitación preparada al llegar

**Criterios de aceptación**
1. Solo se muestran habitaciones del tipo reservado que estén libres en todo el rango de fechas.
2. No se pueden asignar habitaciones fuera de servicio.
3. El sistema impide asignar una habitación que ya tenga otra reserva en fechas que se traslapen.
4. Se puede asignar o cambiar la habitación mientras la reserva esté `Pendiente de pago`, `Confirmada` o `En estadía`.

**Reglas relacionadas:** RN-RES-002, RN-HAB-001
**Depende de:** HU-REC-05

---

## Épica 3: Operación diaria

### HU-REC-10 — Ver la vista del día

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Operación diaria | ALC-REC-09 | Alta | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** ver en una sola pantalla las llegadas y salidas del día
- **Para** organizar el trabajo de recepción

**Criterios de aceptación**
1. Se muestran las llegadas del día (reservas `Confirmada` con entrada hoy), indicando si completaron el check-in anticipado.
2. Se muestran las salidas del día (reservas `En estadía` con salida hoy) y su saldo pendiente.
3. Se muestran las reservas `Pendiente de pago`.
4. Se muestra el número de habitaciones disponibles, ocupadas, sucias y fuera de servicio.
5. Desde cada fila se puede ir directamente al check-in o al check-out.

---

### HU-REC-11 — Ver el calendario Gantt de ocupación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Operación diaria | ALC-REC-12 | Alta | L | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** ver las reservas en un calendario de habitaciones por días
- **Para** entender de un vistazo la ocupación del hotel

**Criterios de aceptación**
1. Las filas son las habitaciones (agrupadas por tipo) y las columnas son los días.
2. Cada reserva se muestra como una barra desde la fecha de entrada hasta la de salida.
3. El color de la barra indica el estado de la reserva y un ícono indica el canal de origen.
4. Las habitaciones fuera de servicio se muestran bloqueadas en los días que corresponda.
5. Se puede navegar por semanas y por meses, y volver a "hoy".
6. Al hacer clic en una barra se muestra el resumen de la reserva y un enlace a su detalle.
7. El calendario se actualiza automáticamente cuando otra persona crea o modifica una reserva.
8. Las reservas sin habitación asignada se muestran en una fila aparte, "Sin asignar".

**Depende de:** HU-REC-05, HU-REC-09

---

### HU-REC-12 — Crear una reserva desde el calendario Gantt

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Operación diaria | ALC-REC-12 | Alta | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** crear una reserva seleccionando días libres en el calendario
- **Para** reservar más rápido viendo la ocupación

**Criterios de aceptación**
1. Al seleccionar un rango de días libres en la fila de una habitación, se abre el formulario de reserva con la habitación y las fechas ya llenadas.
2. No se puede seleccionar un rango que se traslape con otra reserva o con un bloqueo.
3. El formulario aplica las mismas validaciones que HU-REC-05.
4. Al guardar, la nueva reserva aparece en el calendario sin recargar la página.

**Depende de:** HU-REC-11

---

### HU-REC-13 — Consultar el estado de las habitaciones

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Operación diaria | ALC-REC-10 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** ver el estado actual de todas las habitaciones
- **Para** saber cuáles puedo ofrecer

**Criterios de aceptación**
1. Cada habitación muestra su **ocupación** (`Libre` / `Ocupada`) y su **condición** (`Limpia` / `Sucia` / `En limpieza` / `Fuera de servicio`).
2. Se indica si la habitación tiene una llegada programada para hoy.
3. Se puede filtrar por ocupación, condición, tipo y piso.
4. Los estados se actualizan automáticamente cuando Limpieza o Mantenimiento los cambian.
5. Para una habitación `Fuera de servicio` se puede consultar (solo lectura) la incidencia que la bloquea y su estado.

**Notas técnicas:** estados definidos en el documento 07 — Estados; permisos en el documento 09.

---

### HU-REC-14 — Actualizar el estado de una habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Operación diaria | ALC-REC-10 | Media | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** cambiar la condición de una habitación
- **Para** mantener la información del hotel sincronizada

**Criterios de aceptación**
1. Recepción puede marcar una habitación como `Sucia` (por ejemplo, para pedir una limpieza).
2. Recepción **no** puede marcar una habitación como `Limpia`; eso lo hace Limpieza.
3. La ocupación (`Libre` / `Ocupada`) no se cambia a mano: cambia solo con el check-in y el check-out.
4. Para dejar una habitación fuera de servicio se debe reportar una incidencia (HU-REC-22).
5. Todo cambio registra fecha, hora y responsable.

**Reglas relacionadas:** RN-HAB-002

---

## Épica 4: Estadía

### HU-REC-15 — Realizar check-in

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Estadía | ALC-REC-05 | Alta | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** registrar la llegada del huésped
- **Para** confirmar oficialmente su ingreso al hotel

**Criterios de aceptación**
1. Solo se puede hacer check-in de reservas `Confirmada` cuya fecha de entrada sea hoy.
2. Se muestran los datos del huésped principal y de los adicionales para verificarlos, incluido lo cargado en el check-in anticipado.
3. Si la reserva no tiene habitación asignada, se pide asignarla antes de continuar.
4. La habitación asignada debe estar `Libre` y `Limpia`; si no, se avisa y no se permite el check-in.
5. Se registran la fecha, la hora y el recepcionista que hizo el check-in.
6. La reserva pasa a `En estadía` y la habitación a `Ocupada`. (La cuenta del huésped ya existe desde que se creó la reserva.)
7. Desde ese momento el huésped puede usar las funciones de estadía en la app.

**Depende de:** HU-REC-05, HU-REC-09

---

### HU-REC-16 — Realizar check-out

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Estadía | ALC-REC-05 | Alta | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** registrar la salida del huésped
- **Para** finalizar su estadía y liberar la habitación

**Criterios de aceptación**
1. Solo se puede hacer check-out de reservas `En estadía`.
2. Se muestra la cuenta completa: alojamiento, cargos adicionales, pagos y saldo.
3. Se avisa si hay pedidos de room service sin terminar.
4. El saldo debe ser exactamente cero: si hay saldo pendiente, primero se registra el pago; si hay saldo a favor del huésped, primero se registra el reembolso.
5. Al confirmar: la reserva pasa a `Finalizada`, la cuenta se cierra, la habitación pasa a `Libre` + `Sucia` (o `Fuera de servicio` si tiene una incidencia que impide su uso), las solicitudes pendientes se cancelan y se registra fecha, hora y responsable.
6. El huésped recibe por correo el resumen de su cuenta.

**Reglas relacionadas:** RN-RES-015, RN-PAG-016, RN-HAB-003, RN-HAB-008
**Depende de:** HU-REC-15, HU-REC-18

---

## Épica 5: Cuenta y pagos

### HU-REC-17 — Registrar servicios adicionales

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Cuenta y pagos | ALC-REC-06 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** agregar servicios consumidos a la cuenta del huésped
- **Para** incluirlos en su cobro final

**Criterios de aceptación**
1. Solo se pueden agregar cargos a cuentas abiertas (reserva `En estadía`).
2. Se indica concepto (restaurante, lavandería, estacionamiento u otro), cantidad y precio unitario.
3. El total del cargo se calcula automáticamente.
4. El cargo queda registrado con fecha, hora y responsable y se ve al instante en la app del huésped.
5. Un cargo registrado por error se anula con un motivo; no se elimina.

**Depende de:** HU-REC-15

---

### HU-REC-18 — Registrar pagos

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Cuenta y pagos | ALC-REC-06 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** registrar los pagos que hace el huésped en recepción
- **Para** mantener actualizado el saldo de su cuenta

**Criterios de aceptación**
1. Se muestra el total de la cuenta, el monto pagado y el saldo pendiente.
2. Se indica el monto y el método de pago (efectivo, tarjeta u otro) y una referencia opcional.
3. El monto debe ser mayor a cero y no puede superar el saldo pendiente.
4. El pago queda registrado con fecha, hora y responsable, y el saldo se actualiza.

**Reglas relacionadas:** RN-PAG-003
**Depende de:** HU-REC-19

---

### HU-REC-19 — Consultar la cuenta del huésped

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Cuenta y pagos | ALC-REC-06 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** ver todos los cargos y pagos de una reserva
- **Para** informar al huésped cuánto debe pagar

**Criterios de aceptación**
1. Se muestra el cargo por alojamiento con el detalle por noche.
2. Se muestran los cargos adicionales (room service, servicios) con fecha, concepto y monto.
3. Se muestran los pagos (en línea, en recepción y del canal) y los reembolsos.
4. Se muestra el saldo pendiente en quetzales.

**Depende de:** HU-REC-15

---

### HU-REC-20 — Generar comprobante de pago

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Cuenta y pagos | ALC-REC-07 | Media | M | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** generar un comprobante de pago en PDF
- **Para** entregárselo al huésped como evidencia

**Criterios de aceptación**
1. El comprobante incluye datos del hotel, datos del huésped, código de reserva, conceptos cobrados, total, monto pagado, método de pago y fecha.
2. Se puede descargar en PDF y enviar por correo al huésped.
3. Cada comprobante tiene un número correlativo único.
4. El comprobante indica que no es una factura electrónica.

**Depende de:** HU-REC-18

---

## Épica 6: Solicitudes y comunicación

### HU-REC-21 — Registrar una solicitud de huésped y enviarla a un área

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Solicitudes | ALC-REC-11 | Media | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** registrar lo que un huésped pide en recepción y enviarlo al área correcta
- **Para** que sea atendido a tiempo

**Criterios de aceptación**
1. La solicitud se asocia a una reserva `En estadía` y a su habitación.
2. Se indica el tipo (limpieza o artículos), la descripción y la prioridad (normal o alta).
3. La solicitud se crea en estado `Pendiente` y aparece de inmediato al personal de Limpieza.
4. Recepción puede consultar el estado de la solicitud hasta que sea `Atendida`.

**Notas técnicas:** es la misma solicitud que crea el huésped desde la app (HU-HUE-15, HU-HUE-16), con origen "Recepción".

---

### HU-REC-22 — Reportar un daño en una habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Solicitudes | ALC-REC-11 | Media | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** reportar un daño que me informa un huésped
- **Para** que Mantenimiento lo repare

**Criterios de aceptación**
1. Se selecciona la habitación, el tipo de problema y se escribe una descripción.
2. Se indica si el daño impide usar la habitación.
3. Se crea una incidencia en estado `Reportada`, visible para el Administrador.
4. Si el daño impide usar la habitación y está libre, pasa a `Fuera de servicio`.
5. Si la habitación está ocupada, no pasa a `Fuera de servicio` automáticamente; se avisa para gestionar el cambio de habitación del huésped.

**Reglas relacionadas:** RN-HAB-002, RN-MAN-001
**Notas técnicas:** es la misma incidencia que crea el personal de Mantenimiento/Limpieza (HU-MYL-10).

---

### HU-REC-23 — Recibir avisos en tiempo real

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Solicitudes | ALC-REC-14, ALC-TRA-04 | Media | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** recibir un aviso en pantalla cuando pase algo importante
- **Para** reaccionar sin estar revisando la pantalla constantemente

**Criterios de aceptación**
1. Se muestra un aviso cuando llega una nueva reserva en línea o de un canal externo.
2. Se muestra un aviso cuando un huésped hace check-out desde la app.
3. Se muestra un aviso cuando una habitación con llegada hoy pasa a `Limpia`.
4. Al hacer clic en el aviso se abre el detalle correspondiente.
