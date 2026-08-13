---
tags:
  - arquitectura
  - rol/manager-reclutamiento
  - caso-de-uso
aliases:
  - CU RF-12
---

# 🪪 ID: RF-12
🏷️ **Nombre:** Agregar a Blacklist

**Historia:**
Cualquier rol del departamento de Reclutamiento (Reclutadora, Líder de Grupo o Manager) puede registrar manualmente a un colaborador en la [[Core/Módulos/Blacklist|Blacklist]] cuando ha cometido una falta grave. El Manager ejecuta este mismo flujo sin privilegios adicionales: debe proporcionar motivo del veto, descripción detallada y evidencia obligatoria. El colaborador pasa a estado Negro; el registro queda en log auditable y bloquea futuras asignaciones mientras el veto siga vigente. **Ningún rol de este departamento puede levantarlo:** eso es exclusivo de un perfil de Administrador. Las 3 inasistencias son gestionadas automáticamente por el sistema y no requieren acción del Manager. La resolución de disputas (estado Rojo) es competencia exclusiva del [[Inspector]], no del Manager.

**Criterios de aceptación:**
El formulario bloquea el envío si falta motivo, descripción (mín. 30 caracteres) o evidencia adjunta (mín. 1 archivo). Al confirmar, el colaborador pasa a Negro en el [[Semáforo del Colaborador]]. El sistema notifica al colaborador y bloquea futuras asignaciones de forma inmediata. El alta queda en log auditable con autor, fecha y evidencia. El Manager NO puede remover al colaborador de la Blacklist: levantar el veto es exclusivo de un perfil de Administrador (`ROL-ADM-01`).

**Documentación:**
- PRD: PRD-RECL-04 Manager
- Flow: Blacklist
- Prototipo: (link de Figma)

**Flujo:**
`Módulo Blacklist` → MANUAL → `Click "Agregar a Blacklist"` → `Busca colaborador en Pool (campo Colaborador)` → `Selecciona motivo del veto (Catálogo: 3 inasistencias / Disputa / Falta grave)` → `Escribe descripción detallada (mín. 30 caracteres)` → `Adjunta evidencia (mín. 1 archivo, máx. 10 MB c/u)` → `Selecciona fecha del incidente` → `Confirmar` → AUTOMATICO → `Colaborador pasa a Negro en Semáforo del Colaborador + Bloquea futuras asignaciones + Notifica al colaborador + Registra en log auditable (autor, fecha, motivo, evidencia)`
