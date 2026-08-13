---
tags:
  - arquitectura
  - departamento/inspeccion
  - caso-de-uso
aliases:
  - CU RF-13
  - Resolver disputa Inspector
---

# ID: RF-13
**Nombre:** Resolver disputa de colaborador

**Actor principal:** [[Inspector]]

**Historia:**
Cuando el hotel reporta a un colaborador (estado **Rojo** en el [[Semáforo del Colaborador]]), el Inspector de la zona investiga el caso. La investigación puede confirmar la falta (veto a Blacklist) o absolver al colaborador (reincorporación). La autoridad de decisión es exclusiva del Inspector; no escala al [[Manager de Reclutamiento]].

> [!info]
> El estado Rojo lo activa el [[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Supervisor|Supervisor]] del hotel. La regla de 3 inasistencias NO pasa por Rojo: va directamente a Negro de forma automática (sin intervención del Inspector). Solo los reportes manuales del hotel generan el estado Rojo que dispara este flujo.

**Precondiciones:**
- El colaborador está en estado **Rojo** en el [[Semáforo del Colaborador]].
- El Inspector tiene asignada la zona geográfica del hotel afectado.
- Si el Inspector titular de la zona no está disponible, el [[Inspección/Coordinador|Coordinador]] reasigna temporalmente a otro Inspector.

**Postcondiciones:**
- El colaborador transita a **Negro** ([[Core/Módulos/Blacklist|Blacklist]]) si la disputa se resuelve a favor del hotel. El Inspector ya no puede revertirlo.
- El colaborador transita a **Verde fuerte** (reincorporado al pool) si la disputa se resuelve a su favor.
- El resultado queda registrado en el log del sistema con actor, fecha y resolución.

> [!info]
> Una vez que el colaborador alcanza estado **Negro**, el Inspector ya no puede revertirlo: la única salida es `Negro → Blanco`, y la autoriza exclusivamente un perfil de Administrador (`ROL-ADM-01`) *(cambiado el 2026-08-13; antes no existía salida alguna)*. No es una instancia de apelación de la disputa.

**Flujo:**

`Notificación de estado Rojo` → AUTOMATICO → `Sistema notifica al Inspector de zona asignado` → MANUAL → `Inspector accede al caso del colaborador` → `Revisa historial del colaborador, evidencia del hotel y contexto del reporte` → MANUAL → `Inspector emite veredicto`

Rama A — A favor del hotel:
`Veredicto: Blacklist` → MANUAL → `Inspector registra resultado con motivo obligatorio` → AUTOMATICO → `Sistema transita colaborador de Rojo → Negro` → `Colaborador queda vetado` → `Sistema notifica a involucrados (hotel, Reclutadora responsable)` → `Registra en log auditable`

Rama B — A favor del colaborador:
`Veredicto: Reincorporación` → MANUAL → `Inspector registra resultado con motivo obligatorio` → AUTOMATICO → `Sistema transita colaborador de Rojo → Verde fuerte` → `Colaborador queda disponible en el pool para nueva asignación` → `Sistema notifica a involucrados (hotel, Reclutadora responsable)` → `Registra en log auditable`

**Validaciones:**
- Solo el Inspector asignado a la zona del hotel puede emitir el veredicto (o el Inspector temporal designado por el Coordinador).
- El motivo/justificación es obligatorio en ambas ramas.
- El sistema bloquea la transición si el colaborador ya no está en estado Rojo al momento de registrar el veredicto.

> [!info]
> Los campos exactos del formulario de resolución (qué evidencia captura el Inspector, si existe un campo de "días investigados", etc.) no están definidos en la fuente actual. Este detalle queda pendiente de especificación.

**Criterios de aceptación:**
- Solo el Inspector de zona (o sustituto temporal autorizado por el Coordinador) puede ejecutar la resolución.
- La justificación es obligatoria en ambas ramas de decisión.
- El cambio de estado (Rojo → Negro o Rojo → Verde fuerte) se registra en el log con actor, fecha y motivo.
- Las partes involucradas (hotel, Reclutadora) reciben notificación del resultado.
- Los casos de Blacklist resultantes son revisados por el [[Manager de Reclutamiento]] (contexto de seguimiento, no de decisión).

**Documentación:**
- Fuente: [[Inspector]] · [[Inspección/Reglas de Inspección|Reglas de Inspección]]
- Semáforo: [[Semáforo del Colaborador]]
- Módulo: [[Core/Módulos/Blacklist|Blacklist]]

## Relacionado

- [[Inspector]]
- [[Semáforo del Colaborador]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Inspección/Reglas de Inspección|Reglas de Inspección]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Manager General|Manager General]]
- [[Hotel/Supervisor|Supervisor]]
- [[Manager de Reclutamiento]]
- [[00 - Arquitectura Inspector]]
