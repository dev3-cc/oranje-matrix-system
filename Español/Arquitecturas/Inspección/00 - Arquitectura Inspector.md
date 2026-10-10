---
tags:
  - arquitectura
  - departamento/inspeccion
aliases:
  - Arquitectura Inspector
  - Wireframe Inspector
---

# Arquitectura — Inspector

Índice y ancla de la arquitectura del rol [[Inspector]] dentro del sistema Oranje. El Inspector es el rol operativo de campo del [[Inspección/Inspección|departamento de Inspección]]: verifica llegadas, entrega uniformes, investiga casos reportados y gestiona accidentes laborales en su zona.

> [!info]
> La fuente de verdad del rol y sus reglas vive en:
> - [[Inspector]] — definición del rol y responsabilidades.
> - [[Inspección/Reglas de Inspección|Reglas de Inspección]] — reglas de negocio del departamento.
> - [[Inspección/Inspección|Inspección]] — módulo general del departamento.

> [!important]
> **Autoridad propia del Inspector en disputas:**
> El Inspector es el **único rol** que puede ejecutar la entrada manual a Blacklist. Cuando un colaborador está en estado Rojo (reportado por el hotel), el Inspector investiga y resuelve:
> - A favor del hotel → **Negro** ([[Core/Módulos/Blacklist|Blacklist]]) — el Inspector ya no puede revertirlo; solo un Administrador o un rol de Reclutamiento levanta el veto.
> - A favor del colaborador → **Verde fuerte** — reincorporación.
> No escala al Manager de Reclutamiento para esta decisión.

## Alcance de esta arquitectura

Esta carpeta documenta la arquitectura de plataforma del Inspector: los casos de uso que le corresponden, los flujos de interacción con el sistema y las reglas que gobiernan sus acciones. No cubre la arquitectura del [[Inspección/Coordinador|Coordinador]] (que se documentará por separado si se requiere).

## Casos de uso documentados

| ID | Nombre | Descripción |
|---|---|---|
| [[RF-13 Resolver disputa de colaborador]] | Resolver disputa de colaborador | El Inspector investiga un caso Rojo y emite veredicto: Negro (Blacklist) o Verde fuerte (reincorporación). |

## Responsabilidades del Inspector (resumen operativo)

| Acción | Fuente |
|---|---|
| Verificar llegada Día 1 (Verde fuerte → Verde manzana) | [[Inspección/Reglas de Inspección\|Reglas de Inspección]] |
| Entregar uniforme Día 3+ (Verde manzana → Azul claro) | [[Inspección/Reglas de Inspección\|Reglas de Inspección]] |
| Investigar casos en estado Rojo | [[Inspector]] |
| Ejecutar entrada manual a Blacklist (Rojo → Negro) | [[Inspección/Reglas de Inspección\|Reglas de Inspección]] |
| Reincorporar colaborador (Rojo → Verde fuerte) | [[Inspector]] |
| Complementar y cerrar tarjeta de accidente laboral | [[Inspección/Reglas de Inspección\|Reglas de Inspección]] |
| Gestionar transición Gris → Verde fuerte (alta médica) | [[Inspector]] |
| Consultar Indicador de Lunch Extendido | [[Inspección/Reglas de Inspección\|Reglas de Inspección]] |

## Relacionado

- [[Inspector]]
- [[Inspección/Inspección|Inspección]]
- [[Inspección/Coordinador|Coordinador]]
- [[Inspección/Reglas de Inspección|Reglas de Inspección]]
- [[Semáforo del Colaborador]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]
- [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]]
- [[Manager de Reclutamiento]]
