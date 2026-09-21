---
tags:
  - modulo/core
aliases:
  - Semáforo de Revisión del Día
  - Semáforo de Revisión del Timesheet
  - Status Revisión del Día Azul claro
  - Status Revisión del Día Amarillo
  - Status Revisión del Día Morado
  - Status Revisión del Día Pendiente
  - Status Revisión del Día Observado
  - Status Revisión del Día Revisado
---

# Semáforo de Revisión del Día

Estado de revisión de **un día** del [[Timesheet]]: si el [[Hotel/Supervisor|Supervisor]] ya lo dejó resuelto, sigue pendiente o quedó marcado por una anomalía sin resolver.

> [!info]
> Este semáforo es de **un día**. Es distinto del [[Semáforo de Aprobación de la Semana]], que rige el ciclo completo de la semana, y también del [[Indicador de Cumplimiento del Timesheet]], que compara horas reales vs. contractuales por semana y no tiene intervención humana. Los tres conviven dentro de [[Timesheet]] pero miden cosas distintas.

> [!note] Estado derivado, no columna propia
> El color no se guarda como estado explícito: se deriva de dos campos del día — si tiene una anomalía sin resolver y si tiene una nota de revisión escrita.

## Estados

| Color      | Estado    | Responsable         | Descripción                                                                                                                                   |
| ---------- | --------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| Azul claro | Pendiente | — (automático)        | Día sin anomalía y sin nota de revisión. No bloquea nada.                                                                                        |
| Amarillo   | Observado | — (automático)        | El día tiene una anomalía sin resolver: jornada incompleta (falta marca de entrada o de salida) o duración inválida (0 minutos o negativa, ej. salida antes que entrada). Bloquea el envío de la semana a aprobación. |
| Morado     | Revisado  | [[Hotel/Supervisor\|Supervisor]] (+ [[Hotel/Manager de Área\|GH]]/[[Hotel/Manager General\|GM]], herencia) | El Supervisor escribió una nota de revisión (obligatoria en ambos casos) y el día queda resuelto, tenga o no anomalía.                            |

> [!info] Herencia por jerarquía
> El [[Hotel/Manager de Área|Manager de Área]] hereda las atribuciones del [[Hotel/Supervisor|Supervisor]], y el [[Hotel/Manager General|Manager General]] hereda las de ambos, así que también pueden escribir la nota de revisión de un día. Ver [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]].

## Derivación

- `has_anomaly = true` y `review_note = null` → **Observado**
- `review_note != null` → **Revisado** (pisa a Observado si ya se resolvió)
- Ninguna de las anteriores → **Pendiente**

## Detalle por estado

### Azul claro — Pendiente
**Responsable:** el sistema, automático.

El día no presenta anomalía ni tiene nota de revisión. Es el estado de entrada de todo día del timesheet.

**Avance →** el sistema lo mueve a [[#Amarillo — Observado|Amarillo]] apenas detecta una anomalía en alguna marca; o el [[Hotel/Supervisor|Supervisor]] lo mueve directo a [[#Morado — Revisado|Morado]] escribiendo una nota de revisión (un día sin anomalía también puede revisarse).

---

### Amarillo — Observado
**Responsable:** el sistema, automático — la transición hacia este estado no la dispara una persona.

El día no tiene marca de entrada, no tiene marca de salida, o la duración resultante entre ambas es de 0 minutos o negativa (ej. una salida capturada antes que la entrada).

> [!info] Lo que se ve pero no dispara el estado
> Geocerca, marca manual y desfase de reloj entre el teléfono y el servidor (ver [[Timesheet#Ponchado|Ponchado]]) se muestran junto a cada marca en el modal de revisión como información adicional para el [[Hotel/Supervisor|Supervisor]], pero hoy **no** determinan si el día entra a Observado — solo la jornada incompleta o la duración inválida lo hacen.

> [!important] Bloqueo hacia la semana
> Mientras un día está en Observado, el [[Semáforo de Aprobación de la Semana]] no puede pasar a Enviada a aprobación — el sistema rechaza el envío.

**Avance →** el [[Hotel/Supervisor|Supervisor]] escribe la nota de revisión obligatoria y el día pasa a [[#Morado — Revisado|Morado]].

---

### Morado — Revisado
**Responsable:** [[Hotel/Supervisor|Supervisor]] (también el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]], por herencia)

El Supervisor escribió su nota de revisión — obligatoria tanto si el día tenía anomalía como si no — y el día queda resuelto. Es el estado terminal **por diseño**: no hay transición documentada de vuelta a Pendiente ni a Observado.

> [!warning] Pendiente conocido — Morado no es terminal en la práctica
> `recalcDay()` actualiza `has_anomaly` cada vez que llega una marca nueva del día, pero nunca toca `review_note`. La derivación de este semáforo mira `review_note` primero: si ya existe, el día se lee como Revisado sin volver a evaluar `has_anomaly`. Consecuencia: si un día ya Revisado recibe después una marca tardía que vuelve a producir jornada incompleta o duración inválida, el día se sigue mostrando como Morado — la nota vieja tapa la anomalía nueva, y nadie lo ve. Detectado en `timesheets.repository.ts` (`recalcDay`) y `timesheets.service.ts` (`summarize`). No resuelto a la fecha; documentado como hallazgo, sin cambio de código.

## Reglas clave

- Es un semáforo **derivado**: el color no vive en una columna de estado propia, se calcula al leer el día.
- Escribe la nota de revisión que mueve un día a Morado el [[Hotel/Supervisor|Supervisor]] — también el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]], por herencia.
- La nota de revisión es obligatoria en los dos caminos hacia Morado: resolviendo una anomalía o revisando un día sin anomalía.
- Bloquea al [[Semáforo de Aprobación de la Semana]]: una semana con al menos un día en Observado no puede enviarse a aprobación.

## Relacionado

- [[Timesheet]]
- [[Semáforo de Aprobación de la Semana]]
- [[Indicador de Cumplimiento del Timesheet]]
- [[Hotel/Supervisor|Supervisor]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Manager General|Manager General]]
