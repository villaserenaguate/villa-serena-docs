# 00 — Cómo usar los prompts (versión 3)

> **Para qué sirve:** guía corta para que cada integrante use su prompt con la IA sin salirse del plan.
> **Basado en:** 13 — Plan de Trabajo y 14 — Tecnologías y Arquitectura.

---

## 1. Antes de empezar (una sola vez)

1. Clona tu repositorio de la organización `villaserenaguate`.
2. Copia `AGENTS.md` en la raíz del repositorio (si no está ya).
3. Crea tu `.env` a partir de `.env.example`. **Nunca subas el `.env` ni pegues sus claves en un chat de IA.**
4. Levanta los servicios locales desde `villa-serena-infra`: `docker compose -f docker-compose.dev.yml up -d`.

## 2. Qué prompt usa cada persona (objetivo 0)

| Prompt | Repositorio | Responsable | Horas | Cuándo |
|---|---|---|---|---|
| Preparar los 5 repositorios (sección 4 de esta guía, no es prompt) | Todos | Alex | 1 | Jue 1 (opcional) o Vie 2 |
| OBJ-0A — Infra: Docker local | `villa-serena-infra` | Josué | 2 | Jue 1 (opcional) o Vie 2 |
| OBJ-0B — API: proyecto base | `villa-serena-api` | Hugo | 2 | Jue 1 (opcional) o Vie 2 |
| OBJ-0C — API: esquema y datos iniciales | `villa-serena-api` | Josué | 5 | Vie 2 y Lun 5 |
| OBJ-0D — API: seguridad y JWT | `villa-serena-api` | Pablo | 4 | Vie 2 y Lun 5 |
| OBJ-0E — Web: proyecto base y BFF | `villa-serena-web` | Alex (y Kim, diseño base) | 3,5 + 1 | Jue 1 (opcional), Vie 2 |
| OBJ-0F — Móvil: proyecto base | `villa-serena-movil` | Carlos | 1 + 1 (push, Vie 2) | Jue 1 (opcional) y Vie 2 |

**Orden en el API:** OBJ-0B (proyecto base) va primero. Josué y Pablo crean su rama a partir de `main` en cuanto el proyecto base esté fusionado; si todavía no lo está, parten de la rama de Hugo.

El **contrato del API** (`openapi.yaml`, objetivos 0 a 2) se congela el viernes 2 y tiene su propio prompt aparte.

## 3. Cómo usar un prompt

1. Abre tu prompt (archivo `OBJ-0x`).
2. Adjunta a la IA **solo** los documentos de la lista "Documentos que debes adjuntar".
3. Copia el bloque del prompt y pégalo en tu herramienta de IA, **dentro de tu repositorio**.
4. Pide que primero muestre el plan. Revísalo: si propone algo que no está en el prompt, dile que lo quite.
5. Deja que avance en pasos pequeños. Prueba cada paso.
6. Al final, comprueba los puntos de "Cómo saber que quedó terminado".
7. Haz un pull request pequeño a `main` y avisa al grupo.

## 4. Preparar los 5 repositorios (Alex, 1 h)

Organización: `villaserenaguate`. Repositorios: `villa-serena-docs`, `villa-serena-infra`, `villa-serena-api`, `villa-serena-web` y `villa-serena-movil`.

En cada uno:

1. `README.md` con una línea de qué contiene y cómo se arranca (comandos del documento 14, sección 8).
2. `.gitignore` adecuado (Java/Maven, Node/Next.js o Expo) que incluya `.env`.
3. `.env.example` vacío o con los nombres de variables que se conozcan.
4. `AGENTS.md` (este paquete).
5. **Proteger `main`:** Settings → Branches → regla para `main` con "Require a pull request before merging" (1 aprobación).
6. Dar acceso de escritura a los 6 integrantes.

## 5. Cómo revisar lo que entrega la IA

| Revisa | Señal de problema |
|---|---|
| ¿Solo hizo lo que pide el prompt? | Pantallas, campos o validaciones nuevas que no están en los documentos |
| ¿Respetó las versiones? | Cambió Spring Boot, Next.js o Expo de versión |
| ¿Hay secretos en el código? | Claves, contraseñas o tokens escritos en archivos que se suben |
| ¿Tocó piezas compartidas? | Creó migraciones (si no eres Josué) o cambió `openapi.yaml` sin avisar |
| ¿Funciona en local? | Los pasos de "Cómo saber que quedó terminado" fallan |

## 6. Si la IA propone algo fuera del alcance

Respóndele: *"No lo agregues. El hotel es ficticio y el alcance está cerrado; implementa solo lo que pide el prompt."* Si crees que de verdad hace falta, avísalo en la reunión diaria; no lo implementes por tu cuenta.

## 7. Si te atrasas

No trabajes más de 3 h en un día seguro. Avisa en la reunión diaria qué quedó pendiente; el martes 6 se decide si se aplica algún recorte de reserva (documento 13, sección 9.2).
