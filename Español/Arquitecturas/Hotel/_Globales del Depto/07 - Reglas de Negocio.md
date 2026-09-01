---
tags:
  - arquitectura
  - departamento/hotel
aliases:
  - Reglas de Negocio Hotel
---

# 8. REGLAS DE NEGOCIO — DEPARTAMENTO DE HOTEL

---

| ID         | Regla                                            | Descripción                                                                                                                                                                                                                       |
| ---------- | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **RR-H-01** | Habilitación del hotel                          | El hotel **solo puede generar requisiciones** cuando alcanza el status **Naranja** en el [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]]. Antes es prospecto comercial gestionado por Ventas.                  |
| **RR-H-02** | Capa de seguridad — autorización compartida      | El **Manager de Área** o el **Manager General** pueden autorizar una requisición. Si el Supervisor intenta hacerlo, el sistema bloquea con: *"Solo el gerente del hotel puede autorizar la requisición"*.                          |
| **RR-H-03** | Posición mínima para autorizar                   | Una requisición debe tener **al menos 1 posición registrada** para poder autorizarse. Sin posiciones, el sistema bloquea con: *"No tiene posiciones registradas, registre al menos una posición e intente nuevamente"*.            |
| **RR-H-04** | Numeración automática de requisiciones           | Formato `AAAAMMDDHHMM + Homoclave (2 alfanuméricos)`. Ejemplo: `202604081632V1`. Mismo formato para tarjetas de accidente laboral.                                                                                                |
| **RR-H-05** | Cálculo automático de urgencia al autorizar      | Por posición, según el [[Semáforo de Urgencia de Requisición]]: `>120h` → Verde fuerte (Normal), `72-120h` → Amarillo (Medio), `<72h` → Rojo (Urgente).                                                                            |
| **RR-H-06** | Asignación automática de Inspector por zona     | Al autorizarse una requisición, el sistema asigna automáticamente el [[Inspector]] correspondiente según la zona del hotel.                                                                                                       |
| **RR-H-07** | Eliminación física de requisición sin posiciones | Si una requisición no tiene posiciones al salir del editor, se ejecuta **eliminación física automática** sin journal.                                                                                                              |
| **RR-H-08** | Eliminación de requisición — por estado y estado Morado transversal | En **Borrador (Verde manzana)**, la elimina quien la creó (Supervisor, Manager de Área o Manager General), sin motivo. En **Autorizada, En proceso o Cubierta parcialmente**, la elimina el Manager de Área (solo su departamento) o el Manager General (cualquiera del hotel), con motivo obligatorio y solo sin asignaciones activas. En **Cubierta totalmente**, no se elimina. Se alcanza el estado **Morado** en el Semáforo de Requisición desde cualquier estado previo, con journal. Cada posición también pasa a Morado con journal individual.                              |
| **RR-H-09** | Generación de QR (Manager de Área + Manager General) | Tanto el Manager de Área como el Manager General pueden generar / renovar el código **QR** del Timesheet para que los colaboradores ponchen.                                                                                  |
| **RR-H-10** | Reporte de colaborador (Rojo) compartido         | Los **3 roles del Hotel** (Supervisor, Manager de Área, Manager General) pueden reportar a un colaborador (estado **Rojo - Reportado** en [[Semáforo del Colaborador]]). Esta acción inicia investigación del [[Inspector]].      |
| **RR-H-11** | Stand-by (Rosa) compartido                       | Los **3 roles del Hotel** pueden poner a un colaborador en **Stand-by (Rosa)**. El colaborador queda sin Schedule ni Timesheet y no puede ponchar hasta que se cambie el estado.                                                  |
| **RR-H-12** | Acceso al módulo de requisiciones restringido    | El **Manager General**, el **Manager de Área** y el **Supervisor** tienen acceso al módulo de requisiciones. Usuarios sin acceso reciben el mensaje: *"No cuenta con acceso"*.                                                    |
| **RR-H-13** | Soporte de jerarquía simple y extendida          | El sistema soporta dos modelos: **Simple** (Manager General opera también como Manager de Área → SUP → Colaboradores) y **Extendida** (Manager General → Manager de Área por departamento → Supervisor → Colaboradores).         |
| **RR-H-14** | Manager General siempre existe                   | El Manager General **siempre existe**, en ambas jerarquías. En simple, opera también como Manager de Área (misma persona, dos roles). En extendida, supervisa a los Managers de Área de cada departamento.                       |
| **RR-H-15** | Indicador de Lunch Extendido restringido         | Los 3 roles del Hotel **NO tienen acceso** al Indicador de Lunch Extendido. Es exclusivo de roles internos de Oranje ([[Inspector]], [[Inspección/Coordinador\|Coordinador]], [[Manager de Reclutamiento]]).                       |
| **RR-H-16** | Deducción de Lunch (mínimo 30 min)               | Aplica a todos los colaboradores en cada jornada. Lunch <30 min → 30 min (mínimo obligatorio); Lunch ≥30 min → tiempo real; sin ponche de Lunch → 30 min (auto-deducción). Tras 6h continuas debe tomar Lunch.                     |
| **RR-H-17** | Jornada y semana laboral                         | Jornada diaria: **8 horas**. Semana: 5 días de trabajo + 2 de descanso. Bruto semanal: 40 horas. Neto pagable: **37.5 horas** (40 − 30 min de Lunch × 5 jornadas).                                                                  |
| **RR-H-18** | Ciclo de vida de la posición                     | Cada posición tiene fecha de inicio pero **no fecha de fin definida**. La posición termina cuando alguno de los 3 roles del Hotel pone al colaborador en **Stand-by (Rosa)**.                                                    |
| **RR-H-19** | Reporte de accidentes — compartido               | Los **3 roles del Hotel** (Supervisor, Manager de Área, Manager General) pueden reportar accidentes laborales. **Escenario A** (colaborador reporta desde la app → señal simultánea a los 3 + Inspector → acude el más cercano y captura presencial); **Escenario B** (cualquier rol del Hotel detecta primero → crea tarjeta → señal al Inspector). |
| **RR-H-20** | Estado Gris al accidente                         | En ambos escenarios de accidente, el colaborador pasa a estado **Gris (Accidentado)** en el [[Semáforo del Colaborador]], lo que lo protege de la regla de 3 inasistencias mientras se investiga.                                  |

---

## Referencias cruzadas

- [[Hotel/Reglas del Hotel|Reglas del Hotel]] — fuente de verdad operativa
- [[Reglas de Negocio]] — concentrado general del sistema
- [[Core/Módulos/Requisicion/Requisición|Requisición]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Timesheet]]
- [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]]
- [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]]
- [[Semáforo de Requisición]]
- [[Semáforo del Colaborador]]
