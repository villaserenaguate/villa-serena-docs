# OBJ-08 — Construir la web pública (motor de reservas)

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P5 — Web pública y Room Service | OBJ-05, OBJ-06, OBJ-07 | `feat/obj-08-a-explorar`, `-b-reservar`, `-c-mi-reserva` | ~75 h |

**Mientras OBJ-06 y OBJ-07 no estén listos**, se puede avanzar el prompt A con datos simulados y luego conectarlo.

## Antes de empezar

- [ ] OBJ-05 fusionado (layout público y componentes base).
- [ ] Para B y C: OBJ-06 y OBJ-07 fusionados.

---

## Prompt 08-A — Inicio, catálogo, búsqueda y precio

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-08, parte A: las páginas públicas para conocer el hotel y buscar disponibilidad. Implementa HU-HUE-01, HU-HUE-02, HU-HUE-03 y HU-HUE-04 cumpliendo TODOS sus criterios de aceptación.

Lee: docs/04 - Historias de Usuario/HU - Cliente y Huesped.md (HU-HUE-01 a 04), docs/12 - Casos de Uso.md (UC-01), docs/10 - Reglas de Negocio.md (RN-RES-005 a 008, RN-TAR), docs/backend-funciones.md (consultar_disponibilidad) y docs/14 - Tecnologias y Arquitectura.md (rutas públicas).

1. / (inicio): portada con fotos, descripción, ubicación, contacto y horarios de check-in/out desde configuracion_hotel; resumen de amenidades; buscador de disponibilidad visible (HU-HUE-01).
2. /habitaciones y /habitaciones/[id]: catálogo de tipos activos con fotos, descripción, capacidad y precio base en quetzales; detalle con galería (HU-HUE-02).
3. Buscador (en inicio y /reservar): selector de rango de fechas y número de huéspedes con validaciones (sin fechas pasadas, salida > entrada, 1–30 noches, máximo 365 días) (HU-HUE-03).
4. Resultados: tipos disponibles con precio total y desglose por noche indicando temporada y fin de semana (HU-HUE-04); mensaje claro sin disponibilidad sugiriendo otras fechas.
5. Usa Server Components para leer catálogos públicos y consultar_disponibilidad (RPC). No leas tablas de reservas.
6. Diseño responsive (teléfono y computadora), textos en español, montos con formatoQuetzales, imágenes optimizadas con next/image desde el bucket público.
7. Pruebas: Playwright del flujo de búsqueda (con y sin disponibilidad) y del cálculo mostrado (coincide con la función SQL).

Criterios de terminado:
- HU-HUE-01 a 04 cumplidas.
- El precio mostrado coincide con calcular_precio_estadia.
````

---

## Prompt 08-B — Reservar, pagar y confirmación

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-08, parte B: el flujo de reserva y pago de la web pública. Implementa HU-HUE-05, HU-HUE-06 y HU-HUE-07 (interfaz) cumpliendo TODOS sus criterios.

Lee: HU-HUE-05 a 07, docs/12 - Casos de Uso.md (UC-02 y UC-05), docs/10 - Reglas de Negocio.md (RN-RES-001, 011, 012; RN-PAG-001 a 006), docs/backend-funciones.md y docs/stripe.md.

1. /reservar: a partir del tipo y fechas elegidos, formulario del huésped principal (nombre, correo, teléfono, nacionalidad, tipo y número de documento) con Zod de packages/shared (HU-HUE-05).
2. Resumen antes de pagar: tipo, fechas, huéspedes, desglose y total. Si la tarifa cambió, recalcular y avisar (UC-02 A5).
3. Al confirmar: llamar crear_reserva (canal DIRECTO_WEB). Si ya no hay disponibilidad, mostrar el mensaje y volver a la búsqueda (UC-02 A2).
4. Pago: llamar /api/pagos/checkout y redirigir a Stripe Checkout (HU-HUE-06). Mostrar el tiempo límite de 30 minutos.
5. /reservar/resultado: estados confirmada / en proceso (se actualiza sola hasta que llega el webhook) / fallida con botón de reintentar (UC-05 A1 y A4).
6. Protección anti-abuso básica en el formulario: validación en servidor y límite de envíos por IP en el Route Handler o Server Action que crea la reserva.
7. Pruebas Playwright: reserva completa con tarjeta de prueba en desarrollo (o simulando el webhook en local), pago rechazado y reintento.

Criterios de terminado:
- UC-01, UC-02 y UC-05 funcionan de extremo a extremo en dev.<dominio>.
- La reserva queda CONFIRMADA solo después del webhook y llega el correo de confirmación.
````

---

## Prompt 08-C — Mi reserva, cancelación y check-in anticipado

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-08, parte C: la zona "Mi reserva" de la web pública. Implementa HU-HUE-08, 09, 10 y 11 (versión web) cumpliendo TODOS sus criterios.

Lee: HU-HUE-08 a 11, docs/12 - Casos de Uso.md (UC-10, UC-11, UC-12), docs/10 - Reglas de Negocio.md (RN-APP-001 a 003, 007, 009; RN-CAN), docs/auth.md y docs/backend-funciones.md.

1. /mi-reserva: acceso con correo → /api/auth/otp → ingreso del código de 6 dígitos (verifyOtp). Mensaje genérico si el correo no tiene reservas; mensaje de límite de intentos (HU-HUE-08).
2. Lista de reservas del huésped: próximas, en curso y pasadas, con código, fechas, tipo, huéspedes, estado (EstadoBadge) y total (HU-HUE-09).
3. Cancelar: mostrar el resultado de calcular_cancelacion (penalidad y reembolso) antes de confirmar; luego cancelar_reserva; mostrar confirmación (HU-HUE-10). Solo para PENDIENTE_PAGO y CONFIRMADA.
4. Check-in anticipado (HU-HUE-11): disponible desde 24 horas antes de la llegada, solo CONFIRMADA. Confirmar datos, agregar acompañantes (sin superar la capacidad), subir foto del documento al bucket privado "documentos" (JPG/PNG/PDF, máx. 5 MB), peticiones especiales. Marca "check-in anticipado completado". Crea las funciones SQL que falten (o pídeselas a P2 si ya existen en su rama).
5. Cierre de sesión del huésped.
6. Pruebas Playwright: acceso OTP en local (buzón de prueba), cancelación con penalidad, check-in anticipado con archivo válido e inválido.

Criterios de terminado:
- UC-10, UC-11 y UC-12 funcionan.
- Un huésped no puede ver ni cancelar reservas de otro.
````

## Revisión humana

- [ ] Probar el flujo completo en un teléfono real.
- [ ] Revisar textos y ortografía de la web pública.
