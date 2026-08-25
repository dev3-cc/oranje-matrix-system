---
tags:
  - arquitectura
  - rol/reclutadora
  - caso-de-uso
aliases:
  - CU RF-07
---

# 🪪 ID: RF-07
🏷️ **Nombre:** Crear colaborador (Fase 1 — Entrevista inicial)

**Historia:**
La Reclutadora realiza una entrevista inicial al candidato. Captura datos básicos (nombre, edad, género, domicilio, teléfono) y además define Posición, Modalidad de contratación, Nivel de inglés y Nivel de experiencia *(campos agregados a Fase 1 el 2026-08-22; antes los llenaba el colaborador en Fase 2 — ver [[Core/Módulos/Reglas de Negocio#Captura de datos en 3 fases|Reglas de Negocio]])*. Crea el registro en el sistema y, tras crear, se envía link al candidato para completar Fase 2 (alta en App) y Fase 3 (datos de emergencia).

**Criterios de aceptación:**
El registro se crea con estado "Pendiente de alta en app". Se envía link al candidato por email/SMS. La Reclutadora recibe notificación cuando el candidato completa su alta.

**Documentación:**
- PRD: PRD-RECL-02 Reclutadora
- Flow: Crear colaborador
- Prototipo: (link de Figma)

**Flujo:**
`Módulo Reclutamiento → Pool` → MANUAL → `Click en "Nuevo Colaborador"` → `Llena formulario Fase 1 (datos básicos + Posición, Modalidad, Nivel de inglés, Nivel de experiencia)` → `Confirmar` → AUTOMATICO → `Crea registro + Envía link al candidato + Notifica a Reclutadora cuando completa Fase 2`
