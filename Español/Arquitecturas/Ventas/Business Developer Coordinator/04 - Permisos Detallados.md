---
tags:
  - arquitectura
  - rol/bdc
aliases:
  - Permisos Detallados BDC
---

# PERMISOS DETALLADOS POR ROL

## ROL-V-02 · 🧑‍💼 Business Developer Coordinator (BDC)

---

| Módulo                | Funcionalidad                      | Permiso       | Descripción                                                    |
| --------------------- | ---------------------------------- | ------------- | -------------------------------------------------------------- |
| **Pipeline**          | Ver Pipeline global del territorio | 👁️ Ver       | Todos los BDs supervisados                                     |
| Pipeline              | Identificar prospecto              | ➕ Crear       | Eventual (no es su acción principal)                           |
| Pipeline              | Crear perfil del hotel             | 📝 C · E      | Eventual                                                       |
| Pipeline              | Avanzar a Amarillo                 | 📝 C · E      | Junto al BD                                                    |
| Pipeline              | Iniciar negociación (Rosa)         | 📝 C · E      | Junto al BD                                                    |
| Pipeline              | Marcar Rojo                        | —             | Sin acceso (RR-V-06 — exclusivo BD)                            |
| Pipeline              | Marcar Café                        | 📝 C · E      | Compartido con BD                                              |
| Pipeline              | Desbloquear Café                   | 🔍 Investigar | Exclusivo (RR-V-04)                                            |
| Pipeline              | Reactivar desde Café               | 📝 C · E      | Vuelve a Azul Claro (RR-V-07)                                  |
| Pipeline              | Comentar al expediente             | 📝 C · E      | Visible para el BD                                             |
| Pipeline              | Reasignar prospecto a otro BD      | 📝 C · E      | Excepcional                                                    |
| **Contratos**          | Ver contratos y sus T&C                 | 👁️ Ver       | Todos los del territorio                                       |
| Contratos              | Crear Documento de T&C             | ➕ Crear       | Compartido con BD                                              |
| Contratos              | Editar Documento de T&C            | 📝 C · E      | —                                                              |
| Contratos              | ✓ Validar T&C                      | ✓ Aprobar     | **Acción exclusiva — sí final** (RR-V-15)                      |
| Contratos              | ✗ Rechazar T&C con observaciones   | ✗ Rechazar    | Devuelve al BD                                                 |
| **Conversión**        | Crear Usuario del Hotel            | ➕ Crear       | **Precondición exclusiva** (RR-V-02)                           |
| Conversión            | ✓ Aprobar conversión               | ✓ Aprobar     | **Sí final exclusivo** (RR-V-01)                               |
| **Mi Equipo**         | Ver BDs a cargo                    | 👁️ Ver       | Lista con métricas                                             |
| Mi Equipo             | Ver métricas individuales por BD   | 👁️ Ver       | Detalle de desempeño                                           |
| Mi Equipo             | Comunicar con BD                   | 📝 C · E      | Chat / nota interna                                            |
| Mi Equipo             | Solicitar reporte específico       | ➕ Crear       | —                                                              |
| **Clientes Activos**  | Ver clientes activos               | 👁️ Ver       | Solo lectura comercial (RR-V-12)                               |
| Clientes Activos      | Marcar cliente Negro               | 📝 C · E      | **Exclusivo** (RR-V-05)                                        |
| Clientes Activos      | Reactivar desde Negro              | 📝 C · E      | **Exclusivo** — vuelve a Azul Claro (RR-V-07)                  |
| **Reportes**          | Generar reporte de Ventas          | ➕ Crear       | Plantillas de pipeline / conversión / desempeño / Café / Negro |
| Reportes              | Exportar (CSV / PDF / Excel)       | 👁️ Ver       | —                                                              |
| Reportes              | Enviar a dirección                 | ➕ Crear       | Email / link interno                                           |
| Reportes              | Programar envío recurrente         | 📝 C · E      | Semanal / mensual                                              |
| Reportes              | Ver histórico de reportes          | 👁️ Ver       | —                                                              |
| **Dashboard**         | Ver KPIs personales                | 👁️ Ver       | Mis métricas                                                   |
| Dashboard             | Ver KPIs globales del territorio   | 👁️ Ver       | Embudo, heatmap, ranking BDs                                   |
| **Sistema** (transv.) | Recibir notificación               | 👁️ Ver       | Alertas críticas                                               |
