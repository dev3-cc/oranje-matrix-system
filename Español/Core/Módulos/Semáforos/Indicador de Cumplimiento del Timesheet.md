---
tags:
  - modulo/core
aliases:
  - Indicador de Cumplimiento del Timesheet
  - Indicador de Cumplimiento
  - Status Cumplimiento Verde
  - Status Cumplimiento Amarillo
  - Status Cumplimiento Rojo
---

# Indicador de Cumplimiento del Timesheet

Indicador calculado automáticamente que compara el cumplimiento real del colaborador contra los parámetros contractuales del hotel, evaluado por semana.

> [!info]
> Este indicador toma como input los parámetros del [[Core/Módulos/Contrato|Contrato]] y los datos del [[Timesheet]]. Ver también: [[Semáforo del Colaborador]].

## Estructura de navegación

- **Año → Semanas** (numeradas según calendario e inicio/fin de semana del hotel)
- Al seleccionar una semana, el sistema muestra automáticamente **fecha de inicio y fin** (ej. semana 37 = mié 9 sep 2026 → mar 15 sep 2026)
- Una matriz interna determina el rango de días disponibles a partir de año + semana + inicio/fin de semana del hotel

## Estados

| Color    | Estado       | Descripción                                                                              |
| -------- | ------------ | ---------------------------------------------------------------------------------------- |
| Verde    | Cumplimiento | Todo dentro de lo contractual                                                            |
| Amarillo | Alerta       | Desviación moderada (ej. 6/5 días trabajados, 43/40 hrs, 1/2 días de descanso)           |
| Rojo     | Anomalía     | Situación que no debería ocurrir (ej. ponche en día previo al alta del colaborador)       |
| Gris     | Sin datos    | Días previos al alta del colaborador en esa semana (no puede haber ponche)                |

## Comparación contractual vs. real

El sistema compara automáticamente los siguientes indicadores por semana:

| Indicador              | Ejemplo   |
| ---------------------- | --------- |
| Días trabajados / requeridos    | 5/5       |
| Días de descanso / requeridos   | 2/2       |
| Horas trabajadas / requeridas   | 40/40     |

Diferencias detectadas:
- Horas de más
- Horas faltantes
- Días extra trabajados
- Días de descanso no tomados

## Caso de ingreso a media semana

Si el colaborador inicia a media semana, el sistema **prorratea automáticamente** los días restantes del ciclo semanal.

- **Ejemplo:** el hotel solicita un colaborador el miércoles para que inicie el jueves → quedan 6 días en esa semana → el sistema calcula **4 de trabajo + 2 de descanso**
- Los días previos al alta se marcan en **Gris** (no se espera ponche ni actividad)
- Si existe un ponche en un día previo al alta → **Rojo** (anomalía)

> [!important] No puede haber registro de ponche en un día donde el colaborador aún no estaba dado de alta en el hotel.

## Reglas clave

- **Cálculo automático por sistema**
- **Input:** parámetros del [[Core/Módulos/Contrato|Contrato]] + datos del [[Timesheet]]
- **Sin intervención humana**: el sistema evalúa al cierre de cada semana
- **Evaluación semanal** (versión actual)

## Relacionado

- [[Timesheet]]
- [[Semáforo de Revisión del Día]]
- [[Semáforo de Aprobación de la Semana]]
- [[Core/Módulos/Contrato|Contrato]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Semáforo del Colaborador]]
