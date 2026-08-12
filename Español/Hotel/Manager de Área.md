---
tags:
  - modulo/hotel
aliases:
  - Manager de Área
  - Gerente de Departamento
  - Hotel Manager
  - GH
---

# Manager de Área

Rol operativo del módulo de [[Hotel/Hotel|Hotel]]. Responsable de aprobar o rechazar las [[Requisición|requisiciones]], **aprobar las horas trabajadas**, gestionar a los colaboradores asignados y administrar el [[Core/Módulos/Schedule|Schedule]] de su departamento.

> [!note] También llamado **Hotel Manager**. Es el mismo rol: `Manager de Área`, `Gerente de Departamento` y `Hotel Manager` son alias del mismo perfil (`ROL-H-02`).

> [!note] Jerarquía simple
> En hoteles con jerarquía simple, el [[Hotel/Manager General|Manager General]] también opera como Manager de Área (misma persona, dos roles). En hoteles con jerarquía extendida, hay un Manager de Área por cada [[Departamentos del Hotel|departamento]] (Housekeeping, Alimentos, Mantenimiento, Front Desk), subordinados al [[Hotel/Manager General|Manager General]].

## Responsabilidades

### Requisiciones

- Crea [[Requisición|requisiciones]] de personal.
- **Aprueba** la requisición (estado **Autorizada**), que queda disponible en la bandeja compartida de [[Reclutamiento/Reclutamiento|Reclutamiento]] para ser tomada por una [[Reclutadora]] o [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] (modelo [[Self-Pick de Requisiciones|Self-Pick]]).
- **Rechaza** la requisición, regresándola al creador con observaciones (estado **En elaboración**).

> [!important] Capa de seguridad
> Esta aprobación es una capa de seguridad para evitar que lleguen requisiciones falsas o incorrectas al equipo de [[Reclutamiento/Reclutamiento|Reclutamiento]]. Oranje solo recibe requisiciones que un Manager de Área o el [[Hotel/Manager General|Manager General]] ha validado.

### Aprobación de horas

- **Aprueba las horas** de los colaboradores de **su [[Departamentos del Hotel|departamento]]**, en un calendario donde revisa las jornadas completas.
- La aprobación cubre **todos los días de la requisición**. Solo lo aprobado alimenta el [[Timesheet]], el [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] y la facturación al hotel.
- Recibe del [[Hotel/Supervisor|Supervisor]] las jornadas ya revisadas, con los ponches anómalos resueltos y los manuales justificados.
- Si no aprueba antes del cierre semanal, las horas **quedan en espera**: no se pagan ni se facturan, y no hay aprobación automática.

> [!important] Aprobar horas está limitado a **dos roles**: este y el [[Hotel/Manager General|Manager General]], que puede aprobar las de cualquier departamento. El [[Hotel/Supervisor|Supervisor]] **revisa pero no aprueba**: deja la jornada lista, y la aprobación es la que libera el dinero.

### Autorización de horas extra

- Es **el único rol** que autoriza rebasar las **40 horas brutas semanales** de una requisición. Ni el [[Hotel/Supervisor|Supervisor]] ni el [[Hotel/Manager General|Manager General]] lo hacen.
- El disparador es el ponche que cruza las 40 horas acumuladas de esa semana en esa requisición; el sistema lo detecta antes del cierre semanal.
- El colaborador **sí puede quedarse**: las horas extra se registran y se pagan según la política del [[Core/Módulos/Contrato|Contrato]]. Ver [[Core/Módulos/Timesheet#Overtime|Overtime]].

### Gestión de personal asignado

- Manda a colaboradores a descansar (estado **Rosa — Stand-by** en el [[Semáforo del Colaborador]]).
- Reporta colaboradores (estado **Rojo — Reportado** en el [[Semáforo del Colaborador]]).
- Reporta [[Core/Módulos/Accidente Laboral/Accidente Laboral|accidentes laborales]] detectados en la propiedad.
- Gestiona el [[Core/Módulos/Schedule|Schedule]] semanal de su departamento.

## Relacionado

- [[Hotel/Manager General|Manager General]]
- [[Hotel/Supervisor|Supervisor]]
- [[Hotel/Hotel|Hotel]]
- [[Departamentos del Hotel]]
- [[Requisición]]
- [[Timesheet]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Semáforo de Requisición]]
- [[Semáforo del Colaborador]]
- [[Inspector]]
