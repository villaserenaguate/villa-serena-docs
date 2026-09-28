# 03 — Plantilla de Historias de Usuario

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** 02 — Definición de Roles
> **Siguiente documento:** 04 — Historias de Usuario

---

## 1. Archivos y prefijos de ID

| Archivo | Prefijo | Ejemplo |
|---|---|---|
| HU - Administrador | `HU-ADM` | HU-ADM-01 |
| HU - Recepcionista | `HU-REC` | HU-REC-05 |
| HU - Room Service | `HU-RS` | HU-RS-04 |
| HU - Mantenimiento y Limpieza | `HU-MYL` | HU-MYL-07 |
| HU - Cliente y Huésped | `HU-HUE` | HU-HUE-03 |
| HU - Channel Manager | `HU-CM` | HU-CM-02 |

Channel Manager tiene archivo propio porque involucra a varios actores (Administrador, Recepción y el canal externo).

### Reglas de numeración

1. Dos dígitos: 01, 02… 99.
2. **Un ID nunca se reutiliza.** Si una historia se elimina, se marca como `Descartada` y se conserva en el archivo.
3. Dentro de cada archivo, las historias se agrupan por **épica**.

---

## 2. Plantilla

```markdown
### HU-XXX-00 — Título corto en infinitivo

| Plataforma | Épica | Alcance | Prioridad | Tamaño | Estado |
|---|---|---|---|---|---|
| Web privada | Nombre de la épica | ALC-XXX-00 | Alta | M | Pendiente |

**Historia**
- **Como** <rol>
- **Quiero** <acción>
- **Para** <beneficio>

**Criterios de aceptación**
1. Criterio verificable.
2. Criterio verificable.
3. Caso de error.

**Reglas relacionadas:** RN-XXX-000
**Depende de:** HU-XXX-00
**Notas técnicas:** (opcional)
```

El **rol** se indica en el encabezado del archivo y en la línea "Como…" de cada historia.

---

## 3. Significado de cada campo

| Campo | Valores permitidos | Para qué sirve |
|---|---|---|
| Plataforma | `Web pública` / `Web privada` / `App` / `API` | Saber quién la construye (Kim, Carlos o backend) |
| Épica | Nombre del grupo funcional | Organizar el tablero de GitHub |
| Alcance | ID(s) del documento 01 | Trazabilidad: toda historia debe estar dentro del alcance |
| Prioridad | `Alta` / `Media` / `Baja` | Orden de desarrollo |
| Tamaño | `S` (≤ 1 día) / `M` (2–3 días) / `L` (4–5 días) | Planificación. Si es más grande que L, se divide |
| Estado | `Pendiente` / `En desarrollo` / `En revisión` / `Hecha` / `Descartada` | Seguimiento |
| Reglas relacionadas | IDs del documento 10 — Reglas de Negocio | Conectar la historia con las reglas que debe cumplir |
| Depende de | IDs de otras historias | Orden de construcción |

---

## 4. Cómo escribir criterios de aceptación

| Regla | Mal ❌ | Bien ✅ |
|---|---|---|
| Deben ser verificables | "El sistema responde rápido." | "La búsqueda muestra resultados en menos de 2 segundos." |
| Una idea por criterio | "Se valida el correo y se envía la confirmación." | Dos criterios separados. |
| Incluir casos de error | (Solo el caso feliz) | "Si no hay disponibilidad, se muestra un mensaje y no se crea la reserva." |
| Entre 3 y 8 criterios | 15 criterios | Dividir la historia en dos. |

Los criterios de aceptación son la base de las **pruebas automáticas** (CI/CD).

---

## 5. Definición de "Hecho"

Una historia pasa a estado `Hecha` solo si cumple **todo** lo siguiente:

- [ ] Cumple todos sus criterios de aceptación.
- [ ] El código está en un pull request aprobado por al menos 1 compañero.
- [ ] Las pruebas automáticas pasan en GitHub Actions.
- [ ] Está desplegada en el ambiente de pruebas.
- [ ] Los permisos del rol están aplicados en la base de datos.
