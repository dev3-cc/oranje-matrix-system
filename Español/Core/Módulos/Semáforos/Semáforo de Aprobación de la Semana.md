---
tags:
  - modulo/core
aliases:
  - Semáforo de Aprobación de la Semana
  - Ciclo de Aprobación del Timesheet
  - Status Aprobación de la Semana Abierta
  - Status Aprobación de la Semana Enviada a aprobación
  - Status Aprobación de la Semana Aprobada
---

# Semáforo de Aprobación de la Semana

Ciclo de aprobación de un [[Timesheet]] semanal completo: una semana de un colaborador dentro de una [[Core/Módulos/Requisicion/Requisición|requisición]] — una semana = una requisición (D-09 de [[Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]]).

> [!info]
> Este semáforo es de la **semana completa**. Es distinto del [[Semáforo de Revisión del Día]], que rige cada jornada por separado, y también del [[Indicador de Cumplimiento del Timesheet]], que compara horas reales vs. contractuales y no aprueba nada. Los tres conviven dentro de [[Timesheet]] pero miden cosas distintas.

> [!note] Por qué esta paleta (Gris/Naranja/Verde) y no otra
> Se descartó verde/amarillo/rojo porque ninguno de los tres estados es "malo" — pintar Abierta de rojo entrenaría mal al [[Hotel/Supervisor|Supervisor]] — y porque Amarillo ya significa "Observado" (anomalía sin resolver) en el [[Semáforo de Revisión del Día]], que convive en la misma pantalla: reutilizarlo aquí para Enviada a aprobación (lo esperado, lo bueno) generaría ambigüedad visual. Por eso este semáforo usa un set sin intersección con el del Día (Azul claro/Amarillo/Morado). Implementado en `apps/web/src/shared/constants/timesheetStatus.ts` y `WorkerWeekSummary.tsx`; Aprobada además se distingue con una palomita, no solo con color.

## Estados

| Color   | Estado                | Responsable                                                                        | Descripción                                                                                                     |
| ------- | ---------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| Gris    | Abierta                | [[Hotel/Supervisor\|Supervisor]] (+ [[Hotel/Manager de Área\|GH]]/[[Hotel/Manager General\|GM]], herencia) | Neutral: nadie ha actuado todavía. Acepta marcas manuales; el Supervisor puede seguir revisando días ([[Semáforo de Revisión del Día]]). |
| Naranja | Enviada a aprobación   | [[Hotel/Supervisor\|Supervisor]] (+ [[Hotel/Manager de Área\|GH]]/[[Hotel/Manager General\|GM]], herencia)                                                     | El Supervisor la envió. Queda a la espera de un Manager. Ya no admite marcas manuales ni más revisiones de día.    |
| Verde   | Aprobada                | [[Hotel/Manager de Área\|Manager de Área]] o [[Hotel/Manager General\|Manager General]] | Éxito. Mismo significado que "Autorizada" en el [[Semáforo de Requisición]]. Las horas quedan definitivas y alimentan el [[Contabilidad/Consolidado Semanal del Colaborador\|Consolidado Semanal]] y la facturación. |

> [!info] Herencia por jerarquía
> El [[Hotel/Manager de Área|Manager de Área]] hereda las atribuciones del [[Hotel/Supervisor|Supervisor]], y el [[Hotel/Manager General|Manager General]] hereda las de ambos, así que también pueden abrir marcas manuales y enviar la semana a aprobación. La **aprobación** (Naranja → Verde) no se hereda del Supervisor porque nunca fue suya: es una atribución exclusiva del Manager de Área y el Manager General, asentada aparte. Ver [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]].

## Detalle por estado

### Gris — Abierta
**Responsable:** [[Hotel/Supervisor|Supervisor]] (también el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]], por herencia)

Estado inicial del timesheet semanal. Admite marcas manuales del Supervisor y días en cualquier estado del [[Semáforo de Revisión del Día]] (Pendiente, Observado o Revisado).

**Avance →** el Supervisor envía la semana ("Enviar a revisión"); pasa a [[#Naranja — Enviada a aprobación|Enviada a aprobación]].

> [!important] Bloqueo de envío
> El sistema **rechaza** el envío si queda algún día en **Observado** del [[Semáforo de Revisión del Día]]. Son dos semáforos relacionados por esta regla de transición: la semana no avanza mientras el día no esté Revisado o Pendiente.

---

### Naranja — Enviada a aprobación
**Responsable:** [[Hotel/Supervisor|Supervisor]] (quien la envía, también el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]] por herencia); queda a la espera de [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Manager General|Manager General]] para aprobarla.

Ya no admite marcas manuales ni más revisiones de día.

**Avance →** el [[Hotel/Manager de Área|Manager de Área]] (su departamento) o el [[Hotel/Manager General|Manager General]] (cualquier departamento) la aprueba; pasa a [[#Verde — Aprobada|Aprobada]]. Son los **dos únicos roles** que aprueban horas (ver [[Timesheet#Aprobación]]).

> [!important] Sin aprobación antes del cierre
> Si nadie aprueba antes del cierre semanal, las horas **quedan en espera**: no se pagan, no se facturan, no hay aprobación automática ni escalamiento (ver [[Timesheet#Aprobación]] y D-09 en [[Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]]). No existe una transición a "vencida" — el estado permanece en Enviada a aprobación hasta que alguien la apruebe.

---

### Verde — Aprobada
**Responsable:** [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Manager General|Manager General]]

Estado terminal del ciclo. Solo desde aquí las horas alimentan el [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] y la facturación al hotel.

## Reglas clave

- Una semana = una requisición (D-09): el timesheet semanal vive por colaborador **y** por requisición, no por hotel completo.
- Dependencia con [[Semáforo de Revisión del Día]]: no se puede enviar a aprobación con días en Observado.
- Aprobar horas está limitado a **dos roles**: [[Hotel/Manager de Área|Manager de Área]] (su departamento) y [[Hotel/Manager General|Manager General]] (cualquiera).
- El respaldo del Manager General es manual: puede aprobar lo que el Manager de Área dejó pendiente, pero nadie se lo transfiere automáticamente al vencer un plazo.

## Relacionado

- [[Timesheet]]
- [[Semáforo de Revisión del Día]]
- [[Indicador de Cumplimiento del Timesheet]]
- [[Core/Módulos/Requisicion/Requisición|Requisición]]
- [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]]
- [[Hotel/Supervisor|Supervisor]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Manager General|Manager General]]
