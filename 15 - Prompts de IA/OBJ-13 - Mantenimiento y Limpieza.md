# OBJ-13 — Construir el módulo de Mantenimiento y Limpieza

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P6 — App móvil y personal de piso | OBJ-05, OBJ-06, funciones de inventario de OBJ-11 (B) | `feat/obj-13-a-limpieza`, `-b-mantenimiento` | ~55 h |

**Este objetivo crea la lógica de solicitudes e incidencias** que usan Recepción (OBJ-09 D), Admin (OBJ-11 C) y la app (OBJ-15). El panel se diseña para usarse **desde el teléfono** del empleado.

---

## Prompt 13-A — Limpieza, solicitudes e insumos

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-13, parte A (lógica SQL + pantallas en /panel/piso, diseño mobile-first). Implementa HU-MYL-01 a HU-MYL-09 cumpliendo TODOS sus criterios de aceptación.

Lee: docs/04 - Historias de Usuario/HU - Mantenimiento y Limpieza.md (HU-MYL-01 a 09), docs/07 - Estados.md (secciones 4.2 y 7: C1 a C8, Q1 a Q5), docs/08 - Inventario Turnos y Personal.md (secciones 3 y 4), docs/10 - Reglas de Negocio.md (RN-LIM, RN-INV, RN-HAB-005), docs/12 - Casos de Uso.md (UC-07, UC-13) y docs/backend-funciones.md (registrar_movimiento de OBJ-11).

1. Lógica SQL:
   - iniciar_limpieza (C3: SUCIA → EN_LIMPIEZA, asigna al empleado), finalizar_limpieza (C4 → LIMPIA, registra hora; si hay llegada hoy, genera aviso para Recepción), interrumpir_limpieza (C5 → SUCIA). Solo área LIMPIEZA o AMBAS; C4 y C5 solo el empleado a cargo.
   - crear_solicitud(reserva_id, tipo LIMPIEZA|ARTICULOS, items, comentario, prioridad, origen APP|RECEPCION): solo EN_ESTADIA (RN-LIM-006), máximo 1 de limpieza activa por habitación (RN-LIM-005), cantidades ≤ máximo del catálogo (RN-LIM-007). La usan la app y Recepción.
   - tomar_solicitud (Q2 → EN_PROCESO, asignada al empleado), atender_solicitud (Q3 → ATENDIDA; por cada artículo vinculado a producto llama registrar_movimiento con CONSUMO_ENTREGA; si no hay stock, rechaza — RN-INV-007), cancelar_solicitud (Q4: huésped, Recepción o Admin, solo PENDIENTE).
   - Las solicitudes de limpieza de un huésped alojado NO cambian la condición de la habitación (RN-LIM-013).
   - Triggers de transición e historial con la función genérica de OBJ-06.
   - registrar_consumo_limpieza(limpieza_id, productos): usa registrar_movimiento con CONSUMO_LIMPIEZA.
   - reportar_faltante(producto_id, cantidad, comentario): uno PENDIENTE por producto (RN-INV-009).

2. Pantallas (/panel/piso, pensadas para teléfono):
   - Pendientes de limpieza (SUCIA y EN_LIMPIEZA), prioritarias primero con motivo "Llegada hoy", tiempo real (HU-MYL-01).
   - Detalle de habitación: estado, solicitudes pendientes con artículos, incidencias abiertas en solo lectura, observaciones recientes, sin datos personales del huésped (HU-MYL-04).
   - Iniciar, finalizar e interrumpir limpieza (HU-MYL-02, 03); registrar insumos al finalizar (HU-MYL-08).
   - Observaciones (HU-MYL-05).
   - Solicitudes pendientes y en proceso, prioridad alta primero, aviso en tiempo real; tomar y atender con confirmación de artículos entregados (HU-MYL-06, 07).
   - Reportar faltante y ver el estado de mis reportes (HU-MYL-09).
   - Los empleados con área MANTENIMIENTO no ven esta sección (salvo AMBAS).

3. Pruebas: pgTAP de C3–C5, Q1–Q5, límites y consumo de stock; Playwright del flujo de limpieza en viewport de teléfono.

Criterios de terminado:
- HU-MYL-01 a 09 cumplidas; UC-07 y UC-13 (lado del personal) funcionan.
- crear_solicitud documentada en docs/backend-funciones.md para OBJ-09 y OBJ-15.
````

---

## Prompt 13-B — Reportes, mantenimiento e historial

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-13, parte B. Implementa HU-MYL-10 a HU-MYL-17 cumpliendo TODOS sus criterios, y la lógica completa de incidencias.

Lee: HU-MYL-10 a 17, docs/07 - Estados.md (sección 8: M1 a M8; sección 4.2: C6 a C8), docs/10 - Reglas de Negocio.md (RN-MAN, RN-HAB-001 a 003, 006, RN-INV-006, RN-LIM-012), docs/12 - Casos de Uso.md (UC-08) y docs/backend-funciones.md.

1. Lógica SQL de incidencias (máquina de estados completa del documento 07, sección 8):
   - reportar_incidencia (M1): MYL, Recepción o Admin; habitación, tipo, descripción, si impide el uso, foto opcional (bucket operacion). Si impide el uso y la habitación está LIBRE → FUERA_DE_SERVICIO (C6); si está OCUPADA → indicador "Incidencia pendiente" y aviso a Recepción (RN-HAB-002).
   - asignar (M2), reasignar (M3), devolver (M6), cerrar (M7) y cancelar (M8): solo ADMIN (RN-MAN-003) — las pantallas del Admin son OBJ-11 C, pero la lógica se crea aquí.
   - iniciar_orden (M4) y resolver_orden (M5, solución obligatoria): solo el técnico asignado con área MANTENIMIENTO o AMBAS (RN-MAN-009, 010).
   - Al cerrar o cancelar la última orden bloqueante: habitación → SUCIA (C8, RN-HAB-003).
   - registrar_repuestos(incidencia_id, productos): usa registrar_movimiento con CONSUMO_REPUESTO; solo en EN_PROCESO (RN-MAN-008).
   - Objetos olvidados: registrar (asociado a la última reserva de la habitación) y cambiar a DEVUELTO (a quién y cuándo) o DESECHADO (motivo) — MYL, Recepción y Admin (RN-LIM-012).
   - Triggers de transición e historial.

2. Pantallas en /panel/piso:
   - Reportar daño (HU-MYL-10) con foto desde la cámara del teléfono.
   - Objetos olvidados: registrar y cambiar estado (HU-MYL-11, 12).
   - Mis órdenes (solo área MANTENIMIENTO o AMBAS): ordenadas por prioridad y fecha compromiso; iniciar, registrar repuestos y resolver con descripción (HU-MYL-13 a 16).
   - Historial de servicios: limpiezas, solicitudes atendidas y órdenes resueltas o cerradas, con filtros (HU-MYL-17).

3. Documenta todas las funciones de incidencias en docs/backend-funciones.md (las usan OBJ-09 D y OBJ-11 C).

4. Pruebas: pgTAP de M1–M8 permitidas y prohibidas, C6–C8, repuestos sin stock rechazados; Playwright del ciclo reportar → (Admin asigna) → iniciar → resolver.

Criterios de terminado:
- HU-MYL-10 a 17 cumplidas; UC-08 (lado del técnico) funciona.
- La máquina de estados de incidencias pasa todas sus pruebas.
````

## Revisión humana

- [ ] Probar el panel en un teléfono real durante un recorrido de limpieza simulado.
