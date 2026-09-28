# HU — Administrador

> **Rol:** Administrador (`ADMIN`)
> **Plataforma:** Web privada
> **Prefijo:** `HU-ADM`
> **Total de historias:** 20
> **Referencias:** 01 — Alcance (sección C) · 02 — Definición de Roles (3.1)

Este archivo es **nuevo**. Antes el Administrador solo tenía 4 requisitos sueltos (RF-ADM-001 a 004), sin historias de usuario.

El Administrador también puede realizar todas las operaciones de Recepción (ver "HU - Recepcionista").

---

## Épica 1: Personal y turnos

### HU-ADM-01 — Crear un empleado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Personal y turnos | ALC-ADM-01, ALC-ADM-02 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** crear la cuenta de un nuevo empleado
- **Para** que pueda entrar al sistema con el rol que le corresponde

**Criterios de aceptación**
1. Se registran nombre completo, correo, teléfono y rol (`RECEPCION`, `ROOM_SERVICE`, `MANTENIMIENTO_LIMPIEZA` o `ADMIN`).
2. Si el rol es `MANTENIMIENTO_LIMPIEZA`, el Área (`LIMPIEZA`, `MANTENIMIENTO` o `AMBAS`) es obligatoria.
3. No se permiten dos empleados con el mismo correo.
4. El empleado recibe un correo para definir su contraseña.
5. El empleado queda en estado `Activo`.

**Reglas relacionadas:** R-ROL-01, R-ROL-03, R-ROL-05

---

### HU-ADM-02 — Editar o desactivar un empleado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Personal y turnos | ALC-ADM-01, ALC-ADM-02 | Alta | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** modificar los datos de un empleado o desactivarlo
- **Para** mantener actualizado el personal del hotel

**Criterios de aceptación**
1. Se listan los empleados con nombre, rol, área, estado y turno actual; se puede filtrar por rol y estado.
2. Se pueden editar nombre, teléfono, rol y área.
3. Un empleado se puede desactivar o reactivar; **no** se puede eliminar.
4. Un empleado desactivado no puede iniciar sesión, y si tenía sesión abierta, se cierra.
5. Su nombre se conserva en todos los registros históricos.
6. El administrador no puede desactivarse a sí mismo.
7. No se puede desactivar a un empleado con órdenes `ASIGNADA` o `EN_PROCESO`, solicitudes `EN_PROCESO` o habitaciones `En limpieza` a su cargo; se muestra la lista para reasignarlas primero (HU-ADM-20).
8. Al desactivarlo se eliminan sus asignaciones de turno futuras.

**Reglas relacionadas:** R-ROL-02, RN-PER-002, RN-PER-003, RN-PER-004
**Depende de:** HU-ADM-01

---

### HU-ADM-03 — Definir los turnos de trabajo

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Personal y turnos | ALC-ADM-03 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** definir los turnos del hotel con su horario
- **Para** organizar el trabajo del personal

**Criterios de aceptación**
1. Se crea un turno con nombre (ej. Mañana, Tarde, Noche), hora de inicio y hora de fin.
2. Un turno puede cruzar la medianoche (ej. 22:00 a 06:00).
3. Se pueden editar y desactivar turnos.
4. No se puede desactivar un turno que tenga asignaciones futuras.

---

### HU-ADM-04 — Asignar turnos a los empleados

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Personal y turnos | ALC-ADM-03 | Media | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** asignar turnos a los empleados por fecha
- **Para** saber quién trabaja en cada momento

**Criterios de aceptación**
1. Se muestra una vista semanal con los empleados y sus turnos por día.
2. Se asigna un turno a un empleado para una o varias fechas.
3. Un empleado no puede tener dos turnos que se traslapen el mismo día.
4. No se pueden asignar turnos a empleados desactivados ni al rol `ADMIN`.
5. Cada empleado puede ver sus propios turnos de la semana.
6. Se muestra un panel con el personal que está en turno en este momento.

**Reglas relacionadas:** R-ROL-04, RN-TUR-001, RN-TUR-002
**Depende de:** HU-ADM-01, HU-ADM-03

---

### HU-ADM-20 — Reasignar trabajo en curso

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Personal y turnos | ALC-ADM-01 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** pasar a otro empleado el trabajo que alguien dejó en curso
- **Para** que nada quede detenido si un empleado se ausenta o se desactiva

**Criterios de aceptación**
1. Se muestra el trabajo en curso de cada empleado: solicitudes `En proceso`, habitaciones `En limpieza` y órdenes `Asignada` o `En proceso`.
2. Una solicitud `En proceso` o una limpieza `En limpieza` se reasigna a otro empleado activo con área `LIMPIEZA` o `AMBAS`, sin cambiar su estado.
3. Las órdenes de mantenimiento se reasignan como indica HU-ADM-16.
4. Toda reasignación exige un motivo y registra fecha, hora y responsable.
5. El nuevo responsable ve el trabajo en su lista de inmediato.

**Reglas relacionadas:** RN-PER-003, RG-EST-06
**Depende de:** HU-ADM-01

---

## Épica 2: Catálogos

### HU-ADM-05 — Gestionar tipos de habitación

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Catálogos | ALC-ADM-06 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** crear y editar los tipos de habitación
- **Para** mostrarlos en la web y calcular sus precios

**Criterios de aceptación**
1. Se registran nombre, descripción, capacidad máxima de huéspedes y precio base por noche en quetzales.
2. Se pueden subir varias fotos y elegir la foto principal.
3. El precio base debe ser mayor a cero.
4. Un tipo se puede desactivar; al hacerlo deja de mostrarse en la web pública, pero conserva sus reservas existentes.
5. Los cambios se reflejan de inmediato en la web pública.

---

### HU-ADM-06 — Gestionar habitaciones

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Catálogos | ALC-ADM-06 | Alta | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** registrar las habitaciones físicas del hotel
- **Para** que puedan reservarse y asignarse

**Criterios de aceptación**
1. Se registran número de habitación (único), piso y tipo de habitación.
2. Una habitación nueva se crea en condición `Limpia` y ocupación `Libre`.
3. Se puede cambiar el tipo de una habitación solo si no tiene reservas futuras.
4. Una habitación se puede desactivar solo si no tiene reservas futuras ni está ocupada.

**Depende de:** HU-ADM-05

---

### HU-ADM-07 — Gestionar el menú de Room Service

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Catálogos | ALC-ADM-07 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** administrar los ítems del menú de Room Service
- **Para** que los huéspedes y el personal vean opciones y precios correctos

**Criterios de aceptación**
1. Se gestionan categorías (ej. Desayunos, Platos fuertes, Bebidas).
2. Cada ítem tiene nombre, descripción, categoría, precio en quetzales y foto opcional.
3. El Administrador puede marcar un ítem como `Disponible` o `Agotado`; es el único que puede reactivar un ítem agotado.
4. Un ítem se puede desactivar para que deje de aparecer en el menú.
5. Cambiar el precio no afecta los pedidos ya creados.

**Reglas relacionadas:** RN-RS-005

---

### HU-ADM-08 — Gestionar el directorio de amenidades

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Catálogos | ALC-ADM-08 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** administrar la información de las amenidades del hotel
- **Para** que los huéspedes las consulten en la app

**Criterios de aceptación**
1. Cada amenidad tiene nombre, descripción, foto, horario y ubicación.
2. Se puede definir el orden en que aparecen.
3. Una amenidad se puede activar o desactivar.
4. Los cambios se ven en la app y en la web pública sin publicar una nueva versión de la app.

---

## Épica 3: Tarifas dinámicas

**Cálculo del precio por noche:**

```
Precio de la noche = Precio base del tipo
                   × (1 + ajuste de la temporada vigente)
                   × (1 + ajuste de fin de semana, si la noche es viernes o sábado)
```

Ejemplo: precio base Q500, temporada alta +20 %, noche de sábado +10 % → Q500 × 1.20 × 1.10 = **Q660**.

### HU-ADM-09 — Gestionar temporadas

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Tarifas dinámicas | ALC-ADM-09 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** definir temporadas con un ajuste de precio
- **Para** cobrar más en temporada alta y menos en temporada baja

**Criterios de aceptación**
1. Una temporada tiene nombre, fecha de inicio, fecha de fin y porcentaje de ajuste (positivo o negativo).
2. La temporada se aplica a todos los tipos de habitación o solo a los tipos seleccionados.
3. No se permiten dos temporadas que se traslapen para un mismo tipo de habitación.
4. El ajuste no puede dejar un precio en cero o negativo.
5. Los cambios solo afectan reservas nuevas; las reservas existentes conservan su precio.

---

### HU-ADM-10 — Configurar el ajuste de fin de semana

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Tarifas dinámicas | ALC-ADM-09 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** definir un porcentaje de ajuste para las noches de fin de semana
- **Para** reflejar la mayor demanda de esos días

**Criterios de aceptación**
1. Se define un porcentaje de ajuste por tipo de habitación.
2. El ajuste se aplica a las noches de viernes y sábado.
3. Se combina con el ajuste de temporada según la fórmula de esta épica.
4. Los cambios solo afectan reservas nuevas.

---

### HU-ADM-11 — Ver la vista previa de tarifas

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Tarifas dinámicas | ALC-ADM-09 | Media | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** ver en un calendario el precio de cada noche por tipo de habitación
- **Para** verificar que las tarifas configuradas son correctas

**Criterios de aceptación**
1. Se muestra un calendario mensual con el precio final de cada noche por tipo de habitación.
2. Se distingue visualmente qué noches tienen ajuste de temporada o de fin de semana.
3. Al pasar el cursor sobre una noche se ve el desglose del cálculo.
4. Los precios coinciden exactamente con los que ve el cliente en la web pública.

**Depende de:** HU-ADM-09, HU-ADM-10

---

## Épica 4: Inventario

### HU-ADM-12 — Gestionar productos del inventario

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Inventario | ALC-ADM-04 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** registrar los productos que maneja el hotel
- **Para** controlar su existencia

**Criterios de aceptación**
1. Cada producto tiene nombre, categoría (`INSUMO_LIMPIEZA`, `AMENIDAD_HABITACION` o `REPUESTO`), unidad de medida y stock mínimo.
2. El stock inicial es cero; solo cambia con movimientos (HU-ADM-13).
3. No se permiten dos productos con el mismo nombre.
4. Un producto se puede desactivar; deja de aparecer para registrar consumos.
5. El Administrador gestiona el **catálogo de artículos para el huésped** (nombre, cantidad máxima por solicitud, activo).
6. Cada artículo del catálogo puede vincularse a un producto `AMENIDAD_HABITACION` para descontar stock al entregarse; la lencería (toallas, almohadas, cobijas) no se vincula.

**Reglas relacionadas:** RN-INV-006, RN-INV-007, RN-INV-008
**Notas técnicas:** ver documento 08, secciones 3 y 4.

---

### HU-ADM-13 — Registrar entradas y ajustes de inventario

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Inventario | ALC-ADM-04 | Alta | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** registrar compras y ajustes de inventario
- **Para** mantener el stock correcto

**Criterios de aceptación**
1. Una **entrada** aumenta el stock (ej. compra) e indica producto, cantidad y nota.
2. Un **ajuste** corrige el stock (ej. conteo físico) e indica un motivo obligatorio.
3. El stock nunca puede quedar negativo.
4. Cada movimiento registra fecha, hora, tipo y responsable.
5. Se puede ver el historial de movimientos de cada producto, incluidos los consumos de Limpieza y Mantenimiento.

**Depende de:** HU-ADM-12

---

### HU-ADM-14 — Consultar stock y alertas de stock mínimo

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Inventario | ALC-ADM-04 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** ver el stock actual y qué productos están por debajo del mínimo
- **Para** reponerlos a tiempo

**Criterios de aceptación**
1. Se listan los productos con stock actual, stock mínimo y categoría.
2. Los productos con stock igual o menor al mínimo se destacan como alerta.
3. Se puede filtrar por categoría y por "solo alertas".
4. Cuando un producto cae por debajo del mínimo, el Administrador ve un aviso.

**Depende de:** HU-ADM-13

---

### HU-ADM-15 — Atender reportes de faltantes

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Inventario | ALC-ADM-05 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** ver y atender los reportes de faltantes del personal
- **Para** reponer lo que se necesita

**Criterios de aceptación**
1. Se listan los reportes `Pendiente` con producto, cantidad solicitada, empleado y fecha.
2. Al atender un reporte se puede registrar la entrada de inventario correspondiente en el mismo paso.
3. El reporte pasa a `Atendido` con fecha y responsable.
4. El empleado que reportó ve el cambio de estado.

**Depende de:** HU-MYL-09, HU-ADM-13

---

## Épica 5: Supervisión de mantenimiento

### HU-ADM-16 — Revisar incidencias y asignarlas

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Supervisión de mantenimiento | ALC-ADM-11 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** revisar las incidencias reportadas y asignarlas a un técnico
- **Para** que los daños se reparen

**Criterios de aceptación**
1. Se listan las incidencias `Reportada` con habitación, tipo, descripción, quién la reportó y si impide el uso.
2. Al asignar se elige un empleado con área `MANTENIMIENTO` o `AMBAS`, una prioridad (alta, media o baja) y una fecha compromiso.
3. La incidencia pasa a `Asignada` (se convierte en orden de trabajo) y el técnico la ve en su lista.
4. Al elegir técnico, se sugieren primero los que están en turno; si el elegido no está en turno, se muestra una advertencia sin impedir la asignación.
5. Una incidencia solo se puede asignar una vez; después solo se puede reasignar a otro técnico, con motivo.
6. El Administrador puede ver todas las órdenes en cualquier estado, con filtros por estado, habitación y técnico.

**Reglas relacionadas:** RN-MAN-001, RN-MAN-002
**Depende de:** HU-MYL-10, HU-REC-22

---

### HU-ADM-17 — Cerrar o cancelar una orden de mantenimiento

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Supervisión de mantenimiento | ALC-ADM-11 | Alta | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** cerrar las órdenes resueltas o cancelar las que no proceden
- **Para** dar por terminado el trabajo y liberar la habitación

**Criterios de aceptación**
1. Solo se pueden cerrar órdenes `Resuelta`; se muestra la solución y los repuestos usados.
2. Al cerrar, si la habitación estaba `Fuera de servicio` y no tiene otras órdenes abiertas que impidan su uso, pasa a `Sucia` (debe limpiarse antes de estar disponible).
3. Se puede cancelar una orden en cualquier estado antes de `Cerrada`, con motivo obligatorio.
4. Si una orden resuelta no quedó bien, se puede devolver a `En proceso` con un comentario.
5. Cada cambio registra fecha, hora y responsable.

**Reglas relacionadas:** RN-MAN-003, RN-MAN-004, RN-MAN-005, RN-MAN-006, RN-HAB-003
**Depende de:** HU-MYL-15

---

## Épica 6: Indicadores y configuración

### HU-ADM-18 — Ver los indicadores del hotel

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Indicadores y configuración | ALC-ADM-10 | Media | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** ver los indicadores principales del hotel
- **Para** tomar decisiones sobre la operación

**Criterios de aceptación**
1. Se muestra el porcentaje de ocupación (habitaciones ocupadas / habitaciones activas) de hoy y de un rango de fechas.
2. Se muestran los ingresos del rango (cargos vigentes según su fecha, incluidas las penalidades), separados en alojamiento, room service y otros servicios.
3. Se muestra el número de reservas por canal de origen (Directo web, Recepción, Booking, Expedia).
4. Se puede elegir el rango de fechas; por defecto, el mes actual.
5. Solo el Administrador puede ver esta pantalla.

**Reglas relacionadas:** RN-PAG-017

---

### HU-ADM-19 — Configurar los datos generales del hotel

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Indicadores y configuración | ALC-PUB-01, ALC-ADM-08 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** configurar los datos generales del hotel
- **Para** que la web, la app y los correos muestren información correcta

**Criterios de aceptación**
1. Se configuran nombre, descripción, dirección, teléfono, correo de contacto y fotos del hotel.
2. Se configuran la hora de check-in y la hora de check-out.
3. Se configuran el nombre y la contraseña de la red Wi-Fi para huéspedes.
4. Se configura el texto de la política de cancelación que ven los huéspedes.
5. Los cambios se reflejan de inmediato en la web pública, la app y los correos.

**Notas técnicas:** solo las horas de check-in y check-out son configurables (PAR-01, PAR-02); el resto de parámetros son constantes del documento 10. El texto de la política debe coincidir con RN-CAN-002 a RN-CAN-007.
