# OBJ-06 — Levantar el backend de reservas, cuentas y estadía

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P2 — Lógica de negocio | OBJ-01, OBJ-02 | `feat/obj-06-a-reservas`, `-b-cuenta`, `-c-estadia` | ~30 h |

**Es el cuello de botella del proyecto:** casi todos los módulos dependen de él. Conviene terminar A lo antes posible.

## Antes de empezar

- [ ] OBJ-01 y OBJ-02 fusionados en `develop`.
- [ ] Los tres prompts se ejecutan en orden: A → B → C.

---

## Prompt 06-A — Disponibilidad, precio y creación de reservas

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-06, parte A: implementar en SQL (funciones RPC en migraciones nuevas) la disponibilidad, el cálculo de precios y la creación de reservas.

Lee completos: docs/07 - Estados.md (secciones 3, 5 y 11), docs/10 - Reglas de Negocio.md (RN-RES, RN-TAR, RN-PAG-006 a 009, PAR-01 a 06, 10 a 13, 21), docs/12 - Casos de Uso.md (UC-01 y UC-02) y las HU: HU-HUE-03, 04, 05; HU-REC-04, 05, 09.

1. calcular_precio_noche(tipo_habitacion_id, fecha) y calcular_precio_estadia(tipo_habitacion_id, fecha_entrada, fecha_salida):
   - Precio = base × (1 + ajuste de temporada vigente para ese tipo) × (1 + ajuste de fin de semana si la noche es viernes o sábado) (RN-TAR-001, 002).
   - Redondeo a 2 decimales por noche; total = suma de noches (RN-TAR-006).
   - Devuelve el desglose por noche (fecha, precio, si aplicó temporada y/o fin de semana).
   - Siempre > 0 (RN-TAR-004).

2. consultar_disponibilidad(fecha_entrada, fecha_salida, huespedes):
   - Validaciones: fechas no pasadas (hora de Guatemala), 1–30 noches, máximo 365 días (RN-RES-005, 006).
   - Para cada tipo activo con capacidad suficiente: habitaciones disponibles = habitaciones activas del tipo no bloqueadas por mantenimiento − reservas activas (PENDIENTE_PAGO, CONFIRMADA, EN_ESTADIA) de ese tipo que ocupan CADA noche (con o sin habitación asignada) (RN-RES-008). Se muestra el mínimo del rango.
   - Devuelve tipo, cantidad disponible, precio total y desglose.
   - Ejecutable por anon (SECURITY DEFINER), sin exponer datos de reservas (RN-SEG-004).
   - Variante para Recepción que además liste habitaciones específicas libres (HU-REC-04, UC-01 A3).

3. crear_reserva(...) para web (HUESPED/anon) y para Recepción:
   - Bloqueo para evitar sobreventa en concurrencia (por ejemplo, pg_advisory_xact_lock por tipo de habitación, o SELECT … FOR UPDATE), y revalidación de disponibilidad dentro de la misma transacción (RN-RES-001).
   - Crea o reutiliza el huésped por documento/correo; registra datos obligatorios.
   - Código de reserva único no secuencial (ej. "VS-" + 6 caracteres) (RN-RES-009).
   - Canal: DIRECTO_WEB → estado PENDIENTE_PAGO; RECEPCION → CONFIRMADA (RN-RES-011).
   - Si se indica habitación específica, valida que sea del tipo y esté libre (RN-RES-013).
   - Crea la cuenta ABIERTA con el cargo por alojamiento (monto de calcular_precio_estadia) (RN-PAG-009).
   - Registra en historial_estados la creación.
   - Devuelve id, código, estado y total.

4. asignar_habitacion(reserva_id, habitacion_id): solo en PENDIENTE_PAGO, CONFIRMADA o EN_ESTADIA; del tipo reservado; sin traslapes; no FUERA_DE_SERVICIO (RN-RES-013, RN-HAB-001).

5. Permisos: cada función verifica el rol con las funciones de OBJ-02 (ADMIN y RECEPCION para lo interno; la creación web solo con canal DIRECTO_WEB).

6. Pruebas:
   - pgTAP: precio con y sin temporada/fin de semana (ejemplo del documento: Q500 × 1.20 × 1.10 = Q660); disponibilidad con reservas sin asignar; rechazo de fechas inválidas; creación web y de Recepción con el estado correcto; cuenta creada con el cargo.
   - Prueba de concurrencia: dos transacciones intentan reservar la última habitación del tipo al mismo tiempo; solo una tiene éxito (puede ser un script de Vitest contra Supabase local).

7. Regenera los tipos (pnpm gen:types) y exporta en packages/shared los tipos de entrada y salida de estas funciones.

Criterios de terminado:
- Todas las pruebas pasan, incluida la de concurrencia.
- docs/backend-funciones.md lista cada función: parámetros, permisos, errores posibles y reglas que cumple.
````

---

## Prompt 06-B — Modificar, cancelar y cuenta del huésped

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-06, parte B. Las funciones de la parte A ya existen (ver docs/backend-funciones.md).

Lee: docs/07 - Estados.md (secciones 3.3, 3.4 y 5), docs/10 - Reglas de Negocio.md (RN-RES-003, 016, 017; RN-TAR-007; RN-PAG; RN-CAN), docs/12 - Casos de Uso.md (UC-09, UC-10) y las HU: HU-REC-06, 07, 17, 18, 19; HU-HUE-10.

1. modificar_reserva(reserva_id, cambios): solo CONFIRMADA (fechas, huéspedes, tipo, habitación) o EN_ESTADIA (solo fecha de salida y habitación). Revalida disponibilidad con el mismo bloqueo; recalcula el precio con tarifas vigentes y ajusta el cargo por alojamiento; devuelve la diferencia; registra el cambio con responsable (RN-RES-003, 017, RN-TAR-007). Solo RECEPCION y ADMIN (RN-RES-016).

2. calcular_cancelacion(reserva_id) → penalidad y reembolso sin ejecutar nada (para mostrar antes de confirmar):
   - ≥ 48 horas antes de la hora de check-in del día de llegada (hora de Guatemala): reembolso 100 % (RN-CAN-002).
   - < 48 horas: penalidad = primera noche; reembolso = pagado − penalidad (RN-CAN-003).
   - Sin pagos: sin penalidad (RN-CAN-006). Canal externo: sin reembolso del hotel (RN-CAN-007).

3. cancelar_reserva(reserva_id, motivo, actor): solo PENDIENTE_PAGO o CONFIRMADA (RN-CAN-001). Pasa a CANCELADA, anula el cargo por alojamiento, registra el cargo de penalidad si aplica, cierra la cuenta y deja registrado el reembolso pendiente de procesar por Stripe (la llamada a Stripe es OBJ-07) (RN-CAN-008, 009). El huésped solo puede cancelar sus propias reservas; motivo automático "Cancelada por el huésped".

4. Cuenta:
   - agregar_cargo(reserva_id, concepto, cantidad, precio_unitario): solo con reserva EN_ESTADIA y cuenta ABIERTA (RN-PAG-010).
   - anular_cargo(cargo_id, motivo) (RN-PAG-011).
   - registrar_pago_recepcion(reserva_id, monto, metodo, referencia): monto > 0 y ≤ saldo (RN-PAG-013).
   - registrar_reembolso_saldo_favor(reserva_id, metodo): para saldo negativo antes del check-out (RN-PAG-016).
   - Vista o función estado_cuenta(reserva_id): alojamiento con desglose por noche, cargos, pagos, reembolsos y saldo = cargos VIGENTE − (pagos APROBADO − reembolsos parciales) (RN-PAG-012). El huésped solo ve la suya.

5. Pruebas pgTAP para cada regla: modificar en estados permitidos y prohibidos; cancelación con 72 h, 20 h y sin pagos (usa el ejemplo del documento 10: 3 noches a Q600, cancelación a 20 h → penalidad Q600, reembolso Q1,200); cargos solo EN_ESTADIA; pago mayor al saldo rechazado; saldo correcto.

6. Actualiza docs/backend-funciones.md y los tipos en packages/shared.

Criterios de terminado:
- Todas las pruebas pasan.
- Ninguna función permite una transición fuera del documento 07.
````

---

## Prompt 06-C — Check-in, check-out y control de transiciones

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-06, parte C: check-in, check-out, el control general de transiciones y el historial de estados.

Lee completos: docs/07 - Estados.md (todo, en especial secciones 3, 4, 5, 10 y 11), docs/10 - Reglas de Negocio.md (RN-RES-014, 015, RN-HAB, RN-LIM-009, RG-EST) y docs/12 - Casos de Uso.md (UC-03 y UC-04) y las HU: HU-REC-15, 16; HU-HUE-20.

1. Triggers de validación de transiciones (lista cerrada, RG-EST-01) para: reservas, ocupación y condición de habitaciones, cuentas, cargos, pagos. Cualquier cambio no listado en el documento 07 lanza un error claro en español. Los estados finales no cambian (RG-EST-04).
   (Pedidos, solicitudes e incidencias tendrán su trigger en OBJ-12 y OBJ-13; deja una función genérica reutilizable para que lo hagan igual.)

2. Trigger genérico de historial (RG-EST-02): cada cambio de estado inserta en historial_estados la entidad, estados anterior y nuevo, actor (empleado o huésped actual, o SISTEMA si no hay sesión), fecha y motivo (leído de una variable de sesión o columna de motivo).

3. realizar_check_in(reserva_id): transición R6. Condiciones: CONFIRMADA, fecha de entrada = hoy (Guatemala), habitación asignada LIBRE + LIMPIA (RN-RES-014). Efectos: reserva EN_ESTADIA, habitación OCUPADA, registro de fecha, hora y responsable.

4. realizar_check_out(reserva_id, origen: 'RECEPCION' | 'APP'): transición R9. Condiciones: EN_ESTADIA, saldo exactamente 0 (RN-RES-015); desde la app además: sin pedidos NUEVO/EN_PREPARACION/EN_CAMINO (RN-RS-011) y dentro de la ventana 00:00 del día de salida hasta la hora de check-out (RN-APP-008). Efectos (documento 07, sección 11): reserva FINALIZADA, cuenta CERRADA, habitación LIBRE + SUCIA desde cualquier condición (C1, RN-HAB-008) o FUERA_DE_SERVICIO si tiene incidencia abierta que impide su uso (C7), solicitudes PENDIENTE → CANCELADA con motivo "Estadía finalizada" (Q5). El correo de resumen es OBJ-07: deja un registro o evento para que se envíe.

5. marcar_habitacion_sucia(habitacion_id) para Recepción (C2) y reglas de condición C3 a C8 como funciones o triggers (las pantallas de limpieza son OBJ-13; aquí solo la lógica de condición que dependa de check-out y de incidencias: C6, C7, C8).

6. Pruebas pgTAP: cada transición permitida R1–R9 y C1–C8 pasa; al menos una transición prohibida por entidad se rechaza; historial se registra; check-in con habitación sucia rechazado; check-out con saldo ≠ 0 rechazado; check-out con incidencia bloqueante deja la habitación FUERA_DE_SERVICIO.

7. Actualiza docs/backend-funciones.md y los tipos.

Criterios de terminado:
- Todas las pruebas pasan.
- El documento 07 está implementado para reservas, habitaciones y cuentas.
````

## Revisión humana

- [ ] Revisar la prueba de concurrencia y ejecutarla varias veces.
- [ ] Revisar con P4 (Recepción) que las funciones devuelven lo que las pantallas necesitan.
