# OBJ-16 — Construir la preparación para el Channel Manager

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P2 — Lógica de negocio | OBJ-06 | `feat/obj-16-a-api-canal`, `-b-canal-simulado` | ~40 h |

---

## Prompt 16-A — Diseño, canales, claves y API

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-16, parte A. Implementa HU-CM-01, HU-CM-02 y HU-CM-03 cumpliendo TODOS sus criterios, y el documento de diseño ALC-CM-01.

Lee: docs/04 - Historias de Usuario/HU - Channel Manager.md, docs/10 - Reglas de Negocio.md (RN-CM-001 a 007, RN-TAR-010, RN-PAG-008, RN-CAN-007, RN-RES), docs/12 - Casos de Uso.md (UC-16), docs/01 - Alcance del Proyecto.md (sección D y D-05) y docs/backend-funciones.md.

1. Documento de diseño docs/channel-manager.md:
   - Qué es un Channel Manager y qué parte cubre la versión 1 (recepción de reservas y cancelaciones) y qué queda en Fase 2 (conexión real, envío de disponibilidad y tarifas, mapeo de tipos).
   - Arquitectura de adaptadores: un adaptador por canal que traduce el formato del canal al modelo interno.
   - Cómo se sincronizarían disponibilidad, tarifas e inventario (ARI) en una versión futura: diagrama de secuencia Mermaid.
   - Seguridad: claves por canal, idempotencia, registro de peticiones.

2. Gestión de canales en /panel/admin (HU-CM-01): Booking y Expedia (cada uno corresponde a un canal de origen), activar/desactivar, generar y regenerar clave mostrada UNA sola vez y guardada como hash con pgcrypto (RN-CM-001); cantidad de reservas recibidas por canal.

3. API en apps/web/src/app/api/canal/v1:
   - POST /reservas (HU-CM-02): autenticación por encabezado con la clave; canal ACTIVO (RN-CM-002); validación Zod del cuerpo (id externo, tipo de habitación, fechas, huéspedes, datos del huésped, monto); idempotencia por (canal, id externo) devolviendo la reserva existente (RN-CM-004); validación de disponibilidad con la misma función que la web (RN-CM-003); crea la reserva CONFIRMADA con canal de origen, cargo por alojamiento = monto del canal (RN-TAR-010) y pago APROBADO método CANAL (RN-PAG-008). Usa una función SQL crear_reserva_canal.
   - POST /reservas/{id_externo}/cancelacion (HU-CM-03): solo el canal dueño, solo CONFIRMADA, sin reembolso del hotel (RN-CAN-007); repetir la cancelación no produce cambios ni errores.
   - Registra cada petición (aceptada o rechazada) en canal_peticiones con canal, cuerpo resumido, resultado y fecha (RN-CM-006).
   - Respuestas JSON con códigos HTTP correctos (201, 200, 400, 401, 409, 422) y mensajes claros.

4. OpenAPI: docs/openapi/canal-v1.yaml y página de documentación interactiva con Scalar en /api/canal/v1/docs.

5. Pruebas Vitest: clave inválida, canal inactivo, reserva nueva, reserva repetida sin duplicar, sin disponibilidad, cancelación de otro canal rechazada, cancelación repetida.

Criterios de terminado:
- HU-CM-01 a 03 cumplidas; OpenAPI publicado.
- docs/channel-manager.md completo.
````

---

## Prompt 16-B — Canal simulado y visibilidad

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-16, parte B. Implementa HU-CM-04 (datos y filtros) y HU-CM-05 cumpliendo TODOS sus criterios.

Lee: HU-CM-04 y 05, docs/12 - Casos de Uso.md (UC-16 A5), docs/channel-manager.md y docs/openapi/canal-v1.yaml.

1. Canal simulado en /panel/admin/canal-simulado (solo ADMIN):
   - Elegir canal (Booking o Expedia), tipo de habitación, fechas y datos del huésped, o generarlos al azar.
   - Enviar la reserva A TRAVÉS de la API real (fetch al endpoint con la clave del canal, que el Admin pega o se toma de una variable de entorno solo de servidor), mostrando la respuesta completa.
   - Reenviar la misma reserva para demostrar que no se duplica.
   - Enviar la cancelación de una reserva simulada.
   - Historial de envíos de la sesión.

2. Visibilidad (HU-CM-04): canal de origen e id externo en el detalle de reserva y en búsquedas, filtro por canal, e ícono en el Gantt (coordina con P4 si el Gantt ya existe). Aviso en tiempo real a Recepción al llegar una reserva de canal.

3. Prueba Playwright del flujo: enviar reserva simulada → aparece en el Gantt con el ícono → cancelar → desaparece.

Criterios de terminado:
- HU-CM-04 y 05 cumplidas; UC-16 completo demostrable en dev.<dominio>.
````

## Revisión humana

- [ ] Ensayar la demostración del canal simulado (será parte de la presentación final).
