---
tags:
  - modulo/core
aliases:
  - Reglas de Negocio
  - Business Rules
  - Reglas del Sistema
---

# Reglas de Negocio

Documento centralizado con las reglas de negocio que gobiernan el sistema Oranje. Cada regla referencia el módulo donde se aplica y los roles involucrados.

> [!info] Las reglas aquí documentadas son la fuente de verdad. Los semáforos y flujos implementan estas reglas, pero este archivo las define.

## Colaborador

> [!tip] Para la referencia completa, ver [[Reglas del Colaborador]].

### Captura de datos en 3 fases
- **Fase 1 — Entrevista inicial:** la [[Reclutadora]] captura los datos básicos del candidato (nombre completo, edad, género, domicilio y teléfono) y además define **Posición, Modalidad de contratación, Nivel de inglés y Nivel de experiencia** — son decisiones de Oranje, no del colaborador *(cambiado el 2026-08-22; antes los llenaba el colaborador en Fase 2)*
- **Fase 2 — Alta en la plataforma (`/empleado`):** el propio Colaborador completa Tipo de transporte (obligatorio); SSN, ITIN y el documento de SSN/ITIN son opcionales; si no proporciona SSN ni ITIN, se activa automáticamente la retención del 16% sobre su pago (reembolsable) — ver [[Deducciones]]. Tiene **3 días desde su alta** para cargar SSN o ITIN (ver *Plazo de SSN/ITIN* abajo). También puede subir, de forma **opcional**, su comprobante de domicilio como documento del expediente; no bloquea la validación *(decisión del 2026-08-22)*
- **Fase 3 — Datos de emergencia:** el Colaborador completa desde `/empleado`: contacto de emergencia (nombre, teléfono, parentesco), tipo de sangre y alergias o condiciones médicas (opcional)

### Plazo de SSN/ITIN
- El colaborador tiene **3 días desde su alta** para cargar SSN o ITIN en `/empleado`, con recordatorio por notificación los días 1 a 3
- **Día 4:** al entrar a `/empleado` se le muestra un aviso interceptor — ya debió cargarlo por temas fiscales; la retención del 16% sigue activa (reembolsable)
- **Día 5:** su acceso a la plataforma **se suspende** hasta que contacte a Oranje ([[Customer Service/Customer Service|Customer Service]]); el desbloqueo lo hace Oranje tras verificar. La suspensión es solo de acceso: datos e historial no se pierden
- La retención del 16% es independiente del plazo: aplica mientras no haya SSN/ITIN verificado

### Progresión del colaborador (Semáforo)
- **→ Blanco:** cuando el colaborador completa sus datos en la app (Fase 2 + Fase 3) y queda pendiente de validación por la Reclutadora. Sin accesos habilitados, sin asignación.
- **Blanco → Verde fuerte:** cuando la Reclutadora aprueba la validación (RF-08). Colaborador habilitado en el Pool, disponible para asignación.
- **Verde fuerte → Verde manzana:** al ser asignado a una posición y asistir el Día 1. El [[Inspector]] verifica su llegada en sitio
- **Verde manzana → Azul claro:** cuando poncha en la propiedad al tercer día. El [[Inspector]] le entrega su uniforme
- **Azul claro → Naranja:** al completar 7 días (automático por sistema)
- **Naranja → Verde fuerte:** cuando el colaborador queda libre (fin de asignación fija o reincorporado)

### Estado Amarillo (Disponible voluntario)
- Lo activa el propio colaborador durante un descanso
- Es el único estado que el colaborador puede activar por sí mismo

### Asignación temporal (Café)
- La [[Reclutadora]] o el [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] asigna temporalmente al colaborador (→ Café) y define la duración en días al momento de la asignación
- El estado se cierra automáticamente al vencer los días asignados; al cerrarse, vuelve a Verde fuerte o Amarillo según su estado previo
- La Reclutadora o el Líder de Grupo puede cancelar manualmente la asignación temporal antes de su vencimiento; al cancelar, el colaborador regresa de inmediato a su estado previo (Verde fuerte o Amarillo) y queda liberado del Schedule del hotel

### Estado Rosa (Stand-by)
- El [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] pueden poner a un colaborador en estado Rosa
- Indica que el colaborador está en espera por decisión del hotel (vacaciones, temporada baja)
- La posición no tiene fecha de fin; termina cuando el [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] retira al colaborador del estado Rosa. Al salir de Rosa, el colaborador regresa a Verde fuerte.

### Inasistencia (Morado)
- El sistema marca Morado cuando el colaborador no asiste sin justificación
- Cada inasistencia se registra individualmente

### Regla de 3 inasistencias
- 3 inasistencias acumuladas → [[Core/Módulos/Blacklist|Blacklist]] automático (estado Negro)
- Aplica al [[Semáforo del Colaborador]]
- Responsable: Sistema (automático)

### Resolución de Reportes (Rojo)
- El hotel ([[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Supervisor|Supervisor]]) activa el estado Rojo (reportado)
- El [[Inspector]] de la zona investiga la disputa y tiene **autoridad propia para decidir** el resultado:
  - **Negro** ([[Core/Módulos/Blacklist|Blacklist]]), si la disputa es a favor del hotel
  - **Verde fuerte** (reincorporado), si la disputa es a favor del colaborador

### Protección por Accidente Laboral (Gris)
- Cualquier estado activo → Gris cuando se genera un reporte de accidente laboral
- Protege al colaborador de la regla de 3 inasistencias; las inasistencias durante incapacidad no cuentan
- Gris → Verde fuerte: requiere alta médica + cierre de tarjeta de accidente por el [[Inspector]]
- Referencia: [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]]

## Estructura organizacional del hotel

### Dos jerarquías soportadas
- La plataforma soporta dos configuraciones jerárquicas para el hotel, según su tamaño y complejidad:
  - **Jerarquía simple:** [[Hotel/Manager General|Manager General]] (también opera como [[Hotel/Manager de Área|Manager de Área]]) → [[Hotel/Supervisor|SUP]] → Colaboradores de Oranje
  - **Jerarquía extendida:** [[Hotel/Manager General|Manager General]] → [[Hotel/Manager de Área|Manager de Área]] → [[Hotel/Supervisor|Supervisor]] → Colaboradores de Oranje

### Departamentos del hotel
- Los [[Departamentos del Hotel]] son: Housekeeping, Alimentos, Mantenimiento y Front Desk
- En la jerarquía extendida, cada departamento tiene su propio Gerente y Supervisor(es)
- Las [[Posiciones]] solicitadas en las requisiciones corresponden a un departamento específico del hotel

### Manager General
- El [[Hotel/Manager General|Manager General]] es la máxima autoridad del hotel y **siempre existe** en ambas jerarquías
- En jerarquía simple, también opera como [[Hotel/Manager de Área|Manager de Área]] (misma persona, dos roles)
- Puede crear, autorizar y rechazar requisiciones
- Tiene visibilidad global del [[Core/Módulos/Schedule|Schedule]] y [[Timesheet]] de todos los departamentos
- Punto de contacto principal con Oranje a nivel directivo

## Requisición y Autorización

### Acceso al sistema
- El [[Hotel/Manager General|Manager General]] (GM), el [[Hotel/Manager de Área|Manager de Área]] (GH) y el [[Hotel/Supervisor|Supervisor]] (SUP) tienen acceso al módulo de requisiciones
- Usuarios sin acceso reciben mensaje: "No cuenta con acceso"

### Creación y elaboración
- Cualquier rol del hotel ([[Hotel/Manager General|Manager General]], [[Hotel/Manager de Área|Manager de Área]] o [[Hotel/Supervisor|Supervisor]]) crea la requisición (estado Verde manzana — En elaboración)
- El número de requisición se genera automáticamente: Año (4 dígitos) + Mes (2) + Día (2) + Hora (2, formato 24h) + Minutos (2) + Homoclave (2 caracteres alfanuméricos aleatorios). Ejemplo: `202604081632V1`
- El mismo formato aplica para el número de posición

### Autorización de Requisición
- Solo el [[Hotel/Manager General|Manager General]] (GM) o el [[Hotel/Manager de Área|Manager de Área]] (GH) pueden autorizar una requisición
- El SUP recibe mensaje de bloqueo: "Solo el gerente del hotel puede autorizar la requisición"
- Debe existir al menos una posición para poder autorizar; de lo contrario: "No tiene posiciones registradas, registre al menos una posición e intente nuevamente"
- El rechazo regresa la requisición al creador con observaciones (estado "En elaboración")

### Al autorizar
- El sistema calcula automáticamente la urgencia de cada posición
- Cada posición pasa de Dorado a Naranja y el sistema calcula su prioridad
- Las posiciones autorizadas quedan reflejadas en el [[Core/Módulos/Schedule|Schedule]] de la semana correspondiente a su fecha de inicio
- El [[Inspector]] en la cabecera se asigna automáticamente según la [[Core/Catálogos/Zonas|zona]] del hotel

### Fórmula de Urgencia
- Se calcula por posición: `fecha de autorización de la requisición` vs `fecha de inicio de la posición`
- `> 120 horas` → Verde fuerte (Normal)
- `72 – 120 horas` → Amarillo (Medio)
- `< 72 horas` → Rojo (Urgente)
- El sistema reevalúa automáticamente y ajusta el color conforme avanza el tiempo
- Referencia: [[Semáforo de Urgencia de Requisición]]

### Cobertura de Posiciones
- Requisición = Azul claro solo si TODAS las posiciones están en Verde (100% cubierta)
- Posición en Amarillo: hasta 25% faltante
- Posición en Rojo: más del 25% faltante
- Si alguna posición está en Amarillo o Rojo → Requisición en Rojo
- Referencia: [[Semáforo de Posiciones de la Requisición]]

### Asignación (Self-Pick colaborativo)
- **Modelo colaborativo (RR-15):** una requisición puede tener **N reclutadores participantes** trabajándola a la vez; no hay dueño único. Tomar una requisición ya tomada **no la transfiere ni la bloquea**: el reclutador se **une** como participante adicional (acción "Unirme") sin desplazar a los existentes ni retroceder el semáforo
- La requisición autorizada queda en la bandeja compartida; una [[Reclutadora]] o [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] la toma → la requisición pasa a Amarillo (En proceso)
- **Salir (no liberar):** un reclutador participante puede **salir**; se retira solo a él. La requisición sigue en Amarillo (En proceso) si quedan otros reclutadores y **no se resetea** lo que otros ya asignaron; solo vuelve a Verde (Autorizada) cuando sale el **último** reclutador
- **Avance compartido + lock por posición/slot:** el avance de cobertura se comparte entre todos los reclutadores participantes; cada uno puede buscar en el [[Pool de Colaboradores]] y asignar colaboradores. El lock de concurrencia opera a nivel de **posición/slot** (dos reclutadores no asignan el mismo colaborador a la misma posición; gana el primero, el segundo ve "posición ya cubierta"), **no** a nivel de la requisición completa
- Si lleva >24h sin ser tomada, el sistema la asigna automáticamente a la [[Reclutadora]] con menor carga de requisiciones activas. El proceso es transparente (el [[Manager de Reclutamiento]] no recibe notificación)
- Si no hay match en el [[Pool de Colaboradores]], la requisición queda en espera; el Flujo de Reclutamiento ya está siempre activo
- **Historial (RR-16):** cada acción (tomar, unirse, salir, asignar/desasignar colaborador, cambio de status, cierre) se registra en un timeline cronológico inmutable con actor y timestamp, visible para los reclutadores participantes, el Líder de Grupo y el Manager de Reclutamiento

### Ciclo de vida de la posición
- La posición tiene fecha de inicio pero no fecha de fin definida
- Termina cuando el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Supervisor|Supervisor]] pone al colaborador en Stand-by (estado Rosa en el [[Semáforo del Colaborador]])

### Eliminación (Morado)
- Estado transversal: se alcanza desde cualquier estado cuando se elimina la requisición
- Requisición sin posiciones al salir del editor → eliminación física automática
- El sistema ejecuta RUTINA Journal Requisición en toda eliminación
- Al eliminar una requisición con posiciones → cada posición también pasa a Morado con journal individual

### Journals automáticos
- Los journals registran un **evento por acción con actor** (rol y nombre) y timestamp, no solo cambios de status. Son **inmutables** y alimentan el [[Core/Módulos/Requisicion/Requisición#Historial de la Requisición|Historial de la Requisición]] (RR-16)
- **RUTINA Journal Requisición:** registra Requisición, Hotel, Manager General / Manager de Área, **Reclutadores** (lista de participantes), Inspector, **Tipo de evento** (`TOMO` / `SE_UNIO` / `SALIO` / `CAMBIO_STATUS`), **Actor**, Status, Nota, Fecha y hora del evento
- **RUTINA Journal Posición:** registra Número de requisición, Número de posición, Posición, Cantidad de personas, Fecha de inicio, Fecha fin, **Tipo de evento** (`ASIGNO_COLAB` / `REASIGNO` / `CAMBIO_STATUS`), **Actor**, **Colaborador** (cuando aplica), Status, Fecha y hora del evento

> [!important] Qué es la "Fecha fin" del Journal Posición
> **No es la fecha de fin de la posición** — esa no existe: la posición termina cuando el colaborador pasa a **Rosa**, no en una fecha pactada. La "Fecha fin" del journal es el **cierre del periodo semanal**: los 7 días que se usan para pagar (5 de trabajo + 2 de descanso, 40 horas brutas). Es el mismo periodo sobre el que se evalúa el umbral de overtime y se genera el Consolidado Semanal.
>
> Se aclara aquí porque el nombre invita a leerlo como el fin de la posición, y eso contradiría la regla de arriba.

## Blacklist

### Blacklist automático
- Disparado por regla de 3 inasistencias
- Estado: Negro en [[Semáforo del Colaborador]]

### Blacklist manual
- Disparado por disputa resuelta a favor del hotel (estado Rojo → investigación del [[Inspector]])
- El [[Inspector]] de la zona decide con autoridad propia; no requiere validación del [[Manager de Reclutamiento]]

### Salida del estado Negro
- Negro **se puede levantar**, pero solo por un perfil de **Administrador** (`ROL-ADM-01`). Ningún rol de Reclutamiento ni del hotel puede hacerlo *(regla cambiada el 2026-08-13; antes el veto era permanente y sin apelación)*
- **No es rehabilitación automática ni instancia de apelación:** es una acción administrativa deliberada, con motivo obligatorio, que queda en el historial
- Al levantarse, el colaborador regresa a **Blanco** —no a Verde fuerte— y tiene que volver a pasar por la validación de la [[Reclutadora]] antes de ser asignable
- El registro del veto **no se borra**: se marca como levantado. Un colaborador puede haber sido vetado más de una vez a lo largo del tiempo, pero **solo un veto puede estar vigente**
- El colaborador no aparece en búsquedas activas; el historial se conserva para consulta interna

### Consulta obligatoria
- La [[Reclutadora]] debe consultar la [[Core/Módulos/Blacklist|Blacklist]] antes de reclutar a un candidato, para evitar volver a reclutar a alguien vetado

## Accidente Laboral

### Activación de estado Gris
- El reporte de accidente activa estado Gris en [[Semáforo del Colaborador]] desde cualquier estado activo
- **Escenario A:** el Colaborador reporta desde la app; la señal llega simultáneamente al SUP y al [[Inspector]] de zona asignado
- **Escenario B:** el SUP ([[Hotel/Supervisor|Supervisor]]) reporta; la señal llega al Inspector
- Se genera un número de reporte automático (mismo patrón que requisición: fecha/hora + homoclave)

### Cierre de tarjeta
- El [[Inspector]] es siempre el responsable final del cierre de la tarjeta de accidente
- Gris → Verde fuerte: requiere alta médica + cierre de tarjeta por el Inspector
- El colaborador queda disponible para reasignación tras el cierre

### Journal de accidente
- Cada cambio de status genera registro con: Número de reporte, Hotel, Colaborador, Reportado por, Status, Nota, Fecha y hora del status

## Onboarding y Ventas

### Datos del hotel
- El [[Ventas/Roles/Business Developer|BD]] recopila en Status Onboarding Azul Claro: nombre del hotel, email, teléfono, **dirección**, nombre y cargo del contacto, y necesidad del negocio
- La **ubicación** del hotel se guarda como coordenadas: es lo que delimita la geocerca del ponche y lo que pinta los pines de Mi Territorio
- La dirección y la ubicación son datos distintos y ambos se conservan: las coordenadas no se leen ni se copian, y la dirección no delimita nada

### Habilitación del hotel
- El hotel solo puede generar requisiciones cuando alcanza el Status Naranja en el [[Semáforo Onboarding]]
- Antes de Naranja, el hotel es un prospecto comercial gestionado por [[Ventas/Ventas|Ventas]]

### Conversión de prospecto a cliente
- Solo el [[Ventas/Roles/Business Developer Coordinator|Business Developer Coordinator]] (BDC) puede aprobar la conversión
- **Precondición:** creación del [[Ventas/Onboarding-Hotel/Conceptos/Usuario del Hotel|Usuario del Hotel]] en el sistema
- Al aprobar, se dispara el [[Ventas/Onboarding-Hotel/Conceptos/Trigger Automático de Conversión|Trigger Automático de Conversión]] con acciones en paralelo:
  - Sistema envía email de bienvenida al hotel
  - Sistema notifica al Business Developer asignado
  - Hotel desaparece de la lista de prospectos

### Documento de Términos y Condiciones
- Se crea en Status Onboarding Amarillo por el BD o el BDC
- Se negocia en Status Onboarding Rosa
- Contenido: Pay rate, Bill rate, Overtime, Festivos, Calendario
- Validación final: [[Ventas/Roles/Business Developer Coordinator|Business Developer Coordinator]] antes del cierre del [[Contrato]]

### Propuesta Personalizada
- Se elabora y envía en Status Onboarding Verde
- Se ajusta/retoma desde Status Onboarding Café cuando hay estancamiento

### Reactivación de estados
- Rojo, Negro y Café siempre reactivan hacia Azul Claro
- Café no es un status terminal: es un puente de desbloqueo operado por el BDC

### Trazabilidad
- Todo cambio de status en Onboarding queda registrado con fecha, responsable y comentario

## Indicador de Calidad / QA

### Principio fundamental
- [[QA/QA|QA]] no ejecuta la operación de ningún departamento; solo observa, mide y retroalimenta

### Estructura del equipo
- Existen 6 [[QA/Operador de QA|Operadores de QA]], uno por cada departamento supervisado: Inspección, Hotel, Colaborador, Ventas, Reclutamiento y [[Customer Service/Customer Service|Customer Service]]
- Cada operador está asignado de forma fija a un único departamento

### Actualización del Indicador
- El [[QA/Operador de QA|Operador de QA]] propone el cambio de estado con base en métricas
- El [[QA/Manager de QA|Manager de QA]] valida y aprueba la actualización
- Solo el Manager de QA puede actualizar formalmente el [[Indicador de Calidad]] de cada departamento
- Cada departamento tiene su propio Indicador independiente; estado inicial es Verde

### Transiciones del Indicador
- **→ Verde:** estado inicial cuando QA comienza a supervisar un departamento
- **Verde → Amarillo:** cuando el Operador detecta métricas fuera de rango o emite observaciones no atendidas
- **Amarillo → Rojo:** cuando las observaciones persisten sin atención o las métricas se deterioran significativamente
- **Rojo → Amarillo:** cuando el departamento comienza a atender observaciones y muestra mejora
- **Amarillo → Verde:** cuando todas las observaciones están resueltas y las métricas regresan a parámetros

### Escalamiento
- Departamento en Rojo sin mejora tras notificación → el [[QA/Manager de QA|Manager de QA]] escala a dirección

### Métricas y KPIs por departamento
- Cada departamento tiene entre 5 y 6 KPIs concretos que el [[QA/Operador de QA|Operador de QA]] monitorea
- Cada KPI tiene umbrales definidos (Meta / En riesgo / Crítico) que alimentan el [[Indicador de Calidad]]
- Referencia completa: [[QA/Métricas y KPIs por Departamento|Métricas y KPIs por Departamento]]

## Reclutamiento y Pool

### Naturaleza del flujo
- El [[Reclutamiento/Flujo de Reclutamiento|Flujo de Reclutamiento]] es continuo: Reclutamiento siempre está contratando, haya o no requisiciones abiertas
- Las requisiciones sin match pueden acelerar o priorizar ciertas posiciones/zonas, pero no son condición para iniciar el flujo

### Distribución de requisiciones (Self-Pick)
- Las requisiciones autorizadas quedan disponibles en una bandeja compartida, priorizada por el [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición|Semáforo de Urgencia]]
- Las [[Reclutadora|Reclutadoras]] y [[Reclutamiento/Líder de Grupo de Reclutadoras|Líderes de Grupo]] toman libremente las requisiciones de la bandeja
- Si una requisición lleva más de 24 horas sin ser tomada, el sistema la asigna automáticamente a la [[Reclutadora]] con menor carga de requisiciones activas. El proceso es transparente (el [[Manager de Reclutamiento]] no recibe notificación)

### Aprobación del colaborador
- La [[Reclutadora]] valida y aprueba al colaborador después de que completa su alta en la app
- La Reclutadora habilita el acceso del colaborador a los paneles
- Colaborador aprobado ingresa al [[Pool de Colaboradores]] con [[Semáforo del Colaborador]] en estado Verde fuerte (Disponible)

### Elegibilidad del Pool
- Solo entran al Pool colaboradores que pasaron el filtro y fueron aprobados por Reclutamiento

## Schedule y Timesheet

### Configuración del Schedule
- La semana del hotel está definida por el contrato (inicio y fin de semana)
- Al crearse una requisición con fecha de inicio dentro de la semana, sus posiciones quedan reflejadas en el [[Core/Módulos/Schedule|Schedule]] de esa semana

### Jornada laboral

- La jornada diaria es de **8 horas**
- La semana laboral es de 7 días: **5 de trabajo + 2 de descanso**
- Total semanal bruto: **40 horas** (8 hrs × 5 días)
- Total semanal neto pagable: **37.5 horas** (40 hrs − 30 min de lunch × 5 jornadas)

### Asignación en Schedule
- Al asignar un colaborador desde el [[Pool de Colaboradores]], queda registrado en el schedule del hotel

### Dependencia del Timesheet
- El [[Timesheet]] se crea a partir del [[Core/Módulos/Schedule|Schedule]]; no puede existir de forma independiente
- Si el [[Core/Módulos/Schedule|Schedule]] se modifica después de que el [[Timesheet]] fue creado, el Timesheet se actualiza automáticamente para reflejar los cambios

### Mecanismo de ponchado

- El colaborador poncha **desde su teléfono, dentro de la propiedad**, en el formulario de la app móvil
- El ponche exige **ubicación GPS dentro del perímetro del hotel**. Fuera de ese perímetro el sistema **bloquea** el ponche
- Se registran **cuatro marcas por jornada**, de las cuales solo dos son obligatorias *(cambiado el 2026-08-13; antes eran tres y la de Comida no tenía regreso)*:
  - **Entrada** — inicio de jornada · **obligatoria** · requiere foto
  - **Salida a lunch** — opcional
  - **Regreso de lunch** — opcional
  - **Salida** — fin de jornada · **obligatoria** · requiere foto
- El par de lunch es **opcional a propósito**: si el colaborador no lo poncha, no pasa nada — se le deducen los 30 minutos igual. Existe para **medir**, no para calcular la nómina
- La foto se toma con la cámara en el momento del ponche; no se admite una imagen guardada
- El ponche funciona **sin señal**: la marca se guarda en el teléfono y se envía cuando hay conexión
- Cuando el ponche se captura sin señal, la validación del perímetro ocurre **al sincronizar**. Si el sistema lo rechaza, entra al calendario del [[Hotel/Supervisor|Supervisor]] como ponche anómalo
- Esto contabiliza el tiempo trabajado del colaborador

### Aprobación de ponches

- Los ponches **no son definitivos hasta que el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]] los aprueban**. Son los **dos únicos roles** que pueden aprobar horas
- La aprobación es **por departamento, no por hotel**: cada [[Hotel/Manager de Área|Manager de Área]] aprueba las horas de los colaboradores de su [[Departamentos del Hotel|departamento]]
- El [[Hotel/Manager General|Manager General]] puede aprobar las horas de **cualquier departamento** del hotel, por su visibilidad global
- Es la misma pareja de autoridad que ya rige la [[Core/Módulos/Requisicion/Requisición|requisición]]: quien puede autorizarla es quien puede aprobar las horas que genera
- La aprobación cubre **todos los días de la requisición**: revisa las jornadas completas en el calendario de ponches
- Solo los ponches aprobados alimentan el [[Timesheet]] y, por lo tanto, el [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] y la facturación al hotel
- El [[Hotel/Supervisor|Supervisor]] **revisa pero no aprueba**: resuelve los ponches anómalos —fuera del perímetro, sin foto, o con desfase entre la hora de captura y la de sincronización—, registra los manuales y deja la jornada lista para aprobación

### Horas sin aprobar

- Si nadie aprueba antes del cierre semanal, las horas **quedan en espera de aprobación**
- Las horas en espera **no se pagan ni se facturan** hasta que se aprueben
- **No hay aprobación automática y el plazo no vence:** la aprobación sigue pendiente hasta que ocurra
- Tampoco hay escalamiento automático, pero el [[Hotel/Manager General|Manager General]] puede aprobar en cualquier momento lo que el [[Hotel/Manager de Área|Manager de Área]] no haya aprobado

### Restricción de ponchado por estado del colaborador

- El ponchado solo es posible cuando el colaborador tiene un [[Timesheet]] activo, lo cual requiere estar inscrito en el [[Core/Módulos/Schedule|Schedule]] de un hotel
- Sin asignación activa (fija o temporal) no hay Schedule, sin Schedule no hay Timesheet, y sin Timesheet no hay ponchado
- Estados que **no permiten ponchado**: Rosa (Stand-by) y Amarillo (Disponible voluntario), porque en ninguno de los dos existe asignación activa
- El único camino para que un colaborador en descanso registre horas es la ruta completa: **Rosa → Amarillo → Café**, donde la [[Reclutadora]] lo asigna temporalmente y se genera el Schedule/Timesheet correspondiente

### Deducción de Lunch

> [!important] Esta regla aplica a **todos** los colaboradores sin excepción, en cada jornada.

- El sistema deduce **30 minutos de lunch por jornada**, **siempre**: haya o no marcas de lunch, y sin importar cuánto haya durado en realidad
- El par de lunch **no toca la nómina**. Un colaborador que se toma 50 minutos cobra igual que uno que se toma 20: a los dos se les deducen 30
- La deducción de lunch impacta directamente el cómputo de horas pagables en el [[Timesheet]]

> [!important] Por qué la deducción es fija aunque ya se capture la duración
> Con el par de lunch **sí** se sabe cuánto duró, pero esa duración alimenta el **[[Timesheet#Indicador de Lunch Extendido|Indicador de Lunch Extendido]]** —una herramienta de supervisión interna de Oranje— y **no** el cálculo del pago. La decisión es deliberada: el indicador sirve para detectar un patrón y hablar con la persona, no para descontarle.
>
> Consecuencia asumida: quien se toma 90 minutos de lunch cobra 60 de ellos. El indicador lo marca; la nómina no lo corrige.
>
> Y un sesgo que conviene tener presente: al ser opcional, **solo ponchan los cumplidos** —justo los que no se pasan—, así que el indicador ve menos de lo que busca.

### Deducción de Breaks

**Eliminada** (2026-08-13). Los breaks ya no se ponchan y **no se deducen**.

Se evaluó recuperar el par Salida Break / Entrada Break y se descartó: con la deducción de lunch fija, un par de marcas opcionales que no descuenta nada no aporta nada al cálculo y nadie lo ponchará. El cálculo de horas pagables queda cerrado sin él.

### Fórmula de horas pagables

- **Horas netas** = (Salida − Entrada) − 30 min de lunch
- Los breaks no entran en la fórmula mientras no se capturen

### Indicador de Lunch Extendido

> [!warning] Indicador sin cálculo posible
> Este indicador compara la duración real del lunch contra 30 minutos. Con una sola marca de **Comida** no hay duración que comparar, así que **no puede calcularse**. Se conserva la definición porque el negocio la sigue necesitando: o se recupera la marca de fin de comida, o el indicador se retira.

- El sistema identifica automáticamente a los colaboradores cuyo tiempo de lunch excede los 30 minutos
- **Visibilidad restringida** — solo roles internos de Oranje:
  - [[Inspector]]
  - [[Inspección/Coordinador|Coordinador]]
  - [[Manager de Reclutamiento]]
- **No visible para el hotel:** el [[Hotel/Manager General|Manager General]], el [[Hotel/Manager de Área|Manager de Área]] y el [[Hotel/Supervisor|Supervisor]] no tienen acceso a este indicador
- **Propósito:** herramienta de supervisión interna para detectar patrones y tomar acciones operativas; no es punitivo de forma automática

### Indicador de Cumplimiento del Timesheet

- El sistema calcula automáticamente un indicador semaforizado que compara el cumplimiento real del colaborador contra los parámetros contractuales del hotel
- **Input:** parámetros del [[Core/Módulos/Contrato|Contrato]] + datos del [[Timesheet]]
- **Evaluación semanal:** la estructura es Año → Semanas (numeradas según calendario e inicio/fin de semana del hotel)
- **Comparación:** días trabajados/requeridos, días de descanso, horas trabajadas/requeridas
- **Ingreso a media semana:** el sistema prorratea automáticamente los días restantes del ciclo; los días previos al alta se marcan en Gris
- Referencia completa: [[Core/Módulos/Semáforos/Indicador de Cumplimiento del Timesheet|Indicador de Cumplimiento del Timesheet]]

### Consolidado Semanal y Pago al Colaborador

- El sistema genera automáticamente un [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] por colaborador al cierre de cada semana
- El consolidado agrupa los [[Timesheet|Timesheets]] de todos los hoteles donde el colaborador trabajó esa semana
- El cálculo aplica el pay rate del [[Core/Módulos/Contrato|Contrato]] de cada hotel por separado
- El overtime se calcula **por requisición**, según la política pactada en cada contrato
- El periodo de pago es **semanal**
- **Oranje paga al colaborador**; cada hotel paga a Oranje según su bill rate
- La [[Contadora]] revisa y el [[Manager de Contabilidad]] aprueba el Consolidado antes de ejecutar el pago
- El umbral de overtime es de **40 horas brutas semanales por colaborador en cada [[Core/Módulos/Requisicion/Requisición|requisición]]**
- Las horas que un colaborador trabaja por encima de 40 en una misma requisición y en una misma semana cuentan como **overtime dentro de esa requisición**
- Si el colaborador trabaja en más de una requisición durante la semana, el umbral se evalúa **en cada una por separado**; las horas de requisiciones distintas no se suman para efectos de overtime

### Autorización de horas extra

- El colaborador **sí puede quedarse** más allá de su jornada; esas horas se registran y se pagan
- Quien autoriza rebasar las 40 horas brutas semanales de una requisición es el **[[Hotel/Manager de Área|Manager de Área]]**, y solo él. Es el mismo reparto que ya rige la aprobación de horas: el [[Hotel/Supervisor|Supervisor]] **revisa**, el Manager de Área **aprueba**
- El disparador es el ponche que cruza las 40 horas acumuladas de esa semana en esa requisición. El sistema lo detecta antes del cierre semanal porque lleva el acumulado
- Las 8 horas diarias no son un tope independiente: salen de 40 ÷ 5. El overtime se puede generar de dos maneras que caen en la misma bolsa de 40 horas — **un día más largo o un sexto día**

## Inspección y Zonas

### Asignación por zona
- Cada hotel pertenece a una [[Core/Catálogos/Zonas|zona]] geográfica
- A cada zona le corresponde un [[Inspector]] asignado por el [[Inspección/Coordinador|Coordinador]]
- El Inspector de la zona del hotel es responsable de darle seguimiento a cualquier disputa en ese hotel

### Responsabilidades del Inspector
- **Día 1:** verifica la llegada del colaborador en la propiedad (transición Verde fuerte → Verde manzana)
- **Día 3:** entrega el uniforme al colaborador (transición → Azul claro)
- **Reportes:** investiga disputas (estado Rojo) y resuelve hacia Negro o Verde fuerte
- **Accidentes:** responsable final del cierre de la tarjeta de accidente; gestiona transición Gris → Verde fuerte

### Coordinador
- El [[Inspección/Coordinador|Coordinador]] asigna inspectores a las zonas geográficas y supervisa su trabajo en campo

## Manejo de errores

### Fallo de ponche
- Si el sistema falla al registrar un ponche, el colaborador puede reintentar.
- Si el fallo persiste, el sistema notifica al [[Hotel/Supervisor|Supervisor]] para que tome acción manual.
- **Ponche rechazado por ubicación:** si el colaborador está en la propiedad pero el GPS no lo ubica dentro del perímetro —sótanos, cocinas, edificios con estructura metálica— el ponche queda registrado como rechazado y se notifica al Supervisor. El Supervisor puede registrarlo manualmente con **motivo obligatorio**, y queda asentado quién lo registró.

### Colisión de IDs (homoclave)
- Si al generar un número de requisición, posición o reporte se produce una colisión de homoclave (duplicado), el sistema regenera la homoclave automáticamente hasta obtener un valor único.

### Desconexión durante operación
- Si el usuario pierde conexión mientras realiza una operación (captura de datos, llenado de formulario, etc.), la operación se guarda como borrador automáticamente.
- El borrador puede ser retomado y completado cuando se restablezca la conexión.

## Relacionado

- [[Semáforo del Colaborador]]
- [[Semáforo de Requisición]]
- [[Semáforo de Urgencia de Requisición]]
- [[Semáforo de Posiciones de la Requisición]]
- [[Semáforo Onboarding]]
- [[Indicador de Calidad]]
- [[Core/Módulos/Semáforos/Indicador de Cumplimiento del Timesheet|Indicador de Cumplimiento del Timesheet]]
- [[Core/Módulos/Requisicion/Flujo de Requisición|Flujo de Requisición]]
- [[Core/Módulos/Accidente Laboral/Flujo de Accidente Laboral|Flujo de Accidente Laboral]]
- [[Reclutamiento/Flujo de Reclutamiento|Flujo de Reclutamiento]]
- [[Ventas/Onboarding-Hotel/Flujo de Onboarding|Flujo de Onboarding]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Pool de Colaboradores]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Timesheet]]
- [[Hotel/Manager General|Manager General]]
- [[Hotel/Manager de Área|Manager de Área]]
- [[Hotel/Supervisor|Supervisor]]
- [[Inspector]]
- [[Inspección/Coordinador|Coordinador]]
- [[Reclutadora]]
- [[Manager de Reclutamiento]]
- [[QA/QA|QA]]
- [[QA/Manager de QA|Manager de QA]]
- [[QA/Operador de QA|Operador de QA]]
- [[Ventas/Roles/Business Developer Coordinator|Business Developer Coordinator]]
- [[Ventas/Onboarding-Hotel/Conceptos/Usuario del Hotel|Usuario del Hotel]]
- [[Ventas/Onboarding-Hotel/Conceptos/Trigger Automático de Conversión|Trigger Automático de Conversión]]
- [[Ventas/Onboarding-Hotel/Conceptos/Documento de Términos y Condiciones|Documento de Términos y Condiciones]]
- [[Core/Catálogos/Zonas|Zonas]]
