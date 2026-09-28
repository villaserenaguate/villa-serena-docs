# HU — Channel Manager

> **Actores:** Administrador (`ADMIN`), Recepcionista (`RECEPCION`) y Canal externo (`CANAL`, actor no humano)
> **Plataformas:** Web privada y API
> **Prefijo:** `HU-CM`
> **Total de historias:** 5
> **Referencias:** 01 — Alcance (sección D, decisión D-05) · 02 — Definición de Roles (sección 4)

Este archivo es **nuevo**. El enunciado pide *"preparar el sistema para conectarse con un Channel Manager (Booking, Expedia)"*. Estas historias implementan esa preparación contra un **canal simulado**. La conexión real queda en Fase 2 (F2-10).

**ALC-CM-01** (documento de diseño de la integración) no es una historia de usuario. Es un entregable técnico de Arquitectura (Josué) y se elabora en el paso de tecnologías.

---

## Épica 1: Configuración de canales

### HU-CM-01 — Registrar un canal y generar su clave de acceso

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Configuración de canales | ALC-CM-03 | Media | S | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** registrar un canal externo y generar su clave de API
- **Para** que solo los canales autorizados puedan enviar reservas

**Criterios de aceptación**
1. Se registra un canal (Booking o Expedia) con su estado (activo o inactivo); cada canal corresponde a un canal de origen (`BOOKING` o `EXPEDIA`).
2. El sistema genera una clave de API única por canal y la muestra **una sola vez**.
3. La clave se puede regenerar; la anterior deja de funcionar de inmediato.
4. Un canal inactivo no puede enviar reservas.
5. Se muestra la cantidad de reservas recibidas por cada canal.
6. El canal simulado (HU-CM-05) usa las claves de estos canales; no se registra como un canal aparte.

**Notas técnicas:** la clave se guarda cifrada (hash), nunca en texto plano.

---

## Épica 2: Recepción de reservas externas

### HU-CM-02 — Recibir una reserva desde un canal externo

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| API | Recepción de reservas externas | ALC-CM-03 | Alta | L | Pendiente |

**Historia**
- **Como** canal externo
- **Quiero** enviar una reserva al sistema del hotel por API
- **Para** que la reserva hecha en mi plataforma quede registrada en Villa Serena

**Criterios de aceptación**
1. La API acepta una reserva con: identificador externo, tipo de habitación, fechas, número de huéspedes, datos del huésped principal y monto.
2. La petición debe incluir una clave de API válida de un canal activo; si no, se rechaza.
3. El sistema valida la disponibilidad con las mismas reglas que la web pública; si no hay, rechaza la reserva con un error claro.
4. Si llega de nuevo una reserva con el mismo identificador externo y canal, **no** se duplica; se responde con la reserva ya existente.
5. La reserva se crea en estado `Confirmada`, con el canal de origen y un código propio; el cargo por alojamiento es el **monto enviado por el canal** y se registra un pago `Aprobado` con método `CANAL` por ese monto, porque el canal ya cobró al huésped.
6. Cada petición recibida (aceptada o rechazada) queda registrada con fecha, canal y resultado.
7. La API está documentada con OpenAPI.

**Reglas relacionadas:** RN-RES-001, RN-RES-002, RN-TAR-010, RN-PAG-008, RN-CM-003, RN-CM-004
**Depende de:** HU-CM-01

---

### HU-CM-03 — Recibir la cancelación de una reserva externa

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| API | Recepción de reservas externas | ALC-CM-03 | Media | M | Pendiente |

**Historia**
- **Como** canal externo
- **Quiero** notificar al hotel que una reserva fue cancelada en mi plataforma
- **Para** que la habitación se libere en Villa Serena

**Criterios de aceptación**
1. La API recibe la cancelación con el identificador externo de la reserva y una clave de API válida.
2. Solo el canal que creó la reserva puede cancelarla.
3. Solo se cancelan reservas `Confirmada`; si ya está `En estadía` o `Finalizada`, se rechaza con un error claro.
4. La reserva pasa a `Cancelada` con motivo "Cancelada por el canal" y la habitación se libera.
5. Si la cancelación llega dos veces, la segunda no produce cambios ni errores.

**Reglas relacionadas:** RN-RES-004
**Depende de:** HU-CM-02

---

## Épica 3: Visibilidad del canal

### HU-CM-04 — Identificar el canal de origen de cada reserva

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Visibilidad del canal | ALC-CM-02 | Alta | S | Pendiente |

**Historia**
- **Como** recepcionista
- **Quiero** saber por qué canal llegó cada reserva
- **Para** atender correctamente al huésped y conocer de dónde vienen las reservas

**Criterios de aceptación**
1. Toda reserva tiene un canal de origen: `Directo web`, `Recepción`, `Booking` o `Expedia`.
2. El canal se muestra en el detalle de la reserva, en las búsquedas y como ícono en el calendario Gantt.
3. Las reservas de canales externos muestran también su identificador externo.
4. Se puede filtrar la búsqueda de reservas por canal.
5. Recepción recibe un aviso en pantalla cuando llega una reserva de un canal externo.

**Depende de:** HU-CM-02, HU-REC-08, HU-REC-11

---

## Épica 4: Canal simulado

### HU-CM-05 — Enviar reservas de prueba desde el canal simulado

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Canal simulado | ALC-CM-04 | Alta | M | Pendiente |

**Historia**
- **Como** administrador
- **Quiero** generar reservas de prueba desde un canal simulado
- **Para** demostrar que el sistema está preparado para integrarse con Booking o Expedia

**Criterios de aceptación**
1. Existe una pantalla (o herramienta separada) que imita a un canal externo.
2. Se puede elegir el canal a simular (Booking o Expedia), el tipo de habitación, las fechas y los datos del huésped, o generarlos al azar.
3. La reserva se envía **a través de la API real** (HU-CM-02), usando la clave del canal.
4. Se muestra la respuesta de la API (aceptada o rechazada, y el motivo).
5. Se puede enviar la cancelación de una reserva simulada (HU-CM-03).
6. Se puede reenviar la misma reserva para demostrar que no se duplica.

**Depende de:** HU-CM-02, HU-CM-03
