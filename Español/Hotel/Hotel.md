---
tags:
  - modulo/hotel
aliases:
  - Hotel
  - Módulo de Hotel
---

# Hotel

Módulo que representa al hotel como cliente de Oranje. El hotel solicita personal a través de [[Requisición|requisiciones]] y gestiona su operación semanal desde el [[Core/Módulos/Schedule|Schedule]], que es el eje donde convergen la demanda de posiciones, la cobertura de colaboradores asignados y el registro de tiempo trabajado ([[Timesheet]]). Opera como contraparte del equipo de [[Reclutamiento/Reclutamiento|Reclutamiento]].

## Contenido del módulo

### Roles

- [[Hotel/Manager General|Manager General]] — Máxima autoridad del hotel. Siempre existe en ambas jerarquías.
- [[Hotel/Manager de Área|Manager de Área]] — También llamado **Hotel Manager**. Rol operativo por departamento: aprueba o rechaza requisiciones, **aprueba las horas trabajadas** y gestiona el personal asignado.
- [[Hotel/Supervisor|Supervisor]] — Crea las requisiciones de personal y revisa los ponches antes de la aprobación.

### Estructura organizacional

Dependiendo del tamaño y complejidad del hotel, la plataforma soporta dos configuraciones jerárquicas:

#### Jerarquía simple

Para hoteles pequeños o con estructura plana. El Manager General también opera como Manager de Área (misma persona, dos roles).

```
Manager General (GM) → SUP → Colaboradores de Oranje
```

#### Jerarquía extendida

Para hoteles grandes con múltiples [[Departamentos del Hotel|departamentos]] operativos.

```
Manager General (GM)
  └── Manager de Área (uno por departamento)
       └── Supervisor(es)
            └── Colaboradores de Oranje
```

#### Equivalencia de roles

| Jerarquía simple | Jerarquía extendida | Responsabilidades en plataforma |
|---|---|---|
| [[Hotel/Manager General\|Manager General]] | [[Hotel/Manager General\|Manager General]] | Supervisión general, visibilidad global, todas las acciones operativas |
| [[Hotel/Manager General\|Manager General]] (mismo rol) | [[Hotel/Manager de Área\|Manager de Área]] | Aprueba requisiciones, **aprueba las horas trabajadas**, gestiona schedule, reporta colaboradores |
| [[Hotel/Supervisor\|SUP]] | [[Hotel/Supervisor\|Supervisor]] | Crea requisiciones, **revisa ponches** (no aprueba), reporta colaboradores, reporta accidentes laborales |

#### Departamentos del hotel

- **Housekeeping** — Housekeeper, Houseman, Laundry
- **Alimentos** — Chef
- **Mantenimiento**
- **Front Desk**

Ver catálogo completo en [[Departamentos del Hotel]].

### Procesos

- Creación y autorización de [[Requisición|requisiciones]].
- Gestión del [[Core/Módulos/Schedule|Schedule]] semanal.
- Registro de tiempo trabajado vía [[Timesheet]].

## Conceptos Core relacionados

- [[Requisición]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Timesheet]]
- [[Semáforo de Requisición]]
- [[Semáforo de Urgencia de Requisición]]
- [[Semáforo de Posiciones de la Requisición]]
- [[Semáforo del Colaborador]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Posiciones]]
- [[Departamentos del Hotel]]
- [[Modalidades de Contratación]]
- [[Niveles de Inglés]]
- [[Zonas]]
- [[Inspector]]

## Relación con otros módulos

- [[Reclutamiento/Reclutamiento|Reclutamiento]] — Recibe las requisiciones aprobadas y asigna personal.
- [[Ventas/Ventas|Ventas]] — Responsable del onboarding de nuevos hoteles.
- [[Customer Service/Customer Service|Customer Service]] — Canal de atención post-onboarding para consultas, quejas y seguimiento de incidencias.
