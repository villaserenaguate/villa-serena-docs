# 02 — Definición de Roles

> **Proyecto:** Property Management System (PMS) para Hoteles Boutique — Hotel ficticio "Villa Serena"
> **Estado:** ✅ Aprobado por el equipo
> **Fecha de aprobación:** 24 de septiembre de 2026
> **Documento anterior:** 01 — Alcance del Proyecto
> **Siguiente documento:** 03 — Plantilla de Historias de Usuario

---

## Índice

1. [Propósito del documento](#1-propósito-del-documento)
2. [Resumen de roles](#2-resumen-de-roles)
3. [Detalle por rol](#3-detalle-por-rol)
4. [Actores no humanos](#4-actores-no-humanos)
5. [Reglas generales de roles](#5-reglas-generales-de-roles)
6. [Problemas del reporte que resuelve este documento](#6-problemas-del-reporte-que-resuelve-este-documento)
7. [Cambios que genera en la documentación](#7-cambios-que-genera-en-la-documentación)

---

## 1. Propósito del documento

Define **quién usa el sistema**, **desde dónde**, **cómo entra** y **de qué es responsable**. Es la base para:

- Las historias de usuario (puntos 4, 5 y 6).
- La matriz de permisos y la seguridad en la base de datos (punto 9).
- Los casos de uso (punto 12).
- Las pantallas que ve cada usuario en el frontend y en la app.

El detalle de **qué acción exacta** puede hacer cada rol se define en el documento 09 — Matriz de Permisos. Este documento define las responsabilidades generales.

---

## 2. Resumen de roles

| Rol | Código | Dónde trabaja | Cómo entra | Quién crea la cuenta |
|---|---|---|---|---|
| Administrador | `ADMIN` | Web privada | Correo + contraseña | Cuenta inicial creada al instalar el sistema |
| Recepcionista | `RECEPCION` | Web privada | Correo + contraseña | Administrador |
| Room Service | `ROOM_SERVICE` | Web privada | Correo + contraseña | Administrador |
| Mantenimiento/Limpieza | `MANTENIMIENTO_LIMPIEZA` | Web privada | Correo + contraseña | Administrador |
| Cliente/Huésped | `HUESPED` | Web pública + app Android | Sin sesión (Cliente) o correo + código OTP (Huésped) | Se crea automáticamente en su primer acceso |

> El **código** del rol es el valor que se guardará en la base de datos y se usará en el código fuente. Debe escribirse exactamente igual en todo el proyecto.

---

## 3. Detalle por rol

### 3.1 Administrador (`ADMIN`)

**Descripción:** responsable de la configuración y supervisión del hotel. Tiene acceso a todo el sistema.

**Responsabilidades:**

| Área | Responsabilidad | Alcance |
|---|---|---|
| Personal | Crear, editar y desactivar empleados; asignar rol y área | ALC-ADM-01, 02 |
| Turnos | Definir turnos y asignarlos a empleados | ALC-ADM-03 |
| Inventario | Productos, stock, movimientos, stock mínimo; atender reportes de faltantes | ALC-ADM-04, 05 |
| Catálogos | Habitaciones, tipos de habitación, menú de Room Service, amenidades | ALC-ADM-06, 07, 08 |
| Tarifas | Precio base, temporadas y ajuste de fin de semana | ALC-ADM-09 |
| Indicadores | Ocupación, ingresos, reservas por canal | ALC-ADM-10 |
| Mantenimiento | **Encargado:** revisar incidencias, asignar órdenes, cerrarlas y cancelarlas | ALC-ADM-11 |
| Recepción | Puede realizar todas las operaciones de Recepción (cobertura y demostraciones) | ALC-REC-* |

**Decisiones:**

- El Administrador **es el "encargado"** mencionado en RN-MAN-003 y RF-MAN-004. No existe un rol "encargado" separado.
- El Administrador **puede operar como Recepcionista** sin cambiar de cuenta.

### 3.2 Recepcionista (`RECEPCION`)

**Descripción:** atiende al huésped en recepción y gestiona el ciclo completo de la reserva.

**Responsabilidades:**

| Área | Responsabilidad | Alcance |
|---|---|---|
| Huéspedes | Registrar huéspedes principales y adicionales; consultar historial | ALC-REC-01, 08 |
| Reservas | Crear, modificar, cancelar y buscar reservas; consultar disponibilidad | ALC-REC-02, 03, 08 |
| Habitaciones | Asignar habitación; consultar y actualizar su estado | ALC-REC-04, 10 |
| Estadía | Check-in y check-out | ALC-REC-05 |
| Cuenta | Registrar pagos y servicios adicionales; consultar cuenta; generar comprobante | ALC-REC-06, 07 |
| Operación diaria | Vista del día y calendario Gantt | ALC-REC-09, 12 |
| Solicitudes | Registrar solicitudes de huéspedes y enviarlas al área responsable; reportar daños | ALC-REC-11 |

**No puede:** gestionar personal, turnos o inventario; modificar tarifas o catálogos; asignar, cerrar o cancelar órdenes de mantenimiento.

### 3.3 Room Service (`ROOM_SERVICE`)

**Descripción:** prepara y entrega los pedidos de comida a las habitaciones. Un solo rol cubre cocina y entrega.

**Responsabilidades:**

| Área | Responsabilidad | Alcance |
|---|---|---|
| Pedidos | Ver la cola, ver el detalle, actualizar estados, cancelar con motivo | ALC-RS-01, 02, 04, 05 |
| Pedido telefónico | Registrar pedidos hechos por teléfono | ALC-RS-03 |
| Menú | Consultar el menú y marcar ítems como agotados | ALC-RS-06, 07 |
| Historial | Consultar pedidos entregados | ALC-RS-09 |

**No puede:** reactivar ítems agotados (lo hace el Administrador, según HU-RS-07); ver pagos, reservas ni datos personales del huésped más allá del nombre y la habitación.

### 3.4 Mantenimiento/Limpieza (`MANTENIMIENTO_LIMPIEZA`)

**Descripción:** mantiene las habitaciones limpias y en funcionamiento. Es **un solo rol** con un atributo **Área** que asigna el Administrador.

**Atributo Área:**

| Área | Código | Qué ve por defecto |
|---|---|---|
| Limpieza | `LIMPIEZA` | Habitaciones pendientes de limpieza, solicitudes de limpieza y artículos, insumos, objetos olvidados |
| Mantenimiento | `MANTENIMIENTO` | Órdenes de mantenimiento asignadas, repuestos |
| Ambas | `AMBAS` | Todo lo anterior |

**Responsabilidades:**

| Área | Responsabilidad | Alcance |
|---|---|---|
| Limpieza | Habitaciones pendientes y prioritarias; estados de limpieza; finalización | ALC-MYL-01, 02 |
| Solicitudes | Atender solicitudes de limpieza y artículos; confirmar entrega | ALC-MYL-03 |
| Habitación | Consultar información y registrar observaciones | ALC-MYL-04 |
| Insumos | Registrar insumos usados y reportar faltantes | ALC-MYL-05, 06 |
| Reportes | Reportar daños (crea una incidencia) y objetos olvidados | ALC-MYL-07, 08 |
| Mantenimiento | Ver órdenes asignadas, iniciarlas, resolverlas y registrar repuestos | ALC-MYL-09, 10 |
| Historial | Consultar servicios realizados | ALC-MYL-11 |

**Flujo de incidencias:**

1. **Cualquier empleado** de este rol, o Recepción, reporta un daño → se crea una **incidencia**.
2. El **Administrador** revisa la incidencia y la asigna a un empleado con área `MANTENIMIENTO` o `AMBAS` → se convierte en **orden de trabajo**.
3. El empleado asignado la inicia y la resuelve.
4. El **Administrador** la cierra.

**No puede:** asignar, cerrar ni cancelar órdenes; ver reservas, pagos ni datos personales del huésped más allá del número de habitación.

### 3.5 Cliente/Huésped (`HUESPED`)

**Descripción:** persona que reserva y se hospeda en el hotel. Es **un solo rol**, pero lo que puede hacer **depende del estado de su reserva**.

| Situación | Tiene sesión | Qué puede hacer | Dónde |
|---|---|---|---|
| **Cliente** (visitante) | No | Ver habitaciones, consultar disponibilidad, reservar y pagar | Web pública |
| **Huésped con reserva futura** | Sí (OTP) | Consultar su reserva (web y app); cancelarla (solo web pública); check-in anticipado (web y app) | Web pública y app |
| **Huésped en estadía** (después del check-in) | Sí (OTP) | Room service, solicitudes, amenidades, ver cuenta, pagar y check-out | App |
| **Huésped con estadía finalizada** | Sí (OTP) | Ver su historial en solo lectura | App |

**Reglas del rol:**

- Su perfil se crea automáticamente con el correo que registró al reservar. Al entrar por primera vez con el código OTP, el sistema lo vincula con todas sus reservas.
- Los **huéspedes adicionales** de una reserva son **registros** (nombre, documento, nacionalidad), **no usuarios**. Solo el **huésped principal** entra a la app.
- Un huésped solo puede ver **sus propias** reservas, pedidos, solicitudes y cuenta.

---

## 4. Actores no humanos

No son roles de usuario, pero participan en los procesos y se usan en la matriz de permisos y en los casos de uso.

| Actor | Código | Qué hace | Cómo se autentica |
|---|---|---|---|
| Sistema | `SISTEMA` | Tareas automáticas: calcular tarifas, liberar disponibilidad, pasar habitaciones a limpieza después del check-out, enviar correos, cerrar el acceso de estadías finalizadas | Proceso interno del servidor |
| Pasarela de pago (Stripe) | `PASARELA` | Confirma o rechaza pagos mediante webhook | Firma del webhook |
| Canal externo (simulado) | `CANAL` | Envía reservas a la API del Channel Manager | Clave de API por canal |

---

## 5. Reglas generales de roles

| # | Regla |
|---|---|
| R-ROL-01 | **Un empleado tiene un solo rol.** El Administrador ya tiene acceso a todo, por lo que no es necesario combinar roles. |
| R-ROL-02 | **Los empleados no se eliminan, se desactivan.** Un empleado desactivado no puede entrar al sistema, pero su nombre se conserva en el historial de cambios (ALC-TRA-03). |
| R-ROL-03 | **Solo el Administrador crea cuentas de personal.** El personal no puede registrarse por su cuenta. |
| R-ROL-04 | **Todo el personal, excepto el Administrador, trabaja en turnos.** El detalle se define en el documento 08. |
| R-ROL-05 | **El rol Mantenimiento/Limpieza requiere un Área** (`LIMPIEZA`, `MANTENIMIENTO` o `AMBAS`). |
| R-ROL-06 | **El huésped solo accede a su propia información.** |
| R-ROL-07 | **Los permisos se aplican en la base de datos**, no solo en la interfaz. Ocultar un botón no es suficiente. |

---

## 6. Problemas del reporte que resuelve este documento

| Problema del reporte | Cómo se resuelve |
|---|---|
| 3.3 — Mantenimiento/Limpieza documentado como dos roles | Un solo rol con atributo Área |
| 3.3 — El personal "se reporta a sí mismo" | Las incidencias pasan por el Administrador antes de convertirse en orden |
| 3.3 — Rol "encargado" inexistente | El Administrador es el encargado |
| 3.4 — Cliente y Huésped mezclados | Un solo rol; las capacidades dependen del estado de la reserva |
| 3.4 — Sin regla de acceso ni expiración de la app | Acceso por OTP; la estadía finalizada queda en solo lectura |
| 3.1 — Administrador sin responsabilidades definidas | Responsabilidades definidas en la sección 3.1 |

---

## 7. Cambios que genera en la documentación

| Documento | Cambio | Se aplica en |
|---|---|---|
| HU Limpieza + HU Mantenimiento | Se unen en **HU Mantenimiento/Limpieza** | Punto 6 |
| HU Huésped | Se separan funciones de web pública y de app | Punto 6 |
| Reglas de Negocio (RN-MAN-003) | "Encargado" → **Administrador** | Punto 10 |
| Requisitos Funcionales (RF-MAN-004) | "Encargado" → **Administrador** | Punto 11 |
| HU Mantenimiento HU-01 | "Reportadas por el personal de limpieza" → "reportadas por cualquier empleado y asignadas por el Administrador" | Punto 6 |
