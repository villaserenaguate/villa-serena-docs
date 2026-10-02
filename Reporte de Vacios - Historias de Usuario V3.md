# Reporte de vacíos — Historias de Usuario V3

> **Fecha:** 30 de septiembre de 2026
> **Revisado:** las 68 historias de `Documentación V3 / 04 - Historias de Usuario` (índice + 7 archivos).
> **Regla de las propuestas:** ninguna agrega pantallas, estados ni procesos. Cada propuesta **corrige una frase**, **acepta una limitación** o **quita algo**.
> **Estado (30 sep):** aplicado en las historias **todo lo aprobado**: V-01 a V-06, R-01 a R-04 y X-01 a X-07. También se aplicó la nota de los datos fiscales (sin bloqueo del check-out) y la opción extra: la serie y el número inicial solo vienen en los datos iniciales y no se editan.

---

## Resumen

| Tipo | Cantidad | Efecto en el trabajo |
|---|---|---|
| A. Vacíos (algo que el sistema no sabe resolver) | 6 | 1 estado y 1 proceso programado menos; el resto, sin cambio |
| B. Contradicciones de redacción | 4 | Sin cambio (solo texto) |
| C. Extras que suman trabajo sin ser necesarios | 7 | Menos validaciones y menos pruebas |
| **Total** | **17** | **El trabajo baja; nada sube** |

---

## A. Vacíos

### V-01 — El estado `No-show` existe, pero ninguna historia dice cuándo ocurre

- **Qué sucede:** el índice lista `No-show` como estado de la reserva y el Gantt lo oculta (HU-REC-08), pero ninguna historia dice quién o qué pone una reserva en `No-show`. Si el huésped nunca llega, la reserva se queda `Confirmada` para siempre y **sigue ocupando cupo** en la disponibilidad.
- **Propuesta (quitar):** eliminar el estado `No-show`. Si el huésped no llega, Recepción lo **cancela** con HU-REC-05 y motivo "No se presentó". Como faltan menos de 48 horas, la regla ya existente da **sin reembolso**: el resultado es igual al de un no-show.
- **Cambio en las HU:** índice (lista de estados) y HU-REC-08 criterio 2 ("las reservas `Cancelada` no se muestran").
- **Efecto:** un estado y un proceso programado menos.

### V-02 — Un huésped que llega después de medianoche no puede hacer check-in

- **Qué sucede:** HU-REC-12 solo permite el check-in si la fecha de entrada es **hoy**. Quien llega a la 1:00 a. m. del día siguiente ya no puede entrar, aunque haya pagado.
- **Propuesta (misma condición, otra forma):** permitir el check-in de una reserva `Confirmada` si **hoy está entre la fecha de entrada y el día anterior a la salida**. Es la misma comparación de fechas; no agrega trabajo. Las noches no usadas se cobran igual (no hay ajustes).
- **Cambio en las HU:** HU-REC-12 criterio 1.

### V-03 — El huésped que no hace check-out a tiempo

- **Qué sucede:** ninguna historia dice qué pasa si el huésped sigue en la habitación después de la hora de salida. La app cierra su ventana de check-out (HU-HUE-16) y la reserva queda `En estadía` con la fecha ya vencida.
- **Propuesta (aceptar):** no hacer nada automático. Recepción hace el check-out cuando el huésped baje (HU-REC-14 no limita la fecha), **sin cargo por salida tarde**. Si la habitación tenía otra llegada, el check-in se bloquea solo porque no está `Libre` + `Limpia` (regla que ya existe).
- **Cambio en las HU:** una nota en HU-REC-14.

### V-04 — No hay forma de recuperar el acceso si el único Administrador olvida su contraseña

- **Qué sucede:** solo el Administrador restablece contraseñas (HU-ADM-02). El primer Administrador se crea con variables de entorno **solo si no existe ninguno**. Si el único Administrador olvida su contraseña, nadie puede entrar a administrar.
- **Propuesta (aceptar, sin código):** en los datos de prueba se crean **dos** Administradores. En local, además, se puede reiniciar la base. No se construye "olvidé mi contraseña" para el personal.
- **Cambio en las HU:** ninguno; una línea en la tarea técnica de datos iniciales (ver V-06).

### V-05 — No hay correo al cancelar una reserva

- **Qué sucede:** HU-REC-05 cancela y reembolsa, pero no avisa al cliente. Quien reservó por la web no se entera por el sistema.
- **Propuesta (aceptar):** Recepción avisa por teléfono o por correo propio. Un correo nuevo sería otra plantilla, otro evento y otra prueba.
- **Cambio en las HU:** una nota en HU-REC-05: "no se envía correo de cancelación".

### V-06 — Datos que el sistema necesita al arrancar y que no tienen una tarea

- **Qué sucede:** varias historias dicen "se cargan en los datos iniciales", pero esa carga no aparece en la lista de tareas técnicas del índice. Faltan: los canales y sus claves (HU-CM-03), el catálogo de artículos con su máximo (HU-HUE-13), los datos del hotel y fiscales con la serie (HU-ADM-08) y los usuarios de prueba de cada rol.
- **Propuesta (ordenar, no agregar):** una sola tarea técnica, **"Datos iniciales (Flyway)"**, que reúna todo eso. Ese trabajo ya se tenía que hacer; solo queda visible y asignado.
- **Cambio en las HU:** índice, sección de tareas técnicas (una fila nueva).

---

## B. Contradicciones de redacción

### R-01 — Las horas de check-in y check-out están escritas fijas y además son configurables

- **Qué sucede:** HU-ADM-08 permite cambiar las horas (15:00 y 12:00), pero HU-HUE-01, HU-HUE-07, HU-REC-05, HU-REC-12 y HU-HUE-16 las escriben fijas. Si el Administrador las cambia, el texto y la regla de 48 horas no coinciden.
- **Propuesta (quitar):** **las horas son fijas**: check-in 15:00 y check-out 12:00, como constantes. Se borra HU-ADM-08 criterio 2. Así desaparece la contradicción y una opción de configuración.
- **Cambio en las HU:** HU-ADM-08 criterio 2 (eliminar); quitar "configurable" en HU-HUE-16 criterio 1.

### R-02 — ¿El Administrador puede usar las pantallas de Recepción?

- **Qué sucede:** el encabezado de HU - Recepcionista dice "El Administrador también puede realizar todas estas operaciones", pero HU - Administrador no lo dice, y también le permite reportar daños (HU-MYL-06).
- **Propuesta (quitar):** cada rol usa **solo sus pantallas**. El Administrador tiene las suyas y el canal simulado (necesario para la demostración). En la demostración se entra con el usuario de cada rol. Así hay menos menús por rol y menos combinaciones de permisos que probar.
- **Cambio en las HU:** borrar la frase del encabezado de HU - Recepcionista y la mención a HU-MYL-06 en HU - Administrador.

### R-03 — "Una sola sesión de pago por reserva" choca con el pago del check-out en la app

- **Qué sucede:** HU-HUE-06 criterio 4 dice "una sola sesión de pago por reserva", pero HU-HUE-16 crea **otra** sesión de Stripe para el saldo del check-out.
- **Propuesta (texto):** cambiarlo a "una sola sesión de pago **para el pago de la reserva**".
- **Cambio en las HU:** HU-HUE-06 criterio 4.

### R-04 — HU-HUE-16 depende de HU-REC-14

- **Qué sucede:** el check-out de la app aparece como dependiente del check-out de Recepción. En realidad las dos usan el mismo servicio del backend, y quien haga la app podría pensar que debe esperar a la pantalla de Recepción.
- **Propuesta (texto):** cambiar la dependencia por "usa el mismo servicio de check-out del backend que HU-REC-14".
- **Cambio en las HU:** HU-HUE-16, campo "Depende de".

---

## C. Extras que suman trabajo y no son necesarios para que el sistema funcione

Cada uno pasa la prueba "¿se necesita para funcionar o para presentar?" con un **no**.

| # | Historia | Qué pide hoy | Propuesta |
|---|---|---|---|
| X-01 | HU-ADM-04 crit. 5 | Antes de desactivar una habitación, revisar noche por noche que su tipo no se quede sin cupo | Desactivar solo si **no está `Ocupada` y no tiene reservas asignadas**. Se quita el cálculo por noches. |
| X-02 | HU-ADM-02 crit. 5 | Validar que siempre quede al menos un Administrador `Activo` | Basta con "**no puede desactivarse ni cambiarse el rol a sí mismo**": quien desactiva es un Administrador activo, así que siempre queda uno. Se quita la consulta del "último". |
| X-03 | HU-ADM-02 crit. 7 | Registro con fecha, hora y responsable de desactivar, reactivar y restablecer la contraseña | Quitar. El historial de cambios (ALC-TRA-03) ya cubre reservas, habitaciones, pedidos e incidencias, que es lo que se muestra. |
| X-04 | HU-REC-17 | Aviso de reservas futuras asignadas cuando la habitación queda `Fuera de servicio` | Quitar el aviso. Recepción ve el problema en el Gantt y reasigna con HU-REC-07. |
| X-05 | HU-RS-01 crit. 7 y HU-RS-07 crit. 5 | Indicador "Sin conexión" y recarga de lo que llegó mientras tanto | Quitar el indicador. La librería STOMP se reconecta sola; al reconectar, se recarga la cola (una línea). |
| X-06 | HU-RS-07 | Aviso con **sonido** (con un clic para activarlo por el bloqueo del navegador) | Dejar solo el aviso visual. El sonido pasa a Nivel 2. |
| X-07 | HU-REC-15 crit. 4 | Desglose de impuestos (pendiente de aprobar en RN-FAC-003) | Mostrar solo **"Total (IVA incluido)"**, sin desglose. Esto cierra el pendiente RN-FAC-003. |

> **Relación con los datos fiscales:** si se aplica V-06 (datos fiscales en la carga inicial), también se puede quitar el bloqueo por "faltan datos de facturación" (HU-ADM-08 crit. 5, HU-REC-15 crit. 6 y HU-HUE-16 crit. 5): los datos siempre existen desde el arranque. Se queda solo la validación de que los campos no se guarden vacíos.

---

## Revisados y sin vacío (no requieren cambio)

- **Huésped con dos reservas a la vez:** HU-HUE-09 ya tiene el selector.
- **Pago aprobado en la app, pero sin terminar el check-out:** el saldo queda en 0 y Recepción termina el check-out sin pedir pago (HU-REC-14 crit. 3).
- **Huéspedes adicionales sin acceso a la app:** es una decisión que ya está escrita (HU-REC-02 crit. 3).
- **Recepción no puede crear pedidos o solicitudes por el huésped:** es una limitación aceptada; el huésped usa la app.
- **Reembolso rechazado por Stripe:** la reserva no se cancela (HU-REC-05).

---

## Para decidir

Marca qué aplicar y lo paso a las historias en un solo cambio:

1. **A (V-01 a V-06):** ¿se aplican todos?
2. **B (R-01 a R-04):** ¿horas fijas (R-01) y Administrador solo con sus pantallas (R-02)?
3. **C (X-01 a X-07):** ¿se quitan todos, incluida la nota de los datos fiscales?
