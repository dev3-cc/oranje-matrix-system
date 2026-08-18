---
tags:
  - modulo/core
  - notificaciones
aliases:
  - Catálogo de Notificaciones
  - Notificaciones
---

# Catálogo de Notificaciones

Lista consolidada de **qué evento avisa a quién**. Hasta ahora las notificaciones vivían dispersas en 107 archivos del vault, mencionadas de pasada dentro de los flujos de cada Caso de Uso — nunca juntas.

> [!warning] Este documento es una PROPUESTA extraída, no una fuente de verdad
> Cada renglón se sacó de un archivo existente y trae su origen. **Ninguno se inventó.** Pero al juntarlos aparecen huecos que ningún archivo suelto dejaba ver: eventos que notifican a un rol que no los puede accionar, y acciones importantes que no notifican a nadie. Ver la sección *Huecos*.
>
> Validar este catálogo es lo que desbloquea el módulo de notificaciones: sin él la tabla existe y nadie le escribe.

## Alcance

- **Canal: solo push** (decidido el 2026-08-18). El vault no menciona correo ni SMS en ninguna parte.
- **Plazo:** *"en menos de 1 minuto"*, que es el que el vault repite en cinco Casos de Uso.
- **Destinatario:** siempre un usuario, nunca un rol abstracto. El Colaborador también tiene renglón en `identity.user`, así que hay un solo camino.
- **Retención:** leídas **30 días**, no leídas **para siempre**.

---

## Ventas

| Código | Cuándo | A quién | Origen |
|---|---|---|---|
| `SALES_PROSPECT_CREATED` | El BD crea el perfil del hotel | BDC | RF-V-02 |
| `SALES_PROPOSAL_SENT` | El BD envía la Propuesta | BDC | RF-V-05 |
| `SALES_TERMS_CREATED` | El BD crea el Documento de T&C | BDC | RF-V-08 |
| `SALES_TERMS_VALIDATED` | El BDC valida el T&C | BD | RF-V-10 |
| `SALES_TERMS_REJECTED` | El BDC lo rechaza con observaciones | BD | RF-V-10 |
| `SALES_STALLED` | Se marca estancamiento (Café) | BDC | RF-V-17 |
| `SALES_UNBLOCKED` | El BDC desbloquea el prospecto | BD original | RF-V-18 |
| `SALES_CLOSED_RED` | Se cierra como Rojo | BD original | RF-V-18 |
| `SALES_CLIENT_BLACK` | El cliente se marca Negro | BD asignado | RF-V-20 |
| `SALES_CONVERSION_APPROVED` | El BDC aprueba la conversión a cliente | BD y BDC | RF-V-12 |
| `SALES_REJECTION_HANDLED` | Se gestiona un rechazo (Rojo) | BDC | RF-V-15 |
| `SALES_AUTO_CONVERSION` | Se dispara el trigger automático de conversión | BD asignado | Flujo de Onboarding |

## Hotel

| Código | Cuándo | A quién | Origen |
|---|---|---|---|
| `REQ_SENT_FOR_APPROVAL` | El Supervisor manda la requisición a autorizar | Manager de Área | RF-H-03 |
| `REQ_AUTHORIZED` | El GM o GH la autoriza | Supervisor | RF-H-05 |
| `REQ_REJECTED` | La rechaza con motivo | Supervisor | RF-H-06 |
| `REQ_DELETED` | Se elimina una requisición con posiciones | Supervisor | RF-H-07 |
| `REQ_TAKEN` | Una Reclutadora la toma de la bandeja | Supervisor y Manager de Área | 11 - Respuestas |
| `REQ_FULLY_COVERED` | Llega al 100% de cobertura | Supervisor y Manager de Área | 11 - Respuestas |
| `REQ_ESCALATED_DELAY` | Lleva demasiado sin cubrirse | Manager de Reclutamiento | RF-H-27 |
| `REQ_INSPECTOR_ASSIGNED` | Se autoriza y se le asigna Inspector por zona | Inspector | 11 - Respuestas |
| `WORKER_REPORTED` | El hotel reporta a un colaborador (Rojo) | Inspector de zona | RF-H-18 |
| `ACCIDENT_REPORTED_A` | El colaborador reporta desde la app | **Supervisor e Inspector, simultáneamente** | RF-H-20 |
| `ACCIDENT_REPORTED_B` | El hotel reporta el accidente | Inspector de zona | RF-H-21 |
| `SCHEDULE_EDITED` | Se edita el Schedule de la semana | colaboradores asignados | RF-H-12 |
| `PUNCH_CORRECTED` | El Supervisor corrige un ponche | colaborador | RF-H-14 |
| `STANDBY_APPLIED` | El hotel manda a descansar (Rosa) | colaborador y Manager de Área | RF-H-17 |
| `FILE_COMMENTED` | Se comenta un expediente | Gerente de Departamento y destinatarios adicionales | RF-H-26 |

## Reclutamiento

| Código | Cuándo | A quién | Origen |
|---|---|---|---|
| `WORKER_PENDING_REVIEW` | El colaborador completa la Fase 2 | Reclutadora | RF-07 |
| `WORKER_VALIDATED` | La Reclutadora valida el alta | colaborador | RF-08 |
| `WORKER_BLACKLISTED` | Se agrega a Blacklist | colaborador | RF-12 |
| `REQ_PARTICIPANT_JOINED` | Otra Reclutadora se une a la requisición | los demás participantes | RF-39 |
| `REQ_REASSIGNED` | Se reasigna a otra Reclutadora | Reclutadora origen y destino, y ambos Líderes | RF-37 · RF-EXC-03 |
| `COVERAGE_CLOSURE_REVIEWED` | El Líder aprueba o rechaza el cierre | Reclutadora | RF-05 |
| `INCIDENT_RESOLVED` | Se resuelve una incidencia | involucrados | RF-30 |
| `INCIDENT_ESCALATED` | Se escala a Comercial | BD/BDC del hotel | RF-31 |
| `STATUS_FORCED` | El Manager fuerza un cambio de semáforo | Líder y Reclutadora afectados | RF-21 |
| `UNASSIGN_REQUESTED` | Se solicita desasignar un colaborador | hotel | RF-18 |
| `REASSIGN_REQUESTED` | Se solicita reasignación | Manager | RF-17 |
| `TEAM_CHANGED` | Cambia la relación Líder–Reclutadora | usuario y Líder afectado | RF-29 |
| `REPORT_SENT` | El Líder envía su reporte | Manager | RF-25 |

## Inspección y QA

| Código | Cuándo | A quién | Origen |
|---|---|---|---|
| `DISPUTE_RESOLVED` | El Inspector resuelve la disputa | involucrados: hotel, Reclutadora, Líder | RF-13 · RF-30 |
| `QUALITY_RED_NO_IMPROVEMENT` | Un departamento sigue en Rojo tras la notificación | dirección (lo escala el Manager de QA) | Indicador de Calidad |
| `QUALITY_STATE_CHANGED` | Cambia el Indicador de Calidad de un área | Manager de QA y el responsable del área | 11 - Respuestas |

## Colaborador

Los que ve en su sección *Notificaciones* — [[Arquitecturas/Colaborador/Casos de Uso/RF-C-09 Consultar notificaciones|RF-C-09]] los lista completos.

| Código | Cuándo |
|---|---|
| `WORKER_VALIDATED` | Su alta fue aprobada |
| `WORKER_VALIDATION_REJECTED` | Su alta fue rechazada o necesita correcciones |
| `WORKER_ASSIGNED` | Nueva asignación: hotel, posición y fechas |
| `WORKER_STATE_CHANGED` | Cambió su estado en el [[Semáforo del Colaborador]] |
| `WORKER_TEMP_ENDED` | Terminó su asignación temporal |
| `ACCIDENT_CARD_CLOSED` | Se cerró su tarjeta de accidente |
| `PUNCH_REMINDER` | Recordatorio de ponche |

---

## Operación y pago — propuestos el 2026-08-18

Los seis que faltaban. Al juntar el catálogo se vio que **tres acciones importantes no avisaban a nadie**, y las tres tienen consecuencia directa sobre el dinero o sobre el ciclo de vida del colaborador.

El criterio que los ordena: **se le avisa a quien puede hacer algo.**

| Código | Cuándo | A quién | Por qué |
|---|---|---|---|
| `HOURS_APPROVED` | El Manager de Área o el Manager General aprueba las horas | colaborador | Es lo que libera su pago |
| `HOURS_PENDING` | Cierre semanal con horas sin aprobar | **Manager de Área** | Él es quien puede aprobar |
| `HOURS_STILL_PENDING` | Sigue sin aprobarse | **Manager General** | Puede aprobar lo que el otro no aprobó |
| `PAYMENT_RELEASED` | Se libera el pago | colaborador | Con el monto neto |
| `ABSENCE_RECORDED` | Se registra una inasistencia | colaborador | **Con el contador: "van 2 de 3"** |
| `ABSENCE_WARNING` | Segunda inasistencia | Reclutadora | Es su colaborador |

### Por qué "en espera" no va al colaborador

Porque **no puede hacer nada al respecto**. Sus horas quedaron sin aprobar por una decisión del hotel, y avisarle solo le genera angustia sin darle una acción. Quien tiene el botón es el Manager de Área, y a él sí se le avisa.

> [!warning] `HOURS_STILL_PENDING` roza una regla — y no la rompe
> [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] es explícito: *"no hay aprobación automática y el plazo no vence"*, y *"tampoco hay escalamiento automático"*.
>
> **Avisar no es escalar.** La responsabilidad no se transfiere: el Manager de Área sigue siendo el responsable, y el Manager General no queda obligado a nada. Solo se le informa que hay algo que puede aprobar — que es una facultad que el mismo documento ya le da: *"el Manager General puede aprobar en cualquier momento lo que el Manager de Área no haya aprobado"*.
>
> Si en la práctica esto se vuelve un escalamiento de hecho, hay que decidirlo como regla de negocio, no dejarlo pasar como notificación.

### Por qué el aviso de inasistencia es el que más importa

El vault dice que *"cada inasistencia se registra individualmente"* y que **tres acumuladas vetan automáticamente**. Sin aviso, una consecuencia seria le llega a la persona sin que hubiera tenido oportunidad de corregir.

Por eso el contador va **dentro del mensaje** —*"van 2 de 3"*— y no como un aviso genérico: el número es la advertencia.

La [[Reclutadora]] entra en la segunda porque el colaborador es suyo, y le da margen para hablar con él antes de que el sistema lo vete solo.

> [!important] Depende de algo que hoy no funciona
> `ABSENCE_RECORDED` necesita que `operations.timesheet_day.is_absence` se llene, y hoy **nadie lo llena** — es el pendiente 23 del ADR. Sin eso no hay contador y el aviso no se puede mandar.

### Lo que NO se avisa, y es deliberado

**El consolidado autorizado.** Contabilidad valida y autoriza en dos pasos internos, pero para el colaborador eso no significa nada todavía: el dinero no se ha movido. Se le avisa cuando el pago **se libera**, que es lo que su pantalla *Mi Pago* ya distingue como *"Pagado"* frente a *"En cálculo"*.

---

## Los cuatro patrones que aparecieron al juntarlas

**1. Casi siempre viene con "+ Registra en log".** Notificar y auditar son hermanos, no lo mismo: el [[Arquitecturas/_Globales/Journal - Modelo de Datos|journal]] guarda **qué pasó** y es inmutable; la notificación guarda **quién debe saberlo** y se marca como leída.

**2. Un evento puede tener varios destinatarios.** *"Notifica simultáneamente al Supervisor e Inspector de zona"* (RF-H-20). Por eso es **una fila por destinatario**, no una fila con una lista: cada quien la lee en su momento.

**3. La notificación casi siempre lleva contexto.** *"con link al expediente"*, *"con la solicitud específica"*, *"con investigación adjunta"*. No es un texto suelto: apunta a algo.

**4. El ida y vuelta es el patrón dominante en Ventas.** El BD hace, el BDC valida, y el BDC responde. De los 12 eventos de Ventas, 7 son de ese tipo.

---

## Huecos

**a) Eventos que el vault menciona pero sin destinatario claro.** *"Notifica a involucrados"* aparece en RF-30 y en 09 - Campos de formulario, y en un lugar se aclara que son *"Líder, Reclutadora, Inspector, hotel"*. En el otro, no.

**b)** ~~Acciones importantes que no notifican a nadie.~~ **RESUELTO** el 2026-08-18 con los seis tipos de *Operación y pago*: las horas aprobadas y en espera, el pago liberado, y las inasistencias con su contador.

**c) `PUNCH_REMINDER` no tiene regla.** RF-C-09 lo lista con un *"si aplica"* entre paréntesis. Falta definir a qué hora se manda y con qué condición.

**d) La auto-asignación a las 24 horas.** [[Reglas de Reclutamiento]] dice que una requisición sin tomar se asigna sola a la Reclutadora con menor carga. No dice si se le avisa — y es el caso donde más falta hace, porque le llegó trabajo que no pidió.

## Relacionado

- [[Arquitecturas/Colaborador/Casos de Uso/RF-C-09 Consultar notificaciones|RF-C-09 Consultar notificaciones]]
- [[Arquitecturas/_Globales/Journal - Modelo de Datos|Journal]]
- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]]
