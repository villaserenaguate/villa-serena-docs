# 09 — Matriz de Permisos

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** 08 — Inventario, Turnos y Personal
> **Siguiente documento:** 10 — Reglas de Negocio

---

## Índice

1. [Propósito y leyenda](#1-propósito-y-leyenda)
2. [Decisiones aprobadas](#2-decisiones-aprobadas)
3. [Matriz por módulo](#3-matriz-por-módulo)
4. [Actores no humanos](#4-actores-no-humanos)
5. [Notas de la matriz](#5-notas-de-la-matriz)
6. [Reglas de visibilidad de datos](#6-reglas-de-visibilidad-de-datos)
7. [Guía de implementación](#7-guía-de-implementación)
8. [Ajustes aplicados a las historias de usuario](#8-ajustes-aplicados-a-las-historias-de-usuario)

---

## 1. Propósito y leyenda

Define **qué acción exacta puede hacer cada rol** en el sistema. Es la referencia para:

- **Pablo y Hugo:** políticas de seguridad a nivel de fila (RLS) en Supabase y permisos de almacenamiento.
- **Backend:** validaciones de autorización en cada operación.
- **Kim y Carlos:** qué menús, pantallas y botones ve cada rol.
- **Alex:** pruebas de autorización (cada "—" es una prueba que debe ser rechazada).

| Símbolo | Significado |
|---|---|
| ✅ | Permitido |
| 👤 | Permitido solo sobre sus propios registros |
| 👁 | Solo consulta (lectura) |
| ⚠️ | Permitido con condición (ver nota numerada) |
| — | No permitido |

**Columnas:** `ADMIN` · `RECEPCION` · `ROOM_SERVICE` · `MYL` (= `MANTENIMIENTO_LIMPIEZA`) · `HUESPED`

---

## 2. Decisiones aprobadas

| # | Decisión |
|---|---|
| P-01 | El Administrador es de **solo consulta** en las tareas operativas de Room Service y Mantenimiento/Limpieza (no inicia limpiezas, no avanza pedidos, no inicia ni resuelve órdenes). Conserva las acciones del documento 07: cancelar pedidos, marcar y reactivar ítems agotados, crear y cancelar solicitudes, reportar incidencias, y asignar, cerrar y cancelar órdenes. |
| P-02 | Recepción puede **consultar las incidencias** de las habitaciones, para saber por qué una habitación está fuera de servicio. |
| P-03 | El huésped **no modifica** su reserva; solo puede cancelarla. Los cambios los hace Recepción. |
| P-04 | Los permisos se aplican en la **base de datos y el backend**, no solo en la interfaz. |

---

## 3. Matriz por módulo

### 3.1 Reservas y huéspedes

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Ver disponibilidad y precios | ✅ | ✅ | — | — | ✅ ¹ |
| Crear reserva | ✅ | ✅ | — | — | ✅ ¹ |
| Modificar reserva | ✅ | ✅ | — | — | — |
| Cancelar reserva | ✅ | ✅ | — | — | 👤 |
| Ver reservas | ✅ | ✅ | — | — | 👤 |
| Buscar reservas y ver historial de huéspedes | ✅ | ✅ | — | — | 👤 |
| Ver datos personales del huésped | ✅ | ✅ | ⚠️ ² | — | 👤 |
| Ver foto del documento de identidad | ✅ | ✅ | — | — | 👤 |
| Registrar o editar huéspedes y adicionales | ✅ | ✅ | — | — | 👤 ³ |
| Check-in anticipado | — | — | — | — | 👤 |
| Asignar o cambiar habitación | ✅ | ✅ | — | — | — |
| Check-in | ✅ | ✅ | — | — | — |
| Check-out | ✅ | ✅ | — | — | 👤 (app) |
| Calendario Gantt y vista del día | ✅ | ✅ | — | — | — |

### 3.2 Cuenta y pagos

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Ver cuenta (cargos, pagos, saldo) | ✅ | ✅ | — | — | 👤 |
| Agregar cargos de servicios adicionales | ✅ | ✅ | — | — | — |
| Anular cargos | ✅ | ✅ | — | — | — |
| Registrar pago en recepción | ✅ | ✅ | — | — | — |
| Pagar en línea (Stripe) | — | — | — | — | 👤 |
| Generar comprobante de pago | ✅ | ✅ | — | — | 👤 (por correo) |

### 3.3 Habitaciones y limpieza

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Ver estado de las habitaciones | ✅ | ✅ | ⚠️ ⁴ | ✅ | — |
| Marcar habitación como `SUCIA` | ✅ | ✅ | — | — | — |
| Iniciar, finalizar o interrumpir limpieza | 👁 | 👁 | — | ⚠️ ⁵ | — |
| Reasignar limpieza `EN_LIMPIEZA` | ✅ | — | — | — | — |
| Registrar insumos usados en una limpieza | — | — | — | ⚠️ ⁵ | — |
| Registrar observaciones | 👁 | 👁 | — | ✅ | — |
| Registrar objeto olvidado | 👁 | 👁 | — | ✅ | — |
| Devolver o desechar objeto olvidado | ✅ | ✅ | — | ✅ | — |

### 3.4 Room Service

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Ver menú | ✅ | ✅ | ✅ | — | ✅ ¹ |
| Ver cola y detalle de pedidos | 👁 | 👁 | ✅ | — | 👤 |
| Crear pedido | — | — | ✅ (teléfono) | — | 👤 ⁶ |
| Avanzar estado del pedido | — | — | ✅ | — | — |
| Cancelar pedido | ✅ | — | ✅ | — | — |
| Marcar ítem como agotado | ✅ | — | ✅ | — | — |
| Reactivar ítem agotado | ✅ | — | — | — | — |
| Ver historial de pedidos | ✅ | 👁 | ✅ | — | 👤 |

### 3.5 Solicitudes de huésped

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Crear solicitud (limpieza o artículos) | ✅ | ✅ | — | — | 👤 ⁶ |
| Ver solicitudes | ✅ | ✅ | — | ⚠️ ⁵ | 👤 |
| Tomar o atender solicitud | — | — | — | ⚠️ ⁵ | — |
| Cancelar solicitud `PENDIENTE` | ✅ | ✅ | — | — | 👤 |
| Reasignar solicitud `EN_PROCESO` | ✅ | — | — | — | — |

### 3.6 Mantenimiento

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Reportar incidencia | ✅ | ✅ | — | ✅ | — |
| Ver incidencias y órdenes | ✅ | 👁 | — | 👤 ⁷ | — |
| Asignar, reasignar, devolver, cerrar o cancelar orden | ✅ | — | — | — | — |
| Iniciar o resolver orden | — | — | — | 👤 ⁸ | — |
| Registrar repuestos usados | — | — | — | 👤 ⁸ | — |

### 3.7 Inventario

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Ver productos y stock | ✅ | — | — | 👁 | — |
| Gestionar productos y catálogo de artículos para huésped | ✅ | — | — | — | — |
| Registrar entradas y ajustes | ✅ | — | — | — | — |
| Registrar consumos | — | — | — | ✅ | — |
| Reportar faltante | — | — | — | ✅ | — |
| Ver y atender reportes de faltante | ✅ | — | — | 👤 (ver) | — |

### 3.8 Administración

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Gestionar empleados | ✅ | — | — | — | — |
| Definir y asignar turnos | ✅ | — | — | — | — |
| Ver mis turnos | — | 👤 | 👤 | 👤 | — |
| Ver personal en turno | ✅ | — | — | — | — |
| Gestionar catálogos (tipos de habitación, habitaciones, menú, amenidades) | ✅ | — | — | — | — |
| Gestionar tarifas (temporadas, fin de semana) | ✅ | — | — | — | — |
| Ver indicadores | ✅ | — | — | — | — |
| Configurar datos generales del hotel | ✅ | — | — | — | — |
| Ver amenidades | ✅ | ✅ | ✅ | ✅ | ✅ ¹ |

### 3.9 Channel Manager

| Acción | ADMIN | RECEPCION | ROOM_SERVICE | MYL | HUESPED |
|---|---|---|---|---|---|
| Gestionar canales y claves de API | ✅ | — | — | — | — |
| Usar el canal simulado | ✅ | — | — | — | — |
| Ver canal de origen de las reservas | ✅ | ✅ | — | — | — |

---

## 4. Actores no humanos

| Actor | Qué puede hacer | Cómo se autentica |
|---|---|---|
| `CANAL` | Crear reservas y cancelar **solo sus propias** reservas mediante la API | Clave de API de un canal `ACTIVO` |
| `PASARELA` | Notificar el resultado de los pagos mediante el webhook | Firma del webhook de Stripe |
| `SISTEMA` | Ejecutar los efectos automáticos del documento 07 (sección 11): vencimiento de pagos, no-show, check-out, correos, alertas de stock | Proceso interno del servidor |

---

## 5. Notas de la matriz

| # | Nota |
|---|---|
| ¹ | También sin sesión (visitante de la web pública). |
| ² | Solo nombre, número de habitación y piso de los huéspedes con pedidos activos. No ve documento, teléfono, correo ni pagos. |
| ³ | Solo sus propios datos y los de sus acompañantes, durante el check-in anticipado. |
| ⁴ | Solo número y piso de las habitaciones ocupadas, para registrar pedidos telefónicos. |
| ⁵ | Solo empleados con área `LIMPIEZA` o `AMBAS`. |
| ⁶ | Solo si su reserva está `EN_ESTADIA`. |
| ⁷ | Las órdenes asignadas a él, las incidencias que él reportó y, en solo lectura, las incidencias abiertas de la habitación que consulta (HU-MYL-04). |
| ⁸ | Solo órdenes asignadas a él, y solo con área `MANTENIMIENTO` o `AMBAS`. |

---

## 6. Reglas de visibilidad de datos

| ID | Regla |
|---|---|
| RN-SEG-001 | El huésped solo ve sus propios registros (reservas, cuenta, pedidos, solicitudes). |
| RN-SEG-002 | Room Service y Mantenimiento/Limpieza no ven datos personales de los huéspedes (documento, teléfono, correo, pagos). |
| RN-SEG-003 | Las fotos de documentos de identidad se guardan en almacenamiento privado; solo las ven Recepción, el Administrador y el propio huésped. |
| RN-SEG-004 | La información pública (tipos de habitación, amenidades, datos del hotel, disponibilidad y precios) se consulta sin sesión, sin exponer reservas ni datos de otros huéspedes. |
| RN-SEG-005 | Un empleado `INACTIVO` no tiene ningún permiso. |
| RN-SEG-006 | Los permisos se aplican en la base de datos (RLS) y en el backend; la interfaz solo oculta lo que el usuario no puede hacer. |
| RN-SEG-007 | Las claves con privilegios totales (service role) solo existen en el servidor y nunca se incluyen en la web ni en la app. |
| RN-SEG-008 | Los secretos (claves de Stripe, SMTP, API) no se guardan en el repositorio Git. |

---

## 7. Guía de implementación

Orientación para Pablo, Hugo y el backend. Los detalles técnicos se definen en el paso de arquitectura.

### 7.1 Datos del usuario que usan las políticas

| Dato | De dónde sale | Para qué |
|---|---|---|
| Identificador del usuario | Sesión de Supabase Auth | Saber quién hace la petición |
| Rol | Perfil del empleado, o "huésped" si tiene perfil de huésped | Aplicar las columnas de la matriz |
| Área | Perfil del empleado (solo MYL) | Notas ⁵ y ⁸ |
| Estado | Perfil del empleado | RN-SEG-005 |

Se recomienda crear **funciones auxiliares** en la base de datos (por ejemplo, "rol del usuario actual" y "área del usuario actual") para reutilizarlas en todas las políticas.

### 7.2 Tipos de política por tabla

| Tipo de dato | Política de lectura | Política de escritura |
|---|---|---|
| Catálogos públicos (tipos de habitación, amenidades, menú, datos del hotel) | Cualquiera, incluso sin sesión (solo registros activos) | Solo `ADMIN` |
| Reservas, cuentas, cargos, pagos | `ADMIN`, `RECEPCION`; `HUESPED` solo las suyas | Según la matriz; los cambios de estado, por funciones del servidor |
| Pedidos de Room Service | `ADMIN`, `RECEPCION`, `ROOM_SERVICE`; `HUESPED` solo los suyos | Según la matriz |
| Solicitudes | `ADMIN`, `RECEPCION`, MYL (área Limpieza o Ambas); `HUESPED` solo las suyas | Según la matriz |
| Incidencias y órdenes | `ADMIN`, `RECEPCION` (lectura); MYL solo asignadas o reportadas por él | Según la matriz |
| Inventario | `ADMIN`; MYL solo lectura | `ADMIN`; consumos de MYL por funciones del servidor |
| Empleados, turnos, tarifas, canales | `ADMIN` (cada empleado lee su propio perfil y sus turnos) | Solo `ADMIN` |
| Historial de estados | Mismos permisos de lectura que el registro de origen | Solo el sistema (nunca directo) |

### 7.3 Disponibilidad pública

La disponibilidad y los precios se exponen mediante una **función o endpoint** que devuelve solo tipos de habitación, cantidad disponible y precio. **Nunca** se da lectura pública directa a la tabla de reservas.

### 7.4 Almacenamiento de archivos

| Contenedor | Contenido | Acceso |
|---|---|---|
| Público | Fotos del hotel, habitaciones, menú y amenidades | Lectura pública; escritura solo `ADMIN` |
| Privado: documentos | Fotos de documentos de identidad | `ADMIN`, `RECEPCION` y el huésped dueño |
| Privado: operación | Fotos de incidencias y objetos olvidados | `ADMIN`, `RECEPCION`, MYL |

---

## 8. Ajustes aplicados a las historias de usuario

| Historia | Ajuste |
|---|---|
| HU-REC-13 | Nuevo criterio: Recepción puede consultar la incidencia de una habitación fuera de servicio (solo lectura) |
| HU-MYL-04 | (Revisión de consistencia) MYL ve en solo lectura las incidencias abiertas de la habitación consultada; nota ⁷ ajustada |
| HU-ADM-20 | (Revisión de consistencia) El Admin reasigna solicitudes y limpiezas en curso |
