# OBJ-12 — Construir el módulo de Room Service

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P5 — Web pública y Room Service | OBJ-05, OBJ-06 | `feat/obj-12-room-service` | ~35 h |

**Este objetivo crea la lógica de pedidos** que también usa la app (OBJ-15).

---

## Prompt 12-A — Pedidos, menú, cargos y avisos

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-12: el módulo completo de Room Service (lógica SQL + pantallas en /panel/room-service). Implementa HU-RS-01 a HU-RS-10 cumpliendo TODOS sus criterios de aceptación.

Lee: docs/04 - Historias de Usuario/HU - Room Service.md, docs/07 - Estados.md (sección 6, transiciones S1 a S5), docs/10 - Reglas de Negocio.md (RN-RS-001 a 011, RN-PAG-010), docs/09 - Matriz de Permisos.md (3.4 y nota ²), docs/12 - Casos de Uso.md (UC-06) y docs/backend-funciones.md.

1. Lógica en SQL (migración nueva):
   - crear_pedido(reserva_id | habitacion_id, items[{item_id, cantidad}], notas, origen APP|TELEFONO): solo reservas EN_ESTADIA (RN-RS-006); rechaza ítems AGOTADO o inactivos (RN-RS-005); congela el precio de cada ítem; estado NUEVO. La usará la app (OBJ-15) y el pedido telefónico.
   - avanzar_pedido(pedido_id): NUEVO → EN_PREPARACION → EN_CAMINO → ENTREGADO, sin saltos ni retrocesos (RN-RS-001, 002). Solo ROOM_SERVICE.
   - cancelar_pedido(pedido_id, motivo): antes de ENTREGADO, motivo obligatorio, sin reactivación (RN-RS-003, 004). ROOM_SERVICE y ADMIN; el huésped no puede (RN-RS-010).
   - Al pasar a ENTREGADO: genera UN solo cargo en la cuenta "Room Service — Pedido #n" (RN-RS-007), idempotente aunque el cambio se reciba dos veces.
   - Trigger de transiciones con la función genérica de OBJ-06 e historial de estados.
   - marcar_agotado(item_id) para ROOM_SERVICE; reactivar solo ADMIN (RN-RS-008).
   - Vista limitada para Room Service con nombre del huésped, habitación y piso, sin otros datos personales (nota ²).

2. Pantallas:
   - Cola de pedidos activos ordenada por antigüedad, con tiempo transcurrido y color por estado; todos los turnos (HU-RS-01).
   - Detalle del pedido con notas destacadas e historial de estados (HU-RS-02).
   - Pedido telefónico: solo habitaciones ocupadas, ítems disponibles (HU-RS-03).
   - Botón para avanzar el estado y cancelar con motivo (HU-RS-04, 05).
   - Menú con búsqueda y botón "Marcar agotado" (HU-RS-06, 07).
   - Historial con filtros por fecha, habitación, estado y turno (por defecto el turno actual del empleado) y tiempo total hasta la entrega (HU-RS-09).
   - Aviso en tiempo real con sonido al llegar un pedido nuevo, con enlace al detalle (HU-RS-10).

3. Pruebas: pgTAP de transiciones permitidas y prohibidas, cargo único al entregar, sin cargo al cancelar, rechazo de ítem agotado; Playwright del ciclo completo de un pedido telefónico.

Criterios de terminado:
- HU-RS-01 a 10 cumplidas; UC-06 funciona desde la web.
- docs/backend-funciones.md documenta crear_pedido para OBJ-15.
````

## Revisión humana

- [ ] Probar con dos sesiones: crear un pedido y verificar el aviso sonoro en la otra.
