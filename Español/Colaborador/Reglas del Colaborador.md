---
tags:
  - modulo/colaborador
aliases:
  - Reglas del Colaborador
---

# Reglas del Colaborador

Consolidación de todas las reglas de negocio que aplican al Colaborador dentro del sistema Oranje. Referencia cruzada con [[Reglas de Negocio]] (concentrado general del sistema).

## Captura de datos en 3 fases

### Fase 1 — Entrevista inicial
Capturada por la [[Reclutadora]] durante el primer contacto:

| Campo | Capturado por |
|---|---|
| Nombre completo | Reclutadora |
| Edad | Reclutadora |
| Género | Reclutadora |
| Domicilio | Reclutadora |
| Teléfono | Reclutadora |

### Fase 2 — Alta en la app
Completada por el propio Colaborador:

| Campo | Catálogo asociado |
|---|---|
| SSN | — |
| ITIN | — |
| Posición | [[Posiciones]] |
| Nivel de inglés | [[Niveles de Inglés]] |
| Nivel de experiencia | — |
| Tipo de transporte | — |
| Modalidad | [[Modalidades de Contratación]] |

> [!info]
> SSN e ITIN son **opcionales**. Si el colaborador no proporciona ni SSN ni ITIN, el sistema activa automáticamente la **retención del 16%** sobre su pago (reembolsable). Ver [[Deducciones]].

### Fase 3 — Datos de emergencia
Completada por el propio Colaborador desde la app:

| Campo | Descripción |
|---|---|
| Contacto de emergencia — nombre | Persona a contactar en caso de emergencia |
| Contacto de emergencia — teléfono | Teléfono del contacto |
| Contacto de emergencia — parentesco | Relación con el colaborador |
| Tipo de sangre | Grupo sanguíneo |
| Alergias o condiciones médicas | Información médica relevante |

## Semáforo y transiciones

El [[Semáforo del Colaborador]] define 12 estados. A continuación se documentan las reglas de transición que gobiernan el ciclo de vida del colaborador.

### Progresión estándar

| Transición | Condición | Responsable |
|---|---|---|
| → Blanco | Cuando el colaborador completa sus datos en la app (Fase 2 + Fase 3); pendiente de validación por la Reclutadora. Sin accesos habilitados. | Sistema |
| Blanco → Verde fuerte | Cuando la Reclutadora aprueba la validación (RF-08). Colaborador habilitado en el Pool, accesos propagados. | Reclutadora |
| Verde fuerte → Verde manzana | Al ser asignado a una posición y asistir el Día 1 | [[Inspector]] (verifica en sitio) |
| Verde manzana → Azul claro | Al ponchar en la propiedad el tercer día | Sistema + [[Inspector]] (entrega uniforme) |
| Azul claro → Naranja | Al completar 7 días | Sistema (automático) |
| Naranja → Verde fuerte | Al quedar libre (fin de asignación fija o reincorporado) | Sistema |

### Disponibilidad y asignaciones

- **Amarillo (Disponible voluntario):** lo activa el propio colaborador **desde la app, sin aprobación de nadie**. Es autoservicio y el **único estado que el colaborador puede activar por sí mismo**.
- **Café (Asignación temporal):** la [[Reclutadora]] o el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] asigna temporalmente al colaborador y define la duración (días asignados) al momento de la asignación. El estado se cierra automáticamente al vencer esos días; al cerrarse, vuelve a Verde fuerte o Amarillo según su estado previo. La Reclutadora o el Líder de Grupo también puede cancelar manualmente la asignación temporal antes de su vencimiento; al cancelar, el colaborador regresa de inmediato a su estado previo (Verde fuerte o Amarillo) y queda liberado del Schedule del hotel.

### Stand-by (Rosa)

- El [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] pueden poner a un colaborador en Rosa.
- Indica espera por decisión del hotel (vacaciones, temporada baja).
- La posición no tiene fecha de fin; termina cuando el [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] retira al colaborador del estado Rosa. Al salir, regresa a Verde fuerte.

## Reglas de incidencia

### Inasistencia (Morado)

- El sistema marca Morado cuando el colaborador no asiste sin justificación.
- Cada inasistencia se registra individualmente.

### Regla de 3 inasistencias

- 3 inasistencias acumuladas → [[Core/Módulos/Blacklist|Blacklist]] automático (estado Negro).
- Responsable: Sistema (no requiere acción manual).

### Reporte del hotel (Rojo)

- El [[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Supervisor|Supervisor]] activa el estado Rojo (reportado).
- El [[Inspector]] investiga el caso y resuelve hacia:
  - **Negro** ([[Core/Módulos/Blacklist|Blacklist]]), si la disputa es a favor del hotel.
  - **Verde fuerte** (reincorporado), si la disputa es a favor del colaborador.
- El [[Manager de Reclutamiento]] tiene visibilidad de los casos de Blacklist como supervisión posterior, pero la decisión la toma el [[Inspector]].

## Protección por Accidente Laboral (Gris)

- Cualquier estado activo → **Gris** cuando se genera un reporte de [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]].
- Mientras el colaborador esté en Gris, las inasistencias **no cuentan** para la regla de 3 inasistencias → Negro.
- **Gris → Verde fuerte** requiere: alta médica + cierre de tarjeta de accidente por el [[Inspector]].
- Referencia: [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]].

## Ponchado y Timesheet

> [!info] Las reglas de ponchado y deducción de Lunch también se documentan en [[Hotel/Reglas del Hotel|Reglas del Hotel]] desde la perspectiva del hotel.

### Mecanismo de ponchado

- El colaborador poncha **desde su teléfono, dentro de la propiedad**, en el formulario de la app móvil.
- Cada marca exige **ubicación GPS dentro del perímetro del hotel**; fuera de él, el ponche se bloquea.
- Se registran **cuatro marcas por jornada**, solo dos obligatorias:
  - **Entrada** — inicio de jornada · **obligatoria** · con foto tomada en el momento
  - **Salida a lunch** — opcional
  - **Regreso de lunch** — opcional
  - **Salida** — fin de jornada · **obligatoria** · con foto tomada en el momento
- El par de lunch es opcional: si no lo ponchas, se te deducen los 30 minutos igual. Sirve para medir, no para calcular tu pago
- El ponche funciona **sin señal**: la marca se guarda en el teléfono y se envía al recuperar conexión.
- Los ponches **no son definitivos hasta que el [[Hotel/Manager de Área|Manager de Área]] de su departamento o el [[Hotel/Manager General|Manager General]] los aprueban**; el [[Hotel/Supervisor|Supervisor]] los revisa antes. Ver [[Timesheet#Aprobación]].
- Si el colaborador está en la propiedad pero el GPS no lo ubica dentro del perímetro, el ponche se rechaza y el Supervisor puede registrarlo manualmente con motivo.
- El [[Timesheet]] se crea a partir del [[Core/Módulos/Schedule|Schedule]]; no puede existir de forma independiente.

### Restricción por estado del semáforo

- El colaborador solo puede ponchar si tiene un [[Timesheet]] activo, lo cual requiere estar inscrito en el [[Core/Módulos/Schedule|Schedule]] de un hotel con una asignación activa (fija o temporal)
- En estado **Rosa** (Stand-by): no hay asignación activa → no hay Schedule → no hay Timesheet → no puede ponchar
- En estado **Amarillo** (Disponible voluntario): el colaborador declaró disponibilidad, pero aún no tiene asignación → no puede ponchar
- En estado **Café** (Asignación temporal): la [[Reclutadora]] lo asignó, se genera Schedule y Timesheet → puede ponchar
- El camino para trabajar durante un descanso es: **Rosa → Amarillo → Café**. Cada transición queda registrada en el journal del [[Semáforo del Colaborador]]

### Deducción de Lunch

> [!important] Esta regla aplica a **todos** los colaboradores sin excepción, en cada jornada.

- Se deducen **30 minutos por jornada, siempre**: haya o no marcas de lunch, y sin importar cuánto haya durado en realidad.
- **Horas brutas** = Salida − Entrada
- **Horas netas** = Horas brutas − 30 min de lunch
- Después de 6 horas continuas de trabajo, el colaborador debe tomar su lunch.

> [!info] Los **breaks** ya no se ponchan y su deducción quedó **eliminada** (2026-08-13). Ver [[Reglas de Negocio#Deducción de Breaks]].

### Indicador de Lunch Extendido

> [!warning] Sin marca de fin de comida no hay duración que comparar, así que este indicador **no puede calcularse** con el mecanismo vigente.

- El sistema marca automáticamente a los colaboradores cuyo lunch excede 30 minutos.
- **Visible para:** [[Inspector]], [[Inspección/Coordinador|Coordinador]], [[Manager de Reclutamiento]].
- **No visible para:** [[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]], [[Hotel/Supervisor|Supervisor]].
- Propósito: supervisión interna de Oranje; no es punitivo de forma automática.

## Pago semanal

- El colaborador recibe pago **semanal** por parte de Oranje
- El monto se calcula a partir del [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]], que agrupa los [[Timesheet|Timesheets]] de todos los hoteles donde trabajó esa semana
- Si trabajó en múltiples hoteles, cada hotel aporta sus horas con el pay rate de su [[Core/Módulos/Contrato|Contrato]]
- El overtime se calcula **por [[Core/Módulos/Requisicion/Requisición|requisición]]**, según la política pactada en cada contrato. El umbral son 40 horas brutas semanales en cada requisición, evaluado por separado: las horas de requisiciones distintas no se suman

## Elegibilidad y Pool

- Solo entran al [[Pool de Colaboradores]] los colaboradores que pasaron el filtro y fueron aprobados por Reclutamiento.
- La [[Reclutadora]] debe consultar la [[Core/Módulos/Blacklist|Blacklist]] antes de reclutar a un candidato.
- Colaborador aprobado por la Reclutadora (RF-08) ingresa al Pool con [[Semáforo del Colaborador]] en estado Verde fuerte (Disponible).

## Supervisión de Calidad (QA)

- Un [[QA/Operador de QA|Operador de QA]] está asignado de forma fija a la supervisión del ámbito Colaborador.
- QA **no ejecuta** la gestión del Colaborador; solo observa métricas agregadas del pool y el ciclo de vida.
- Las métricas específicas que el Operador de QA monitorea para Colaborador están definidas en [[QA/Métricas y KPIs por Departamento#Colaborador|Métricas y KPIs — Colaborador]].
- Si el [[Core/Módulos/Semáforos/Indicador de Calidad|Indicador de Calidad]] del ámbito Colaborador alcanza estado **Rojo** sin mejora tras notificación, el Manager de QA escala a dirección.

## Relacionado

- [[Semáforo del Colaborador]]
- [[Reglas de Negocio]]
- [[Pool de Colaboradores]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Timesheet]]
- [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]
- [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]]
- [[Reclutamiento/Flujo de Reclutamiento|Flujo de Reclutamiento]]
- [[Core/Módulos/Requisicion/Flujo de Requisición|Flujo de Requisición]]
- [[Reclutadora]]
- [[Manager de Reclutamiento]]
- [[Hotel/Manager General|Manager General]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Supervisor|Supervisor]]
- [[Inspector]]
- [[Inspección/Coordinador|Coordinador]]
- [[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|Arquitectura Colaborador]]
