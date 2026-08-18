---
tags:
  - arquitectura
  - globales
  - decisiones
aliases:
  - Decisiones de Arquitectura
  - ADR Oranje
  - Arquitectura de Despliegue
---

# Decisiones de Arquitectura

> [!info] Alcance
> Decisiones de infraestructura, despliegue, stack y modelo, con su justificación. Acompaña al diagrama [[Arquitecturas/_Globales/Arquitectura GCP - Propuesta.drawio|Arquitectura GCP - Propuesta]] y al [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]].

---

## D-01 · Monolito modular en un solo desplegable

**Decisión:** un repositorio, un servicio de Cloud Run, una base de datos. Los módulos son carpetas, no servicios.

### Por qué

Asignar un colaborador a un slot dispara cinco operaciones que deben ser atómicas:

1. Ocupar el slot
2. Recalcular la cobertura de la posición
3. Recalcular el estado de la requisición
4. Escribir el journal (RR-16)
5. Generar la entrada en el [[Core/Módulos/Schedule|Schedule]]

Con servicios separados eso son llamadas de red. Si la segunda falla, el slot queda ocupado sin asignación. Y dos reclutadores podrían ganar el mismo slot — exactamente lo que RR-15 prohíbe.

En un solo proceso es **una transacción de Postgres**: o pasa todo, o no pasa nada.

### Estructura

```
oranje-backend/
├── src/
│   ├── modules/
│   │   ├── identity/        Roles y matriz de permisos
│   │   ├── commercial/        Onboarding · Contrato
│   │   ├── demand/          Requisición · Posición · Slot
│   │   ├── coverage/        Self-Pick · Asignación · Blacklist
│   │   ├── operations/        Schedule · Timesheet · Ponche
│   │   ├── settlement/      Consolidado · Deducciones · Factura
│   │   ├── supervision/      Inspección · QA · Customer Service
│   │   ├── journal/          Append-only
│   │   ├── notifications/   Consumidor de eventos
│   │   └── catalogs/        Posiciones · Zonas · Modalidades
│   └── main.ts
└── Dockerfile
```

### Fronteras reales

Los módulos no son decorativos:

| Nivel | Cómo se separa |
|---|---|
| Código | Un módulo llama a otro por su interfaz pública, nunca a sus tablas |
| Base de datos | Un esquema de Postgres por módulo (`demand.requisition`, `settlement.invoice`) |
| Equipo | Cada módulo puede tener dueño distinto |

### Si algún día hay que partirlo

Candidatos: **Notificaciones** y **Supervisión**. Ninguno participa en la transacción del núcleo; ambos son consumidores de eventos.

> [!warning] **Demanda, Cobertura y Operación no se separan nunca.** Están unidos por la transacción, y esa unión es una regla de negocio, no un detalle de implementación.

### Escala que lo justifica

5,000 colaboradores y ~100 usuarios concurrentes. A ese volumen los microservicios solo agregan latencia, complejidad y modos de falla.

### Un esquema por módulo: reafirmado el 2026-08-07

Se evaluó la alternativa **plana** —todas las tablas en `public`, sin prefijo— y se descartó. El argumento en contra que hay que conocer, porque es real: **con un solo backend y un solo usuario de conexión, los esquemas no hacen cumplir nada.** El aislamiento de "un módulo no lee las tablas de otro" se logra con `GRANT` por rol de Postgres, y eso exige un usuario de conexión por módulo, que este monolito no tiene. Sin eso, el esquema es documentación ejecutable, no una barrera.

Se mantiene porque el prefijo dice de quién es cada tabla sin abrir nada, y porque **la decisión es reversible en los dos sentidos** con `ALTER TABLE ... SET SCHEMA`: una migración mecánica, sin tocar datos.

Lo que sí dejó de ser un riesgo: **`multiSchema` de Prisma es GA desde la versión 6.13.0** — ya no es *preview feature* y no lleva flag. Era la única dependencia técnica frágil de esta decisión.

---

## D-02 · Concurrencia del Self-Pick en Postgres, sin Redis

**Decisión:** el bloqueo de RR-15 se resuelve con `SELECT ... FOR UPDATE SKIP LOCKED` sobre la fila del slot.

**Por qué:** da exactamente la semántica de la regla —gana el primero, el segundo ve *"posición ya cubierta"*, nadie espera— dentro de la misma transacción que actualiza la cobertura. Un lock en Redis sería una pieza móvil externa a la transacción: si el proceso muere entre el lock y el commit, el estado queda inconsistente.

Memorystore queda como caché de sesiones, opcional. Detalle en [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]] sección 3.

---

## D-03 · Pub/Sub fuera del núcleo transaccional

**Decisión:** la mensajería asíncrona solo cubre notificaciones, emails, recálculo de urgencia e indicadores de QA. Nunca la asignación.

**Por qué:** meter el núcleo en un bus lo vuelve de consistencia eventual, y entonces "gana el primero" deja de cumplirse.

Volumen real: decenas o cientos de eventos por hora. No requiere streaming ni Kafka.

---

## D-04 · Sin API Gateway

**Decisión:** Cloud Load Balancing enruta directo a Cloud Run.

**Por qué:** con un solo backend, un API Gateway agrega un salto y un costo sin aportar ruteo, cuotas ni versionado que hagan falta. El Load Balancer sí se justifica por **Cloud Armor** (WAF), que con SSN e ITIN de por medio no es opcional.

---

## D-05 · Firebase para la app móvil

**Decisión:** Firebase Auth (JWT), FCM para push y Firestore para sincronización offline en la app del [[Colaborador/Colaborador|Colaborador]] y del [[Inspector]].

**Por qué:** el colaborador **poncha desde su teléfono, dentro de la propiedad, con GPS y foto**, en lugares donde muchas veces no hay señal; el Inspector marca GPS en zona. La sincronización offline es un requisito operativo, no una comodidad. Firestore la resuelve de fábrica.

La autoridad de identidad es Firebase Auth; los **permisos** viven en Postgres (`identity.role_permission`), no en el token.

> [!warning] Firestore es buzón, no fuente de verdad. Guarda las marcas que el dispositivo no pudo enviar; en cuanto hay red, viajan al backend y **Postgres decide**. Ningún cálculo de nómina, cobertura ni semáforo lee de Firestore.

**Las fotos no pasan por Firestore.** Un documento de Firestore tope 1 MB. La imagen se guarda en el almacenamiento local del dispositivo y sube a **Cloud Storage con URL firmada** cuando hay señal; en Firestore solo viaja la marca con su referencia.

---

## D-06 · Un proyecto de GCP por ambiente

**Decisión:** `oranje-prod`, `oranje-staging`, `oranje-dev` como proyectos separados.

**Por qué:** aislamiento duro. Un experimento no puede tocar la base de datos de nómina.

---

## D-07 · Observabilidad y secretos desde el día uno

**Decisión:** Cloud Logging y Monitoring con alertas sobre el cálculo de nómina, Secret Manager, y cifrado de campo para SSN e ITIN.

**Por qué:** un error de cálculo de nómina **no lanza excepción**. Paga mal y nadie se entera hasta que reclama el colaborador. Postergar la observabilidad a una fase posterior no es viable en un sistema que mueve dinero y datos fiscales.

---

## D-08 · El ponche se valida en el servidor

**Decisión:** el backend evalúa la geocerca, guarda las dos horas (dispositivo y servidor) y exige que la foto venga de la cámara. La app **nunca** decide si el ponche es válido.

### Por qué

El ponche produce nómina y factura. Todo lo que el cliente afirme sobre él es, en la práctica, un campo editable:

| Amenaza | Mitigación |
|---|---|
| GPS falso por aplicación | La app manda coordenadas crudas; `ST_DWithin` corre en Postgres. Si la app enviara `dentro_de_geocerca`, el control no existe |
| Reloj del dispositivo alterado | Se guardan `capturado_at` (dispositivo) y `sincronizado_at` (servidor). La diferencia anómala se marca para revisión |
| Foto tomada de la galería | Captura solo desde cámara; sin selector de archivos |

Con captura offline no se puede usar la hora del servidor como verdad —el ponche ocurre a las 6:00 y sincroniza a las 14:00—, pero tampoco confiar ciegamente en el teléfono. Por eso se guardan las dos y se revisan las diferencias.

### El calendario del Supervisor es el punto de validación

No es una vista de consulta: es donde se aprueban las marcas limpias y se resuelven las anómalas antes de que el [[Timesheet]] alimente el Consolidado. Es el control humano que cierra lo que la geocerca no puede probar sola.

### Consecuencia operativa del bloqueo

Se decidió **bloquear** el ponche fuera de la geocerca. Eso deja abierto qué pasa cuando el GPS falla dentro de la propiedad (sótanos, cocinas, estructura metálica): sin regla de excepción, un colaborador que trabajó no cobra. Ver [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]] sección 5.

---

## D-09 · La aprobación del hotel es el corte entre operación y dinero

**Decisión:** el ponche tiene estado. Solo los ponches **aprobados por el [[Hotel/Manager de Área|Manager de Área]] —el de su departamento— o por el [[Hotel/Manager General|Manager General]] —cualquier departamento—** alimentan el [[Timesheet]], el Consolidado y la factura. El [[Hotel/Supervisor|Supervisor]] revisa y resuelve anómalos, pero **no aprueba**: son dos pasos con responsables distintos.

### Por qué

Sin ese corte, cualquier marca capturada en un teléfono entra directo al cálculo de nómina. Con GPS y foto la evidencia es fuerte, pero no es prueba: el GPS falla en sótanos y la foto no dice si la persona trabajó el turno completo. La aprobación humana es lo que convierte una captura en un dato pagable.

### Consecuencias técnicas

| Consecuencia | Detalle |
|---|---|
| Máquina de estados del ponche | `pendiente → revisado → aprobado` \| `rechazado`. El cálculo de horas filtra por `aprobado` |
| Dos acciones, dos permisos | `revisar` (Supervisor) y `aprobar` (Manager de Área · Manager General) son acciones distintas en `identity.role_permission`, no una sola con varios titulares |
| El Consolidado depende de una acción humana | Si nadie aprueba, la semana no se puede cerrar. Necesita alerta de ponches pendientes antes del corte semanal |
| Journal | La revisión, la aprobación y el rechazo se asientan con actor y motivo (RR-16) |
| Alcance del permiso | Aprobar se acota **al departamento**, no al hotel ni a la requisición. El Manager de Área alcanza `hotel_id + department_id`; el Manager General, solo `hotel_id` |

> [!note] Cómo se implementa el alcance (resuelto el 2026-08-07)
> Esta decisión decía *"el permiso lleva `hotel_id + departamento_id`"*, lo que se leía como columnas de alcance en la tabla de permisos. **El efecto se conserva y el mecanismo cambia:** el alcance vive en `identity.user` (`hotel_id` + `department_id` nulable), y la tabla de permisos queda como la Matriz pura por rol.
>
> El motivo: con el alcance en el permiso hay que insertar una fila por hotel, por departamento y por acción, y mantenerla cada vez que alguien cambia de puesto. Con el alcance en la persona, cambiar a alguien de departamento es un `UPDATE` de una columna. `department_id IS NULL` es lo que distingue al Manager General.
>
> Ver [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]] sección 9 para el diseño completo y la consulta de autorización.

### Si no aprueba a tiempo

**Las horas quedan en espera.** No se pagan ni se facturan y no hay aprobación automática: el estado `pendiente` simplemente persiste.

| Consecuencia | Detalle |
|---|---|
| El estado no caduca | Ningún job cambia `pendiente → aprobado` por vencimiento de plazo. Un cron que "cierre" la semana aprobando lo pendiente contradiría esta decisión |
| El cierre semanal es parcial | El Consolidado se genera con lo aprobado; lo pendiente queda fuera y sigue vivo |
| El respaldo es humano, no automático | El [[Hotel/Manager General|Manager General]] puede aprobar lo que un Manager de Área dejó pendiente, pero **nadie le transfiere la jornada**: tiene que verla. Por eso la alerta de pendientes antes del corte no es una comodidad — es lo que convierte ese respaldo en algo real |

> [!warning] Pendiente con Contabilidad
> Falta definir qué pasa con las horas aprobadas **después** del cierre semanal: si reabren el [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado]] de su semana o entran en el siguiente. No es cosmético: el umbral de overtime de D-10 es semanal, así que mover horas de semana cambia lo que se paga.

---

## D-10 · El umbral de overtime se evalúa por requisición

**Decisión:** las 40 horas brutas semanales se cuentan **por colaborador y por requisición**, no por hotel. Lo que exceda cuenta como overtime dentro de esa misma requisición.

### Por qué importa en el modelo

`consolidado_detalle` estaba diseñado con **una fila por hotel**. Si el umbral es por requisición y un colaborador puede participar en más de una requisición del mismo hotel en una semana, la fila por hotel ya no alcanza: el detalle se rompe **por requisición**, y de ahí sale tanto el pago como la factura.

El vínculo existe en el modelo sin agregar tablas: `assignment → slot → position → requisition`.

> [!note] Confirmado el 2026-08-12
> La consecuencia que este ADR marcaba como *"a validar con Contabilidad"* — que un colaborador con 40 horas en dos requisiciones distintas acumula **80 horas sin generar overtime** — **es la regla, no un descuido.** [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] la enuncia sin ambigüedad: *"si el colaborador trabaja en más de una requisición durante la semana, el umbral se evalúa en cada una por separado; las horas de requisiciones distintas no se suman para efectos de overtime"*.
>
> Antes, con el umbral por hotel, dos requisiciones del mismo hotel sí lo generaban.

### Cómo se derivan las 8 horas diarias

No son una regla aparte: salen de **40 ÷ 5**. [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] fija la semana laboral en 7 días — **5 de trabajo + 2 de descanso** — y el bruto semanal en 40 horas (8 hrs × 5 días).

Consecuencia para `operations`: el overtime se puede generar de **dos maneras** que caen en la misma bolsa de 40 horas — un día más largo, o un sexto día. No hay tope diario por requisición, y ponerlo sería un error: el colaborador **sí** se puede quedar horas extra, y esas horas hay que registrarlas y pagarlas.

> [!warning] El overtime NO es una restricción de base de datos
> Se evaluó escribirlo como `CHECK` y no funciona, por dos razones:
> 1. Un `CHECK` solo ve su propia fila. Las horas esperadas viven en `demand.position` (`start_time` + 8h), en otra tabla.
> 2. Un `CHECK` solo sabe **rechazar**, y el overtime es un hecho que hay que **registrar**. No se tira un ponche porque el colaborador se quedó tarde.
>
> El overtime es un **valor derivado**: `max(0, horas_reales − horas_esperadas)` por día y por requisición. Lo único que sí merece `CHECK` es el absurdo: `worked_minutes BETWEEN 0 AND 1440` — un día no puede tener 25 horas, y eso sí atrapa un ponche corrupto o un error de zona horaria.

### Quién autoriza que el colaborador se quede

**Solo el [[Hotel/Manager de Área|Manager de Área]]** (2026-08-12). Es consistente con el reparto que ya existe: el [[Hotel/Supervisor|Supervisor]] **revisa** las horas y el Manager de Área las **aprueba**. El disparador es el ponche que cruza las 40 horas acumuladas de esa semana en esa requisición — el sistema lo sabe antes de que cierre la semana porque lleva el acumulado.

Asentado con autorización del usuario en [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] (nueva subsección *Autorización de horas extra*) y propagado a [[Core/Módulos/Timesheet|Timesheet]] y [[Hotel/Manager de Área|Manager de Área]].

### La requisición no tiene duración

Se evaluó la hipótesis de que una requisición dura **una semana**, y **la documentación no la sostiene**: la cabecera de [[Core/Módulos/Requisicion/Requisición|Requisición]] tiene 7 campos y **ninguno es fecha**, y la posición tiene fecha de inicio con una nota explícita de que **no tiene fecha de fin**. La requisición se cierra **por estado** — Azul claro, *cubierta totalmente*, en el [[Semáforo de Requisición]] — no por calendario; y la posición termina cuando el colaborador pasa a **Rosa**.

Lo que dura una semana es el **periodo de pago**, no la requisición. Por eso el umbral de 40 horas es semanal aunque la requisición siga abierta, y por eso `demand.position` **no lleva** fecha de fin: el diagrama de Demanda ya está correcto.

**Consecuencia resuelta:** la `RUTINA Journal Posición` registra un campo *"Fecha fin"* que parecía contradecir la regla de arriba. No la contradice — es el **cierre del periodo semanal** (los 7 días que se usan para pagar), no el fin de la posición. Aclarado con un callout en la fuente de verdad, porque el nombre invita a la lectura equivocada. Al modelarlo en `settlement`, la columna debe llamarse por lo que es (`week_end_date` o equivalente), no `end_date`.

### Deuda que cerró este ADR

Seis archivos afirmaban que el overtime se calculaba **por hotel**, contradiciendo la fuente de verdad. Corregidos el 2026-08-12: [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado Semanal]] (el cálculo y la tabla de contenido), [[Reglas del Colaborador]] y las tres Simulaciones (Ciclo de Vida del Colaborador, Punto de Vista de Reclutamiento, Punto de Vista de Contabilidad). No era cosmético: **por hotel, dos requisiciones del mismo hotel sí suman 80 horas y generan overtime; por requisición, no.** Cambia lo que se paga y lo que se factura.

---

## D-11 · El dominio del código va en inglés

**Decisión:** entidades, tablas, columnas, modelos, endpoints **y los 10 esquemas de Postgres** se nombran en **inglés**, sin excepciones. Todo lo que lee el usuario sigue en español.

### Alcance exacto

| Qué | Idioma |
|---|---|
| Tabla, columna, modelo Prisma, DTO, endpoint | **Inglés** |
| Esquema Postgres | **Español** — los 10 módulos de D-01 |
| `code` de un estado de semáforo | **Inglés** (`ORANGE`, `LIGHT_BLUE`) |
| `name` y `description` de catálogos, mensajes de error, textos de UI | **Español** |
| Términos que ya son ingleses en el vault (Schedule, Timesheet, Blacklist, Self-Pick, Onboarding) | Sin cambio |

### Por qué

El repositorio de código vigente ya está en inglés (`Employee`, `StaffingRequest`), y con el dominio en español habría que migrarlo o convivir a medias. La frontera queda en un solo lugar: **el identificador va en inglés, el texto que se muestra va en español.**

Los esquemas no se traducen porque los fija D-01 y lo repite [[Estándares de Base de Datos]] sección 2 (*"Esquema: nombre del módulo de D-01"*). Si algún día se traducen, cuesta el prefijo y nada más.

### Costo aceptado

El vault seguirá en español, así que buscar `Requisición` en el código no encontrará nada y buscar `StaffingRequest` en el vault tampoco. **El glosario canónico de [[Estructura de Proyecto y Nomenclatura]] sección 8 deja de ser documentación y pasa a ser obligatorio:** es lo único que evita que Propuesta se escriba `Proposal`, `Offer` y `Quote` en tres archivos distintos.

### Consecuencias documentales

**Los tres documentos afectados ya están alineados** (2026-08-06):

| Documento | Qué cambió |
|---|---|
| [[Estructura de Proyecto y Nomenclatura]] sección 8 | Enunciado invertido, ejemplos de código al revés, y el glosario de 20 filas reescrito con el identificador en inglés y la tabla Postgres en inglés. Nueva sección *El costo, y quién lo paga*: agregar un concepto obliga a agregar su fila en el mismo PR |
| [[Estándares de Base de Datos]] sección 2 y sección 15.3 | Toda la nomenclatura y sus ejemplos en inglés (`is_`/`has_` en vez de `es_`/`tiene_`, `created_at`, `ux_slot_active_assignment`), más un callout explicando por qué `demand.requisition` mezcla los dos idiomas a propósito. La pendiente 3 queda cerrada |
| [[Estándares de Desarrollo]] sección 4 | Rutas, campos JSON, códigos de error y ramas de Git en inglés, con una tabla nueva que fija la frontera: **identificadores en inglés, textos que lee una persona en español** (descripciones de test, `summary` de OpenAPI, asunto del commit) |

> [!warning]
> **El Colaborador se llama `Worker` en el código.** Decidido el 2026-08-06; con esto el idioma queda cerrado por completo.
>
> `Employee` está descartado: el [[Colaborador/Colaborador|Colaborador]] no es empleado del hotel —el vínculo es siempre vía `assignment`, con fechas— así que ese nombre mete en la entidad central una relación laboral que el negocio niega.
>
> Se evaluó y se descartó dejarlo en español como excepción: `colaborador` viviría dentro de `colaborador_id`, `getColaboradorById`, `availableColaboradores`, en la palabra más frecuente del sistema. Es exactamente el "medio identificador en cada idioma" que [[Estructura de Proyecto y Nomenclatura]] sección 8 rechaza. Y el 2026-08-07 desapareció el único precedente que quedaba: los esquemas también pasaron a inglés.
>
> **En el vault, en la UI y en las juntas sigue siendo Colaborador.** `Worker` solo existe en el código; el glosario canónico es el puente y por eso es obligatorio.

---

## D-12 · Redux Toolkit con RTK Query, Tailwind con NativeWind

**Decisión:** un solo store de Redux Toolkit por cliente. La caché del servidor vive en **RTK Query**, no en slices. Los estilos son **Tailwind**, con un preset compartido en `packages/ui`, y **NativeWind** en el móvil para escribir las mismas clases.

### Por qué

**Un solo `createApi`, endpoints inyectados por feature.** Si cada feature crea el suyo, se pierde la invalidación cruzada por tags —autorizar una requisición deja de refrescar el Schedule— y aparecen tantas cachés como features.

**La respuesta del servidor no se copia a un slice.** Un `requisiciones: []` dentro de un slice es la respuesta de ayer. Los slices guardan lo que el servidor no sabe: qué filtro está abierto, qué rol tiene activo el usuario.

**El preset de Tailwind se comparte; los componentes no.** Los tokens del sistema Oranje se definen una vez y sirven a web y móvil. Un `<button>` no existe en React Native, así que `packages/ui/src/components` es solo web.

### Qué se descarta

| Alternativa | Por qué no |
|---|---|
| TanStack Query + Zustand | Dos sistemas de estado conviviendo, con la regla frágil de no duplicar datos del servidor en el store |
| CSS plano del sistema Oranje | `oranje-seed.css` migra al preset; los tokens (`--o-500`, `--sb`, `--st-*`) no se renombran |
| Componentes compartidos web/móvil | Se escriben dos veces con la misma API antes que forzar uno solo |

Detalle de carpetas y nomenclatura en [[Estructura de Proyecto y Nomenclatura]] sección 1, sección 4, sección 6 y sección 7.5.

> [!important] Enmienda del 2026-08-10 · Tailwind 4 parte el preset en dos emisiones
> Esta decisión dice *"un preset compartido en `packages/ui`"*, y eso se escribió pensando en **Tailwind 3**, donde el preset es un objeto JS que se importa desde los dos `tailwind.config`. **Tailwind 4 eliminó la configuración en JS**: el tema se declara en CSS con `@theme`. Y **NativeWind estable (4.2.6) sigue exigiendo Tailwind 3** — el soporte de v4 está en `5.0.0-preview.4`.
>
> No se puede tener las dos cosas hoy, así que:
> - **`apps/web` va en Tailwind 4**, con `@tailwindcss/vite`. Es lo que la CLI de shadcn genera por defecto; arrancar la web en v3 es nacer con deuda.
> - **`apps/mobile` se quedará en Tailwind 3 + NativeWind 4** hasta que NativeWind 5 sea estable. Cuando lo sea, el móvil sube y la asimetría desaparece.
> - **`tokens.ts` sigue siendo la única definición**, que es lo que esta decisión realmente protege. Lo que cambia es su salida: en vez de un `tailwind-preset.ts` único, emite un `tokens.css` con `@theme` para el web y un objeto de tema para el config v3 del móvil. **Dos emisiones, una fuente.**
>
> Lo que se pierde: el preset deja de ser literalmente el mismo archivo en los dos lados. Lo que se conserva: que un color de semáforo se defina una sola vez. El riesgo real —que web y móvil diverjan— lo cubre `tokens.ts`, no el formato del preset.

---

## D-13 · El ciclo comercial es una entidad, y el semáforo de onboarding vive en él

**Decisión:** `prospect` modela un **ciclo comercial** de un hotel, y ahí vive el estado del [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]]. `hotel` guarda solo lo permanente del edificio más `activated_at`. Un hotel no puede tener dos ciclos abiertos al mismo tiempo. *"¿Es cliente?"* se resuelve con una vista, no con una columna.

### Por qué

Un hotel se prospecta más de una vez. Rechazado en un año, retomado dos después con otra necesidad, otro contacto y otro [[Business Developer|BD]]. Con el estado en `hotel`, el segundo intento **sobrescribe la historia del primero**: no queda cuánto tardó, quién lo trabajó ni por qué se cayó.

El dueño comercial es del ciclo, no del edificio. Así la tasa de conversión de cada BD queda limpia cuando un prospecto se reasigna (RF-V-24), y `closed_at − opened_at` es el tiempo de conversión del embudo (RF-V-26) sin cálculo aparte.

### La restricción que lo sostiene

```sql
CREATE UNIQUE INDEX ux_prospect_hotel_open
  ON commercial.prospect (hotel_id) WHERE closed_at IS NULL;
```

Un índice único **parcial**: permite el histórico de ciclos cerrados y garantiza uno solo abierto. Sin él, dos BD trabajan el mismo hotel sin saberlo.

### Por qué no tablas separadas de prospecto y cliente

| Razón | Detalle |
|---|---|
| Negro es un **cliente** y Rojo un **prospecto** | Reactivar un Negro a Azul claro obligaría a mudar la fila de la tabla de clientes a la de prospectos, y a devolverla si vuelve a cerrar |
| Las FK del resto del sistema apuntan a `hotel` | `requisicion`, `contrato`, `factura` y `usuario`. Si los clientes vivieran en otra tabla, o se rompen al reactivar o esa tabla no aporta nada |
| El invariante deja de ser declarable | "Un hotel en Naranja tiene fila de cliente" no se puede expresar con una FK: exige triggers, que no se ven en el código de la aplicación |

`commercial.vw_client` filtra por los estados Naranja y Negro; `vw_prospect`, por los otros siete. Es un join, así que no hay nada que se pueda desincronizar.

> [!warning]
> Naranja y Negro son estados de un hotel **que ya es cliente**, no de una negociación. Con los 9 estados en `prospect`, el ciclo queda abierto durante Naranja y Negro y solo cierra al archivarse. Funciona, pero la versión conceptualmente limpia —que Naranja y Negro pasen a ser estados del hotel y el ciclo se quede con los siete comerciales— **parte el Semáforo Onboarding en dos dimensiones**, o sea modifica la fuente de verdad. Requiere autorización explícita y pasar por el subagente `semaforo-guardian`.

---

## Correcciones aplicadas a la propuesta original

La propuesta externa que se evaluó tenía la infraestructura bien encaminada y el dominio incompleto.

| Corrección | Detalle |
|---|---|
| **+4 módulos** | Liquidación, Supervisión, Journal y Notificaciones. Los originales eran 6; el negocio necesita 10 |
| **−Redis Lock** | Sustituido por locks de fila de Postgres (D-02) |
| **Pub/Sub al borde** | Sacado del camino de la asignación (D-03) |
| **−API Gateway** | Redundante con un solo backend (D-04) |
| **+Modelo de datos** | La propuesta no incluía ninguno. Ver [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]] |
| **+Seguridad** | Cifrado de campo, retención documental y auditoría de acceso a PII |

---

## D-14 · Cada semáforo guarda su historia, no solo su estado actual

**Decisión:** toda entidad con semáforo lleva **dos cosas**: una columna con el estado actual y una **tabla de historia** con una fila por cada cambio. La columna es un atajo de rendimiento; la tabla es la verdad. Aplica a los **7 semáforos**, no solo al de Onboarding.

```sql
commercial.prospect_state_history
  id · prospect_id · from_state_id · to_state_id
  reason_id · user_id · occurred_at

ck_state_history_no_self
  CHECK (from_state_id IS DISTINCT FROM to_state_id)
ix_state_history_prospect (prospect_id, occurred_at DESC)
```

El servicio de transición escribe las dos en la misma transacción: `UPDATE` del estado actual + `INSERT` en la historia.

### Por qué

Una columna de estado contesta *"¿en qué color está?"* y nada más. El negocio pregunta otras tres cosas que esa columna no puede contestar:

| Pregunta | Quién la hace |
|---|---|
| ¿Cuánto tiempo estuvo en cada estado? | Los KPIs de todos los departamentos: tiempo de conversión, tiempo de cobertura, tiempo en Blanco |
| ¿Por qué pasó a este estado? | `requires_reason` aplica a **cualquier** transición, no solo al cierre. Sin historia, el motivo de un rechazo no tiene dónde guardarse |
| ¿Quién lo movió y cuándo? | Trazabilidad de RR-16 a nivel de negocio, no de auditoría |

### Por qué el journal no sustituye esta tabla

Los eventos de cambio de estado sí se asientan en `journal.journal`, y aun así hace falta la tabla. No es duplicación: son dos cosas con forma distinta.

| `journal.journal` | La tabla de historia |
|---|---|
| Append-only, particionado por mes, retención de 24 meses (Estándares de BD sección 9) | Vive tanto como su entidad |
| `payload` en `jsonb` — no consultable por diseño | Columnas tipadas con FK a los estados |
| Paginación por cursor; es auditoría del sistema | Se consulta con `GROUP BY` y ventanas; es dato de negocio |

Calcular "tiempo promedio en Amarillo" recorriendo `jsonb` de una tabla particionada con retención es la clase de consulta que funciona en la demo y se cae en producción.

### Consecuencia

`prospect.onboarding_state_id` queda registrado como **desnormalización deliberada**: es la última fila de la historia, y existe porque el pipeline se consulta en cada carga de pantalla y no va a resolver el estado actual con una subconsulta ordenada por fecha en cada fila.

Los otros 6 semáforos necesitan su tabla equivalente. Hoy solo existe la de `prospect`; ver [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]] sección 4.

---

## D-15 · La primera migración es `catalogs` + `identity` + `commercial`, y se empieza por Ventas

**Decisión:** el primer módulo que se construye es **Ventas** (`commercial`). Pero la primera migración **no es de Ventas sola**: los tres esquemas van juntos, porque las FK cruzan.

### Por qué Ventas primero

| Razón | Detalle |
|---|---|
| Es casi un solo módulo | Ventas vive en `commercial` y solo **lee** de `catalogs` e `identity`. Ningún otro departamento tiene una frontera tan limpia |
| No toca dinero | No hay nómina, factura ni cálculo de horas. Un error se corrige sin devolver un pago |
| No tiene concurrencia | Nada equivalente al Self-Pick (D-02). Sin locks de fila, sin `SKIP LOCKED` |
| Es la puerta del resto | Sin un hotel en Naranja no hay requisición, y sin requisición no hay nada. Construirlo primero es el orden natural del negocio |
| Ejercita el patrón que se repite 7 veces | El servicio de transición del semáforo. Sale bien aquí y los otros 6 se copian |

### Por qué los tres esquemas juntos

`commercial.prospect` tiene FK a `identity.user` y a `catalogs.status_light_state`. Migrar Ventas sola falla en la primera FK.

**Orden dentro de la migración:** `catalogs` → `identity` → `commercial`.

### Lo que tiene que existir antes de correr `prisma migrate` la primera vez

| # | Qué | Por qué no es opcional |
|---|---|---|
| 1 | Monorepo, NestJS, Prisma y la instancia de Cloud SQL | No hay dónde migrar |
| 2 | El **seed** del semáforo: 9 estados y 12 transiciones con su rol y su `requires_reason` | Las tablas vacías no tienen reglas. El seed está en `Ventas/Semáforo Onboarding y Base de Datos.drawio`, página 3 |
| 3 | `journal.journal` **particionado por mes** | Estándares de BD sección 9: retrofitearlo exige reescribir la tabla con la aplicación detenida |
| 4 | Extensiones `postgis`, `pgcrypto`, `btree_gist` en `public` | La geocerca, los uuid y las restricciones de exclusión dependen de ellas |
| 5 | Resolver el módulo del expediente del Colaborador | D-01 no le asigna uno. Provisional en `coverage/`; la alternativa es un módulo `personal/` y serían 11. Ver [[Estructura de Proyecto y Nomenclatura]] sección 3 |

### El modelo vigente de Ventas

**7 tablas** en `commercial`: `hotel`, `hotel_contact`, `prospect`, `prospect_state_history`, `contact_attempt`, `proposal`, `user_zone`. Ventas no crea ningún catálogo.

La fuente es `Español/Arquitecturas/Ventas/Ventas - Modelo de Datos v2.drawio` — página 1 el ER, página 2 la normalización con las 5 desnormalizaciones deliberadas, página 3 las 9 restricciones en SQL. Los otros diagramas de esa carpeta son borradores anteriores.

> [!warning] Lo que el modelo de Ventas todavía no puede afirmar
> **ROSA no tiene fila que lo respalde.** El estado sí se trackea —`prospect_state_history` guarda cuándo entró, quién lo movió y por qué— pero los términos negociados no tienen dónde guardarse. `terms_and_conditions` se modela cuando se diseñe el Contrato, porque sus campos son los de `commercial.contract` + `contract_rate`: es su borrador, y modelar el hijo antes del padre es adivinar.

---

## D-16 · La librería de componentes se copia al repositorio: shadcn/ui en el web, react-native-reusables en el móvil

**Decisión:** ninguna librería de componentes entra como dependencia de runtime. Se **copia el código**: **shadcn/ui** (sobre Radix) en `apps/web` y **react-native-reusables** (el mismo enfoque portado a NativeWind, sobre rn-primitives) en `apps/mobile`. Los tokens Oranje siguen siendo la única fuente de tema (D-12): las variables que shadcn espera se **derivan** de ellos, no las sustituyen.

### Por qué

**Es la única familia que cumple D-12 sin pelearse con ella.** D-12 decidió que un componente que existe en los dos lados se escribe dos veces con la misma API. Con librerías que se copian eso deja de ser un sacrificio y pasa a ser el modo natural: `<Button variant="primary">` tiene la misma firma en web y móvil, y cada plataforma su implementación.

**El tema no se duplica.** shadcn se tematiza con variables CSS, así que el mapeo es una línea (`--primary: var(--o-500)`). Una librería con tema propio obliga a mantener dos definiciones del naranja Oranje sincronizadas a mano.

**Los semáforos exigen variantes que ninguna librería trae.** Los 12 estados del [[Semáforo del Colaborador]] con sus `--st-*` no caben en un `variant="primary"` ajeno. Sobre código propio, agregar la variante es editar un `cva`.

**La accesibilidad se hereda.** Radix y rn-primitives resuelven foco, teclado y `aria` en Dialog, Select y Dropdown — que es justo lo que sale mal cuando esos componentes se escriben a mano.

**El código vive en el repo**, así que pasa por review y por el Storybook que [[Estándares de Desarrollo]] sección 8 exige para `packages/ui`. Un wrapper de un tercero no se puede documentar así.

### Dónde vive cada cosa

| Qué | Dónde | Quién lo consume |
|---|---|---|
| Primitivas de shadcn/ui | `packages/ui/src/components/ui/` | `apps/web` |
| Composiciones Oranje (`SemaforoBadge`, `KpiCard`, `FlowStepper`) | `packages/ui/src/components/` | `apps/web` |
| Componentes atados a estado, ruta o permiso | `apps/web/src/shared/components/` | solo esa app |
| Primitivas de react-native-reusables y sus composiciones | `apps/mobile/src/shared/ui/` | solo `apps/mobile` |

> [!important]
> **Esto corrige una duplicación que ya existía.** [[Estructura de Proyecto y Nomenclatura]] listaba `Button, Modal, Table, StatusChip` en dos lugares a la vez —sección 4 en `apps/web/src/shared/components/` y sección 6 en `packages/ui/src/components/`— sin decir cuál manda. La CLI de shadcn escribe en **una** ruta configurada en `components.json`, así que la ambigüedad tenía que cerrarse: **lo reutilizable vive en `packages/ui`**, y `shared/components/` de la app queda para lo que depende de su store o su router.

### Qué hay que ajustar al copiar

| Lo que trae la librería | Lo que manda el vault | Qué se hace |
|---|---|---|
| Variables `--background`, `--primary`, `--ring`, `--radius` | Tokens Oranje de [[Convenciones de Diseño]] | Se declaran **derivadas** en `tokens.css`; ningún token Oranje se renombra ni se redefine |
| Iconos de `lucide-react` | **Material Icons** (Round y Outlined) | Se cambia el import al copiar. El código es nuestro, es una edición mecánica |
| Tipografía por defecto | **Montserrat** | Ya viene del preset compartido |
| Archivos en `kebab-case` (`button.tsx`) | Sección 7.1: componentes React en `PascalCase.tsx` | **Excepción acotada a `components/ui/`**: se conservan los nombres de shadcn para que `shadcn diff` siga sirviendo al actualizar. Fuera de esa carpeta rige sección 7.1 |
| Nada equivalente a `--st-*` | Un color = un significado por semáforo | Los colores de semáforo **no** se mapean a variables de shadcn: son su propia escala, consumida por `SemaforoBadge` |

### Lo que ninguna de las dos cubre

Del catálogo de [[Convenciones de Diseño]] quedan sin equivalente el **KPI rico**, los **accesos rápidos**, el **stepper de flujo** y el **chip de semáforo**: se escriben como composiciones sobre las primitivas. Y las tablas de datos —requisiciones, timesheet, consolidado— se componen con **TanStack Table**, porque shadcn aporta el markup, no el ordenamiento, la paginación ni el filtrado.

### Qué se descarta

| Alternativa | Por qué no |
|---|---|
| **DaisyUI** | Trae su propio sistema de tema (`--p`, `--b1`) que habría que mantener sincronizado con los tokens Oranje, y **no existe en React Native**: NativeWind no interpreta sus clases semánticas, así que el móvil necesitaría otra librería y se pierde el "mismas clases en los dos lados" de D-12 |
| Flowbite React / Preline UI | El mismo tema propio, y el catálogo apunta más a sitios de marketing que a pantallas de operación con tablas y formularios |
| gluestack-ui v2 | Sí es universal sobre NativeWind, pero sus componentes son compartidos web/móvil: contradice D-12 de frente |
| Radix, Base UI o React Aria a secas | Cero estilos. Son la base de shadcn, no un sustituto: usarlas solas es escribir treinta componentes antes de la primera pantalla |
| Tailwind Plus (Catalyst) | Pago, solo React y sin camino a React Native |

> [!warning]
> **El riesgo que se asume está en el móvil.** react-native-reusables es un proyecto **comunitario** y su catálogo va detrás del de shadcn. Antes de la primera pantalla de `apps/mobile` hay que verificar que existan los componentes que el ponche necesita; lo que falte se escribe a mano sobre rn-primitives. Si faltara demasiado, la salida es **gluestack-ui v2** — y eso reabre D-12, no es un cambio de dependencia.

> [!note]
> **El Storybook obligatorio no alcanza al móvil.** [[Estándares de Desarrollo]] sección 8 lo exige para `packages/ui`, y los componentes del móvil viven en `apps/mobile/src/shared/ui/`. Quedan fuera a propósito: montar Storybook de React Native es una decisión aparte que hoy no se justifica.

---

## D-17 · Las tres librerías que el frontend necesitaba y ningún documento nombraba

**Decisión:** **React Router 8** en *data mode*, **Recharts** para gráficas y **`@vis.gl/react-google-maps`** para el mapa. Ninguna estaba elegida: [[Estructura de Proyecto y Nomenclatura]] sección 4 dibujaba `app/router.tsx` sin decir con qué, y el maquetado de Ventas pedía un embudo y un mapa sin librería asignada.

### Router — React Router 8, paquete `react-router`, *data mode*

Dos precisiones que no son cosméticas:

- **El paquete es `react-router`, no `react-router-dom`.** Desde la v7 —la que fusionó Remix— está unificado y `-dom` quedó como alias. Instalar el viejo es arrastrar un nombre muerto.
- **Va en *data mode* (`createBrowserRouter`), no en *framework mode*.** El framework mode trae SSR y rutas por archivo; **D-04 dice que `apps/web` es un artefacto estático detrás del Load Balancer**, sin servidor que lo renderice. Elegir framework mode contradice D-04 de frente.

**TanStack Router descartado:** su ventaja es el type-safety de rutas, y se paga con un plugin de build y un árbol de rutas generado. El pipeline ya carga suficiente.

### Gráficas — Recharts

El dashboard de Ventas necesita un **embudo de conversión**, y ahí se decide:

| Alternativa | Embudo | Por qué no gana |
|---|---|---|
| **Recharts** | `<FunnelChart>` nativo | — |
| Chart.js + `react-chartjs-2` | **No tiene**; requiere `chartjs-chart-funnel`, plugin comunitario | Anima muy bien, pero obliga a un plugin de terceros justo para la gráfica que sí se necesita |
| Apache ECharts | `series.type: 'funnel'` | Las mejores transiciones, y ~3× el peso. Un dashboard interno no lo justifica |

Recharts además es **componentes de React**, así que no hay que envolver una API imperativa para que conviva con el render declarativo del resto.

**Sí anima**, que era el requisito: animación de entrada y de cambio de datos, configurable por serie.

> [!warning]
> **La paleta de series sigue abierta.** Recharts no trae colores propios que respeten el sistema, y los del Portfolio (#945BFF, #00BEA5, #E12BFF) son del template de referente, no de Oranje — ver pendiente 5 de [[Convenciones de Diseño]]. Hasta que exista esa escala, una gráfica de más de dos series no tiene con qué pintarse sin inventar color.

### Mapa — `@vis.gl/react-google-maps` sobre Google Maps

Es la librería **oficial de Google para React** (sustituyó a `@react-google-maps/api`). Google Maps era la elección del usuario; lo que hay que asentar son sus tres consecuencias:

1. **La API key es pública.** Va dentro del bundle estático, no hay dónde esconderla. Se protege con **restricción por referrer HTTP** en la consola de GCP y limitando la key a Maps JavaScript API. Es la **excepción explícita a D-07**: no todo secreto del frontend cabe en Secret Manager, porque este no es un secreto — es un identificador con control de origen.
2. **Cloud Armor y la CSP tienen que dejar pasar `maps.googleapis.com`.** Hoy la web no llama a ningún host externo; este es el primero.
3. **Maps JavaScript API cobra por carga de mapa.** *Mi Territorio* del BD abre el mapa en cada sesión. El costo escala con usuarios activos, no con datos.

### Qué queda fuera a propósito

La **tipografía** no es una librería: Montserrat se sirve como fuente variable desde el bundle (`@fontsource-variable/montserrat`), por la misma razón que el mapa se documenta — evitar peticiones a hosts externos que después hay que abrir en la CSP.

---

## D-18 · El catálogo de roles: 19 filas, IDs unificados, y dos roles transversales

**Decisión:** `identity.role` se siembra con **19 roles**. Los IDs de Reclutamiento pasan de `ROL-01…ROL-05` a `ROL-R-NN`, el Sistema y el Administrador dejan de existir por triplicado, y los 8 roles de los departamentos sin `Arquitecturas/` reciben ID.

### El problema que resolvía

`Roles del Sistema.md` estaba **vacío** — el catálogo que debía consolidar los roles no existía. Y los IDs estaban repartidos en las Portadas de cada departamento con tres formatos distintos:

| Departamento | Cómo estaba |
|---|---|
| Hotel, Ventas | `ROL-H-01`, `ROL-V-01` — con letra de departamento |
| Reclutamiento | `ROL-01`…`ROL-05` — **sin letra** |
| Inspección, QA, Customer Service, Contabilidad | los roles existen como notas, **sin ningún ID** |

Sin catálogo de roles no se puede sembrar `identity.role`, y sin esa tabla `status_light_transition.authorized_role_id` no apunta a nada: **el semáforo no camina.** Era el bloqueo de toda la Fase 3.

### La convención del número

**El rol operativo de base es 01, y de ahí hacia arriba.** No es una invención: es lo que ya hacían Hotel y Ventas. El `ROL-H-01` es el Supervisor —el de campo— y el `ROL-H-03` el Manager General; el `ROL-V-01` es el BD y el `ROL-V-02` su BDC.

Por eso el Inspector es `ROL-I-01` y el Coordinador `ROL-I-02`; el Operador de QA es `ROL-Q-01` y su Manager `ROL-Q-02`.

### El Sistema y el Administrador son transversales, no de un departamento

Estaban tres veces: `ROL-H-04`/`ROL-V-03`/`ROL-04` para el Sistema, y `ROL-H-05`/`ROL-V-04`/`ROL-05` para el Administrador.

Quedan como **`ROL-SYS-01`** y **`ROL-ADM-01`**, uno de cada uno. Con tres IDs habría que **repetir sus permisos tres veces** en `role_permission`, y un usuario Administrador tendría que declarar "de qué departamento" es — cuando su alcance es todo el sistema.

### Los 19

| `code` | `name` | `department` |
|---|---|---|
| `ROL-H-01` · `ROL-H-02` · `ROL-H-03` | Supervisor · Manager de Área · Manager General | Hotel |
| `ROL-R-01` · `ROL-R-02` · `ROL-R-03` | Reclutadora · Líder de Grupo · Manager de Reclutamiento | Reclutamiento |
| `ROL-V-01` · `ROL-V-02` | Business Developer · Business Developer Coordinator | Ventas |
| `ROL-I-01` · `ROL-I-02` | Inspector · Coordinador | Inspección |
| `ROL-Q-01` · `ROL-Q-02` | Operador de QA · Manager de QA | QA |
| `ROL-CS-01` · `ROL-CS-02` | Agente de CS · Customer Service Manager | Customer Service |
| `ROL-CO-01` · `ROL-CO-02` | Contadora · Manager de Contabilidad | Contabilidad |
| `ROL-C-01` | Colaborador | — |
| `ROL-SYS-01` · `ROL-ADM-01` | Sistema · Administrador | — |

### Consecuencias

**Deuda de propagación:** los `ROL-0N` de Reclutamiento aparecen **48 veces en ~34 archivos** del vault, y siguen con el formato viejo. La base ya usa `ROL-R-NN`; el vault todavía no. Hasta que se propague, quien lea la Matriz de Permisos de Reclutamiento va a ver un ID que no existe en `identity.role`.

**`Roles del Sistema.md` sigue vacío.** El catálogo real vive hoy en `apps/api/prisma/seed.ts`, que es donde la sección 5 de [[Estándares de Desarrollo]] manda los datos semilla. Pero el vault debería tener su propia versión legible.

**Los 8 roles nuevos no tienen `Arquitecturas/`.** Tienen ID y fila en la base, pero ningún `01 - Portada` que los describa. El ID existe antes que su documentación — al revés de como se hizo con Hotel, Ventas y Reclutamiento.

---

## D-19 · La Requisición carga tres semáforos, y por eso la Posición lleva dos pares de columnas

**Decisión:** `demand.position` guarda **dos pares** de columnas de estado —`coverage_state_id` + `coverage_light_id` y `urgency_state_id` + `urgency_light_id`— porque la Posición pertenece a dos semáforos distintos a la vez.

### Por qué tres semáforos sobre dos filas

Una requisición no tiene un estado, tiene tres, y cada uno mide **una dimensión distinta**:

| Semáforo | Dónde vive | Qué mide |
|---|---|---|
| [[Core/Módulos/Semáforos/Semáforo de Requisición\|Requisición]] | `requisition` | El ciclo de vida de la cabecera |
| [[Core/Módulos/Semáforos/Semáforo de Posiciones de la Requisición\|Posiciones]] | `position` | El % de cobertura de esa posición |
| [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición\|Urgencia]] | `position` | El tiempo restante |

Es la regla de oro aplicada: **un semáforo, una dimensión.** Colapsarlos en una columna obligaría a inventar estados compuestos —"cubierta parcialmente y urgente"— y a multiplicar los colores.

### Lo que compran los dos pares

Cada par sostiene su propia **FK compuesta** contra `catalogs.status_light_state (id, status_light_id)`. Sin la columna redundante del semáforo, nada impediría que la urgencia tomara "Dorado" —un color del Semáforo de Posiciones— y **ninguna consulta se quejaría**.

Verificado contra la instancia: el intento se rechaza en el motor.

Son dos desnormalizaciones deliberadas más, en la misma línea que `prospect.status_light_id` de **D-13**: redundancia a cambio de una garantía del motor.

### Y las dos que son derivadas y aun así se guardan

`coverage_state_id` sale de contar slots ocupados. `urgency_state_id` sale de la fórmula de RR-H-05 sobre `authorized_at` contra `start_date`, y la recalcula un job de Cloud Scheduler.

Las dos se guardan porque el tablero las **filtra** en cada carga; recalcularlas en cada consulta obligaría a repetir la fórmula en cada lugar que las lea.

### Tres desviaciones de nomenclatura, a propósito

`Modelo de Datos` sección 3 nombra columnas que este modelo **no usó**, y conviene saber por qué antes de "corregirlas":

| Sección 3 dice | Se usó | Por qué |
|---|---|---|
| `index` en `slot` | **`ordinal`** | "Índice" en la sección 2 es un objeto de base de datos con su convención `ix_`. Y en el ORM se leería como el índice de un arreglo |
| `index` en `position` | **`line_number`** | La posición es un renglón de la requisición, como una partida de factura |
| `schedule` | **`start_time`** | `schedule` colisiona con el módulo Schedule (`operations.schedule`), que es el calendario semanal — otra cosa |

La autoridad de nomenclatura es la sección 2 de [[Estándares de Base de Datos]], no la sección 3 de `Modelo de Datos`, que describe entidades.

### El borrado, que el semáforo obliga

`requisition` y `position` llevan `deleted_at`. El argumento no es el estándar: es que **el Semáforo de Requisición tiene un estado Morado llamado "Eliminada"**, y una fila borrada físicamente no puede tener estado. Si Morado existe, el borrado es lógico por definición.

Por eso `ux_position_requisition_line` es **parcial**: sin el `WHERE deleted_at IS NULL`, eliminar el renglón 2 impediría volver a usar ese número.

`slot` **no** lleva `deleted_at`: si `quantity` baja, el slot libre se borra físicamente —nunca tuvo a nadie— y el ocupado lo impide la FK de `coverage.assignment` con `ON DELETE RESTRICT`. La regla la hace cumplir el motor.

> [!warning] Contradicción del vault, pendiente del semaforo-guardian
> El [[Core/Módulos/Semáforos/Semáforo de Requisición|Semáforo de Requisición]] describe Morado como *"Requisición eliminada **físicamente**"*. Debería decir "eliminada" a secas: como está, contradice a la sección 4 de [[Estándares de Base de Datos]] y a sí mismo. Corregirlo toca la fuente de verdad, así que va por el subagente `semaforo-guardian`.

### Enmienda del 2026-08-12 · el semáforo se fija por `code`, no por uuid

**La FK compuesta no fijaba el semáforo.** El patrón que esta decisión estableció —`(state_id, status_light_id) → status_light_state(id, status_light_id)`— garantiza que el estado y el semáforo declarado coincidan **entre sí**, pero no que sean los del semáforo correcto. Un `worker` que declarara `status_light_id = ONBOARDING` podía tomar NARANJA del Onboarding, porque **ese par existe**. Se descubrió probándolo: un insert que pasó cuando debía fallar.

Faltaba un CHECK contra el semáforo, y ahí está lo no obvio: **no puede ser contra el uuid.** El `id` es uuid v7 generado por el seed, así que cambia en cada ambiente — un CHECK con el literal sería válido en dev y falso en staging. El **`code` sí es estable**: `'WORKER'` es `'WORKER'` en todas partes, y ya era `UNIQUE` en `status_light`.

`catalogs.status_light_state` gana `status_light_code` (desnormalizado, FK con `ON UPDATE CASCADE`) y un único `(id, status_light_code)`. Las **seis referencias** pasan de guardar el uuid a guardar el code, cada una con FK compuesta y CHECK:

| Tabla | Fijada a |
|---|---|
| `personal.worker` · `personal.worker_state_history` | `WORKER` |
| `commercial.prospect` · `commercial.prospect_state_history` | `ONBOARDING` |
| `demand.requisition` · `demand.requisition_state_history` | `REQUISITION` |
| `demand.position` | `POSITION_COVERAGE` **y** `URGENCY` |

**Las dos tablas de historia estaban peor:** sus FK eran **simples** contra `status_light_state(id)`, así que ni siquiera impedían pares mal formados — podían registrar una transición entre estados de dos semáforos distintos.

`catalogs.status_light_transition` **no** se fija, y es correcto: cada fila pertenece a su propio semáforo. Lo que necesitaba —que el origen y el destino sean del mismo— ya lo da la columna compartida.

Los 8 CHECK quedaron **validados por Postgres** contra los datos existentes, con 0 estados de `code` huérfano y 0 donde el uuid y el code no coincidan.

### Enmienda del 2026-08-12 · dos límites de Prisma que cambiaron el diseño

**1. Las columnas `GENERATED` no se pueden usar.** `personal.worker` iba a llevar `has_tax_id` (la retención del 16%) e `is_profile_complete` como `GENERATED ALWAYS AS ... STORED`. En Postgres funcionaban perfecto; el problema es que **Prisma no sabe representarlas** y su diff queda en un bucle imposible:

| En el datamodel | Prisma propone | Resultado |
|---|---|---|
| Sin declararlas | `DROP COLUMN` | pérdida de datos |
| Declaradas | `DROP DEFAULT` | Postgres: *"column is a generated column"* |
| Con `@default` | `SET DEFAULT` | el mismo error |

Los tres rompen la siguiente migración. **Se resolvió con una vista:** `personal.vw_worker` expone las dos derivadas más la **edad calculada** (que es la razón por la que no se guarda `edad`, ver la desviación de `birth_date`). La vista consigue lo que buscábamos —la derivación vive en la base y no se puede desincronizar— y Prisma la ignora.

**2. Los índices parciales no se pueden declarar.** `ux_slot_active_assignment` tiene que ser parcial (`WHERE status = 'ACTIVE'`) porque un único simple sobre `slot_id` prohibiría reusar el slot al cerrar la asignación. Prisma no lo sabe expresar, así que el índice **no está en el datamodel**. Se verificó que el diff no lo propone para `DROP`, pero lo que de verdad lo protege es el test de la Fase 4 que intenta dos asignaciones activas sobre el mismo slot y espera el error del motor: si alguien borra el índice, ese test truena. Es la misma trampa que se comió el índice espacial de la geocerca el 2026-08-08.

> [!note] Dos cosas que la migración enseñó
> **`prisma migrate deploy` NO envuelve el archivo en una transacción.** El primer intento del cambio a `code` se aplicó a medias y hubo que reescribir la migración entera como idempotente (`IF EXISTS` / `IF NOT EXISTS`, y cada constraint borrada antes de crearse).
>
> **Una vista definida con `SELECT *` ata la tabla a su lista de columnas.** `personal.vw_worker` bloqueaba el `DROP COLUMN`: *"cannot drop column status_light_id because view personal.vw_worker depends on it"*. La vista se cae al inicio de la migración y se recrea al final.

---

## D-20 · El turno planeado es una tabla, y es lo único que puede hacer cumplir RR-05

**Decisión:** `operations.schedule_entry` guarda el turno **planeado** de cada día como un `tstzrange`, y ahí vive la restricción de exclusión de RR-05:

```sql
EXCLUDE USING gist (worker_id WITH =, shift_range WITH &&)
```

### Por qué no cabía en `coverage.assignment`

`assignment.validity` es un **`daterange`**: solo ve días. Y la regla —precisada el 2026-08-12— es que el colaborador **sí puede** estar en dos hoteles el mismo día mientras los turnos no se solapen. Hotel A de 6am a 2pm y Hotel B de 3pm a 11pm se ven como el mismo día, así que una exclusión sobre `validity` habría rechazado el caso legal.

`schedule_entry` es la **primera tabla del sistema con horas reales**. Por eso la regla bajó hasta aquí y no antes.

### Por qué sobre lo planeado y no sobre los ponches

Porque el choque hay que **impedirlo al asignar**, no descubrirlo cuando el colaborador ya se presentó a los dos hoteles.

### Se evaluó eliminar la tabla y derivarla

La pregunta fue directa: si el turno sale de `assignment.validity` + `position.start_time` + 8 horas, ¿para qué guardarlo? Para el cálculo de horas y overtime, en efecto **no hace falta** — eso sale de los ponches reales. Pero se rompen dos cosas:

1. **Un `EXCLUDE` necesita FILAS con rangos.** No opera sobre un conjunto calculado al vuelo; sin filas materializadas, Postgres no tiene qué comparar y RR-05 vuelve a ser lógica de aplicación.
2. **Sin turno planeado no se distingue una FALTA de un DESCANSO.** La semana es 5 de trabajo + 2 de descanso y **el vault no dice cuáles 2**. Sin eso, la regla de 3 inasistencias → Negro no se puede disparar.

`worker_id` va **desnormalizado** desde `assignment` por la misma razón: un `EXCLUDE` no puede hacer JOIN, y necesita las dos columnas en la misma fila.

### Verificado contra la instancia

| Turno | Resultado |
|---|---|
| 6am-2pm | entra |
| 10am-6pm | **rechazado** — se solapa |
| 3pm-11pm el mismo día | **entra** — es el caso que la regla permite |
| 10pm-6am | **rechazado** — choca con el de 3pm |
| 11:30pm-7:30am | entra, **cruza medianoche** sin problema |

El turno nocturno sale gratis precisamente porque el rango lleva fecha completa. Con dos columnas de solo-hora, `22:00 → 06:00` sería un rango invertido y no habría forma de compararlo.

---

## D-21 · Cuatro marcas de ponche, y la duración del lunch se mide pero no se paga

**Decisión (2026-08-13, autorizada por el usuario):** el ponche pasa de **tres** marcas a **cuatro**, de las cuales solo dos son obligatorias.

| Marca | Obligatoria | Foto |
|---|---|---|
| `CLOCK_IN` | **sí** | sí |
| `LUNCH_OUT` | no | no |
| `LUNCH_IN` | no | no |
| `CLOCK_OUT` | **sí** | sí |

### El vault se contradecía, y ninguna de las dos versiones quedó

[[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] y [[Timesheet]] decían **tres** marcas; [[Core/Módulos/Schedule|Schedule.md]] seguía diciendo **seis** (pares de lunch y de break). Era el pendiente 2 del [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]], abierto desde antes.

Cada pieza resolvió algo distinto:

- **Vuelve el regreso de lunch**, y con él el **Indicador de Lunch Extendido**, que con tres marcas era literalmente incalculable: sin fin de comida no hay duración que comparar contra los 30 minutos. [[Timesheet]] ya lo admitía.
- **Los breaks se eliminan.** Se evaluó recuperar su par y se descartó: con la deducción fija no descuentan nada, así que serían dos marcas que nadie ponchará. La *Deducción de Breaks* pasa de "regla sin origen de datos" a **regla eliminada**.

### La deducción sigue fija en 30 minutos, aunque ya se conozca la real

Es lo no obvio de esta decisión. El par de lunch alimenta el **indicador** —supervisión interna de Oranje, que el hotel no ve— y **no** el cálculo del pago.

> [!warning] Consecuencia asumida
> Quien se toma **90 minutos** de lunch **cobra 60 de ellos**. El indicador lo marca; la nómina no lo corrige.
>
> Y un sesgo que conviene tener presente: al ser el par opcional, **solo ponchan los cumplidos** —justo los que no se pasan—, así que el indicador ve menos de lo que busca.
>
> Verificado en la instancia: con 50 minutos de lunch reales, `net_minutes` sigue siendo 450 sobre un bruto de 480.

### Un ponche manual tampoco lleva foto

El `CHECK` de la foto exceptúa a `is_manual`. Es justo el caso en que el colaborador **no pudo** ponchar —el GPS falló dentro de la propiedad—, así que exigírsela sería pedirle lo que no tiene. Lo registra el [[Hotel/Supervisor|Supervisor]] y lo aprueba el [[Hotel/Manager de Área|Manager de Área]], mismo reparto que el resto de las horas.

### El doble reloj, y por qué sobrevive aunque el ponche sea web

`device_at` queda **nulable**: el ponche será web por ahora y no hay modo offline que llenarlo. Cuando exista la app móvil empieza a llegar, sin migración.

`server_at` **se queda igual**, pero la razón cambia: no es por el offline, es porque **el reloj del cliente no es de fiar** — cualquiera cambia la hora de su dispositivo, en web también. Es la única hora confiable.

> [!note] Deuda que esto abre
> El vault dice que el ponche es desde la **app móvil** y que **funciona sin señal**. Si el ponche web se vuelve definitivo, eso hay que corregirlo en la fuente de verdad.

---

## D-22 · La propuesta se envía dentro de Verde, y el ciclo comercial se cierra a mano

**Decisión (2026-08-17):** dos huecos que llevaban meses abiertos en `commercial` se cierran juntos, porque son el mismo problema — el semáforo avanzaba y se archivaba sin que nada lo respaldara.

| | Endpoint | Permiso |
|---|---|---|
| Propuesta | `POST · PATCH · GET /prospects/:id/proposals` | `proposals:create` · `proposals:read` |
| | `POST /prospects/:id/proposals/:proposalId/send` | `proposals:send` |
| Archivo | `POST /prospects/:id/close` | `pipeline:close_cycle` |

### El vault se contradecía sobre dónde vive la propuesta

`commercial.proposal` existía desde el 2026-08-06 sin un solo endpoint que la escribiera. Al construirlos apareció el conflicto:

| Documento | Qué dice |
|---|---|
| [[Core/Módulos/Reglas de Negocio\|Reglas de Negocio]] | "Se elabora **y envía** en Status Onboarding Verde" |
| [[Ventas/Reglas de Ventas\|Reglas de Ventas]] | "Verde: Propuesta Personalizada elaborada y enviada" |
| [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | "**al enviar** la propuesta, pasa a Verde" |

Gana la fuente de verdad. La línea del semáforo se corrigió: se entra a Verde al **empezar a elaborarla**, no al enviarla.

> [!important] La consecuencia técnica no es cosmética
> La exigencia va al **salir** de Verde, no al entrar. De Verde no se avanza a Amarillo, Rojo ni Café sin una propuesta enviada — los tres suponen que el hotel ya la vio. Es `422 PROPOSAL_REQUIRED`.
>
> El nombre del status describe **cómo se sale de él**, no cómo se entra. Leerlo al revés es lo que produjo la primera implementación, que quedó invertida.

Y la propuesta solo se trabaja donde la regla dice: **Verde o Café** (`422 PROPOSAL_STATE_INVALID`). Café es el puente de desbloqueo, donde la propuesta se ajusta o se retoma.

### Una versión por envío, y el índice único es quien decide

Cada `POST` crea una **versión nueva** en borrador. Solo puede haber **un borrador abierto** por ciclo, y lo enviado **no se edita**: si el hotel pide un cambio, se versiona.

La versión se calcula dentro de la transacción, pero quien de verdad impide el empate es `ux_proposal_prospect_version`: dos BD guardando a la vez leerían el mismo máximo.

Las tarifas viajan por la API como **cadena**, nunca como `number` de JavaScript — es la misma regla de [[Arquitecturas/_Globales/Estándares de Base de Datos|Estándares de Base de Datos]] sección 3, aplicada al transporte y no solo al cálculo.

### Archivar no es marcar Rojo ni Negro

Rojo, Negro y Café **son reactivables** hacia Azul Claro (RR-V-07). Si llegar ahí cerrara el ciclo, no habría nada que reactivar. Por eso cerrar es un acto aparte y deliberado: *este hotel ya no va*.

- Motivo **obligatorio**, del catálogo del semáforo
- Un hotel en **Naranja no se archiva**: es cliente activo (`409 PROSPECT_IS_CLIENT`). Primero pasa a Negro
- Al cerrar, el hotel **queda libre** para abrir un ciclo nuevo — antes un prospecto rechazado lo bloqueaba para siempre
- Se conserva el color con el que se archivó

> [!warning] La Matriz de Permisos no tenía esta acción
> `pipeline:close_cycle` (BD y BDC) **se agregó**. [[Ventas/Reglas de Ventas|Reglas de Ventas]] mencionaba *"decisión de reactivar o archivar"* en Rojo, pero la [[Arquitecturas/Ventas/_Globales del Depto/06 - Matriz de Permisos|Matriz de Ventas]] nunca la listó. Es el tercer hueco de la Matriz detectado al construir contra ella.

### Lo que esto deja sin cerrar

- **Rosa sigue sin fila que lo sustente.** Lo que se negocia ahí es el **Documento de T&C**, creado en Amarillo — otro documento, no la propuesta. Sigue sin modelarse desde el 2026-08-06, y la razón no cambió: sus campos son los de `settlement.contract`, y modelarlo antes de diseñar `settlement` es adivinar la forma del padre desde el hijo.
- **`proposal_rate` sigue aplazada.** Las tarifas son globales y tentativas; la tarifa por posición llega como migración aditiva cuando el negocio la pida.
- **El catálogo de motivos no sirve para archivar.** Los cuatro sembrados —`HOTEL_CLOSED`, `MANAGEMENT_CHANGE`, `OPERATION_PAUSED`, `COMMERCIAL_DISPUTE`— se pensaron para Negro y Café. Falta el motivo más común: *no hay interés* o *eligió otro proveedor*. Es aditivo: una fila de catálogo y una línea de seed.

---

## D-23 · La Requisición y el modelo colaborativo: el semáforo lo mueven los hechos

**Decisión (2026-08-17):** `demand` y `coverage` dejan de ser tablas sin consumidor. Las cuatro tablas de la Requisición llevaban desde el 11 de agosto sin un solo endpoint, y `coverage` desde el 12.

| Endpoint | Quién |
|---|---|
| `POST · GET /requisitions` · `GET /requisitions/:id` | Supervisor, GH, GM |
| `POST /requisitions/:id/authorize` | **Solo GM y GH** |
| `POST · DELETE /requisitions/:id/participants` | Reclutadora, Líder |
| `POST /assignments` · `DELETE /assignments/:id` | Reclutadora, Líder |

### Ningún endpoint mueve el semáforo a mano

No hay un `PATCH /state`. El estado es **consecuencia** de un hecho del negocio:

| Hecho | Efecto |
|---|---|
| Se crea la requisición | VERDE MANZANA · cobertura DORADO |
| El GM o GH autoriza | VERDE · cobertura NARANJA · **se calcula la urgencia** |
| El primer reclutador toma | AMARILLO |
| Se asigna un colaborador | La cobertura del renglón se recalcula |
| Sale el último reclutador | Vuelve a VERDE (RR-15) |

**La urgencia no existe en un borrador.** RR-H-05 la mide desde `authorized_at` contra `start_date`, así que antes de autorizar no hay desde dónde medir. Se calcula al autorizar, y ahí nacen los tres niveles: más de 120 h `STRONG_GREEN`, entre 72 y 120 `YELLOW`, menos de 72 `RED`.

Los slots se generan solos, uno por unidad de `quantity` y numerados desde 1 — es el orden que el Self-Pick necesita para ocuparlos de forma determinista.

> [!warning] El cierre en AZUL CLARO se implementó saltándose al Líder de Grupo
> Hoy la requisición cierra **sola** al ocuparse el último slot. **RF-05 dice otra cosa:** la Reclutadora *marca* la requisición como cubierta, el sistema la deja en **"Pendiente de aprobación"**, y solo tras la aprobación del [[Reclutamiento/Líder de Grupo de Reclutadoras|Líder de Grupo]] pasa a Azul claro. Si el Líder rechaza, vuelve a Amarillo con motivo obligatorio.
>
> El estado *Pendiente de aprobación* **no existe** en el [[Core/Módulos/Semáforos/Semáforo de Requisición|Semáforo de Requisición]], que tiene seis. Es el mismo hueco que el pendiente 6 —el proponer→aprobar del Indicador de Calidad—, y ahora son **dos** los casos que lo piden. Ver el pendiente 20.

### Azul claro y Rojo son terminales

Las transiciones sembradas no tienen salida desde ninguno de los dos. Por eso liberar una asignación de una requisición cerrada responde `409` en vez de dejarla afirmando estar cubierta al 100% con un lugar vacío.

### Tres cosas rotas que aparecieron al construir esto

Ninguna se buscaba; las tres bloqueaban o falseaban el trabajo:

1. **`app_user` no tenía `USAGE` sobre el esquema `personal`.** Nació el 2026-08-12 como el esquema 11 y quedó fuera de los `GRANT` que sí tienen los otros diez, así que la aplicación no podía leer un solo colaborador y toda asignación moría con `42501`. Lo delata que la misma migración ya hacía `GRANT SELECT ON personal.vw_worker TO app_user` — permiso **inerte** sin `USAGE` sobre el esquema que lo contiene.

2. **`coverage.assignment.status` violaba su propio `CHECK`.** Nació con `DEFAULT 'active'` contra un `CHECK IN ('ACTIVE','CLOSED','CANCELLED')`: toda inserción por el camino normal fallaba. Y como `ux_slot_active_assignment` filtra por `status = 'ACTIVE'`, en minúsculas el índice tampoco habría impedido dos asignaciones activas sobre el mismo slot — **la garantía de no-doble-asignación no existía**. Verificado contra la instancia antes de tocarlo.

3. **El `.gitignore` se comía el módulo.** `coverage/` sin anclar captura cualquier carpeta con ese nombre, incluida `apps/api/src/modules/coverage`, que es uno de los diez módulos de D-01. Habría desaparecido sin aviso al commitear.

---

## D-24 · El turno planeado y el ponche: dos reglas que ahora sí se aplican

**Decisión (2026-08-17):** `operations` estrena endpoints. Las cinco tablas —Schedule, turno planeado, Timesheet, día y marca— llevaban desde el 13 de agosto sin nada que insertara una fila, así que **RR-05 y la aprobación de horas estaban escritas pero no vigentes**.

| Endpoint | Quién |
|---|---|
| `POST · GET /schedules` · `POST /schedules/:id/entries` | GM y GH |
| `POST /punches` | El colaborador |
| `POST /punches/manual` | El Supervisor |
| `POST /timesheet-days/:id/review` · `/timesheets/:id/submit` | El Supervisor |
| `POST /timesheets/:id/approve` | **Solo GM y GH** |

### RR-05 la impone el motor, no la aplicación

`no_shift_overlap` es un `EXCLUDE` sobre `(worker_id =, shift_range &&)`. El servicio no consulta si hay choque: intenta insertar y traduce el `23P01` a un `409` con nombre.

La diferencia no es de estilo. Un `SELECT` previo seguido de un `INSERT` deja una ventana entre los dos, y dos peticiones simultáneas se cuelan por ahí. La restricción no tiene ventana.

> [!note] Lo que la regla dice de verdad
> Verificado contra la instancia con el **mismo colaborador en dos hoteles el mismo día**: `07:00–15:00` en el hotel A y `14:00–22:00` en el B **choca**; `16:00–23:00` en el B **pasa**.
>
> El criterio son las **horas**, no el hotel ni el día. Es exactamente lo que D-20 decidió al mover la restricción desde `coverage.assignment` —donde un `daterange` solo ve días— hasta aquí.

El turno nocturno cae solo: si la hora de fin es menor o igual a la de inicio, el rango cruza a la madrugada siguiente. `22:00–06:00` son 480 minutos, sin caso especial.

### La geocerca la calcula Postgres

`ST_DWithin` contra el radio del hotel, siguiendo D-08. Un cliente que decide si está dentro es un cliente al que se le puede mentir.

Estar fuera es `422` **con la salida** —pedir un ponche manual al Supervisor—, no un rechazo mudo. Y si el hotel no tiene coordenadas cargadas, `inside_geofence` queda nulo en vez de bloquear: el dato falta, el colaborador no tiene la culpa.

La foto solo se exige en los extremos. El par de lunch es opcional y no la lleva; el ponche manual tampoco, porque es justo el caso en que el colaborador **no pudo** ponchar.

### La deducción de lunch sigue fija, y el día a medias no avanza

540 minutos brutos son **510 netos**, se haya comido 20 minutos o 90. La duración real se guarda y alimenta el Indicador de Lunch Extendido, pero no descuenta — es la consecuencia asumida en D-21.

Un día con entrada pero **sin salida** queda marcado con anomalía y **bloquea el envío a revisión**. Sin eso, una jornada a medias llegaría a nómina como cero horas sin que nadie lo note, que es justo el tipo de error que D-07 advierte: no lanza excepción, paga mal y se descubre tres meses después.

El reparto de D-09 quedó verificado: el Supervisor revisa, resuelve anomalías y manda a revisión, pero **aprobar le da `403`**. El Manager General aprueba cualquier departamento; el de Área, solo el suyo. Y un Timesheet aprobado ya no admite marcas.

---

## D-25 · El Consolidado Semanal: el cálculo vive en SQL y lleva dos firmas

**Decisión (2026-08-18):** `settlement` estrena endpoints y con eso el recorrido corre completo — prospecto, hotel cliente, requisición, asignación, turno, ponche, horas aprobadas y **pago**.

### El cálculo entero corre en `numeric`, dentro de la base

No es preferencia de estilo. [[Arquitecturas/_Globales/Estándares de Base de Datos|Estándares de Base de Datos]] sección 3 lo dice sin margen: toda aritmética sobre montos se hace en `numeric` y **nunca** en el `number` de JavaScript.

Por eso la generación es **una sola sentencia** —CTEs encadenados que agrupan las horas, resuelven la tarifa y escriben cabecera y detalle— y no un `for` en TypeScript. Y por eso los montos salen de la API como **cadena**: convertirlos a `number` para transportarlos desharía en la orilla lo que se cuidó en el centro.

### El rate se resuelve por prioridad, y queda congelado

1. El **rate interno** de la persona (`personal.worker_rate`), si está vigente.
2. Si no, el del **contrato** del hotel (`commercial.contract_rate`).

`is_internal_rate` congela cuál se aplicó. Importa porque el interno **puede ser mayor** que el contractual y la diferencia la absorbe Oranje: la factura al hotel jamás lo refleja. Son dos ramas que no se mezclan, y por eso el consolidado usa `overtime_pay_multiplier` y nunca el de facturación.

> [!note] Un consolidado por persona y semana
> No por hotel. Si el colaborador trabajó en tres hoteles, son tres renglones de detalle y **un solo** cheque. Es lo que ya decía `ux_consolidation_worker_week`.

### Las dos firmas del Flujo de Nómina

La Contadora **valida**, el Manager de Contabilidad **autoriza**, y cada uno recibe `403` en el paso del otro. La base ya lo exigía con `ck_consolidation_signatures`; ahora también lo exige la aplicación, que es lo que hace que el error salga con nombre en vez de como violación de restricción.

El neto **no se escribe**: se recalcula al aplicar cada deducción y `ck_consolidation_amounts` lo verifica. Tampoco puede quedar negativo — si las deducciones superan lo ganado, el sistema tiene que decidir qué hacer, no emitir una deuda.

> [!warning] Contabilidad no tiene Matriz de Permisos
> El departamento no tiene carpeta en `Arquitecturas/`, así que las seis filas del módulo `payroll` **se derivaron de [[Contabilidad/Flujo de Nómina|Flujo de Nómina]]**, que sí dice quién valida y quién autoriza. Coinciden con las dos firmas que la base ya exigía, pero quedan marcadas en el encabezado de `permissions.ts` para revisarse cuando su matriz exista.
>
> Es la primera vez que se siembran permisos de un departamento sin matriz. Los otros tres sin arquitectura —Inspección, QA y Customer Service— siguen sin una sola fila.

Verificado contra la instancia: una semana de **40 horas netas a 20.00** da bruto **800.00**; con deducciones de 25.00 y 48.00, neto **727.00**.

---

## D-26 · El Colaborador, el Contrato y la factura: el sistema completo de punta a punta

**Decisión (2026-08-18):** las tres piezas que faltaban para que el recorrido cierre. Con ellas, de once tablas sin endpoint quedan cuatro.

### El Colaborador entra por el estado incompleto

`personal.worker` es la entidad central del sistema —así la define [[CLAUDE.md]]— y no tenía un solo endpoint. Había **dos colaboradores en la base, los dos insertados a mano**: todo lo construido encima corría sobre gente que nadie podía dar de alta.

Nace en **BLANCO** y ahí se queda hasta que la Reclutadora lo valide (RF-08). Lo que decide si puede validarse **no es una regla del servicio**: es `is_profile_complete` de `personal.vw_worker`, los nueve campos que esa vista ya declaraba obligatorios desde el 2026-08-12.

> [!note] Por qué las columnas no pueden ser `NOT NULL`
> Los datos llegan en **tres fases** —entrevista, el propio colaborador, emergencia— y entre una y otra la fila existe a medias. **Eso ES el estado Blanco.** Una restricción `NOT NULL` haría imposible representarlo.

`?onlyAvailable=true` reproduce `coverage.vw_pool`: VERDE FUERTE o AMARILLO y sin veto vigente. El matiz está en `lifted_at` — un veto **ya levantado** sí devuelve al colaborador al Pool.

### El Contrato: tres guards que la base dejó por escrito

La migración del 2026-08-13 los enunció como pendientes del servicio, y aquí se implementan:

| Guard | Por qué la base no puede |
|---|---|
| Un contrato `ACTIVE` sin ninguna tarifa | Una FK no obliga a que existan hijos, y no hay restricción que lo exprese |
| Un segundo contrato vigente por hotel | `ux_contract_active` sí lo impide, pero rebotaría como violación de único; el servicio contesta con el número del que estorba |
| Tarifas fuera del borrador | Cambiar un rate bajo un contrato firmado reescribe lo que ya se facturó |

Y los dos márgenes, que la base también verifica pero conviene explicar antes de llegar a ella: el `bill_rate` nunca por debajo del `pay_rate`, y el hotel nunca paga menos recargo que el colaborador. **Los dos errores cuestan dinero en cada hora trabajada y no lanzan excepción** — se descubren en el margen, tarde.

### La factura y el Consolidado son ramas espejo que no se tocan

| | Consolidado | Factura |
|---|---|---|
| Tarifa | `pay_rate` | `bill_rate` |
| Recargo | `overtime_pay_multiplier` | `overtime_bill_multiplier` |
| Rate interno | **Sí lo aplica** | **Nunca aparece** |

Esa última fila es la decisión. El rate interno de la persona puede ser mayor al del contrato, y **esa diferencia la absorbe Oranje, no el hotel**. Mezclar las ramas sería facturarle al cliente un acuerdo del que no es parte.

> [!success] El margen ya se puede medir
> Verificado contra la instancia: 40 horas netas a `bill 35.00` son **1400.00** facturados; las mismas horas a `pay 20.00` son **800.00** pagados. Margen **600.00**.

Si el hotel no tiene contrato vigente con tarifa para esas posiciones, el JOIN no encuentra nada y la factura **no se crea**: `422` en vez de una factura en cero.

### Dos cosas que aparecieron

1. **Inventé nueve valores de enum del Colaborador** en vez de leer los `CHECK`. Era `MALE`/`FEMALE` y no `F`/`M`, `ONE_TO_TWO` y no `SOME`, `O_POS` y no `O+`. Los DTO son ahora copia literal de la restricción. Es el mismo error que ya había costado una migración con `assignment.status`.
2. **`worker_state_history` no tiene columna `note`**, a diferencia de `prospect_state_history`. La nota del cambio de estado va al journal. Dos historias que deberían tener la misma forma y no la tienen.

---

## Decisiones pendientes

1. ~~**RR-05 — exclusividad absoluta o cobertura temporal.**~~ **RESUELTO** (2026-08-12 / 2026-08-13). Ni una ni otra: el criterio son las **horas**, no el hotel ni el tipo de asignación. La restricción vive en `operations.schedule_entry` — ver **D-20**.
2. ~~**Las transiciones de los otros 6 semáforos.**~~ **RESUELTO en su mayor parte** (2026-08-12 / 2026-08-13). **115 transiciones** sembradas en 4 semáforos: Colaborador (89, incluidas las 34 del Blacklist), Onboarding (13), Requisición (9) y Calidad (4). Los otros tres —Posiciones, Urgencia y Cumplimiento del Timesheet— **no necesitan filas**: son **derivados**. Nadie pide pasar a Rojo; se pasa solo cuando la cobertura, las horas restantes o las horas trabajadas cruzan un umbral, y los recalcula un job. `status_light_transition` es para pasos que una **persona** pide y el sistema autoriza.
3. ~~**`journal.journal` ya viene tarde.**~~ **RESUELTO** (2026-08-12). Creado particionado por mes, con 17 particiones hasta 2027-12, mientras estaba vacío y el costo era cero.
4. ~~**El módulo del expediente del Colaborador.**~~ **RESUELTO** (2026-08-12). Esquema `personal/` propio, y la razón es de **permisos**: el expediente lleva SSN e ITIN, y un esquema aparte es un solo `GRANT` en vez de permisos columna por columna. El proyecto pasa a **11 esquemas**.
5. ~~**La identidad del Colaborador.**~~ **RESUELTO** (2026-08-12). El Colaborador **sí** lleva renglón en `identity.user` —un solo camino de login para todos— con `hotel_id` nulo, porque no pertenece a un hotel sino a Oranje. Se descartó la tabla `identity.account`: basta una columna `firebase_uid` única, y una tabla aparte solo haría falta si una persona tuviera varios proveedores. Firebase guarda la contraseña; nosotros no guardamos ningún hash.

### Lo que sigue abierto

6. **El flujo proponer → aprobar del Indicador de Calidad.** El vault dice que el [[Operador de QA]] **propone** el cambio con base en sus mediciones y el [[Manager de QA]] **valida y aprueba**. `status_light_transition` es de **un solo paso**, así que solo quedó sembrado el Manager —la transición se efectúa al aprobar— y **la propuesta del Operador no se registra en ningún lado**. Falta decidir si es un estado más, una tabla `state_change_request`, o si vive solo en la UI. Se resuelve al modelar `supervision`.
7. **La evidencia del ponche manual.** El vault dice quién lo registra y ahora también quién lo aprueba, pero no **con qué evidencia** se justifica. Hoy `manual_reason` es texto libre.
8. **Los días festivos.** [[Core/Módulos/Schedule|Schedule.md]] dice que se registran "como datos de la operación" para alimentar el módulo de **forecast**, que está sin definir. Y el Contrato pacta *"2x bill rate en días festivos"*, pero el [[Contabilidad/Consolidado Semanal del Colaborador|Consolidado]] admite que la regla de cálculo del recargo **aún no está definida**. Modelarlo ahora sería adivinar la forma de un cálculo que nadie escribió.
9. **El ponche web contra el ponche móvil.** Ver la deuda de **D-21**.
10. **La deuda de los `ROL-0N` viejos.** Siguen apareciendo en ~34 archivos del vault, y `Roles del Sistema.md` sigue vacío. Ver **D-18**.
11. **El motivo de archivo del ciclo comercial.** El catálogo del Semáforo Onboarding no tiene *no hay interés* ni *eligió otro proveedor*, que son las razones más comunes de archivo. Ver **D-22**.
12. **El Documento de T&C.** Rosa no tiene fila que lo respalde. Se resuelve al modelar `settlement`. Ver **D-22**.
13. **La duración del turno.** `demand.position` guarda solo `start_time`. El dato del negocio es **8.5 horas brutas** (8 netas tras los 30 min de lunch) y **varía**, pero no está en [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]]. Falta decidir dónde vive —`position`, el Contrato o el Schedule— y asentarlo en la fuente de verdad. Sin ella `operations` no puede calcular la hora de salida ni comparar lo planeado contra lo ponchado.
14. **El GH responsable frente a quien autoriza.** `demand.requisition` lleva `area_manager_user_id` y `authorized_by` como columnas distintas, porque un Manager General puede autorizar la requisición de un departamento cuyo GH es otro. Falta confirmarlo con el negocio; si siempre coinciden, sobra una columna.
15. **La homoclave del número de requisición.** El formato es `AAAAMMDDHHMM` + 2 caracteres. Falta definir quién los genera y contra qué se verifica que no choquen: si es aleatoria, necesita reintento.
16. **Nube definitiva.** GCP es defendible por Cloud Run y Firebase. La decisión debe basarse en qué domina el equipo, no en capacidades de mensajería —equivalentes en ambas nubes.
17. ~~**Número de marcas del ponche.**~~ **RESUELTO** (2026-08-13). Son **cuatro**, dos obligatorias. Ver **D-21**.
18. **Excepción al ponche bloqueado.** Falta la regla del ponche manual cuando el GPS falla dentro de la propiedad.
20. **El estado *Pendiente de aprobación* de la Requisición.** RF-05 lo exige entre AMARILLO y AZUL CLARO: la Reclutadora marca, el Líder aprueba. El [[Core/Módulos/Semáforos/Semáforo de Requisición|Semáforo de Requisición]] no lo tiene, y hoy el API cierra solo. Es el **segundo** caso del mismo patrón proponer→aprobar (ver pendiente 6), así que la salida debería servir a los dos: un estado más, una tabla `state_change_request`, o una columna en la entidad. Ver **D-23**.
21. **Quién cierra una requisición en ROJO, y cuándo.** El vault dice **qué significa** —cerró con al menos una posición en Amarillo o Rojo— pero no quién lo dispara ni en qué momento. RF-05 cubre solo el camino del 100%. Sin esa regla, una requisición a medias se queda en AMARILLO para siempre.
22. **El overtime nunca se calcula.** `timesheet_day.overtime_minutes` existe y hoy siempre vale 0: nadie lo llena. D-10 dice que el umbral semanal es **por requisición** y por eso el Timesheet se rompe así, pero falta el paso que compara las horas de la semana contra el umbral y reparte el excedente entre los días. Sin eso, la nómina va a pagar tiempo normal por tiempo extra. Ver **D-24**.
23. **La falta contra el descanso.** `is_absence` tampoco lo llena nadie. Distinguirlos necesita cruzar el turno **planeado** contra el ponchado —para eso existe `schedule_entry`—, y ese cruce todavía no se hace. Un colaborador sin ponche hoy simplemente no tiene día.
24. ~~**La factura al hotel.**~~ **RESUELTO** (2026-08-18). Ver **D-26**: el margen ya se mide.
25. **La Matriz de Permisos de Contabilidad.** Las seis filas de `payroll` salieron de `Flujo de Nómina`, no de una matriz. Ver **D-25**.
26. **La nota del cambio de estado del Colaborador.** `worker_state_history` no tiene columna `note` y `prospect_state_history` sí. Hoy va al journal. Ver **D-26**.
27. **El expediente y el rate interno siguen sin endpoint.** `personal.worker_document` guarda SSN e ITIN cifrados y `personal.worker_rate` el pay rate personal — la nómina ya lo lee, pero nadie puede darlo de alta ni autorizar un aumento.
28. **Cobertura de react-native-reusables.** D-16 la elige para el móvil sin haber verificado su catálogo contra las pantallas del ponche. Se verifica antes de la primera pantalla de `apps/mobile`; si el hueco es grande, la alternativa es gluestack-ui v2 y eso reabre D-12.

> [!note]
> **Cerradas en esta ronda:** el idioma del esquema (D-11) y el nombre inglés del Colaborador, que es **`Worker`** desde el 2026-08-06.

---

## Relacionado

- [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]]
- [[Arquitecturas/_Globales/Árbol de Reglas de Negocio|Árbol de Reglas de Negocio]]
- [[Arquitecturas/_Globales/Índice de Arquitecturas|Índice de Arquitecturas]]
- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] — fuente de verdad
- [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento|_GUIA - Plantilla de Arquitectura de Departamento]]
