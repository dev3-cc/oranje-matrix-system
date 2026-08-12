---
tags:
  - arquitectura
  - rol/reclutadora
  - caso-de-uso
aliases:
  - CU RF-15
---

# 🪪 ID: RF-15
🏷️ **Nombre:** Asignar colaborador a hotel

**Historia:**
La Reclutadora encuentra un colaborador del Pool que cumple con los requisitos de una posición de la requisición. Lo asigna al hotel y la requisición actualiza su porcentaje de cobertura. La asignación bloquea al colaborador solo en las **horas** de ese turno (regla RR-05): el mismo día en otro hotel, con turno que no se solape, sí se permite.

**Criterios de aceptación:**
Al asignar, la requisición actualiza su % de cobertura en tiempo real. El colaborador queda bloqueado para otros hoteles. Se genera entrada automática en el Schedule del hotel.

**Documentación:**
- PRD: PRD-RECL-02 Reclutadora
- Flow: Asignar colaborador
- Prototipo: (link de Figma)

**Flujo:**
`Detalle de Requisición` → MANUAL → `Click "Asignar colaborador"` → `Pool filtrado por posición/zona` → `Seleccionar candidato` → AUTOMATICO → `Verifica Blacklist (RF-11)` → `Verifica solape de horas (RR-05)` → `Asigna + Actualiza cobertura + Genera Schedule (RF-16)`
