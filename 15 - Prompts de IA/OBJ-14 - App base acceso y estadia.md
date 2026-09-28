# OBJ-14 — Construir la app Android: base, acceso y estadía

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P6 — App móvil y personal de piso | OBJ-02, OBJ-06 | `feat/obj-14-a-app-base`, `-b-app-estadia` | ~35 h |

## Antes de empezar

- [ ] OBJ-02 y OBJ-06 fusionados; `/api/auth/otp` funcionando en desarrollo.
- [ ] Expo Go instalado en el teléfono (Play Store o App Store, versión para **SDK 54**).
- [ ] Cuenta de Expo del equipo creada (OBJ-03).

---

## Prompt 14-A — Proyecto Expo, acceso OTP y mis reservas

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-14, parte A: crear la app móvil del huésped en apps/mobile con React Native + Expo SDK 54, e implementar el acceso y la lista de reservas (HU-HUE-08 y HU-HUE-09, versión app).

Lee: docs/14 - Tecnologias y Arquitectura.md (secciones 4.2, 6 y 10), docs/04 - Historias de Usuario/HU - Cliente y Huesped.md (HU-HUE-08, 09, 12), docs/10 - Reglas de Negocio.md (RN-APP-001 a 006), docs/auth.md y docs/09 - Matriz de Permisos.md.

1. Proyecto:
   - Crea apps/mobile con create-expo-app en TypeScript, fijando Expo SDK 54 (verifica que "expo" quede en ~54.x) y Expo Router.
   - Integra el monorepo: usa packages/shared (tipos, Zod, estados) y verifica que Metro resuelve el paquete (con node-linker=hoisted ya configurado).
   - NativeWind para estilos con la misma paleta de la web; componentes base: Boton, Campo, Tarjeta, EstadoBadge (etiquetas y colores desde packages/shared), Cargando, Vacio, Error.
   - Solo módulos incluidos en Expo SDK 54 (regla de Expo Go). Nada de código nativo propio.
   - Variables EXPO_PUBLIC_SUPABASE_URL, EXPO_PUBLIC_SUPABASE_ANON_KEY y EXPO_PUBLIC_API_URL (URL de la web para /api/auth/otp y /api/pagos/checkout).
   - Esquema de enlaces (scheme) "villaserena" en app.json.

2. Supabase en la app: cliente con la sesión guardada en expo-secure-store (RNF-SEC-010), refresco automático al volver a primer plano, TanStack Query.

3. Acceso (HU-HUE-08): pantalla de correo → POST a /api/auth/otp (misma respuesta genérica) → pantalla de código de 6 dígitos → verifyOtp. Mensajes de error y de límite de intentos. La sesión se mantiene hasta cerrar sesión.

4. Mis reservas (HU-HUE-09): próximas, en curso y pasadas, con código, fechas, tipo, huéspedes, estado y total. Al abrir la app, si hay una estadía EN_ESTADIA, ir directo a ella.

5. Navegación (Expo Router): grupo (auth) sin sesión y grupo (principal) con pestañas: Inicio/Estadía, Room Service, Solicitudes, Amenidades, Cuenta. Las pestañas de servicios se muestran deshabilitadas con un mensaje si la reserva no está EN_ESTADIA (RN-APP-005); se implementan en OBJ-15.

6. Sentry (@sentry/react-native) con DSN por variable de entorno, sin datos personales.

7. Pruebas: pruebas unitarias de utilidades y del flujo de acceso (mockeando Supabase) con el runner que recomiende Expo; prueba manual documentada en Expo Go.

Criterios de terminado:
- pnpm --filter mobile start muestra el QR y la app corre en Expo Go en un Android real.
- Un huésped del seed entra con OTP y ve solo sus reservas.
````

---

## Prompt 14-B — Estadía, amenidades, cuenta, historial, check-in anticipado y APK

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-14, parte B en apps/mobile. Implementa HU-HUE-11 (versión app), HU-HUE-12, HU-HUE-18, HU-HUE-19 y HU-HUE-21 cumpliendo TODOS sus criterios, y configura el APK.

Lee: HU-HUE-11, 12, 18, 19, 21, docs/10 - Reglas de Negocio.md (RN-APP-005 a 009, RN-PAG-012), docs/12 - Casos de Uso.md (UC-11, UC-12) y docs/backend-funciones.md (estado_cuenta, check-in anticipado).

1. Detalle de estadía (HU-HUE-12): código, fechas, hora de check-out, tipo y número de habitación (si está asignada), huéspedes, estado; selector si hay varias reservas.
2. Check-in anticipado (HU-HUE-11): igual que en la web — desde 24 h antes, solo CONFIRMADA; datos, acompañantes, foto del documento con expo-image-picker o la cámara (JPG/PNG, máx. 5 MB) al bucket privado "documentos", peticiones especiales.
3. Amenidades (HU-HUE-18): lista con foto, descripción, horario y ubicación; Wi-Fi (red y contraseña) desde configuracion_hotel con botón para copiar.
4. Mi cuenta (HU-HUE-19): alojamiento, cargos, pagos y saldo con estado_cuenta; se actualiza en tiempo real (Supabase Realtime); solo lectura.
5. Historial (HU-HUE-21): estadías FINALIZADA con detalle de cuenta, sin acciones (RN-APP-006).
6. APK con EAS Build:
   - eas.json con perfil "preview" (buildType apk, variables de producción) y "development".
   - Nombre "Villa Serena", ícono y pantalla de inicio con la identidad del hotel, package com.villaserena.app (o el que defina el equipo).
   - Documenta en apps/mobile/README.md cómo ejecutar en Expo Go y cómo generar el APK (eas build -p android --profile preview y la alternativa --local).
   - Verifica que el workflow apk.yml de OBJ-04 funciona con este proyecto.
7. Pruebas manuales documentadas y unitarias de formateo de cuenta.

Criterios de terminado:
- HU-HUE-11 (app), 12, 18, 19 y 21 cumplidas.
- El APK generado con EAS Build se instala en un Android real y se conecta a producción.
````

## Revisión humana

- [ ] Instalar el APK en al menos dos teléfonos Android distintos.
- [ ] Verificar que no hay claves de servidor en el código de la app (solo la anon key pública).
