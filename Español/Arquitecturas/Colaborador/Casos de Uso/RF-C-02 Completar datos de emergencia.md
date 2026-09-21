---
tags:
  - arquitectura
  - rol/colaborador
  - caso-de-uso
aliases:
  - CU RF-C-02
---

# 🪪 ID: RF-C-02
🏷️ **Nombre:** Completar datos de emergencia — Fase 3

**Historia:**
Una vez que el Colaborador completó la Fase 2, completa su perfil de emergencia y salud desde `/collaborator`: nombre, teléfono y parentesco del contacto de emergencia, tipo de sangre, y alergias o condiciones médicas (opcional). Al tener ambas fases completas (Fase 2 + Fase 3), el estado del colaborador queda en **Blanco** pendiente de validación por la [[Reclutadora]]. La Reclutadora valida el alta y, al aprobarla, transiciona al colaborador de Blanco → Verde fuerte (ver RF-08 de la Reclutadora). Los datos de emergencia quedan disponibles para uso en caso de [[Core/Módulos/Accidente Laboral/Accidente Laboral|accidente laboral]].

**Criterios de aceptación:**
Nombre del contacto de emergencia: obligatorio, mínimo 3 caracteres. Teléfono del contacto: obligatorio, formato de teléfono válido. Parentesco: obligatorio, catálogo (Madre / Padre / Cónyuge / Hermano/a / Hijo/a / Amigo/a / Otro). Tipo de sangre: obligatorio, catálogo (A+, A–, B+, B–, AB+, AB–, O+, O–, No sé). Alergias o condiciones médicas: opcional, máximo 500 caracteres. Al guardar, el sistema confirma: *"Datos de emergencia guardados"*. Los datos de Fase 3 complementan el alta; no cambian el estado del semáforo por sí solos. La Reclutadora es quien transiciona de Blanco → Verde fuerte al aprobar el alta completa (Fase 2 + Fase 3).

**Documentación:**
- PRD: PRD-COLAB-01
- Flow: Flujo de Reclutamiento — Alta del Colaborador
- Prototipo: (link de Figma)

**Flujo:**
`/colaborador → Sección Alta → Fase 3` → MANUAL → `Ingresa nombre del contacto de emergencia` → `Ingresa teléfono del contacto` → `Selecciona parentesco (catálogo)` → `Selecciona tipo de sangre (catálogo)` → `Ingresa alergias / condiciones médicas (opcional)` → `Guardar` → AUTOMATICO → Si campos válidos → `Datos guardados · Confirmación: "Datos de emergencia guardados" · Alta Fase 2 + Fase 3 completa · Estado permanece en Blanco · Reclutadora notificada del alta pendiente de revisión completa` / Si campo inválido o faltante → `Bloquea guardado · Muestra mensaje de error junto al campo inválido`
