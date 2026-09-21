---
tags:
  - arquitectura
  - rol/colaborador
  - caso-de-uso
aliases:
  - CU RF-C-01
---

# 🪪 ID: RF-C-01
🏷️ **Nombre:** Completar alta en `/collaborator` — Fase 2

**Historia:**
El Colaborador recibe acceso a la plataforma (`/collaborator`) después de que la [[Reclutadora]] captura sus datos iniciales y define Posición, Modalidad de contratación, Nivel de inglés y Nivel de experiencia en la entrevista (Fase 1) *(cambiado el 2026-08-22; antes estos cuatro campos los llenaba el colaborador aquí)*. Ahora debe completar su perfil desde `/collaborator` ingresando su Tipo de transporte. De forma opcional puede ingresar su SSN y/o ITIN; si lo hace, se habilita la carga del documento de SSN/ITIN (foto o PDF de la tarjeta, también opcional pero recomendada). Si no proporciona ni SSN ni ITIN, el sistema le muestra un aviso informándole que se aplicará una retención del 16% sobre su pago (reembolsable) y le permite continuar sin bloquearlo; tiene **3 días desde su alta** para cargarlo antes de un aviso interceptor (día 4) y la suspensión de su acceso (día 5) hasta que contacte a Oranje (ver [[Reglas del Colaborador#Plazo de SSN/ITIN]]). Al enviar el formulario, el sistema guarda los datos y notifica a la Reclutadora que hay un alta pendiente de revisión. El colaborador queda en espera de validación.

**Criterios de aceptación:**
SSN, ITIN y el documento de SSN/ITIN son **opcionales**. Si el colaborador no proporciona ni SSN ni ITIN, el sistema muestra un aviso: *"Sin SSN ni ITIN se aplicará una retención del 16% sobre tu pago (reembolsable) — ver [[Deducciones]]"*; el envío **no se bloquea**. Si proporciona ITIN, no se aplica la retención (ITIN es un TaxID). Si ingresa SSN o ITIN: SSN válido: formato XXX-XX-XXXX; ITIN válido: formato 9XX-XX-XXXX; ambos se enmascaran tras el ingreso y se almacenan cifrados (RNF-C-03). Si ingresa SSN o ITIN, el campo de carga de documento se habilita (opcional · recomendado); si se carga, debe ser JPG, PNG o PDF con tamaño máximo de 10 MB. Tipo de transporte es el único campo **obligatorio** de este formulario; sin él, el sistema bloquea el envío y muestra mensaje de error junto al campo inválido. Posición, Nivel de inglés, Nivel de experiencia y Modalidad **ya no se capturan aquí**: los define la Reclutadora en la Fase 1. Al enviar, el estado del colaborador transita a → **Blanco (Pre-asignación)**. La Reclutadora recibe notificación de alta pendiente. El colaborador ve confirmación: *"Tu alta fue enviada. Te avisaremos cuando sea revisada"*.

**Documentación:**
- PRD: PRD-COLAB-01
- Flow: Flujo de Reclutamiento — Alta del Colaborador
- Prototipo: (link de Figma)

**Flujo:**
`/colaborador → Login → Dashboard` → MANUAL → `Sección Alta → Fase 2` → `Ingresa SSN (opcional, enmascarado) y/o ITIN (opcional, enmascarado)` → Si ingresa SSN/ITIN → `Se habilita campo Adjuntar documento de SSN/ITIN (JPG/PNG/PDF, máx. 10 MB — opcional · recomendado)` / Si no ingresa SSN ni ITIN → `Sistema muestra aviso: "Sin SSN ni ITIN se aplicará una retención del 16% sobre tu pago (reembolsable). Tienes 3 días para cargarlo." · El colaborador puede continuar` → `Selecciona Tipo de transporte (catálogo · obligatorio)` → `Enviar` → AUTOMATICO → Si campo obligatorio válido → `Estado → Blanco · SSN/ITIN y documento cifrados en almacenamiento (si fueron proporcionados) · Retención 16% activada automáticamente si no hay SSN ni ITIN (ver [[Deducciones]]) · Notificación push a la Reclutadora: "Alta pendiente de revisión" · Confirmación al colaborador: "Tu alta fue enviada"` / Si Tipo de transporte inválido o faltante → `Bloquea envío · Muestra mensaje de error junto al campo inválido`
