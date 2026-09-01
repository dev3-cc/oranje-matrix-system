---
tags:
  - arquitectura
  - rol/manager-de-area
aliases:
  - Respuestas Sistema Manager de Área
---

# 7. RESPUESTAS DEL SISTEMA — MANAGER DE ÁREA

---

| Evento                                                | Respuesta del Sistema                                                                                                |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Manager autoriza requisición                          | Cambia estado a Autorizada · Calcula urgencia · Asigna Inspector por zona · Refleja posiciones en Schedule · Envía a bandeja de Reclutamiento (Self-Pick) · Notifica al Supervisor |
| Manager rechaza requisición                           | Cambia estado a En elaboración · Notifica al Supervisor con observaciones · Queda en bandeja del Supervisor para corrección |
| Manager elimina su borrador (Verde manzana)           | Sin motivo obligatorio · Cada posición pasa a Morado con journal individual · Estado de la requisición a Morado |
| Manager elimina requisición Autorizada / En proceso / Cubierta parcial (solo de su depto) | Requiere motivo y sin asignaciones activas · Cada posición pasa a Morado con journal individual · Estado de la requisición a Morado · Mensaje: *"Al confirmar la eliminación de la requisición, las posiciones registradas y la requisición serán eliminadas físicamente"* |
| Sistema elimina físicamente requisición vacía         | Sin journal · Sin notificación (automático)                                                                          |
| Manager genera / renueva QR                           | QR válido inmediatamente · Notifica a colaboradores asignados · Queda en log con fecha de generación                 |
| Manager corrige ponche                                | Cambio aplicado · Log auditable con autor, fecha, motivo · Notifica al colaborador del cambio                        |
| Manager edita Schedule                                | Cambios persisten · Notifica al colaborador afectado · Recalcula Indicador de Cumplimiento                          |
| Manager pone colaborador en Stand-by (Rosa)           | Estado en Semáforo del Colaborador a Rosa · Sin Schedule ni Timesheet · Notifica al colaborador y al Supervisor    |
| Manager quita Stand-by                                 | Estado vuelve a estado anterior · Reactivación en Schedule y Timesheet                                              |
| Manager reporta colaborador (Rojo)                    | Estado a Rojo · Notifica al Inspector de zona · Inicia investigación · Queda en log auditable                       |
| Sistema asigna Inspector por zona automáticamente     | Notifica al Inspector con datos del hotel y la requisición · Visible en el detalle de la requisición                 |
| Reclutamiento toma una requisición autorizada         | Notifica al Manager de Área: *"Tu requisición fue tomada por [Reclutadora]"* · Cambia estado a En proceso          |
| Reclutamiento asigna colaborador                      | Notifica al Manager de Área · Colaborador aparece en Schedule semanal · Actualiza % de cobertura                  |
| Requisición queda cubierta al 100%                    | Notifica al Manager de Área: *"Tu requisición fue cubierta al 100%"* · Cambia estado a Cubierta                    |
| Colaborador completa su jornada con cumplimiento Rojo | Alerta visible en Dashboard · Notifica al Manager de Área · Sugerencia de corrección                              |
| Indicador de Cumplimiento del depto cae a Rojo        | Notifica al Manager de Área y al Manager General (jerarquía extendida)                                            |
| Sesión iniciada                                        | Redirige a Dashboard con KPIs cargados                                                                                |
| Sesión expirada                                        | Redirige a login                                                                                                       |
| Error en validación de formulario                     | Muestra mensajes de error claros junto a cada campo inválido                                                          |
