---
tipo: simulación
perspectiva: hotel
hotel_ficticio: Hotel Riviera Maya Beach Resort
zona: Sureste
jerarquía: extendida
tags:
  - simulación
  - hotel
  - ciclo-completo
aliases:
  - Simulación Hotel
---

# Simulación completa — Punto de vista del Hotel

> [!abstract] Propósito
> Esta simulación narra el ciclo de vida completo de un hotel dentro del sistema Oranje, desde que es identificado como prospecto comercial hasta su operación diaria con personal asignado. Todos los datos son ficticios, pero cada acción, transición de semáforo y regla de negocio respeta fielmente la documentación del vault.

## Personajes de la simulación

| Personaje | Rol | Departamento |
|---|---|---|
| Carlos Méndez | [[Manager General]] (GM) | Hotel Riviera Maya Beach Resort |
| Laura Torres | [[Manager de Área]] (GH) | Housekeeping |
| Pedro Ramírez | [[Supervisor]] (SUP) | Housekeeping |
| Sofía Vega | [[Business Developer]] (BD) | Ventas — Oranje |
| Ricardo Luna | [[Business Developer Coordinator]] (BDC) | Ventas — Oranje |
| Ana Martínez | [[Reclutadora]] | Reclutamiento — Oranje |
| Miguel Ochoa | [[Inspector]] | Inspección — Oranje (zona Sureste) |
| María López | Colaboradora | Housekeeper |
| Juan Hernández | Colaborador | Houseman |
| Elena Cruz | Colaboradora | Housekeeper |
| Roberto Díaz | Colaborador (reemplazo) | Houseman |

---

## Fase 1 — Onboarding comercial

> Referencia: [[Flujo de Onboarding]] · [[Semáforo Onboarding]]

### 1.1 — Identificación del hotel

Sofía Vega, [[Business Developer]] asignada a la zona Sureste, identifica al **Hotel Riviera Maya Beach Resort** como un hotel con alta rotación de personal de housekeeping y potencial interés en servicios de staffing.

Sofía crea el perfil del hotel en el sistema.

> [!info] Semáforo Onboarding
> **Gris** → Hotel identificado

### 1.2 — Contacto y recopilación de datos

Sofía realiza una visita en frío al hotel. Se reúne con Carlos Méndez (GM) y recopila los datos básicos: nombre del hotel, dirección, contacto principal, número de habitaciones, departamentos activos y necesidad estimada de personal.

Registra todo en el perfil del sistema.

> [!info] Semáforo Onboarding
> Gris → **Azul Claro** — Contacto y recopilación de datos

### 1.3 — Propuesta enviada

Sofía elabora una [[Propuesta Personalizada]] para el Hotel Riviera Maya: servicios de staffing para el departamento de Housekeeping, con cobertura de Housekeepers, Housemen y personal de lavandería. Incluye descripción de servicios, modelo de operación y propuesta de precios preliminar.

Envía la propuesta por correo al GM Carlos Méndez y da seguimiento.

> [!info] Semáforo Onboarding
> Azul Claro → **Verde** — Propuesta enviada

### 1.4 — Seguimiento y términos

Carlos Méndez responde con interés. Sofía, con apoyo de Ricardo Luna ([[Business Developer Coordinator]]), crea el [[Documento de Términos y Condiciones]] con los parámetros comerciales:

| Parámetro | Valor |
|---|---|
| Pay rate (Housekeeper) | $180 MXN/hora |
| Pay rate (Houseman) | $160 MXN/hora |
| Bill rate (Housekeeper) | $280 MXN/hora |
| Bill rate (Houseman) | $250 MXN/hora |
| Overtime | 1.5x bill rate |
| Festivos | 2x bill rate |
| Inicio de semana | Lunes |
| Fin de semana | Domingo |

> [!info] Semáforo Onboarding
> Verde → **Amarillo** — En seguimiento tras propuesta

### 1.5 — Negociación de términos

Carlos solicita ajustar el bill rate de Houseman a $240 MXN/hora. Ricardo Luna (BDC) participa directamente en la negociación. Después de dos rondas de ajuste, ambas partes llegan a un acuerdo. Se firma el [[Contrato]].

> [!info] Semáforo Onboarding
> Amarillo → **Rosa** — Negociación de términos

### 1.6 — Conversión a cliente activo

Ricardo Luna (BDC) aprueba la conversión del Hotel Riviera Maya a cliente activo. Solo el BDC tiene autoridad para esta acción.

> [!tip] Acciones automáticas del sistema — [[Trigger Automático de Conversión]]
> 1. Se crea el [[Usuario del Hotel]] en el sistema
> 2. El sistema envía email de bienvenida al hotel
> 3. Se notifica a Sofía Vega (BD asignada)
> 4. El hotel desaparece de la lista de prospectos

> [!info] Semáforo Onboarding
> Rosa → **Naranja** — Acuerdo firmado, hotel cliente activo

> [!warning] Regla de negocio
> El hotel solo puede generar [[Requisición|requisiciones]] a partir de este momento. Antes de alcanzar Naranja en el [[Semáforo Onboarding]], el hotel es un prospecto comercial sin acceso operativo.

---

## Fase 2 — Primera requisición de personal

> Referencia: [[Flujo de Requisición]] · [[Semáforo de Requisición]] · [[Requisición]]

### 2.1 — Creación de la requisición

Es lunes 19 de mayo de 2026. Pedro Ramírez ([[Supervisor|SUP]]) del departamento de Housekeeping necesita personal. Abre la app y crea una requisición con las siguientes posiciones:

| # | Posición | Cantidad | Modalidad | Fecha de inicio | Horario | Inglés |
|---|---|---|---|---|---|---|
| 1 | Housekeeper | 2 | Tiempo completo | 25 mayo 2026 | 07:00–15:00 | Básico |
| 2 | Houseman | 1 | Tiempo completo | 25 mayo 2026 | 07:00–15:00 | Básico |

> [!tip] Acción automática del sistema
> Se genera el número de requisición: **202605190830A3**
> Formato: `Año(4) + Mes(2) + Día(2) + Hora(2) + Minutos(2) + Homoclave(2)`

> [!info] Semáforo de Requisición
> → **Verde manzana** — En elaboración

> [!info] Semáforo de Posiciones
> Posición 1 (Housekeeper ×2): → **Dorado** — En preparación
> Posición 2 (Houseman ×1): → **Dorado** — En preparación

### 2.2 — Autorización

Laura Torres ([[Manager de Área|GH]] de Housekeeping) revisa la requisición `202605190830A3` en su bandeja. Verifica las posiciones, cantidades y fechas. Todo correcto. Autoriza la requisición.

> [!warning] Regla de negocio
> Solo el [[Manager General]] o el [[Manager de Área]] pueden autorizar una requisición. Si Pedro (SUP) intentara autorizar, el sistema bloquearía con: *"Solo el gerente del hotel puede autorizar la requisición"*.

> [!tip] Acciones automáticas al autorizar
> 1. **Requisición** pasa a Verde (Autorizada)
> 2. **Posiciones** pasan de Dorado a Naranja (Autorizadas)
> 3. **Cálculo de urgencia**: fecha de autorización (19 mayo 08:45) vs fecha de inicio (25 mayo 07:00) = ~142 horas → **Verde fuerte** (Normal, >120h)
> 4. **Inspector asignado**: Miguel Ochoa — automático según [[Zonas|zona Sureste]] del hotel
> 5. **Posiciones reflejadas** en el [[Schedule]] de la semana del 25 de mayo

> [!info] Semáforo de Requisición
> Verde manzana → **Verde** — Autorizada

> [!info] Semáforo de Posiciones
> Dorado → **Naranja** — Autorizadas

> [!info] Semáforo de Urgencia
> → **Verde fuerte** — Normal (142 horas disponibles)

### 2.3 — Self-Pick por Reclutamiento

La requisición `202605190830A3` aparece en la bandeja compartida de [[Self-Pick de Requisiciones]], priorizada por nivel de urgencia. Ana Martínez ([[Reclutadora]]) la ve y la toma a las 12:30 del mismo día.

> [!warning] Regla de negocio
> Si ninguna reclutadora toma la requisición en 24 horas, el sistema la asigna automáticamente a la reclutadora con menor carga de trabajo.

> [!info] Semáforo de Requisición
> Verde → **Amarillo** — En proceso

### 2.4 — Cobertura

Ana Martínez consulta el [[Pool de Colaboradores]] y busca candidatos que hagan match con los requisitos: posición, zona geográfica compatible, disponibilidad y modalidad.

**Resultado de búsqueda:**

| Colaborador | Posición | Estado en semáforo | Match |
|---|---|---|---|
| María López | Housekeeper | Verde fuerte (Disponible) | Sí |
| Elena Cruz | Housekeeper | Verde fuerte (Disponible) | Sí |
| Juan Hernández | Houseman | Verde fuerte (Disponible) | Sí |

Ana asigna a los tres colaboradores. Los registra en el [[Schedule]] del hotel para la semana del 25 de mayo.

> [!info] Semáforo de Posiciones
> **Posición 1** (Housekeeper ×2): Naranja → **Verde** — 100% cubierta (2/2)
> **Posición 2** (Houseman ×1): Naranja → **Verde** — 100% cubierta (1/1)

> [!warning] Regla de negocio
> Requisición = Azul claro **solo si** TODAS las posiciones están en Verde. Si alguna posición cierra en Amarillo o Rojo, la requisición cierra en Rojo.

> [!info] Semáforo de Requisición
> Amarillo → **Azul claro** — Cubierta totalmente

> [!info] Semáforo del Colaborador
> María López: Verde fuerte → **Blanco** (Pre-asignación)
> Elena Cruz: Verde fuerte → **Blanco** (Pre-asignación)
> Juan Hernández: Verde fuerte → **Blanco** (Pre-asignación)

---

## Fase 3 — Día 1: llegada de colaboradores

> Referencia: [[Reglas de Inspección]] · [[Semáforo del Colaborador]] · [[Inspector]]

### 3.1 — Verificación de llegada

Es domingo 25 de mayo de 2026, 06:45 AM. María López, Juan Hernández y Elena Cruz llegan al Hotel Riviera Maya Beach Resort.

Miguel Ochoa ([[Inspector]] de zona Sureste) se presenta en la propiedad. Verifica presencialmente la llegada de cada colaborador: confirma identidad, registra la hora de llegada y valida que están en el lugar correcto.

> [!info] Semáforo del Colaborador
> María López: Blanco → **Verde manzana** (Día 1-2)
> Juan Hernández: Blanco → **Verde manzana** (Día 1-2)
> Elena Cruz: Blanco → **Verde manzana** (Día 1-2)

### 3.2 — Primer ponchado

Los tres colaboradores ponchan su primer día de trabajo desde su teléfono, dentro de la propiedad, con GPS y foto.

Registro de ponchado de María López — Día 1 (25 mayo):

| Evento | Hora |
|---|---|
| Entrada | 07:00 |
| Salida Lunch | 11:30 |
| Entrada Lunch | 12:00 |
| Salida Break | 14:00 |
| Entrada Break | 14:15 |
| Salida | 15:00 |

> [!warning] Regla de negocio — Deducción de lunch
> Lunch tomado: 30 min (12:00 − 11:30). Como es exactamente 30 min (mínimo obligatorio), se deduce 30 min. Si hubiera sido menor a 30 min, se deducirían 30 min de todas formas. Si hubiera sido mayor, se deduciría el tiempo real.

> [!tip] Cálculo automático del sistema — [[Timesheet]]
> - Horas brutas: 15:00 − 07:00 = 8:00
> - Deducción lunch: 0:30
> - Deducción break: 0:15
> - **Horas netas pagables: 7:15**

---

## Fase 4 — Día 3: entrega de uniforme

> Referencia: [[Reglas de Inspección]] · [[Inspector]]

### 4.1 — Entrega de uniforme

Es martes 27 de mayo de 2026. Miguel Ochoa ([[Inspector]]) regresa al Hotel Riviera Maya para entregar los uniformes de Oranje a los tres colaboradores.

Miguel entrega personalmente el uniforme a María López, Juan Hernández y Elena Cruz. Registra la entrega en la app.

> [!info] Semáforo del Colaborador
> María López: Verde manzana → **Azul claro** (Día 3+)
> Juan Hernández: Verde manzana → **Azul claro** (Día 3+)
> Elena Cruz: Verde manzana → **Azul claro** (Día 3+)

A partir de este momento, los tres colaboradores operan con normalidad: ponchan diariamente, su [[Timesheet]] se genera a partir del [[Schedule]], y su trabajo queda registrado en el sistema.

---

## Fase 5 — Operación semanal normal

> Referencia: [[Timesheet]] · [[Schedule]] · [[Indicador de Cumplimiento del Timesheet]]

### 5.1 — Semana completa de trabajo

La primera semana completa transcurre sin incidentes. Los tres colaboradores trabajan 5 jornadas (lunes a viernes) con 2 días de descanso (sábado y domingo).

**Resumen del [[Timesheet]] de María López — Semana del 26 mayo al 1 junio:**

| Día | Entrada | Salida | Lunch | Break | Horas netas |
|---|---|---|---|---|---|
| Lun 26 | 07:00 | 15:00 | 30 min | 15 min | 7:15 |
| Mar 27 | 07:00 | 15:00 | 30 min | 15 min | 7:15 |
| Mié 28 | 07:00 | 15:00 | 35 min | 15 min | 7:10 |
| Jue 29 | 07:00 | 15:00 | 30 min | 15 min | 7:15 |
| Vie 30 | 07:00 | 15:00 | 30 min | 15 min | 7:15 |
| **Total** | | | | | **36:10** |

> [!warning] Regla — Jornada semanal
> Jornada diaria: 8 horas brutas. Semana laboral: 7 días (5 trabajo + 2 descanso). Total semanal bruto: 40 horas. Total semanal neto pagable esperado: 37.5 horas (40 hrs − 30 min lunch × 5 jornadas, sin contar breaks). María registra 36:10 horas netas por los breaks de 15 min diarios.

### 5.2 — Indicador de Lunch Extendido

El miércoles 28, Juan Hernández toma un lunch de 45 minutos en lugar de los 30 reglamentarios.

> [!tip] Acción automática del sistema
> El **Indicador de Lunch Extendido** se activa para Juan Hernández el miércoles 28. Se deduce el tiempo real (45 min) en lugar del mínimo de 30 min.

> [!warning] Regla de visibilidad
> El Indicador de Lunch Extendido es visible **solo** para: Miguel Ochoa ([[Inspector]]), el [[Coordinador]] y el [[Manager de Reclutamiento]].
> **No es visible** para: Carlos Méndez (GM), Laura Torres (GH) ni Pedro Ramírez (SUP).
> No es punitivo de forma automática — es una herramienta de supervisión interna de Oranje.

### 5.3 — Transición a Fijo

Al completar 7 días de operación continua, el sistema transita automáticamente a los tres colaboradores al estado Naranja (Fijo).

> [!info] Semáforo del Colaborador
> María López: Azul claro → **Naranja** (Fijo)
> Juan Hernández: Azul claro → **Naranja** (Fijo)
> Elena Cruz: Azul claro → **Naranja** (Fijo)

---

## Fase 6 — Incidente: reporte de colaborador

> Referencia: [[Semáforo del Colaborador]] · [[Reglas de Inspección]] · [[Blacklist]]

### 6.1 — Reporte por parte del hotel

Es jueves 5 de junio de 2026. Juan Hernández ha llegado tarde tres días de la semana y su rendimiento ha bajado significativamente. Pedro Ramírez ([[Supervisor|SUP]]) decide reportarlo desde la app.

> [!info] Semáforo del Colaborador
> Juan Hernández: Naranja → **Rojo** (Reportado)

> [!warning] Regla de negocio
> El estado Rojo puede ser activado por: [[Manager General]], [[Manager de Área]] o [[Supervisor]]. La acumulación de 3 inasistencias **no** pasa por Rojo; va directo a Negro ([[Blacklist]] automático).

### 6.2 — Investigación del Inspector

Miguel Ochoa ([[Inspector]]) recibe la notificación del reporte. Se presenta en el hotel, entrevista a Pedro (SUP) y a Laura (GH), revisa los registros de ponchado de Juan y documenta la situación.

Después de su investigación, Miguel determina que la disputa **favorece al hotel**: los tardíos están documentados en el [[Timesheet]] y no hay justificación válida por parte del colaborador.

> [!warning] Regla — Autoridad del Inspector
> El [[Inspector]] tiene autoridad propia para decidir el resultado:
> - Disputa a favor del hotel → **Negro** ([[Blacklist]])
> - Disputa a favor del colaborador → **Verde fuerte** (reincorporado)

> [!info] Semáforo del Colaborador
> Juan Hernández: Rojo → **Negro** (Blacklist)

Juan Hernández queda **vetado** del sistema Oranje. No puede ser asignado a ningún hotel mientras el veto siga vigente; solo un perfil de Administrador puede levantarlo.

### 6.3 — Requisición de reemplazo

Laura Torres ([[Manager de Área|GH]]) necesita cubrir la vacante dejada por Juan. Crea una nueva requisición desde la app:

| # | Posición | Cantidad | Modalidad | Fecha de inicio | Horario | Inglés |
|---|---|---|---|---|---|---|
| 1 | Houseman | 1 | Tiempo completo | 7 junio 2026 | 07:00–15:00 | Básico |

> [!tip] Acción automática del sistema
> Número de requisición generado: **202606050915B7**

Laura misma autoriza la requisición (como GH tiene autoridad para hacerlo).

> [!tip] Acciones automáticas al autorizar
> 1. Requisición → **Verde** (Autorizada)
> 2. Posición → **Naranja** (Autorizada)
> 3. Urgencia: fecha de autorización (5 junio 09:15) vs fecha de inicio (7 junio 07:00) = ~46 horas → **Rojo** (Urgente, <72h)
> 4. Inspector Miguel Ochoa asignado automáticamente

> [!info] Semáforo de Urgencia
> → **Rojo** — Urgente (<72 horas)

### 6.4 — Cobertura urgente

La requisición urgente aparece en la bandeja de [[Self-Pick de Requisiciones]] destacada por su nivel de urgencia Rojo. Ana Martínez ([[Reclutadora]]) la toma de inmediato.

Ana busca en el [[Pool de Colaboradores]] y encuentra a Roberto Díaz (Houseman, estado Verde fuerte — Disponible, zona Sureste). Lo asigna a la posición.

> [!info] Semáforo de Posiciones
> Posición 1 (Houseman ×1): Naranja → **Verde** — 100% cubierta

> [!info] Semáforo de Requisición
> Amarillo → **Azul claro** — Cubierta totalmente

Roberto Díaz inicia el proceso estándar: llegada verificada por Miguel (Inspector) en Día 1, entrega de uniforme en Día 3, y transición a Naranja (Fijo) después del Día 7.

---

## Fase 7 — Contingencia: accidente laboral

> Referencia: [[Flujo de Accidente Laboral]] · [[Accidente Laboral]] · [[Reglas de Inspección]]

### 7.1 — Reporte del accidente (Escenario A)

Es miércoles 18 de junio de 2026, 10:20 AM. María López sufre una caída en el área de lavandería del hotel mientras trasladaba un carrito de ropa. Se lastima el tobillo derecho.

María abre la app de Oranje y genera un reporte de accidente desde su teléfono.

> [!tip] Acciones automáticas del sistema
> 1. Se genera una **tarjeta de accidente laboral** con número automático
> 2. María López transita a estado **Gris** (Accidentada) en el [[Semáforo del Colaborador]]
> 3. La señal llega **simultáneamente** a Pedro Ramírez (SUP) y a Miguel Ochoa (Inspector de zona Sureste)

> [!info] Semáforo del Colaborador
> María López: Naranja → **Gris** (Accidentada)

> [!warning] Regla de protección
> Mientras María esté en estado Gris, sus inasistencias **no cuentan** para la regla de 3 inasistencias → [[Blacklist]]. El estado Gris protege al colaborador accidentado.

### 7.2 — Información presencial del Supervisor

Pedro Ramírez ([[Supervisor|SUP]]) recibe la notificación y acude físicamente al área de lavandería. Captura la información presencial en la tarjeta de accidente:

| Campo | Información capturada |
|---|---|
| Ubicación exacta | Área de lavandería, pasillo entre lavadoras industriales y estación de planchado |
| Circunstancias | Colaboradora resbaló con piso mojado mientras trasladaba carrito de ropa sucia |
| Testigos | Elena Cruz (compañera de turno) |
| Atención inmediata | Se aplicó hielo en el tobillo, se sentó a la colaboradora en área de descanso |

### 7.3 — Seguimiento médico del Inspector

Miguel Ochoa ([[Inspector]]) recibe la notificación en la app y se desplaza al hotel. Evalúa la situación y decide trasladar a María al centro médico más cercano.

Miguel complementa la tarjeta de accidente con la información médica:

| Campo | Información capturada |
|---|---|
| Traslado | Centro Médico del Sureste, llegada 11:45 AM |
| Diagnóstico | Esguince de tobillo grado I |
| Días de incapacidad | 5 días (del 18 al 22 de junio) |
| Observaciones médicas | Reposo absoluto, no cargar peso, revisión de seguimiento el día 23 |

### 7.4 — Cierre de la tarjeta

El lunes 23 de junio, María López recibe el alta médica. Miguel Ochoa ([[Inspector]]) cierra la tarjeta de accidente en el sistema.

> [!warning] Regla de negocio
> El [[Inspector]] es **siempre** el responsable final del cierre de la tarjeta de accidente. Sin excepción. El cierre requiere alta médica documentada.

> [!info] Semáforo del Colaborador
> María López: Gris → **Verde fuerte** (Disponible)

María queda disponible en el [[Pool de Colaboradores]] para ser reasignada. Ana Martínez ([[Reclutadora]]) la reasigna al Hotel Riviera Maya para continuar su posición de Housekeeper.

---

## Fase 8 — Stand-by

> Referencia: [[Semáforo del Colaborador]] · [[Reglas del Hotel]]

### 8.1 — Activación de Stand-by

Es julio de 2026. La temporada baja reduce la ocupación del hotel significativamente. Laura Torres ([[Manager de Área|GH]]) decide que no necesita a las dos Housekeepers simultáneamente.

Laura pone a Elena Cruz en **Stand-by** desde la app.

> [!info] Semáforo del Colaborador
> Elena Cruz: Naranja → **Rosa** (Stand-by)

> [!warning] Reglas de Stand-by
> - Elena **no tiene** [[Schedule]] ni [[Timesheet]] mientras esté en Rosa
> - Elena **no puede** ponchar
> - **Sin fecha de fin definida** — el estado se mantiene hasta que cualquier rol del hotel lo cambie
> - La acción puede ser ejecutada por: [[Manager General]], [[Manager de Área]] o [[Supervisor]]

### 8.2 — Reactivación

Tres semanas después, la ocupación del hotel repunta. Laura Torres reactiva a Elena Cruz desde la app, cambiando su estado de vuelta a operativo.

> [!info] Semáforo del Colaborador
> Elena Cruz: Rosa → estado operativo

Elena reaparece en el [[Schedule]] del hotel y puede ponchar nuevamente. Su [[Timesheet]] se genera a partir de la semana de reactivación.

---

## Fase 9 — Facturación semanal

> Referencia: [[Facturación al Hotel]] · [[Flujo de Nómina]] · [[Contrato]]

### 9.1 — Generación automática de factura

Al cierre de la semana del 26 mayo al 1 junio, el sistema genera automáticamente la factura para el Hotel Riviera Maya Beach Resort.

**Factura — Semana del 26 mayo al 1 junio de 2026**

| Colaborador | Posición | Horas regulares | Horas OT autorizadas | Bill rate | Subtotal |
|---|---|---|---|---|---|
| María López | Housekeeper | 36.17 | 0 | $280/hr | $10,127.60 |
| Juan Hernández | Houseman | 35.75 | 0 | $240/hr | $8,580.00 |
| Elena Cruz | Housekeeper | 36.17 | 0 | $280/hr | $10,127.60 |

| Concepto | Monto |
|---|---|
| Subtotal servicios | $28,835.20 |
| Acreditaciones | $0.00 |
| **Total a cobrar** | **$28,835.20** |

> [!warning] Reglas de facturación
> - Se usa exclusivamente el **bill rate** del [[Contrato]]. El pay rate (lo que Oranje paga al colaborador) nunca se refleja en la factura.
> - Solo se factura **overtime autorizado** por el hotel. Horas extra no autorizadas no se incluyen.
> - Si la semana cruzara un cambio de mes y el contrato indica "Factura partida por mes: sí", el sistema generaría dos facturas separadas.

> [!tip] Acción automática del sistema
> La factura se genera con un folio único asignado por Oranje. Se envía al hotel como documento fiscal.

---

## Fase 10 — Supervisión de calidad (QA)

> Referencia: [[Indicador de Calidad]] · [[Operador de QA]] · [[Manager de QA]]

### 10.1 — Monitoreo continuo

Un [[Operador de QA]] está asignado de forma fija al departamento Hotel. Este operador monitorea continuamente los indicadores de calidad del servicio de staffing que Oranje proporciona al Hotel Riviera Maya y a todos los hoteles clientes.

El [[Indicador de Calidad]] del departamento Hotel se mantiene en **Verde** (Calidad óptima) durante las primeras semanas de operación.

> [!warning] Regla de escalamiento
> Si el [[Indicador de Calidad]] alcanza estado **Rojo** (Calidad crítica) sin mejora después de la notificación, el [[Manager de QA]] escala a dirección. QA no ejecuta la operación del Hotel; solo observa, mide y retroalimenta.

---

## Resumen de transiciones de semáforo

### [[Semáforo Onboarding]]
```
Gris → Azul Claro → Verde → Amarillo → Rosa → Naranja (cliente activo)
```

### [[Semáforo de Requisición]] (Requisición 202605190830A3)
```
Verde manzana → Verde → Amarillo → Azul claro (cubierta totalmente)
```

### [[Semáforo de Posiciones de la Requisición]]
```
Dorado → Naranja → Verde (100% cubierta)
```

### [[Semáforo del Colaborador]] — María López
```
Verde fuerte → Blanco → Verde manzana → Azul claro → Naranja → Gris → Verde fuerte
```

### [[Semáforo del Colaborador]] — Juan Hernández
```
Verde fuerte → Blanco → Verde manzana → Azul claro → Naranja → Rojo → Negro (Blacklist)
```

### [[Semáforo del Colaborador]] — Elena Cruz
```
Verde fuerte → Blanco → Verde manzana → Azul claro → Naranja → Rosa (Stand-by) → reactivada
```

---

## Módulos y conceptos referenciados

| Módulo | Referencia |
|---|---|
| Hotel como cliente | [[Hotel]] · [[Reglas del Hotel]] |
| Roles del hotel | [[Manager General]] · [[Manager de Área]] · [[Supervisor]] |
| Onboarding comercial | [[Flujo de Onboarding]] · [[Semáforo Onboarding]] · [[Propuesta Personalizada]] · [[Documento de Términos y Condiciones]] · [[Contrato]] |
| Requisiciones | [[Requisición]] · [[Flujo de Requisición]] · [[Semáforo de Requisición]] · [[Semáforo de Posiciones de la Requisición]] · [[Semáforo de Urgencia de Requisición]] |
| Asignación de personal | [[Pool de Colaboradores]] · [[Reclutadora]] · [[Self-Pick de Requisiciones]] |
| Operación diaria | [[Schedule]] · [[Timesheet]] · [[Semáforo del Colaborador]] |
| Inspección | [[Inspector]] · [[Coordinador]] · [[Reglas de Inspección]] |
| Contingencias | [[Accidente Laboral]] · [[Flujo de Accidente Laboral]] · [[Blacklist]] |
| Facturación | [[Facturación al Hotel]] · [[Flujo de Nómina]] |
| Calidad | [[Indicador de Calidad]] · [[Indicador de Cumplimiento del Timesheet]] · [[Operador de QA]] · [[Manager de QA]] |

---

## Simulaciones relacionadas

- [[Simulación - Punto de Vista de Ventas]] — Detalla el proceso comercial de onboarding desde la perspectiva del BD y BDC hasta la conversión del hotel a cliente activo.
- [[Simulación - Punto de Vista de Inspección]] — Muestra la operación de campo del Inspector: verificación Día 1, Día 3, reportes y accidentes desde su perspectiva.
- [[Simulación - Punto de Vista de Reclutamiento]] — Cubre el proceso de reclutamiento y asignación de personal que el hotel solicita mediante requisiciones.
- [[Simulación - Ciclo de Vida del Colaborador]] — Narra la experiencia completa del colaborador asignado al hotel, incluyendo estados y transiciones que afectan la operación diaria.
