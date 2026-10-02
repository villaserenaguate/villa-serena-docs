# OBJ-0B — API: proyecto base

| Dato | Valor |
|---|---|
| Objetivo | 0 — Base (documento 13) |
| Repositorio | `villa-serena-api` |
| Responsable | Hugo |
| Horas estimadas | 2 h |
| Cubre | Proyecto base de Spring; tarea técnica: historial de cambios de estado (ALC-TRA-03) |
| Depende de | Nada. Puede hacerse el jueves 1 por la noche (opcional). **Josué (OBJ-0C) y Pablo (OBJ-0D) parten de este proyecto** |

> **Antes de empezar:** prepara tu computadora con la `16 - Guia de Arranque del Proyecto.md` (instalar, `.env` y encender Docker). Para crear tu rama y subir tu trabajo con un pull request, sigue la sección 3 de `00 - Como usar los prompts.md`.

## Documentos que debes adjuntar a la IA

- `AGENTS.md`
- `14 - Tecnologias y Arquitectura.md` (secciones 2, 4.1, 5, 6 y 7)
- `07 - Estados.md` (sección 10, reglas generales del historial)

## Prompt

```text
Trabajas en el repositorio villa-serena-api del proyecto Villa Serena (lee AGENTS.md
y los documentos adjuntos). Responde en español.

Objetivo: crear el proyecto base del backend, sin lógica de negocio todavía.

Crea:
1. Proyecto Maven con Spring Boot 4.1 y Java 21 (con ./mvnw). Paquete raíz
   com.villaserena.api y los paquetes vacíos de la sección 7 del documento 14
   (config, auth, reservas, estadia, roomservice, piso, personal, catalogos,
   facturacion, canal, notificaciones, comun; sin inventario, que es Nivel 2).
2. Dependencias: spring-boot-starter-webmvc, -data-jpa, -validation, -flyway
   (con flyway-database-postgresql), -actuator, -security-oauth2-resource-server
   (solo la dependencia; la configuración la hace Pablo), driver de PostgreSQL,
   micrometer-registry-prometheus y springdoc-openapi para Spring Boot 4.
   Si una versión no es compatible con Spring Boot 4.1, avísame antes de cambiarla.
3. application.yml que lea todo del entorno (.env): conexión a PostgreSQL,
   puerto 8080, zona horaria de la JVM y de Jackson en UTC, y
   hibernate.jdbc.time_zone=UTC. Zona de negocio America/Guatemala como bean
   ZoneId o constante en comun (AD-19).
4. Actuator: exponer health y prometheus.
5. CORS: permitir solo http://localhost:3000 (variable de entorno), métodos
   GET, POST, PUT, PATCH y DELETE, con credenciales.
6. Manejo de errores global (@RestControllerAdvice) con un formato único:
   { "codigo", "mensaje", "detalles" } y mensajes en español. Casos: validación
   (400), no encontrado (404), prohibido (403), conflicto (409) y error interno
   (500, sin mostrar detalles técnicos).
7. Historial de cambios de estado (comun): entidad y servicio
   HistorialEstadoService.registrar(tipoEntidad, idEntidad, estadoAnterior,
   estadoNuevo, idResponsable, fecha). La tabla historial_estados la crea Josué
   en Flyway; tú solo creas la entidad JPA que la mapea, con estos campos.
8. springdoc: Swagger UI en /swagger-ui.html y JSON en /v3/api-docs.
9. .env.example (sin valores reales), .gitignore con .env y README.md con los
   pasos para arrancar.

Mientras Pablo no termine la seguridad, deja una configuración temporal que
permita /actuator/** y /swagger-ui/** y /v3/api-docs/** sin sesión, marcada con
un comentario TODO para Pablo.

No hagas:
- No crees migraciones de Flyway (solo Josué). Si el arranque necesita una,
  deja Flyway activado y espera la de Josué, o usa
  spring.flyway.enabled=false temporalmente en tu .env local.
- No agregues endpoints de negocio, WebSocket, Stripe ni correo todavía.

Primero muéstrame el plan de archivos; después créalos.
```

## Cómo saber que quedó terminado

1. Con los servicios de OBJ-0A arriba, `./mvnw spring-boot:run` arranca sin errores.
2. http://localhost:8080/actuator/health responde `{"status":"UP"}`.
3. http://localhost:8080/actuator/prometheus muestra métricas y Prometheus marca el API como "UP".
4. http://localhost:8080/swagger-ui.html abre.
5. Una petición a una ruta inexistente responde con el formato de error en español.
6. `git status` no muestra ningún `.env`.

## Al terminar

Cuando tu pull request se fusione, abre `17 - Avance del Proyecto.md` (repositorio `villa-serena-docs`), cambia tu casilla de `[ ]` a `[x]` y agrega el número del PR. Si no sabes cómo, avisa en el grupo y Josué la marca.
