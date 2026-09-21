---
tags:
  - arquitectura
  - rol/business-developer
  - caso-de-uso
aliases:
  - CU RF-V-08 (BD)
---

# 🪪 ID: RF-V-08
🏷️ **Nombre:** Crear Documento de T&C

**Historia:**
Cuando el hotel responde con interés a la Propuesta (status Amarillo), el BD crea el **Documento de Términos y Condiciones** con los 5 campos obligatorios definidos por RR-V-10: Pay rate, Bill rate, Overtime, Festivos, Calendario. Una vez completo, lo envía al BDC para validación. Sin este documento, no se puede iniciar la negociación formal (Rosa).

**Criterios de aceptación:**
Solo aplica en status Amarillo. Los 5 campos obligatorios son requeridos (RR-V-10). Adjuntos opcionales. Al guardar, queda como borrador editable. Al enviar al BDC, se bloquea para edición y queda pendiente de validación. El BDC puede rechazar con observaciones (vuelve a editable) o aprobar (RF-V-10).

**Documentación:**
- PRD: PRD-VENTAS-02 Business Developer
- Flow: Crear Documento de T&C
- Prototipo: (link de Figma)

**Flujo:**
`Detalle de prospecto en Amarillo → Sidebar Contratos → Click "Crear Documento de T&C"` → MANUAL → `Llena Pay rate (>0), Bill rate (>0), Overtime, Festivos (catálogo), Calendario (rango)` → `Vigencia y renovación opcional` → `Adjunta documentos opcional` → `Guardar borrador` → AUTOMATICO → `Borrador guardado` → MANUAL → `Click "Enviar al BDC para validación"` → AUTOMATICO → Si los 5 campos OK → `Documento queda pendiente de validación + Notifica al BDC + Bloquea edición` / Si falta algún campo → `Bloquea con "Complete los 5 campos obligatorios del T&C"`
