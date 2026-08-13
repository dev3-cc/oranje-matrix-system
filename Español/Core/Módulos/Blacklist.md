---
tags:
  - modulo/core
aliases:
  - Blacklist
  - Lista Negra
---

# Blacklist

Lista de colaboradores vetados que no pueden volver a ser reclutados.

## Qué es

Registro de personas que han sido bloqueadas por:

- Inasistencias.
- Faltas graves cometidas en el hotel.

## Uso en Reclutamiento

A las [[Reclutadora|reclutadoras]] les sirve saber quién está en blacklist para:

- **Evitar volver a reclutar** a alguien vetado cuando se postula.
- **Tener un registro** de colaboradores bloqueados.

## Cómo se entra a la blacklist

Un colaborador pasa a estado **Negro** en el [[Semáforo del Colaborador]] por:

- **Falta grave (manual)** → cualquier rol del departamento de Reclutamiento ([[Reclutadora]], [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] o [[Manager de Reclutamiento]]) puede agregarlo con **motivo y evidencia** obligatorios; queda registro auditable.
- **3 inasistencias** → blacklist automático por sistema.
- **Disputa resuelta a favor del hotel** → tras un reporte del hotel (estado Rojo) e investigación del [[Inspector]].

## Proceso de investigación

Cuando un colaborador entra en estado Rojo (reporte del hotel), el [[Inspector]] de la zona investiga la disputa.

> [!info] El Inspector tiene autoridad propia para decidir el resultado. No requiere escalamiento al Manager de Reclutamiento para emitir la resolución.

- **Resolución a favor del hotel:** el colaborador pasa a **Negro** ([[Core/Módulos/Blacklist|Blacklist]]).
- **Resolución a favor del colaborador:** el colaborador regresa a **Verde fuerte** (disponible).

## Salida del estado Negro

> [!important] El veto se puede levantar, pero **solo por un perfil de Administrador**.
> Cambiado el 2026-08-13. Antes este documento decía que Negro era permanente y que no existía rehabilitación ni apelación.

**No es rehabilitación automática ni una instancia de apelación**: es una acción administrativa deliberada, con **motivo obligatorio**, y ningún rol de Reclutamiento o del hotel la puede ejecutar.

Al levantarse, el colaborador regresa a **Blanco** —no a Verde fuerte— y vuelve a pasar por la validación de la [[Reclutadora]] antes de ser asignable. Es el mismo patrón de punto único de reingreso que usa el [[Semáforo Onboarding]].

Mientras el veto esté vigente:

- El colaborador **no se elimina** del sistema.
- **No aparece en búsquedas activas** de reclutamiento ni en el [[Pool de Colaboradores]].
- El **historial se conserva** íntegro para consulta interna.

Y el registro del veto **nunca se borra**: se marca como levantado, con quién lo levantó y por qué. Un colaborador puede haber sido vetado más de una vez a lo largo del tiempo, pero **solo un veto puede estar vigente**.

## Relacionado

- [[Semáforo del Colaborador]]
- [[Reclutadora]]
- [[Inspector]]
- [[Flujo de Reclutamiento]]
