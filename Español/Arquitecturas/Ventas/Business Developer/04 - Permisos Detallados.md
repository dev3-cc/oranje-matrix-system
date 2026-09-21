---
tags:
  - arquitectura
  - rol/business-developer
aliases:
  - Permisos Detallados Business Developer
---

# PERMISOS DETALLADOS POR ROL

## ROL-V-01 · 🤝 Business Developer (BD)

---

| Módulo                | Funcionalidad                       | Permiso  | Descripción                               |
| --------------------- | ----------------------------------- | -------- | ----------------------------------------- |
| **Pipeline**          | Ver Pipeline (mi territorio)        | 👁️ Ver  | Solo prospectos de mi territorio asignado |
| Pipeline              | Ver Pipeline global                 | —        | Sin acceso (solo BDC)                     |
| Pipeline              | Identificar prospecto (Gris)        | ➕ Crear  | Acción inicial del rol                    |
| Pipeline              | Crear perfil del hotel (Azul Claro) | 📝 C · E | Datos básicos                             |
| Pipeline              | Registrar visita en frío            | ➕ Crear  | Documenta interacción presencial          |
| Pipeline              | Registrar intentos de contacto      | ➕ Crear  | Llamadas / emails / visitas               |
| Pipeline              | Avanzar a Amarillo (interés)        | 📝 C · E | Cambio de status con motivo               |
| Pipeline              | Iniciar negociación (Rosa)          | 📝 C · E | Junto al BDC                              |
| Pipeline              | Marcar rechazo (Rojo)               | 📝 C · E | Con motivo obligatorio                    |
| Pipeline              | Reactivar desde Rojo                | 📝 C · E | Vuelve a Azul Claro                       |
| Pipeline              | Marcar estancamiento (Café)         | 📝 C · E | El BDC se encarga del desbloqueo          |
| Pipeline              | Desbloquear Café / Reactivar Café   | —        | Sin acceso (RR-V-04 — exclusivo BDC)      |
| Pipeline              | Marcar / Reactivar Negro            | —        | Sin acceso (RR-V-05 — exclusivo BDC)      |
| Pipeline              | Aprobar conversión                  | —        | Sin acceso (RR-V-01 — exclusivo BDC)      |
| **Propuestas**        | Ver propuestas                      | 👁️ Ver  | Mis propuestas                            |
| Propuestas            | Elaborar Propuesta Personalizada    | ➕ Crear  | En status Verde (RR-V-09)                 |
| Propuestas            | Editar borrador de propuesta        | 📝 C · E | Antes de enviar                           |
| Propuestas            | Enviar propuesta al hotel           | ➕ Crear  | Cambio a estado "Enviada"                 |
| Propuestas            | Duplicar propuesta como plantilla   | ➕ Crear  | Para usar en otro prospecto               |
| **Contratos**          | Ver contratos y sus T&C                  | 👁️ Ver  | Mis T&C                                   |
| Contratos              | Crear Documento de T&C              | ➕ Crear  | En status Amarillo (RR-V-10)              |
| Contratos              | Editar borrador del T&C             | 📝 C · E | Antes de enviar al BDC                    |
| Contratos              | Enviar T&C al BDC para validación   | ➕ Crear  | Solicita validación final                 |
| Contratos              | Validar T&C                         | —        | Sin acceso (exclusivo BDC)                |
| **Conversión**        | Crear Usuario del Hotel             | —        | Sin acceso (exclusivo BDC)                |
| Conversión            | Aprobar conversión                  | —        | Sin acceso (exclusivo BDC)                |
| **Mi Territorio**     | Ver mis rutas y zonas               | 👁️ Ver  | Mapa con prospectos                       |
| Mi Territorio         | Planear ruta del día                | 📝 C · E | Marcar prospectos a visitar               |
| **Clientes Activos**  | Ver clientes activos (referente)    | 👁️ Ver  | Solo lectura — sin operación (RR-V-12)    |
| Clientes Activos      | Registrar contacto comercial        | ➕ Crear  | Visita de cortesía / follow-up            |
| **Dashboard**         | Ver KPIs personales                 | 👁️ Ver  | Mis prospectos, propuestas, conversiones  |
| **Sistema** (transv.) | Recibir notificación                | 👁️ Ver  | Alertas y notificaciones                  |
