---
tags:
  - arquitectura
  - rol/manager-de-area
  - caso-de-uso
aliases:
  - CU RF-H-07
---

# 🪪 ID: RF-H-07
🏷️ **Nombre:** Eliminar requisición con posiciones

**Historia:**
Cuando una requisición Autorizada, En proceso o Cubierta parcialmente ya tiene posiciones registradas pero debe cancelarse (cambio de planes del hotel, error grave detectado tras autorización, decisión de no cubrir), el Manager de Área (solo las de su departamento) o el Manager General (cualquiera del hotel) la elimina manualmente. A diferencia de la eliminación física automática (sin journal) de requisiciones vacías, esta requiere justificación y deja journal individual por cada posición.

**Criterios de aceptación:**
El Manager de Área (solo las de su departamento) o el Manager General (cualquiera del hotel) pueden eliminar requisiciones Autorizadas, En proceso o Cubiertas parcialmente con posiciones. La justificación es obligatoria y la requisición no debe tener asignaciones activas (se liberan primero). Cada posición pasa al estado **Morado** (transversal — RR-H-08) con journal individual. La requisición también pasa a Morado. Mensaje de confirmación: *"Al confirmar la eliminación de la requisición, las posiciones registradas y la requisición serán eliminadas físicamente"*. Notifica al Supervisor.

**Documentación:**
- PRD: PRD-HOTEL-03 Manager de Área
- Flow: Eliminación de requisición con posiciones
- Prototipo: (link de Figma)

**Flujo:**
`Sidebar → Requisiciones → Detalle de requisición` → MANUAL → `Click "Eliminar requisición"` → `Justificación obligatoria (mín. 20 caracteres)` → `Marca checkbox de confirmación` → `Confirmar` → AUTOMATICO → `Estado de cada posición a Morado + Journal individual por posición + Estado de requisición a Morado + Notifica al Supervisor + Registra en log auditable`
