---
tags:
  - arquitectura
  - modulo/reclutamiento
aliases:
  - Arquitectura Reclutadora
  - Wireframe Reclutadora
  - Arquitectura Reclutador
---

# Arquitectura — Reclutadora

Wireframe de la plataforma Oranje para el rol [[Reclutadora]]. Define el flujo de entrada, header global, sidebar de módulos y el detalle de cada módulo al que tiene acceso.

> [!info]
> La Reclutadora es el **rol operativo base** del módulo de [[Reclutamiento/Reclutamiento|Reclutamiento]]. Ejecuta todo el ciclo de reclutamiento, validación y asignación de colaboradores a hoteles.

> [!important]
> **Modelo Self-Pick colaborativo (RR-15):** las requisiciones llegan al sistema y la Reclutadora las **toma libremente** desde la bandeja de Autorizadas. Nadie le asigna requisiciones — ella decide qué toma y cuándo. Tomar una requisición **NO bloquea a las demás**: una misma requisición puede tener **varios reclutadores participantes** trabajándola a la vez. Tomar una ya tomada significa **unirse** como reclutador participante adicional; nadie pierde la requisición.

## N0 — Inicio

```
PLATAFORMA ORANGE
        │
        ▼
LOGIN / AUTORIZACIÓN
        │
        ▼
ROL IDENTIFICADO / RECLUTADORA
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
- Rol: Reclutadora

**Mi Líder de Grupo**
- Nombre del Líder al que reporta
- Grupo al que pertenece

**Mi zona asignada**
- Ej. "Zona Centro"

**Mis métricas (mes en curso)**
- Requisiciones cubiertas
- Tasa de cobertura personal
- Candidatos aprobados
- Tiempo promedio de asignación
- Asignaciones temporales activas

**Configuración**
- Cambiar contraseña
- Preferencias de notificación

🚪 Cerrar sesión

### 🔍 BUSCADOR (global)

**Qué se puede buscar**
- Requisiciones por ID o número
- Colaboradores por nombre / documento / teléfono
- Hoteles por nombre
- Vetados en [[Core/Módulos/Blacklist|Blacklist]]

**Cómo funciona**
- Resultados en vivo agrupados por tipo
- Cada resultado lleva al detalle correspondiente
- Atajo: `/` o `Ctrl+K`

**Filtros rápidos**
- Tipo (req · colaborador · hotel · blacklist)
- Búsqueda reciente (últimos 5)

### 🔔 NOTIFICACIONES (campanita con badge)

**Por tipo de evento**
- 📋 Nueva requisición disponible para tomar
- 👤 Candidato completó alta en App → listo para validar
- 🔴 Una de mis requisiciones entró a urgencia Red (<72h)
- ⚫ Blacklist actualizado
- 🟢 Mi requisición quedó cubierta al 100%
- 🩷 Colaborador asignado pasó a Stand-by (Rosa) por el hotel
- 🔴 Colaborador asignado fue reportado (Rojo) por el hotel
- ⬜ Colaborador asignado tuvo accidente laboral (Gris)
- 📊 Mi Líder de Grupo solicitó info para reporte

**Estados**
- 🟠 Sin leer (cuenta en el badge)
- 🟢 Leída

**Acciones**
- Click → te lleva al módulo relacionado
- Marcar como leída
- Marcar todas como leídas

---

## N1 — SIDEBAR (Módulos de la Reclutadora)

```
SIDEBAR
   ├─ DASHBOARD
   ├─ RECLUTAMIENTO   (Pool + Entrevistas + Nuevo Colaborador)
   ├─ REQUISICIÓN     (Cola de Autorizadas + Mis Tomadas — Self-Pick)
   └─ BLACKLIST       (consulta + agregar)
```

---

## 📊 Módulo DASHBOARD

### KPIs personales
- Requisiciones tomadas (mes)
- Requisiciones abiertas en proceso
- Requisiciones urgentes (Red) bajo mi responsabilidad
- Candidatos pendientes de validar
- Alertas críticas

### Vistas resumen
- **Pool por posición:**
  - Housekeeper (8 disponibles)
  - Hoseman (3 disponibles)
  - Chef (1 disponible)
- **Mis Requisiciones (resumen):**
  - Req #001 · Housekeeper · 🔴 Red (en proceso)
  - Req #002 · Chef · 🟡 Yellow (en proceso)
  - Req #003 · Hoseman · 🟢 Green (cubierta)
  - [Ver todas →] navega a Requisiciones
- **Bandeja de disponibles para tomar:**
  - Listado corto de las requisiciones autorizadas sin tomar (orden por urgencia)

### Bandeja Acción
- Últimas actualizaciones de Blacklist
- Candidatos que completaron App → Validar

### Acciones rápidas
- Nuevo Candidato
- Buscar en Pool
- Consultar Blacklist
- Tomar requisición (lleva a la bandeja de disponibles)

---

## 🧑‍🤝‍🧑 Módulo RECLUTAMIENTO

### Pool de Colaboradores

**Lista** (estados del [[Semáforo del Colaborador]])
- ⚪ Blanco — Pre-asignación
- 🟢 Verde manzana — Día 1-2 onboarding
- 🔵 Azul claro — Día 3+ entrega uniforme
- 🟠 Naranja — Fijo
- 🟢 Verde fuerte — Disponible
- 🟡 Amarillo — Disponible voluntario
- 🟤 Café — Asignación temporal
- 🩷 Rosa — Stand-by
- 🟣 Morado — No regresó
- 🔴 Rojo — Reportado
- ⚫ Negro — Blacklist
- ⬜ Gris — Accidentado

**Filtros**
- Posición ([[Core/Catálogos/Posiciones|Posiciones]]: Housekeeper, Hoseman, Chef, Laundry)
- Zona ([[Core/Catálogos/Zonas|Zonas]]: Centro, Sur, Este, Oeste, Noroeste, Sureste)
- Modalidad ([[Core/Catálogos/Modalidades de Contratación|Modalidades]]: Tiempo completo, Medio tiempo, Temporal, Según solicitud)
- Nivel de inglés ([[Core/Catálogos/Niveles de Inglés|Inglés]]: Básico, Intermedio, Avanzado, Conversacional)
- Buscar por nombre / documento / teléfono

**Detalle**
- Datos personales: nombre, documento, teléfono, domicilio
- Datos laborales: posición, modalidad, inglés, experiencia
- Semáforo actual del colaborador
- Historial: hoteles donde ha trabajado
- Documentos: SSN, ITIN, PDFs subidos

**Acciones**
- Asignar a requisición (Verde fuerte / Amarillo → Café para asignación temporal)
- Editar colaborador (Pendiente)
- Validar alta en App (cuando el colaborador completó su auto-registro)
- Habilitar accesos a paneles
- Ver historial de asignaciones
- Consultar si está en Blacklist

### 📝 Entrevistas (sub-vista — candidatos en proceso)

> [!info]
> Esta sub-vista da seguimiento a los candidatos que **ya pasaron por entrevista (Fase 1)** pero que aún **no están en el Pool**. Resuelve la pregunta que antes solo respondía una notificación: *¿quiénes están en cola? ¿quién lleva días sin completar la app? ¿cuántos están listos para validar?*.

**Pestañas internas (estado del candidato)**
- 🟠 **Pendientes de App** — Fase 1 hecha, esperando que el colaborador descargue la app y complete Fase 2.
- 🟡 **Pendientes de Validar** — el colaborador completó Fase 2 + Fase 3 en la app y está esperando que la Reclutadora valide (RF-08).
- ⚪ **Borradores** — entrevistas iniciadas pero no guardadas (timeout / cierre accidental).
- ⚫ **Abandonados** — candidatos con >X días sin completar la app (umbral configurable).
- ✅ **Validados (últimos 30 días)** — histórico reciente de los que ya pasaron al Pool.

**Filtros (transversales a todas las pestañas)**
- Posición ([[Core/Catálogos/Posiciones|Posiciones]]: Housekeeper, Hoseman, Chef, Laundry)
- Zona ([[Core/Catálogos/Zonas|Zonas]]: Centro, Sur, Este, Oeste, Noroeste, Sureste)
- Modalidad prevista ([[Core/Catálogos/Modalidades de Contratación|Modalidades]])
- Días en estado (slider: 1-3 / 4-7 / >7 — los de >7 con alerta visual)
- Fecha de entrevista (rango)
- Origen (referido / aplicación directa / reclutamiento activo)
- Buscar por nombre / documento / teléfono

**Detalle del candidato (al click)**
- **Cabecera:** foto + nombre + teléfono + posición prevista + timeline visual `Fase 1 ✅ → Fase 2 🟡 → Fase 3 ⚪ → Validación`.
- **Días desde entrevista** con indicador de color (🟢 <3 · 🟡 3-7 · 🔴 >7).
- **Tabs internos:**
  1. **Datos Fase 1** — capturados por la Reclutadora en la entrevista (editable).
  2. **Datos Fase 2** — completados por el colaborador en la app (posición, inglés, experiencia, transporte, modalidad; SSN/ITIN y documento opcionales). Solo lectura. Marca lo que falta.
  3. **Datos Fase 3** — datos de emergencia (contacto, tipo de sangre, alergias). Solo lectura.
  4. **Histórico de comunicación** — cuándo se envió el link, recordatorios, abrió la app, etc.
  5. **Documentos** — cédula, fotos subidas, comprobantes.

**Acciones**
- ✏️ **Editar datos Fase 1** (corrección de captura).
- 📩 **Reenviar link de App** (Pendientes de App).
- 🔔 **Enviar recordatorio** (Pendientes de App con >3 días).
- ✅ **Validar alta** (Pendientes de Validar — dispara RF-08, mueve al Pool con estado Verde fuerte).
- ❌ **Rechazar alta** (Pendientes de Validar — con motivo obligatorio).
- 🚫 **Marcar como abandonado** (Pendientes de App con >X días sin actividad).
- 📞 **Llamar / Contactar** (cualquier pestaña).
- 🗑️ **Eliminar borrador** (solo Borradores).

### + Nuevo Colaborador (modal form)
**Datos capturados en entrevista inicial (Fase 1):**
- Nombre completo
- Fecha de nacimiento (la **edad se calcula automáticamente** desde la fecha; no se captura)
- Género
- Teléfono
- Correo
- Domicilio

> [!info]
> El alta de Fase 1 NO solicita documento / ID ni subir cédula. El colaborador completa documentos en la app (Fase 2).

> [!info]
> Tras crear el candidato, se le envía link para completar **Fase 2** (alta en App: posición, inglés, experiencia, transporte, modalidad — obligatorios; SSN, ITIN y documento de SSN/ITIN opcionales; sin SSN ni ITIN → retención 16% reembolsable activada automáticamente, ver [[Deducciones]]) y **Fase 3** (datos de emergencia: contacto, tipo de sangre, alergias). El candidato queda visible en la sub-vista **Entrevistas → Pendientes de App** mientras completa el proceso, y migra a **Pendientes de Validar** cuando termina.

---

## 📋 Módulo REQUISICIONES

### Sub-vistas

**🟢 Bandeja de Autorizadas (disponibles para tomar)**
- Listado de todas las requisiciones que el [[Hotel/Manager de Área|Manager de Área]] aprobó.
- Cualquier Reclutadora o Líder puede **tomar** la que quiera (Self-Pick colaborativo, RR-15). Tomar **NO bloquea a las demás**.
- Una requisición ya tomada sigue visible aquí con la etiqueta **"Compartida · N reclutadores"**: tomarla significa **Unirme** como reclutador participante adicional.
- Ordenadas por urgencia (Red primero) y antigüedad en cola.

**🟡 Mis Requisiciones (las que tomé o en las que participo)**
- Las requisiciones que **yo tomé** O en las que **participo** junto a otros reclutadores (modelo colaborativo — no hay dueño único).
- Las compartidas muestran la etiqueta **"Compartida · N reclutadores"**.
- Por sub-estado:
  - 🟡 En proceso (uno o varios reclutadores asignando colaboradores)
  - 🔵 Cubiertas (100%)
  - 🔴 Parciales (cerradas con faltantes)
  - 📂 Toda

### Filtro
- Urgencia (🔴 🟡 🟢) — del [[Semáforo de Urgencia de Requisición]]
- Posición
- Hotel
- Zona
- Estado
- Buscar por ID

### Detalle
- Cabecera (hotel, fechas, urgencia)
- **Reclutadores activos en esta requisición** (RF-40) — lista de todos los reclutadores participantes que la están trabajando ahora mismo (rol + nombre), con etiqueta "Compartida · N reclutadores". El avance de cobertura es compartido entre todos.
- Posiciones solicitadas (con [[Semáforo de Posiciones de la Requisición]])
- Schedule del hotel (contexto)
- Colaboradores ya asignados (los asignados por **cualquier** reclutador participante, con quién lo asignó)

### Historial de la requisición (RF-41)
- Timeline cronológico **inmutable** con el **actor** de cada evento (rol + nombre) y timestamp.
- Registra: quién la tomó / se unió, quién salió, quién asignó/desasignó qué colaborador a qué posición, quién la cerró.
- Visible para todos los reclutadores participantes, el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] y el [[Hotel/Manager de Área|Manager de Área]].

### Acciones
- **🎯 Tomar requisición** *(desde bandeja de Autorizadas — pasa a Mis Requisiciones)*
- **🤝 Unirme a la requisición** *(cuando ya hay otros reclutadores participantes — me uno como reclutador adicional sin desplazar a nadie, RF-39)*
- **👤 Asignar colaborador** (abre Pool filtrado por posición/zona/inglés; el lock es a nivel de posición/slot: dos reclutadores no asignan el mismo colaborador a la misma posición)
- **🔄 Asignación temporal** (transición Verde fuerte/Amarillo → Café)
- **📜 Ver historial** de la requisición (timeline cronológico con actor — RF-41)
- Marcar como cubierta (cuando todas las posiciones están al 100%)
- Marcar como parcial (cierre con faltantes)
- Reportar problema (escala al [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]])
- Salir de la requisición (me retira solo a mí; sigue **En proceso** si quedan otros reclutadores; no resetea lo que otros asignaron; vuelve a **Autorizada** solo cuando sale el **último** reclutador)

> [!important]
> Diferencia clave: la Reclutadora **toma** requisiciones libremente y **trabaja** ella misma. NO distribuye a nadie (no tiene grupo a su cargo).

---

> [!info]
> **Schedule del Hotel** no es un módulo de la Reclutadora — es una vista contextual del módulo Hotel que se consulta durante el proceso de asignar (dentro del módulo Reclutamiento). Permite ver horarios y posiciones vacantes antes de asignar un colaborador.

---

## ⚫ Módulo BLACKLIST

### Lista
- Todos los vetados
- Por motivo (3 inasistencias, disputa, falta grave)

### Filtros
- Buscar por nombre / documento
- Por motivo
- Por fecha de ingreso a blacklist
- Por zona

### Detalle
- Info del colaborador
- Motivo del veto
- Fecha de ingreso
- Quién lo propuso

> [!warning]
> La Reclutadora puede **levantar un veto** de Blacklist, con motivo obligatorio (ampliado el 2026-10-10; antes era exclusivo del Administrador). El colaborador regresa a Blanco.
> **Consulta obligatoria antes de cada reclutamiento** (regla del [[Flujo de Reclutamiento]]) — para evitar volver a reclutar a alguien vetado.

---

## Flujo operativo de la Reclutadora

```
1. Login → Dashboard
   │
   ▼
2. Veo bandeja de Requisiciones Autorizadas (sin tomar)
   │
   ▼
3. Tomo una requisición que pueda cubrir
   │
   ▼
4. Reviso Schedule del hotel para entender la semana
   │
   ▼
5. Voy al Pool, busco candidatos, consulto Blacklist
   │
   ▼
6a. Si el candidato no existe:
      → Creo nuevo Colaborador (Fase 1: entrevista)
      → Le envío link para Fase 2 (alta en App)
      → El candidato aparece en Entrevistas → Pendientes de App
      → Cuando completa, migra a Pendientes de Validar y recibo notificación
      → Voy a Entrevistas → Pendientes de Validar, valido y habilito accesos
   │
6b. Si el candidato existe en Pool (Verde fuerte / Amarillo):
      → Lo asigno directamente a la posición
   │
   ▼
7. Repito hasta cubrir todas las posiciones
   │
   ▼
8. Marco la requisición como Cubierta (100%) o Parcial
```

### Eventos posteriores que vigilo
- **Día 1:** [[Inspector]] verifica llegada del colaborador (Verde fuerte → Verde manzana).
- **Día 3:** Inspector entrega uniforme (Verde manzana → Azul claro).
- **Día 7:** Sistema marca como Naranja (Fijo) automáticamente.
- **Reportes:** si el hotel reporta al colaborador (Rojo) o si tiene accidente laboral (Gris), recibo notificación.

---

## Diferencias clave vs Líder de Grupo y Manager

| Aspecto | Reclutadora | Líder de Grupo | Manager |
|---|---|---|---|
| Toma requisiciones | ✅ libremente | ✅ libremente | ✅ excepcional |
| Distribuye a otros | ❌ | ❌ (modelo self-pick) | ❌ |
| Crear colaborador | ✅ | ✅ | ✅ |
| Validar alta App | ✅ | ✅ | ✅ |
| Asignar al Schedule | ✅ | ✅ | ✅ |
| Mi Grupo / Mis Líderes | ❌ | ✅ Mis Reclutadoras | ✅ Mis Líderes |
| Reportes formales | ❌ | ✅ | ✅ |
| Aprobar/remover Blacklist | ❌ | ❌ | ✅ exclusivo |
| Gestión de equipo (alta de roles) | ❌ | ❌ | ✅ exclusivo |

---

## Diagrama ASCII general del wireframe

```
╔══════════════════════════════════════════════════════════════╗
║                       PLATAFORMA ORANGE                       ║
║                              │                                ║
║                              ▼                                ║
║                   LOGIN / AUTORIZACIÓN                        ║
║                              │                                ║
║                              ▼                                ║
║                    ROL: RECLUTADORA                           ║
╚══════════════════════════════════════════════════════════════╝
                                │
                                ▼
┌──────────────────────────────────────────────────────────────┐
│ HEADER:  [👤 Perfil]   [🔍 Buscador]   [🔔 Notificaciones]   │
└──────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────┬───────────────────────────────────────────────┐
│  SIDEBAR     │              ÁREA DE TRABAJO                  │
│              │                                               │
│ 📊 Dashboard │  KPIs personales · Pool · Mis req · Bandeja  │
│ 🧑 Reclutam. │  Pool · Entrevistas · Nuevo Colaborador (F1)  │
│ 📋 Requisic. │  Bandeja Autorizadas · Mis tomadas · Asignar  │
│ ⚫ Blacklist │  Consulta · Motivos · Histórico               │
└──────────────┴───────────────────────────────────────────────┘
```

---

## Relacionado

- [[Reclutadora]] (definición del rol)
- [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo de Reclutadoras]]
- [[Manager de Reclutamiento]]
- [[Reclutamiento/Reclutamiento|Reclutamiento]]
- [[Flujo de Reclutamiento]]
- [[Core/Módulos/Requisicion/Requisición|Requisición]]
- [[Core/Módulos/Pool de Colaboradores|Pool de Colaboradores]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Semáforo del Colaborador]]
- [[Semáforo de Requisición]]
- [[Inspector]] (verifica llegada y uniforme de los colaboradores que asigno)
- [[Arquitectura Líder de Grupo|Arquitectura del Líder de Grupo]]
- [[Arquitectura Manager de Reclutamiento|Arquitectura del Manager]]
