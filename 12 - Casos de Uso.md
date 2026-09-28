# 12 — Casos de Uso

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** 📝 Para revisión del equipo
> **Fecha:** 24 de septiembre de 2026
> **Documento anterior:** 11 — Requisitos Funcionales
> **Reemplaza a:** "Casos de uso principales" (documentación inicial)

---

## Índice

1. [Propósito y plantilla](#1-propósito-y-plantilla)
2. [Actores](#2-actores)
3. [Mapa de casos de uso](#3-mapa-de-casos-de-uso)
4. [Reservas](#4-reservas) — UC-01, UC-02, UC-09, UC-10, UC-14
5. [Pagos](#5-pagos) — UC-05
6. [Estadía](#6-estadía) — UC-11, UC-12, UC-03, UC-04
7. [Servicios al huésped](#7-servicios-al-huésped) — UC-06, UC-13
8. [Operación de habitaciones](#8-operación-de-habitaciones) — UC-07, UC-08
9. [Procesos automáticos](#9-procesos-automáticos) — UC-15
10. [Channel Manager](#10-channel-manager) — UC-16
11. [Administración](#11-administración) — UC-17, UC-18, UC-19
12. [Correspondencia con los casos de uso originales](#12-correspondencia-con-los-casos-de-uso-originales)

---

## 1. Propósito y plantilla

Un **caso de uso** describe **una interacción completa** entre un actor y el sistema para lograr un objetivo, incluyendo qué pasa cuando algo sale mal. A diferencia de las historias de usuario (una necesidad puntual), un caso de uso **recorre varias historias** de principio a fin.

Se conservan los IDs originales UC-01 a UC-08 y se agregan UC-09 a UC-19.

**Plantilla de cada caso de uso:**

| Campo | Contenido |
|---|---|
| Actores | Principal y secundarios (documento 02) |
| Historias | Historias de usuario que cubre (documento 04) |
| Precondiciones | Qué debe ser cierto antes de empezar |
| Flujo principal | Pasos del caso exitoso |
| Flujos alternativos | Variantes y errores (`A1`, `A2`…), indicando en qué paso ocurren |
| Postcondiciones | Qué es cierto al terminar con éxito |
| Reglas | Reglas de negocio aplicables (documento 10) |

---

## 2. Actores

| Actor | Tipo | Documento |
|---|---|---|
| Cliente (visitante sin sesión) | Humano | 02, 3.5 |
| Huésped (con sesión OTP) | Humano | 02, 3.5 |
| Recepcionista | Humano | 02, 3.2 |
| Room Service | Humano | 02, 3.3 |
| Mantenimiento/Limpieza (MYL) | Humano | 02, 3.4 |
| Administrador | Humano | 02, 3.1 |
| Sistema | No humano (procesos automáticos) | 02, 4 |
| Pasarela de pago (Stripe) | No humano (sistema externo) | 02, 4 |
| Canal externo | No humano (sistema externo) | 02, 4 |

---

## 3. Mapa de casos de uso

| UC | Nombre | Cliente / Huésped | Recepción | Room Service | MYL | Admin | Sistema / externos |
|---|---|---|---|---|---|---|---|
| UC-01 | Buscar disponibilidad | ● | ● | | | ○ | |
| UC-02 | Crear reserva | ● | ● | | | ○ | Pasarela |
| UC-03 | Realizar check-in | | ● | | | ○ | |
| UC-04 | Realizar check-out | ● | ● | | | ○ | Sistema, Pasarela |
| UC-05 | Procesar pago en línea | ● | | | | | Pasarela, Sistema |
| UC-06 | Gestionar pedido de Room Service | ● | | ● | | ○ | Sistema |
| UC-07 | Limpiar una habitación | | ○ | | ● | | Sistema |
| UC-08 | Gestionar incidencia de mantenimiento | | ● | | ● | ● | Sistema |
| UC-09 | Modificar reserva | | ● | | | ○ | |
| UC-10 | Cancelar reserva | ● | ● | | | ○ | Sistema, Pasarela |
| UC-11 | Acceder como huésped (OTP) | ● | | | | | |
| UC-12 | Hacer check-in anticipado | ● | ○ | | | | |
| UC-13 | Atender solicitud de limpieza o artículos | ● | ● | | ● | | Sistema |
| UC-14 | Gestionar reservas en el calendario Gantt | | ● | | | ○ | |
| UC-15 | Ejecutar procesos automáticos (vencimiento de pago y no-show) | | | | | | Sistema, Pasarela |
| UC-16 | Recibir reserva o cancelación de un canal externo | | ○ | | | ● | Canal |
| UC-17 | Configurar tarifas dinámicas | | | | | ● | |
| UC-18 | Gestionar inventario y reposición | | | | ● | ● | Sistema |
| UC-19 | Gestionar personal y turnos | | | | | ● | |

● Actor principal · ○ Participa o puede ejecutarlo

---

## 4. Reservas

### UC-01 — Buscar disponibilidad

| Campo | Contenido |
|---|---|
| Actores | Cliente, Recepcionista (principales) |
| Historias | HU-HUE-03, HU-HUE-04, HU-REC-04 |
| Precondiciones | Existen tipos de habitación y habitaciones activas con tarifa configurada. |
| Reglas | RN-RES-001, 002, 005, 006, 007, 008 · RN-HAB-001 · RN-TAR-001 a 008 |

**Flujo principal**

1. El actor indica fecha de entrada, fecha de salida y número de huéspedes.
2. El sistema valida las fechas y la cantidad de huéspedes.
3. El sistema calcula, para cada tipo con capacidad suficiente, cuántas habitaciones libres hay en **todas** las noches del rango.
4. El sistema calcula el precio de cada noche y el total con las tarifas vigentes.
5. El sistema muestra los tipos disponibles con fotos, capacidad, precio total y desglose por noche.

**Flujos alternativos**

- **A1 (paso 2) — Fechas inválidas:** fechas pasadas, salida igual o anterior a la entrada, más de 30 noches o más de 365 días de anticipación. Se muestra el error y no se busca.
- **A2 (paso 3) — Sin disponibilidad:** se informa que no hay habitaciones y se sugiere cambiar las fechas.
- **A3 (paso 5) — Recepción:** además de los tipos, se listan las habitaciones específicas disponibles (número y piso).

**Postcondiciones:** el actor conoce las opciones y precios. No se modifica ningún dato.

---

### UC-02 — Crear reserva

| Campo | Contenido |
|---|---|
| Actores | Cliente o Recepcionista (principal) · Pasarela (secundario, solo web) |
| Historias | HU-HUE-05, HU-HUE-06, HU-HUE-07, HU-REC-01, HU-REC-05 |
| Precondiciones | El actor completó UC-01 y eligió un tipo de habitación. |
| Reglas | RN-RES-001, 002, 009, 010, 011, 012 · RN-PAG-006, 007, 009 · RN-TAR-007 |

**Flujo principal (web pública)**

1. El cliente ingresa los datos del huésped principal (nombre, documento, correo, teléfono, nacionalidad).
2. El sistema muestra el resumen: tipo, fechas, huéspedes y total.
3. El cliente confirma.
4. El sistema **revalida la disponibilidad**.
5. El sistema crea la reserva en `PENDIENTE_PAGO`, con canal `DIRECTO_WEB` y código único, y crea la cuenta `ABIERTA` con el cargo por alojamiento.
6. El sistema redirige al cliente al pago (continúa en **UC-05**).
7. Al confirmarse el pago, la reserva pasa a `CONFIRMADA` y se envía el correo de confirmación con el código y el enlace a la app.

**Flujos alternativos**

- **A1 (paso 1) — Datos incompletos o correo inválido:** se marcan los campos con error y no se continúa.
- **A2 (paso 4) — Ya no hay disponibilidad:** otra persona reservó mientras tanto. Se avisa, no se crea la reserva y se vuelve a UC-01.
- **A3 (paso 6) — El pago no se completa en 30 minutos:** la reserva se cancela automáticamente (**UC-15**).
- **A4 — Reserva en Recepción:**
  1. El recepcionista selecciona un huésped existente o lo registra (HU-REC-01).
  2. Indica fechas, huéspedes, tipo y, opcionalmente, una habitación específica.
  3. El sistema muestra la tarifa aplicada y el total.
  4. El sistema revalida la disponibilidad y crea la reserva directamente en `CONFIRMADA`, con canal `RECEPCION` y la cuenta `ABIERTA`. No se requiere pago por adelantado.
- **A5 (paso 5) — La tarifa cambió durante el proceso:** se recalcula el total y se avisa al cliente antes de pagar.

**Postcondiciones:** existe una reserva `CONFIRMADA` (o `PENDIENTE_PAGO` en espera de pago) con cuenta abierta, y la disponibilidad se redujo.

---

### UC-09 — Modificar reserva

| Campo | Contenido |
|---|---|
| Actores | Recepcionista (principal) · Administrador |
| Historias | HU-REC-06, HU-REC-09 |
| Precondiciones | La reserva está `CONFIRMADA` o `EN_ESTADIA`. |
| Reglas | RN-RES-003, 013, 016, 017 · RN-TAR-007 · RN-PAG-016 |

**Flujo principal**

1. El recepcionista busca la reserva (HU-REC-08) y elige "Modificar".
2. Cambia fechas, número de huéspedes, tipo o habitación.
3. El sistema revalida la disponibilidad para las nuevas condiciones.
4. El sistema recalcula el precio y muestra la diferencia; si queda un saldo a favor del huésped, se reembolsará antes del check-out (RN-PAG-016).
5. El recepcionista confirma.
6. El sistema guarda los cambios, ajusta el cargo por alojamiento y registra el cambio con responsable.

**Flujos alternativos**

- **A1 (paso 2) — Reserva `EN_ESTADIA`:** solo se permite cambiar la fecha de salida y la habitación.
- **A2 (paso 3) — Sin disponibilidad:** se muestra el conflicto y no se guarda.
- **A3 — El huésped solicita el cambio:** el huésped no puede modificar desde la web o la app; debe contactar a Recepción (RN-RES-016).

**Postcondiciones:** la reserva y su cuenta reflejan las nuevas condiciones, sin traslapes.

---

### UC-10 — Cancelar reserva

| Campo | Contenido |
|---|---|
| Actores | Huésped o Recepcionista (principal) · Sistema, Pasarela (secundarios) |
| Historias | HU-HUE-10, HU-REC-07 |
| Precondiciones | La reserva está `PENDIENTE_PAGO` o `CONFIRMADA`. |
| Reglas | RN-CAN-001 a 010 · RN-RES-004 |

**Flujo principal**

1. El actor selecciona la reserva y elige "Cancelar".
2. El sistema calcula la penalidad y el reembolso según la anticipación: 100 % de reembolso si faltan 48 horas o más para la llegada; si faltan menos, se retiene la primera noche.
3. El sistema muestra el resultado y pide el motivo (Recepción) o confirmación (huésped).
4. El actor confirma.
5. El sistema pasa la reserva a `CANCELADA` y libera la disponibilidad.
6. El sistema anula el cargo por alojamiento, registra la penalidad si aplica y solicita el reembolso a Stripe.
7. El sistema cierra la cuenta y envía un correo al huésped con el detalle.

**Flujos alternativos**

- **A1 (paso 2) — Reserva sin pagos:** no se genera penalidad ni reembolso (RN-CAN-006).
- **A2 (paso 2) — Reserva de un canal externo:** lo normal es que la cancelación llegue por el canal (**UC-16**). Si Recepción la cancela, el hotel no procesa reembolso; lo gestiona el canal (RN-CAN-007).
- **A3 (paso 6) — Falla el reembolso en Stripe:** la cancelación se mantiene, el reembolso queda pendiente y se registra el error para reintentarlo.

**Postcondiciones:** reserva `CANCELADA`, cuenta `CERRADA`, disponibilidad liberada, reembolso solicitado si correspondía.

---

### UC-14 — Gestionar reservas en el calendario Gantt

| Campo | Contenido |
|---|---|
| Actores | Recepcionista (principal) · Administrador |
| Historias | HU-REC-11, HU-REC-12, HU-CM-04 |
| Precondiciones | Existen habitaciones activas. |
| Reglas | RN-RES-002, 010 · RN-HAB-001 |

**Flujo principal**

1. El recepcionista abre el calendario Gantt; por defecto se muestra la semana actual.
2. El sistema muestra las habitaciones agrupadas por tipo (filas) y los días (columnas), con cada reserva como una barra coloreada según su estado y con el ícono de su canal.
3. El recepcionista navega por semanas o meses.
4. El recepcionista hace clic en una barra y el sistema muestra el resumen de la reserva.
5. El recepcionista selecciona un rango de días libres en una habitación.
6. El sistema abre el formulario de reserva con la habitación y las fechas llenadas (continúa en **UC-02, A4**).
7. Al guardar, la nueva barra aparece sin recargar la página.

**Flujos alternativos**

- **A1 (paso 5) — El rango se traslapa con otra reserva o un bloqueo:** el sistema no permite la selección.
- **A2 (paso 2) — Reservas sin habitación asignada:** se muestran en una fila aparte, "Sin asignar".
- **A3 (en cualquier momento) — Otro usuario crea o modifica una reserva:** el calendario se actualiza automáticamente.

**Postcondiciones:** Recepción tiene una vista actualizada de la ocupación; si creó una reserva, esta existe como en UC-02.

---

## 5. Pagos

### UC-05 — Procesar pago en línea

| Campo | Contenido |
|---|---|
| Actores | Huésped o Cliente (principal) · Pasarela (Stripe) · Sistema |
| Historias | HU-HUE-06, HU-HUE-20 |
| Precondiciones | Existe una cuenta `ABIERTA` con saldo mayor a cero. |
| Reglas | RN-PAG-001 a 005, 012 |

**Flujo principal**

1. El sistema registra un pago `PENDIENTE` por el monto a pagar y crea una sesión de pago en Stripe.
2. El actor es redirigido a la página segura de Stripe e ingresa los datos de tarjeta (de prueba).
3. Stripe procesa el pago y envía un **webhook** al sistema.
4. El sistema verifica la **firma** del webhook.
5. El sistema verifica que el evento **no se haya procesado antes** (idempotencia).
6. El sistema marca el pago como `APROBADO` y actualiza el saldo.
7. Si el pago era de una reserva `PENDIENTE_PAGO`, la reserva pasa a `CONFIRMADA` (continúa UC-02, paso 7).
8. El actor regresa al sitio y ve el resultado.

**Flujos alternativos**

- **A1 (paso 3) — Pago rechazado o expirado:** el webhook indica fallo; el pago pasa a `FALLIDO` y el actor puede intentar de nuevo.
- **A2 (paso 4) — Firma inválida:** el evento se rechaza y se registra como sospechoso; no cambia nada.
- **A3 (paso 5) — Evento repetido:** se responde con éxito a Stripe sin volver a procesarlo.
- **A4 (paso 8) — El actor vuelve antes de que llegue el webhook:** la página muestra "Pago en proceso" y se actualiza cuando llega la confirmación.

**Postcondiciones:** el pago queda `APROBADO` o `FALLIDO` una sola vez; el saldo y el estado de la reserva son correctos.

---

## 6. Estadía

### UC-11 — Acceder como huésped (OTP)

| Campo | Contenido |
|---|---|
| Actores | Huésped |
| Historias | HU-HUE-08, HU-HUE-09 |
| Precondiciones | El huésped tiene al menos una reserva hecha con su correo. |
| Reglas | RN-APP-001 a 004 · RN-PER-006 |

**Flujo principal**

1. El huésped abre la app (o "Mi reserva" en la web) y escribe su correo.
2. El sistema envía un código de 6 dígitos al correo.
3. El huésped escribe el código.
4. El sistema valida el código (vigente, no usado).
5. Si es el primer acceso, el sistema vincula al huésped con todas las reservas hechas con ese correo.
6. El sistema muestra sus reservas: próximas, en curso y pasadas.

**Flujos alternativos**

- **A1 (paso 2) — El correo no tiene reservas:** se muestra el mismo mensaje genérico ("Si el correo tiene reservas, recibirás un código"), sin revelar nada.
- **A2 (paso 4) — Código incorrecto o vencido:** se muestra un error; el huésped puede pedir un nuevo código.
- **A3 (paso 4) — Se supera el límite de intentos por IP:** se muestra un mensaje para intentar más tarde (RN-APP-002).

**Postcondiciones:** el huésped tiene sesión y solo ve sus propias reservas.

---

### UC-12 — Hacer check-in anticipado

| Campo | Contenido |
|---|---|
| Actores | Huésped (principal) · Recepcionista (consulta el resultado) |
| Historias | HU-HUE-11, HU-REC-02 |
| Precondiciones | El huésped tiene sesión (UC-11); la reserva está `CONFIRMADA` y faltan 24 horas o menos para la llegada. |
| Reglas | RN-APP-007, 009 · RN-RES-007 · RN-SEG-003 |

**Flujo principal**

1. El huésped selecciona su reserva y elige "Check-in anticipado".
2. Confirma o corrige sus datos personales.
3. Registra a los huéspedes adicionales.
4. Sube la foto de su documento de identidad.
5. Escribe peticiones especiales (opcional).
6. El sistema guarda todo y marca la reserva como "Check-in anticipado completado".

**Flujos alternativos**

- **A1 (paso 1) — Faltan más de 24 horas:** la opción aparece deshabilitada con la fecha en que se habilita.
- **A2 (paso 3) — Se supera la capacidad de la habitación:** no se permite agregar más huéspedes.
- **A3 (paso 4) — Archivo de más de 5 MB o formato no permitido:** se rechaza con un mensaje.

**Postcondiciones:** Recepción ve los datos completos al hacer el check-in (UC-03). La reserva sigue `CONFIRMADA`.

---

### UC-03 — Realizar check-in

| Campo | Contenido |
|---|---|
| Actores | Recepcionista (principal) · Administrador |
| Historias | HU-REC-15, HU-REC-09, HU-REC-02, HU-REC-10 |
| Precondiciones | Reserva `CONFIRMADA` con fecha de entrada hoy. |
| Reglas | RN-RES-013, 014 |

**Flujo principal**

1. El recepcionista busca la reserva desde la vista del día o el buscador.
2. El sistema muestra los datos del huésped principal y de los adicionales, incluido lo cargado en el check-in anticipado.
3. El recepcionista verifica la identidad y los datos.
4. El sistema confirma que la reserva tiene una habitación asignada, `LIBRE` y `LIMPIA`.
5. El recepcionista confirma el check-in.
6. El sistema pasa la reserva a `EN_ESTADIA` y la habitación a `OCUPADA`, y registra fecha, hora y responsable.
7. Se habilitan las funciones de estadía en la app del huésped.

**Flujos alternativos**

- **A1 (paso 4) — Sin habitación asignada:** el recepcionista asigna una (HU-REC-09) y continúa.
- **A2 (paso 4) — La habitación no está `LIMPIA`:** el sistema no permite el check-in. El recepcionista asigna otra habitación limpia del mismo tipo o espera a Limpieza (UC-07).
- **A3 (paso 3) — Faltan huéspedes adicionales:** se registran en ese momento (HU-REC-02).
- **A4 (paso 1) — La fecha de entrada no es hoy:** el sistema no permite el check-in.

**Postcondiciones:** reserva `EN_ESTADIA`, habitación `OCUPADA`, huésped habilitado en la app.

---

### UC-04 — Realizar check-out

| Campo | Contenido |
|---|---|
| Actores | Recepcionista o Huésped (principal) · Sistema · Pasarela |
| Historias | HU-REC-16, HU-REC-18, HU-REC-19, HU-HUE-19, HU-HUE-20 |
| Precondiciones | Reserva `EN_ESTADIA`. |
| Reglas | RN-RES-015 · RN-PAG-016 · RN-RS-011 · RN-HAB-002, 003, 008 · RN-LIM-009 · RN-APP-006, 008 |

**Flujo principal (Recepción)**

1. El recepcionista busca la estadía desde la vista del día.
2. El sistema muestra la cuenta completa: alojamiento, cargos, pagos y saldo.
3. Si hay saldo pendiente, el recepcionista registra el pago (HU-REC-18); si hay saldo a favor del huésped, registra el reembolso.
4. Con saldo exactamente cero, el recepcionista confirma el check-out.
5. El sistema pasa la reserva a `FINALIZADA` y cierra la cuenta.
6. La habitación pasa a `LIBRE` + `SUCIA` (desde cualquier condición) y aparece en los pendientes de Limpieza.
7. Las solicitudes `PENDIENTE` se cancelan con el motivo "Estadía finalizada".
8. El sistema envía al huésped el resumen de la cuenta por correo, y la estadía queda en solo lectura en la app.

**Flujos alternativos**

- **A1 — Check-out desde la app (huésped):**
  1. Entre las 00:00 del día de salida y la hora de check-out, el huésped abre "Mi cuenta" y elige "Hacer check-out".
  2. Si hay pedidos de room service en curso, el sistema no lo permite (RN-RS-011).
  3. Si hay saldo, el huésped paga (**UC-05**).
  4. Con saldo cero, confirma; el sistema continúa en el paso 5.
- **A2 (paso 6) — La habitación tiene una incidencia que impide su uso:** pasa a `FUERA_DE_SERVICIO` en lugar de `SUCIA`.
- **A3 (paso 2) — Hay pedidos de room service en curso (Recepción):** se muestra una advertencia; el recepcionista decide si continuar.
- **A4 (A1, paso 1) — Pasó la hora de check-out:** la opción ya no está disponible en la app; el huésped debe ir a Recepción.

**Postcondiciones:** reserva `FINALIZADA`, cuenta `CERRADA` con saldo cero, habitación liberada para limpieza.

---

## 7. Servicios al huésped

### UC-06 — Gestionar pedido de Room Service

| Campo | Contenido |
|---|---|
| Actores | Huésped o Room Service (inicia) · Room Service (atiende) · Sistema |
| Historias | HU-HUE-13, HU-HUE-14, HU-RS-01 a HU-RS-10 |
| Precondiciones | Reserva `EN_ESTADIA`; el menú tiene ítems `DISPONIBLE`. |
| Reglas | RN-RS-001 a 011 · RN-PAG-010 |

**Flujo principal**

1. El huésped elige ítems del menú en la app, indica cantidades y notas, y envía el pedido.
2. El sistema crea el pedido en `NUEVO` y avisa en tiempo real a Room Service.
3. Room Service revisa el detalle y lo pasa a `EN_PREPARACION`.
4. Room Service lo pasa a `EN_CAMINO` al salir hacia la habitación.
5. Room Service lo pasa a `ENTREGADO` al entregarlo.
6. El sistema genera **un** cargo en la cuenta del huésped.
7. En cada cambio, el huésped ve el nuevo estado en la app.

**Flujos alternativos**

- **A1 (paso 1) — Pedido telefónico:** Room Service registra el pedido eligiendo la habitación ocupada (HU-RS-03); continúa en el paso 2.
- **A2 (paso 1) — Ítem agotado:** no se puede agregar.
- **A3 (pasos 3 a 5) — Cancelación:** Room Service o el Admin cancelan con motivo; no se genera cargo y el huésped ve el motivo.
- **A4 (paso 3) — Un ítem se agota durante la preparación:** Room Service marca el ítem como agotado y cancela el pedido con ese motivo (A3).

**Postcondiciones:** pedido `ENTREGADO` con cargo en la cuenta, o `CANCELADO` sin cargo.

---

### UC-13 — Atender solicitud de limpieza o artículos

| Campo | Contenido |
|---|---|
| Actores | Huésped o Recepcionista (inicia) · MYL, área Limpieza (atiende) · Sistema |
| Historias | HU-HUE-15, HU-HUE-16, HU-HUE-17, HU-REC-21, HU-MYL-06, HU-MYL-07 |
| Precondiciones | Reserva `EN_ESTADIA`. |
| Reglas | RN-LIM-003 a 009, 011 · RN-INV-007, 008 |

**Flujo principal**

1. El huésped pide una limpieza o elige artículos y cantidades en la app.
2. El sistema crea la solicitud en `PENDIENTE` y avisa en tiempo real a Limpieza.
3. Un empleado de Limpieza la toma: pasa a `EN_PROCESO` y queda asignada a él.
4. El empleado atiende la solicitud en la habitación.
5. El empleado la marca como `ATENDIDA` confirmando lo entregado.
6. Por cada artículo consumible entregado, el sistema descuenta el inventario.
7. El huésped ve el cambio de estado en la app.

**Flujos alternativos**

- **A1 (paso 1) — Solicitud desde Recepción:** el recepcionista la registra (HU-REC-21); continúa en el paso 2.
- **A2 (paso 1) — Ya hay una solicitud de limpieza activa para la habitación:** no se permite crear otra.
- **A3 (paso 1) — Cantidad mayor al máximo del catálogo:** no se permite.
- **A4 (antes del paso 3) — Cancelación:** el huésped o Recepción cancelan la solicitud `PENDIENTE`.
- **A5 (paso 6) — Stock insuficiente de un consumible:** no se puede marcar `ATENDIDA`; el empleado reporta el faltante (**UC-18**).
- **A6 (antes del paso 3) — Check-out del huésped:** la solicitud se cancela automáticamente.

**Postcondiciones:** solicitud `ATENDIDA` con el inventario actualizado, o `CANCELADA`.

---

## 8. Operación de habitaciones

### UC-07 — Limpiar una habitación

| Campo | Contenido |
|---|---|
| Actores | MYL, área Limpieza (principal) · Sistema · Recepcionista (recibe aviso) |
| Historias | HU-MYL-01 a HU-MYL-05, HU-MYL-08, HU-MYL-11 |
| Precondiciones | La habitación está `SUCIA` (por check-out, por mantenimiento o marcada por Recepción). |
| Reglas | RN-LIM-001, 002, 010 · RN-HAB-005 · RN-INV-001, 002, 006 |

**Flujo principal**

1. El empleado ve la lista de pendientes; las prioritarias (llegada hoy) aparecen primero.
2. Selecciona una habitación y elige "Iniciar limpieza"; la habitación pasa a `EN_LIMPIEZA`.
3. Limpia la habitación.
4. Registra los insumos usados; el sistema los descuenta del inventario.
5. Elige "Finalizar limpieza"; la habitación pasa a `LIMPIA` y sale de la lista.
6. Si la habitación tiene una llegada hoy, Recepción recibe un aviso.

**Flujos alternativos**

- **A1 (paso 3) — Encuentra un daño:** reporta una incidencia (**UC-08**).
- **A2 (paso 3) — Encuentra un objeto olvidado:** lo registra (HU-MYL-11).
- **A3 (paso 3) — Observación importante:** la registra (HU-MYL-05).
- **A4 (paso 3) — Debe interrumpir la limpieza:** la habitación vuelve a `SUCIA`.
- **A5 (paso 4) — Stock insuficiente:** el sistema rechaza el consumo; el empleado reporta el faltante (**UC-18**).

**Postcondiciones:** habitación `LIMPIA`, disponible para check-in, con los insumos descontados.

---

### UC-08 — Gestionar incidencia de mantenimiento

| Campo | Contenido |
|---|---|
| Actores | MYL o Recepcionista (reporta) · Administrador (asigna y cierra) · MYL, área Mantenimiento (repara) · Sistema |
| Historias | HU-MYL-10, HU-REC-22, HU-ADM-16, HU-MYL-13 a HU-MYL-16, HU-ADM-17 |
| Precondiciones | La habitación existe. |
| Reglas | RN-MAN-001 a 011 · RN-HAB-001, 002, 003, 006 · RN-INV-006 |

**Flujo principal**

1. Un empleado reporta el daño: habitación, tipo, descripción y si impide el uso. Se crea la incidencia `REPORTADA`.
2. Si impide el uso y la habitación está libre, pasa a `FUERA_DE_SERVICIO`.
3. El Administrador revisa la incidencia y la asigna a un técnico (con sugerencia de técnicos en turno), con prioridad y fecha compromiso. Pasa a `ASIGNADA`.
4. El técnico ve la orden en su lista y la inicia (`EN_PROCESO`).
5. El técnico repara y registra los repuestos usados (se descuentan del inventario).
6. El técnico la marca `RESUELTA`, describiendo la solución.
7. El Administrador revisa y la **cierra** (`CERRADA`).
8. Si era la última orden que impedía el uso, la habitación pasa a `SUCIA` (continúa en **UC-07**).

**Flujos alternativos**

- **A1 (paso 2) — La habitación está ocupada:** no cambia de estado; queda con el indicador "Incidencia pendiente" y pasa a `FUERA_DE_SERVICIO` en el check-out (UC-04, A2).
- **A2 (paso 3) — El técnico elegido no está en turno:** se muestra una advertencia; el Admin decide.
- **A3 (pasos 3 a 6) — Reasignación:** el Admin reasigna la orden con motivo.
- **A4 (paso 7) — La reparación no fue aceptada:** el Admin la devuelve a `EN_PROCESO` con comentario.
- **A5 (cualquier paso antes de cerrar) — Cancelación:** el Admin cancela con motivo; si era la última orden que impedía el uso, la habitación pasa a `SUCIA`.
- **A6 (paso 5) — Stock insuficiente de repuestos:** el consumo se rechaza; el técnico reporta el faltante (UC-18).

**Postcondiciones:** orden `CERRADA` o `CANCELADA`; la habitación vuelve al flujo de limpieza.

---

## 9. Procesos automáticos

### UC-15 — Ejecutar procesos automáticos (vencimiento de pago y no-show)

| Campo | Contenido |
|---|---|
| Actores | Sistema (principal) · Pasarela |
| Historias | HU-HUE-06 (criterio 5) |
| Precondiciones | El proceso programado se ejecuta periódicamente en el servidor. |
| Reglas | RN-RES-004, 012 · RN-CAN-004, 005, 006, 008, 009 |

**Flujo principal — Vencimiento de pago**

1. El sistema busca reservas `PENDIENTE_PAGO` creadas hace más de 30 minutos.
2. Para cada una, verifica que no haya un pago aprobado en proceso.
3. Pasa la reserva a `CANCELADA` con el motivo "Pago no completado", cierra la cuenta y libera la disponibilidad.

**Flujo principal — No-show**

1. A las 23:59 de cada día, el sistema busca reservas `CONFIRMADA` con fecha de entrada hoy y sin check-in.
2. Para cada una, pasa la reserva a `NO_SHOW`.
3. Aplica la penalidad (primera noche) y solicita el reembolso del resto a Stripe.
4. Cierra la cuenta y libera la disponibilidad de las noches restantes.

**Flujos alternativos**

- **A1 (vencimiento, paso 2) — El webhook de pago llega justo en ese momento:** si el pago ya está `APROBADO`, la reserva no se cancela y se confirma normalmente.
- **A2 (no-show, paso 3) — Reserva sin pagos:** no se aplica penalidad (RN-CAN-006).
- **A3 (no-show, paso 3) — Reserva de canal externo:** no se procesa reembolso (RN-CAN-007).

**Postcondiciones:** no quedan reservas apartadas indefinidamente ni habitaciones bloqueadas por huéspedes que no llegaron.

---

## 10. Channel Manager

### UC-16 — Recibir reserva o cancelación de un canal externo

| Campo | Contenido |
|---|---|
| Actores | Canal externo (principal) · Administrador (configura y simula) · Recepcionista (recibe aviso) |
| Historias | HU-CM-01 a HU-CM-05 |
| Precondiciones | El canal está registrado y `ACTIVO`, con clave de API. |
| Reglas | RN-CM-001 a 007 · RN-RES-001, 002, 010 · RN-PAG-008 |

**Flujo principal — Nueva reserva**

1. El canal envía a la API una reserva con su clave, identificador externo, tipo de habitación, fechas, huéspedes, datos del huésped y monto.
2. El sistema valida la clave y que el canal esté activo.
3. El sistema verifica que no exista ya una reserva con ese identificador externo y canal.
4. El sistema valida la disponibilidad con las mismas reglas que la web.
5. El sistema crea la reserva `CONFIRMADA` con el canal de origen, la cuenta con un cargo por alojamiento igual al **monto enviado por el canal** (RN-TAR-010) y un pago `APROBADO` con método `CANAL` por ese monto.
6. El sistema registra la petición y responde al canal con el código de la reserva.
7. Recepción recibe un aviso y la reserva aparece en el Gantt con el ícono del canal.

**Flujos alternativos**

- **A1 (paso 2) — Clave inválida o canal inactivo:** se rechaza la petición y se registra.
- **A2 (paso 3) — Reserva repetida:** se responde con la reserva existente sin duplicarla.
- **A3 (paso 4) — Sin disponibilidad:** se rechaza con un error claro y se registra.
- **A4 — Cancelación desde el canal:** el canal envía el identificador externo; el sistema verifica que la reserva sea de ese canal y esté `CONFIRMADA`, la cancela sin procesar reembolso y libera la disponibilidad. Si ya estaba cancelada, responde sin cambios.
- **A5 — Canal simulado:** el Administrador genera reservas o cancelaciones desde la herramienta de simulación (HU-CM-05), que usa esta misma API.

**Postcondiciones:** la reserva externa existe una sola vez en el sistema, con su canal identificado, o fue rechazada con un motivo registrado.

---

## 11. Administración

### UC-17 — Configurar tarifas dinámicas

| Campo | Contenido |
|---|---|
| Actores | Administrador |
| Historias | HU-ADM-05, HU-ADM-09, HU-ADM-10, HU-ADM-11 |
| Precondiciones | Existen tipos de habitación con precio base. |
| Reglas | RN-TAR-001 a 007 |

**Flujo principal**

1. El Administrador crea una temporada con nombre, fechas, porcentaje de ajuste y tipos de habitación a los que aplica.
2. El sistema valida que no se traslape con otra temporada del mismo tipo y que ningún precio quede en cero o negativo.
3. El Administrador define (o ajusta) el porcentaje de fin de semana de cada tipo.
4. El Administrador abre la vista previa y revisa el precio final de cada noche.
5. Los nuevos precios aplican de inmediato a las **búsquedas y reservas nuevas**.

**Flujos alternativos**

- **A1 (paso 2) — Temporada traslapada:** se muestra el conflicto y no se guarda.
- **A2 (paso 2) — Precio resultante ≤ 0:** no se guarda.

**Postcondiciones:** las tarifas vigentes están configuradas; las reservas existentes conservan su precio.

---

### UC-18 — Gestionar inventario y reposición

| Campo | Contenido |
|---|---|
| Actores | Administrador (principal) · MYL (consume y reporta) · Sistema (alertas) |
| Historias | HU-ADM-12 a HU-ADM-15, HU-MYL-08, HU-MYL-09, HU-MYL-16 |
| Precondiciones | Existen productos registrados. |
| Reglas | RN-INV-001 a 009 |

**Flujo principal**

1. Un empleado de MYL detecta que falta un producto y crea un reporte de faltante `PENDIENTE`.
2. (En paralelo) cuando un consumo deja el stock en el mínimo o menos, el sistema alerta al Administrador.
3. El Administrador revisa los reportes y las alertas.
4. El Administrador registra la **entrada** de inventario (compra) desde el reporte.
5. El sistema aumenta el stock y pasa el reporte a `ATENDIDO`.
6. El empleado ve que su reporte fue atendido.

**Flujos alternativos**

- **A1 (paso 1) — Ya existe un reporte pendiente del mismo producto:** se sugiere actualizar el existente.
- **A2 — Conteo físico:** el Administrador registra un **ajuste** con motivo; el stock no puede quedar negativo.
- **A3 — Consumos:** Limpieza (UC-07, UC-13) y Mantenimiento (UC-08) descuentan stock; si no alcanza, el consumo se rechaza.

**Postcondiciones:** el stock refleja los movimientos reales y los faltantes quedan atendidos.

---

### UC-19 — Gestionar personal y turnos

| Campo | Contenido |
|---|---|
| Actores | Administrador |
| Historias | HU-ADM-01 a HU-ADM-04, HU-ADM-20 |
| Precondiciones | El Administrador tiene sesión. |
| Reglas | RN-PER-001 a 007 · RN-TUR-001 a 004 · R-ROL-01 a 05 |

**Flujo principal**

1. El Administrador crea un empleado con nombre, correo, teléfono, rol y (si es MYL) área.
2. El sistema crea la cuenta y envía un correo para que el empleado defina su contraseña.
3. El Administrador define los turnos (nombre y horario).
4. El Administrador asigna turnos a los empleados en la vista semanal.
5. El sistema valida que no haya traslapes y muestra el panel "personal en turno ahora".

**Flujos alternativos**

- **A1 (paso 1) — Correo ya usado por otro empleado o por un huésped:** no se permite.
- **A2 (paso 1) — Rol MYL sin área:** no se permite.
- **A3 — Desactivar empleado:** si tiene órdenes `ASIGNADA` o `EN_PROCESO`, solicitudes `EN_PROCESO` o habitaciones `EN_LIMPIEZA` a su cargo, el sistema lo impide y las lista para reasignarlas (HU-ADM-20). Si no, lo desactiva, cierra su sesión y elimina sus turnos futuros.
- **A4 (paso 4) — Turnos traslapados, empleado inactivo o rol Admin:** no se permite la asignación.

**Postcondiciones:** el personal tiene cuentas con el rol correcto y turnos asignados sin conflictos.

---

## 12. Correspondencia con los casos de uso originales

| Original | Nuevo | Cambio |
|---|---|---|
| UC-01 Buscar disponibilidad | UC-01 | Se agregan actores, validaciones y flujos alternativos |
| UC-02 Crear reserva | UC-02 | Se separan web y recepción; se integra con UC-05 |
| UC-03 Check-in | UC-03 | Se agregan validaciones de habitación y fecha |
| UC-04 Check-out | UC-04 | Se agrega el check-out desde la app y los efectos automáticos |
| UC-05 Pago online | UC-05 | Se quita la referencia a "Spring" (la tecnología se decide en el paso de arquitectura) |
| UC-06 Pedido Room Service | UC-06 | Se agregan pedido telefónico, cancelación y agotados |
| UC-07 Limpieza | UC-07 | Se agregan insumos, objetos olvidados e interrupción |
| UC-08 Mantenimiento | UC-08 | "Encargado" → Administrador; estados del documento 07 |
| — | UC-09 a UC-19 | **Nuevos** |

**Total:** 19 casos de uso (8 originales corregidos + 11 nuevos).
