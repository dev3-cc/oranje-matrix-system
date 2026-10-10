---
tags:
  - modulo/core
aliases:
  - Semáforo del Colaborador
  - Status Colaborador Blanco
  - Status Colaborador Verde manzana
  - Status Colaborador Azul claro
  - Status Colaborador Naranja
  - Status Colaborador Verde fuerte
  - Status Colaborador Amarillo
  - Status Colaborador Café
  - Status Colaborador Rosa
  - Status Colaborador Morado
  - Status Colaborador Rojo
  - Status Colaborador Gris
  - Status Colaborador Negro
---

# Semáforo del Colaborador

Sistema de estados visuales que representa la situación actual de cada colaborador dentro de Oranje. Cada color corresponde a un estado con reglas propias de transición.

> [!info]
> Este semáforo describe al **colaborador**. Los estados de la requisición se manejan en otros semáforos: [[Semáforo de Requisición]], [[Semáforo de Urgencia de Requisición]] y [[Semáforo de Posiciones de la Requisición]].

## Estados

| Color         | Estado                                | Descripción                                                                                                                                                                        |
| ------------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Blanco        | Pre-asignación                        | El colaborador subió sus datos en la app pero aún no ha sido validado por la Reclutadora. Estado pre-validación: sin accesos habilitados, sin asignación.                          |
| Verde manzana | Día 1-2                               | Colaborador nuevo, primeros días, siempre que esté asistiendo. El [[Inspector]] verifica su llegada el día 1.                                                                      |
| Azul claro    | Día 3+                                | Ponchó en la propiedad al tercer día. El [[Inspector]] le entrega su uniforme.                                                                                                     |
| Naranja       | Fijo                                  | Pasó una semana, listo para colaborar fijamente en el hotel.                                                                                                                       |
| Verde fuerte  | Disponible                            | Disponible para asignación. Aplica a: (1) colaborador recién validado por la Reclutadora (Fase 2 aprobada, pertenece a Oranje); (2) colaborador reincorporado tras fin de asignación fija, disputa a su favor o alta médica. |
| Amarillo      | Disponible voluntario                 | En descanso de un hotel, se pone disponible para asignación temporal.                                                                                                              |
| Café          | Asignación temporal                   | Asignado temporalmente a cubrir la jornada completa o una parte de la jornada.                                                                                                     |
| Rosa          | Stand-by                              | El hotel lo mandó a descansar (vacaciones, temporada baja).                                                                                                                        |
| Morado        | No regresó                            | No asistió por causa propia.                                                                                                                                                       |
| Rojo          | Reportado                             | El hotel lo reportó; [[Inspector]] revisa el caso.                                                                                                                                 |
| Gris          | Accidentado                           | El colaborador sufrió un [[Core/Módulos/Accidente Laboral/Accidente Laboral\|accidente laboral]] y está en incapacidad médica. Protegido de [[Core/Módulos/Blacklist\|Blacklist]]. |
| Negro         | [[Core/Módulos/Blacklist\|Blacklist]] | Disputa resuelta a favor del hotel, colaborador bloqueado.                                                                                                                         |

> [!info] Herencia por jerarquía
> El [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] hereda las atribuciones de la [[Reclutadora]], y el [[Manager de Reclutamiento]] hereda las de ambos — incluidas las transiciones de este semáforo que ellos mueven. El [[Inspección/Coordinador|Coordinador]] hereda las del [[Inspector]], salvo cerrar un accidente laboral (D-32 exige ser el `inspector_id` asignado a la tarjeta). Ver [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]].

## Reglas clave

### Entradas al sistema

- **→ Blanco**: cuando el colaborador completa sus datos en la app (Fase 2 + Fase 3) y queda pendiente de validación por la Reclutadora. Sin accesos habilitados, sin asignación.
- **Blanco → Verde fuerte**: cuando la [[Reclutadora]] aprueba la validación (RF-08); también el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] o el [[Manager de Reclutamiento]], por herencia. El colaborador queda habilitado en el Pool, con accesos propagados, disponible para asignación.
	- Puede validarse con el expediente a medias, a sabiendas (confirmación explícita), falte lo que falte: lo de Fase 1 lo completa Reclutamiento con «Editar». El colaborador tiene 3 días desde su app para completar su parte (Fases 2 y 3: transporte, contacto de emergencia, tipo de sangre); vencido el plazo, su acceso se bloquea hasta completarlo, pero **el semáforo no retrocede** — sigue en `Verde fuerte`. Ver [[Core/Módulos/Reglas de Negocio#Validación con expediente incompleto|Reglas de Negocio · Validación con expediente incompleto]].
- **Verde fuerte → Verde manzana**: al ser asignado a una posición y asistir el día 1. El [[Inspector]] verifica su llegada en sitio.

### Progresión como fijo

- **Verde manzana → Azul claro**: cuando poncha en la propiedad al tercer día. En este momento el [[Inspector]] le entrega su uniforme.
- **Azul claro → Naranja**: al completar 7 días (fijo por sistema).

### Disponibilidad y asignaciones temporales

- **Naranja → Verde fuerte**: cuando el colaborador queda libre (fin de asignación fija o reincorporado).
- **Verde fuerte / Naranja / Rosa → Amarillo**: son 3 transiciones, no una. El propio colaborador lo activa desde la app, sin aprobación de ningún rol y con confirmación modal obligatoria (RR-C-02). Es autoservicio (disponible voluntario, ya sea estando libre, fijo o en descanso de un hotel). Bloqueado desde `Café` (ya tiene asignación activa) y desde `Morado`, `Rojo`, `Gris` o `Negro` — [[Arquitecturas/Colaborador/09 - Campos del Formulario|Campos del Formulario]] · [[Arquitecturas/Colaborador/10 - Validaciones|Validaciones]]. Amarillo es una declaración de disponibilidad, no una asignación; el colaborador no tiene [[Core/Módulos/Schedule|Schedule]] ni [[Timesheet]] y no puede ponchar hasta ser asignado (→ Café).
- **Verde fuerte → Café**: la [[Reclutadora]] o el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] lo asigna temporalmente (→ Café); también el [[Manager de Reclutamiento]], por herencia. Se define la duración (días asignados) al momento de la asignación. El estado se cierra automáticamente al vencer esos días; al cerrarse, vuelve a `Verde fuerte`.
- **Amarillo → Café**: mismo mecanismo de asignación temporal, pero el colaborador llegó al Pool por disponibilidad voluntaria. Al vencer los días asignados, regresa a `Amarillo` si sigue en periodo de descanso, o a `Verde fuerte` si ya no lo está.
- **Café → Verde fuerte / Amarillo (cancelación manual)**: la [[Reclutadora]] o el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] puede cancelar manualmente la asignación temporal antes de que venzan los días; también el [[Manager de Reclutamiento]], por herencia. Al cancelar, el colaborador regresa de inmediato a su estado previo (`Verde fuerte` o `Amarillo`) y queda liberado del Schedule del hotel.

### Incidencias

- **→ Morado**: el sistema lo marca cuando el colaborador no asiste sin justificación.
- **Morado → estado previo**: el sistema lo detecta cuando el colaborador vuelve a ponchar y lo regresa de inmediato a su estado previo (por ejemplo `Naranja`, si era fijo). No es una aprobación de ningún rol. Regresar de Morado **no borra** la inasistencia: cada inasistencia se registra individualmente ([[Core/Módulos/Reglas de Negocio#Inasistencia (Morado)|Reglas de Negocio → Inasistencia (Morado)]]) y sigue contando hacia la regla de 3 → Negro. Mientras está en Morado, el colaborador no puede activar `Amarillo`.
- **3 inasistencias → Negro**: [[Core/Módulos/Blacklist|Blacklist]] automático por sistema.
- **Cualquier estado → Negro (falta grave manual)**: la [[Reclutadora]], el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] o el [[Manager de Reclutamiento]] puede vetar al colaborador desde **cualquier estado**, incluido `Blanco` sin validar (así el que se postula estando vetado no vuelve a entrar). Requiere **motivo y evidencia** obligatorios; queda registro auditable ([[Core/Módulos/Blacklist|Blacklist]]).
- **Rosa**: lo activa el [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] cuando mandan al colaborador a descansar (vacaciones, temporada baja). El colaborador no tiene asignación activa, por lo tanto no tiene [[Core/Módulos/Schedule|Schedule]] ni [[Timesheet]] y no puede ponchar. Al reactivarlo, regresa a **Verde fuerte**.
- **Rojo**: lo pone el hotel ([[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Supervisor|Supervisor]]). Luego [[Inspector]] investiga el caso (también el [[Inspección/Coordinador|Coordinador]], por herencia) y el resultado lo lleva a:
	- **Negro** ([[Core/Módulos/Blacklist|Blacklist]]), o
	- **Verde fuerte** (reincorporado).
- **Negro → Blanco**: no es rehabilitación automática ni instancia de apelación — es una acción deliberada, con **motivo obligatorio**, que pueden disparar la [[Reclutadora]] (`ROL-R-01`), el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] (`ROL-R-02`), el [[Manager de Reclutamiento]] (`ROL-R-03`) o el Administrador (`ROL-ADM-01`, rol transversal; ver [[Arquitecturas/_Globales/Decisiones de Arquitectura#D-18 · El catálogo de roles: 19 filas, IDs unificados, y dos roles transversales|D-18]]). El colaborador regresa a `Blanco` — no a `Verde fuerte` — porque debe volver a pasar por la validación de la [[Reclutadora]] antes de ser asignable de nuevo, el mismo patrón de punto único de reingreso que usa el [[Semáforo Onboarding]].
- **Casos de [[Core/Módulos/Blacklist|Blacklist]]**: revisados por el [[Manager de Reclutamiento]].

### Accidente laboral

- **Cualquier estado activo → Gris**: cuando se genera un reporte de [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]. El colaborador queda fuera de la operación por causa médica.
- **Gris → Verde fuerte**: al recibir el alta médica y cerrarse la tarjeta de accidente. El colaborador queda disponible para reasignación.
- **Protección de Blacklist**: mientras el colaborador esté en estado `Gris`, las inasistencias **no cuentan** para la regla de 3 inasistencias → Negro.

## Relacionado

- [[Semáforo de Requisición]]
- [[Semáforo de Urgencia de Requisición]]
- [[Semáforo de Posiciones de la Requisición]]
- [[Flujo de Reclutamiento]]
- [[Manager de Reclutamiento]]
- [[Reclutadora]]
- [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo de Reclutadoras]]
- [[Inspector]]
- [[Inspección/Coordinador|Coordinador]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]
