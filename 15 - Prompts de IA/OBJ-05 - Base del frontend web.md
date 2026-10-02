# OBJ-05 — Construir la base del frontend web

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P4 — Web base y Recepción | OBJ-02, OBJ-04 | `feat/obj-05-base-web` | ~15 h |

## Antes de empezar

- [ ] OBJ-02 y OBJ-04 (A) fusionados en `develop`.
- [ ] **Si existe el frontend de Kim**, indicar su ruta en el prompt para reutilizar sus componentes y estilos.

---

## Prompt 05-A — Estructura, sesión, layout y componentes compartidos

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-05: dejar lista la base de la web (apps/web) sobre la que se construyen todos los módulos.

Lee: docs/14 - Tecnologias y Arquitectura.md (secciones 4.1 y 6), docs/02 - Definicion de Roles.md, docs/07 - Estados.md (etiquetas y colores de la sección 4.3 y tablas de estados) y docs/09 - Matriz de Permisos.md.

1. Estructura de rutas (App Router) en apps/web/src/app:
   - (publico)/ con layout público (encabezado, pie) y página de inicio provisional.
   - login/ para el personal.
   - (panel)/panel/ con layout del panel y subcarpetas recepcion, room-service, piso, admin (cada una con una página inicial provisional).

2. Supabase en la web con @supabase/ssr:
   - Cliente de navegador, cliente de servidor (cookies) y middleware que refresca la sesión.
   - Utilidad de servidor obtenerUsuarioActual() que devuelve el usuario, su rol y área (usando las funciones SQL de OBJ-02).

3. Login del personal (correo y contraseña) con React Hook Form + Zod, mensajes de error en español y cierre de sesión.

4. Protección de rutas por rol (middleware + verificación en el layout del servidor):
   - /panel/recepcion: RECEPCION y ADMIN.
   - /panel/room-service: ROOM_SERVICE y ADMIN (el Admin solo consulta, P-01).
   - /panel/piso: MANTENIMIENTO_LIMPIEZA y ADMIN (el Admin solo consulta).
   - /panel/admin: solo ADMIN.
   - Un empleado INACTIVO o un rol sin permiso ve una pantalla "Sin permiso".
   - Después del login, cada rol va a su sección inicial.

5. Layout del panel: menú lateral según el rol (y el área, en MYL), nombre del usuario, rol y turno actual si existe, y cierre de sesión. Responsive: en teléfono el menú se colapsa.

6. Componentes compartidos (shadcn/ui + propios) en apps/web/src/components:
   - Instala los componentes de shadcn/ui necesarios (button, input, select, dialog, table, tabs, toast/sonner, badge, card, calendar, popover, dropdown-menu, form, skeleton).
   - EstadoBadge: recibe tipo de entidad y código de estado y muestra la etiqueta y color del documento 07.
   - TablaDatos: TanStack Table con búsqueda, filtros, orden y paginación.
   - SelectorRangoFechas (entrada/salida) con validaciones básicas.
   - Formato: formatoQuetzales(n) → "Q 1,250.00"; formatoFecha y formatoFechaHora en America/Guatemala.
   - Componentes de estado vacío, carga y error.

7. packages/shared:
   - src/estados.ts: todos los códigos de estado del documento 07 como constantes y tipos, con su etiqueta en español y color sugerido. EstadoBadge debe usar este archivo (también lo usará la app).
   - src/validaciones/: esquemas Zod base (correo, teléfono, documento DPI/pasaporte, rango de fechas con RN-RES-005 y 006).

8. Tiempo real: hook reutilizable useSuscripcionTabla(tabla, filtro, alCambiar) con Supabase Realtime que invalide consultas de TanStack Query.

9. Pruebas: Vitest para formatoQuetzales, fechas y esquemas Zod; Playwright para login y redirección por rol (con usuarios del seed).

Criterios de terminado:
- Cada rol de prueba entra y ve solo su menú.
- Abrir la ruta de otro rol muestra "Sin permiso" o redirige.
- EstadoBadge muestra las etiquetas del documento 07 desde packages/shared.
- pnpm build de la web pasa en CI.
````

## Revisión humana

- [ ] Probar el login con cada usuario del seed.
- [ ] Revisar en teléfono (o con las herramientas de desarrollo del navegador) que el panel es usable.
