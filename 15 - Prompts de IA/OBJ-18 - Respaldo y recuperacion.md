# OBJ-18 — Implementar respaldo y recuperación de datos

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P1 — Datos y seguridad | OBJ-01, OBJ-03 (y los workflows de OBJ-04 B) | `feat/obj-18-backups` | ~6 h |

---

## Prompt 18-A — Backups automáticos y restauración probada

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-18: backups y recuperación. Recuerda: el plan gratuito de Supabase NO tiene backups automáticos (docs/14 - Tecnologias y Arquitectura.md, sección 10).

Lee: docs/14 - Tecnologias y Arquitectura.md (RNF-DAT-002 y 003) y docs/cicd.md.

1. Workflow de GitHub Actions (agrega un trabajo a programados.yml o crea backup.yml), diario y con ejecución manual:
   - pg_dump de producción (esquema + datos, incluido auth si es posible con las herramientas de Supabase CLI; documenta qué se incluye y qué no, por ejemplo los archivos de Storage).
   - Comprimir y cifrar el archivo (gpg con una clave guardada en GitHub Secrets).
   - Guardarlo como artefacto con retención de 7 días (o en un release privado), sin exponer datos en los logs.

2. Script de restauración (scripts/restaurar-backup.sh o .ts) que descifra y restaura en una base de datos local o en el proyecto de desarrollo, con confirmación explícita antes de sobrescribir.

3. Prueba de restauración real: restaurar el último backup en local, verificar conteos de tablas principales y que la app funciona. Registrar fecha, duración y resultado.

4. docs/backups.md:
   - Política: frecuencia diaria, retención 7 días, RPO ≤ 24 h, RTO ≤ 2 h.
   - Paso a paso para recuperar el sistema.
   - Registro de pruebas de restauración (tabla con fecha y resultado).

Criterios de terminado:
- El backup diario corre en Actions.
- Se restauró con éxito al menos una vez y quedó documentado.
````

## Revisión humana

- [ ] Verificar que la clave de cifrado está guardada fuera de GitHub también (si se pierde, los backups no sirven).
