# OBJ-02 — Implementar autenticación, roles y permisos

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P1 — Datos y seguridad | OBJ-01 | `feat/obj-02-auth-permisos` | ~20 h |

## Antes de empezar

- [ ] OBJ-01 fusionado en `develop`.
- [ ] Para el correo real se necesita OBJ-03 (Resend con dominio). En local se usa el buzón de prueba de Supabase (Inbucket/Mailpit).

---

## Prompt 02-A — Autenticación y perfiles

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es la primera parte del OBJ-02: autenticación del personal y del huésped.

Lee: docs/02 - Definicion de Roles.md, docs/09 - Matriz de Permisos.md (sección 7), docs/10 - Reglas de Negocio.md (RN-APP-001 a 004, RN-PER-001 a 007, RN-SEG), docs/04 - Historias de Usuario/HU - Cliente y Huesped.md (HU-HUE-08) y HU - Administrador.md (HU-ADM-01, 02).

1. Configuración de Supabase Auth (supabase/config.toml y documéntalo en docs/auth.md):
   - Registro público DESHABILITADO.
   - Acceso con correo y contraseña para el personal.
   - OTP por correo para el huésped: código de 6 dígitos, vence en 10 minutos (600 s).
   - Límite de verificación de OTP por IP (RN-APP-002, PAR-15): déjalo en el valor por defecto y documenta dónde se cambia.
   - SMTP: en local usa el buzón de prueba; deja documentado cómo se configura Resend en desarrollo y producción (OBJ-03).
   - Plantilla del correo OTP en español con el nombre del hotel.

2. Funciones auxiliares en SQL (SECURITY DEFINER, search_path fijo, en una migración nueva):
   - rol_actual() → rol del empleado activo del usuario autenticado, o 'HUESPED' si es huésped, o null.
   - area_actual() → área del empleado (o null).
   - empleado_actual_id() y huesped_actual_id().
   - es_empleado_activo() → true solo si el empleado existe y está ACTIVO (RN-SEG-005).

3. Acceso del huésped por OTP sin revelar si el correo existe (HU-HUE-08, RN-APP-003):
   - Route Handler POST apps/web/src/app/api/auth/otp: recibe el correo; con la clave service_role verifica si hay reservas con ese correo y que NO sea correo de un empleado (RN-PER-006). Si hay reservas y el usuario de auth no existe, lo crea (admin.createUser) y vincula huespedes.usuario_id. Luego envía el OTP (signInWithOtp con shouldCreateUser: false). Responde SIEMPRE el mismo mensaje genérico.
   - La verificación del código la hace el cliente con verifyOtp (web y app).
   - Este endpoint lo usarán la web pública y la app móvil.

4. Creación de empleados por el Admin (base para HU-ADM-01; la pantalla es de OBJ-11):
   - Route Handler POST apps/web/src/app/api/empleados: solo si el llamador es ADMIN activo. Crea el usuario (invitación por correo para definir contraseña) y el registro en empleados. Valida con Zod. Rechaza correos ya usados por empleados o huéspedes.
   - Endpoint para desactivar: marca INACTIVO, bloquea el usuario en auth y cierra sus sesiones. (La regla de trabajo en curso RN-PER-003 se agrega en OBJ-11.)

5. Pruebas:
   - pgTAP para las funciones auxiliares (empleado activo, inactivo, huésped, anónimo).
   - Vitest para /api/auth/otp: correo con reservas, sin reservas y de empleado → misma respuesta.

Criterios de terminado:
- Un empleado de prueba entra con contraseña; un huésped de prueba entra con OTP en local.
- Un empleado INACTIVO no puede operar.
- /api/auth/otp no revela si un correo existe.
- docs/auth.md explica la configuración.
````

---

## Prompt 02-B — Políticas RLS y almacenamiento

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es la segunda parte del OBJ-02: aplicar TODA la matriz de permisos con Row Level Security y configurar Storage.

Lee completo: docs/09 - Matriz de Permisos.md (incluye notas ¹ a ⁸ y secciones 6 y 7) y docs/02 - Definicion de Roles.md.

1. Crea políticas RLS (en migraciones nuevas, un archivo por módulo) para cada tabla según la matriz, usando las funciones auxiliares del prompt 02-A. Reglas generales:
   - Catálogos públicos (configuracion_hotel, tipos_habitacion, amenidades, items_menu, categorias_menu, articulos_huesped): lectura para anon y authenticated solo de registros activos; escritura solo ADMIN.
   - Reservas, cuentas, cargos, pagos: ADMIN y RECEPCION leen todo; HUESPED solo lo suyo; ROOM_SERVICE y MANTENIMIENTO_LIMPIEZA no leen (Room Service ve nombre y habitación del huésped solo mediante una vista o función limitada, nota ²).
   - Pedidos: ADMIN y RECEPCION leen; ROOM_SERVICE lee y actualiza; HUESPED solo los suyos.
   - Solicitudes: ADMIN, RECEPCION y MYL con área LIMPIEZA o AMBAS; HUESPED solo las suyas.
   - Incidencias: ADMIN todas; RECEPCION solo lectura; MYL solo asignadas a él, reportadas por él y lectura de las abiertas de una habitación (nota ⁷).
   - Inventario: ADMIN todo; MYL solo lectura de productos.
   - Empleados, turnos, tarifas, canales: solo ADMIN (cada empleado lee su propio perfil y sus turnos).
   - historial_estados: lectura con los mismos permisos que la entidad de origen; escritura nunca directa (solo funciones/triggers).
   - Ninguna tabla de reservas es legible por anon. La disponibilidad pública se expondrá con una función en OBJ-06.
   - Las escrituras que cambian estados se harán mediante funciones SQL (OBJ-06); aquí permite UPDATE directo solo donde la matriz lo indica y no haya transición de estado de por medio.

2. Storage (buckets creados por migración o config):
   - publico: lectura pública, escritura ADMIN (fotos de hotel, habitaciones, menú, amenidades).
   - documentos: privado; ADMIN, RECEPCION y el huésped dueño (carpeta por huesped_id) (RNF-SEC-009).
   - operacion: privado; ADMIN, RECEPCION y MYL (fotos de incidencias y objetos olvidados).
   - Límite de 5 MB y tipos JPG, PNG, PDF (PAR-20).

3. Pruebas pgTAP de autorización: para CADA módulo de la matriz, al menos una prueba de acción permitida (✅) y una rechazada (—) por rol, más:
   - Un huésped no ve reservas de otro huésped.
   - anon no puede leer reservas.
   - Un empleado INACTIVO no lee nada.

4. Documenta en docs/permisos-implementacion.md qué política implementa cada fila de la matriz.

Criterios de terminado:
- supabase test db pasa con todas las pruebas de autorización.
- Ninguna tabla de public queda con RLS activado y sin políticas (salvo que sea intencional y esté documentado).
- Buckets creados con sus políticas.
````

## Revisión humana

- [ ] Probar manualmente en Supabase Studio con usuarios de distintos roles.
- [ ] Revisar que la clave `service_role` solo se use en Route Handlers.
