# OBJ-01 — Levantar la base de datos del sistema

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P1 — Datos y seguridad | OBJ-04 (A) | `feat/obj-01-base-datos` | ~25 h |

## Antes de empezar

- [ ] OBJ-04 (A) terminado: el monorepo existe y `docs/` tiene la documentación.
- [ ] Docker instalado y **Supabase CLI** instalado (`supabase --version`).
- [ ] `supabase init` ya ejecutado en la raíz (si no, el prompt A lo hace).

---

## Prompt 01-A — Modelo de datos y migraciones

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-01 (documento docs/13 - Objetivos del Proyecto.md): crear el modelo de datos completo del PMS Villa Serena en Supabase (PostgreSQL) mediante migraciones.

Lee primero, completos:
- docs/07 - Estados.md (todos los estados y códigos)
- docs/08 - Inventario Turnos y Personal.md
- docs/10 - Reglas de Negocio.md (reglas y parámetros PAR-01 a PAR-21)
- docs/02 - Definicion de Roles.md (códigos de rol y área)
- docs/04 - Historias de Usuario/ (hojea todas para detectar datos que cada pantalla necesita)

Pasos:

1. Diseña el diagrama entidad-relación y guárdalo en docs/er.md como diagrama Mermaid (erDiagram), con una breve descripción de cada tabla. Muéstrame el diagrama y espera mi aprobación antes de escribir migraciones.

2. Usa estos nombres de tabla (ajusta solo si encuentras un problema y explícalo):
   configuracion_hotel, tipos_habitacion, habitaciones, amenidades,
   huespedes, reservas, reserva_huespedes (adicionales),
   cuentas, cargos, pagos, reembolsos, comprobantes,
   temporadas, temporada_tipos (si la temporada aplica a tipos específicos),
   categorias_menu, items_menu, pedidos, pedido_items,
   articulos_huesped (catálogo de artículos solicitables), solicitudes, solicitud_items,
   limpiezas, limpieza_consumos, observaciones, objetos_olvidados,
   incidencias (también son las órdenes de mantenimiento), incidencia_repuestos,
   productos, movimientos_inventario, reportes_faltante,
   empleados, turnos, asignaciones_turno,
   canales, canal_peticiones,
   historial_estados.

3. Crea tipos enumerados (enum) con los códigos EXACTOS del documento 07: estado_reserva, ocupacion_habitacion, condicion_habitacion, estado_cuenta, estado_cargo, estado_pago, metodo_pago (EFECTIVO, TARJETA, STRIPE, CANAL, OTRO), estado_pedido, tipo_solicitud (LIMPIEZA, ARTICULOS), estado_solicitud, estado_incidencia, prioridad (ALTA, MEDIA, BAJA), estado_faltante, estado_objeto, disponibilidad_item (DISPONIBLE, AGOTADO), estado_empleado, rol (ADMIN, RECEPCION, ROOM_SERVICE, MANTENIMIENTO_LIMPIEZA), area (LIMPIEZA, MANTENIMIENTO, AMBAS), canal_origen (DIRECTO_WEB, RECEPCION, BOOKING, EXPEDIA), categoria_producto (INSUMO_LIMPIEZA, AMENIDAD_HABITACION, REPUESTO), tipo_movimiento (ENTRADA, AJUSTE, CONSUMO_LIMPIEZA, CONSUMO_ENTREGA, CONSUMO_REPUESTO).

4. Divide las migraciones en archivos lógicos, por ejemplo:
   0001 extensiones (btree_gist, pgcrypto, pg_cron) y enums
   0002 catálogos del hotel
   0003 personal y turnos
   0004 huéspedes, reservas, cuentas, cargos, pagos
   0005 operación (pedidos, solicitudes, limpiezas, incidencias, objetos, observaciones)
   0006 inventario
   0007 canales
   0008 historial de estados e índices

5. Restricciones obligatorias en la base de datos:
   - Sin reservas activas traslapadas por habitación: restricción EXCLUDE USING gist sobre (habitacion_id WITH =, daterange(fecha_entrada, fecha_salida, '[)') WITH &&) aplicada solo cuando estado IN ('PENDIENTE_PAGO','CONFIRMADA','EN_ESTADIA') y habitacion_id no es nulo.
   - fecha_salida > fecha_entrada; estadía de 1 a 30 noches (RN-RES-005).
   - Stock de productos >= 0 (RN-INV-002).
   - Montos numeric(10,2) >= 0 donde aplique.
   - UNIQUE: correo de empleado, número de habitación, código de reserva, (canal_id, id_externo) en reservas, número correlativo de comprobante.
   - Empleado con rol MANTENIMIENTO_LIMPIEZA requiere área (CHECK); otros roles, área nula.
   - Una cuenta por reserva (UNIQUE reserva_id en cuentas).
   - Solicitudes: como máximo 1 solicitud de LIMPIEZA activa (PENDIENTE o EN_PROCESO) por habitación (índice único parcial, RN-LIM-005).
   - Reportes de faltante: como máximo 1 PENDIENTE por producto (índice único parcial, RN-INV-009).

6. Columnas comunes: id uuid (default gen_random_uuid()), creado_en timestamptz default now(), actualizado_en. Las entidades de catálogo tienen activo boolean. Los registros hechos por personal guardan el empleado responsable (creado_por, etc.).

7. Relaciona empleados y huespedes con auth.users mediante una columna usuario_id (uuid, nullable para huéspedes que aún no han entrado).

8. historial_estados: tabla genérica con entidad (texto), entidad_id, estado_anterior, estado_nuevo, actor_id, actor_tipo (EMPLEADO, HUESPED, SISTEMA, PASARELA, CANAL), motivo, creado_en. Los triggers que la llenan se hacen en OBJ-06; aquí solo la tabla.

9. Índices para búsquedas frecuentes: fechas de reserva, estado, documento y correo del huésped, código de reserva, canal, habitacion_id.

10. Activa Row Level Security en TODAS las tablas del esquema public (sin políticas todavía: todo queda denegado; las políticas se crean en OBJ-02). Déjalo indicado en un comentario.

11. NO crees todavía funciones de negocio, triggers de transición ni políticas: eso es OBJ-06 y OBJ-02.

12. Ejecuta supabase db reset y confirma que todas las migraciones corren sin errores desde cero.

13. Genera los tipos TypeScript en packages/shared (script pnpm gen:types si existe; si no, créalo).

Criterios de terminado:
- docs/er.md con el diagrama aprobado.
- supabase db reset corre sin errores.
- Insertar dos reservas activas traslapadas en la misma habitación es rechazado por la base de datos.
- RLS activado en todas las tablas.
- Tipos generados en packages/shared.
````

---

## Prompt 01-B — Datos de prueba y pruebas del esquema

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Continúas el OBJ-01. El esquema ya existe en supabase/migrations. Ahora crea los datos de prueba y las primeras pruebas automáticas.

Lee: docs/er.md, docs/10 - Reglas de Negocio.md (sección 2, parámetros) y docs/08 - Inventario Turnos y Personal.md.

1. Crea supabase/seed.sql con datos realistas en español:
   - configuracion_hotel: Villa Serena, dirección ficticia en Antigua Guatemala, check-in 15:00, check-out 12:00, Wi-Fi, texto de la política de cancelación según RN-CAN-002 a 007.
   - 3 tipos de habitación (ej. Estándar Q450, Superior Q650, Suite Q950) con capacidad y descripción.
   - 12 habitaciones en 3 pisos, todas LIBRE + LIMPIA.
   - 1 temporada alta (+20 %) en diciembre y ajuste de fin de semana (+10 %) por tipo.
   - 4 categorías y ~15 ítems de menú; ~6 amenidades.
   - Catálogo de artículos para huésped: toallas, almohadas, cobijas (sin producto) y jabón, shampoo, papel higiénico (vinculados a productos AMENIDAD_HABITACION).
   - ~12 productos de inventario con stock y stock mínimo, de las 3 categorías.
   - 3 turnos (Mañana, Tarde, Noche).
   - Empleados de prueba, 1 por rol (y 2 de MANTENIMIENTO_LIMPIEZA: uno LIMPIEZA y uno MANTENIMIENTO). Crea también sus usuarios en auth.users para el ambiente local, con contraseñas de prueba documentadas en docs/usuarios-prueba.md (solo para local).
   - 2 canales: Booking y Expedia (ACTIVO), sin clave todavía.
   - Algunas reservas de ejemplo en distintos estados para poder ver datos en pantallas.

2. Crea pruebas pgTAP en supabase/tests/ que verifiquen:
   - Rechazo de reservas traslapadas en la misma habitación.
   - Aceptación de reservas consecutivas (salida de una = entrada de otra).
   - Rechazo de stock negativo.
   - Rechazo de empleado MANTENIMIENTO_LIMPIEZA sin área.
   - Rechazo de 2 solicitudes de limpieza activas en la misma habitación.
   - Que RLS está activado en todas las tablas de public.

3. Ejecuta supabase db reset y supabase test db; todo debe pasar.

Criterios de terminado:
- El seed carga sin errores y los datos se ven en Supabase Studio local.
- Todas las pruebas pgTAP pasan.
- docs/usuarios-prueba.md indica que esas credenciales son SOLO para local.
````

## Revisión humana

- [ ] El diagrama ER cubre todas las entidades de las HU (revisar con P2 y P4).
- [ ] Los enums coinciden letra por letra con el documento 07.
- [ ] No hay políticas ni lógica de negocio adelantada (eso es OBJ-02 y OBJ-06).
