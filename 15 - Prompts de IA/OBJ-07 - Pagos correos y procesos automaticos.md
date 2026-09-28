# OBJ-07 — Integrar pagos en línea, correos y procesos automáticos

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P2 — Lógica de negocio | OBJ-06, OBJ-03 | `feat/obj-07-a-stripe`, `-b-correos`, `-c-procesos` | ~20 h |

## Antes de empezar

- [ ] OBJ-06 fusionado; OBJ-03 con Stripe sandbox, Resend y variables configuradas.
- [ ] Stripe CLI instalada para probar webhooks en local (`stripe listen --forward-to localhost:3000/api/stripe/webhook`).

---

## Prompt 07-A — Stripe: pago, webhook y reembolsos

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-07, parte A: integrar Stripe (solo sandbox) para pagos y reembolsos.

Lee: docs/07 - Estados.md (sección 5.3, pagos P1 a P6), docs/10 - Reglas de Negocio.md (RN-PAG-001 a 008, 016; RN-CAN-008; RN-RES-012), docs/12 - Casos de Uso.md (UC-05) y las HU: HU-HUE-06 y HU-HUE-20. Revisa docs/backend-funciones.md.

1. POST apps/web/src/app/api/pagos/checkout:
   - Recibe reserva_id (y el origen: web o app). Verifica que quien llama puede pagar esa reserva (cliente de la reserva recién creada en la web o huésped autenticado dueño).
   - Calcula el monto EN EL SERVIDOR desde la base de datos (saldo pendiente o total de la reserva), nunca desde el cliente.
   - Registra un pago PENDIENTE (P2) mediante una función SQL.
   - Crea una sesión de Stripe Checkout en GTQ con metadata (pago_id, reserva_id), expiración acorde a los 30 minutos (PAR-06), y URLs de éxito/cancelación: para web, /reservar/resultado; para la app, un enlace de retorno (deep link) que recibirá como parámetro.
   - Devuelve la URL de Checkout.

2. POST apps/web/src/app/api/stripe/webhook:
   - Verifica la firma con STRIPE_WEBHOOK_SECRET (RNF-SEC-007); rechaza firmas inválidas con 400 y registra el intento.
   - Idempotencia: guarda el id de cada evento procesado; si ya existe, responde 200 sin reprocesar (RN-PAG-002, 003).
   - checkout.session.completed → pago APROBADO (P3); si la reserva estaba PENDIENTE_PAGO → CONFIRMADA (R3). Deja registrado el evento para el correo de confirmación.
   - checkout.session.expired o pago fallido → pago FALLIDO (P4).
   - Eventos de reembolso → actualizan el registro de reembolso.
   - Las actualizaciones se hacen con funciones SQL usando la clave service_role (actor PASARELA en el historial).

3. Reembolsos: función de servidor procesarReembolsosPendientes() que toma los reembolsos pendientes creados por cancelar_reserva y por saldo a favor, llama a la API de reembolsos de Stripe (total o parcial) y actualiza su estado; pago → REEMBOLSADO solo si es total (RN-CAN-008). Expónla en un Route Handler protegido (llamado por el proceso programado de la parte C y tras cancelar).

4. Página /reservar/resultado: muestra "confirmada", "en proceso" (si el webhook aún no llega; se actualiza sola) o "fallida" con opción de reintentar (UC-05 A4).

5. Pruebas (Vitest): verificación de firma, evento repetido no duplica, monto calculado en servidor ignora montos del cliente, cálculo de reembolso parcial. Documenta en docs/stripe.md cómo probar con tarjetas de prueba (4242 4242 4242 4242, rechazo 4000 0000 0000 0002) y Stripe CLI.

Criterios de terminado:
- Un pago de prueba confirma la reserva SOLO al llegar el webhook.
- Reenviar el mismo evento no duplica el pago.
- Un reembolso parcial de prueba se refleja en Stripe y en la cuenta.
````

---

## Prompt 07-B — Correos y comprobante PDF

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-07, parte B: correos transaccionales y comprobante de pago en PDF.

Lee: docs/10 - Reglas de Negocio.md (RN-PAG-015, RN-CAN-010, PAR-01, 02), docs/04 - Historias de Usuario (HU-HUE-07, HU-HUE-20, HU-REC-07, HU-REC-16, HU-REC-20).

1. Plantillas con React Email en apps/web/src/emails, en español, con logo y datos del hotel desde configuracion_hotel:
   - Confirmación de reserva: código, fechas, tipo, huéspedes, total pagado, horarios de check-in/out, enlace de descarga de la app y enlace a "Mi reserva" (HU-HUE-07).
   - Cancelación: motivo, penalidad y reembolso (RN-CAN-010).
   - Resumen de check-out: detalle de la cuenta.
   - Comprobante de pago (con el PDF adjunto).

2. Servicio enviarCorreo(tipo, datos) con Resend; reintento simple y registro del error si falla (HU-HUE-07 criterio 5).

3. Disparadores: una tabla o cola de "correos pendientes" que llenan las funciones SQL (reserva confirmada, cancelada, check-out, pago) y un Route Handler protegido que la procesa (llamado tras cada evento y por el proceso programado de la parte C). No envíes correos desde el navegador.

4. Comprobante PDF con @react-pdf/renderer:
   - GET apps/web/src/app/api/comprobantes/[id]: solo RECEPCION, ADMIN o el huésped dueño.
   - Contenido: datos del hotel, huésped, código de reserva, conceptos, total, monto pagado, método, fecha, número correlativo único y la leyenda "Este documento no es una factura electrónica" (RN-PAG-015).

5. Pruebas: render de cada plantilla con datos de ejemplo (Vitest) y generación del PDF.

Criterios de terminado:
- En desarrollo llegan los 4 tipos de correo a una bandeja real.
- El PDF se descarga con número correlativo.
````

---

## Prompt 07-C — Procesos programados y alertas

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-07, parte C: procesos automáticos con pg_cron.

Lee: docs/10 - Reglas de Negocio.md (RN-RES-012, RN-CAN-004 a 009, RN-INV-005, PAR-06, 09, 21), docs/12 - Casos de Uso.md (UC-15) y docs/08 - Inventario Turnos y Personal.md (sección 3.3).

1. Migración con trabajos de pg_cron (recuerda: pg_cron usa UTC; Guatemala es UTC-6 sin horario de verano):
   - Cada 5 minutos: vencer_reservas_no_pagadas() → reservas PENDIENTE_PAGO creadas hace más de 30 minutos y sin pago APROBADO pasan a CANCELADA con motivo "Pago no completado" (R4); cierra la cuenta y libera disponibilidad (UC-15 A1: si el pago ya está aprobado, no se cancela).
   - Diario a las 05:59 UTC (23:59 Guatemala): marcar_no_show() → reservas CONFIRMADA con entrada hoy y sin check-in pasan a NO_SHOW (R8), con penalidad según RN-CAN-005, 006, 007 y reembolso pendiente.
   - Cada 5 minutos: llamar (con pg_net o desde el Route Handler de procesamiento) el procesamiento de correos pendientes y reembolsos pendientes, o documenta la alternativa si prefieres que lo dispare Next.js.

2. Alerta de stock mínimo (RN-INV-005): trigger en productos/movimientos que, al quedar el stock ≤ mínimo, crea una alerta visible para el ADMIN (tabla de alertas o notificaciones) que Realtime pueda escuchar.

3. Pruebas pgTAP simulando la hora: reserva pendiente vieja se cancela y una nueva no; no-show con y sin pagos; alerta de stock generada.

4. Documenta en docs/procesos-programados.md cada trabajo, su horario en UTC y en Guatemala, y cómo verlos en Supabase.

Criterios de terminado:
- Una reserva no pagada se cancela sola a los 30 minutos (probado en desarrollo).
- El no-show se marca y aplica la penalidad.
- La alerta de stock aparece al consumir por debajo del mínimo.
````

## Revisión humana

- [ ] Hacer una reserva completa en desarrollo con tarjeta de prueba y verificar el correo.
- [ ] Revisar en Stripe (sandbox) que los montos y reembolsos coinciden.
