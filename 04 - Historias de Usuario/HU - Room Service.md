# HU — Room Service

> **Rol:** Room Service (`ROOM_SERVICE`)
> **Plataforma:** Web privada
> **Prefijo:** `HU-RS`
> **Total de historias:** 10
> **Referencias:** 01 — Alcance (sección F) · 02 — Definición de Roles (3.3)

Estas historias conservan la numeración original (HU-01 a HU-10 → HU-RS-01 a HU-RS-10) y agregan criterios faltantes.

**Flujo de estados del pedido:** `Nuevo → En preparación → En camino → Entregado`, con `Cancelado` posible desde cualquier estado antes de `Entregado`.

---

## Épica 1: Gestión de pedidos

### HU-RS-01 — Ver pedidos activos

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Gestión de pedidos | ALC-RS-01 | Alta | M | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** ver la lista de pedidos pendientes
- **Para** saber cuáles debo atender primero

**Criterios de aceptación**
1. Se muestran los pedidos en estado `Nuevo`, `En preparación` y `En camino`.
2. Cada pedido muestra número de habitación, hora del pedido, tiempo transcurrido y estado.
3. Los pedidos se ordenan por antigüedad (el más antiguo primero).
4. Cada estado se distingue visualmente (color o etiqueta).
5. La lista se actualiza automáticamente cuando llega un pedido nuevo o cambia un estado.
6. Se muestran todos los pedidos activos, sin importar el turno en que se crearon (el filtro por turno está en HU-RS-09).

**Reglas relacionadas:** RN-RS-001

---

### HU-RS-02 — Ver el detalle de un pedido

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Gestión de pedidos | ALC-RS-02 | Alta | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** ver el detalle completo de un pedido
- **Para** prepararlo correctamente

**Criterios de aceptación**
1. Se muestran ítems, cantidades, precio de cada ítem y total.
2. Se muestran las notas especiales (alergias, preferencias) de forma destacada.
3. Se muestran nombre del huésped, número de habitación y piso.
4. Se muestra el origen del pedido (App o Teléfono) y el historial de estados con fecha y hora.

**Depende de:** HU-RS-01

---

### HU-RS-03 — Registrar un pedido telefónico

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Gestión de pedidos | ALC-RS-03 | Media | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** registrar un pedido que el huésped hizo por teléfono
- **Para** que quede en el sistema igual que uno hecho desde la app

**Criterios de aceptación**
1. Solo se pueden seleccionar habitaciones con una reserva `En estadía`.
2. Se seleccionan ítems del menú y cantidades; los ítems agotados no se pueden seleccionar.
3. Se pueden agregar notas u observaciones.
4. El pedido se guarda en estado `Nuevo`, con origen "Teléfono" y el empleado que lo registró.
5. El huésped puede ver el pedido en su app.

**Reglas relacionadas:** RN-RS-005

---

### HU-RS-04 — Actualizar el estado de un pedido

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Gestión de pedidos | ALC-RS-04, ALC-TRA-03 | Alta | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** avanzar el estado de un pedido
- **Para** que los demás roles y el huésped conozcan su progreso

**Criterios de aceptación**
1. El estado solo avanza en orden: `Nuevo → En preparación → En camino → Entregado`.
2. No se permite saltar estados ni retroceder.
3. Cada cambio registra fecha, hora y empleado responsable.
4. El huésped ve el nuevo estado en la app sin recargar.
5. Un pedido `Entregado` ya no se puede modificar.

**Reglas relacionadas:** RN-RS-001, RN-RS-002

---

### HU-RS-05 — Cancelar un pedido

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Gestión de pedidos | ALC-RS-05 | Media | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** cancelar un pedido indicando el motivo
- **Para** llevar control de las incidencias (ítem agotado, error de registro, etc.)

**Criterios de aceptación**
1. Solo se pueden cancelar pedidos que no estén `Entregado`.
2. El motivo es obligatorio.
3. El pedido pasa a `Cancelado` y no puede reactivarse; solo se consulta en el historial.
4. Un pedido cancelado no genera cargo en la cuenta del huésped.
5. El huésped ve en la app que su pedido fue cancelado y el motivo.

**Reglas relacionadas:** RN-RS-003, RN-RS-004

---

## Épica 2: Menú

### HU-RS-06 — Consultar el menú

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Menú | ALC-RS-06 | Media | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** consultar el menú con precios y disponibilidad
- **Para** tomar pedidos sin errores

**Criterios de aceptación**
1. El menú se muestra por categorías con nombre, descripción, precio y disponibilidad (`Disponible` / `Agotado`).
2. Se puede buscar un ítem por nombre.
3. Los ítems agotados no se pueden agregar a nuevos pedidos.

**Reglas relacionadas:** RN-RS-005
**Depende de:** HU-ADM-07

---

### HU-RS-07 — Marcar un ítem como agotado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Menú | ALC-RS-07 | Media | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** marcar un ítem del menú como agotado
- **Para** evitar que se sigan haciendo pedidos con ese producto

**Criterios de aceptación**
1. Room Service puede marcar un ítem como `Agotado`.
2. Un ítem agotado aparece como no disponible en la app del huésped de inmediato.
3. Solo el Administrador puede volver a marcarlo como `Disponible` (HU-ADM-07).
4. Los pedidos ya creados con ese ítem no se modifican.

**Reglas relacionadas:** RN-RS-005

---

## Épica 3: Facturación

### HU-RS-08 — Cargar el pedido a la cuenta de la habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Facturación | ALC-RS-08 | Alta | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** que el importe del pedido se agregue automáticamente a la cuenta del huésped
- **Para** que se cobre al hacer el check-out

**Criterios de aceptación**
1. El cargo se genera automáticamente cuando el pedido pasa a `Entregado`.
2. El monto es la suma de precio × cantidad de cada ítem, con el precio vigente al momento del pedido.
3. El cargo queda asociado a la reserva y habitación, con el concepto "Room Service — Pedido #".
4. El cargo es visible de inmediato para Recepción y para el huésped en la app.
5. Un pedido genera un solo cargo, aunque el cambio a `Entregado` se reciba dos veces.

**Depende de:** HU-RS-04, HU-REC-19

---

## Épica 4: Historial

### HU-RS-09 — Consultar el historial de pedidos

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Historial | ALC-RS-09 | Baja | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** consultar los pedidos entregados y cancelados
- **Para** verificar que todo se completó correctamente

**Criterios de aceptación**
1. Se puede filtrar por fecha, habitación, estado y turno.
2. Por defecto se muestran los pedidos del turno actual.
3. Se muestra el tiempo total desde que se creó el pedido hasta que se entregó.
4. Los pedidos cancelados muestran su motivo.

**Depende de:** HU-ADM-04

---

## Épica 5: Notificaciones

### HU-RS-10 — Recibir aviso de un pedido nuevo

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Notificaciones | ALC-RS-10, ALC-TRA-04 | Alta | S | Pendiente |

**Historia**
- **Como** encargado de Room Service
- **Quiero** recibir un aviso cuando llega un pedido nuevo
- **Para** atenderlo sin estar revisando la pantalla constantemente

**Criterios de aceptación**
1. Cuando llega un pedido nuevo se muestra un aviso en pantalla con sonido.
2. El aviso incluye número de habitación y hora del pedido.
3. Al hacer clic en el aviso se abre el detalle del pedido.
4. El aviso llega sin recargar la página.
