# 00 — Cómo usar los prompts (versión 3)

> **Para qué sirve:** guía paso a paso para que cada integrante use su prompt con la IA, aunque no tenga experiencia con estas tecnologías.
> **Basado en:** 13 — Plan de Trabajo y 14 — Tecnologías y Arquitectura.
> **Comandos:** escritos para la terminal de Windows (`cmd`). En PowerShell funcionan igual, salvo donde se indica.

---

## 1. Qué instalar antes de empezar

| Programa | Quién lo necesita | Cómo comprobar que funciona |
|---|---|---|
| **Git** | Todos | `git --version` |
| **Docker Desktop** (con WSL 2 en Windows). Debe estar **abierto** para trabajar | Todos (para levantar la base de datos y los servicios) | `docker --version` y `docker ps` sin error |
| **Visual Studio Code** u otro editor | Todos | — |
| Tu **herramienta de IA** (Claude Code, Codex, Copilot o Cursor) | Todos | — |
| **JDK 21** (por ejemplo, Eclipse Temurin 21) | Josué, Hugo y Pablo (API) | `java -version` muestra 21 |
| **Node.js 22 LTS** y **pnpm** (`npm install -g pnpm`) | Alex, Kim (web) y Carlos (app) | `node -v` y `pnpm -v` |
| **Expo Go de SDK 54** en un teléfono Android. Se descarga desde **expo.dev/go**, **no** desde la Play Store | Carlos | La app abre |
| Cuenta de **Expo** y **EAS CLI** (`npm install -g eas-cli`) | Carlos (viernes 2) | `eas --version` |
| **DBeaver** (opcional), para ver la base de datos | Quien quiera | — |

No necesitas instalar Maven: el proyecto del API trae su propio Maven (`mvnw`).

## 2. Qué prompt usa cada persona (objetivo 0)

| Prompt | Repositorio | Responsable | Horas | Cuándo |
|---|---|---|---|---|
| Preparar los 5 repositorios (sección 6 de esta guía, no es prompt) | Todos | Alex | 1 | Jue 1 (opcional) o Vie 2 |
| OBJ-0A — Infra: Docker local | `villa-serena-infra` | Josué | 2 | Jue 1 (opcional) o Vie 2 |
| OBJ-0B — API: proyecto base | `villa-serena-api` | Hugo | 2 | Jue 1 (opcional) o Vie 2 |
| OBJ-0C — API: esquema y datos iniciales | `villa-serena-api` | Josué | 5 | Vie 2 y Lun 5 |
| OBJ-0D — API: seguridad y JWT | `villa-serena-api` | Pablo | 4 | Vie 2 y Lun 5 |
| OBJ-0E — Web: proyecto base y BFF | `villa-serena-web` | Alex (y Kim, diseño base) | 3,5 + 1 | Jue 1 (opcional), Vie 2 |
| OBJ-0F — Móvil: proyecto base | `villa-serena-movil` | Carlos | 1 + 1 (push, Vie 2) | Jue 1 (opcional) y Vie 2 |

**Orden en el API:** OBJ-0B (proyecto base de Hugo) va primero. Josué y Pablo empiezan cuando Hugo lo haya fusionado en `main` (sección 3, paso 1). Si todavía no está, parten de la rama de Hugo: `git switch obj0-proyecto-base` y desde ahí crean la suya.

**Todos necesitan el Docker de Josué (OBJ-0A)** para probar: cuando esté en `main`, cada uno clona `villa-serena-infra` y lo levanta en su computadora (sección 4).

El **contrato del API** (`openapi.yaml`, objetivos 0 a 2) se congela el viernes 2 y tiene su propio prompt aparte.

## 3. Paso a paso con Git (cada tarea)

Cada quien trabaja en **su computadora**, con su copia del repositorio y **su propia rama**. Nadie trabaja directo en `main`.

**1. La primera vez: clonar el repositorio** en una carpeta de trabajo (no dentro de `Documentación V3`):

```bat
cd /d "D:\Proyectos"
git clone https://github.com/villaserenaguate/<repositorio>.git
cd <repositorio>
```

**2. Antes de cada tarea: actualizar y crear tu rama**

```bat
git switch main
git pull
git switch -c obj0-<nombre-corto>
```

Ejemplos de nombres: `obj0-docker-local`, `obj0-proyecto-base`, `obj0-esquema`, `obj0-seguridad-jwt`, `obj0-web-bff`, `obj0-app-base`.

**3. Trabajar con la IA** (sección 5).

**4. Revisar qué vas a subir**

```bat
git status
```

En la lista **no debe aparecer `.env`** ni ningún archivo con claves. Si aparece, detente y avisa.

**5. Guardar y subir**

```bat
git add -A
git commit -m "obj0: <qué hiciste, en pocas palabras>"
git push -u origin obj0-<nombre-corto>
```

**6. Abrir el pull request en GitHub**

1. Entra al repositorio. Si ves el aviso amarillo **Compare & pull request**, púlsalo.
2. Si no aparece: pestaña **Pull requests** → **New pull request**. Arriba hay dos menús: `base: main ← compare: ...`. En **compare** elige tu rama.
3. Escribe qué hiciste y cómo lo probaste. Pulsa **Create pull request** y avisa al grupo.
4. Otro integrante lo revisa y pulsa **Squash and merge** → **Confirm**.

**7. Después del merge**

```bat
git switch main
git pull
git branch -D obj0-<nombre-corto>
```

## 4. El archivo `.env` (secretos)

1. En cada repositorio hay un `.env.example` con los nombres de las variables. Cópialo:
   ```bat
   copy .env.example .env
   ```
2. Abre `.env` y pon tus valores.
3. Los valores que deben ser **iguales para todos** (por ejemplo, los hashes de los usuarios de prueba y de las claves de los canales) se comparten **por mensaje privado** entre el equipo.
4. **Nunca subas el `.env` a Git ni pegues sus claves en un chat de IA.**

**Levantar los servicios** (con Docker Desktop abierto), desde la carpeta de `villa-serena-infra`:

```bat
docker compose -f docker-compose.dev.yml up -d
docker compose -f docker-compose.dev.yml ps
```

Para apagarlos: `docker compose -f docker-compose.dev.yml down`.

## 5. Cómo usar un prompt con la IA

1. Asegúrate de que `AGENTS.md` y `CLAUDE.md` están en la raíz de tu repositorio (sección 6).
2. Abre tu prompt (archivo `OBJ-0x`) y adjunta a la IA **solo** los documentos de "Documentos que debes adjuntar". Están en el repositorio `villa-serena-docs` o en la carpeta `Documentación V3`.
3. Abre la herramienta de IA **dentro de tu repositorio** y pega el bloque del prompt.
4. La IA primero debe mostrar un plan. **Léelo antes de aceptar.** Si propone algo que no está en el prompt, dile que lo quite.
5. Deja que avance en pasos pequeños. Si un comando falla, pega el error completo a la IA y pídele que lo explique antes de cambiar nada.
6. Al final, comprueba cada punto de "Cómo saber que quedó terminado". Si uno falla, la tarea no está terminada.
7. Sube tus cambios (sección 3, pasos 4 a 6).

**Frases útiles para la IA:**

- *"Antes de escribir código, muéstrame el plan."*
- *"Explícame en palabras simples qué hace este archivo."*
- *"No cambies la versión de Spring Boot, Next.js ni Expo."*
- *"No lo agregues: el alcance está cerrado. Implementa solo lo que pide el prompt."*
- *"Dame el comando exacto para Windows (cmd) para probarlo."*

## 6. Preparar los 5 repositorios (Alex, 1 h)

Organización: `villaserenaguate`. Repositorios: `villa-serena-docs`, `villa-serena-infra`, `villa-serena-api`, `villa-serena-web` y `villa-serena-movil`. (`villa-serena-docs` ya tiene la documentación V3.)

En cada uno de los otros cuatro:

1. `README.md` con una línea de qué contiene y cómo se arranca (documento 14, sección 8).
2. `.gitignore` adecuado (Java/Maven, Node/Next.js o Expo) que incluya `.env`.
3. `.env.example` vacío (cada responsable lo completa).
4. `AGENTS.md` y `CLAUDE.md`, copiados de `15 - Prompts de IA`.
5. **Proteger `main`:** Settings → Branches → regla para `main` con "Require a pull request before merging" (1 aprobación).
6. Dar acceso de escritura a los 6 integrantes.

## 7. Cómo revisar lo que entrega la IA

| Revisa | Señal de problema |
|---|---|
| ¿Solo hizo lo que pide el prompt? | Pantallas, campos o validaciones nuevas que no están en los documentos |
| ¿Respetó las versiones? | Cambió Spring Boot, Next.js o Expo de versión |
| ¿Hay secretos en el código? | Claves, contraseñas o tokens escritos en archivos que se suben |
| ¿Tocó piezas compartidas? | Creó migraciones (si no eres Josué) o cambió `openapi.yaml` sin avisar |
| ¿Funciona en local? | Los pasos de "Cómo saber que quedó terminado" fallan |

## 8. Errores comunes y qué hacer

| Mensaje o situación | Qué significa | Qué hacer |
|---|---|---|
| `fatal: not a git repository` | Estás en otra carpeta | Entra a la carpeta del repositorio con `cd /d "<ruta>"` |
| `Deletion of directory '.git/...' failed. Should I try again? (y/n)` | Windows no deja borrar una carpeta interna vacía porque un programa la tiene abierta | Escribe `n`. No afecta al repositorio |
| `cannot lock ref ...` al hacer `git fetch` | Una referencia local quedó a medio actualizar | Repite `git fetch`. Si sigue: `git update-ref -d refs/remotes/origin/main` y `git fetch` |
| `Cannot connect to the Docker daemon` o `docker` no responde | Docker Desktop está cerrado | Ábrelo y espera a que diga "Running" |
| `port is already allocated` | Otro programa usa ese puerto | Cierra ese programa o cambia el puerto en tu `.env` |
| `'.' no se reconoce como un comando` al usar `./mvnw` | En `cmd` se escribe distinto | Usa `mvnw.cmd spring-boot:run` (en PowerShell sí funciona `./mvnw`) |
| La app en el teléfono no llega al API | Teléfono y computadora en redes distintas, o se usó `localhost` | Misma red Wi-Fi y la IP local de la computadora en `EXPO_PUBLIC_API_URL` |
| El pull request dice que tiene conflictos | Otra persona cambió lo mismo | No lo resuelvas a ciegas: avisa a Josué |
| La IA quiere actualizar versiones o agregar funciones | Se sale del alcance | Recházalo (frases de la sección 5) |

## 9. Si te atrasas

No trabajes más de 3 h en un día seguro. Avisa en la reunión diaria qué quedó pendiente; el martes 6 se decide si se aplica algún recorte de reserva (documento 13, sección 9.2).
