# OBJ-11 — Construir Administración: personal, turnos, inventario, supervisión e indicadores

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P3 — Plataforma y calidad | OBJ-05, OBJ-02 (C también: funciones de incidencias de OBJ-13) | `feat/obj-11-a-personal`, `-b-inventario`, `-c-supervision` | ~60 h |

**Coordinación:** el prompt B crea las funciones de inventario que usa OBJ-13 (P6). El prompt C usa las funciones de incidencias que crea OBJ-13 (A o B). Acuerden el orden con P6.

---

## Prompt 11-A — Personal, turnos y reasignación

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-11, parte A, en /panel/admin. Implementa HU-ADM-01, 02, 03, 04 y 20 cumpliendo TODOS sus criterios de aceptación.

Lee: docs/04 - Historias de Usuario/HU - Administrador.md (HU-ADM-01 a 04 y 20), docs/08 - Inventario Turnos y Personal.md (secciones 5 y 6), docs/10 - Reglas de Negocio.md (RN-PER, RN-TUR, R-ROL), docs/12 - Casos de Uso.md (UC-19) y docs/auth.md (/api/empleados).

1. Empleados: listado con filtros por rol y estado y turno actual; crear (usa /api/empleados; área obligatoria para MANTENIMIENTO_LIMPIEZA); editar; desactivar/reactivar; el Admin no puede desactivarse a sí mismo (HU-ADM-01, 02).
2. Regla RN-PER-003 en la base de datos: no se puede desactivar a un empleado con órdenes ASIGNADA o EN_PROCESO, solicitudes EN_PROCESO o habitaciones EN_LIMPIEZA a su cargo; la interfaz muestra la lista para reasignar. Al desactivar: se eliminan sus turnos futuros y se cierra su sesión (RN-PER-004).
3. Turnos: crear, editar y desactivar (puede cruzar medianoche; no desactivar con asignaciones futuras) (HU-ADM-03, RN-TUR-004).
4. Asignación semanal: vista de empleados × días; asignar a una o varias fechas; sin traslapes ni asignar a ADMIN o INACTIVO (RN-TUR-001, 002). Panel "personal en turno ahora". Cada empleado ve sus turnos de la semana en su propio panel (HU-ADM-04).
5. Reasignar trabajo en curso (HU-ADM-20): solicitudes EN_PROCESO y limpiezas EN_LIMPIEZA a otro empleado activo con área LIMPIEZA o AMBAS, sin cambiar estado, con motivo y registro del responsable. Las órdenes se reasignan con la función de incidencias (OBJ-13 / HU-ADM-16).
6. Funciones SQL para asignaciones y reasignación con validaciones en la base de datos.
7. Pruebas: pgTAP de traslape de turnos, desactivación bloqueada y reasignación; Playwright de crear empleado y asignar turno.

Criterios de terminado:
- HU-ADM-01 a 04 y 20 cumplidas; UC-19 funciona.
````

---

## Prompt 11-B — Inventario, catálogo de artículos y faltantes

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-11, parte B. Implementa HU-ADM-12, 13, 14 y 15 cumpliendo TODOS sus criterios.

Lee: HU-ADM-12 a 15, docs/08 - Inventario Turnos y Personal.md (secciones 3 y 4), docs/10 - Reglas de Negocio.md (RN-INV-001 a 009) y docs/12 - Casos de Uso.md (UC-18).

1. Funciones SQL de inventario (las usará también OBJ-13):
   - registrar_movimiento(producto_id, tipo, cantidad, motivo, origen_tipo, origen_id): actualiza el stock en la misma transacción, guarda stock resultante y responsable, rechaza stock negativo (RN-INV-001 a 004). ENTRADA y AJUSTE solo ADMIN (AJUSTE con motivo obligatorio). Los consumos (CONSUMO_LIMPIEZA, CONSUMO_ENTREGA, CONSUMO_REPUESTO) solo MYL y respetando la categoría del producto (RN-INV-006).
   - Documenta la firma en docs/backend-funciones.md para P6.
2. Productos: listado con stock, mínimo, categoría y alerta (stock ≤ mínimo); crear, editar, desactivar; stock inicial 0 (HU-ADM-12, 14).
3. Catálogo de artículos para el huésped: nombre, cantidad máxima por solicitud, producto vinculado opcional (solo AMENIDAD_HABITACION; la lencería no se vincula), activo (HU-ADM-12, criterios 5 y 6).
4. Entradas y ajustes con historial de movimientos por producto, incluidos consumos de otras áreas (HU-ADM-13).
5. Alertas de stock mínimo en tiempo real para el Admin (usa la alerta de OBJ-07 C si existe; si no, créala).
6. Reportes de faltante: lista de PENDIENTE; atender registrando la entrada en el mismo paso → ATENDIDO; el empleado que reportó ve el cambio (HU-ADM-15).
7. Pruebas pgTAP del stock (nunca negativo, categorías correctas, ajuste sin motivo rechazado) y Playwright de entrada + atención de faltante.

Criterios de terminado:
- HU-ADM-12 a 15 cumplidas; UC-18 funciona.
- Funciones de inventario documentadas para OBJ-13.
````

---

## Prompt 11-C — Supervisión de mantenimiento e indicadores

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-11, parte C. Implementa HU-ADM-16, 17 y 18 cumpliendo TODOS sus criterios.

Lee: HU-ADM-16 a 18, docs/07 - Estados.md (sección 8, transiciones M1 a M8), docs/10 - Reglas de Negocio.md (RN-MAN, RN-HAB-003, RN-PAG-017), docs/12 - Casos de Uso.md (UC-08, lado del Admin) y docs/backend-funciones.md (funciones de incidencias creadas en OBJ-13; si aún no existen, coordina con P6 o créalas siguiendo exactamente el documento 07).

1. Bandeja de incidencias REPORTADA: habitación, tipo, descripción, quién reportó, si impide el uso, foto.
2. Asignar (M2): técnico con área MANTENIMIENTO o AMBAS, prioridad y fecha compromiso; sugerir primero técnicos en turno y advertir (sin bloquear) si no lo está (HU-ADM-16).
3. Reasignar con motivo (M3); devolver RESUELTA a EN_PROCESO con comentario (M6); cerrar (M7) mostrando la solución y los repuestos; cancelar con motivo (M8). Al cerrar o cancelar la última orden bloqueante, la habitación pasa a SUCIA (HU-ADM-17, RN-HAB-003).
4. Vista de todas las órdenes con filtros por estado, habitación y técnico.
5. Indicadores (HU-ADM-18): ocupación de hoy y de un rango; ingresos del rango = cargos VIGENTE por fecha, incluidas penalidades (RN-PAG-017), separados en alojamiento, room service y otros; reservas por canal. Gráficas con Recharts (shadcn charts). Rango por defecto: mes actual. Cálculos en funciones SQL, no en el navegador.
6. Pruebas: Playwright del ciclo asignar → (técnico resuelve en OBJ-13) → cerrar; pgTAP de los cálculos de indicadores con datos conocidos.

Criterios de terminado:
- HU-ADM-16 a 18 cumplidas; UC-08 (lado Admin) funciona.
- Los indicadores coinciden con un cálculo manual sobre el seed.
````

## Revisión humana

- [ ] Validar indicadores contra una hoja de cálculo hecha a mano con los datos del seed.
