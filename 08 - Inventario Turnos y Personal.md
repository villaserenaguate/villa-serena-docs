# 08 — Inventario, Turnos y Personal

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** 07 — Estados
> **Siguiente documento:** 09 — Matriz de Permisos

---

## Índice

1. [Propósito](#1-propósito)
2. [Decisiones aprobadas](#2-decisiones-aprobadas)
3. [Inventario](#3-inventario)
4. [Catálogo de artículos para el huésped](#4-catálogo-de-artículos-para-el-huésped)
5. [Turnos](#5-turnos)
6. [Personal](#6-personal)
7. [Cómo se conectan los tres módulos](#7-cómo-se-conectan-los-tres-módulos)
8. [Reglas de negocio de este documento](#8-reglas-de-negocio-de-este-documento)
9. [Ajustes aplicados a las historias de usuario](#9-ajustes-aplicados-a-las-historias-de-usuario)

---

## 1. Propósito

Define cómo funcionan y cómo se relacionan tres módulos del Administrador que antes estaban desconectados: **inventario**, **turnos** y **personal**. Resuelve el problema 3.5 del Reporte de Revisión.

---

## 2. Decisiones aprobadas

| # | Decisión |
|---|---|
| D8-01 | Los **artículos consumibles** (jabón, shampoo, papel higiénico) se descuentan del inventario al entregarse al huésped. |
| D8-02 | La **lencería** (toallas, almohadas, cobijas) **no se controla** en el inventario en la versión 1, porque se reutiliza. Sí se puede solicitar desde la app. |
| D8-03 | Los **turnos son informativos**: no bloquean el acceso al sistema. |
| D8-04 | **No se puede desactivar** a un empleado con trabajo en curso hasta reasignarlo. |

---

## 3. Inventario

### 3.1 Producto

| Campo | Descripción |
|---|---|
| Nombre | Único |
| Categoría | `INSUMO_LIMPIEZA`, `AMENIDAD_HABITACION` o `REPUESTO` |
| Unidad de medida | Unidad, litro, rollo, galón, caja, etc. |
| Stock actual | Se calcula con los movimientos; nunca negativo |
| Stock mínimo | Umbral para la alerta |
| Activo | Sí / No |

| Categoría | Ejemplos | Quién la consume |
|---|---|---|
| `INSUMO_LIMPIEZA` | Desinfectante, bolsas de basura, detergente | Limpieza (HU-MYL-08) |
| `AMENIDAD_HABITACION` | Jabón, shampoo, papel higiénico | Limpieza (HU-MYL-08) y entrega de artículos al huésped (HU-MYL-07) |
| `REPUESTO` | Focos, llaves de ducha, filtros de aire | Mantenimiento (HU-MYL-16) |

### 3.2 Movimientos de inventario

El stock **solo cambia mediante movimientos**. Nunca se edita el número de stock directamente.

| Código | Efecto | Quién | Origen (registro vinculado) |
|---|---|---|---|
| `ENTRADA` | + stock | `ADMIN` | Compra, o atención de un reporte de faltante (HU-ADM-13, 15) |
| `AJUSTE` | + o − stock | `ADMIN` | Conteo físico; motivo obligatorio (HU-ADM-13) |
| `CONSUMO_LIMPIEZA` | − stock | MYL | Limpieza de habitación (HU-MYL-08) |
| `CONSUMO_ENTREGA` | − stock | MYL | Solicitud de artículos atendida (HU-MYL-07) |
| `CONSUMO_REPUESTO` | − stock | MYL | Orden de mantenimiento (HU-MYL-16) |

**Cada movimiento guarda:** producto, tipo, cantidad, stock resultante, responsable, fecha y hora, motivo o nota, y el registro que lo originó (limpieza, solicitud, orden o reporte de faltante).

### 3.3 Alertas y reportes de faltante

Son dos mecanismos **distintos** y complementarios:

| Mecanismo | Cómo se origina | Quién lo ve | Cómo se resuelve |
|---|---|---|---|
| **Alerta de stock mínimo** | Automática: el stock queda igual o por debajo del mínimo | Administrador | Desaparece cuando una entrada sube el stock sobre el mínimo |
| **Reporte de faltante** | Manual: el personal lo reporta (HU-MYL-09) | Administrador y quien lo reportó | El Admin registra la entrada y el reporte pasa a `ATENDIDO` (HU-ADM-15) |

### 3.4 Flujo de reposición

```
Personal detecta que falta ──► Reporte de faltante (PENDIENTE)
                                        │
Stock ≤ mínimo ──► Alerta al Admin      │
                          │             │
                          ▼             ▼
                  Admin registra ENTRADA de inventario
                          │
                          ├──► Stock actualizado
                          └──► Reporte de faltante → ATENDIDO
```

---

## 4. Catálogo de artículos para el huésped

Es la lista de lo que el huésped puede pedir desde la app (HU-HUE-16) o por medio de Recepción (HU-REC-21). Lo administra el Administrador.

| Campo | Descripción |
|---|---|
| Nombre | Ej. "Toalla de baño", "Jabón", "Almohada adicional" |
| Cantidad máxima por solicitud | Ej. 4 |
| Producto de inventario vinculado | **Opcional.** Solo para consumibles (`AMENIDAD_HABITACION`) |
| Activo | Sí / No |

| Artículo | ¿Vinculado al inventario? | ¿Descuenta stock al entregarse? |
|---|---|---|
| Jabón, shampoo, papel higiénico | Sí (`AMENIDAD_HABITACION`) | ✅ Sí (`CONSUMO_ENTREGA`) |
| Toallas, almohadas, cobijas | No (lencería) | ❌ No |

**Al marcar una solicitud de artículos como `ATENDIDA`:** por cada artículo vinculado a un producto se registra un movimiento `CONSUMO_ENTREGA` con la cantidad entregada. Si no hay stock suficiente, no se puede marcar como atendida hasta que el Admin registre una entrada o un ajuste.

---

## 5. Turnos

### 5.1 Turno

| Campo | Descripción |
|---|---|
| Nombre | Ej. Mañana, Tarde, Noche |
| Hora de inicio / hora de fin | Puede cruzar la medianoche (ej. 22:00 – 06:00) |
| Activo | Sí / No |

### 5.2 Asignación de turno

| Campo | Descripción |
|---|---|
| Empleado | Cualquier rol excepto `ADMIN` |
| Fecha | Día en que **inicia** el turno |
| Turno | Turno activo |

### 5.3 Para qué se usan los turnos

| Uso | Dónde |
|---|---|
| Filtro "mi turno" en el historial de pedidos (ventana de tiempo del turno actual del empleado) | HU-RS-09 |
| Panel "personal en turno ahora" | HU-ADM-04 |
| Sugerir técnicos en turno al asignar una orden y advertir (sin bloquear) si el elegido no está en turno | HU-ADM-16 |
| Cada empleado consulta sus turnos de la semana | HU-ADM-04 |

**Los turnos no bloquean el acceso.** Un empleado puede entrar y trabajar fuera de su turno (por ejemplo, para cubrir a un compañero).

---

## 6. Personal

### 6.1 Modelo de usuarios

| Tipo | Autenticación | Perfil |
|---|---|---|
| Empleado | Usuario de Supabase Auth (correo + contraseña) | Perfil de empleado: nombre, teléfono, rol, área (solo MYL), estado |
| Huésped | Usuario de Supabase Auth (OTP por correo) | Perfil de huésped: nombre, documento, nacionalidad, teléfono, correo. Se vincula al usuario en su primer acceso |

Un mismo correo **no** puede ser empleado y huésped a la vez.

### 6.2 Responsable de cada acción

Todo registro creado o modificado por el personal guarda **quién lo hizo**: cambios de estado, cargos, pagos, movimientos de inventario, observaciones, reportes, asignaciones.

### 6.3 Desactivación de un empleado

| Paso | Detalle |
|---|---|
| 1. Verificación | El sistema revisa si el empleado tiene **órdenes `ASIGNADA` o `EN_PROCESO`**, **solicitudes `EN_PROCESO`** o **habitaciones `EN_LIMPIEZA`** a su cargo |
| 2. Bloqueo | Si tiene trabajo en curso, **no se permite** desactivarlo; se muestra la lista para que el Admin lo reasigne |
| 3. Desactivación | El estado pasa a `INACTIVO`; se cierra su sesión y no puede volver a entrar |
| 4. Limpieza | Se eliminan sus **asignaciones de turno futuras** |
| 5. Historial | Su nombre se conserva en todos los registros históricos |

**Reasignar trabajo en curso (HU-ADM-20):** el Admin pasa las solicitudes `EN_PROCESO` y las limpiezas `EN_LIMPIEZA` a otro empleado con área `LIMPIEZA` o `AMBAS`, y las órdenes a otro técnico. Se registra como un cambio de responsable con motivo, sin cambiar el estado.

---

## 7. Cómo se conectan los tres módulos

```
                        ┌──────────────┐
          crea/desactiva│ ADMINISTRADOR│ asigna turnos
        ┌───────────────┤              ├───────────────┐
        ▼               └──────┬───────┘               ▼
  ┌──────────┐                 │ ENTRADA / AJUSTE  ┌────────┐
  │ EMPLEADO │─────────────────┼──────────────────►│ TURNOS │
  └────┬─────┘                 ▼                   └────────┘
       │                ┌────────────┐
       ├─ Limpieza ────►│ INVENTARIO │◄── CONSUMO_LIMPIEZA / CONSUMO_ENTREGA
       ├─ Mantenimiento►│            │◄── CONSUMO_REPUESTO
       │                └─────┬──────┘
       │                      │ stock ≤ mínimo
       └─ Reporte de faltante─┴──────────► Alerta / tarea para el ADMINISTRADOR
```

---

## 8. Reglas de negocio de este documento

Estas reglas se consolidarán en el documento 10 — Reglas de Negocio.

### Inventario

| ID | Regla |
|---|---|
| RN-INV-001 | El stock solo cambia mediante movimientos de inventario. |
| RN-INV-002 | El stock nunca puede quedar negativo; un consumo o ajuste que lo deje negativo se rechaza. |
| RN-INV-003 | Todo movimiento registra producto, tipo, cantidad, stock resultante, responsable, fecha y origen. |
| RN-INV-004 | Un `AJUSTE` requiere motivo obligatorio y solo lo hace el Administrador. |
| RN-INV-005 | Cuando el stock queda igual o por debajo del mínimo, se genera una alerta para el Administrador. |
| RN-INV-006 | Los consumos de limpieza solo usan productos `INSUMO_LIMPIEZA` o `AMENIDAD_HABITACION`; los de mantenimiento, solo `REPUESTO`. |
| RN-INV-007 | Al atender una solicitud de artículos, cada artículo vinculado a un producto descuenta stock (`CONSUMO_ENTREGA`). |
| RN-INV-008 | La lencería no se controla en el inventario en la versión 1. |
| RN-INV-009 | No puede existir más de un reporte de faltante `PENDIENTE` por producto. |

### Turnos

| ID | Regla |
|---|---|
| RN-TUR-001 | Un empleado no puede tener asignados dos turnos que se traslapen. |
| RN-TUR-002 | No se asignan turnos al rol `ADMIN` ni a empleados `INACTIVO`. |
| RN-TUR-003 | Los turnos son informativos: no restringen el acceso al sistema. |
| RN-TUR-004 | No se puede desactivar un turno con asignaciones futuras. |

### Personal

| ID | Regla |
|---|---|
| RN-PER-001 | Solo el Administrador crea, edita y desactiva empleados. |
| RN-PER-002 | Los empleados no se eliminan; se desactivan. |
| RN-PER-003 | No se puede desactivar a un empleado con órdenes `ASIGNADA` o `EN_PROCESO`, solicitudes `EN_PROCESO` o habitaciones `EN_LIMPIEZA` a su cargo. |
| RN-PER-004 | Al desactivar a un empleado se eliminan sus asignaciones de turno futuras y se cierra su sesión. |
| RN-PER-005 | El Administrador no puede desactivarse a sí mismo. |
| RN-PER-006 | Un mismo correo no puede pertenecer a un empleado y a un huésped. |
| RN-PER-007 | El rol `MANTENIMIENTO_LIMPIEZA` requiere un área (`LIMPIEZA`, `MANTENIMIENTO` o `AMBAS`). |

---

## 9. Ajustes aplicados a las historias de usuario

| Historia | Ajuste |
|---|---|
| HU-ADM-02 | Desactivación bloqueada con trabajo en curso; se eliminan turnos futuros |
| HU-ADM-04 | Se agrega el panel "personal en turno ahora" |
| HU-ADM-12 | Categorías con código; catálogo de artículos para el huésped |
| HU-ADM-16 | Sugiere técnicos en turno y advierte si el elegido no lo está |
| HU-HUE-16 | El catálogo de artículos lo administra el Admin |
| HU-MYL-07 | Al atender artículos consumibles se descuenta stock |
| HU-ADM-20 | Nueva (revisión de consistencia): reasignar trabajo en curso |
