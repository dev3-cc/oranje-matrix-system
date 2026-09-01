---
tags:
  - modulo/core
aliases:
  - Semáforo de Requisición
  - Status Requisición Verde manzana
  - Status Requisición Verde
  - Status Requisición Amarillo
  - Status Requisición Azul claro
  - Status Requisición Rojo
  - Status Requisición Morado
  - Status Requisición Apple Green
  - Status Requisición Green
  - Status Requisición Yellow
  - Status Requisición Light Blue
  - Status Requisición Red
  - Status Requisición Purple
---

# Semáforo de Requisición

Sistema de estados visuales que representa el ciclo de vida de una [[Requisición]], desde que el [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] comienzan a elaborarla hasta que queda cubierta.

> [!info]
> Este semáforo describe el **ciclo de vida general** de la requisición. Las otras dimensiones se manejan en semáforos separados: [[Semáforo de Urgencia de Requisición]] (clasificación por tiempo) y [[Semáforo de Posiciones de la Requisición]] (porcentaje de cobertura por posición). Para el estado del colaborador ver [[Semáforo del Colaborador]].

> [!note] Paralelismo con [[Semáforo de Posiciones de la Requisición]]
> Cada estado de la requisición tiene su espejo a nivel posición: `Verde manzana`↔`Dorado` · `Verde`↔`Naranja` · `Azul claro`↔`Verde` · `Rojo`↔`Amarillo/Rojo` · `Morado`↔`Morado`.

## Estados

| Color         | Estado                | Responsable                                                 | Descripción                           |
| ------------- | --------------------- | ----------------------------------------------------------- | ------------------------------------- |
| Verde manzana | En elaboración        | [[Hotel/Manager General\|GM]], [[Hotel/Manager de Área\|GH]] o [[Hotel/Supervisor\|SUP]] | En elaboración por el hotel.          |
| Verde         | Autorizada            | [[Hotel/Manager General\|GM]] o [[Hotel/Manager de Área\|GH]]                             | Autorizada por el hotel.  |
| Amarillo      | En proceso            | Reclutadores activos (varios)                               | En proceso de asignación de personal. |
| Azul claro    | Cubierta totalmente   | Reclutadores activos (varios)                               | Requisición cubierta totalmente.      |
| Rojo          | Cubierta parcialmente | Reclutadores activos (varios)                               | Requisición cubierta parcialmente.    |
| Morado        | Eliminada             | Creador ([[Hotel/Manager General\|GM]], [[Hotel/Manager de Área\|GH]] o [[Hotel/Supervisor\|SUP]]) desde Verde manzana; [[Hotel/Manager de Área\|GH]] (su departamento) o [[Hotel/Manager General\|GM]] (cualquiera) desde Verde/Amarillo/Rojo | Requisición eliminada. No existe desde Azul claro. |

## Detalle por estado

### Verde manzana — En elaboración
**Responsable:** [[Hotel/Manager General|GM]], [[Hotel/Manager de Área|GH]] o [[Hotel/Supervisor|SUP]]

El hotel inicia la creación de la requisición y sus posiciones.

**Avance →** cuando el [[Hotel/Manager General|GM]] o el [[Hotel/Manager de Área|GH]] autoriza la requisición, pasa a [[#Verde — Autorizada|Verde]]. Solo el GM o GH pueden autorizar; si lo intenta el SUP, el sistema bloquea la acción.

---

### Verde — Autorizada
**Responsable:** [[Hotel/Manager General|GM]] o [[Hotel/Manager de Área|GH]]

La requisición queda lista para asignación. El sistema calcula automáticamente la urgencia (ver [[Semáforo de Urgencia de Requisición]]).

**Avance →** una [[Reclutadora]] o [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] toma la requisición de la bandeja compartida; pasa a [[#Amarillo — En proceso|Amarillo]]. El cambio de status registra al **actor** en el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]].

> [!note] Retorno desde Amarillo
> Bajo el modelo colaborativo (RR-15), la requisición solo regresa a **Verde** (Autorizada) cuando **sale el último** reclutador participante. Mientras quede al menos un reclutador activo, permanece en Amarillo.

---

### Amarillo — En proceso
**Responsable:** Reclutadores activos (varios)

**Varios reclutadores participantes** pueden trabajar la requisición a la vez (modelo colaborativo, RR-15); no hay dueño único. El avance que muestra el semáforo **consolida el trabajo de todos** los reclutadores activos. Cada uno busca y asigna colaboradores a las posiciones (ver [[Semáforo de Posiciones de la Requisición]] y [[Semáforo del Colaborador]]). Cada cambio de status registra al **actor** en el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]].

### Decisión

**¿Se cubren todas las posiciones?**

- **SÍ →** [[#Azul claro — Cubierta totalmente|Azul claro]]
- **NO →** [[#Rojo — Cubierta parcialmente|Rojo]]

---

### Azul claro — Cubierta totalmente
**Responsable:** Reclutadores activos (varios)

Todas las posiciones llegaron a 100% (ver `Verde` en [[Semáforo de Posiciones de la Requisición]]); el resultado consolida el trabajo de todos los reclutadores participantes. El cierre registra al **actor** (quién la cerró) en el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]].

**Fin del ciclo activo** — la requisición queda cerrada satisfactoriamente.

---

### Rojo — Cubierta parcialmente
**Responsable:** Reclutadores activos (varios)

La requisición cerró con al menos una posición en `Amarillo` o `Rojo` a nivel [[Semáforo de Posiciones de la Requisición|posición]]; el resultado consolida el trabajo de todos los reclutadores participantes. El cierre registra al **actor** (quién la cerró) en el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]].

**Fin del ciclo activo** — con cobertura incompleta.

---

### Morado — Eliminada
**Responsable:** depende del estado de origen — ver reglas abajo.

Estado transversal: se alcanza desde varios estados previos cuando se elimina la requisición. Eliminar es una **transición** a Morado con registro en journal — la fila nunca se borra físicamente. Cada posición de la requisición también pasa a `Morado` con journal individual.

- **Desde [[#Verde manzana — En elaboración|Verde manzana]]:** la elimina **quien la creó** ([[Hotel/Manager General|GM]], [[Hotel/Manager de Área|GH]] o [[Hotel/Supervisor|SUP]]), **sin motivo obligatorio** — el borrador pudo crearse por error y [[Reclutamiento/Reclutamiento|Reclutamiento]] todavía no ve borradores.
- **Desde [[#Verde — Autorizada|Verde]], [[#Amarillo — En proceso|Amarillo]] o [[#Rojo — Cubierta parcialmente|Rojo]]:** la elimina el [[Hotel/Manager de Área|GH]] (solo las de **su departamento**) o el [[Hotel/Manager General|GM]] (cualquiera del hotel), con **motivo obligatorio**, y únicamente si la requisición **no tiene asignaciones activas** — primero deben liberarse (ver [[Semáforo del Colaborador]]). Es el mismo reparto con que se aprueban las horas trabajadas (D-09): mismo alcance, misma pareja.
- **Desde [[#Azul claro — Cubierta totalmente|Azul claro]]:** no se elimina — esta transición no existe.

**Fin del ciclo** — no hay retorno desde Morado.

## Relacionado

- [[Requisición]]
- [[Semáforo de Urgencia de Requisición]]
- [[Semáforo de Posiciones de la Requisición]]
- [[Semáforo del Colaborador]]
- [[Flujo de Reclutamiento]]
- [[Reclutamiento/Self-Pick de Requisiciones|Self-Pick de Requisiciones]]
- [[Reclutadora]]
- [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo de Reclutadoras]]
- [[Manager de Reclutamiento]]
- [[Hotel/Manager General|Manager General]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Supervisor|Supervisor]]
