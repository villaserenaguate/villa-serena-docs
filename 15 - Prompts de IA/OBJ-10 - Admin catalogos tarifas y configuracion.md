# OBJ-10 — Construir Administración: catálogos, tarifas y configuración

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P1 — Datos y seguridad | OBJ-05, OBJ-01 (y la función de precios de OBJ-06 para la vista previa) | `feat/obj-10-a-catalogos`, `-b-tarifas` | ~45 h |

## Antes de empezar

- [ ] OBJ-05 fusionado.
- [ ] Para el prompt B: `calcular_precio_noche` de OBJ-06 (A) disponible.

---

## Prompt 10-A — Catálogos del hotel y configuración general

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-10, parte A, en /panel/admin. Implementa HU-ADM-05, 06, 07, 08 y 19 cumpliendo TODOS sus criterios de aceptación.

Lee: docs/04 - Historias de Usuario/HU - Administrador.md (HU-ADM-05 a 08 y 19), docs/10 - Reglas de Negocio.md (RN-HAB-007, RN-RS-005, 008, PAR-01, 02), docs/09 - Matriz de Permisos.md (3.4 y 3.8) y docs/er.md.

1. Tipos de habitación: listado, crear y editar (nombre, descripción, capacidad, precio base > 0), varias fotos al bucket público con foto principal, activar/desactivar (HU-ADM-05).
2. Habitaciones: número único, piso, tipo; nueva en LIBRE + LIMPIA; no cambiar tipo ni desactivar con reservas futuras u ocupada (HU-ADM-06). Estas validaciones deben existir también en la base de datos (trigger o función), no solo en la interfaz.
3. Menú de Room Service: categorías e ítems (nombre, descripción, categoría, precio, foto opcional), disponibilidad DISPONIBLE/AGOTADO (el Admin es el único que reactiva), activar/desactivar; cambiar el precio no afecta pedidos existentes (HU-ADM-07).
4. Amenidades: nombre, descripción, foto, horario, ubicación, orden y activo (HU-ADM-08).
5. Configuración general del hotel: nombre, descripción, dirección, teléfono, correo, fotos, horas de check-in/out, Wi-Fi y texto de la política de cancelación (HU-ADM-19). Los cambios se reflejan en la web pública (revalidación de caché de Next.js).
6. Formularios con React Hook Form + Zod; tablas con TablaDatos; mensajes en español.
7. Pruebas Playwright: crear tipo con foto, crear habitación, marcar y reactivar ítem agotado; intento de cambiar el tipo de una habitación con reservas futuras rechazado.

Criterios de terminado:
- HU-ADM-05 a 08 y 19 cumplidas.
- Un Admin configura el hotel desde cero sin tocar la base de datos.
````

---

## Prompt 10-B — Tarifas dinámicas y vista previa

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-10, parte B. Implementa HU-ADM-09, 10 y 11 cumpliendo TODOS sus criterios.

Lee: HU-ADM-09 a 11 (incluye la fórmula de la épica 3), docs/10 - Reglas de Negocio.md (RN-TAR-001 a 008), docs/12 - Casos de Uso.md (UC-17) y docs/backend-funciones.md (calcular_precio_noche).

1. Temporadas: nombre, fecha inicio, fecha fin, porcentaje (positivo o negativo) y tipos a los que aplica (todos o seleccionados). La base de datos rechaza traslapes para un mismo tipo (RN-TAR-003) y precios resultantes ≤ 0 (RN-TAR-004). Crea la restricción o función que falte.
2. Ajuste de fin de semana por tipo de habitación (viernes y sábado) (HU-ADM-10).
3. Vista previa: calendario mensual por tipo con el precio final de cada noche, marcas de temporada y fin de semana, y desglose al pasar el cursor. Usa calcular_precio_noche: los precios deben coincidir EXACTAMENTE con la web pública (HU-ADM-11).
4. Mostrar claramente que los cambios solo afectan reservas nuevas (RN-TAR-007).
5. Pruebas: pgTAP de traslape de temporadas y precio ≤ 0; Playwright de crear temporada y ver la vista previa actualizada.

Criterios de terminado:
- HU-ADM-09 a 11 cumplidas; UC-17 funciona.
- Precio de la vista previa = precio de la web pública para las mismas fechas.
````

## Revisión humana

- [ ] Comparar a mano 3 fechas de la vista previa con la búsqueda de la web pública.
