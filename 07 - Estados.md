# 07 — Estados

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** 04 — Historias de Usuario
> **Siguiente documento:** 08 — Inventario, Turnos y Personal

---

## Índice

1. [Propósito y cómo leer este documento](#1-propósito-y-cómo-leer-este-documento)
2. [Decisiones aprobadas](#2-decisiones-aprobadas)
3. [Reserva](#3-reserva)
4. [Habitación](#4-habitación)
5. [Cuenta, cargos y pagos](#5-cuenta-cargos-y-pagos)
6. [Pedido de Room Service](#6-pedido-de-room-service)
7. [Solicitud de huésped](#7-solicitud-de-huésped)
8. [Incidencia / orden de mantenimiento](#8-incidencia--orden-de-mantenimiento)
9. [Estados simples](#9-estados-simples)
10. [Reglas generales](#10-reglas-generales)
11. [Efectos automáticos entre módulos](#11-efectos-automáticos-entre-módulos)
12. [Ajustes aplicados a las historias de usuario](#12-ajustes-aplicados-a-las-historias-de-usuario)

---

## 1. Propósito y cómo leer este documento

Define **todos los estados** de cada elemento del sistema, **qué cambios de estado están permitidos**, **quién** puede hacerlos, **bajo qué condición** y **qué efectos** producen.

- Cada tabla de transiciones es una **lista cerrada**: todo cambio que no aparezca está **prohibido**.
- El **código** (ej. `EN_ESTADIA`) es el valor que se guarda en la base de datos y se usa en el código fuente. Es idéntico en base de datos, backend, web y app.
- La **etiqueta** (ej. "En estadía") es el texto que ve el usuario.
- **Actores:** `ADMIN`, `RECEPCION`, `ROOM_SERVICE`, `MANTENIMIENTO_LIMPIEZA` (abreviado **MYL**), `HUESPED`, `SISTEMA`, `PASARELA`, `CANAL` (ver documento 02).

**Uso por integrante:**

| Integrante | Uso |
|---|---|
| Pablo y Hugo (BD) | Tipos enumerados, restricciones y tabla de historial de estados |
| Backend | Validación de transiciones y efectos automáticos |
| Kim y Carlos (web y app) | Qué botones mostrar en cada estado y qué etiqueta usar |
| Alex (CI/CD) | Casos de prueba: cada fila es una prueba permitida; cada transición no listada, una prueba de rechazo |

---

## 2. Decisiones aprobadas

| # | Decisión |
|---|---|
| E-01 | **La cuenta del huésped se crea junto con la reserva**, no en el check-in. |
| E-02 | **La limpieza de habitaciones ocupadas es solo a pedido del huésped.** No existe limpieza diaria automática. |
| E-03 | **El no-show lo marca el sistema automáticamente.** |
| E-04 | **La habitación tiene dos dimensiones de estado:** ocupación y condición. |
| E-05 | **Todo cambio de estado queda registrado en un historial.** |

---

## 3. Reserva

### 3.1 Estados

| Código | Etiqueta | Ocupa disponibilidad | Final |
|---|---|---|---|
| `PENDIENTE_PAGO` | Pendiente de pago | ✅ | — |
| `CONFIRMADA` | Confirmada | ✅ | — |
| `EN_ESTADIA` | En estadía | ✅ | — |
| `FINALIZADA` | Finalizada | — | ✅ |
| `CANCELADA` | Cancelada | — | ✅ |
| `NO_SHOW` | No-show | — | ✅ |

**Regla de sobreventa:** una habitación (o un cupo de un tipo de habitación) está ocupada en una fecha si existe una reserva en `PENDIENTE_PAGO`, `CONFIRMADA` o `EN_ESTADIA` que incluya esa noche.

### 3.2 Diagrama

```
           (web)                    (recepción / canal)
             │                              │
             ▼                              │
     ┌───────────────┐   webhook OK         ▼
     │PENDIENTE_PAGO │ ───────────────► ┌──────────┐  check-in   ┌────────────┐  check-out  ┌────────────┐
     └───────────────┘                  │CONFIRMADA│ ──────────► │ EN_ESTADIA │ ──────────► │ FINALIZADA │
             │                          └──────────┘             └────────────┘             └────────────┘
             │ vence / cancelación        │      │
             ▼                            │      │ no llegó
     ┌───────────────┐    cancelación     │      ▼
     │   CANCELADA   │ ◄──────────────────┘  ┌─────────┐
     └───────────────┘                       │ NO_SHOW │
                                             └─────────┘
```

### 3.3 Transiciones

| # | De → A | Quién | Condición | Efectos |
|---|---|---|---|---|
| R1 | (nueva) → `PENDIENTE_PAGO` | `HUESPED` (web pública) | Hay disponibilidad | Se crea la cuenta con el cargo por alojamiento; se aparta la habitación |
| R2 | (nueva) → `CONFIRMADA` | `RECEPCION`, `ADMIN`, `CANAL` | Hay disponibilidad | Se crea la cuenta con el cargo por alojamiento |
| R3 | `PENDIENTE_PAGO` → `CONFIRMADA` | `PASARELA` | Webhook de pago aprobado | Correo de confirmación con código y enlace a la app |
| R4 | `PENDIENTE_PAGO` → `CANCELADA` | `SISTEMA` | Venció el tiempo límite de pago | Libera la habitación; cierra la cuenta |
| R5 | `PENDIENTE_PAGO` → `CANCELADA` | `HUESPED`, `RECEPCION`, `ADMIN` | Motivo obligatorio | Libera la habitación; cierra la cuenta |
| R6 | `CONFIRMADA` → `EN_ESTADIA` | `RECEPCION`, `ADMIN` (check-in) | Fecha de entrada = hoy; habitación asignada; habitación `LIBRE` + `LIMPIA` | Habitación → `OCUPADA`; se habilitan las funciones de estadía en la app |
| R7 | `CONFIRMADA` → `CANCELADA` | `HUESPED`, `RECEPCION`, `ADMIN`, `CANAL` (solo sus reservas) | Motivo obligatorio | Libera la habitación; reembolso según la política; cierra la cuenta; correo al huésped |
| R8 | `CONFIRMADA` → `NO_SHOW` | `SISTEMA` | Terminó el día de llegada sin check-in (hora exacta en el documento 10) | Libera la habitación; cargo de no-show según la política; cierra la cuenta |
| R9 | `EN_ESTADIA` → `FINALIZADA` | `RECEPCION`, `ADMIN`, `HUESPED` (app) | Saldo = 0; sin pedidos de room service en curso (en la app) | Ver **efectos del check-out** (sección 11) |

### 3.4 Modificaciones que no cambian el estado

| Acción | Estados permitidos | Quién | Efecto |
|---|---|---|---|
| Cambiar fechas, huéspedes, tipo o habitación | `CONFIRMADA` | `RECEPCION`, `ADMIN` | Revalida disponibilidad; ajusta el cargo por alojamiento; si queda saldo a favor, se reembolsa antes del check-out |
| Cambiar fecha de salida o habitación | `EN_ESTADIA` | `RECEPCION`, `ADMIN` | Revalida disponibilidad; ajusta el cargo por alojamiento |
| Asignar o cambiar habitación | `PENDIENTE_PAGO`, `CONFIRMADA`, `EN_ESTADIA` | `RECEPCION`, `ADMIN` | Revalida que no haya traslapes |
| Check-in anticipado | `CONFIRMADA` | `HUESPED` | Marca "check-in anticipado completado" |

---

## 4. Habitación

La habitación tiene **dos dimensiones independientes**.

### 4.1 Ocupación

| Código | Etiqueta |
|---|---|
| `LIBRE` | Libre |
| `OCUPADA` | Ocupada |

| # | De → A | Quién | Condición |
|---|---|---|---|
| O1 | `LIBRE` → `OCUPADA` | `SISTEMA` | Al hacer check-in (R6) |
| O2 | `OCUPADA` → `LIBRE` | `SISTEMA` | Al hacer check-out (R9) |

**La ocupación nunca se cambia a mano.**

### 4.2 Condición

| Código | Etiqueta |
|---|---|
| `LIMPIA` | Limpia |
| `SUCIA` | Sucia |
| `EN_LIMPIEZA` | En limpieza |
| `FUERA_DE_SERVICIO` | Fuera de servicio |

| # | De → A | Quién | Condición | Efectos |
|---|---|---|---|---|
| C1 | `LIMPIA` / `EN_LIMPIEZA` / `SUCIA` → `SUCIA` | `SISTEMA` | Check-out sin incidencias que impidan el uso (desde cualquier condición) | Aparece en pendientes de limpieza |
| C2 | `LIMPIA` → `SUCIA` | `RECEPCION`, `ADMIN` | Manual (ej. pedir una revisión de limpieza) | Aparece en pendientes de limpieza |
| C3 | `SUCIA` → `EN_LIMPIEZA` | MYL (área Limpieza o Ambas) | — | Registra inicio y empleado |
| C4 | `EN_LIMPIEZA` → `LIMPIA` | MYL (el empleado a cargo) | — | Registra finalización; aviso a Recepción si hay llegada hoy |
| C5 | `EN_LIMPIEZA` → `SUCIA` | MYL (el empleado a cargo) | Limpieza interrumpida | Vuelve a pendientes |
| C6 | `LIMPIA` / `SUCIA` / `EN_LIMPIEZA` → `FUERA_DE_SERVICIO` | `SISTEMA` | Se reporta una incidencia que impide el uso **y** la habitación está `LIBRE` | Se excluye de disponibilidad |
| C7 | `OCUPADA` + cualquier condición → `FUERA_DE_SERVICIO` | `SISTEMA` | Check-out de una habitación con incidencia abierta que impide el uso | Se excluye de disponibilidad |
| C8 | `FUERA_DE_SERVICIO` → `SUCIA` | `SISTEMA` | Se cierra o cancela la **última** orden abierta que impedía el uso | Aparece en pendientes de limpieza |

**Prohibido:** `RECEPCION` no puede marcar una habitación como `LIMPIA`. Nadie puede poner una habitación en `FUERA_DE_SERVICIO` a mano: siempre se hace mediante una incidencia.

**Limpieza a pedido de un huésped alojado:** se gestiona solo como **solicitud** (sección 7) y **no cambia** la condición de la habitación. La condición solo cambia por check-out, por Recepción (C2) o por mantenimiento (C6 a C8) (RN-LIM-013).

### 4.3 Estado combinado que ve Recepción

| Se muestra como | Ocupación | Condición | Color sugerido |
|---|---|---|---|
| Disponible | `LIBRE` | `LIMPIA` | Verde |
| Ocupada | `OCUPADA` | cualquiera excepto `FUERA_DE_SERVICIO` | Azul |
| Sucia | `LIBRE` | `SUCIA` | Amarillo |
| En limpieza | `LIBRE` | `EN_LIMPIEZA` | Naranja |
| Fuera de servicio | `LIBRE` | `FUERA_DE_SERVICIO` | Gris / rojo |

**Indicadores adicionales:** "Llega hoy" (hay una reserva `CONFIRMADA` con entrada hoy), "Sale hoy" (reserva `EN_ESTADIA` con salida hoy) y "Incidencia pendiente" (habitación ocupada con incidencia que impide el uso).

---

## 5. Cuenta, cargos y pagos

### 5.1 Cuenta del huésped

| Código | Etiqueta | Final |
|---|---|---|
| `ABIERTA` | Abierta | — |
| `CERRADA` | Cerrada | ✅ |

| # | De → A | Quién | Condición |
|---|---|---|---|
| K1 | (nueva) → `ABIERTA` | `SISTEMA` | Al crear la reserva (R1, R2). Se agrega el cargo por alojamiento |
| K2 | `ABIERTA` → `CERRADA` | `SISTEMA` | La reserva pasa a `FINALIZADA`, `CANCELADA` o `NO_SHOW` |

**Reglas de la cuenta:**

- Solo existe **una cuenta por reserva**.
- El **cargo por alojamiento** se agrega al crear la reserva y se recalcula si cambian las fechas o la habitación.
- Los **cargos adicionales** (room service, servicios) solo se permiten cuando la reserva está `EN_ESTADIA`.
- **Saldo** = suma de cargos `VIGENTE` − (suma de pagos `APROBADO` − reembolsos parciales). Los pagos `PENDIENTE`, `FALLIDO` y `REEMBOLSADO` no cuentan (ver RN-PAG-012).
- **Saldo a favor** (negativo): se reembolsa antes del check-out, por Stripe si se pagó en línea o en efectivo si se pagó en recepción. El check-out exige saldo **exactamente 0** (RN-PAG-016).

### 5.2 Cargo

| Código | Etiqueta |
|---|---|
| `VIGENTE` | Vigente |
| `ANULADO` | Anulado |

| # | De → A | Quién | Condición |
|---|---|---|---|
| G1 | (nuevo) → `VIGENTE` | `SISTEMA` (alojamiento, room service), `RECEPCION`, `ADMIN` (servicios) | Cuenta `ABIERTA` |
| G2 | `VIGENTE` → `ANULADO` | `RECEPCION`, `ADMIN` | Motivo obligatorio; cuenta `ABIERTA` |

Los cargos **nunca se eliminan**, solo se anulan.

### 5.3 Pago

| Código | Etiqueta | Final |
|---|---|---|
| `PENDIENTE` | Pendiente | — |
| `APROBADO` | Aprobado | — |
| `FALLIDO` | Fallido | ✅ |
| `REEMBOLSADO` | Reembolsado | ✅ |

| # | De → A | Quién | Condición |
|---|---|---|---|
| P1 | (nuevo) → `APROBADO` | `RECEPCION`, `ADMIN` | Pago en recepción (efectivo, tarjeta u otro); monto ≤ saldo |
| P2 | (nuevo) → `PENDIENTE` | `HUESPED` | Inicia un pago en línea con Stripe |
| P3 | `PENDIENTE` → `APROBADO` | `PASARELA` | Webhook de pago exitoso |
| P4 | `PENDIENTE` → `FALLIDO` | `PASARELA` | Webhook de pago fallido o expirado |
| P5 | `APROBADO` → `REEMBOLSADO` | `SISTEMA` | Reembolso **total** por cancelación. Un reembolso **parcial** se registra asociado al pago, que sigue `APROBADO` (RN-CAN-008) |
| P6 | (nuevo) → `APROBADO` | `SISTEMA` | Reserva recibida de un canal: pago por el monto del canal con método `CANAL` (RN-PAG-008) |

**Idempotencia:** cada evento de Stripe tiene un identificador único. Si un evento ya fue procesado, se ignora (RN-PAG-002, RN-PAG-003).

---

## 6. Pedido de Room Service

| Código | Etiqueta | Final |
|---|---|---|
| `NUEVO` | Nuevo | — |
| `EN_PREPARACION` | En preparación | — |
| `EN_CAMINO` | En camino | — |
| `ENTREGADO` | Entregado | ✅ |
| `CANCELADO` | Cancelado | ✅ |

```
NUEVO ──► EN_PREPARACION ──► EN_CAMINO ──► ENTREGADO
  │             │                │
  └─────────────┴────────────────┴──► CANCELADO (motivo obligatorio)
```

| # | De → A | Quién | Condición | Efectos |
|---|---|---|---|---|
| S1 | (nuevo) → `NUEVO` | `HUESPED` (app), `ROOM_SERVICE` (teléfono) | Reserva `EN_ESTADIA`; ítems `DISPONIBLE` | Aviso en tiempo real a Room Service |
| S2 | `NUEVO` → `EN_PREPARACION` | `ROOM_SERVICE` | — | El huésped ve el cambio |
| S3 | `EN_PREPARACION` → `EN_CAMINO` | `ROOM_SERVICE` | — | El huésped ve el cambio |
| S4 | `EN_CAMINO` → `ENTREGADO` | `ROOM_SERVICE` | — | Se genera **un** cargo en la cuenta |
| S5 | `NUEVO` / `EN_PREPARACION` / `EN_CAMINO` → `CANCELADO` | `ROOM_SERVICE`, `ADMIN` | Motivo obligatorio | Sin cargo; el huésped ve el motivo |

El huésped **no** puede cancelar pedidos.

---

## 7. Solicitud de huésped

Aplica a solicitudes de **limpieza** y de **artículos**.

| Código | Etiqueta | Final |
|---|---|---|
| `PENDIENTE` | Pendiente | — |
| `EN_PROCESO` | En proceso | — |
| `ATENDIDA` | Atendida | ✅ |
| `CANCELADA` | Cancelada | ✅ |

| # | De → A | Quién | Condición | Efectos |
|---|---|---|---|---|
| Q1 | (nueva) → `PENDIENTE` | `HUESPED` (app), `RECEPCION`, `ADMIN` | Reserva `EN_ESTADIA` | Aviso en tiempo real a Limpieza |
| Q2 | `PENDIENTE` → `EN_PROCESO` | MYL | — | Se asigna al empleado que la tomó |
| Q3 | `EN_PROCESO` → `ATENDIDA` | MYL (el empleado a cargo) | En solicitudes de artículos, confirma la entrega | El huésped ve el cambio |
| Q4 | `PENDIENTE` → `CANCELADA` | `HUESPED`, `RECEPCION`, `ADMIN` | — | — |
| Q5 | `PENDIENTE` → `CANCELADA` | `SISTEMA` | Check-out de la reserva | Motivo "Estadía finalizada" |

"Entregada" (antigua HU-LIM-15) se unifica como `ATENDIDA`.

---

## 8. Incidencia / orden de mantenimiento

Una incidencia **se convierte en orden de trabajo** cuando el Administrador la asigna. Es el mismo registro, que avanza de estado.

| Código | Etiqueta | Final |
|---|---|---|
| `REPORTADA` | Reportada | — |
| `ASIGNADA` | Asignada | — |
| `EN_PROCESO` | En proceso | — |
| `RESUELTA` | Resuelta | — |
| `CERRADA` | Cerrada | ✅ |
| `CANCELADA` | Cancelada | ✅ |

```
REPORTADA ──► ASIGNADA ──► EN_PROCESO ──► RESUELTA ──► CERRADA
    │            │  ▲            │   ▲          │
    │            └──┘ reasignar  │   └──────────┘ devolver (Admin)
    └────────────┴───────────────┴──────────────┴──► CANCELADA (Admin, motivo)
```

| # | De → A | Quién | Condición | Efectos |
|---|---|---|---|---|
| M1 | (nueva) → `REPORTADA` | MYL, `RECEPCION`, `ADMIN` | Habitación, tipo y descripción obligatorios; indica si impide el uso | Si impide el uso y la habitación está `LIBRE` → C6 |
| M2 | `REPORTADA` → `ASIGNADA` | `ADMIN` | Técnico con área `MANTENIMIENTO` o `AMBAS`; prioridad; fecha compromiso | El técnico la ve en su lista |
| M3 | `ASIGNADA` → `ASIGNADA` | `ADMIN` | Reasignación a otro técnico; motivo obligatorio | — |
| M4 | `ASIGNADA` → `EN_PROCESO` | Técnico asignado | — | Registra hora de inicio |
| M5 | `EN_PROCESO` → `RESUELTA` | Técnico asignado | Descripción de la solución obligatoria | Aviso al Administrador |
| M6 | `RESUELTA` → `EN_PROCESO` | `ADMIN` | Comentario obligatorio (reparación no aceptada) | Vuelve al técnico |
| M7 | `RESUELTA` → `CERRADA` | `ADMIN` | — | Si es la última orden que impedía el uso → C8 |
| M8 | `REPORTADA` / `ASIGNADA` / `EN_PROCESO` / `RESUELTA` → `CANCELADA` | `ADMIN` | Motivo obligatorio | Si es la última orden que impedía el uso → C8 |

**Prioridad** (atributo, no estado): `ALTA`, `MEDIA`, `BAJA`.

---

## 9. Estados simples

| Elemento | Códigos | Transiciones | Quién |
|---|---|---|---|
| Reporte de faltante | `PENDIENTE`, `ATENDIDO` | `PENDIENTE → ATENDIDO` | `ADMIN` |
| Objeto olvidado | `REGISTRADO`, `DEVUELTO`, `DESECHADO` | `REGISTRADO → DEVUELTO` (a quién y cuándo) · `REGISTRADO → DESECHADO` (motivo) | MYL, `RECEPCION`, `ADMIN` |
| Ítem del menú | `DISPONIBLE`, `AGOTADO` | `DISPONIBLE → AGOTADO` · `AGOTADO → DISPONIBLE` | Agotar: `ROOM_SERVICE`, `ADMIN` · Reactivar: solo `ADMIN` |
| Empleado | `ACTIVO`, `INACTIVO` | `ACTIVO ↔ INACTIVO` | `ADMIN` (no puede desactivarse a sí mismo) |
| Canal externo | `ACTIVO`, `INACTIVO` | `ACTIVO ↔ INACTIVO` | `ADMIN` |
| Catálogos (tipos de habitación, habitaciones, ítems, amenidades, productos, turnos) | Campo `activo` (sí / no) | Activar / desactivar | `ADMIN` |

---

## 10. Reglas generales

| # | Regla |
|---|---|
| RG-EST-01 | **Lista cerrada:** solo se permiten las transiciones documentadas aquí. |
| RG-EST-02 | **Historial obligatorio:** cada cambio de estado registra elemento, estado anterior, estado nuevo, actor, fecha, hora y motivo (si aplica). Cubre ALC-TRA-03. |
| RG-EST-03 | **Validación en el servidor:** las transiciones se validan en el backend o la base de datos, no solo en la interfaz. |
| RG-EST-04 | **Estados finales inmutables:** un registro en estado final no puede cambiar de estado ni editarse. |
| RG-EST-05 | **Códigos únicos en todo el proyecto:** los códigos de este documento se usan igual en base de datos, backend, web, app y pruebas. |
| RG-EST-06 | **Motivo obligatorio** en toda cancelación, anulación, reasignación o devolución. |
| RG-EST-07 | **Actualización en tiempo real:** los cambios de estado de pedidos, solicitudes, habitaciones y reservas se reflejan sin recargar en las pantallas que los muestran. |

---

## 11. Efectos automáticos entre módulos

| Evento | Efectos automáticos |
|---|---|
| **Reserva creada** (R1, R2) | Cuenta `ABIERTA` + cargo por alojamiento; habitación apartada |
| **Pago en línea aprobado** (R3, P3) | Reserva `CONFIRMADA`; correo de confirmación |
| **Tiempo límite de pago vencido** (R4) | Reserva `CANCELADA`; cuenta `CERRADA`; habitación liberada |
| **Check-in** (R6) | Ocupación `OCUPADA`; funciones de estadía habilitadas en la app |
| **Check-out** (R9) | Cuenta `CERRADA` · ocupación `LIBRE` · condición `SUCIA` (o `FUERA_DE_SERVICIO` si hay incidencia que impide el uso) · solicitudes `PENDIENTE` → `CANCELADA` · app en solo lectura · correo con resumen de cuenta |
| **No-show** (R8) | Cuenta `CERRADA` (con cargo de no-show si aplica); habitación liberada |
| **Cancelación** (R5, R7) | Cuenta `CERRADA`; reembolso si aplica; habitación liberada; correo al huésped |
| **Pedido entregado** (S4) | Cargo `VIGENTE` en la cuenta |
| **Incidencia que impide el uso** (M1) | Habitación `FUERA_DE_SERVICIO` si está libre; si está ocupada, indicador "Incidencia pendiente" |
| **Última orden bloqueante cerrada o cancelada** (M7, M8) | Habitación `SUCIA` |
| **Limpieza finalizada** (C4) | Aviso a Recepción si hay llegada hoy |
| **Consumo de insumos o repuestos** | Descuento de stock; alerta si queda por debajo del mínimo (documento 08) |

---

## 12. Ajustes aplicados a las historias de usuario

| Historia | Ajuste |
|---|---|
| HU-HUE-05 | Nuevo criterio: se crea la cuenta con el cargo por alojamiento |
| HU-HUE-20 | Efectos del check-out alineados con la sección 11 |
| HU-REC-05 | Nuevo criterio: se crea la cuenta con el cargo por alojamiento |
| HU-REC-06 | El cargo por alojamiento se ajusta al modificar la reserva |
| HU-REC-15 | El check-in ya no abre la cuenta (existe desde la reserva) |
| HU-REC-16 | Efectos del check-out alineados con la sección 11 |
| HU-MYL-02 | Nuevo criterio: se puede interrumpir una limpieza (C5) |
| Índice de HU | Estado cambiado a "Aprobado"; tabla de estados remite a este documento |

**Revisión de consistencia (posterior a la aprobación):** etiquetas "Sucia" y "No-show"; C1 desde cualquier condición; limpieza a pedido no cambia la condición; modificaciones solo en `CONFIRMADA`; saldo a favor; transición P6; "empleado a cargo" en C4, C5 y Q3 (permite reasignar, HU-ADM-20).
