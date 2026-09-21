---
tags:
  - arquitectura
  - modulo/ventas
aliases:
  - Arquitectura BDC
  - Wireframe BDC
  - Arquitectura Business Developer Coordinator
---

# Arquitectura — Business Developer Coordinator (BDC)

Wireframe de la plataforma Oranje para el rol [[Ventas/Roles/Business Developer Coordinator|Business Developer Coordinator]]. Define el flujo de entrada, header global, sidebar de módulos y el detalle de cada módulo al que tiene acceso.

> [!info]
> El BDC es el **rol supervisor** del depto Ventas. Supervisa rutas y zonas, valida los términos del Documento de T&C, **da el sí final** a la conversión de prospecto a cliente, y gestiona los casos especiales: estancamiento (Café) y clientes pausados (Negro).

> [!important]
> **Acciones EXCLUSIVAS del BDC** (no del BD):
> 1. Validar Documento de T&C
> 2. Crear Usuario del Hotel
> 3. **Aprobar conversión** (RR-V-01) → dispara el Trigger Automático
> 4. Desbloquear estancamiento (Café — RR-V-04)
> 5. Gestionar cliente pausado (Negro — RR-V-05)
> 6. Generar reportes ejecutivos

## N0 — Inicio

```
PLATAFORMA ORANGE
        │
        ▼
LOGIN / AUTORIZACIÓN
        │
        ▼
ROL IDENTIFICADO / BUSINESS DEVELOPER COORDINATOR (BDC)
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
- Rol: Business Developer Coordinator (BDC)

**Mi territorio supervisado**
- Rutas y zonas a cargo
- Cantidad de BDs supervisados
- Cantidad de prospectos activos
- Cantidad de clientes activos

**Mis métricas (mes en curso)**
- Conversiones aprobadas
- Tasa de conversión del territorio
- Casos Café desbloqueados
- Clientes Negro reactivados
- Tiempo promedio de validación de T&C

**Configuración**
- Cambiar contraseña
- Preferencias de notificación

🚪 Cerrar sesión

### 🔍 BUSCADOR (global)

**Qué se puede buscar**
- Hoteles prospecto / cliente activos
- BDs supervisados
- Propuestas
- Contratos
- Casos Café / Negro

**Cómo funciona**
- Resultados en vivo agrupados por tipo
- Atajo: `/` o `Ctrl+K`

### 🔔 NOTIFICACIONES (campanita con badge)

**Por tipo de evento**
- 📄 BD envió T&C para validación
- ☕ BD marcó prospecto Café (debo desbloquear)
- ⚫ Cliente activo pasó a Negro
- 📊 Solicitud de reporte por dirección
- 📈 BDs alcanzaron metas del mes
- 🔴 Indicador de Calidad bajó

**Estados**
- 🟠 Sin leer (cuenta en el badge)
- 🟢 Leída

---

## N1 — SIDEBAR (Módulos del BDC)

```
SIDEBAR
   ├─ DASHBOARD
   ├─ PIPELINE             (vista global del territorio)
   ├─ CONTRATOS            (validación final)
   ├─ CONVERSIÓN           (Crear Usuario + Aprobar)
   ├─ MI EQUIPO            (BDs a cargo)
   ├─ CLIENTES ACTIVOS     (referente comercial)
   └─ REPORTES             (ejecutivos)
```

> [!note]
> El BDC tiene **vista global** de todo el territorio. NO opera prospectos como el BD (no identifica, no propone), pero sí puede crear T&C y participar en la negociación (Rosa).

---

## 📊 Módulo DASHBOARD

### KPIs del territorio
- Total de prospectos activos por status
- Conversiones del mes (% del territorio)
- T&C pendientes de validación (badge rojo si >24h)
- Casos Café pendientes de desbloquear
- Clientes Negro activos
- Indicador de Calidad ([[QA]])

### Vistas resumen
- **Embudo de conversión:** Gris → Azul Claro → Verde → Amarillo → Rosa → Naranja con conteo por etapa.
- **Heatmap de zonas:** rendimiento por ruta / zona.
- **Ranking de BDs:** por conversiones, propuestas enviadas, tasa de éxito.
- **Casos críticos:** T&C esperando validación, Café sin desbloquear, alertas de calidad.

### Acciones rápidas
- 📄 Validar T&C pendientes
- ☕ Desbloquear Café
- ✅ Aprobar conversión
- 📊 Generar reporte
- 💬 Comunicar con un BD

---

## 📋 Módulo PIPELINE (Vista global del territorio)

### Sub-vistas (todos los status del Semáforo Onboarding, todos los BDs)
- ⚪ **Gris** · 🔵 **Azul Claro** · 🟢 **Verde** · 🟡 **Amarillo** · 🩷 **Rosa**
- 🟠 **Naranja** · 🔴 **Rojo** · ⚫ **Negro** · 🟤 **Café** · 📂 **Toda**

### Filtros
- BD asignado
- Ruta / Zona
- Status
- Fecha de identificación
- Días en status

### Detalle del prospecto
- Mismos campos que ve el BD + **BD asignado** visible.
- Línea de tiempo completa con todos los cambios de status.

### Acciones (perfil supervisor)
- 👁️ **Ver detalle completo** de cualquier prospecto.
- 💬 **Comentar al expediente** (visible para el BD).
- 📤 **Reasignar prospecto a otro BD** (excepcional — BD ausente o sobrecargado).
- 📝 **Editar status / corregir** (con justificación obligatoria).

---

## 📄 Módulo CONTRATOS

### Sub-vistas
- ⏳ **Pendientes de validación** (badge rojo si lleva >24h)
- ✅ **Validados**
- ❌ **Rechazados con observaciones**
- 📂 **Histórico**

### Detalle del T&C
- Datos del hotel
- Pay rate, Bill rate, Overtime, Festivos, Calendario
- BD que lo creó
- Histórico de versiones (si fue rechazado y reenviado)

### Acciones (BDC exclusivo)
- ✓ **Validar T&C** (RF-V-10) — da el sí final para iniciar Rosa.
- ✗ **Rechazar T&C con observaciones** — vuelve al BD para corrección.
- 💬 **Comentar** al BD sobre algún campo.

> [!important]
> La validación del T&C es **prerequisito** para iniciar la negociación (Rosa) y, posteriormente, para aprobar la conversión.

---

## ✅ Módulo CONVERSIÓN

### Sub-vistas
- ⏳ **Listos para conversión** (en Rosa con T&C validado)
- ✅ **Convertidos** (últimos 30 días)
- 📂 **Histórico**

### Acciones (BDC exclusivo)
- ➕ **Crear Usuario del Hotel** (RF-V-11) — precondición obligatoria.
- ✅ **Aprobar conversión** (RF-V-12) — solo si Usuario del Hotel ya creado (RR-V-02).

### Detalle del proceso de conversión

```
PROSPECTO EN ROSA + T&C VALIDADO
         │
         ▼
1. BDC crea USUARIO DEL HOTEL (RF-V-11)
         │
         ▼
2. BDC click "APROBAR CONVERSIÓN" (RF-V-12)
         │
         ▼
3. Sistema dispara TRIGGER AUTOMÁTICO (RF-V-13):
      ├─ Email de bienvenida al hotel
      ├─ Notificación al BD asignado
      └─ Hotel sale de Pipeline (prospectos)
         │
         ▼
4. Status pasa a NARANJA (cliente activo)
         │
         ▼
5. Hotel pasa a operación: Reclutamiento + Inspector
         │
         ▼
6. Contrato se genera con T&C validado como insumo
```

> [!warning]
> Si el BDC intenta aprobar conversión sin haber creado el Usuario del Hotel, el sistema bloquea con: *"Debe crear el Usuario del Hotel antes de aprobar la conversión"* (RR-V-02).

---

## 👥 Módulo MI EQUIPO

### Lista de BDs
- BD · ruta/zona · n° prospectos activos · % conversión · tiempo promedio

### Filtros
- Ruta / Zona
- Estado (activo / vacaciones / baja)
- Carga (alta / media / baja)

### Detalle del BD (al click)
- Datos básicos
- Métricas individuales (conversiones, propuestas, rechazos, tiempo promedio)
- Histórico de prospectos gestionados
- Comunicación (chat / nota interna)

### Acciones
- 👁️ **Ver desempeño** detallado del BD.
- 💬 **Comunicar** con el BD.
- 📤 **Reasignar prospecto** a otro BD del equipo.
- 📊 **Solicitar reporte** específico.

---

## 🏨 Módulo CLIENTES ACTIVOS (referente comercial)

### Sub-vistas
- ✅ **Activos** (status Naranja)
- ⚫ **Negro** (pausados / inactivos — gestión exclusiva BDC)

### Detalle
- Datos comerciales del cliente
- Histórico del onboarding
- T&C aplicable
- BD originario asignado

### Acciones
- 👁️ **Ver detalle del cliente**.
- ⚫ **Marcar Negro** (cliente pausado / inactivo) — RF-V-20.
- ↩️ **Reactivar desde Negro** (vuelve a Azul Claro como prospecto) — RF-V-21.

> [!important]
> Negro y reactivación de Negro son **exclusivas del BDC** (RR-V-05). El BD no tiene acceso.

---

## ☕ Bandeja de Café (vista directa desde Pipeline)

### Sub-vista dedicada
- Todos los prospectos en Café del territorio
- Notas del BD que marcó Café (contexto)

### Acciones (BDC exclusivo)
- 🔍 **Investigar caso** — revisar histórico, contactos, notas del BD.
- ✅ **Desbloquear Café** (RF-V-18) — solución acordada.
- ↩️ **Reactivar a Azul Claro** (RF-V-19) — el BD original recupera el caso.
- 🔄 **Reasignar a otro BD** si aplica.

---

## 📈 Módulo REPORTES

### Tipos de reporte
- Pipeline del territorio
- Conversión por BD / zona / mes
- Casos Café desbloqueados
- Clientes Negro
- Indicador de Calidad
- Reporte ejecutivo para dirección

### Filtros
- Rango de fechas
- BD / Ruta / Zona

### Acciones
- 📊 **Generar reporte** (RF-V-26)
- 📥 **Exportar** (CSV / PDF / Excel)
- 📤 **Enviar a dirección** (RF-V-27)
- 📅 **Programar envío recurrente**

### Histórico
- Lista de reportes generados con destinatario y estado.

---

## Flujo operativo del BDC

```
1. Login → Dashboard (embudo, casos críticos, alertas)
   │
   ▼
2. Reviso T&C pendientes → Valido o rechazo con observaciones
   │
   ▼
3. Reviso Pipeline global del territorio:
      ├─ Casos Café → desbloqueo
      ├─ Casos Negro → gestiono o reactivo
      └─ Casos Rosa con T&C validado → preparo conversión
   │
   ▼
4. Para cada conversión:
      ├─ Creo Usuario del Hotel (precondición)
      └─ Apruebo conversión → dispara Trigger Automático
   │
   ▼
5. Reviso desempeño de mis BDs (Mi Equipo)
   │
   ▼
6. Comunico observaciones, reasigno casos si aplica
   │
   ▼
7. Genero reportes ejecutivos para dirección
```

---

## Diferencias clave vs BD

| Aspecto | BD | BDC |
|---|---|---|
| Identificar prospectos | ✅ | — |
| Visibilidad | Mi territorio | Todo el territorio supervisado |
| Elaborar Propuesta | ✅ | — |
| Crear T&C | ✅ | ✅ |
| **Validar T&C** | ❌ | ✅ exclusivo |
| Negociar (Rosa) | ✅ | ✅ |
| **Crear Usuario del Hotel** | ❌ | ✅ exclusivo |
| **Aprobar conversión** | ❌ | ✅ exclusivo |
| Gestionar Rojo | ✅ | — |
| **Desbloquear Café** | ❌ | ✅ exclusivo |
| **Gestionar Negro** | ❌ | ✅ exclusivo |
| Mi Equipo (BDs) | ❌ | ✅ exclusivo |
| Reportes ejecutivos | ❌ | ✅ exclusivo |

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
║              ROL: BUSINESS DEVELOPER COORDINATOR              ║
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
│ 📊 Dashboard │  Embudo · Heatmap · Ranking BDs · Casos       │
│ 📋 Pipeline  │  Vista global · Filtros por BD/zona/status    │
│ 📄 Contratos │  Validar · Rechazar · Histórico               │
│ ✅ Conversión│  Crear Usuario Hotel · Aprobar conversión     │
│ 👥 Mi Equipo │  BDs a cargo · Métricas individuales          │
│ 🏨 Clientes  │  Activos + Negro · Reactivar                  │
│ 📈 Reportes  │  Generar · Enviar dirección · Programar       │
└──────────────┴───────────────────────────────────────────────┘
```

---

## Relacionado

- [[Ventas/Roles/Business Developer Coordinator|Business Developer Coordinator]] (definición del rol)
- [[Ventas/Roles/Business Developer|Business Developer]]
- [[Ventas/Ventas|Ventas]]
- [[Ventas/Reglas de Ventas|Reglas de Ventas]]
- [[Ventas/Onboarding-Hotel/Onboarding-Hotel|Onboarding-Hotel]]
- [[Ventas/Onboarding-Hotel/Flujo de Onboarding|Flujo de Onboarding]]
- [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]]
- [[Ventas/Onboarding-Hotel/Conceptos/Documento de Términos y Condiciones|Documento de Términos y Condiciones]]
- [[Ventas/Onboarding-Hotel/Conceptos/Trigger Automático de Conversión|Trigger Automático de Conversión]]
- [[Ventas/Onboarding-Hotel/Conceptos/Usuario del Hotel|Usuario del Hotel]]
- [[Core/Módulos/Contrato|Contrato]]
- [[Hotel/Hotel|Hotel]] (destino post-Naranja)
