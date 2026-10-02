# 16 — Guía de arranque del proyecto

> **Para qué sirve:** cómo preparar tu computadora la primera vez, cómo encender el proyecto cada día, cómo retomar el trabajo y qué hacer cuando algo falla.
> **Para quién:** los 6 integrantes. No hace falta experiencia previa con estas tecnologías.
> **Comandos:** escritos para la terminal de Windows (`cmd`). En PowerShell funcionan igual, salvo donde se indica.
> **Relacionado:** `15 - Prompts de IA/00 - Como usar los prompts.md` (cómo trabajar con la IA) y documento 14, sección 8.

---

## 1. Qué instalar (una sola vez)

| Programa | Quién lo necesita | Cómo comprobar que funciona |
|---|---|---|
| **Git** | Todos | `git --version` |
| **Docker Desktop** (con WSL 2 en Windows) | Todos | `docker --version` y `docker ps` sin error |
| **Visual Studio Code** u otro editor | Todos | — |
| Tu **herramienta de IA** (Claude Code, Codex, Copilot o Cursor) | Todos | — |
| **JDK 21** (por ejemplo, Eclipse Temurin 21) | Josué, Hugo y Pablo (API) | `java -version` muestra 21 |
| **Node.js 22 LTS** y **pnpm** (`npm install -g pnpm`) | Alex, Kim (web) y Carlos (app) | `node -v` y `pnpm -v` |
| **Expo Go de SDK 54** en un teléfono Android. Se descarga desde **expo.dev/go**, **no** desde la Play Store | Carlos | La app abre |
| Cuenta de **Expo** y **EAS CLI** (`npm install -g eas-cli`) | Carlos | `eas --version` |
| **DBeaver** (opcional), para ver la base de datos | Quien quiera | — |

No hace falta instalar Maven: el API trae su propio Maven (`mvnw`).

**Cuentas:** no hay que crear cuentas para PostgreSQL, Mailpit, MinIO ni Grafana; sus usuarios y contraseñas los inventas tú en el `.env`. Solo harán falta cuentas de **Stripe** en modo prueba (para los pagos, más adelante) y de **Expo** (Carlos).

---

## 2. Primera vez: preparar el proyecto

### 2.1 Clonar los repositorios

Crea una carpeta de trabajo (por ejemplo, `D:\Proyectos`), **fuera** de la carpeta de documentación, y clona lo que necesites:

```bat
cd /d "D:\Proyectos"
git clone https://github.com/villaserenaguate/villa-serena-infra.git
git clone https://github.com/villaserenaguate/<tu-repositorio>.git
```

| Quién | Repositorios |
|---|---|
| Todos | `villa-serena-infra` (los servicios de Docker) |
| Josué, Hugo y Pablo | `villa-serena-api` |
| Alex y Kim | `villa-serena-web` (y `villa-serena-api` para probar de punta a punta) |
| Carlos | `villa-serena-movil` (y `villa-serena-api` para probar de punta a punta) |
| Quien edite documentación | `villa-serena-docs` |

### 2.2 Crear tu `.env`

**Qué es:** un archivo de texto con la **configuración privada de tu computadora** (contraseñas, claves y direcciones). Cada línea tiene un nombre y un valor, por ejemplo `POSTGRES_PASSWORD=VillaSerena2026`. Docker, Spring, Next.js y Expo lo leen al arrancar, así las contraseñas no se escriben en el código. Está en `.gitignore`: **nunca se sube a GitHub**. Lo que sí se sube es su plantilla, `.env.example`.

**Cómo crearlo** (en cada repositorio que tenga `.env.example`):

1. Dentro de la carpeta del repositorio:
   ```bat
   copy .env.example .env
   ```
   (En PowerShell o Git Bash: `cp .env.example .env`).
2. Abre `.env` con VS Code o el Bloc de notas y reemplaza cada `<TU_...>` por tu valor. **Los símbolos `<` y `>` no van:** se reemplaza todo.
   ```env
   # Antes
   MINIO_ROOT_PASSWORD=<TU_CONTRASENA_MINIO>
   # Después
   MINIO_ROOT_PASSWORD=VillaSerena2026
   ```
3. Reglas para los valores:
   - Sin espacios alrededor del `=` y sin comillas.
   - Solo letras, números y, si quieres, `!`, `-` o `_`. **Evita `#`, `$` y `"`**.
   - Las contraseñas de MinIO deben tener **al menos 8 caracteres**.
   - Como todo es local y de prueba, las contraseñas **las inventas tú**.
4. Los valores que deben ser **iguales para todos** (la contraseña de PostgreSQL que también usa el API, y los hashes de los usuarios de prueba y de las claves de los canales) se comparten **por mensaje privado** entre el equipo.
5. **Nunca subas el `.env` ni pegues su contenido en un chat de IA.** Si la IA necesita saber qué variables hay, muéstrale el `.env.example`.

### 2.3 Encender los servicios por primera vez

Con Docker Desktop abierto, dentro de `villa-serena-infra`:

```bat
docker compose -f docker-compose.dev.yml up -d
```

La primera vez **descarga las imágenes** (puede tardar varios minutos y necesita buena conexión a Internet). Después, comprueba que funcionan las direcciones de la sección 3.

---

## 3. Qué servicios corren en Docker

| Servicio | Para qué sirve | Dirección en tu computadora |
|---|---|---|
| **PostgreSQL** | La base de datos | Puerto 5432 (la usa el API) |
| **Mailpit** | Atrapa los correos de prueba (códigos, confirmaciones y facturas) para verlos sin enviarlos de verdad | http://localhost:8025 |
| **MinIO** | Guarda archivos (fotos y PDF de facturas). Funciona igual que Cloudflare R2, que se usará después en el servidor | Consola: http://localhost:9001 |
| **Prometheus** | Recoge las métricas del API | http://localhost:9090 |
| **Grafana** | Muestra el tablero del API | http://localhost:3001 |

**Imagen de MinIO:** las imágenes oficiales de MinIO ya no están en Docker Hub. Se usa el fork `pgsty/minio`, compatible con S3 (aprobado por el equipo).

---

## 4. Cada día: encender el proyecto

1. Abre **Docker Desktop** y espera a que diga "Running".
2. Enciende los servicios, dentro de `villa-serena-infra`:
   ```bat
   docker compose -f docker-compose.dev.yml up -d
   docker compose -f docker-compose.dev.yml ps -a
   ```
   Todos deben decir `running`, menos `minio-init`, que dice `exited (0)`. Es normal: solo crea los buckets y termina. Tus datos se conservan de un día a otro.
3. Enciende lo que vayas a usar, cada uno en **su propia ventana de terminal**:

| Parte | Carpeta | Comando | Dirección |
|---|---|---|---|
| API (Spring) | `villa-serena-api` | `mvnw.cmd spring-boot:run` (en PowerShell: `./mvnw spring-boot:run`) | http://localhost:8080/swagger-ui.html |
| Web (Next.js) | `villa-serena-web` | `pnpm dev` | http://localhost:3000 |
| App (Expo) | `villa-serena-movil` | `npx expo start` y escanear el código QR con Expo Go | En el teléfono |
| Stripe (más adelante) | Cualquiera | `stripe listen --forward-to localhost:8080/<ruta-del-webhook>` | — |

El API debe estar encendido para que funcionen la web y la app. La app necesita que el teléfono y la computadora estén en la **misma red Wi-Fi**.

---

## 5. Retomar el trabajo (continuar donde lo dejaste)

**Antes de empezar una tarea nueva**, trae lo último que subió el equipo:

```bat
git switch main
git pull
git switch -c obj<N>-<nombre-corto>
```

**Si vas a continuar una tarea que dejaste a medias** (tu rama ya existe):

```bat
git switch obj<N>-<nombre-corto>
git pull
```

**Si alguien cambió las migraciones o el contrato** (`openapi.yaml`), después de `git pull` vuelve a arrancar el API para que Flyway aplique los cambios, y en la web y la app vuelve a generar los tipos.

Cómo subir tu trabajo y abrir el pull request: `15 - Prompts de IA/00 - Como usar los prompts.md`, sección 3.

---

## 6. Al terminar: apagar

1. Detén el API, la web y la app con `Ctrl + C` en sus terminales.
2. Apaga los servicios (opcional), dentro de `villa-serena-infra`:
   ```bat
   docker compose -f docker-compose.dev.yml down
   ```
   Esto **no borra** los datos. Cerrar Docker Desktop también los apaga.
3. **Solo si quieres empezar de cero** (borra la base de datos y los archivos de MinIO):
   ```bat
   docker compose -f docker-compose.dev.yml down -v
   ```

---

## 7. Errores comunes y qué hacer

| Mensaje o situación | Qué significa | Qué hacer |
|---|---|---|
| `fatal: not a git repository` | Estás en otra carpeta | Entra a la carpeta del repositorio con `cd /d "<ruta>"` |
| `Deletion of directory '.git/...' failed. Should I try again? (y/n)` | Windows no deja borrar una carpeta interna vacía porque un programa la tiene abierta | Escribe `n`. No afecta al repositorio |
| `cannot lock ref ...` al hacer `git fetch` | Una referencia local quedó a medio actualizar | Repite `git fetch`. Si sigue: `git update-ref -d refs/remotes/origin/main` y `git fetch` |
| `Cannot connect to the Docker daemon` o `docker` no responde | Docker Desktop está cerrado | Ábrelo y espera a que diga "Running" |
| `short read ... unexpected EOF` o `no such host` al descargar imágenes | Se cortó Internet o falla el DNS mientras Docker descargaba | Revisa tu Wi-Fi; si usas VPN o proxy, apágalo (o configúralo en Docker Desktop → Settings → Resources → Proxies). Luego repite `docker compose ... up -d`; Docker continúa donde se quedó |
| `port is already allocated` | Otro programa usa ese puerto | Cierra ese programa o cambia el puerto en tu `.env` |
| `'.' no se reconoce como un comando` al usar `./mvnw` | En `cmd` se escribe distinto | Usa `mvnw.cmd spring-boot:run` |
| La app en el teléfono no llega al API | Teléfono y computadora en redes distintas, o se usó `localhost` | Misma red Wi-Fi y la IP local de la computadora en `EXPO_PUBLIC_API_URL` |
| El pull request dice que tiene conflictos | Otra persona cambió lo mismo | No lo resuelvas a ciegas: avisa a Josué |
| La IA quiere actualizar versiones o agregar funciones | Se sale del alcance | Recházalo (frases de la guía de prompts, sección 5) |
