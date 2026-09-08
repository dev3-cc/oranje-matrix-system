---
tags:
  - modulo/core
aliases:
  - Auditoría de Presentación y Ambiente
  - Auditoría de Presentación Personal
  - Percepción de Ambiente y Recursos
---

# Auditoría de Presentación y Ambiente

Dos auditorías que el [[Hotel/Supervisor|Supervisor]] realiza, semana a semana, sobre su hotel asignado (uno solo, mismo alcance de `identity.user.hotel_id` que el resto de su rol). Son un **registro sin consecuencia**: no mueven ningún semáforo.

> [!info]
> Módulo definido a partir de dos mockups externos (ajenos a este vault) que Hugo compartió como referencia visual. Antes de esta nota no existía nada equivalente en el vault.

## Qué son

### Auditoría de Presentación Personal

Es **por colaborador**. Evalúa la presentación individual de un colaborador asignado al hotel, agrupada en 4 categorías (contenido inicial; el detalle completo y editable vive en [[Reactivos de Auditoría]]):

- **Uniformidad** — uniforme completo, limpio y planchado; zapatos de seguridad reglamentarios; gafete/ID visible.
- **Higiene** — uñas, cabello, higiene personal general.
- **Seguridad** — sin joyería excesiva; uso de guantes/protección si aplica.
- **Actitud** — puntualidad al iniciar jornada; disposición y cortesía.

### Percepción de Ambiente y Recursos

Es **por hotel**, no por colaborador. Evalúa las condiciones que el hotel provee al personal asignado, agrupada en 4 categorías (ver también [[Reactivos de Auditoría]]):

- **Insumos** — químicos y materiales suficientes; aspiradoras/carritos en buen estado; lencería (sábanas/toallas) suficiente.
- **Relaciones** — trato digno y cordial del personal del hotel; comunicación efectiva con el Ama de Llaves.
- **Carga laboral** — número de habitaciones asignadas es justo.
- **Entorno** — áreas de descanso adecuadas.

> [!note] Los reactivos son un catálogo editable
> Las categorías y reactivos de arriba son el contenido inicial de [[Reactivos de Auditoría]], que administra el Administrador (agregar, renombrar, eliminar) igual que [[Posiciones]] o [[Zonas]] — no van fijos en este módulo.

## Cómo se responde cada reactivo

Cada reactivo se marca **CUMPLE**, **NO** o **N/A**.

## Cálculo del score

Cada reactivo tiene su propio **peso**, editable por el Administrador junto con el reactivo (ver [[Reactivos de Auditoría]]) — no todos valen lo mismo. Los reactivos marcados **N/A se excluyen del cálculo** (ni su peso ni su respuesta cuentan). El score es el porcentaje ponderado de los reactivos en CUMPLE sobre el total de peso respondido (CUMPLE + NO). Cada auditoría queda registrada con su score, fecha y quién la realizó, como historial — y **se puede corregir** después de guardada (por ejemplo si el Supervisor marcó algo por error); al corregir una respuesta, el score se recalcula. Cada corrección a un reactivo queda registrada aparte (qué decía antes, qué quedó después, quién y cuándo corrigió) — no solo la última corrección: el rastro completo de cambios de esa respuesta.

## Frecuencia

Ambas auditorías son **semanales**, en el mismo ciclo — ninguna se hace con más frecuencia que la otra. No hay un día fijo dentro de la semana: el Supervisor puede auditar cualquier día antes del corte semanal, y el sistema le va recordando de forma progresiva conforme se acerca el vencimiento sin que la haya hecho.

## Cobertura

El Supervisor **elige** a quién auditar esa semana. No es obligatorio cubrir a todos los colaboradores de su hotel cada semana, ni existe una lista de pendientes obligatoria.

## Mecánica de acceso

En el dashboard del Supervisor, su hotel asignado se muestra como una tarjeta (mismo patrón visual que ya usan otras tarjetas de hotel de la plataforma) con la acción **Auditar**, que ofrece dos caminos:

- **Auditar por ambiente** → abre directo la Percepción de Ambiente y Recursos de ese hotel — la tarjeta ya identifica el hotel, no hay que elegir nada más.
- **Auditoría de personal** → abre la lista del personal de ese hotel; junto a cada colaborador se indica cuánto tiempo lleva sin auditarse (según el ciclo semanal); el Supervisor elige a quién auditar de esa lista.

## Consecuencia

> [!important] Sin efecto en semáforos
> Es un registro puro: no mueve el [[Semáforo del Colaborador]], no mueve el [[Semáforo de Requisición]], y no toca el [[Indicador de Calidad]] de QA.

## Dónde se ve el resultado

El score de la Auditoría de Presentación Personal se muestra en el perfil del [[Colaborador/Colaborador|Colaborador]].

> [!warning] Pendiente
> El vault no define un lugar de la plataforma donde mostrar el score de la Percepción de Ambiente y Recursos (es por hotel, no por colaborador). Queda pendiente de definir con Hugo.

## Relacionado

- [[Hotel/Supervisor|Supervisor]]
- [[Colaborador/Colaborador|Colaborador]]
- [[Reactivos de Auditoría]]
- [[Semáforo del Colaborador]]
- [[Indicador de Calidad]]
- [[Hotel/Hotel|Hotel]]
