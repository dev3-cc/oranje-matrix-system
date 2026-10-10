---
tags:
  - arquitectura
  - rol/manager-reclutamiento
aliases:
  - Arquitectura Manager de Reclutamiento
  - Wireframe Manager de Reclutamiento
---

# Arquitectura — Manager de Reclutamiento

Wireframe de la plataforma Oranje para el rol [[Manager de Reclutamiento]]. Define el flujo de entrada, header global, sidebar de módulos y el detalle de cada módulo al que tiene acceso.

> [!info]
> El Manager **no distribuye requisiciones en el flujo normal** (modelo Self-Pick aprobado). Las requisiciones llegan al sistema y las Reclutadoras y Líderes de Grupo las toman libremente según su capacidad. El Manager solo interviene en **casos especiales** (escalamientos, ausencias, requisiciones VIP, balanceo entre grupos, corrección).

> [!important]
> **Funciones principales del Manager:**
> 1. Gestionar el equipo (dar de alta Líderes de Grupo y Reclutadoras).
> 2. Resolver problemas y escalamientos.
> 3. Supervisar a los Líderes de Grupo.
> 4. Consultar y agregar a Blacklist (remover es exclusivo del Administrador; las disputas las resuelve el Inspector de zona).
> 5. Ver métricas globales del módulo Reclutamiento.
> 6. Tomar requisiciones excepcionalmente (caso especial).

## N0 — Inicio

```
PLATAFORMA ORANGE
        │
        ▼
LOGIN / AUTORIZACIÓN
        │
        ▼
ROL IDENTIFICADO / MANAGER DE RECLUTAMIENTO
        │
        ▼
MÓDULOS / SIDEBAR
```

---

## HEADER (presente en toda la app)

### 👤 PERFIL DEL USUARIO (dropdown)

**Mi información**
- Nombre completo + foto
- Correo + teléfono
- Rol: Manager de Reclutamiento

**Mi equipo**
- Líderes de Grupo a cargo (cantidad)
- Reclutadoras totales (cantidad)
- Zonas cubiertas

**Mis métricas globales**
- Cobertura global del módulo
- Requisiciones pendientes (sin tomar)
- Casos escalados pendientes
- Indicador de Calidad del módulo

🚪 Cerrar sesión

### 🔍 BUSCADOR (global)

- Requisiciones por ID
- Colaboradores por nombre / documento / teléfono
- Hoteles por nombre
- Vetados en Blacklist
- Reclutadoras y Líderes del módulo
- Casos de incidencias / disputas

### 🔔 NOTIFICACIONES (campanita con badge)

- 🚨 Caso escalado por Líder o Inspector
- 🔴 Requisición en urgencia Red sin tomar (>X horas)
- ⚫ Solicitud de aprobación de Blacklist
- 📊 Reporte recibido de un Líder de Grupo
- 🛡️ Indicador de Calidad bajó
- 💼 Requisición VIP autorizada

---

## N1 — SIDEBAR (Módulos del Manager)

```
SIDEBAR
   ├─ DASHBOARD
   ├─ RECLUTAMIENTO            (Pool + Entrevistas global del depto + apoyo operativo)
   ├─ REQUISICIÓN              (vista global + intervención excepcional + semáforos)
   ├─ BLACKLIST                (consulta global + agregar)
   ├─ MI EQUIPO                ← exclusivo (Líderes + Reclutadoras: gestión + supervisión)
   ├─ INCIDENCIAS              ← exclusivo (recibe escalamientos)
   └─ REPORTES                 (recibe de Líderes + genera globales)
```

---

## 📊 Módulo DASHBOARD

- KPIs globales del módulo
- Requisiciones críticas (sin tomar mucho tiempo)
- Top performers / Líderes con baja cobertura
- Casos escalados pendientes
- Indicador de Calidad

---

## 🧑‍🤝‍🧑 Módulo RECLUTAMIENTO (vista global del depto)

### Pool de Colaboradores
- Acceso completo a todo el Pool del depto (mismas pestañas/filtros que Reclutadora y Líder).
- Filtros adicionales del Manager: por Líder de Grupo, por Reclutadora responsable.

### 📝 Entrevistas (sub-vista — vista global con trazabilidad de "quién hizo qué")

> [!info]
> El Manager **rara vez recluta él mismo** (caso excepcional). Su uso principal de esta sub-vista es **auditar y supervisar todo el depto**: ver el historial completo de reclutamientos, identificar Reclutadoras o Líderes con candidatos atascados, detectar patrones (alto abandono en cierta zona, validaciones lentas, etc.).

**Toggle superior**
- 🌐 **Histórico Global del Depto** *(default)* — TODAS las entrevistas hechas por cualquier Reclutadora o Líder del depto.
- 👤 **Mis Entrevistas** — solo las que el Manager hizo personalmente (caso especial — RF-EXC-01 cuando interviene directamente).

**Pestañas internas (estado del candidato)**
- 🟠 **Pendientes de App** — Fase 1 hecha, esperando que el colaborador complete Fase 2.
- 🟡 **Pendientes de Validar** — el colaborador completó Fase 2 + Fase 3, esperando validación.
- ⚪ **Borradores** — entrevistas iniciadas pero no guardadas.
- ⚫ **Abandonados** — candidatos con >X días sin completar la app.
- ✅ **Validados** — histórico de los que ya pasaron al Pool.

**Filtros (transversales — más amplios que Líder/Reclutadora)**
- **Líder de Grupo** — filtra por todo el grupo de un Líder específico.
- **Reclutadora** — filtra por quién hizo la entrevista.
- **Zona** ([[Core/Catálogos/Zonas|Zonas]])
- **Posición** ([[Core/Catálogos/Posiciones|Posiciones]])
- Modalidad prevista
- Días en estado (slider)
- Fecha de entrevista (rango)
- Origen (referido / aplicación directa / reclutamiento activo)
- Buscar por nombre / documento / teléfono

**Detalle del candidato (al click)**
- **Cabecera:** foto + nombre + teléfono + posición prevista + timeline visual `Fase 1 ✅ → Fase 2 🟡 → Fase 3 ⚪ → Validación`.
- **Trazabilidad de responsables** (visible siempre):
  - 👤 **Reclutadora responsable** (quién hizo la Fase 1).
  - 🧑‍🏫 **Líder de Grupo** al que pertenece la Reclutadora.
  - 👁️ **Validador** (quién validó / rechazó la Fase 2, si ya pasó).
- **Días desde entrevista** con indicador de color (🟢 <3 · 🟡 3-7 · 🔴 >7).
- **Tabs internos:**
  1. **Datos Fase 1** — solo lectura (el Manager no edita rutinariamente — solo en intervención excepcional).
  2. **Datos Fase 2** — completados por el colaborador en la app. Solo lectura.
  3. **Datos Fase 3** — datos de emergencia. Solo lectura.
  4. **Histórico de comunicación** — cuándo se envió el link, recordatorios, abrió la app.
  5. **Documentos** — cédula, fotos, comprobantes.
  6. **Bitácora del expediente** — todas las acciones (quién, cuándo, qué hizo).

**Acciones (perfil supervisor)**
- 👁️ **Ver detalle** del candidato.
- 📊 **Ver desempeño del responsable** (Reclutadora o Líder — lleva a métricas individuales globales, RF-23).
- 💬 **Comentar al expediente** (visible para Reclutadora y Líder responsables).
- 🚨 **Solicitar acción** a la Reclutadora o al Líder (ej. "reenvía el link, lleva 8 días").
- 📤 **Reasignar candidato a otra Reclutadora / otro grupo** (excepcional — Reclutadora o Líder no disponible, balanceo).
- ✅ **Validar alta en intervención** (excepcional — RF-EXC, queda en log auditable).
- ❌ **Rechazar alta** con motivo (excepcional).
- 🚫 **Marcar como abandonado** (excepcional — normalmente lo hace la Reclutadora o Líder).
- 📥 **Exportar** lista filtrada (CSV / PDF) para seguimiento y supervisión del Manager.

**KPIs visibles en cabecera (vista global)**
- Total candidatos en proceso (depto).
- Tasa de conversión Fase 1 → Pool (mes en curso vs. mes anterior).
- Promedio de días por etapa.
- Top 3 Reclutadoras por candidatos validados.
- Bottom 3 Reclutadoras con candidatos atascados (>7 días).

### + Nuevo Colaborador (modal — apoyo operativo excepcional)
Mismo formulario que Reclutadora / Líder. El Manager solo lo usa cuando interviene directamente (queda registrado como excepción).

> [!important]
> La validación / rechazo / edición rutinaria la hace la Reclutadora que originó la entrevista (o su Líder de Grupo en su ausencia). El Manager interviene solo como **auditor o ejecutor excepcional**, y cualquier acción suya queda en log auditable (regla RR-12).

---

## 📋 Módulo REQUISICIONES (vista global, intervención excepcional)

- Lista por estado (Autorizadas / En proceso / Cubiertas / Parciales)
- Filtros por urgencia, posición, hotel, zona, tiempo en cola

> [!info]
> **Modelo colaborativo (RR-15).** Una requisición puede tener **varios reclutadores participantes** trabajándola a la vez (no hay dueño único). Tomar una requisición ya tomada NO la transfiere ni la bloquea: el reclutador se **une como participante adicional** y el avance de cobertura es **compartido**. El Manager ve a todos los reclutadores activos de cada requisición y puede agregar uno sin desplazar a los existentes.

- Acciones excepcionales:
  - 🎯 Tomar requisición personalmente (caso especial — se registra como participante; otros pueden unirse)
  - 👤 Asignar manualmente (VIP, balanceo, ausencia) — con justificación
  - ➕ Agregar reclutador a requisición (lo suma como participante sin desplazar a los existentes — modelo colaborativo)
  - Reasignar a otra Reclutadora (transferencia) o mantener a la original como participante de apoyo
  - 📜 Ver historial de la requisición (timeline cronológico: quién tomó / se unió / salió, quién asignó/desasignó cada colaborador, con fecha y autor — RR-16)
  - Forzar cambio de semáforo (con log)

---

## ⚫ Módulo BLACKLIST (consulta global + agregar)

- 👁️ Consultar Blacklist completa del departamento
- ✅ Agregar a Blacklist (con motivo y evidencia — igual que cualquier rol de Reclutamiento)

> [!note]
> El Manager **agrega** a Blacklist y **también puede levantar un veto**, igual que el Administrador (`ROL-ADM-01`); el colaborador regresa a Blanco para ser revalidado *(cambiado el 2026-08-13; ampliado el 2026-10-10 por decisión de Hugo, cuando era exclusivo del Administrador)*. Las disputas (estado Rojo del colaborador) las resuelve el **Inspector de zona**, no el Manager.

---

## 👥 Módulo MI EQUIPO (exclusivo del Manager)

Sub-vistas:
- 👥 Líderes de Grupo (lista + detalle + métricas + grupo anidado)
- 🧑 Reclutadoras (lista + detalle + Líder al que pertenecen)

Acciones de gestión:
- ➕ Dar de alta Líder / Reclutadora
- 📝 Editar usuario
- 🔁 Mover Reclutadora a otro Líder
- ⏸️ Marcar inactivo / vacaciones / baja

Acciones de supervisión:
- 📊 Ver desempeño detallado
- 📝 Solicitar reporte
- 🚨 Escalar a Dirección

---

## ⚠️ Módulo INCIDENCIAS / DISPUTAS

- Casos abiertos / resueltos
- ✅ Resolver caso (decisión final con motivo)
- 🚨 Escalar a Dirección
- Cerrar caso

---

## 📈 Módulo REPORTES

- Reportes recibidos de Líderes
- Generar reporte global
- Exportar (CSV / PDF / Excel)
- Programar envío recurrente

---

## Diferencias clave vs Líder de Grupo

| Aspecto | Líder de Grupo | Manager de Reclutamiento |
|---|---|---|
| Toma requisiciones (Self-Pick) | Sí | Sí pero excepcional |
| Reclutamiento (Pool + Asignación) | Acceso operativo | Acceso completo |
| Blacklist | Consultar + Agregar | Consultar global + Agregar (igual que cualquier rol) |
| Mi Grupo / Mi Equipo | Ve Reclutadoras del grupo | Ve Líderes + Reclutadoras + gestión |
| Resolver disputas (Inspector) | ❌ | ❌ (lo resuelve el Inspector de zona) |
| Reportes | Genera y envía | Recibe + genera globales |

---

## Relacionado

- [[Manager de Reclutamiento]] (definición del rol)
- [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo de Reclutadoras]]
- [[Reclutadora]]
- [[Reclutamiento/Reclutamiento|Reclutamiento]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[QA/QA|QA]]
