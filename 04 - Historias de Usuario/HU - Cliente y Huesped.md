# HU — Cliente y Huésped

> **Rol:** Cliente/Huésped (`HUESPED`)
> **Plataformas:** Web pública y App Android
> **Prefijo:** `HU-HUE`
> **Total de historias:** 21
> **Referencias:** 01 — Alcance (secciones A y E) · 02 — Definición de Roles (3.5)

Recordatorio del rol: el **Cliente** navega y reserva sin sesión. El **Huésped** entra con correo + código OTP, y lo que puede hacer depende del estado de su reserva.

---

## Épica 1: Explorar y reservar (Web pública)

### HU-HUE-01 — Ver información del hotel

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-01 | Media | S | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** ver la información general del hotel
- **Para** conocer Villa Serena antes de decidir mi reserva

**Criterios de aceptación**
1. La página de inicio muestra nombre, descripción, fotos, ubicación y datos de contacto del hotel.
2. Se muestran los horarios de check-in y check-out configurados por el Administrador.
3. Se muestra un acceso directo al buscador de disponibilidad.
4. Se muestra un resumen de las amenidades del hotel.
5. La página se visualiza correctamente en computadora y en teléfono.

**Depende de:** HU-ADM-08, HU-ADM-19

---

### HU-HUE-02 — Explorar el catálogo de habitaciones

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-02 | Alta | S | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** ver los tipos de habitación del hotel
- **Para** elegir la que mejor se adapta a mi viaje

**Criterios de aceptación**
1. Se listan todos los tipos de habitación activos.
2. Cada tipo muestra nombre, fotos, descripción, capacidad máxima y precio base por noche en quetzales.
3. Al seleccionar un tipo se muestra su detalle con todas sus fotos.
4. Los tipos de habitación desactivados por el Administrador no se muestran.

**Depende de:** HU-ADM-05

---

### HU-HUE-03 — Consultar disponibilidad por fechas

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-03 | Alta | M | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** buscar habitaciones disponibles indicando fechas y número de huéspedes
- **Para** saber qué opciones tengo para mi estadía

**Criterios de aceptación**
1. Se seleccionan fecha de entrada y fecha de salida en un calendario.
2. No se permiten fechas pasadas ni una fecha de salida igual o anterior a la de entrada.
3. Se indica el número de huéspedes; solo se muestran tipos de habitación con capacidad suficiente.
4. Solo se muestran tipos con al menos una habitación libre en **todas** las noches del rango.
5. Se excluyen habitaciones fuera de servicio y habitaciones con reservas que se traslapen.
6. Si no hay disponibilidad, se muestra un mensaje claro y se sugiere cambiar las fechas.

**Reglas relacionadas:** RN-RES-001, RN-RES-002, RN-HAB-001
**Depende de:** HU-ADM-05, HU-ADM-06

---

### HU-HUE-04 — Ver el precio total de mi estadía

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-05 | Alta | M | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** ver el precio total de mi estadía antes de reservar
- **Para** conocer exactamente cuánto voy a pagar

**Criterios de aceptación**
1. Para cada tipo disponible se muestra el precio total del rango de fechas en quetzales.
2. Se muestra el desglose por noche, indicando qué noches tienen tarifa de temporada o de fin de semana.
3. El precio se calcula con las tarifas vigentes configuradas por el Administrador.
4. El precio mostrado es el mismo que se cobra en el pago.
5. Si una tarifa cambia mientras el cliente está reservando, el total se recalcula antes de pagar y se avisa del cambio.

**Depende de:** HU-HUE-03, HU-ADM-09, HU-ADM-10
**Notas técnicas:** el cálculo debe hacerse en el servidor, nunca solo en el navegador.

---

### HU-HUE-05 — Reservar sin crear una cuenta

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-04 | Alta | M | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** reservar una habitación ingresando solo mis datos
- **Para** asegurar mi hospedaje sin registrarme ni llamar por teléfono

**Criterios de aceptación**
1. Se solicitan nombre completo, correo, teléfono, nacionalidad y número de documento (DPI o pasaporte) del huésped principal.
2. Todos los campos son obligatorios y el correo debe tener un formato válido.
3. Se muestra un resumen (tipo de habitación, fechas, huéspedes, total) antes de continuar al pago.
4. Antes de crear la reserva se **revalida la disponibilidad**; si ya no hay, se avisa y no se crea.
5. La reserva se crea en estado `Pendiente de pago` con canal de origen `Directo web` y un código de reserva único.
6. Mientras está `Pendiente de pago`, la habitación queda apartada para evitar sobreventa.
7. Junto con la reserva se crea la cuenta del huésped con el cargo por alojamiento.

**Reglas relacionadas:** RN-RES-001, RN-RES-002
**Depende de:** HU-HUE-04

---

### HU-HUE-06 — Pagar mi reserva en línea

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-06 | Alta | L | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** pagar mi reserva con tarjeta en línea
- **Para** dejar confirmada mi reserva

**Criterios de aceptación**
1. El pago se realiza en la página segura de Stripe (modo prueba); el sistema nunca recibe ni guarda los datos de la tarjeta.
2. La reserva pasa a `Confirmada` **solo** cuando llega el webhook de pago exitoso de Stripe, no por la redirección del navegador.
3. Si el mismo webhook llega dos veces, el pago se registra una sola vez.
4. Si el pago falla, la reserva sigue en `Pendiente de pago` y el cliente puede intentar de nuevo.
5. Si el pago no se confirma en 30 minutos, la reserva se cancela automáticamente y la habitación se libera.
6. Al volver de Stripe, el cliente ve una página con el estado de su reserva (confirmada, en proceso o fallida).

**Reglas relacionadas:** RN-PAG-001, RN-PAG-002, RN-PAG-003, RN-PAG-006, RN-RES-012
**Depende de:** HU-HUE-05

---

### HU-HUE-07 — Recibir la confirmación por correo

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Explorar y reservar | ALC-PUB-07, ALC-TRA-05 | Alta | S | Pendiente |

**Historia**
- **Como** cliente
- **Quiero** recibir un correo cuando mi reserva quede confirmada
- **Para** tener el comprobante y saber cómo acceder a la app

**Criterios de aceptación**
1. El correo se envía automáticamente cuando la reserva pasa a `Confirmada`.
2. Incluye código de reserva, fechas, tipo de habitación, número de huéspedes, total pagado y horarios de check-in/out.
3. Incluye el enlace de descarga de la app Android y las instrucciones para entrar con el correo.
4. Incluye un enlace a "Mi reserva" en la web pública.
5. Si el envío falla, se reintenta y el error queda registrado.

**Depende de:** HU-HUE-06

---

## Épica 2: Mi reserva (Web pública y App)

### HU-HUE-08 — Entrar con mi correo y un código

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública, App | Mi reserva | ALC-PUB-08, ALC-APP-01, ALC-TRA-01 | Alta | M | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** entrar escribiendo mi correo y un código que me llega por email
- **Para** acceder a mis reservas sin tener que recordar una contraseña

**Criterios de aceptación**
1. El huésped escribe su correo y recibe un código de 6 dígitos por email.
2. El código tiene 6 dígitos, vence en 10 minutos y solo puede usarse una vez.
3. Si el correo no tiene ninguna reserva, se muestra un mensaje genérico, sin revelar si el correo existe.
4. Al entrar por primera vez, el sistema vincula al huésped con todas las reservas hechas con ese correo.
5. La sesión se mantiene abierta en la app hasta que el huésped cierre sesión.
6. Los intentos de verificación están limitados por dirección IP (configuración de Supabase Auth); al superar el límite se muestra un mensaje para intentar más tarde.

**Reglas relacionadas:** RN-APP-001, RN-APP-002, RN-APP-003

**Depende de:** HU-HUE-05
**Notas técnicas:** Supabase Auth con OTP por correo y un proveedor SMTP propio.

---

### HU-HUE-09 — Consultar mis reservas

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública, App | Mi reserva | ALC-PUB-08 | Alta | S | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** ver todas mis reservas
- **Para** revisar los detalles de mis estadías próximas, actuales y pasadas

**Criterios de aceptación**
1. Se listan todas las reservas del huésped, agrupadas en próximas, en curso y pasadas.
2. Cada reserva muestra código, fechas, tipo de habitación, número de huéspedes, estado y total.
3. El huésped solo ve sus propias reservas.
4. Al seleccionar una reserva se muestran las acciones disponibles según su estado.

**Reglas relacionadas:** R-ROL-06
**Depende de:** HU-HUE-08

---

### HU-HUE-10 — Cancelar mi reserva

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública | Mi reserva | ALC-PUB-08 | Media | M | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** cancelar una reserva futura
- **Para** liberar la habitación si ya no voy a viajar

**Criterios de aceptación**
1. Solo se pueden cancelar reservas en estado `Confirmada` o `Pendiente de pago`.
2. Antes de confirmar se muestra la política de cancelación y el monto a reembolsar: 100 % si faltan 48 horas o más para la llegada; si faltan menos, se descuenta la primera noche.
3. El huésped debe confirmar la cancelación.
4. La reserva pasa a `Cancelada`, se registra el motivo "Cancelada por el huésped" y la habitación se libera.
5. Si corresponde reembolso, se solicita a Stripe (modo prueba) y queda registrado.
6. El huésped recibe un correo confirmando la cancelación.

**Reglas relacionadas:** RN-RES-004, RN-RES-016, RN-CAN-001, RN-CAN-002, RN-CAN-003, RN-CAN-008
**Depende de:** HU-HUE-09

---

### HU-HUE-11 — Hacer check-in anticipado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web pública, App | Mi reserva | ALC-PUB-09 | Media | M | Pendiente |

**Historia**
- **Como** huésped con reserva confirmada
- **Quiero** completar mis datos antes de llegar al hotel
- **Para** que mi registro en recepción sea más rápido

**Criterios de aceptación**
1. El check-in anticipado está disponible desde 24 horas antes de la fecha de entrada.
2. El huésped confirma o corrige sus datos personales.
3. Puede registrar a los huéspedes adicionales (nombre, documento, nacionalidad).
4. Puede subir una foto de su documento de identidad en formato JPG, PNG o PDF, de máximo 5 MB.
5. Puede escribir peticiones especiales (cama adicional, piso alto, etc.).
6. La reserva queda marcada como "Check-in anticipado completado", visible para Recepción.
7. El check-in anticipado **no** reemplaza el check-in en recepción; solo lo agiliza.

**Depende de:** HU-HUE-09
**Notas técnicas:** el documento se guarda en almacenamiento privado; solo Recepción y el propio huésped pueden verlo.

---

## Épica 3: Mi estadía (App)

### HU-HUE-12 — Ver los detalles de mi estadía

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-02 | Alta | S | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** ver en la app los detalles de mi estadía
- **Para** tener a mano toda la información de mi reserva

**Criterios de aceptación**
1. Se muestran código de reserva, fechas, hora de check-out, tipo y número de habitación, y huéspedes registrados.
2. Se muestra el estado actual de la reserva.
3. El número de habitación se muestra cuando ya fue asignada.
4. Si el huésped tiene varias reservas, puede elegir cuál ver; por defecto se muestra la que está en curso.

**Depende de:** HU-HUE-08

---

### HU-HUE-13 — Pedir room service

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-03 | Alta | M | Pendiente |

**Historia**
- **Como** huésped alojado
- **Quiero** pedir comida desde la app
- **Para** recibirla en mi habitación sin llamar a recepción

**Criterios de aceptación**
1. Solo está disponible si la reserva está en estado `En estadía`.
2. Se muestra el menú por categorías con nombre, descripción, precio y disponibilidad.
3. Los ítems agotados se muestran como no disponibles y no se pueden agregar.
4. El huésped elige ítems y cantidades y puede escribir notas (alergias, preferencias).
5. Antes de enviar se muestra el total y el aviso de que se cargará a la cuenta de la habitación.
6. El pedido se crea en estado `Nuevo` y aparece de inmediato en la pantalla de Room Service.

**Reglas relacionadas:** RN-RS-005
**Depende de:** HU-HUE-12, HU-ADM-07

---

### HU-HUE-14 — Seguir mi pedido en tiempo real

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-04, ALC-TRA-04 | Alta | M | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** ver el estado de mi pedido mientras se prepara
- **Para** saber cuándo va a llegar

**Criterios de aceptación**
1. Se muestra el estado actual: `Nuevo`, `En preparación`, `En camino`, `Entregado` o `Cancelado`.
2. El estado se actualiza automáticamente, sin recargar la pantalla.
3. Si el pedido se cancela, se muestra el motivo.
4. Se muestra el historial de pedidos de la estadía con su estado y total.

**Reglas relacionadas:** RN-RS-001
**Depende de:** HU-HUE-13, HU-RS-04

---

### HU-HUE-15 — Solicitar limpieza de mi habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-05 | Alta | S | Pendiente |

**Historia**
- **Como** huésped alojado
- **Quiero** solicitar la limpieza de mi habitación desde la app
- **Para** que la limpien sin tener que ir a recepción

**Criterios de aceptación**
1. Solo está disponible si la reserva está en estado `En estadía`.
2. El huésped puede agregar un comentario opcional (ej. "después de las 2 p.m.").
3. La solicitud se crea en estado `Pendiente` y aparece de inmediato al personal de Limpieza.
4. No se puede crear una nueva solicitud de limpieza si ya hay una `Pendiente` o `En proceso` para la misma habitación.

**Depende de:** HU-HUE-12

---

### HU-HUE-16 — Solicitar artículos para mi habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-05 | Alta | S | Pendiente |

**Historia**
- **Como** huésped alojado
- **Quiero** pedir artículos como toallas o almohadas desde la app
- **Para** recibirlos en mi habitación

**Criterios de aceptación**
1. Solo está disponible si la reserva está en estado `En estadía`.
2. Se muestra el catálogo de artículos activos definido por el Administrador (toallas, papel higiénico, jabón, almohadas, cobijas, etc.).
3. El huésped elige uno o varios artículos y la cantidad de cada uno, con un máximo por artículo.
4. La solicitud se crea en estado `Pendiente` y aparece de inmediato al personal de Limpieza.

**Depende de:** HU-HUE-12, HU-ADM-12

---

### HU-HUE-17 — Ver el estado de mis solicitudes

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-05, ALC-TRA-04 | Media | S | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** ver el estado de mis solicitudes de limpieza y artículos
- **Para** saber si ya fueron atendidas

**Criterios de aceptación**
1. Se listan las solicitudes de la estadía con tipo, fecha, hora y estado (`Pendiente`, `En proceso`, `Atendida`, `Cancelada`).
2. El estado se actualiza automáticamente.
3. El huésped puede cancelar una solicitud mientras esté `Pendiente`.

**Depende de:** HU-HUE-15, HU-HUE-16

---

### HU-HUE-18 — Explorar el directorio de amenidades

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-06 | Alta | S | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** consultar las amenidades del hotel
- **Para** aprovechar los servicios durante mi estadía

**Criterios de aceptación**
1. Se listan las amenidades activas con nombre, foto, descripción, horario y ubicación.
2. Se muestran el nombre de la red Wi-Fi y su contraseña.
3. Las amenidades desactivadas por el Administrador no se muestran.
4. La información es visible para cualquier huésped con sesión, sin importar el estado de su reserva.

**Depende de:** HU-ADM-08, HU-ADM-19

---

### HU-HUE-19 — Ver mi cuenta

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-07 | Alta | M | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** ver mis consumos y mi saldo
- **Para** controlar mis gastos durante la estadía

**Criterios de aceptación**
1. Se muestra el cargo por alojamiento y cada cargo adicional (room service, servicios) con fecha, concepto y monto.
2. Se muestran los pagos realizados con fecha, método y monto.
3. Se muestra el saldo pendiente en quetzales.
4. Los nuevos cargos aparecen sin recargar la pantalla.
5. La información es de solo lectura.

**Depende de:** HU-HUE-12, HU-REC-19

---

### HU-HUE-20 — Pagar mi saldo y hacer check-out desde la app

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-08 | Alta | L | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** pagar mi saldo y hacer el check-out desde el teléfono
- **Para** retirarme del hotel sin hacer fila en recepción

**Criterios de aceptación**
1. Solo está disponible si la reserva está `En estadía`, desde las 00:00 del día de salida hasta la hora de check-out (12:00); después, el check-out se hace en Recepción.
2. No se permite si hay pedidos de room service sin terminar (`Nuevo`, `En preparación` o `En camino`).
3. Si hay saldo pendiente, el huésped paga con Stripe (modo prueba); el pago se confirma por webhook.
4. Con saldo en cero, el huésped confirma el check-out.
5. Al confirmar: la reserva pasa a `Finalizada`, la cuenta se cierra, la habitación pasa a `Libre` + `Sucia` (o `Fuera de servicio` si tiene una incidencia que impide su uso) y las solicitudes pendientes se cancelan.
6. El huésped recibe por correo el comprobante de pago y el resumen de su cuenta.
7. La estadía queda en modo solo lectura en la app.

**Reglas relacionadas:** RN-PAG-001, RN-PAG-002, RN-HAB-003, RN-HAB-008, RN-APP-008, RN-RS-011
**Estados:** documento 07, transición R9 y sección 11
**Depende de:** HU-HUE-19

---

### HU-HUE-21 — Consultar el historial de mis estadías

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| App | Mi estadía | ALC-APP-09 | Baja | S | Pendiente |

**Historia**
- **Como** huésped
- **Quiero** consultar mis estadías anteriores
- **Para** recordar fechas, consumos y pagos

**Criterios de aceptación**
1. Se listan las reservas `Finalizada` con fechas, habitación y total pagado.
2. Se puede ver el detalle de la cuenta de cada estadía.
3. No se permiten pedidos, solicitudes ni pagos sobre estadías finalizadas.

**Depende de:** HU-HUE-09
