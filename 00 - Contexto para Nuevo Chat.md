# 00 — Contexto para continuar en un nuevo chat

> **Proyecto:** PMS (Property Management System) para el hotel boutique ficticio **"Villa Serena"**
> **Fecha:** 30 de septiembre de 2026 (actualizado el 1 de octubre de 2026, con el plan 13 aprobado y los cinco repositorios)
> **Para qué sirve:** reúne todo lo necesario para que un chat nuevo (o cualquier integrante del equipo) continúe el trabajo de documentación **sin repetir decisiones ya tomadas**.
> **Cómo usarlo:** en el chat nuevo, pide que lea este archivo primero y después los documentos de la carpeta `Documentación V3`. Al final hay un mensaje de arranque listo para copiar (sección 12).

---

## 1. El proyecto en pocas palabras

Es el **proyecto final del curso de Desarrollo Web**. Es un sistema de administración para un hotel **ficticio**, con tres partes:

| Parte | Quién la usa | Tecnología |
|---|---|---|
| **Web pública** (motor de reservas) | Cliente | Next.js + React |
| **Web privada** (Recepción, Room Service, Mantenimiento/Limpieza, Administrador) | Personal del hotel | Next.js + React (BFF) |
| **App Android** (conserjería) | Huésped | React Native + Expo |
| **Backend / API REST** | Todo lo anterior | Spring Boot + PostgreSQL |

**Flujo principal** (todo el Nivel 1 existe para que este flujo funcione de principio a fin):

1. **Reservar** en la web, pagando el 100 % con Stripe en modo prueba. Recepción y el canal simulado también crean reservas.
2. **Check-in** en Recepción, con habitación libre y limpia.
3. **Estadía** en la app: room service, limpieza, artículos y notificaciones push.
4. **Operación** en la web privada: Room Service, Limpieza y Mantenimiento.
5. **Check-out** (en la app o en Recepción): pago único, factura (impresa y por correo) y la habitación pasa a Libre + Sucia.

---

## 2. Equipo y fechas

| Integrante | Frente |
|---|---|
| **Josué (Joss)** | Arquitectura, infraestructura, migraciones, contrato e integración |
| Kim | Frontend web |
| Carlos | App móvil |
| Alex | Web: BFF, resultado del pago, canal simulado, Room Service, incidencias y cliente de tiempo real |
| Pablo | API: seguridad, reservas, pagos, cuentas y check-in/out |
| Hugo | API: base, habitaciones, limpieza, operación, Outbox, WebSocket y factura |

- **Hito del 10 de octubre de 2026** (sábado): el sistema debe estar **casi terminado y funcionando en local** (backend, web y app, con Docker). **No es la entrega final.** El equipo busca conseguir más tiempo.
- VPS, Cloudflare, CI/CD y backups van **después** del 10 de octubre.
- Los frentes son un reparto inicial: todos tienen habilidades parecidas y trabajan con IA; las tareas se pueden mover.
- **20 h por integrante:** días seguros viernes 2 y lunes 5 a viernes 9, máximo 3 h por día (18 h seguras); hasta 2 h opcionales entre jueves 1 y domingo 4. El jueves solo bases/repositorios/Docker si se terminan hoy el plan y los prompts del objetivo 0; el domingo solo recuperación, sin tareas fijas.
- Viernes 9: 18 h del equipo para integración. Capacidad nominal de construcción: **102 h**, de las cuales 12 h son opcionales; segura: **90 h**. Estimación activa: **108,5 h**; se acepta la diferencia nominal de **6,5 h como margen de error**, no como horas extra.
- Objetivo 5 fuera del hito. Los otros tres recortes quedan de reserva para decidir el martes 6: check-out solo en Recepción, Gantt básico y sin huéspedes adicionales, en ese orden.

---

## 3. Cómo trabajar con Joss (importante)

1. **Responder siempre en español.** Respuestas concisas.
2. **Criterio principal: no aumentar el trabajo.** Ante cada regla o caso, la pregunta es: *"¿Es realmente necesario para que el sistema funcione o para presentar el proyecto? Es una empresa ficticia."* Si la respuesta es no, **se quita o se acepta como limitación**.
   - Ejemplo dado por Joss: que el cliente modifique su reserva es **innecesario** (Recepción cancela y crea otra).
   - Ejemplo: "¿qué pasa con el trabajo en curso de un empleado que se desactiva?" → **no es necesario**: nadie se desactivará a media presentación.
3. **Las propuestas deben preferir, en este orden:** quitar algo → aceptar una limitación → corregir una frase. **Nunca** proponer pantallas, estados o procesos nuevos salvo que algo indispensable se rompa.
4. **Primero el informe, después los cambios.** Cuando Joss pide revisar, se entrega un informe sin modificar nada. Él responde punto por punto y luego se aplica.
5. **No editar archivos mientras Joss los está leyendo o revisando.**
6. Los cambios van en la **carpeta nueva** (`Documentación V3`). La carpeta `Documentación Definitiva` (v2) **no se toca**.
7. Lenguaje simple: frases cortas, tablas cuando ayudan y sin relleno. En las referencias se escribe la palabra "sección".
8. **Trabajo en paralelo con GPT:** algunos documentos los redacta GPT (así se hicieron el 10 y el 11) y Claude los revisa con un informe. Joss pasa los archivos entre ambos.

---

## 4. Dónde está todo

### 4.1 En la computadora de Joss

Carpeta raíz: `D:\Bibliotecas Joss\Universidad 2do\Proyecto Final Desarrollo Web\`

| Carpeta / archivo | Qué es | Estado |
|---|---|---|
| `Documentación Inicial - Proyecto Final\` | Documentos originales (Notion) y la primera revisión | Histórico |
| `Documentación Definitiva\` | **Versión 2**: documentos 01 a 15 (incluidos los prompts de IA OBJ-01 a OBJ-20), el registro de cambios y el **Reporte de Revisión 2** (94 hallazgos) | Histórico; **no modificar** |
| **`Documentación V3\`** | **Versión 3 (la vigente)** | **En construcción** |
| `Frontend Villa Serena\villaserena-web\` | Código del frontend web (Kim) | No revisado en estas sesiones |

**Contenido de `Documentación V3\`:**

| Archivo | Estado |
|---|---|
| `00 - Contexto para Nuevo Chat.md` | Este documento |
| `01 - Alcance del Proyecto.md` | ✅ Versión 3 terminada |
| `04 - Historias de Usuario\` (índice + 7 archivos, 68 HU) | ✅ Versión 3 terminada, revisada y con los vacíos corregidos |
| `Reporte de Vacios - Historias de Usuario V3.md` | ✅ Aplicado completo |
| `02 - Definicion de Roles.md` | ✅ 5 roles y 3 actores que no son usuarios (SISTEMA, STRIPE, CANAL) |
| `07 - Estados.md` | ✅ Estados, transiciones y efectos de cada entidad |
| `08 - Inventario Turnos y Personal.md` | ✅ Personal, perfil del huésped y catálogo de artículos; turnos e inventario aislado en Nivel 2 |
| `09 - Matriz de Permisos.md` | ✅ Permisos por rol (notas 1 a 14) |
| `10 - Reglas de Negocio.md` | ✅ 170 reglas (RN) y 21 parámetros (PAR). Redactado por GPT y revisado |
| `11 - Requisitos Funcionales.md` | ✅ 113 requisitos (RF). Redactado por GPT y revisado |
| `12 - Casos de Uso.md` | ✅ 19 casos de uso |
| `14 - Tecnologias y Arquitectura.md` | ✅ Decisiones AD-01 a AD-19, BFF, seguridad y entorno local |
| `13 - Plan de Trabajo.md` | ✅ T-01 a T-10 y R-01 a R-04 aplicados; calendario con máximo 3 h por día seguro y margen de error aceptado |
| `15 - Prompts de IA\` | ✅ Objetivo 0 listo: `AGENTS.md`, `CLAUDE.md`, guía `00 - Como usar los prompts.md` y OBJ-0A a OBJ-0F. Pendiente: contrato del API y objetivos 1 a 4 |
| `16 - Guia de Arranque del Proyecto.md` | ✅ Instalar, `.env`, encender y apagar el entorno, retomar el trabajo y errores comunes |

### 4.2 En el proyecto de Claude ("Proyecto Final Desarrollo Web")

- `claude/V3/...` → copia de la versión 3 (mismos archivos).
- `claude/...` (sin V3) → versión 2, solo como referencia.

> **Aviso técnico:** en sesiones anteriores, un editor abierto en la computadora de Joss (con autoguardado) **revirtió archivos** recién guardados. Si un archivo "vuelve" a una versión vieja, hay que cerrar o recargar la pestaña del editor y volver a guardar.
>
> **Saltos de línea:** los archivos 07, 10, 11, `HU - Cliente y Huesped.md` y `HU - Recepcionista.md` usan saltos de línea de Windows (CRLF), porque GPT los guardó así; los demás usan LF. Al editarlos hay que conservar el formato que ya tienen.

---

## 5. Cómo se llegó aquí

| Fase | Qué se hizo |
|---|---|
| 1 | Revisión de la documentación inicial (Notion) |
| 2 | Documentación v2 completa (01 a 15): alcance de 81 funcionalidades, 101 HU, estados, reglas, permisos, casos de uso, arquitectura y 20 objetivos con prompts de IA |
| 3 | Cambio de tecnologías impuestas por el catedrático y corrección en cascada de toda la v2 |
| 4 | **Reporte de Revisión 2**: 94 hallazgos (18 altos, 44 medios, 32 bajos) |
| 5 | Nueva fecha (10 de octubre) → **recorte del alcance**: nace la versión 3 |
| 6 | Alcance v3 + 68 HU v3 |
| 7 | **Reporte de vacíos de las HU v3**: 17 puntos, todos aprobados y aplicados (30 de septiembre) |
| 8 | **Tres revisiones de inconsistencias de las HU v3** (30 sep – 1 oct): 25 hallazgos, todos aprobados y aplicados en las HU, el índice y el Alcance (D-10, D-13 y D-16). No se guardó un reporte aparte; las decisiones están en la sección 7 del índice de HU y aquí abajo |
| 9 | **Documentos derivados** (1 oct): 02, 07, 08, 09, 12 y 14 (Claude) y 10 y 11 (GPT). Cada uno se revisó con informe y se aplicaron las correcciones aprobadas. Al final se agregaron referencias cruzadas: cada HU tiene "Reglas relacionadas", cada caso de uso tiene "Estados (07)" y "Reglas (10)", y el 14 indica qué reglas protege cada restricción |

---

## 6. Por qué el alcance y las historias son tan simples

Es una decisión **deliberada** del equipo, no un descuido. Un chat nuevo **no debe "mejorar" las historias** agregando validaciones, estados o casos borde.

### 6.1 Las razones

1. **El tiempo no alcanza.** Las estimaciones de 820, 440 y 220 h pertenecen a etapas anteriores. El plan 13 vigente estima **108,5 h de construcción frente a 102 h nominales** (90 h seguras), después de recortar Administración. La diferencia nominal se acepta como margen de error y el martes 6 se deciden los recortes de reserva. Cada regla extra empeora el problema.
2. **El hotel es ficticio.** No hay huéspedes reales, ni dinero real (Stripe en modo prueba), ni facturas ante la SAT. Los casos que en un hotel real serían graves (fraude, auditoría, conflictos entre empleados, salidas tarde) **no ocurren en una presentación**.
3. **Lo que se evalúa** son las **tecnologías obligatorias** del catedrático (Spring Security + JWT, BFF con Next.js, PostgreSQL, Stripe, Cloudflare, Grafana, API REST + CORS) y **un flujo completo que funcione**. No se evalúa la cantidad de reglas de negocio.
4. **Cada regla extra cuesta varias veces:** se diseña, se programa en el backend, se refleja en la web o la app, se prueba y se documenta. Quitar una regla ahorra todo eso.
5. **Criterio del recorte (Alcance, sección 1):** una funcionalidad se conserva solo si **la pidió el ingeniero** o si sin ella **se rompe el flujo principal**.

### 6.2 Cómo se nota en los documentos

| En lugar de… (v2 o "sistema real") | La v3 hace… | Por qué basta |
|---|---|---|
| Abonos, anticipos y pagos parciales | **Pago único**: 100 % al reservar en la web; todo en el check-out en Recepción | Un solo flujo de pago |
| Modificar reservas | Recepción **cancela y crea otra** | Evita recalcular precios y disponibilidad |
| Penalidades y reembolsos parciales | Cancelación **todo o nada** (48 h) y solo desde Recepción | Una sola regla |
| Estado `No-show` automático | Recepción cancela con el motivo "No se presentó" | Un estado y un proceso menos |
| Cambio de habitación en la estadía | Solo **antes** del check-in | El daño se atiende con el huésped dentro |
| Facturas con anulación y copias | Emitir, imprimir y enviar el PDF; serie fija cargada al arrancar | Lo mínimo para demostrar la impresión |
| Mantenimiento con 6 estados y asignación | 3 estados; el técnico **toma** la incidencia | Sin pantalla de asignación |
| Inventario integrado | Sin integración (inventario aislado en Nivel 2) | Room Service solo marca "Agotado" |
| Tiempo real para todo | Solo **4 eventos** | El resto se actualiza al abrir la pantalla |
| Avisos, sonidos, indicadores de conexión | Solo el aviso visual | El sonido pasa a Nivel 2 |
| Horas de check-in y check-out configurables | **Fijas**: 15:00 y 12:00 | Una opción de configuración menos |
| Validaciones defensivas ("último Admin", cupo por noche, datos fiscales faltantes) | Reglas simples o datos cargados al arrancar | El caso no puede ocurrir en la demostración |
| Administrador con acceso a todo | Cada rol usa **solo sus pantallas** | Menos permisos que probar |

**Regla para el chat nuevo:** si algo parece "faltar", primero hay que revisar si fue **descartado a propósito** (Alcance, sección 7; índice de HU, secciones 6 y 7; Reporte de Vacíos). Solo se propone algo si **rompe el flujo principal**, y siempre con la opción que menos trabajo agregue.

---

## 7. Decisiones vigentes (resumen)

El detalle está en `01 - Alcance del Proyecto.md`, sección 3.

| # | Decisión |
|---|---|
| D-01 a D-03 | Un solo hotel, quetzales (GTQ), todo en español |
| D-04 | Stripe en modo prueba, confirmado por webhook |
| D-05 | Channel Manager **simulado**: solo recibe reservas por la API |
| D-06 | Personal en la web privada; huésped solo en la app Android |
| D-07 / D-08 | Reserva sin cuenta; el huésped entra a la app con correo + código de 6 dígitos (OTP) |
| D-10 | React Native + Expo SDK 54, APK con EAS Build |
| D-11 | Tecnologías obligatorias del catedrático (sección 8) |
| D-12 / D-22 | Todo en una VPS, pero **después** del 10 de octubre; el hito es **en local con Docker** |
| D-13 / D-18 | Factura de demostración (sin SAT), impresión en 80 mm o carta, PDF por correo; sin anulación ni "COPIA"; serie fija cargada al arrancar |
| D-14 | Pago único, sin abonos |
| D-15 | No se modifican reservas |
| D-16 | Cancelación todo o nada (≥ 48 h antes de las 15:00 del día de llegada: reembolso total; con menos tiempo o si no llega: nada). Solo Recepción cancela |
| D-17 | Sin cambio de habitación durante la estadía |
| D-19 | Mantenimiento: Reportada → En proceso (el técnico la toma) → Resuelta |
| D-20 | Sin integración de inventario |
| D-21 | Tiempo real solo para: nuevo pedido, cambio de estado del pedido, nueva solicitud y cambio de estado de habitación |

**Decisiones tomadas el 30 de septiembre (Reporte de Vacíos, ya aplicadas):**

- **Sin estado `No-show`:** Recepción cancela con el motivo "No se presentó", sin reembolso. No se envía correo de cancelación.
- **Check-in** permitido desde la fecha de entrada hasta el día anterior a la salida (llegadas después de medianoche).
- **Salida tarde:** nada automático y sin cargo; Recepción hace el check-out cuando el huésped baje.
- **Datos iniciales (Flyway):** canales y sus claves, catálogo de artículos, datos del hotel y fiscales, serie y número inicial de la factura, usuarios de prueba de cada rol y **dos Administradores**.
- **Horas fijas:** 15:00 y 12:00.
- **El Administrador** usa solo sus pantallas y el canal simulado.
- **Factura:** total con "IVA incluido", sin desglose de impuestos.
- **Se quitaron (30 sep):** validación de cupo al desactivar una habitación, validación del "último Administrador" (basta con que nadie pueda desactivarse a sí mismo), registro de desactivaciones, aviso de reservas asignadas al quedar `Fuera de servicio`, indicador "Sin conexión", sonido del aviso, bloqueo del check-out por datos fiscales faltantes y edición de la serie de la factura.

**Decisiones de las revisiones de inconsistencias (30 sep – 1 oct, ya aplicadas):**

| Tema | Decisión |
|---|---|
| Cancelaciones | Recepción solo cancela reservas `Confirmada`. Las `Pendiente de pago` se cancelan solas a los 30 minutos, con los mismos efectos (cuenta cerrada, cupo libre) |
| Reservas de canal | No se cancelan desde el sistema, ni siquiera si el huésped no llega: quedan `Confirmada` (limitación aceptada) |
| Datos del huésped | Los mismos 6 datos obligatorios en web, Recepción y canal (nombre, correo, teléfono, nacionalidad, tipo y número de documento). El huésped se identifica por su **correo**: si ya existe, se usa ese perfil sin cambiar sus datos |
| Indicadores | Ingresos = suma de los pagos `Aprobado` del rango (el reembolsado deja de sumar). Reservas por canal = todas las creadas en el rango, incluidas las canceladas |
| "Llegan hoy" | Solo muestra las entradas de hoy; quien llega después de medianoche se busca por nombre o código (limitación aceptada) |
| Sesiones | App: token de acceso de 15 min + refresh de 7 días que se renueva en cada uso. Empleado desactivado: su sesión abierta sigue hasta que vence el token de acceso (máx. 15 min) |
| Factura | Muestra los pagos de la cuenta (método y monto). Métodos de pago: Stripe, Canal, Efectivo, Tarjeta u Otro |
| Reportar daños | Solo Recepción y Mantenimiento/Limpieza (el Administrador no) |
| Tiempo real en la app | La app se conecta al WebSocket con su propio JWT de huésped, sin pasar por el BFF (describirlo en el documento 14 v3) |
| Se quitaron (revisiones) | Recalcular el precio si la tarifa cambia durante la reserva, información fija si la configuración no carga, bloqueo del correo de un empleado como huésped, revisión del estado del empleado en cada acción y cancelación manual de reservas `Pendiente de pago` |

**Decisiones de los documentos 02 a 14 (1 oct, ya aplicadas):**

| Tema | Decisión |
|---|---|
| Códigos de estado (07) | Reserva: `PENDIENTE_PAGO`, `CONFIRMADA`, `EN_ESTADIA`, `FINALIZADA`, `CANCELADA`. Habitación: `LIBRE`/`OCUPADA` + `LIMPIA`/`SUCIA`/`EN_LIMPIEZA`/`FUERA_DE_SERVICIO`. Pedido: `NUEVO`, `EN_PREPARACION`, `EN_CAMINO`, `ENTREGADO`, `CANCELADO`. Solicitud: `PENDIENTE`, `EN_PROCESO`, `ATENDIDA`, `CANCELADA`. Incidencia: `REPORTADA`, `EN_PROCESO`, `RESUELTA`. Cuenta: `ABIERTA`/`CERRADA`. Cargo: `VIGENTE`/`ANULADO`. Pago: `PENDIENTE`, `APROBADO`, `FALLIDO`, `REEMBOLSADO`. Factura: `EMITIDA`. Artículo del menú: `DISPONIBLE`/`AGOTADO`. Catálogos: `ACTIVO`/`INACTIVO` |
| Métodos de pago | `STRIPE`, `CANAL`, `EFECTIVO`, `TARJETA`, `OTRO` |
| Roles y permisos (02, 09) | Cada rol usa **solo sus pantallas** (R-ROL-08). Los cargos se anulan solo con la cuenta `ABIERTA` (nota 14 del 09). La cuenta se puede consultar en cualquier estado |
| Sesión web (14, sección 6.1) | Next.js como BFF: el JWT va en una cookie httpOnly, se revisa el `Origin` contra CSRF y la web pide a Spring un ticket de 60 s para abrir el WebSocket |
| Tiempo real | Solo 4 eventos. Temas: `/topic/pedidos` (Room Service), `/user/queue/pedidos` (huésped), `/topic/solicitudes` (Mantenimiento/Limpieza con área Limpieza o Ambas) y `/topic/habitaciones` (Recepción y Mantenimiento/Limpieza con área Limpieza o Ambas) |
| Push y correos | Push solo para pedido `ENTREGADO` y solicitud `ATENDIDA`. Correos solo para el código OTP, la confirmación de la reserva y la factura |
| Tareas automáticas | `@Scheduled` solo para cancelar a los 30 min las reservas web sin pago y reintentar correos y push (Outbox) |
| Stripe | Stripe Checkout; Spring escucha solo 2 webhooks (sesión pagada y sesión vencida); en local se usa Stripe CLI |
| Zona horaria (AD-19) | America/Guatemala: las fechas se guardan en UTC y se convierten para calcular "hoy", las 48 h, el check-in y el check-out |
| Archivos subidos | Solo JPG o PNG de hasta 5 MB |
| Room Service | Sin horario de atención (se retiró PAR-18). Las reglas RN-RS-009 y RN-APP-009 quedaron retiradas |
| Casos de uso (12) | 19 casos: se quitaron UC-09 y UC-12 y se agregó UC-21 |

---

## 8. Tecnologías (resumen del documento 14, versión 3)

| Capa | Tecnología |
|---|---|
| Backend | Spring Boot 4.1, Java 21, Maven; Spring Security + OAuth2 Resource Server (JWT: acceso de 15 min, refresh de 7 días rotativo y guardado como hash), BCrypt; JPA + Flyway; springdoc (OpenAPI) |
| Base de datos | PostgreSQL 17 (`btree_gist` con EXCLUDE para evitar reservas traslapadas) |
| Tiempo real | Spring WebSocket + STOMP; la web obtiene un ticket de 60 s a través del BFF |
| Tareas programadas | `@Scheduled` + Outbox (por ejemplo, expiración del pago a los 30 min) |
| Correo | Spring Mail (Resend) + Thymeleaf; en local, **Mailpit** |
| Pagos | Stripe Java SDK, modo prueba, Stripe Checkout, 2 webhooks; Stripe CLI en local |
| PDF | OpenPDF |
| Archivos | Cloudflare R2; en local, **MinIO** |
| Monitoreo | Actuator + Micrometer → Prometheus → Grafana (tablero mínimo) |
| Web | Next.js 15 como BFF (cookies httpOnly; versión 15 o 16 pendiente, P-01), Tailwind, shadcn/ui, TanStack Query, react-day-picker, EventCalendar (Gantt, `resourceTimelineMonth`), @stomp/stompjs |
| App | React Native + Expo SDK 54 + Expo Router, expo-secure-store, expo-notifications (push: Spring → Expo Push → FCM; requiere un development build), EAS Build. La app llama directo a Spring con su propio JWT |
| Local | Docker Compose: PostgreSQL, Mailpit, MinIO, Prometheus y Grafana. El API, la web y la app corren en la computadora de cada integrante |
| Repositorios | **5 obligatorios en `villaserenaguate`:** `villa-serena-docs`, `villa-serena-infra`, `villa-serena-api`, `villa-serena-web`, `villa-serena-movil`. Cada frontend copia `villa-serena-api/openapi.yaml` y genera sus tipos con `openapi-typescript`; sin monorepo, `packages/shared` ni workspaces |
| Después | VPS, Cloudflare Tunnel, CI/CD (GitHub Actions) y backups |

**Notas:** Spring Boot 4 usa los starters `spring-boot-starter-flyway`, `-webmvc` y `-security-oauth2-resource-server`. Next.js 16 renombró `middleware` a `proxy`. Expo Go de la Play Store ya está en el SDK 57; el de SDK 54 se instala desde expo.dev/go.

**Seguridad (AGENTS.md):** nunca subir secretos a Git. Los secretos van solo en un `.env` fuera de Git o en GitHub Secrets. **Nunca pegar claves secretas en un chat de IA.**

---

## 9. Estado de las historias de usuario v3

| Archivo | HU | Nivel 1 | Nivel 2 |
|---|---|---|---|
| Cliente y Huésped (HU-HUE) | 18 | 17 | 1 (amenidades y Wi-Fi) |
| Recepcionista (HU-REC) | 17 | 16 | 1 (crear reserva seleccionando días en el Gantt) |
| Room Service (HU-RS) | 7 | 7 | 0 |
| Mantenimiento y Limpieza (HU-MYL) | 8 | 8 | 0 |
| Administrador (HU-ADM) | 13 | 10 | 3 (amenidades/Wi-Fi, turnos, inventario aislado) |
| Channel Manager (HU-CM) | 3 | 3 | 0 |
| Personal del Hotel (HU-EMP) | 2 | 2 | 0 |
| **Total** | **68** | **63** | **5** |

- **Plantilla:** Plataforma | Épica | Alcance | Nivel | Tamaño | Estado; Historia; 3 a 8 criterios con valores concretos; Depende de; Reglas relacionadas (documento 10); Reemplaza a (v2); Notas técnicas.
- **Reducción:** las HU obligatorias bajaron de 101 a 63 (−38 %); el alcance, de 81 funcionalidades a 58 obligatorias.
- **Tareas técnicas** (no son HU): diseño breve de la integración con canales, permisos por rol, historial de cambios de estado, WebSocket para los 4 eventos, Grafana en local, Docker local, primer Administrador por variables de entorno y datos iniciales (Flyway).

---

## 10. Pendientes abiertos

- [ ] **Confirmar con el ingeniero** si turnos e inventario son obligatorios. Si lo son, ALC-ADM-03 y ALC-ADM-04 pasan a Nivel 1 en su versión mínima.
- [ ] **Confirmar con el catedrático** los supuestos S-01 a S-04 (documento 14, sección 14) y si basta el PDF de 80 mm cuando no haya impresora térmica.
- [x] ~~Versión de Expo~~ → **Resuelto (1 oct):** se queda en SDK 54.
- [ ] **Versión de Next.js (P-01):** 15 (documentado) o 16. Decidir antes de crear el proyecto web.
- [x] **Plan de trabajo:** disponibilidad y movilidad de tareas confirmadas; ver secciones 2 y 7 del documento 13. No volver a pedir esta confirmación.
- [ ] **Martes 6:** decidir los recortes de reserva según el avance; no aplicarlos antes.
- [ ] **Documento 15:** empezar por los prompts del objetivo 0 para permitir el arranque opcional de esta noche.

---

## 11. Próximos pasos: rehacer en cascada

El orden acordado. Cada documento se crea en `Documentación V3`, a partir del Alcance v3 y las HU v3, y **con el mismo criterio de simplicidad**.

| # | Documento | Estado / qué hacer |
|---|---|---|
| 1 | 07 — Estados v3 | ✅ Terminado |
| 2 | 10 — Reglas de Negocio v3 | ✅ Terminado (GPT) y revisado |
| 3 | 02 — Roles · 08 — Inventario, Turnos y Personal · 09 — Matriz de Permisos | ✅ Terminados |
| 4 | 11 — Requisitos Funcionales · 12 — Casos de Uso | ✅ Terminados (el 11 por GPT) y revisados |
| 5 | 14 — Tecnologías y Arquitectura | ✅ Terminado |
| 6 | **13 — Plan de trabajo** | ✅ Actualizado con T-01 a T-10 aprobados, 108,5 h estimadas frente a 102 h nominales, días seguros de máximo 3 h y recortes de reserva pendientes del martes 6 |
| 7 | **15 — Prompts de IA** | **Siguiente: primero los prompts del objetivo 0**, usando los cinco repositorios y el calendario aprobado. El resto se prepara después |

**Cómo trabajar cada documento:**

1. Crearlo en `Documentación V3`, también en el proyecto (`claude/V3/...`).
2. Validarlo contra las HU: que no contradiga ningún criterio y que todos los IDs mencionados existan.
3. Avisar a Joss en un resumen corto.

---

## 12. Mensaje de arranque para el chat nuevo

Copiar y pegar:

```text
Hola. Continúo el proyecto PMS Villa Serena (proyecto final de Desarrollo Web).
Háblame siempre en español.

Primero lee "Documentación V3/00 - Contexto para Nuevo Chat.md" (también está en el
proyecto como claude/V3/00 - Contexto para Nuevo Chat.md). Después lee el Alcance v3,
el índice de historias de usuario v3 y el documento 14 (Tecnologías y Arquitectura).
Si necesitas el detalle de una historia, lee el archivo de ese rol.

Reglas importantes:
- El criterio principal es NO aumentar el trabajo: el hotel es ficticio y el tiempo es
  muy corto. No agregues validaciones, estados ni pantallas; si algo falta, propón
  quitar algo o aceptar la limitación.
- Primero me das un informe y yo apruebo; después modificas.
- Trabaja solo en la carpeta "Documentación V3". No toques "Documentación Definitiva".

Tarea siguiente: documento 15 — Prompts de IA, empezando por el objetivo 0.
Lee el plan 13 ya aprobado: cinco repositorios, máximo 3 h por día seguro,
objetivo 5 recortado y otros tres recortes solo de reserva hasta el martes 6.
El contrato 0 a 2 se congela el viernes 2; 3A a 4, el lunes 5. No vuelvas a
pedir confirmación de horas o frentes. Primero presenta el informe o propuesta
y después aplica lo que apruebe. El arranque de esta noche es opcional.
```
