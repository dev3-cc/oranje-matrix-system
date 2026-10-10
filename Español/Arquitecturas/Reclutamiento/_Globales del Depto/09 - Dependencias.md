---
tags:
  - arquitectura
  - departamento/reclutamiento
aliases:
  - Dependencias Reclutamiento
---

# 9. DEPENDENCIAS — DEPARTAMENTO DE RECLUTAMIENTO

---

## Dependencias internas (otros módulos del sistema Oranje)

| Dependencia                      | Descripción                                                                                                                                        |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Módulo Hotel**                 | El depto Reclutamiento recibe las requisiciones autorizadas por el Manager de Área. Sin requisiciones aprobadas no hay flujo de cobertura.       |
| **Módulo Schedule**              | Al asignar un colaborador, se genera automáticamente la entrada en el Schedule del hotel. Bidireccional: Reclutamiento escribe, Schedule consulta. |
| **Módulo Timesheet**             | El Timesheet lee a los colaboradores asignados. El Manager de Reclutamiento ve el indicador de Lunch Extendido.                                    |
| **Módulo Pool de Colaboradores** | Es el repositorio central donde Reclutamiento alimenta nuevos colaboradores y desde donde consume para cubrir requisiciones.                       |
| **Módulo Blacklist**             | Consulta obligatoria antes de cada asignación. Los tres roles del depto (Reclutadora, Líder de Grupo, Manager) pueden consultar y agregar (falta grave · con motivo y evidencia, registro auditable). Los tres roles del depto también pueden levantar el veto, igual que el Administrador (`ROL-ADM-01`), con motivo obligatorio (ampliado el 2026-10-10). Las disputas son resueltas por el Inspector.                                                             |
| **Módulo Inspección**            | El Inspector investiga disputas de Blacklist y casos Rojo del Semáforo del Colaborador. Recibe escalamientos del Manager.                          |
| **Módulo Onboarding-Hotel**      | Recibe escalamientos del Manager cuando una incidencia afecta la relación comercial con el hotel.                                                  |
| **Módulo QA**                    | Supervisa al departamento de Reclutamiento mediante el Indicador de Calidad. El Operador de QA emite observaciones formales.                       |

---

## Dependencias externas (sistemas o apps fuera del módulo)

| Dependencia                     | Descripción                                                                                                                                  |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **App del Colaborador**         | El colaborador completa su Fase 2 (alta en app) y Fase 3 (datos de emergencia). El sistema recibe vía webhook o API la confirmación de alta. |
| **Sistema de Notificaciones**   | Envía notificaciones automáticas a los roles del depto ante eventos relevantes.                                                              |
| **Sistema de Auditoría / Logs** | Registra journals de requisiciones y posiciones; trazabilidad de cambios excepcionales.                                                      |

---

## Dependencias de catálogos (configuración del sistema)

| Catálogo                        | Descripción                                                                              |
| ------------------------------- | ---------------------------------------------------------------------------------------- |
| **Posiciones**                  | Housekeeper, Hoseman, Chef, Laundry, etc. Mantenido por el Administrador.                |
| **Zonas**                       | Centro, Sur, Este, Oeste, Noroeste, Sureste. Asignación de Inspectores se basa en zonas. |
| **Modalidades de Contratación** | Tiempo completo, Medio tiempo, Temporal, Según solicitud.                                |
| **Niveles de Inglés**           | Básico, Intermedio, Avanzado, Conversacional.                                            |

---

## Dependencias de roles externos

| Rol externo                                              | Cuándo interviene                                            |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| **Manager de Área**                                    | Autoriza las requisiciones que llegan al depto.              |
| **Colaborador del Gerente del Hotel (GHC)**              | Crea las requisiciones en el módulo Hotel.                   |
| **Inspector** *(módulo Inspección)*                      | Investiga disputas de Blacklist y casos Rojo.                |
| **Business Developer / BDC** *(módulo Onboarding-Hotel)* | Recibe escalamientos comerciales del Manager.                |
| **Operador de QA** *(módulo QA)*                         | Emite observaciones de calidad sobre el desempeño del depto. |
