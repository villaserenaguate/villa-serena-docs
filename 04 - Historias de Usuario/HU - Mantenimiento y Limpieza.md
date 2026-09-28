# HU — Mantenimiento y Limpieza

> **Rol:** Mantenimiento/Limpieza (`MANTENIMIENTO_LIMPIEZA`), con atributo **Área**: `LIMPIEZA`, `MANTENIMIENTO` o `AMBAS`
> **Plataforma:** Web privada (diseñada para usarse también desde el teléfono del empleado)
> **Prefijo:** `HU-MYL`
> **Total de historias:** 17
> **Referencias:** 01 — Alcance (sección G) · 02 — Definición de Roles (3.4)

Este archivo **une** los antiguos "HU - Limpieza" (15 historias) y "HU - Mantenimiento" (4 historias). Las historias repetidas o muy pequeñas se fusionaron.

**Estados usados en este archivo** (se formalizan en el documento 07):

| Elemento | Estados |
|---|---|
| Condición de la habitación | `Limpia` · `Sucia` · `En limpieza` · `Fuera de servicio` |
| Solicitud de huésped | `Pendiente → En proceso → Atendida` · `Cancelada` |
| Incidencia / orden de mantenimiento | `Reportada → Asignada → En proceso → Resuelta → Cerrada` · `Cancelada` |
| Reporte de faltante | `Pendiente → Atendido` |
| Objeto olvidado | `Registrado → Devuelto` · `Desechado` |

---

## Épica 1: Limpieza de habitaciones (Área Limpieza)

### HU-MYL-01 — Ver habitaciones pendientes de limpieza

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Limpieza de habitaciones | ALC-MYL-01 | Alta | M | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** ver las habitaciones que necesitan limpieza, ordenadas por prioridad
- **Para** organizar mi trabajo y atender primero las más urgentes

**Criterios de aceptación**
1. Se listan las habitaciones en condición `Sucia` o `En limpieza`.
2. Cada habitación muestra número, piso, condición actual y si está ocupada o libre.
3. Las habitaciones **prioritarias** aparecen primero, identificadas y con el motivo "Llegada hoy".
4. Una habitación es prioritaria si tiene una llegada programada para hoy.
5. Al marcar una habitación como `Limpia`, desaparece de la lista.
6. La lista se actualiza automáticamente.

**Reglas relacionadas:** RN-LIM-002
**Origen:** antiguas HU-LIM-01 y HU-LIM-11

---

### HU-MYL-02 — Iniciar la limpieza de una habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Limpieza de habitaciones | ALC-MYL-02 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** indicar que empecé a limpiar una habitación
- **Para** informar el progreso del servicio

**Criterios de aceptación**
1. Solo se puede iniciar la limpieza de habitaciones `Sucia`.
2. La habitación pasa a `En limpieza` y se registran la hora de inicio y el empleado.
3. Recepción ve el cambio de inmediato.
4. Si la limpieza se interrumpe, el empleado a cargo puede devolver la habitación a `Sucia`.

**Reglas relacionadas:** RN-LIM-001
**Origen:** antigua HU-LIM-02

---

### HU-MYL-03 — Finalizar la limpieza de una habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Limpieza de habitaciones | ALC-MYL-02, ALC-TRA-03 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** registrar que terminé de limpiar una habitación
- **Para** indicar que está lista para usarse

**Criterios de aceptación**
1. Solo se puede finalizar la limpieza de habitaciones `En limpieza`.
2. La habitación pasa a `Limpia` y se registran la fecha, la hora de finalización y el empleado.
3. La habitación desaparece de la lista de pendientes.
4. Si la habitación tiene una llegada hoy, Recepción recibe un aviso.

**Reglas relacionadas:** RN-LIM-001, RN-LIM-002
**Origen:** antigua HU-LIM-03

---

### HU-MYL-04 — Consultar la información de una habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Limpieza de habitaciones | ALC-MYL-04 | Media | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento/Limpieza
- **Quiero** consultar la información de una habitación
- **Para** conocer su estado y lo que necesita

**Criterios de aceptación**
1. Se muestran número, piso, tipo, ocupación y condición.
2. Se muestran las solicitudes pendientes de esa habitación con los artículos pedidos.
3. Se muestran, en solo lectura, las incidencias abiertas de la habitación y las observaciones recientes.
4. Se indica si la habitación está ocupada, pero no se muestra ningún dato personal del huésped.

**Reglas relacionadas:** R-ROL-06
**Origen:** antigua HU-LIM-12

---

### HU-MYL-05 — Registrar observaciones de una habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Limpieza de habitaciones | ALC-MYL-04 | Baja | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento/Limpieza
- **Quiero** registrar observaciones encontradas en una habitación
- **Para** informar cualquier situación importante que no sea un daño

**Criterios de aceptación**
1. Se escribe una observación asociada a una habitación.
2. Se registran la fecha, la hora y el empleado.
3. Las observaciones se pueden consultar después desde la información de la habitación.
4. Si la observación describe un daño, el sistema sugiere reportar una incidencia (HU-MYL-10).

**Origen:** antigua HU-LIM-13

---

## Épica 2: Solicitudes de huéspedes (Área Limpieza)

### HU-MYL-06 — Ver solicitudes de limpieza y artículos

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Solicitudes de huéspedes | ALC-MYL-03 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** ver las solicitudes de limpieza y artículos de los huéspedes
- **Para** atender las habitaciones que lo necesitan

**Criterios de aceptación**
1. Se listan las solicitudes `Pendiente` y `En proceso`.
2. Cada solicitud muestra número de habitación, tipo (limpieza o artículos), fecha, hora, prioridad y estado.
3. Las solicitudes de artículos muestran cada artículo y su cantidad.
4. Las solicitudes de prioridad alta aparecen primero; luego por antigüedad.
5. Las solicitudes nuevas aparecen sin recargar y con un aviso.

**Origen:** antiguas HU-LIM-04 y HU-LIM-05

---

### HU-MYL-07 — Atender y completar una solicitud

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Solicitudes de huéspedes | ALC-MYL-03, ALC-TRA-03 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** actualizar el estado de una solicitud y confirmar la entrega de artículos
- **Para** llevar control de lo pendiente y lo atendido

**Criterios de aceptación**
1. El estado avanza en orden: `Pendiente → En proceso → Atendida`.
2. Al tomar una solicitud (`En proceso`) queda asignada al empleado que la tomó.
3. Para solicitudes de artículos, al marcarla `Atendida` se confirma que los artículos y cantidades fueron entregados, y los artículos consumibles se descuentan del inventario (`CONSUMO_ENTREGA`).
4. Si no hay stock suficiente de un artículo consumible, no se puede marcar `Atendida` hasta que el Administrador registre una entrada o un ajuste.
5. Cada cambio registra fecha, hora y empleado.
6. Las solicitudes `Atendida` salen de la lista de pendientes, pero se pueden consultar en el historial.
7. El huésped ve el cambio de estado en la app.

**Reglas relacionadas:** RN-LIM-003, RN-INV-007
**Origen:** antiguas HU-LIM-06 y HU-LIM-15 (se unifica "Entregada" como `Atendida`)

---

## Épica 3: Insumos

### HU-MYL-08 — Registrar insumos utilizados

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Insumos | ALC-MYL-05 | Media | S | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** registrar los productos que usé durante una limpieza
- **Para** llevar control de los insumos consumidos

**Criterios de aceptación**
1. Al finalizar una limpieza (o después) se seleccionan los productos usados y la cantidad.
2. Solo se pueden seleccionar productos del inventario de tipo "Insumo de limpieza" o "Amenidad de habitación".
3. La cantidad se descuenta del stock del inventario.
4. No se puede registrar una cantidad mayor al stock disponible.
5. El registro queda asociado a la limpieza realizada, con fecha y empleado.

**Depende de:** HU-MYL-03, HU-ADM-12
**Origen:** antigua HU-LIM-09

---

### HU-MYL-09 — Reportar falta de insumos

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Insumos | ALC-MYL-06 | Media | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento/Limpieza
- **Quiero** reportar los productos que están por agotarse
- **Para** que el Administrador los reponga

**Criterios de aceptación**
1. Se selecciona el producto y se indica la cantidad necesaria y un comentario opcional.
2. El reporte se crea en estado `Pendiente` y lo ve el Administrador.
3. El empleado puede consultar el estado de sus reportes.
4. No se puede crear un segundo reporte `Pendiente` del mismo producto; se sugiere actualizar el existente.

**Depende de:** HU-ADM-12
**Origen:** antigua HU-LIM-10

---

## Épica 4: Reportes

### HU-MYL-10 — Reportar un daño (incidencia)

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reportes | ALC-MYL-07 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento/Limpieza
- **Quiero** reportar los daños que encuentre en una habitación
- **Para** que sean reparados

**Criterios de aceptación**
1. Se selecciona la habitación y el tipo de problema (iluminación, plomería, puertas, mobiliario, aire acondicionado, otro).
2. Se escribe una descripción (obligatoria) y se indica si el daño impide usar la habitación.
3. Se puede adjuntar una foto opcional.
4. Se crea una incidencia en estado `Reportada`, visible para el Administrador.
5. Si impide el uso y la habitación está libre, pasa a `Fuera de servicio`. Si está ocupada, se avisa a Recepción.

**Reglas relacionadas:** RN-MAN-001, RN-HAB-002
**Origen:** antigua HU-LIM-07

---

### HU-MYL-11 — Registrar un objeto olvidado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reportes | ALC-MYL-08 | Media | S | Pendiente |

**Historia**
- **Como** personal de Limpieza
- **Quiero** registrar los objetos olvidados que encuentre en las habitaciones
- **Para** facilitar su devolución al huésped

**Criterios de aceptación**
1. Se registra la habitación, la descripción del objeto y una foto opcional.
2. Se registran automáticamente la fecha, la hora y el empleado.
3. El sistema asocia el objeto con la última reserva de esa habitación.
4. El objeto queda en estado `Registrado` (pendiente de devolución) y lo ve Recepción.

**Origen:** antigua HU-LIM-08

---

### HU-MYL-12 — Registrar la devolución de un objeto olvidado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Reportes | ALC-MYL-08 | Baja | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento/Limpieza o recepcionista
- **Quiero** registrar qué pasó con un objeto olvidado
- **Para** cerrar su seguimiento

**Criterios de aceptación**
1. Se listan los objetos en estado `Registrado`, con filtros por fecha y habitación.
2. Un objeto se puede marcar como `Devuelto` (indicando a quién y cuándo) o `Desechado` (indicando el motivo).
3. El cambio registra fecha, hora y responsable.

**Depende de:** HU-MYL-11
**Origen:** NUEVO (completa ALC-MYL-08)

---

## Épica 5: Mantenimiento (Área Mantenimiento)

### HU-MYL-13 — Ver mis órdenes de mantenimiento

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Mantenimiento | ALC-MYL-09 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento
- **Quiero** ver las órdenes de mantenimiento que me asignaron
- **Para** saber qué daños debo reparar

**Criterios de aceptación**
1. Se listan las órdenes asignadas al empleado en estado `Asignada` y `En proceso`.
2. Cada orden muestra habitación, tipo de problema, descripción, prioridad (alta, media o baja) y fecha compromiso.
3. Las órdenes se ordenan por prioridad y luego por fecha compromiso.
4. Solo lo ven los empleados con área `MANTENIMIENTO` o `AMBAS`.

**Depende de:** HU-ADM-16
**Origen:** antigua HU-MAN-01 (corregida: las órdenes las asigna el Administrador)

---

### HU-MYL-14 — Iniciar una orden de mantenimiento

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Mantenimiento | ALC-MYL-09, ALC-TRA-03 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento
- **Quiero** indicar que comencé a trabajar en una orden
- **Para** informar que el problema está siendo atendido

**Criterios de aceptación**
1. Solo se pueden iniciar órdenes `Asignada` al propio empleado.
2. Se muestra toda la información del problema antes de iniciar.
3. La orden pasa a `En proceso` y se registran la hora de inicio y el empleado.

**Reglas relacionadas:** RN-MAN-002, RN-MAN-007
**Origen:** antigua HU-MAN-02

---

### HU-MYL-15 — Resolver una orden de mantenimiento

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Mantenimiento | ALC-MYL-09, ALC-TRA-03 | Alta | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento
- **Quiero** marcar una orden como resuelta
- **Para** registrar que el daño fue reparado

**Criterios de aceptación**
1. Solo se pueden resolver órdenes `En proceso` del propio empleado.
2. La descripción de la solución es obligatoria.
3. La orden pasa a `Resuelta` y se registran la fecha, la hora de finalización y el empleado.
4. El Administrador recibe aviso para revisarla y cerrarla.

**Reglas relacionadas:** RN-MAN-002, RN-MAN-007
**Origen:** antigua HU-MAN-03

---

### HU-MYL-16 — Registrar repuestos utilizados

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Mantenimiento | ALC-MYL-10 | Media | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento
- **Quiero** registrar los repuestos que usé en una reparación
- **Para** descontarlos del inventario

**Criterios de aceptación**
1. En una orden `En proceso` se seleccionan repuestos del inventario (tipo "Repuesto") y la cantidad.
2. La cantidad se descuenta del stock.
3. No se puede registrar una cantidad mayor al stock disponible.
4. Los repuestos quedan asociados a la orden.

**Reglas relacionadas:** RN-MAN-008
**Depende de:** HU-MYL-14, HU-ADM-12
**Origen:** RF-MAN-011

---

## Épica 6: Historial

### HU-MYL-17 — Consultar el historial de servicios realizados

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Historial | ALC-MYL-11 | Baja | S | Pendiente |

**Historia**
- **Como** personal de Mantenimiento/Limpieza
- **Quiero** consultar los servicios que ya se realizaron
- **Para** llevar control de las tareas atendidas

**Criterios de aceptación**
1. Se listan limpiezas finalizadas, solicitudes atendidas y órdenes resueltas o cerradas.
2. Cada registro muestra habitación, tipo de servicio, fecha, hora, empleado y estado final.
3. Se puede filtrar por habitación, fecha, tipo de servicio y empleado.
4. Las órdenes de mantenimiento muestran la descripción de la solución.

**Origen:** antiguas HU-LIM-14 y HU-MAN-04
