---
tags:
  - modulo/core
aliases:
  - Timesheet
---

# Timesheet

Registro de tiempo trabajado por un colaborador en un hotel. Se crea a partir del [[Core/Módulos/Schedule|Schedule]] y se alimenta con los ponchados del colaborador durante la semana.

## Origen

- Cada colaborador inscrito en el [[Core/Módulos/Schedule|Schedule]] del hotel tiene su propio timesheet.
- El schedule es el que abre la puerta a la creación del timesheet.

## Ponchado

El colaborador poncha **desde su teléfono, dentro de la propiedad**, en el formulario de la app móvil. Se registran **cuatro marcas por jornada**, de las cuales solo dos son obligatorias:

- **Entrada** — inicio de jornada · **obligatoria** · con foto
- **Salida a lunch** — opcional
- **Regreso de lunch** — opcional
- **Salida** — fin de jornada · **obligatoria** · con foto

El par de lunch es **opcional a propósito**: si no se poncha, se deducen los 30 minutos igual. Existe para **medir**, no para calcular el pago.

Cada marca exige **ubicación GPS dentro del perímetro del hotel**; fuera de él, el ponche se bloquea. La foto se toma con la cámara en ese momento, no se admite una imagen guardada. El ponche funciona sin señal: se guarda en el teléfono y se envía al recuperar conexión, y la validación del perímetro ocurre al sincronizar.

Esto contabiliza el tiempo trabajado del colaborador.

## Aprobación

Los ponches **no son definitivos hasta que el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]] los aprueban**, en un calendario donde revisan las jornadas completas de la [[Core/Módulos/Requisicion/Requisición|requisición]].

- La aprobación es **por departamento**: el Manager de Área —también llamado **Hotel Manager**— aprueba las horas del suyo; el Manager General, las de cualquiera
- Son los **dos únicos roles** que pueden aprobar horas
- La aprobación cubre todos los días de la requisición
- Solo los ponches aprobados alimentan este timesheet y, en consecuencia, el [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] y la facturación
- Antes de eso, el [[Hotel/Supervisor|Supervisor]] **revisa sin aprobar**: resuelve los ponches anómalos (fuera del perímetro, sin foto, o con desfase entre captura y sincronización) y registra los manuales
- Si nadie aprueba antes del cierre semanal, las horas **quedan en espera de aprobación**: no se pagan ni se facturan, y no hay aprobación automática

## Overtime

El umbral es de **40 horas brutas semanales por colaborador en cada [[Core/Módulos/Requisicion/Requisición|requisición]]**. Lo que exceda esas 40 horas cuenta como overtime dentro de esa misma requisición.

Si el colaborador trabaja en más de una requisición en la semana, el umbral se evalúa en cada una por separado; las horas no se suman entre requisiciones.

Las 8 horas diarias no son un tope aparte: salen de 40 ÷ 5. El overtime se genera de dos maneras que caen en la misma bolsa de 40 horas — **un día más largo o un sexto día**.

Quien autoriza rebasar las 40 horas es el **[[Hotel/Manager de Área|Manager de Área]]**, y solo él. El disparador es el ponche que cruza las 40 horas acumuladas de esa semana en esa requisición.

## Uso por el hotel

En otra parte del panel, el hotel puede ver cuánto va a tener que pagar al finalizar la semana por todas las horas trabajadas registradas en los timesheets.

## Deducción de Lunch

El sistema aplica una deducción de lunch al cómputo de horas del colaborador en cada jornada. Después de 6 horas continuas de trabajo, el colaborador debe tomar su lunch.

### Regla de deducción

Se deducen **30 minutos por jornada, siempre**: haya o no marcas de lunch, y sin importar cuánto haya durado en realidad. Quien se toma 50 minutos cobra igual que quien se toma 20.

> [!info] La regla aplica a **todos** los colaboradores sin excepción. Ver [[Reglas de Negocio#Deducción de Lunch]] para la definición formal.

### Cálculo de horas pagables

- **Horas brutas** = Salida − Entrada
- **Horas netas** = Horas brutas − 30 min de lunch
- La deducción de lunch aplica en cada jornada sin excepción

> [!info] Los **breaks** ya no se ponchan y su deducción quedó **eliminada** (2026-08-13). Con la deducción de lunch fija, un par de marcas que no descuenta nada no aportaba al cálculo. Ver [[Reglas de Negocio#Deducción de Breaks]].

### Indicador de Lunch Extendido

> [!info] Este indicador **revive** con la marca de *Regreso de lunch* (2026-08-13). Ya hay duración que comparar contra los 30 minutos.
>
> Dos cosas que hay que tener presentes al leerlo: **no descuenta nada** —la deducción sigue siendo fija en 30— y, al ser el par de lunch opcional, **solo ponchan los cumplidos**, justo los que no se pasan. El indicador ve menos de lo que busca.

El sistema marca automáticamente a los colaboradores cuyo lunch excede 30 minutos.

- **Quién lo ve:** [[Inspector]], [[Inspección/Coordinador|Coordinador]], [[Manager de Reclutamiento]]
- **Quién NO lo ve:** [[Hotel/Manager de Área|Manager de Área]], [[Hotel/Supervisor|Supervisor]]
- **Qué muestra:** colaborador, hotel, fecha, tiempo de lunch real
- **Cuándo se activa:** cuando el tiempo de lunch registra más de 30 minutos

> [!important] Este indicador es una herramienta de supervisión interna de Oranje. El hotel no tiene visibilidad sobre los tiempos de lunch individuales de los colaboradores.

## Relacionado

- [[Core/Módulos/Schedule|Schedule]]
- [[Core/Módulos/Requisicion/Requisición|Requisición]]
- [[Hotel/Supervisor|Supervisor]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Reclutadora]]
- [[Reglas de Negocio]]
- [[Inspector]]
- [[Inspección/Coordinador|Coordinador]]
- [[Manager de Reclutamiento]]
