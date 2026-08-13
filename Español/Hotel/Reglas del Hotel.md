---
tags:
  - modulo/hotel
aliases:
  - Reglas del Hotel
---

# Reglas del Hotel

Consolidación de todas las reglas de negocio que aplican a los roles del Hotel dentro del sistema Oranje. Referencia cruzada con [[Reglas de Negocio]] (concentrado general del sistema).

## Jerarquías soportadas

La plataforma soporta dos modelos organizacionales para el hotel:

| Jerarquía | Estructura |
|---|---|
| **Simple** | [[Hotel/Manager General\|Manager General]] (también opera como [[Hotel/Manager de Área\|Manager de Área]]) → [[Hotel/Supervisor\|SUP]] → Colaboradores de Oranje |
| **Extendida** | [[Hotel/Manager General\|Manager General]] → [[Hotel/Manager de Área\|Manager de Área]] → [[Hotel/Supervisor\|Supervisor]] → Colaboradores de Oranje |

- En la jerarquía simple, el [[Hotel/Manager General|Manager General]] también opera como [[Hotel/Manager de Área|Manager de Área]] (misma persona, dos roles).
- Los departamentos del hotel son: **Housekeeping, Alimentos, Mantenimiento y Front Desk** (ver [[Core/Catálogos/Departamentos del Hotel|Departamentos del Hotel]]).
- En la jerarquía extendida, cada departamento tiene su propio Gerente y Supervisor(es).

## Habilitación del hotel

> [!important] El hotel solo puede generar requisiciones cuando alcanza el status **Naranja** en el [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]].

- Antes de alcanzar Naranja, el hotel es un prospecto comercial gestionado por Ventas.

## Requisiciones — creación y autorización

### Acceso al módulo

- El [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] y el [[Hotel/Supervisor|Supervisor]] tienen acceso al módulo de requisiciones.
- Usuarios sin acceso reciben el mensaje: **"No cuenta con acceso"**.

### Creación

- Cualquier rol del hotel ([[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Supervisor|Supervisor]]) crea la requisición (estado **Verde manzana** — En elaboración en el [[Core/Módulos/Semáforos/Semáforo de Requisición|Semáforo de Requisición]]).
- El número de requisición se genera automáticamente: `Año (4) + Mes (2) + Día (2) + Hora (2, 24h) + Minutos (2) + Homoclave (2 alfanuméricos)`. Ejemplo: `202604081632V1`.

### Autorización

> [!important] Solo el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]] pueden autorizar una requisición. Si el [[Hotel/Supervisor|Supervisor]] intenta hacerlo, el sistema bloquea la acción con el mensaje: **"Solo el gerente del hotel puede autorizar la requisición"**.

- Para autorizar debe existir **al menos una posición** registrada. Si no: **"No tiene posiciones registradas, registre al menos una posición e intente nuevamente"**.
- El rechazo regresa la requisición al creador con observaciones (estado **En elaboración**).

> [!info] La autorización del [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]] es una **capa de seguridad** para evitar que lleguen requisiciones falsas o incorrectas a Reclutamiento.

## Efectos automáticos al autorizar

Al autorizar una requisición, el sistema ejecuta automáticamente:

| Efecto | Detalle |
|---|---|
| Cálculo de urgencia | Por posición, según la [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición\|fórmula de urgencia]]: `> 120h` → Verde fuerte (Normal), `72–120h` → Amarillo (Medio), `< 72h` → Rojo (Urgente) |
| Transición de posiciones | De Dorado (Gold) a Naranja (Orange) en el [[Core/Módulos/Semáforos/Semáforo de Posiciones de la Requisición\|Semáforo de Posiciones]] |
| Reflejo en Schedule | Las posiciones quedan en el [[Core/Módulos/Schedule\|Schedule]] de la semana correspondiente a su fecha de inicio |
| Asignación de Inspector | El [[Inspector]] se asigna automáticamente según la zona del hotel |

## Ciclo de vida de la posición

- Cada posición tiene **fecha de inicio pero no fecha de fin** definida.
- La posición termina cuando el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] pone al colaborador en **Stand-by** (estado Rosa en el [[Semáforo del Colaborador]]).

## Eliminación de requisición

- Al confirmar la eliminación, el sistema muestra: **"Al confirmar la eliminación de la requisición, las posiciones registradas y la requisición serán eliminadas físicamente"**.
- El estado **Morado** es transversal: se alcanza desde cualquier estado cuando se elimina la requisición.
- Si una requisición no tiene posiciones al salir del editor → **eliminación física automática**.
- Al eliminar una requisición con posiciones → cada posición también pasa a Morado con journal individual.

## Gestión de personal asignado

Responsabilidades sobre los colaboradores asignados (todos los roles del hotel):

| Acción | Efecto en [[Semáforo del Colaborador]] | Descripción |
|---|---|---|
| Revisar ponches | — | Acción del [[Hotel/Supervisor\|Supervisor]]: resuelve anómalos y registra manuales. No aprueba |
| Aprobar horas | — | Solo [[Hotel/Manager de Área\|Manager de Área]] (su departamento) y [[Hotel/Manager General\|Manager General]] (cualquiera). Solo lo aprobado alimenta el [[Timesheet]] y la facturación |
| Poner en Stand-by | → **Rosa** | Espera por decisión del hotel (vacaciones, temporada baja). Sin fecha de fin; termina cuando cualquier rol del hotel cambia el estado. El colaborador queda sin Schedule ni Timesheet y no puede ponchar |
| Reportar colaborador | → **Rojo** | Inicia investigación por parte del [[Inspector]] |
| Gestionar Schedule semanal | — | Administra las asignaciones del [[Core/Módulos/Schedule\|Schedule]] del hotel |

> [!important] Todos los roles del hotel ([[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] y [[Hotel/Supervisor|Supervisor]]) pueden poner a un colaborador en estado Rosa (Stand-by).

## Timesheet y deducción de Lunch

> [!info] Las reglas de ponchado y deducción de Lunch también se documentan en [[Colaborador/Reglas del Colaborador|Reglas del Colaborador]] desde la perspectiva del colaborador.

### Dependencia

- El [[Timesheet]] se crea a partir del [[Core/Módulos/Schedule|Schedule]]; no puede existir de forma independiente.
- La semana del hotel está definida por el contrato (inicio y fin de semana).

### Jornada

- La jornada diaria es de **8 horas**
- La semana laboral es de 7 días: **5 de trabajo + 2 de descanso**
- Total semanal bruto: **40 horas** (8 hrs × 5 días)
- Total semanal neto pagable: **37.5 horas** (40 hrs − 30 min de lunch × 5 jornadas)

### Ponchado

- El colaborador poncha **desde su teléfono, dentro de la propiedad**, en el formulario de la app móvil.
- Cada marca exige **ubicación GPS dentro del perímetro del hotel**; fuera de él, el ponche se bloquea.
- Se registran **cuatro marcas por jornada**, solo dos obligatorias:
  - **Entrada** — inicio de jornada · **obligatoria** · con foto
  - **Salida a lunch** — opcional
  - **Regreso de lunch** — opcional
  - **Salida** — fin de jornada · **obligatoria** · con foto
- Los ponches **no son definitivos hasta que el [[Hotel/Manager de Área|Manager de Área]] (su departamento) o el [[Hotel/Manager General|Manager General]] (cualquiera) los aprueban**. El [[Hotel/Supervisor|Supervisor]] revisa antes, pero no aprueba. Ver [[Timesheet#Aprobación]].

### Deducción de Lunch

> [!important] Esta regla aplica a **todos** los colaboradores sin excepción, en cada jornada.

- Se deducen **30 minutos por jornada, siempre**: haya o no marcas de lunch, y sin importar su duración real. El par de lunch alimenta el [[Timesheet#Indicador de Lunch Extendido|Indicador de Lunch Extendido]], no el pago.
- **Horas brutas** = Salida − Entrada
- **Horas netas** = Horas brutas − 30 min de lunch
- Después de 6 horas continuas de trabajo, el colaborador debe tomar su lunch.

> [!info] Los **breaks** ya no se ponchan y su deducción quedó **eliminada** (2026-08-13). Ver [[Reglas de Negocio#Deducción de Breaks]].

## Indicador de Lunch Extendido

> [!note] El [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] y el [[Hotel/Supervisor|Supervisor]] **no tienen acceso** al Indicador de Lunch Extendido. Es exclusivo de roles internos de Oranje ([[Inspector]], [[Inspección/Coordinador|Coordinador]], [[Manager de Reclutamiento]]).

## Indicador de Cumplimiento del Timesheet

El sistema calcula automáticamente un indicador semaforizado (Verde / Amarillo / Rojo) que compara el cumplimiento real del colaborador contra los parámetros contractuales del hotel, evaluado por semana. Ver referencia completa: [[Core/Módulos/Semáforos/Indicador de Cumplimiento del Timesheet|Indicador de Cumplimiento del Timesheet]].

## Accidente Laboral — responsabilidades del Supervisor

El [[Hotel/Supervisor|Supervisor]] tiene un rol activo en el reporte y captura de accidentes laborales:

### Escenario A — Colaborador reporta desde la app

1. El colaborador genera el reporte desde la app.
2. La señal llega **simultáneamente** al [[Hotel/Supervisor|SUP]] y al [[Inspector]] de zona asignado.
3. El SUP acude físicamente y captura: ubicación exacta, circunstancias, testigos y atención inmediata brindada.

### Escenario B — Supervisor detecta primero

1. El [[Hotel/Supervisor|SUP]] detecta el accidente en la propiedad.
2. Crea la tarjeta de accidente desde la app.
3. La señal llega al [[Inspector]] de zona.

- En ambos escenarios, el colaborador pasa a estado **Gris** en el [[Semáforo del Colaborador]] (protección contra la regla de 3 inasistencias).
- Referencia: [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]].

## Manager General

El [[Hotel/Manager General|Manager General]] es la máxima autoridad del hotel y **siempre existe** en ambas jerarquías:

- Tiene **visibilidad global** del [[Core/Módulos/Schedule|Schedule]] y [[Timesheet]] de todos los departamentos.
- Puede crear, autorizar y rechazar requisiciones.
- Puede poner Stand-by, reportar colaboradores y reportar accidentes laborales.
- En jerarquía simple, también opera como [[Hotel/Manager de Área|Manager de Área]] (misma persona).
- Punto de contacto principal con Oranje a nivel directivo.

## Supervisión de Calidad (QA)

- Un [[QA/Operador de QA|Operador de QA]] está asignado de forma fija al departamento de Hotel.
- QA **no ejecuta** la operación del Hotel; solo observa, mide y retroalimenta.
- Las métricas específicas que el Operador de QA monitorea para Hotel están definidas en [[QA/Métricas y KPIs por Departamento#Hotel|Métricas y KPIs — Hotel]].
- Si el [[Core/Módulos/Semáforos/Indicador de Calidad|Indicador de Calidad]] del departamento alcanza estado **Rojo** sin mejora tras notificación, el Manager de QA escala a dirección.

## Resumen de responsabilidades por rol

| Acción | [[Hotel/Manager General\|Manager General]] | [[Hotel/Manager de Área\|Manager de Área]] | [[Hotel/Supervisor\|Supervisor]] |
|---|---|---|---|
| Crear requisición | Sí | Sí | Sí |
| Autorizar requisición | Sí | Sí | No |
| Rechazar requisición | Sí | Sí | No |
| Revisar ponches y resolver anómalos | No | No | Sí |
| **Aprobar horas** | Sí (cualquier departamento) | Sí (su departamento) | No |
| Poner en Stand-by (Rosa) | Sí | Sí | Sí |
| Reportar colaborador (Rojo) | Sí | Sí | Sí |
| Reportar accidente laboral | Sí | Sí | Sí |
| Gestionar Schedule semanal | Visibilidad global | Sí (su departamento) | No |
| Ver Indicador de Lunch Extendido | No | No | No |
| Visibilidad global Schedule/Timesheet | Sí | Sí (su departamento) | No |

## Relacionado

- [[Reglas de Negocio]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Supervisor|Supervisor]]
- [[Hotel/Manager General|Manager General]]
- [[Core/Módulos/Requisicion/Flujo de Requisición|Flujo de Requisición]]
- [[Core/Módulos/Semáforos/Semáforo de Requisición|Semáforo de Requisición]]
- [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición|Semáforo de Urgencia de Requisición]]
- [[Core/Módulos/Semáforos/Semáforo de Posiciones de la Requisición|Semáforo de Posiciones de la Requisición]]
- [[Semáforo del Colaborador]]
- [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Timesheet]]
- [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]
- [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Catálogos/Departamentos del Hotel|Departamentos del Hotel]]
- [[Pool de Colaboradores]]
