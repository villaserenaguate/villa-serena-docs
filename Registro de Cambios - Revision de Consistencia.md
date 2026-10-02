# Registro de Cambios — Revisión de Consistencia

> **Proyecto:** PMS Villa Serena
> **Fecha:** 24 de septiembre de 2026
> **Alcance de la revisión:** documentos 01 a 12 y las historias de usuario
> **Resultado:** 27 hallazgos (7 altos, 10 medios, 10 bajos), todos aplicados con aprobación del equipo

---

## 1. Verificación automática (después de los cambios)

| Verificación | Resultado |
|---|---|
| Historias de usuario definidas | 96 (antes 95; se agregó HU-ADM-20) |
| Reglas de negocio | 142 RN + 7 R-ROL + 7 RG-EST = 156 |
| Parámetros del sistema | 21 (antes 20; se agregó PAR-21) |
| Requisitos funcionales | 108 (101 V1 + 7 Fase 2) |
| Referencias rotas (HU, RN, ALC, PAR) | 0 |
| Historias sin requisito funcional | 0 |
| Historias con menos de 3 o más de 8 criterios | 0 |

---

## 2. Cambios aplicados

### Severidad alta

| # | Hallazgo | Solución aplicada | Archivos |
|---|---|---|---|
| 1 | Saldo a favor (negativo) sin definir | Nueva RN-PAG-016: se reembolsa antes del check-out; el check-out exige saldo exactamente 0 | 07, 10, 11, 12, HU-REC-06, HU-REC-16 |
| 2 | Precio de las reservas de canal sin definir | Nueva RN-TAR-010: el cargo es el monto enviado por el canal | 10, 11, 12, HU-CM-02 |
| 3 | Pago del canal ausente en los estados | Nueva transición P6 en el documento 07; criterio en HU-CM-02 | 07, HU-CM-02 |
| 4 | Zona horaria sin definir | Nuevo PAR-21: America/Guatemala (UTC-6); la BD guarda UTC | 10 |
| 5 | "Descuentos" sin mecanismo | Nueva RN-PAG-018: no hay descuentos en la versión 1; se quitó de HU-REC-19 y RF-PAG-002 | 10, 11, HU-REC-19 |
| 6 | Check-out con habitación `EN_LIMPIEZA` o `SUCIA` | C1 ahora parte de cualquier condición; nueva RN-HAB-008 | 07, 10, 12 |
| 7 | Limpieza a pedido de un huésped alojado | Nueva RN-LIM-013: se gestiona como solicitud y no cambia la condición | 07, 10 |

### Severidad media

| # | Hallazgo | Solución aplicada | Archivos |
|---|---|---|---|
| 8 | Modificar reservas `PENDIENTE_PAGO` | Solo se modifican reservas `CONFIRMADA` o `EN_ESTADIA`; se agregó "tipo" a HU-REC-06 | 07, 12, HU-REC-06 |
| 9 | Ventana del check-out desde la app | HU-HUE-20 alineada con RN-APP-008 (00:00 a 12:00 del día de salida) | HU-HUE-20 |
| 10 | Visibilidad de incidencias para MYL | Nota ⁷ ampliada: incidencias abiertas de la habitación consultada, en solo lectura | 09, HU-MYL-04 |
| 11 | Filtro de turno en HU-RS-01 | HU-RS-01 pasa a "Ver pedidos activos"; el filtro por turno queda en HU-RS-09 | 08, HU-RS-01 |
| 12 | "Canal simulado" registrado como canal | Solo se registran Booking y Expedia; el simulador usa sus claves | HU-CM-01 |
| 13 | Plataformas de cancelación e historial | Documento 02 alineado: cancelación solo en web; historial solo en app | 02 |
| 14 | Reasignar solicitudes sin historia | Nueva HU-ADM-20 y RF-ADM-008 | HU-ADM, 08, 09, 11, 12, índice |
| 15 | Empleado desactivado con limpieza en curso | RN-PER-003 ampliada con habitaciones `EN_LIMPIEZA` | 08, 10, 12, HU-ADM-02 |
| 16 | Efectos del check-out desactualizados | Documento 01 corregido y remitido al documento 07 | 01 |
| 17 | Cambio de habitación "mientras no esté Finalizada" | Ahora: en `PENDIENTE_PAGO`, `CONFIRMADA` o `EN_ESTADIA` | HU-REC-09 |

### Severidad baja

| # | Hallazgo | Solución aplicada | Archivos |
|---|---|---|---|
| 18 | Etiquetas distintas ("Por limpiar", "No se presentó") | Se unificó a "Sucia" y "No-show" | 07 |
| 19 | Leyenda incompleta del documento 10 | Se agregaron `D01`, `D04` y `REV` | 10 |
| 20 | "Salida reciente" como prioridad no definida | Se quitó el ejemplo | HU-MYL-01 |
| 21 | Redacción confusa sobre datos personales | Reescrito | HU-MYL-04 |
| 22 | Check-in sin exigir habitación `Libre` | Se exige `Libre` y `Limpia` | HU-REC-15 |
| 23 | Correo de empleado usado como huésped | Nuevo criterio con RN-PER-006 | HU-REC-01 |
| 24 | Dependencia del Wi-Fi | HU-HUE-18 depende de HU-ADM-19 | HU-HUE-18 |
| 25 | Web pública sin requisito funcional | Nuevos RF-HAB-006 y RF-ADM-007 | 11 |
| 26 | Definición de "ingresos" | Nueva RN-PAG-017: cargos vigentes por fecha, incluidas penalidades | 10, 11, HU-ADM-18 |
| 27 | Menú visible sin sesión no marcado | Nota ¹ agregada en la matriz | 09 |

---

## 3. Además

En los documentos 07 y 08, "el mismo empleado" se cambió por **"el empleado a cargo"** (transiciones C4, C5 y Q3), para que la reasignación de HU-ADM-20 sea válida.
