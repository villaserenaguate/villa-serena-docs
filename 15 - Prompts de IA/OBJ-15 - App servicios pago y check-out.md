# OBJ-15 — Construir la app Android: servicios, pago y check-out

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P6 — App móvil y personal de piso | OBJ-14, OBJ-07, OBJ-12, OBJ-13 (A) | `feat/obj-15-a-servicios`, `-b-checkout` | ~35 h |

## Antes de empezar

- [ ] `crear_pedido` (OBJ-12), `crear_solicitud` (OBJ-13 A), `/api/pagos/checkout` (OBJ-07 A) y `realizar_check_out` (OBJ-06 C) disponibles en desarrollo.

---

## Prompt 15-A — Room service y solicitudes desde la app

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-15, parte A en apps/mobile. Implementa HU-HUE-13, 14, 15, 16 y 17 cumpliendo TODOS sus criterios de aceptación.

Lee: HU-HUE-13 a 17, docs/07 - Estados.md (secciones 6 y 7), docs/10 - Reglas de Negocio.md (RN-RS-005, 006, 010; RN-LIM-005 a 008), docs/12 - Casos de Uso.md (UC-06 y UC-13) y docs/backend-funciones.md (crear_pedido, crear_solicitud, cancelar_solicitud).

1. Room service (HU-HUE-13): menú por categorías con precio y disponibilidad (agotados visibles pero no seleccionables), carrito con cantidades, notas de alergias, total y aviso de cargo a la cuenta; enviar con crear_pedido. Solo con reserva EN_ESTADIA.
2. Seguimiento en vivo (HU-HUE-14): estado actual con EstadoBadge, motivo si se cancela, historial de pedidos de la estadía; actualización con Supabase Realtime sin recargar. El huésped no puede cancelar pedidos (RN-RS-010).
3. Solicitar limpieza (HU-HUE-15) con comentario opcional; si ya hay una activa, mostrarlo y no permitir otra.
4. Solicitar artículos (HU-HUE-16) del catálogo activo con cantidad máxima por artículo.
5. Mis solicitudes (HU-HUE-17): tipo, fecha, hora y estado en vivo; cancelar mientras esté PENDIENTE.
6. Manejo de errores de las funciones SQL con mensajes claros en español.
7. Pruebas unitarias del carrito y del cálculo de total; prueba manual en Expo Go con Room Service abierto en la web al mismo tiempo.

Criterios de terminado:
- HU-HUE-13 a 17 cumplidas; UC-06 y UC-13 funcionan desde la app.
- Un pedido hecho desde la app aparece al instante en el panel de Room Service y su estado se actualiza en la app.
````

---

## Prompt 15-B — Pagar el saldo y check-out digital

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-15, parte B en apps/mobile. Implementa HU-HUE-20 cumpliendo TODOS sus criterios.

Lee: HU-HUE-20, docs/07 - Estados.md (transición R9 y sección 11), docs/10 - Reglas de Negocio.md (RN-APP-008, RN-RS-011, RN-PAG-001, 002, 016, RN-RES-015), docs/12 - Casos de Uso.md (UC-04 A1 y A4; UC-05), docs/stripe.md y docs/backend-funciones.md.

1. En "Mi cuenta", botón "Hacer check-out" visible solo desde las 00:00 del día de salida hasta la hora de check-out (hora de Guatemala); fuera de esa ventana, mensaje para ir a Recepción (UC-04 A4).
2. Si hay pedidos NUEVO, EN_PREPARACION o EN_CAMINO: bloquear con mensaje (RN-RS-011).
3. Si hay saldo pendiente: pedir la URL a /api/pagos/checkout (origen app, con Linking.createURL como enlace de retorno para que funcione en Expo Go y en el APK) y abrir Stripe Checkout con expo-web-browser (openAuthSessionAsync). Al volver, mostrar "pago en proceso" hasta que el webhook confirme (Realtime sobre la cuenta).
4. Con saldo exactamente 0: confirmar y llamar realizar_check_out con origen APP. Mostrar el resumen final; la estadía queda en solo lectura y el correo de resumen llega (OBJ-07).
5. Si hay saldo a favor del huésped: mostrar que Recepción debe registrar el reembolso antes del check-out (RN-PAG-016).
6. Pruebas manuales documentadas: pago con 4242 4242 4242 4242, pago rechazado, check-out fuera de ventana, con pedido en curso.

Criterios de terminado:
- HU-HUE-20 cumplida; UC-04 A1 funciona en Expo Go y en el APK.
- Recepción recibe el aviso de check-out desde la app (HU-REC-23).
````

## Revisión humana

- [ ] Probar el pago en el APK instalado (no solo en Expo Go): el regreso desde Stripe debe abrir la app.
