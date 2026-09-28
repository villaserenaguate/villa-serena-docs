# OBJ-09 — Construir el panel de Recepción

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P4 — Web base y Recepción | OBJ-05, OBJ-06 | `feat/obj-09-a-reservas`, `-b-gantt`, `-c-estadia`, `-d-cuenta` | ~115 h |

Es el módulo más grande. Los cuatro prompts se ejecutan en orden: A → B → C → D.

## Antes de empezar

- [ ] OBJ-05 y OBJ-06 fusionados en `develop`.

---

## Prompt 09-A — Huéspedes, reservas, disponibilidad y asignación

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-09, parte A, en /panel/recepcion. Implementa HU-REC-01 a HU-REC-09 cumpliendo TODOS sus criterios de aceptación.

Lee: docs/04 - Historias de Usuario/HU - Recepcionista.md (HU-REC-01 a 09), docs/12 - Casos de Uso.md (UC-01, UC-02 A4, UC-09, UC-10), docs/09 - Matriz de Permisos.md (3.1), docs/10 - Reglas de Negocio.md (RN-RES, RN-CAN, RN-PER-006) y docs/backend-funciones.md.

1. Huéspedes: registro con validaciones, aviso de duplicados por documento o correo, rechazo de correo de empleado (HU-REC-01); acompañantes en la reserva sin superar capacidad (HU-REC-02); historial del huésped (HU-REC-03).
2. Disponibilidad con habitaciones específicas y precio total (HU-REC-04).
3. Crear reserva: huésped existente o nuevo, fechas, huéspedes, tipo o habitación, tarifa aplicada y total antes de confirmar; canal RECEPCION; nace CONFIRMADA (HU-REC-05).
4. Modificar reserva con diferencia de precio y aviso de saldo a favor (HU-REC-06); cancelar con motivo, penalidad y reembolso calculados (HU-REC-07).
5. Buscador de reservas por nombre, documento, código, fecha, estado y canal con TablaDatos (HU-REC-08); detalle de reserva con historial de estados.
6. Asignar o cambiar habitación mostrando solo las válidas (HU-REC-09).
7. Todas las operaciones usan las funciones SQL de OBJ-06; la interfaz no calcula precios ni disponibilidad por su cuenta.
8. Pruebas Playwright: crear, modificar y cancelar una reserva; asignar habitación; intento de traslape rechazado.

Criterios de terminado:
- HU-REC-01 a 09 cumplidas.
- UC-02 A4, UC-09 y UC-10 funcionan desde el panel.
````

---

## Prompt 09-B — Vista del día y calendario Gantt

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-09, parte B: la vista del día y el calendario Gantt. Implementa HU-REC-10, HU-REC-11 y HU-REC-12 (y la parte visual de HU-CM-04: ícono de canal) cumpliendo TODOS sus criterios.

Lee: HU-REC-10 a 12, HU-CM-04, docs/12 - Casos de Uso.md (UC-14), docs/07 - Estados.md (etiquetas y colores de reserva y habitación) y docs/14 - Tecnologias y Arquitectura.md (el Gantt es un componente propio con Tailwind).

1. /panel/recepcion (inicio) = vista del día: llegadas (con indicador de check-in anticipado), salidas (con saldo), reservas pendientes de pago y conteo de habitaciones por estado combinado; accesos directos a check-in y check-out (HU-REC-10).

2. Componente propio Gantt (sin librerías de pago):
   - Filas: habitaciones agrupadas por tipo; fila adicional "Sin asignar". Columnas: días.
   - Barras desde la fecha de entrada hasta la de salida; color por estado de reserva (documento 07) e ícono por canal (DIRECTO_WEB, RECEPCION, BOOKING, EXPEDIA).
   - Bloqueos visibles para habitaciones fuera de servicio.
   - Navegación por semana y mes, botón "Hoy"; encabezado de días fijo al hacer scroll horizontal.
   - Clic en una barra: resumen de la reserva con enlace al detalle.
   - Selección de un rango de días libres en una fila → abre el formulario de nueva reserva con habitación y fechas prellenadas (HU-REC-12); no permite seleccionar rangos ocupados.
   - Tiempo real: se actualiza cuando otra persona crea, modifica o cancela reservas (useSuscripcionTabla).
   - Rendimiento: debe manejar 30 habitaciones × 31 días sin trabarse.
   - NO implementar arrastrar para mover/extender (Fase 2).

3. Pruebas: Vitest de la lógica de posicionamiento de barras (fechas → columnas, reservas que cruzan meses); Playwright de crear reserva desde el Gantt.

Criterios de terminado:
- HU-REC-10, 11 y 12 cumplidas; UC-14 funciona.
- Dos navegadores abiertos: una reserva creada en uno aparece en el otro sin recargar.
````

---

## Prompt 09-C — Habitaciones, check-in y check-out

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-09, parte C. Implementa HU-REC-13, 14, 15 y 16 cumpliendo TODOS sus criterios.

Lee: HU-REC-13 a 16, docs/07 - Estados.md (secciones 3, 4 y 11), docs/12 - Casos de Uso.md (UC-03 y UC-04), docs/10 - Reglas de Negocio.md (RN-RES-014, 015; RN-HAB; RN-PAG-016) y docs/backend-funciones.md.

1. Estado de habitaciones: ocupación y condición con EstadoBadge, estado combinado del documento 07 (4.3), indicadores "Llega hoy", "Sale hoy" e "Incidencia pendiente"; filtros por ocupación, condición, tipo y piso; tiempo real; consulta de solo lectura de la incidencia de una habitación fuera de servicio (HU-REC-13).
2. Marcar habitación como SUCIA (única acción manual de condición permitida a Recepción) (HU-REC-14).
3. Check-in (HU-REC-15, UC-03): verificación de datos del principal y acompañantes (incluye lo cargado en el check-in anticipado y el documento subido), asignación si falta, validación LIBRE + LIMPIA con los flujos alternativos A1 a A4 del caso de uso, confirmación con realizar_check_in.
4. Check-out (HU-REC-16, UC-04): cuenta completa, advertencia si hay pedidos en curso, exigir saldo exactamente 0 (registrar pago o reembolso de saldo a favor desde aquí), confirmar con realizar_check_out y mostrar el resultado (incluido si la habitación quedó FUERA_DE_SERVICIO).
5. Pruebas Playwright: check-in feliz, check-in con habitación sucia rechazado, check-out con saldo pendiente bloqueado y luego exitoso.

Criterios de terminado:
- HU-REC-13 a 16 cumplidas; UC-03 y UC-04 (flujo de Recepción) funcionan.
````

---

## Prompt 09-D — Cuenta, pagos, comprobante, solicitudes y avisos

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-09, parte D. Implementa HU-REC-17 a HU-REC-23 cumpliendo TODOS sus criterios.

Lee: HU-REC-17 a 23, docs/07 - Estados.md (secciones 5, 7 y 8), docs/10 - Reglas de Negocio.md (RN-PAG, RN-LIM-005 a 008, RN-MAN-001, RN-HAB-002), docs/12 - Casos de Uso.md (UC-08 y UC-13) y docs/backend-funciones.md.

1. Cuenta del huésped (HU-REC-19): alojamiento por noche, cargos, pagos, reembolsos y saldo, en tiempo real.
2. Agregar cargos de servicios y anularlos con motivo (HU-REC-17).
3. Registrar pagos en recepción con validación de monto (HU-REC-18).
4. Comprobante PDF: botón para descargar y enviar por correo (usa /api/comprobantes de OBJ-07) (HU-REC-20).
5. Registrar solicitudes de limpieza o artículos para una habitación en estadía (misma tabla que la app, origen Recepción), con seguimiento de estado (HU-REC-21). Si la función SQL de solicitudes aún no existe (OBJ-13), coordina con P6 o créala respetando el documento 07 (Q1 a Q5).
6. Reportar daño de una habitación: crea incidencia REPORTADA; si impide el uso y está libre → FUERA_DE_SERVICIO; si está ocupada → aviso "Incidencia pendiente" (HU-REC-22). Coordina con P6 la función SQL (M1).
7. Avisos en tiempo real (toasts con enlace): nueva reserva en línea o de canal, check-out desde la app, habitación con llegada hoy pasa a LIMPIA (HU-REC-23).
8. Pruebas Playwright: cargo + pago + comprobante; solicitud creada aparece en la lista; aviso recibido al crear una reserva desde otra sesión.

Criterios de terminado:
- HU-REC-17 a 23 cumplidas.
- El módulo de Recepción completo (23 HU) pasa las pruebas en CI.
````

## Revisión humana

- [ ] Recorrer un día completo de operación con datos del seed: llegada, estadía con cargos, salida.
- [ ] Probar el Gantt con dos usuarios a la vez.
