---
tags:
  - arquitectura
  - rol/manager-de-area
aliases:
  - Acciones Manager de Área
---

# 6. ACCIONES DEL USUARIO — MANAGER DE ÁREA

---

| Acción                                              | Resultado                                                                                            |
| --------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| Autorizar requisición                               | Estado pasa a Autorizada · Cálculo automático de urgencia · Asignación de Inspector · Reflejo en Schedule · Llega a bandeja de Reclutamiento |
| Rechazar requisición con observaciones              | Estado vuelve a En elaboración con observaciones · Notifica al Supervisor                            |
| Eliminar su borrador (Verde manzana)                 | Sin motivo obligatorio · Cada posición pasa a Morado con journal individual · Mensaje de confirmación |
| Eliminar requisición Autorizada / En proceso / Cubierta parcial (solo de su depto) | Motivo obligatorio · Solo si no tiene asignaciones activas (se liberan primero) · Cada posición pasa a Morado con journal individual · Mensaje de confirmación |
| Eliminar borrador / requisición vacía               | Eliminación física automática sin journal                                                            |
| Generar / Renovar código QR del Timesheet           | QR válido inmediatamente · Notifica a colaboradores asignados                                        |
| Editar Schedule semanal                             | Cambios persisten · Notifica al colaborador afectado                                                 |
| Mover colaborador entre días/turnos                 | Schedule actualizado · Notificación al colaborador                                                   |
| Marcar día de descanso del colaborador              | Schedule muestra día como "Descanso"                                                                 |
| Exportar Schedule semanal                           | Descarga PDF/CSV                                                                                       |
| Corregir ponche del Timesheet                       | Cambio aplicado · Justificación obligatoria · Log auditable con autor y fecha                       |
| Exportar Timesheet semanal                          | Descarga PDF/CSV                                                                                       |
| Poner colaborador en Stand-by (Rosa)                | Estado en Semáforo del Colaborador pasa a Rosa · Sin Schedule ni Timesheet hasta cambio de estado    |
| Quitar Stand-by (vuelve a estado anterior)          | Reactiva al colaborador en Schedule y Timesheet                                                      |
| Reportar colaborador (Rojo)                         | Estado pasa a Rojo · Notifica al Inspector · Inicia investigación                                    |
| Solicitar refuerzo de personal                      | Sugerencia de nueva requisición prefilled (la crea el Supervisor)                                    |
| Cancelar acción                                     | Cambios no se guardan                                                                                |
