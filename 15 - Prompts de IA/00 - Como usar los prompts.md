# 15 — Prompts de IA: cómo usarlos

> **Proyecto:** PMS Villa Serena
> **Fecha:** 25 de septiembre de 2026
> **Sirve para:** Claude Code, Antigravity y Codex (los prompts son los mismos para las tres)

---

## 1. Qué hay en esta carpeta

| Archivo | Qué es |
|---|---|
| `AGENTS.md` | Contexto permanente del proyecto. Va en la **raíz del repositorio** |
| `CLAUDE.md` | Una línea (`@AGENTS.md`) para que Claude Code lea el mismo contexto. Va en la raíz |
| `OBJ-01 … OBJ-19` | Un archivo por objetivo, con uno o varios prompts listos para copiar |

Cada archivo de objetivo tiene:

1. **Datos del objetivo:** paquete, dependencias y rama sugerida.
2. **Antes de empezar:** lo que una persona debe tener listo (cuentas, objetivos previos).
3. **Prompts:** bloques para copiar y pegar **completos**, uno por sesión. Los objetivos grandes tienen varios prompts (A, B, C…) que se ejecutan **en orden**.
4. **Revisión humana:** qué verificar antes de aprobar el pull request.

---

## 2. Preparación (una sola vez, día 1)

1. Ejecutar **OBJ-04, prompt A**: convierte el repositorio en monorepo, copia la documentación a `docs/` y coloca `AGENTS.md` y `CLAUDE.md` en la raíz.
2. En paralelo, quien tenga P3 empieza los **pasos manuales de OBJ-03** (crear cuentas, comprar el dominio).
3. Después de eso, cada persona puede empezar su primer objetivo.

**Orden recomendado de inicio:** OBJ-04 (A) → OBJ-01 → OBJ-02 → OBJ-06 → resto según dependencias (documento 13, sección 9).

---

## 3. Cómo ejecutar un prompt

1. **Actualiza tu copia:** `git checkout develop && git pull`.
2. **Crea la rama** indicada en el archivo del objetivo.
3. **Abre la raíz del repositorio** en tu herramienta de IA:
   - **Claude Code:** ejecuta `claude` en la terminal, dentro de la carpeta del repositorio.
   - **Codex:** abre la carpeta del repositorio (CLI, IDE o app de Codex).
   - **Antigravity:** abre la carpeta del repositorio como espacio de trabajo.
4. **Pega el prompt completo** (todo lo que está dentro del bloque).
5. **Revisa el plan** que te propone la IA antes de dejarla programar. Si algo no coincide con la documentación, corrígela.
6. **Revisa los cambios** (diff) y las pruebas antes de hacer commit.
7. **Abre un pull request** hacia `develop` con los IDs de HU y la checklist de "Definición de Hecho".

**Un prompt = una sesión nueva.** No encadenes varios prompts en la misma conversación: la IA pierde precisión cuando el contexto crece demasiado.

---

## 4. Consejos por herramienta (opcionales)

| Herramienta | Consejo |
|---|---|
| Claude Code | Pide que planifique primero (modo plan) en los prompts grandes. Usa `/clear` entre prompts |
| Codex | Pide explícitamente "muestra el plan antes de editar" si trabajas en modo automático |
| Antigravity | Aprovecha su navegador para que pruebe las pantallas web al final de cada prompt de interfaz |

**Útil para cualquier herramienta:** conectar el **MCP de Supabase** (en modo solo lectura sobre el proyecto de desarrollo) permite a la IA consultar el esquema real.

---

## 5. Reglas para el equipo

- **Nunca pegues claves secretas en el chat de la IA.** Las claves van en `.env.local`, que no se sube a Git.
- **La IA propone, ustedes aprueban.** Todo pasa por pull request con revisión de al menos un compañero.
- **Si la IA quiere cambiar una regla de negocio o un estado, detente.** Esos cambios se discuten con el equipo y se actualizan primero en `docs/`.
- **Frontend de Kim:** si ya existen pantallas, agrega al inicio del prompt: *"Ya existen pantallas en `<ruta>`. Reutilízalas y conéctalas; no las rehagas desde cero."*

---

## 6. Índice de prompts

| Objetivo | Paquete | Prompts |
|---|---|---|
| OBJ-01 Base de datos | P1 | A (modelo y migraciones), B (seed y pruebas) |
| OBJ-02 Autenticación y permisos | P1 | A (auth y perfiles), B (RLS y storage) |
| OBJ-03 Infraestructura | P3 | Pasos manuales + A |
| OBJ-04 Repositorio y CI/CD | P3 | A (monorepo), B (pipelines), C (tablero de HU) |
| OBJ-05 Base del frontend web | P4 | A |
| OBJ-06 Backend de reservas | P2 | A (disponibilidad, precio, crear), B (modificar, cancelar, cuenta), C (check-in/out y transiciones) |
| OBJ-07 Pagos, correos y procesos | P2 | A (Stripe), B (correos y PDF), C (pg_cron y alertas) |
| OBJ-08 Web pública | P5 | A (explorar), B (reservar y pagar), C (mi reserva) |
| OBJ-09 Recepción | P4 | A (reservas), B (día y Gantt), C (habitaciones y estadía), D (cuenta y solicitudes) |
| OBJ-10 Admin: catálogos y tarifas | P1 | A (catálogos y configuración), B (tarifas) |
| OBJ-11 Admin: personal, inventario, supervisión | P3 | A (personal y turnos), B (inventario), C (mantenimiento e indicadores) |
| OBJ-12 Room Service | P5 | A |
| OBJ-13 Mantenimiento y Limpieza | P6 | A (limpieza y solicitudes), B (reportes y mantenimiento) |
| OBJ-14 App: base y estadía | P6 | A (proyecto y acceso), B (estadía, cuenta y APK) |
| OBJ-15 App: servicios y check-out | P6 | A (room service y solicitudes), B (pago y check-out) |
| OBJ-16 Channel Manager | P2 | A (diseño y API), B (canal simulado) |
| OBJ-17 Pruebas integrales | P3 | A |
| OBJ-18 Backups | P1 | A |
| OBJ-19 Documentación y demo | Todos | A |
