---
tags:
  - modulo/contabilidad
aliases:
  - Consolidado Semanal del Colaborador
  - Consolidado Semanal
---

# Consolidado Semanal del Colaborador

Resumen semanal que agrupa todos los [[Timesheet|Timesheets]] de un colaborador en los hoteles donde trabajó durante la semana, aplicando el [[Core/Módulos/Contrato|pay rate]] de cada hotel para calcular el monto total a pagar.

## Origen

- El sistema genera automáticamente el Consolidado Semanal al finalizar la semana del hotel (según la configuración de inicio/fin de semana del [[Core/Módulos/Contrato|Contrato]]), a partir de los [[Timesheet|Timesheets]] de esa semana
- Si el colaborador trabajó en un solo hotel, el consolidado contiene un único Timesheet
- Si trabajó en múltiples hoteles (vía asignación temporal [[Semáforo del Colaborador|Café]]), el consolidado agrupa los Timesheets de cada hotel

## Estructura

| Campo              | Descripción                                                                    |
| ------------------ | ------------------------------------------------------------------------------ |
| Colaborador        | Nombre y datos del colaborador                                                 |
| Semana             | Número de semana y rango de fechas                                             |
| Detalle por requisición | Requisición, hotel, horas netas, pay rate del [[Core/Módulos/Contrato\|Contrato]], subtotal |
| Overtime por requisición | Horas extra calculadas según la política del contrato del hotel de esa requisición |
| Total a pagar      | Suma de subtotales de todas las requisiciones                                  |

## Cálculo

- **Por cada [[Core/Módulos/Requisicion/Requisición|requisición]] en la que trabajó:**
  - Horas netas × pay rate del contrato del hotel de esa requisición = subtotal regular
  - Horas de overtime × tasa de overtime de ese mismo contrato = subtotal overtime
- **Umbral de overtime:** se calcula **por requisición** a partir de las **40 horas brutas semanales** (8 hrs × 5 días). Las horas que excedan ese umbral dentro de una requisición se consideran overtime de esa requisición
- **Total a pagar** = Σ (subtotal regular + subtotal overtime) de todas las requisiciones
- El overtime se calcula **por requisición**, no por hotel ni de forma global. Si el colaborador trabajó en dos requisiciones del mismo hotel, cada una evalúa su umbral por separado y las horas **no se suman**

> [!info] **Festivos** — El [[Core/Módulos/Contrato|Contrato]] define el manejo y recargos por días festivos, pero la regla de cálculo de recargos por festivo aún no está definida. Al definirse, se integrará en esta sección.

### Rate interno

- El pay rate aplicado al colaborador puede ser **mayor** al rate pactado en el [[Core/Módulos/Contrato|Contrato]] del hotel (por acuerdo interno: experiencia, antigüedad o negociación con el colaborador)
- Cuando existe un rate interno, el sistema lo usa para calcular el pago al colaborador en lugar del rate contractual
- El rate interno **no se refleja** en la [[Facturación al Hotel|Factura al Hotel]] (esa siempre usa el bill rate contractual)
- Visible solo para Contabilidad ([[Manager de Contabilidad]] y [[Contadora]])

### Deducciones

- Antes de liberar el pago, el sistema aplica las [[Deducciones]] activas del colaborador
- Las deducciones (uniforme, comida, retención 16%) reducen el monto neto del cheque
- Ver [[Deducciones]] para el detalle de cada tipo y sus condiciones

### Múltiples posiciones en el mismo hotel

- Un colaborador puede tener dos o más posiciones distintas en el mismo hotel durante la misma semana (ej. Breakfast + Housekeeper)
- Cada posición tiene su propio rate
- Se presentan como **líneas separadas** en el Consolidado, cada una con sus horas y subtotal independiente

### Overtime autorizado parcialmente

- El hotel puede autorizar solo una fracción del overtime trabajado
- El sistema permite que el [[Manager de Contabilidad]] ajuste las horas OT pagables según lo autorizado por el hotel
- **Ejemplo:** colaborador trabajó 50 hrs (10 OT), hotel autoriza solo 5 OT → se pagan 5 OT al colaborador y se facturan 5 OT al hotel
- Las horas OT no autorizadas quedan registradas pero no se facturan al hotel

### Asignación del cheque

- Cuando el colaborador trabajó en múltiples hoteles durante la semana, el cheque se asigna al hotel donde acumuló **mayor cantidad de horas**
- Esta asignación es para efectos de impresión y entrega del cheque físico

## Periodo

- El Consolidado Semanal se genera al cierre de cada semana
- El periodo de pago es **semanal**, alineado al ciclo del [[Timesheet]]
- Oranje paga al colaborador; el hotel paga a Oranje según el bill rate de su [[Core/Módulos/Contrato|Contrato]]

## Visibilidad

> [!important] El Consolidado Semanal es de uso exclusivo del departamento de **Contabilidad** de Oranje. El hotel y el colaborador no tienen acceso a este documento.

El Colaborador accede, desde su app, únicamente al **historial de sus pagos ya liberados** (semana, hotel, horas, monto pagado, fecha). No tiene acceso al Consolidado Semanal completo ni al **monto del pago en curso/pendiente**, que permanecen de uso exclusivo de Contabilidad hasta su liberación. Ver [[RF-C-08 Consultar Mi Pago semanal]] y RR-C-05.

## Validación

- El sistema genera el Consolidado automáticamente al finalizar cada semana
- La [[Contadora]] revisa el Consolidado y el [[Manager de Contabilidad]] lo aprueba antes de ejecutar el pago al colaborador
- El pago no se ejecuta sin la aprobación del [[Manager de Contabilidad]]

## Relacionado

- [[Timesheet]]
- [[Core/Módulos/Contrato|Contrato]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Semáforo del Colaborador]]
- [[Contabilidad/Flujo de Nómina|Flujo de Nómina]]
- [[Manager de Contabilidad]]
- [[Contadora]]
- [[Deducciones]]
- [[Facturación al Hotel]]
- [[Vacaciones]]
