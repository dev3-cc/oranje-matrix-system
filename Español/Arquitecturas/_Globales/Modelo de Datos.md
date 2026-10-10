---
tags:
  - arquitectura
  - globales
  - modelo-de-datos
aliases:
  - Modelo de Datos
  - Esquema de Base de Datos
  - Modelo de Dominio
---

# Modelo de Datos

> [!info] Alcance
> Traducción de [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] a un esquema relacional. Define las entidades, sus relaciones y **las restricciones que hacen cumplir cada regla en la base de datos**, no en el código de aplicación.

> [!important] Principio
> Toda regla que pueda vivir como restricción de base de datos, vive ahí. Una regla en el código se puede saltar; una restricción, no.

---

## 1. Las cinco entidades que sostienen el modelo

Son las que traducen las reglas más difíciles. Si alguna falta, el negocio no se puede calcular.

| Entidad           | Regla que hace cumplir    | Qué pasa si no existe                                           |
| -------------------| ---------------------------| -----------------------------------------------------------------|
| **Posición**      | RR-H-03, RR-H-05, RR-H-08 | La urgencia y la cobertura no se pueden calcular por posición   |
| **Slot**          | RR-15                     | No hay unidad de bloqueo; dos reclutadores ganan el mismo lugar |
| **Asignación**    | RR-05, Consolidado        | El colaborador no puede estar en 2 hoteles **en las mismas horas** |
| **Contrato**      | RR-V-10, Consolidado      | No hay pay rate ni bill rate: no hay nómina ni factura          |
| **Participación** | RR-15, RR-16              | No existe el Self-Pick colaborativo                             |

---

## 2. Diagrama de relaciones

```
Zone ──< Hotel ──< Contract ──< ContractRate   (por posición)
          │
          ├──< Requisition ──< Position ──< Slot ──< Assignment >── Worker
          │     │                                        │
          │     └──< Participation >── User              │
          │                                              │
          └──< Schedule ──< ScheduleEntry ───────────────┘
                │
                └──< Timesheet ──< PunchMark
                      │
                      ├──> ConsolidationDetail >── Consolidation >── Worker
                      │
                      └──> InvoiceDetail >── Invoice >── Hotel

Worker ──< BlacklistEntry     (historial: sin borrado, con baja)
Worker ──< WorkerDocument     (Cloud Storage, cifrado)
Worker ──< WorkAccident >── Inspector

StatusLight ──< StatusLightState ──< StatusLightTransition   (las 7 máquinas de estado)
Journal   (append-only, transversal a todo)
```

> [!important] Este diagrama no cubre el módulo `commercial` completo
> Aquí `Hotel` aparece solo como el punto del que cuelga todo lo demás. El **pipeline comercial** —el ciclo de prospección, su semáforo, los contactos, los intentos y la propuesta— no está modelado en este documento.
>
> **Para `commercial` manda `Español/Arquitecturas/Ventas/Ventas - Modelo de Datos v2.drawio`**, con sus 7 tablas: `hotel`, `hotel_contact`, `prospect`, `prospect_state_history`, `contact_attempt`, `proposal`, `user_zone`. Las decisiones que lo sostienen son **D-13** (el ciclo comercial es una entidad) y **D-14** (los semáforos guardan historia).
>
> Consecuencia que hay que tener presente al leer: **el `Hotel` de este diagrama ya no lleva el estado del semáforo** —vive en `prospect`— ni las columnas de contacto, que son `hotel_contact`. Si algo de aquí contradice al diagrama de Ventas, gana el diagrama.

---

## 3. Núcleo transaccional

### Colaborador

**No tiene hotel.** El vínculo con hoteles es siempre vía `assignment`, que tiene fechas. Eso resuelve de una sola vez el [[Pool de Colaboradores]], el trabajo multi-hotel en una semana y el histórico.

| Campo | Notas |
|---|---|
| `id`, `number` | Numeración automática |
| `name`, `phone`, `address`, `birth_date` | Fase 1 — entrevista |
| `catalog_position_id`, `english_level_id`, `hiring_modality_id`, `transport` | Fase 2 — alta en app |
| `ssn_encrypted`, `itin_encrypted` | Cifrado de campo, no solo en reposo |
| `withholding_16` | Derivado: sin SSN ni ITIN → `true` (RR-06) |
| `emergency_contact`, `blood_type`, `allergies` | Fase 3 |
| `status_light_state_id` | FK a `status_light_state` — 12 estados |
| `validated_by`, `validated_at` | RR-07: validación manual de la Reclutadora |

> [!warning] `status_light_state_id` es una llave foránea, nunca texto libre. Las transiciones válidas viven en `status_light_transition`.

### Requisición y Posición

`requisition` — `number` con formato `AAAAMMDDHHMM` + homoclave de 2 caracteres, `hotel_id`, `status_light_state_id`, `created_by`, `authorized_by`, `authorized_at`, `inspector_id` (asignado automáticamente por zona, RR-13).

`position` — `requisition_id`, `catalog_position_id`, `quantity`, `start_date`, `hotel_department_id`, `coverage_status`, `urgency`.

La urgencia **no se guarda como verdad**: se recalcula. Fórmula de RR-H-05, sobre `authorized_at` vs `start_date`:

| Diferencia | Urgencia |
|---|---|
| `> 120 h` | Verde fuerte (Normal) |
| `72 – 120 h` | Amarillo (Medio) |
| `< 72 h` | Rojo (Urgente) |

Un job de Cloud Scheduler la reevalúa periódicamente.

### Slot — la unidad de bloqueo

`slot` — `position_id`, `index`. Una fila por cada persona solicitada: `quantity = 3` genera 3 slots.

Es lo que permite cumplir RR-15 sin Redis. La asignación bloquea **la fila del slot**, no la requisición:

```sql
BEGIN;
  SELECT id FROM slot
   WHERE position_id = $1 AND status = 'free'
   ORDER BY index
   LIMIT 1
   FOR UPDATE SKIP LOCKED;      -- gana el primero; el segundo ve otro slot o ninguno

  INSERT INTO assignment (...);
  UPDATE slot SET status = 'taken' WHERE id = $slot;
  -- recalcular cobertura de la posición y estado de la requisición
  INSERT INTO journal (...);     -- RR-16
COMMIT;
```

`FOR UPDATE SKIP LOCKED` da exactamente la semántica de la regla: gana el primero, el segundo ve *"posición ya cubierta"*, y nadie queda esperando.

### Asignación

`assignment` — `slot_id`, `worker_id`, `type` (`fixed` | `temporary`), `validity` (rango de fechas), `assigned_by`, `status`.

**RR-05 no se hace cumplir aquí.** Se precisó el 2026-08-12: el colaborador **sí puede** estar en dos hoteles el mismo día, siempre que los turnos no se solapen. Y `validity` es un `daterange`, así que solo ve **días**: Hotel A de 6am a 2pm y Hotel B de 3pm a 11pm el mismo día se ven como el mismo día, y una restricción de exclusión sobre `validity` los rechazaría — bloqueando el caso legal.

La restricción de exclusión por **horas** vive en `operations`, sobre el turno del día, donde hay un `tstzrange` con fecha y hora reales:

```sql
EXCLUDE USING gist (worker_id WITH =, shift_range WITH &&)
```

Con timestamps reales sale gratis lo que con horas sueltas es un dolor: el **turno nocturno** (10pm-6am cruza medianoche y un rango de solo-hora no lo puede representar) y los **días alternados** (lunes/miércoles en un hotel, martes/jueves en otro: simplemente no hay renglón el martes).

> [!warning] Consecuencia asumida
> Hasta que exista `operations`, el solape de horas solo lo evita la aplicación. Lo que la base **sí** impide desde hoy es que dos colaboradores tomen el mismo slot: `ux_slot_active_assignment`, un único parcial sobre `slot_id WHERE status='ACTIVE'`.

### Participación — el Self-Pick

`participation` — `requisition_id`, `user_id`, `joined_at`, `left_at`.

Modela RR-15 completo:

- Varios reclutadores activos a la vez: varias filas con `left_at IS NULL`
- **Unirse** inserta una fila; no desplaza a nadie
- **Salir** solo llena `left_at`; no revierte lo asignado
- La requisición vuelve a *Autorizada* solo cuando `COUNT(*) WHERE left_at IS NULL = 0`

---

## 4. Los semáforos como datos

Las 7 máquinas de estado no se codifican: se configuran.

| Tabla | Contenido |
|---|---|
| `status_light` | Los 7: Colaborador, Requisición, Posiciones, Urgencia, Onboarding, Calidad, Cumplimiento |
| `status_light_state` | `status_light_id`, `code`, `color`, `name`, `description` |
| `status_light_transition` | `from_state_id`, `to_state_id`, `authorized_role_id`, `requires_reason`, `requires_evidence` |

Así, *"solo el Manager General o el Manager de Área pueden autorizar"* (RR-H-02) es una fila, no un `if`. Y añadir un estado no requiere desplegar.

> [!note] Regla de oro respetada
> Un semáforo = una dimensión. Un color = un significado **por semáforo**. Los colores se reutilizan entre semáforos distintos, y por eso `status_light_state` está calificado por `status_light_id`.

---

## 5. Ponche — evidencia de presencia

El ponche dejó de ser un escaneo de QR. Ahora el colaborador lo captura **desde su teléfono, en la propiedad**, con GPS y foto. Como de aquí sale la nómina y la factura, se modela como **evidencia probatoria**, no como una marca de tiempo.

> [!info] Método de ponche por hotel (2026-09-07)
> La foto es la evidencia del método **Selfie**, el de por defecto. Desde el 2026-09-07 el hotel puede optar por **QR del hotel**: un código secreto con versión, impreso en el acceso, que la app escanea y el servidor verifica junto con la geocerca. El método es un dato de `commercial.hotel` (`punch_method`, `punch_qr_secret`, `punch_qr_version`, `punch_qr_generated_at`) y la marca guarda **la versión del código escaneado** (`punch_mark.qr_version`), nunca el secreto — es la evidencia equivalente a `photo_path`, y `ck_punch_evidence` exige una de las dos en Entrada y Salida (o que la marca sea manual). Regla en [[Core/Módulos/Reglas de Negocio#Método de ponche por hotel|Reglas de Negocio]]; migración `20260907T1200_hotel_punch_method`.

### `operations.punch_mark`

| Campo | Notas |
|---|---|
| `timesheet_id`, `work_date` | Una jornada por fecha |
| `type` | `CLOCK_IN` · `LUNCH` · `CLOCK_OUT` |
| `lat`, `lng`, `accuracy_m` | Lectura cruda del dispositivo. Se guarda siempre, incluso si se rechaza |
| `inside_geofence` | **Lo calcula el servidor**, nunca lo envía la app |
| `photo_path` | Cloud Storage. Obligatoria en `CLOCK_IN` y `CLOCK_OUT`; nula en `LUNCH` |
| `captured_at` | Reloj del **dispositivo** — el momento real del ponche |
| `synced_at` | Reloj del **servidor** — cuándo llegó |
| `source` | `mobile` \| `manual` |
| `recorded_by`, `reason` | Obligatorios solo cuando `source = 'manual'` |
| `status` | `pending` \| `reviewed` \| `approved` \| `rejected` (D-09) |
| `reviewed_by`, `reviewed_at` | [[Hotel/Supervisor|Supervisor]]: resuelve anómalos y deja la jornada lista |
| `approved_by`, `approved_at` | [[Hotel/Manager de Área|Manager de Área]] del departamento o [[Hotel/Manager General|Manager General]] del hotel. **Nadie más** |

**Solo las marcas en `approved` entran al cálculo de horas.** Una marca `pending` existe, se ve en el calendario y no vale dinero. Ese filtro es lo que separa la operación de la nómina (D-09).

El **departamento** que decide quién puede aprobar no se guarda en la marca: se deriva por `timesheet → assignment → slot → position.hotel_department_id`. Duplicarlo aquí sería un segundo lugar donde puede quedar desincronizado.

Se guardan **las dos horas** a propósito. Con captura offline, `captured_at` es la verdad operativa pero es manipulable; `synced_at` no es manipulable pero puede llegar horas después. Una diferencia anómala entre ambas es señal de revisión, no un error.

### La geocerca vive en el hotel

`commercial.hotel` gana `geofence` (`geography(Point)` de PostGIS) y `radius_m`. No es lo mismo que [[Zonas|Zona]]: la Zona agrupa hoteles para asignar [[Inspector]]; la geocerca es el perímetro físico donde el ponche es válido.

### El servidor decide, el teléfono no

```sql
SELECT ST_DWithin(
  h.geofence,
  ST_MakePoint($lng, $lat)::geography,
  h.radius_m + LEAST($accuracy_m, h.max_tolerance_m)
)
```

Si la app decidiera *"estoy dentro"*, bastaría una aplicación de GPS falso para cobrar un turno no trabajado. La app manda coordenadas; el backend responde 422 si quedan fuera.

### Bloqueo fuera de la geocerca

**Regla elegida: el ponche se bloquea.** Eso obliga a definir la tolerancia con cuidado: el radio se amplía con la precisión que reporta el dispositivo, acotada por un máximo configurable por hotel, y una lectura con precisión peor que ese máximo se rechaza en lugar de aceptarse "por si acaso".

> [!warning] **Hueco: falta la válvula de escape.**
> Si el GPS no ubica al colaborador —cocina en sótano, edificio con estructura metálica, teléfono con GPS degradado— un colaborador que **sí** trabajó no puede ponchar y no cobra. [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] sección *Fallo de ponche* solo contempla reintentar, lo que no resuelve este caso.
> Propuesta a autorizar: **ponche manual del [[Hotel/Supervisor|Supervisor]]** con motivo obligatorio, `source = 'manual'` y asiento en el journal (sección 7). Es una regla de negocio, no una decisión técnica.

### Las cuatro marcas

> [!important] Conflicto **RESUELTO** el 2026-08-13, con autorización del usuario.
> El vault se contradecía: `Reglas de Negocio` y `Timesheet.md` decían **tres** marcas; `Schedule.md` seguía diciendo **seis**. Ninguna de las dos quedó.
>
> Son **cuatro**: `CLOCK_IN` · `LUNCH_OUT` · `LUNCH_IN` · `CLOCK_OUT`. Solo las dos de los extremos son **obligatorias**; el par de lunch es opcional.
>
> Qué resolvió cada pieza:
> 1. **El regreso de lunch se recupera**, y con él el [[Timesheet|Indicador de Lunch Extendido]], que con tres marcas era incalculable.
> 2. **Los breaks se eliminan.** Se evaluó recuperar su par y se descartó: con la deducción fija no descuenta nada, así que serían dos marcas que nadie poncharía.
> 3. **La deducción sigue siendo fija en 30 minutos**, aunque ahora se conozca la duración real. Es deliberado: el par de lunch alimenta el indicador —supervisión interna de Oranje— y **no** el pago.
>
> Consecuencia asumida: quien se toma 90 minutos de lunch cobra 60 de ellos. Y como el par es opcional, **solo ponchan los cumplidos**, así que el indicador ve menos de lo que busca.

### Fotos

Van a **Cloud Storage con URL firmada**, no a Firestore. Deben venir de la cámara, no de la galería: si se puede adjuntar una imagen guardada, la foto no prueba nada.

Volumen a presupuestar: 5,000 colaboradores × 2 fotos × 5 días ≈ **50,000 fotos por semana**, del orden de 500 GB al año a 200 KB por imagen. Exige comprimir en el dispositivo y una política de retención con paso a clase Nearline. La foto de una persona es dato personal: aplica la auditoría de acceso de D-07 y queda asentada en el journal.

---

## 6. Liquidación — donde vive el margen

### Contrato

`contract` — `hotel_id`, `validity`, `week_start`, `week_end`, `holidays`, `overtime_threshold` (40 h brutas **por colaborador y por requisición**, D-10), `invoice_split_by_month`.

`contract_rate` — `contract_id`, `catalog_position_id`, `pay_rate`, `bill_rate`, `overtime_rate`.

Las tarifas van **por posición**, no por hotel: un Chef y un Housekeeper no cuestan lo mismo.

> [!important] `pay_rate` nunca aparece en la factura. `bill_rate` nunca aparece en el recibo del colaborador. Son dos consultas distintas sobre el mismo timesheet — y esa separación es el negocio.

### Consolidado y Factura

`weekly_consolidation` — `worker_id`, `year`, `week`, `status` (`reviewed` por [[Contadora]] → `approved` por [[Manager de Contabilidad]]).

`consolidation_detail` — **una fila por requisición** en la que trabajó esa semana: `requisition_id`, `hotel_id`, `regular_hours`, `overtime_hours`, `applied_pay_rate`, `subtotal`.

Esta separación es la que hace posible *"el cálculo aplica el pay rate del Contrato de cada hotel por separado"* y *"el overtime se calcula por requisición"*.

> [!important] El detalle se rompe **por requisición**, no por hotel (D-10). El umbral de 40 horas se evalúa sobre cada fila por separado, y las horas de requisiciones distintas no se suman para decidir overtime. El vínculo no requiere tablas nuevas: `assignment → slot → position → requisition`.

`invoice` — `hotel_id`, `year`, `week`, `folio`, `calendar_month`, `status`. Cuando una semana cruza dos meses y el contrato lo pide, se generan **dos filas** con distinto `calendar_month`.

### Horas pagables

```
net_hours = (salida − entrada) − actual_lunch − actual_breaks
```

> [!important] Actualizada el 2026-08-13
> La fórmula anterior era `(salida − entrada) − actual_lunch − actual_breaks` y presuponía seis ponches. Con las **cuatro marcas** de la sección 5 la fórmula real es:
>
> ```
> net_hours = (salida − entrada) − 30 min
> ```
>
> Fija, siempre. `actual_breaks` desaparece —los breaks ya no se ponchan ni se deducen— y `actual_lunch` **se mide pero no se descuenta**: alimenta el Indicador de Lunch Extendido, no el pago.

---

## 7. Journal — inmutable de verdad

`journal` — `entity_type`, `entity_id`, `event_type`, `actor_user_id`, `actor_role`, `payload` (JSONB), `occurred_at`.

RR-16 exige inmutabilidad. Se garantiza quitando el permiso, no confiando en la disciplina:

```sql
REVOKE UPDATE, DELETE ON journal FROM app_user;
```

Tipos de evento de RR: `TOOK`, `JOINED`, `LEFT`, `STATUS_CHANGED`, `ASSIGNED_WORKER`, `REASSIGNED`.

El mismo journal cubre la auditoría de acceso a PII: quién consultó qué expediente y cuándo.

---

## 8. Blacklist

`blacklist_entry` — `worker_id`, `reason`, `evidence_path`, `entered_by`, `source` (`MANUAL` | `ABSENCES` | `DISPUTE`), `occurred_at`, más `lifted_at`, `lifted_by` y `lift_reason`.

**Sin borrado, pero con baja.** El registro nunca se elimina; el veto se **levanta** marcando los tres campos de `lifted_*`, que van juntos o no van (un CHECK lo obliga: levantar un veto es la acción que más necesita quedar atribuida). Solo un perfil de Administrador o de Reclutamiento puede hacerlo, y el colaborador regresa a **Blanco**.

Es por eso una tabla de **historial**: la misma persona puede entrar, salir y volver a entrar. Lo que impide dos vetos vigentes a la vez es un único parcial, `ux_blacklist_active` sobre `worker_id WHERE lifted_at IS NULL`.

`reason` y `evidence_path` son `NOT NULL`: la regla exige evidencia obligatoria.

---

## 9. Permisos

Autorizar tiene **dos preguntas**, y confundirlas es el error clásico:

1. **¿Este rol puede hacer esta acción, en principio?** → la Matriz de Permisos.
2. **¿Sobre qué filas?** → el alcance de la persona.

### La capacidad: `identity.role_permission`

`role_permission` — `role_id`, `module`, `action`.

La Matriz de Permisos de cada departamento se carga como filas. Agregar un módulo es un `INSERT`, nunca una migración de esquema.

> [!warning] Nunca columnas booleanas de permiso en la tabla de usuario. Con 16 roles y 9 módulos por departamento, ese diseño obliga a migrar y desplegar por cada cambio.

### El alcance: vive en la persona, no en el permiso

Esta tabla **no** lleva `hotel_id`. Si lo llevara, la fila diría *"el rol Manager de Área aprueba horas en el Hotel Riu"* — y entonces habría que insertar una fila por cada hotel, por cada departamento y por cada acción, y mantenerla cada vez que alguien cambia de puesto.

El alcance es un hecho de la persona, y hay **dos formas** porque el negocio tiene dos:

| Alcance | Dónde vive | Quién lo usa |
|---|---|---|
| Un hotel, un departamento | `identity.user.hotel_id` + `identity.user.department_id` (FK a [[Departamentos del Hotel]]) | [[Hotel/Manager de Área\|Manager de Área]], [[Hotel/Supervisor\|Supervisor]] |
| Un hotel, todos sus departamentos | `user.hotel_id` con `department_id IS NULL` | [[Hotel/Manager General\|Manager General]] |
| Zonas, sin hotel fijo | `commercial.user_zone` (N:M) con `user.hotel_id IS NULL` | [[Business Developer\|BD]], [[Business Developer Coordinator\|BDC]], Inspector |

`department_id` nulo **no** significa "sin alcance": significa **todos los departamentos de mi hotel**. Es lo que separa al Manager General del Manager de Área sin una tabla de excepciones.

La autorización queda así, y es una sola consulta:

```
puede(user, accion, fila) =
      existe role_permission(user.role_id, modulo, accion)
  AND fila.hotel_id = user.hotel_id
  AND (user.department_id IS NULL
       OR user.department_id = fila.department_id)
```

> [!important] Por qué no hay tabla de concesiones individuales
> **D-09** describe el permiso de aprobar como *"una fila acotada al hotel"*. El alcance que exige se cumple igual con las columnas de arriba, y sin una tabla que alguien tenga que mantener a mano.
>
> Una tabla de concesiones se justificaría si el negocio delegara la aprobación —un suplente durante vacaciones— pero **el vault no delega**: el respaldo documentado es el Manager General, que ya alcanza todos los departamentos de su hotel por `department_id IS NULL`. Una tabla de concesiones sin nadie que conceda se llena de filas viejas que nadie se atreve a borrar.
>
> Si algún día se delega, la migración es aditiva y no toca esto.

> [!warning] Guard del servicio, no restricción
> Un Manager de Área **debe** tener `department_id`; un Manager General **no debe** tenerlo. La regla cruza `identity.user` con `identity.role`, así que ningún `CHECK` la expresa: va como trigger con el comentario de su regla, o como validación al crear el usuario.

> [!note]
> Todo este documento usa los identificadores en inglés que fija **D-11**, esquemas incluidos. El puente con los términos del vault es el glosario canónico de [[Estructura de Proyecto y Nomenclatura]] sección 8.

---

## 10. Invariantes que la base de datos hace cumplir

| # | Regla | Mecanismo |
|---|---|---|
| 1 | Un slot, una asignación activa | `UNIQUE (slot_id) WHERE status='active'` |
| 2 | Sin solape de **horas** (RR-05) | `EXCLUDE USING gist` sobre el `tstzrange` del turno — vive en `operations`, no en `assignment` |
| 3 | Gana el primero en el slot (RR-15) | `FOR UPDATE SKIP LOCKED` |
| 4 | Journal inmutable (RR-16) | `REVOKE UPDATE, DELETE` |
| 5 | Blacklist con evidencia (RR-03) | `NOT NULL` en `reason` y `evidence_path` |
| 6 | Requisición no se autoriza sin posiciones (RR-H-03) | Trigger `BEFORE UPDATE` |
| 7 | Estados solo por transición válida | FK a `status_light_transition` |
| 8 | Cobertura 100% para cerrar (RR-04) | Trigger sobre `slot` |
| 9 | Ponche móvil solo dentro de la geocerca | `ST_DWithin` en el servidor + `CHECK (source <> 'mobile' OR inside_geofence)` |
| 10 | Foto obligatoria en entrada y salida | `CHECK (type = 'LUNCH' OR photo_path IS NOT NULL)` |
| 11 | Una marca por tipo y jornada | `UNIQUE (timesheet_id, work_date, type)` |
| 12 | Ponche manual siempre justificado | `CHECK (source <> 'manual' OR (reason IS NOT NULL AND recorded_by IS NOT NULL))` |
| 13 | Solo el ponche aprobado paga (D-09) | El cálculo de horas filtra `status = 'approved'`; `CHECK (status <> 'approved' OR approved_by IS NOT NULL)` |
| 14 | El aprobador tiene alcance sobre la marca (D-09) | `approved_by` es Manager de Área del `department_id` de la jornada, o Manager General de su `hotel_id`. Ningún otro rol |
| 15 | Overtime por requisición (D-10) | `UNIQUE (consolidation_id, requisition_id)` en `consolidation_detail` |

---

## 11. Decisiones pendientes

Preguntas que hay que responder **antes** de escribir el esquema, porque cambian tablas:

1. ~~**RR-05 — ¿exclusividad absoluta o cobertura temporal?**~~ **RESUELTO el 2026-08-12:** el colaborador **sí puede** estar en dos hoteles el mismo día, si las horas no se solapan. Ni exclusividad absoluta ni el `COVER` del `GEMINI.md`: el criterio son las **horas**, no el hotel ni el tipo de asignación. La restricción se mudó a `operations` porque un `daterange` no puede expresarlo. Ver el apartado *Asignación* de la sección 3.
2. **¿Tres marcas de ponche o seis?** El sección 5 documenta el conflicto completo. Con tres se pierden lunch real, breaks y el Indicador de Lunch Extendido, y cambia lo que se paga y lo que se factura. Exige modificar [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] y [[Timesheet]].
3. **Válvula de escape del ponche bloqueado.** Si el GPS falla dentro de la propiedad, hoy el colaborador no puede ponchar y no cobra. Falta la regla del ponche manual del [[Hotel/Supervisor|Supervisor]] y quién lo autoriza.
4. **PostGIS en Cloud SQL.** La geocerca lo asume. Hay que habilitar la extensión y decidir si el perímetro es punto + radio o polígono por hotel.
5. **Horas aprobadas después del cierre semanal.** Si el [[Hotel/Manager de Área|Manager de Área]] no aprueba a tiempo, las horas quedan en espera indefinidamente (D-09). Falta decidir si al aprobarse reabren el `weekly_consolidation` de su semana o entran en el siguiente — el umbral de overtime es semanal, así que la elección cambia el pago.
6. **Colaborador que sirve a dos departamentos.** La aprobación es por departamento (D-09), así que sus jornadas las aprueban dos Managers de Área distintos —o el [[Hotel/Manager General|Manager General]] de una vez—. Falta confirmar si eso parte el [[Timesheet]] por departamento o si basta con que cada marca lleve su `department_id`.

---

## Relacionado

- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] — fuente de verdad
- [[Arquitecturas/_Globales/Árbol de Reglas de Negocio|Árbol de Reglas de Negocio]]
- [[Arquitecturas/_Globales/Índice de Arquitecturas|Índice de Arquitecturas]]
- [[Arquitecturas/_Globales/Estándares de Base de Datos|Estándares de Base de Datos]] — cómo se escribe y evoluciona este esquema
- [[Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]] — D-05 y D-08, el ponche como evidencia
- [[Core/Módulos/Contrato|Contrato]] · [[Core/Módulos/Schedule|Schedule]] · [[Timesheet]]
- [[Core/Módulos/Requisicion/Requisición|Requisición]] · [[Pool de Colaboradores]] · [[Core/Módulos/Blacklist|Blacklist]]
- [[Semáforo del Colaborador]] · [[Semáforo de Requisición]] · [[Semáforo de Posiciones de la Requisición]]
- [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] · [[Contabilidad/Facturación al Hotel|Facturación al Hotel]] · [[Deducciones]]
