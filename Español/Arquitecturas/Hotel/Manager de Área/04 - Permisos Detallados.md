---
tags:
  - arquitectura
  - rol/manager-de-area
aliases:
  - Permisos Detallados Manager de Área
---

# PERMISOS DETALLADOS POR ROL

## ROL-H-02 · 🧑‍💼 Manager de Área / Gerente de Departamento

---

| Módulo                | Funcionalidad                                       | Permiso       | Descripción                                                                                                                                          |
| --------------------- | --------------------------------------------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Requisiciones**     | Ver requisiciones del depto                         | 👁️ Ver        | Vista por estado (pendientes, autorizadas, en proceso, cubiertas, rechazadas)                                                                       |
| Requisiciones         | **Crear requisición**                                | ➕ Crear       | **NUEVO** — Compartido con Supervisor y Manager General                                                                                              |
| Requisiciones         | Editar borrador de requisición                       | 📝 C · E      | Antes de enviar a autorización                                                                                                                       |
| Requisiciones         | Autorizar requisición                                | ✓ Aprobar     | Compartido con Manager General — RR-H-02                                                                                                             |
| Requisiciones         | Rechazar con observaciones                          | ✗ Rechazar    | Devuelve al creador con motivo                                                                                                                       |
| Requisiciones         | Eliminar requisición                                | 📝 C · E      | Su borrador, sin motivo. Autorizada / En proceso / Cubierta parcial **de su departamento**, con motivo obligatorio y sin asignaciones activas — compartido con Manager General (alcance de todo el hotel). Cada posición pasa a Morado con journal individual (RR-H-08) |
| **Schedule**          | Ver Schedule del depto                              | 👁️ Ver        | Calendario semanal completo del depto                                                                                                                |
| Schedule              | Editar Schedule semanal                             | 📝 C · E      | Reordena turnos, mueve colaboradores entre días, marca descansos                                                                                    |
| Schedule              | Exportar Schedule                                    | 👁️ Ver        | PDF / CSV                                                                                                                                             |
| **Timesheet**         | Ver Timesheet del depto                             | 👁️ Ver        | Tabla semanal con 6 ponches por jornada                                                                                                               |
| Timesheet             | Generar / Renovar QR                                 | ➕ Crear       | Acción del Manager de Área y Manager General — RR-H-09                                                                                               |
| Timesheet             | Corregir ponche                                      | 📝 C · E      | Con justificación obligatoria — log auditable                                                                                                        |
| Timesheet             | Ver Indicador de Cumplimiento                       | 👁️ Ver        | Semaforizado por colaborador y jornada                                                                                                               |
| Timesheet             | Ver Indicador de Lunch Extendido                    | —             | Sin acceso (RR-H-15)                                                                                                                                 |
| Timesheet             | Exportar Timesheet                                   | 👁️ Ver        | PDF / CSV                                                                                                                                             |
| **Mi Personal**       | Ver colaboradores asignados                          | 👁️ Ver        | Lista con Semáforo del Colaborador                                                                                                                   |
| Mi Personal           | Poner colaborador en Stand-by (Rosa)                 | 📝 C · E      | Compartido con Supervisor y Manager General — RR-H-11                                                                                                |
| Mi Personal           | Reportar colaborador (Rojo)                          | 🚨 Reportar   | Compartido con Supervisor y Manager General — RR-H-10                                                                                                |
| Mi Personal           | Ver historial del colaborador                       | 👁️ Ver        | Asignaciones previas + incidencias                                                                                                                   |
| **Accidentes**        | Ver accidentes del depto                             | 👁️ Ver        | Lista de casos abiertos y cerrados                                                                                                                   |
| Accidentes            | **Reportar accidente — Escenario A**                 | ➕ Crear       | **NUEVO** — Acude tras notificación simultánea con Supervisor e Inspector. Captura presencial                                                        |
| Accidentes            | **Reportar accidente — Escenario B**                 | ➕ Crear       | **NUEVO** — Detecta accidente y crea tarjeta. Notifica al Inspector                                                                                  |
| Accidentes            | Capturar evidencia presencial                       | 📝 C · E      | Foto / video / notas                                                                                                                                 |
| **Dashboard**         | Ver KPIs del depto                                   | 👁️ Ver        | Cobertura, requisiciones, accidentes, cumplimiento                                                                                                   |
| **Sistema** (transv.) | Recibir notificación                                 | 👁️ Ver        | Alertas críticas y notificaciones automáticas                                                                                                        |
