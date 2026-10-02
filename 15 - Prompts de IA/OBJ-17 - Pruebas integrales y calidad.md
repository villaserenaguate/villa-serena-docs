# OBJ-17 — Ejecutar pruebas integrales y asegurar la calidad

| Paquete | Depende de | Rama sugerida | Carga |
|---|---|---|---|
| P3 — Plataforma y calidad | Todos los módulos | `feat/obj-17-pruebas-integrales` | ~25 h |

Puede empezarse antes, agregando pruebas a medida que cada módulo se fusiona.

---

## Prompt 17-A — Pruebas de extremo a extremo, autorización y datos de demostración

````text
Lee AGENTS.md y sigue su protocolo de trabajo.

Tu tarea es el OBJ-17: asegurar que todo el sistema funciona junto antes de la entrega.

Lee: docs/12 - Casos de Uso.md (todos), docs/09 - Matriz de Permisos.md, docs/10 - Reglas de Negocio.md y docs/13 - Objetivos del Proyecto.md (secciones "Terminado cuando" de cada objetivo). Revisa las pruebas existentes en tests/ y supabase/tests/.

1. Inventario de cobertura: tabla en docs/pruebas.md con cada caso de uso (UC-01 a UC-19) y cada grupo de reglas, indicando qué prueba lo cubre y cuáles faltan.

2. Playwright de extremo a extremo (web), completando lo que falte:
   - UC-01 + UC-02 + UC-05: búsqueda, reserva y pago (webhook simulado en CI).
   - UC-03 y UC-04: check-in y check-out en Recepción.
   - UC-06: pedido telefónico de principio a fin; UC-13: solicitud desde Recepción atendida por Limpieza.
   - UC-15: vencimiento de pago (invocando la función directamente).
   - UC-16: reserva y cancelación por el canal simulado.
   - Prueba de concurrencia de la última habitación ejecutada en CI.

3. Autorización: completar pgTAP para que cada fila de la matriz tenga al menos una prueba permitida y una rechazada por rol.

4. Datos de demostración: supabase/seed-demo.sql con un escenario realista (hotel a medio llenar, reservas de todos los canales, pedidos y solicitudes en curso, una habitación fuera de servicio, inventario con alertas) y un script para cargarlo en desarrollo.

5. Registro de errores: plantilla de issue "Error" en GitHub y lista de errores encontrados en docs/pruebas.md con su estado.

6. Verifica que ci.yml ejecuta todas las pruebas y que el tiempo total es razonable (menos de ~15 minutos).

Criterios de terminado:
- Todas las pruebas pasan en CI.
- No quedan errores críticos abiertos.
- docs/pruebas.md muestra la cobertura por caso de uso.
````

## Revisión humana

- [ ] Una persona que no programó el módulo ejecuta cada caso de uso manualmente.
