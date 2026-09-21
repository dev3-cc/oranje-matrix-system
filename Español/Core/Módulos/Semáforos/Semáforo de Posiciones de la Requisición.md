---
tags:
  - modulo/core
aliases:
  - Semáforo de Posiciones de la Requisición
  - Semáforo de Posiciones
  - Status Posiciones Dorado
  - Status Posiciones Naranja
  - Status Posiciones Verde
  - Status Posiciones Amarillo
  - Status Posiciones Rojo
  - Status Posiciones Morado
  - Status Posiciones Gold
  - Status Posiciones Orange
  - Status Posiciones Green
  - Status Posiciones Yellow
  - Status Posiciones Red
  - Status Posiciones Purple
---

# Semáforo de Posiciones de la Requisición

Estado visual del porcentaje de cobertura de cada posición dentro de una [[Requisición]]. Indica qué tan cerca está cada posición de haber sido completamente cubierta por la [[Reclutadora]].

> [!info]
> Este es uno de los semáforos de la requisición. Ver también: [[Semáforo de Requisición]], [[Semáforo de Urgencia de Requisición]] y el [[Semáforo del Colaborador]].

> [!note] Equivalencia con el sistema técnico
> La documentación usa colores en español, mientras que el sistema interno los nombra en inglés: Dorado=Gold · Naranja=Orange · Verde=Green · Amarillo=Yellow · Rojo=Red · Morado=Purple. Ambos nombres son alias válidos.

## Estados

| Color    | Estado              | Responsable                                                           | Descripción                                                                                         |
| -------- | ------------------- | --------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Dorado   | En preparación      | [[Hotel/Manager General\|GM]], [[Hotel/Manager de Área\|GH]] o [[Hotel/Supervisor\|SUP]] | Posición en preparación por el hotel.                                                               |
| Naranja  | Autorizada          | [[Hotel/Manager General\|GM]] o [[Hotel/Manager de Área\|GH]]                                       | Posición autorizada por el hotel. El sistema calcula prioridad ([[Semáforo de Urgencia de Requisición]]). |
| Verde    | 100% cubierta       | [[Reclutadora]]                                                       | Posición cubierta al 100%.                                                                          |
| Amarillo | Hasta 25% faltante  | [[Reclutadora]]                                                       | Hasta 25% de personal faltante.                                                                     |
| Rojo     | Más de 25% faltante | [[Reclutadora]]                                                       | Más del 25% de personal faltante.                                                                   |
| Morado   | Eliminada           | —                                                                     | Posición eliminada físicamente.                                                                     |

> [!info] Herencia por jerarquía
> El [[Hotel/Manager de Área|GH]] hereda las atribuciones del [[Hotel/Supervisor|SUP]], y el [[Hotel/Manager General|GM]] hereda las de ambos — por eso donde el SUP prepara la posición, también puede el GH y el GM, y donde el GH autoriza, también puede el GM. El alcance no se hereda, se conserva (D-09). Ver [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]].

## Detalle por estado

### Dorado — En preparación
**Responsable:** [[Hotel/Manager General|GM]], [[Hotel/Manager de Área|GH]] o [[Hotel/Supervisor|SUP]]

La posición se crea junto con la requisición y se prepara con sus datos (perfil, cantidad, fecha de inicio).

**Avance →** cuando el [[Hotel/Manager General|GM]] o el [[Hotel/Manager de Área|GH]] autoriza la requisición completa, cada posición pasa a [[#Naranja — Autorizada|Naranja]] y el sistema calcula su prioridad.

---

### Naranja — Autorizada
**Responsable:** [[Hotel/Manager General|GM]] o [[Hotel/Manager de Área|GH]]

La posición queda lista para asignación. El sistema asocia automáticamente un nivel de urgencia (ver [[Semáforo de Urgencia de Requisición]]).

> [!note] Granularidad colaborativa
> Bajo el modelo colaborativo (RR-15), **cada posición/slot puede tener su propio reclutador asignado**: varios reclutadores participantes trabajan la misma requisición y cada uno cubre las posiciones que toma. **Asignar** o **quitar** un colaborador de una posición registra al **actor** (rol y nombre) en el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]] (RR-16). El lock de concurrencia opera a este nivel: dos reclutadores no asignan el mismo colaborador al mismo slot — gana el primero, el segundo ve "posición ya cubierta".

**Avance →** según la cobertura gestionada por los reclutadores participantes:
- 100% cubierta → [[#Verde — 100% cubierta|Verde]]
- Hasta 25% faltante → [[#Amarillo — Hasta 25% faltante|Amarillo]]
- Más del 25% faltante → [[#Rojo — Más de 25% faltante|Rojo]]

---

### Verde — 100% cubierta
**Responsable:** [[Reclutadora]]

Todos los colaboradores asignados a esta posición están confirmados.

---

### Amarillo — Hasta 25% faltante
**Responsable:** [[Reclutadora]]

Falta hasta 25% del personal. La reclutadora sigue buscando coberturas.

---

### Rojo — Más de 25% faltante
**Responsable:** [[Reclutadora]]

Falta más del 25%. Requiere atención prioritaria.

---

### Morado — Eliminada

Estado transversal. Se alcanza desde cualquier estado cuando la posición se elimina físicamente; el sistema registra journal.

## Reglas clave

- **Relación con [[Semáforo de Requisición]]:**
  - La requisición queda **Azul claro** cuando **todas** sus posiciones llegan a `Verde`.
  - La requisición queda **Rojo** si al menos una posición cierra en `Amarillo` o `Rojo`.
- **La prioridad** (Urgencia) se calcula automáticamente al pasar a `Naranja`.
- **Eliminación**: puede suceder en cualquier estado (→ `Morado`).
- **Granularidad colaborativa (RR-15):** cada posición/slot puede tener su reclutador asignado; el lock de concurrencia es por posición/slot, no por requisición completa.
- **Trazabilidad (RR-16):** asignar/quitar un colaborador de una posición registra al **actor** en el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]].

## Relacionado

- [[Requisición]]
- [[Posiciones]]
- [[Semáforo de Requisición]]
- [[Semáforo de Urgencia de Requisición]]
- [[Reclutadora]]
