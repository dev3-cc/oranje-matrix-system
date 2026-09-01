---
tags:
  - arquitectura
  - departamento/hotel
aliases:
  - Criterios de Aceptación Hotel
---

# ✅ CRITERIOS DE ACEPTACIÓN — DEPARTAMENTO DE HOTEL

---

| #          | Criterio                                                                                                                                       |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **AC-H-01** | El hotel solo puede crear requisiciones cuando alcanza status Naranja en el Semáforo Onboarding (RR-H-01).                                    |
| **AC-H-02** | El formulario de creación de requisición no puede enviarse a autorización sin al menos 1 posición registrada (RR-H-03).                        |
| **AC-H-03** | El sistema bloquea la autorización si el rol no es Manager de Área, mostrando: *"Solo el gerente del hotel puede autorizar la requisición"* (RR-H-02). |
| **AC-H-04** | El número de requisición sigue el formato `AAAAMMDDHHMM + Homoclave` y se genera automáticamente al crearse (RR-H-04).                         |
| **AC-H-05** | Al autorizar la requisición, el sistema calcula automáticamente la urgencia por posición (>120h Verde, 72-120h Amarillo, <72h Rojo) (RR-H-05). |
| **AC-H-06** | Al autorizar la requisición, el sistema asigna automáticamente el Inspector según zona del hotel en menos de 30s (RR-H-06).                    |
| **AC-H-07** | Al autorizar la requisición, las posiciones aparecen en el Schedule semanal del hotel en menos de 30s (RI-H-02).                               |
| **AC-H-08** | La requisición autorizada llega a la bandeja de Reclutamiento (Self-Pick) en menos de 30s (RI-H-01).                                           |
| **AC-H-09** | Si la requisición no tiene posiciones al salir del editor, se elimina físicamente sin journal (RR-H-07).                                       |
| **AC-H-10** | En Borrador, la elimina quien la creó, sin motivo. En Autorizada / En proceso / Cubierta parcial, la elimina el Manager de Área (su depto) o el Manager General (cualquiera del hotel), con motivo obligatorio y sin asignaciones activas. En Cubierta totalmente, no se elimina. Cada posición pasa a estado Morado con journal individual (RR-H-08).                               |
| **AC-H-11** | Solo el Manager de Área puede generar / renovar el QR del Timesheet (RR-H-09).                                                                |
| **AC-H-12** | El QR generado es válido inmediatamente; los colaboradores pueden ponchar al instante.                                                          |
| **AC-H-13** | Solo el Manager de Área puede reportar a un colaborador (Rojo); la acción notifica al Inspector y inicia investigación (RR-H-10).            |
| **AC-H-14** | Manager de Área y Supervisor pueden poner en Stand-by (Rosa); el colaborador queda sin Schedule ni Timesheet (RR-H-11).                       |
| **AC-H-15** | El Indicador de Lunch Extendido NO es visible para roles del Hotel (RR-H-15).                                                                   |
| **AC-H-16** | La deducción de Lunch aplica a todos los colaboradores: Lunch <30 min → 30 min mínimo; sin ponche → 30 min auto-deducción (RR-H-16).            |
| **AC-H-17** | El Indicador de Cumplimiento del Timesheet se calcula por colaborador y por jornada (Verde / Amarillo / Rojo).                                 |
| **AC-H-18** | El Supervisor puede reportar accidentes en escenarios A y B; el colaborador pasa a Gris en el Semáforo del Colaborador (RR-H-19, RR-H-20).      |
| **AC-H-19** | Toda corrección de ponche por el Manager de Área queda en log auditable con autor, fecha y motivo obligatorio (RNF-H-03).                    |
| **AC-H-20** | El Manager General no puede autorizar requisiciones; solo comentar y escalar (RR-H-14).                                                        |
| **AC-H-21** | El sistema notifica al rol correspondiente en menos de 1 minuto tras un evento relevante (Notificaciones).                                     |
| **AC-H-22** | La interfaz es responsive: Manager de Área y Manager General desde 7"; Supervisor desde 5" (mobile, RNF-H-04).                                |
| **AC-H-23** | La disponibilidad mensual del módulo es del 99.5% (RNF-H-02).                                                                                   |
| **AC-H-24** | Cada cambio de estado en una requisición o colaborador queda registrado en el journal con fecha, autor y motivo.                               |
| **AC-H-25** | Al rechazar una requisición, las observaciones del Manager de Área son visibles para el Supervisor en el detalle.                            |
