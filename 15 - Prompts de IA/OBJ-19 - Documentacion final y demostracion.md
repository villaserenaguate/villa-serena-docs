# OBJ-19 — Elaborar la documentación final y la demostración

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| Todos | Todos | `docs/obj-19-documentacion-final` | ~15 h (compartido) |

---

## Prompt 19-A — README, manuales, API y guion de demostración

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-19: dejar el proyecto listo para entregar y presentar.

Lee: todo docs/ y revisa el código de apps/web, apps/mobile y supabase para que la documentación refleje lo que realmente existe.

1. README.md raíz actualizado: descripción, capturas, arquitectura (enlace a docs/infraestructura.md), requisitos, instalación paso a paso desde cero (clonar, pnpm install, supabase start, variables, seed, ejecutar web y app), comandos, enlaces a producción, al APK y a la documentación del API.

2. Manuales de uso por rol en docs/manuales/: cliente/huésped (web y app), recepcionista, room service, mantenimiento/limpieza y administrador. Paso a paso con capturas de pantalla (usa marcadores [CAPTURA: …] donde haga falta una imagen que tomará el equipo).

3. Documentación técnica: verifica que docs/backend-funciones.md, docs/er.md, docs/permisos-implementacion.md, docs/openapi/canal-v1.yaml, docs/cicd.md, docs/backups.md y docs/pruebas.md estén completos y coincidan con el código. Lista las diferencias que encuentres entre la documentación definitiva (01 a 14) y lo construido; no cambies los documentos definitivos sin confirmación del equipo.

4. Estado final del alcance: tabla en docs/estado-final.md con las 96 HU y su estado (Hecha / Parcial / No iniciada), y la lista de Fase 2 como trabajo futuro.

5. Guion de demostración docs/demo.md (15–20 minutos): orden de pantallas, usuarios a usar, datos precargados (seed-demo), y qué decir en cada parte: web pública y pago, Recepción y Gantt, app del huésped con room service y check-out, piso (limpieza e incidencia), Admin (tarifas e indicadores), canal simulado, CI/CD y seguridad (RLS). Incluye un plan B si falla la conexión.

6. Esquema de la presentación (diapositivas) en docs/presentacion.md: problema, objetivos, arquitectura, tecnologías, demostración, pruebas, lecciones aprendidas y trabajo futuro.

Criterios de terminado:
- Una persona externa puede instalar y usar el sistema siguiendo el README.
- La demostración se ensayó al menos una vez con el guion.
````

## Revisión humana

- [ ] Cada integrante revisa el manual de su módulo.
- [ ] Ensayo completo de la demostración con cronómetro.
