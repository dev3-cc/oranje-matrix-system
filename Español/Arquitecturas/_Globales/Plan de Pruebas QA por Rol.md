---
tags:
  - arquitectura
  - global
  - qa
aliases:
  - Plan de Pruebas QA por Rol
  - Plan de QA
  - Pruebas por Rol
---

# Plan de Pruebas QA por Rol

> [!info] Alcance
> Plan maestro de QA funcional **por rol** (quién puede qué, quién no, a quién
> reporta y quién lo respalda) más el plan de **carga masiva de usuarios**.
> La fuente de las reglas es [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]];
> la de permisos, la Matriz de cada departamento y el seed (`permissions.ts`, 343 filas).
> Los usuarios de prueba viven en `~/Desktop/oranje-credenciales/credenciales.md`
> (las contraseñas NO se copian a ningún otro documento).

## 1. Herramientas — decisión

| Necesidad | Herramienta | Veredicto |
|---|---|---|
| E2E funcional por rol (navegador) | **Playwright** | **Se queda.** Ya está montado en `apps/web` (`playwright.config.ts`, suite `fugas-esquema`), usa el modo mocks y los usuarios reales de Firebase. |
| E2E con Cypress | Cypress | **No se recomienda.** Duplica exactamente lo que ya hace Playwright, con peor soporte de multi-tab/multi-context (necesario para probar dos roles a la vez) y otra toolchain que mantener. |
| **Carga masiva de usuarios** | Cypress | **No sirve para carga.** Cypress/Playwright levantan un navegador por usuario virtual: 500 usuarios = 500 Chromes. La carga no se hace por UI. |
| Carga masiva de usuarios | **k6** (Grafana) | **Recomendada.** Scripts en JS (mismo idioma del monorepo), golpea la API directo (`/auth/session`, `/users`, `/prospects`…), umbrales declarativos, corre en CI. |
| Pruebas de API + carga | **Karate** (Karate Labs) | **Se puede, con matiz.** Framework de API testing en Gherkin sobre JVM; la carga la hace vía `karate-gatling`. Funciona, pero mete Java/Maven a un monorepo 100% TypeScript: dos toolchains de pruebas que mantener. Si el equipo valora la sintaxis Gherkin legible, es el candidato; si no, k6 cubre la carga con menos fricción. |

> [!note] Karate vs k6 — la comparación pedida
> **Karate** prueba APIs con features Gherkin (`.feature`) y carga con
> `karate-gatling`; corre sobre **JVM** (Java/Maven). **k6** hace solo carga,
> en **JavaScript**, el idioma del monorepo. Los 6 escenarios de la sección 5
> se pueden escribir en cualquiera de los dos sin cambiar el plan; la
> recomendación por defecto es k6 por encaje de stack, y Karate queda como
> alternativa válida si pesa más la legibilidad Gherkin para no-programadores.

**Reparto:** Playwright prueba lo **funcional por rol** (este documento, secciones 3–4).
k6 prueba la **carga** (sección 5). La API a pelo se explora con Postman (82 peticiones, 7 tokens).

## 2. Catálogo de roles: reporta a / respaldo

Los 19 roles de D-18, con su línea de reporte y las atribuciones compartidas o
delegadas que el sistema hace cumplir (no son cortesía organizacional: cada una
tiene guard).

| Código | Rol | Reporta a | Respaldo / atribución compartida | Usuario de prueba |
|---|---|---|---|---|
| ROL-H-01 | Supervisor | Manager de Área | Stand-by (Rosa) compartido con ROL-H-02 | supervisor@casacurtidor.com |
| ROL-H-02 | Manager de Área (Hotel Manager) | Manager General | Aprueba horas de SU departamento; autoriza requisiciones (pareja con ROL-H-03) | mgr-area@casacurtidor.com |
| ROL-H-03 | Manager General | — (máxima autoridad del hotel) | **Respaldo manual** del ROL-H-02: aprueba horas de cualquier departamento (D-09); en jerarquía simple ES el Manager de Área (misma persona, dos roles) | mgr-general@casacurtidor.com |
| ROL-R-01 | Reclutadora | Líder de Grupo | Escala al Líder por timeout del Self-Pick según urgencia | reclutadora@casacurtidor.com |
| ROL-R-02 | Líder de Grupo de Reclutadoras | Manager de Reclutamiento | Aprueba el cierre en Azul claro que la Reclutadora marca (RF-05) | lider-reclutadoras@casacurtidor.com |
| ROL-R-03 | Manager de Reclutamiento | — (dirección de Reclutamiento) | — | mgr-reclutamiento@casacurtidor.com |
| ROL-V-01 | Business Developer (BD) | BDC | — (no convierte, no reactiva Café/Negro, no asigna territorio) | hugo@casacurtidor.com |
| ROL-V-02 | Business Developer Coordinator (BDC) | — (dirección de Ventas) | Conversión Rosa→Naranja EXCLUSIVA (RR-V-01); `territory:assign` compartido con ROL-ADM-01 | bdc@casacurtidor.com |
| ROL-I-01 | Inspector | Coordinador | Cierra el accidente SOLO si es el `inspector_id` de la tarjeta (más estrecho que el rol, D-32) | *no existe* |
| ROL-I-02 | Coordinador de Inspección | — (dirección de Inspección) | — | *no existe* |
| ROL-Q-01 | Operador de QA (5, uno por depto) | Manager de QA | QA observa y mide; NO ejecuta la operación de nadie | *no existe* |
| ROL-Q-02 | Manager de QA | — (dirección de QA) | — | *no existe* |
| ROL-CS-01 | Agente de Customer Service | CS Manager | — (módulo sin construir, 0 permisos) | *no existe* |
| ROL-CS-02 | Customer Service Manager | — (dirección de CS) | — | *no existe* |
| ROL-CO-01 | Contadora | Manager de Contabilidad | Genera y **valida** el consolidado (primera firma) | contadora@casacurtidor.com |
| ROL-CO-02 | Manager de Contabilidad | — (dirección de Contabilidad) | **Autoriza** la nómina (segunda firma); no puede validar | mgr-contabilidad@casacurtidor.com |
| ROL-C-01 | Colaborador | Operativamente, al Supervisor del hotel asignado | Todo su alcance es `_own` (RR-C-01) | *no existe* (su app es `/empleado`) |
| ROL-SYS-01 | Sistema | — | Actor de transiciones automáticas; **jamás debe tener login** | *no existe (a propósito)* |
| ROL-ADM-01 | Administrador | — | ÚNICO que levanta vetos (`blacklist:lift`); CRUD de personal (`users:manage`); `territory:assign` compartido con BDC | admin@casacurtidor.com |

## 3. Plan de pruebas por rol

Convención de IDs: `QA-[código corto]-[NN]`. Cada rol prueba **tres cosas**:
lo que SÍ puede (camino feliz), lo que NO puede (403 esperado — la prueba de la
Matriz), y su relación jerárquica (respaldo/delegación). Todas corren con
Playwright contra los usuarios de prueba, salvo donde se indica API/Postman.

### Hotel

**ROL-H-01 · Supervisor** (revisa, no aprueba — D-09)
- QA-H1-01 ✅ Crea una requisición (nace Azul claro · Por autorizar; el Inspector se asigna solo por zona, RR-13).
- QA-H1-02 ✅ Revisa un `timesheet_day` con anomalía: resuelve, escribe `review_note`, registra ponche manual (motivo obligatorio, `ck_punch_manual`).
- QA-H1-03 ✅ Manda la semana a revisión; ve Mi Personal con semáforo del colaborador.
- QA-H1-04 ✅ Captura presencial de accidente (tarjeta en GRIS); reporta colaborador (Rojo).
- QA-H1-05 🚫 Aprobar horas → **403** (la aprobación es de ROL-H-02/H-03).
- QA-H1-06 🚫 Autorizar una requisición → **403**.
- QA-H1-07 ✅ Pone en Stand-by (Rosa) a un colaborador asignado (atribución compartida con su jefe).

**ROL-H-02 · Manager de Área** (reporta al Manager General)
- QA-H2-01 ✅ Autoriza una requisición (Azul claro → Verde manzana; la urgencia se calcula al autorizar, RR-H-05).
- QA-H2-02 ✅ Aprueba horas de SU departamento (Housekeeping).
- QA-H2-03 🚫 Aprueba horas de OTRO departamento → **403** (alcance `hotel_id + department_id`).
- QA-H2-04 ✅ Cambia estado de requisición con motivo (`requisition_state_history`).
- QA-H2-05 🚫 Validar nómina → **403** (es de Contabilidad).

**ROL-H-03 · Manager General** (respaldo del de Área)
- QA-H3-01 ✅ Aprueba horas de CUALQUIER departamento — la prueba del respaldo D-09: lo que en QA-H2-03 dio 403, aquí pasa.
- QA-H3-02 ✅ Autoriza requisiciones de cualquier departamento.
- QA-H3-03 ✅ Ve el Timesheet Global (Indicador de Cumplimiento; GRIS no evalúa).
- QA-H3-04 🚫 Revisar/levantar vetos de Blacklist → **403** (solo ROL-ADM-01 levanta).
- QA-H3-05 📋 Documentar: el respaldo es MANUAL — nadie le transfiere la jornada al vencer un plazo; las horas sin aprobar quedan en espera (sin pago, sin factura).

### Reclutamiento

**ROL-R-01 · Reclutadora** (reporta al Líder de Grupo)
- QA-R1-01 ✅ Crea colaborador Fase 1 (nace BLANCO; posición/modalidad/inglés/experiencia los decide ella, no el colaborador — D-33).
- QA-R1-02 ✅ Valida el alta (BLANCO → VERDE FUERTE, RF-08) solo si `is_profile_complete` (los 9 campos; la foto no cuenta).
- QA-R1-03 ✅ Toma un slot de la Bolsa Self-Pick; dos sesiones simultáneas sobre el MISMO slot: una gana, la otra recibe 409 (RR-15, `SKIP LOCKED` — correr por API).
- QA-R1-04 ✅ Veta a un colaborador con evidencia (`ck_blacklist_evidence`); colaborador en GRIS → el motor rechaza el veto (D-27).
- QA-R1-05 🚫 Levantar un veto → **403** (solo Administrador).
- QA-R1-06 ✅ Marca cierre en Azul claro… ⚠ hueco documentado: hoy el sistema NO exige la aprobación del Líder (RF-05 pide "pendiente de aprobación"; pendiente de D-23). La prueba deja constancia del comportamiento actual.
- QA-R1-07 🚫 Ver pantallas de Ventas (pipeline, propuestas) → ocultas/403.

**ROL-R-02 · Líder de Grupo** (reporta al Manager de Reclutamiento)
- QA-R2-01 ✅ Ve la cola de su grupo y recibe la escalación del Self-Pick por timeout (plazo según urgencia).
- QA-R2-02 📋 Aprobar el cierre que marcó la Reclutadora — HOY NO EXISTE el paso (mismo hueco de QA-R1-06); la prueba documenta la ausencia.
- QA-R2-03 🚫 Autorizar requisiciones de Hotel → **403**.

**ROL-R-03 · Manager de Reclutamiento**
- QA-R3-01 ✅ Ve requisiciones con la cola autorizada (`read_authorized_queue`); los borradores Azul claro se le ocultan si solo tiene la cola.
- QA-R3-02 ✅ KPIs de su departamento (pool por estado, cola Self-Pick).
- QA-R3-03 🚫 Aprobar horas del hotel → **403**.

### Ventas

**ROL-V-01 · Business Developer** (reporta al BDC)
- QA-V1-01 ✅ Alta de prospecto (hotel nuevo + contacto + ciclo; un solo ciclo abierto por hotel — `ux_prospect_hotel_open`, el segundo da `PROSPECT_ALREADY_OPEN`).
- QA-V1-02 ✅ Recorre su semáforo: Gris→Azul claro→Verde→Amarillo→Rosa; NO puede salir de Verde sin propuesta enviada (D-22).
- QA-V1-03 ✅ Registra intentos; edita SOLO los intentos de su autoría.
- QA-V1-04 ✅ Propuestas: un solo borrador abierto, lo enviado no se edita, una versión por envío.
- QA-V1-05 🚫 Conversión Rosa→Naranja → **no aparece/403** (RR-V-01: exclusiva del BDC).
- QA-V1-06 🚫 Reactivar Café/Negro → **403**; 🚫 asignar territorio → **403**.
- QA-V1-07 ✅ Mi Territorio muestra SUS zonas, sin selector de equipo.

**ROL-V-02 · BDC** (delegaciones más ricas del sistema)
- QA-V2-01 ✅ Aprueba conversión Rosa→Naranja SOLO con los 4 requisitos (propuesta aceptada, T&C validado, contacto principal, **Usuario del Hotel creado** — RR-V-02: sin usuario, el botón bloqueado).
- QA-V2-02 ✅ Devuelve a Café con motivo del catálogo.
- QA-V2-03 ✅ Reactiva Café y Negro → siempre hacia Azul claro (RR-V-07).
- QA-V2-04 ✅ Asigna territorio a un BD (`territory:assign`); Mi Territorio con selector de equipo (Hugo cuelga de él).
- QA-V2-05 ✅ Mi Equipo: ve KPIs de sus BDs.
- QA-V2-06 🚫 Aprobar nómina/horas → **403**.
- QA-V2-07 ✅ Crea el **Usuario del Hotel desde el contacto principal** en la propia pantalla de Conversión (es lo que desbloquea el botón cuando falta, RR-V-02).
- QA-V2-08 ✅ **Poscondiciones de aprobar** — las cinco, en una sola corrida: (1) el prospecto queda en ORANGE; (2) hay fila nueva en `prospect_state_history` (Rosa→Naranja, con el BDC como actor); (3) `hotel.activated_at` toma la fecha; (4) el hotel aparece en **Clientes Activos** (`vw_client`); (5) el hotel queda **habilitado para requisiciones**: crear una para ese hotel ahora pasa, y antes de aprobar fallaba.
- QA-V2-09 🚫 Verificado en seed: `PINK → ORANGE` autoriza SOLO a ROL-V-02 — el intento del BD por API (no solo el modal oculto) → **403**.

### Inspección *(sin usuarios de prueba — crear ROL-I-01 y ROL-I-02 antes de ejecutar)*

**ROL-I-01 · Inspector** (reporta al Coordinador)
- QA-I1-01 ✅ Da seguimiento médico a la tarjeta de accidente de SU zona.
- QA-I1-02 ✅ Cierra la tarjeta con alta médica → el colaborador GRIS vuelve a Verde fuerte (solo pisa el estado si sigue en GRIS).
- QA-I1-03 🚫 OTRO inspector (no el `inspector_id` congelado de la tarjeta) intenta cerrar → **403** (D-32: más estrecho que el rol).
- QA-I1-04 🚫 Vetar al colaborador accidentado → `WORKER_PROTECTED` (el GRIS protege).

**ROL-I-02 · Coordinador**
- QA-I2-01 ✅ Ve tarjetas de todas las zonas. — ⚠ Inspección no tiene Matriz de Permisos: sus filas se derivaron del flujo (tercer departamento en esa situación); validar contra negocio antes de dar por buena cualquier 403.

### QA *(sin usuarios; el módulo mide, no opera)*

**ROL-Q-01/Q-02**
- QA-Q-01 ✅ Lee indicadores de su departamento supervisado (Indicador de Calidad).
- QA-Q-02 🚫 CUALQUIER operación de negocio (crear requisición, aprobar, vetar) → **403** — QA observa, mide y retroalimenta; la prueba es que no puede ejecutar nada.

### Customer Service *(módulo sin construir; 0 permisos sembrados)*
- QA-CS-01 📋 Login posible, cero módulos visibles, todo 403 — documentar como estado esperado hasta que exista el módulo.

### Contabilidad *(solo API/Postman — sin pantallas en el front)*

**ROL-CO-01 · Contadora** (reporta al Manager de Contabilidad)
- QA-CO1-01 ✅ Genera el consolidado (cálculo en SQL, montos como cadena) y lo **valida** (primera firma).
- QA-CO1-02 🚫 **Autorizar** el consolidado → **403** (paso del Manager).
- QA-CO1-03 ✅ Verifica el golden test: 40 h + 2 OT = 840.00 colaborador / 1505.00 hotel / margen 665.00.

**ROL-CO-02 · Manager de Contabilidad**
- QA-CO2-01 ✅ **Autoriza** el consolidado validado (segunda firma).
- QA-CO2-02 🚫 **Validar** → **403** (paso de la Contadora). Las dos firmas nunca en la misma persona.

### Transversales

**ROL-C-01 · Colaborador** (todo `_own`, RR-C-01; su app es `/empleado`)
- QA-C-01 ✅ `GET /workers/me`, signup Fase 2 (transporte; SSN/ITIN opcional con plazo), Fase 3 (emergencia/salud).
- QA-C-02 ✅ Poncha SU asignación; 🚫 ponchar la asignación de otro (id ajeno en el cuerpo) → **403** (D-32 corrigió esto).
- QA-C-03 ✅ Día 3 sin SSN/ITIN pasa; día 4 aviso; día 5 → `TAX_ID_OVERDUE` y suspensión; subir el documento la levanta (D-33).
- QA-C-04 ✅ `/consolidations/me` solo muestra PAID y jamás expone `pay_rate` interno ni deducciones internas (RR-C-05).

**ROL-ADM-01 · Administrador**
- QA-ADM-01 ✅ CRUD de personal del sistema (`users:manage`); los roles de hotel se rechazan hacia su alta propia; correo inmutable.
- QA-ADM-02 ✅ Levanta un veto (los tres campos del lift juntos, `ck_blacklist_lift`) → el colaborador vuelve a BLANCO (no a disponible).
- QA-ADM-03 ✅ Asigna territorio (compartido con BDC).
- QA-ADM-04 🚫 En el front de Ventas casi todo → **403 esperado, no bug** (así está documentado en credenciales).

**ROL-SYS-01 · Sistema**
- QA-SYS-01 🚫 Nunca autentica: no debe existir cuenta Firebase ni aceptar login. Prueba negativa permanente.
- QA-SYS-02 ✅ Sus efectos se observan: BROWN→STRONG_GREEN al vencer la asignación; veto ABSENCES por 3 faltas en 30 días.

### Flujos con candado — los gemelos de la Conversión

Mismo patrón que Rosa→Naranja: una transición con **actor exclusivo** (verificado
en seed), un **candado** (requisitos que desbloquean) y **poscondiciones**
observables. Cada flujo se prueba en las tres dimensiones: el actor correcto
pasa, el actor equivocado recibe 403 por API (no solo botón oculto), y las
poscondiciones se asertan todas.

- QA-FL-01 **Autorizar requisición** (Azul claro → Verde manzana · ROL-H-02/H-03). Poscondiciones: `authorized_at` sellado; la urgencia se calcula contra la fecha de inicio (a 2 días nace Rojo, RR-H-05); los slots aparecen en la **Bolsa Self-Pick**; fila en `requisition_state_history`.
- QA-FL-02 **Enviar propuesta = candado de salida de Verde** (D-22). Sin propuesta enviada, Verde→Amarillo/Rojo/Café → **422**; al enviar: `sent_at` sellado, la versión queda inmutable, y ahora sí se avanza.
- QA-FL-03 **Reactivaciones asimétricas** (RR-V-07, seed): Rojo la reactiva el **BD** sin motivo; Café y Negro SOLO el **BDC** con motivo; las tres aterrizan SIEMPRE en Azul claro. Negativa clave: el BD intenta reactivar Negro por API → **403**.
- QA-FL-04 **Archivar ciclo ≠ Rojo/Negro** (D-22, `pipeline:close_cycle` BD y BDC). Candado: un hotel en Naranja NO se archiva. Poscondiciones: `closed_at` sellado; el hotel queda LIBRE para un ciclo nuevo (`ux_prospect_hotel_open` ya no bloquea); las propuestas del ciclo no se borran.
- QA-FL-05 **Validar alta del colaborador** (BLANCO → VERDE FUERTE · Reclutadora, RF-08). Candado: `is_profile_complete` (los 9). Poscondiciones: aparece en el **Pool** y ya es asignable a un slot.
- QA-FL-06 **Asignar slot** (VERDE FUERTE/Amarillo → CAFÉ · Reclutadora/Líder). Poscondiciones: slot `occupied` (y un slot ocupado no se puede borrar); al **vencer la vigencia** el SISTEMA lo regresa solo a VERDE FUERTE — la prueba observa el efecto del actor automático.
- QA-FL-07 **Stand-by y regreso** (Rosa → VERDE FUERTE · los 3 roles de Hotel, seed). Ponerlo lo comparten Supervisor y Managers; quitarlo lo regresa a disponible. Negativa: la Reclutadora intenta quitar un Stand-by → 403.
- QA-FL-08 **Accidente: crear y cerrar como transacción** (GRIS). Crear la tarjeta mueve a GRIS en la MISMA transacción; cerrar con alta médica devuelve a VERDE FUERTE **solo si sigue en GRIS** — si el estado cambió entre tanto, cerrar NO lo pisa.
- QA-FL-09 **Levantar veto** (NEGRO → BLANCO · solo Administrador, con motivo). Poscondición fuerte: NO vuelve a disponible — queda BLANCO y tiene que reingresar por la validación de la Reclutadora (QA-FL-05 otra vez).
- QA-FL-10 **La cadena de nómina completa, multi-actor**: día sin salida bloquea el envío → el Supervisor manda a revisión → el Manager de Área aprueba (su depto) → la Contadora genera y valida → el Manager de Contabilidad autoriza → el consolidado PAID aparece en `/consolidations/me` del colaborador SIN el pay rate interno. Cada eslabón con el actor equivocado → **403**.
- QA-FL-11 **Renovar contrato** (`ux_contract_active`): activar un segundo ACTIVE sin expirar el primero → rechazado; la renovación es EXPIRED + ACTIVE **en la misma transacción**.
- QA-FL-12 **Incidencias solo en estados operativos** (seed `WORKER_OPERATIONAL`): faltar o accidentarse solo se registra desde Verde manzana/Azul claro/Naranja/Café — "no se falta sin Schedule". Registrar una falta a un colaborador en Amarillo o Rosa → rechazado.
- QA-FL-13 📋 **Disponibilidad voluntaria sin regreso** (hueco D-32): el Colaborador activa VERDE FUERTE → AMARILLO, pero el semáforo NO define la vuelta (desde Amarillo solo Café y Negro) — el endpoint solo activa. La prueba documenta que "desactivar Amarillo" no existe, gana el semáforo.
- QA-FL-14 **Primer login enlaza la cuenta** (hallazgo 2026-08-19): alta por el Administrador deja `firebase_uid` nulo; el PRIMER login con correo verificado lo escribe; el segundo login entra normal. Negativa: correo sin verificar no enlaza.

### Operación transversal: dos turnos en distinto hotel (RR-05)

El criterio de exclusividad son las **horas, no el hotel** — lo impone Postgres
con `EXCLUDE USING gist (worker_id, shift_range)`; el servicio solo traduce el
`23P01` a un 409 (D-24). Estado: verificado a mano el 2026-08-17; falta
automatizarlo. Por API (la pantalla de Schedule del front sigue con fixtures).

- QA-OP-01 ✅ Mismo día, Hotel A 07:00–15:00 + Hotel B 16:00–23:00 → ambos turnos aceptados.
- QA-OP-02 🚫 Hotel A 07:00–15:00 + Hotel B 14:00–22:00 → **409** (solapan 1 hora; da igual que sean hoteles distintos).
- QA-OP-03 ⚠ Turnos ADYACENTES: A 07:00–15:00 + B 15:00–22:00 → deben pasar (el rango es semiabierto); si el motor los rechaza es bug del modelado del rango, no regla de negocio.
- QA-OP-04 🚫 Concurrencia: dos peticiones simultáneas con turnos solapados → exactamente una gana; la garantía es del `EXCLUDE`, no de un `SELECT` previo.
- QA-OP-05 ⚠ Turno nocturno que cruza medianoche (22:00–06:00) vs 05:00–13:00 del día siguiente → deben chocar; documentar cómo se modela el cruce de día.
- QA-OP-06 ⚠ Hoteles en `time_zone` distintas: el solape se evalúa en tiempo absoluto, no en hora local — documentar el comportamiento actual (la columna existe desde Estándares de BD §11).
- QA-OP-07 ✅ Poncha en ambos hoteles el mismo día: cada marca se valida contra la geocerca de SU hotel; cada asignación acumula sus horas por separado.
- QA-OP-08 📋 Aprobación repartida: las horas del hotel A las aprueba el Manager de A y las de B el de B — ninguno ve las del otro (alcance por `hotel_id`).

### Operación transversal: overtime

Cómo lo maneja hoy el sistema: el contrato define los multiplicadores
(`overtime_pay_multiplier` / `overtime_bill_multiplier`, ambos ≥ 1.00 y factura
≥ paga por `ck_contract_multiplier_margin`); el umbral es **semanal POR
REQUISICIÓN** (D-10), y la semana de nómina la fija el contrato
(`week_start_day`/`week_end_day`). `settlement` paga con el multiplicador de
pago y factura con el de facturación — jamás los cruza.

> [!warning] El hueco que estas pruebas deben mantener visible
> `overtime_minutes` existe en la base y **nadie lo llena** (pendiente de D-24):
> hoy el tiempo extra se liquida como tiempo normal. `is_absence` está en la
> misma situación (una falta no se distingue de un descanso).

- QA-OT-01 ✅ Golden de nómina: 40 h + 2 OT → **840.00** al colaborador / **1505.00** al hotel / margen **665.00** (ya existe como test de integración; subirlo también a la suite de API).
- QA-OT-02 📋 Semana con 42 h reales SIN `overtime_minutes` marcado → hoy sale como 42 normales. La prueba queda EN ROJO a propósito: es la evidencia del hueco hasta que alguien llene la columna.
- QA-OT-03 🚫 Contrato con `bill_multiplier` < `pay_multiplier` → rechazado; multiplicador < 1.00 → rechazado.
- QA-OT-04 ⚠ Multi-hotel: 30 h en A + 20 h en B la misma semana (50 h reales) → **NO genera overtime** porque el umbral es por requisición (D-10). Documentar y validar con negocio: ¿50 h reales sin OT es lo deseado? (tema laboral).
- QA-OT-05 ⚠ Contratos con semanas de nómina distintas (A lunes–domingo, B miércoles–martes) → el corte semanal de cada consolidado respeta SU contrato; las horas no se mezclan entre cortes.
- QA-OT-06 📋 Horas aprobadas DESPUÉS del cierre semanal: ¿reabren el consolidado o entran a la siguiente semana? Pendiente abierto desde 2026-08-04 — la respuesta cambia el umbral de OT.

### Eliminar vs archivar — casi nada se borra

Filosofía del sistema (Estándares de BD, D-14, D-22): lo que tiene historia
no se borra, se **archiva, se marca o se cierra**; las tablas de historia y el
`journal` son append-only. Cada caso prueba que el borrado que NO debe existir
de verdad no existe (por API, no solo sin botón), y que el que sí existe
respeta sus candados.

- QA-DEL-01 ✅ **Archivar ciclo ≠ eliminar** (QA-FL-04): el prospecto conserva su historia y sus propuestas; solo gana `closed_at`. Rojo y Negro NO son borrado: son reactivables (RR-V-07).
- QA-DEL-02 🚫 **La requisición no se puede eliminar hoy**: el estado Morado (Eliminada) existe en el semáforo pero **no es alcanzable desde ningún estado** (5 transiciones sembradas); intentar → rechazado. 📋 Validar con negocio si debe existir la transición y desde dónde.
- QA-DEL-03 ✅/🚫 **Slot**: uno libre se borra; uno ocupado **no** (FK de `coverage.assignment`) → rechazo; la requisición sigue viva sin ese slot y la cobertura se recalcula.
- QA-DEL-04 🚫 **Blacklist no se borra**: levantar marca `lifted_*` y la fila queda como historial; no existe DELETE. Un veto levantado sigue visible en el listado con estado Levantada.
- QA-DEL-05 🚫 **Contacto del hotel**: con intentos registrados NO se elimina (`ON DELETE RESTRICT` → 409); sin intentos, sí. ⚠ Documentar qué pasa al eliminar al contacto **principal**: el hotel no debería quedar sin principal.
- QA-DEL-06 ✅ **Propuesta**: solo el **borrador** se descarta (`DELETE` solo si `isDraft`, bitácora 2026-08-28); una enviada → rechazo, porque el hotel ya la vio. Al archivar el ciclo, ninguna se borra.
- QA-DEL-07 🚫 **Hotel no se borra** (decisión de Hugo, 2026-08-28: el borrado de hoteles queda fuera): no existe endpoint; el edificio sobrevive a todos sus ciclos.
- QA-DEL-08 📋 **Usuario del sistema**: el CRUD es `GET/POST/PATCH` — no hay DELETE; la baja debe ser **desactivación** (conserva `journal` y permisos). Documentar cómo se desactiva y que un desactivado no puede entrar.
- QA-DEL-09 ✅ **Colaborador nunca se elimina**: su salida es un estado (Negro por veto o por motivo de salida). ⚠ El **catálogo de motivos de salida sigue sin poblar** — hueco conocido; la prueba lo mantiene visible.
- QA-DEL-10 🚫 **Historias append-only**: `prospect_state_history`, `requisition_state_history`, `worker_state_history` y `journal` no tienen `updated_at` ni `deleted_at`; no existe endpoint que las modifique, y un intento por API → 404/405.
- QA-DEL-11 📋 **Archivos huérfanos**: descartar una propuesta o vetar no borra el objeto del bucket; la retención no está definida (pendiente D-30). Documentar, no fallar.

### Modalidades de contratación (catálogo `hiring_modality`)

La modalidad vive en tres lugares: el perfil del colaborador (la fija la
Reclutadora en Fase 1 — decisión de Oranje, D-33), la posición de la
requisición, y los filtros de Pool y Bolsa. El vault describe el match
(posición + zona + disponibilidad + modalidad) como **criterio de búsqueda de
la Reclutadora**, no como candado del motor.

- QA-MO-01 ✅ La modalidad del colaborador la fija la Reclutadora en Fase 1; **no está en el signup de Fase 2/3** — el colaborador que la manda en el cuerpo la ve ignorada/rechazada (D-33: se pudo elegir su propia modalidad y se corrigió).
- QA-MO-02 🚫 Valor fuera del catálogo `catalogs.hiring_modality` → rechazado (FK); el catálogo solo lo administra Oranje.
- QA-MO-03 ✅ La posición de la requisición lleva su modalidad (Por evento, Nómina…): se captura al crear, viaja a la autorización y se muestra en la Bolsa Self-Pick.
- QA-MO-04 ✅ Los filtros de Pool y Bolsa por modalidad devuelven solo lo que corresponde (el Pool filtra colaboradores; la Bolsa, slots).
- QA-MO-05 📋 **El match NO es candado**: hoy el motor permite asignar un colaborador Tiempo completo a un slot Por evento — el vault lo describe como criterio de la Reclutadora, no como regla dura. La prueba documenta el comportamiento y la pregunta va a negocio: ¿debe el sistema impedir el mismatch o solo mostrarlo?

### Handoffs entre departamentos — el que sigue VE su trabajo

Cada handoff se prueba con DOS sesiones a la vez (el que entrega y el que
recibe): al completarse el paso, el trabajo aparece en las pantallas del
siguiente — y NO antes.

> [!warning] El aviso automático todavía no existe
> El mecanismo de "avisarle al área que sigue" son las notificaciones (56 tipos
> sembrados, reglas de destinatario en el evento), pero **nadie publica eventos
> aún** (pendiente #1 de D-31): la tabla existe vacía. Hoy la visibilidad es
> POR PANTALLA. QA-HO-07 mantiene ese hueco a la vista.

- QA-HO-01 **Ventas → Hotel** (Naranja): al aprobar la conversión, el **Usuario del Hotel** creado en el paso puede entrar, y el hotel ya es elegible para crear requisiciones. Antes de Naranja, crear una requisición para ese hotel falla.
- QA-HO-02 **Hotel llena, Reclutamiento cubre** — el reparto de la requisición: la CREA el Supervisor (o Manager), la AUTORIZA el Manager de Área/General, y Reclutamiento **no la llena ni la edita: la cubre** tomando slots. Negativas: la Reclutadora intenta crear o editar una requisición → **403**; el Supervisor intenta tomar un slot de la Bolsa → **403**.
- QA-HO-03 **Hotel → Reclutamiento** (autorización): la requisición en **Azul claro (borrador) NO le aparece** a Reclutamiento; al autorizarse (Verde manzana) aparece en su cola y sus slots en la Bolsa Self-Pick — con las dos sesiones abiertas, el refresco del lado receptor la muestra sin tocar nada del lado emisor.
- QA-HO-04 **Reclutamiento → Hotel** (cobertura): al asignar un colaborador, el Semáforo de Posiciones del hotel sube (0/2 → 1/2), el colaborador aparece en **Mi Personal** del Supervisor y el Schedule refleja demanda + cobertura.
- QA-HO-05 **Reclutamiento → Colaborador**: asignado (CAFÉ), su turno le aparece en `/empleado` ("Tu turno de hoy") y ya puede ponchar — antes de la asignación, ponchar falla.
- QA-HO-06 **Hotel → Contabilidad**: SOLO las horas aprobadas llegan al consolidado; una semana enviada pero sin aprobar queda EN ESPERA — no aparece en nómina ni en factura (bitácora 2026-08-04).
- QA-HO-07 ⏸ **APLAZADO (decisión de Hugo, 2026-08-25): las notificaciones NO se prueban por ahora** — nadie publica eventos todavía (pendiente #1 de D-31) y no tiene caso una suite en rojo sobre un módulo sin arrancar. El caso queda redactado para activarse cuando exista `infra/pubsub/`: tras convertir/autorizar/asignar, la fila debe aparecer en `notifications.notification`.

### Carga de archivos (módulo `files`, D-30)

Todo archivo entra por `POST /files` **multipart** (no URL firmada: comprimir
exige que los bytes pasen por el servidor). El bucket es privado; lo que se
guarda es el **path**, y la URL firmada (caduca en 1 h) se compone al leer.
Cinco superficies usan archivos: foto de perfil, documento del expediente
(SSN/ITIN, comprobante), foto del ponche, evidencia de Blacklist — y las fotos
de hotel que NO se suben (vienen de Places, D-34).

- QA-AR-01 ✅ Subir foto de perfil: se comprime a WebP (2.65 MB → ~2 KB en la medición de D-30) y **pierde el EXIF** — subir una foto con coordenadas GPS y verificar que el archivo servido ya no las trae.
- QA-AR-02 🚫 Basura con `Content-Type: image/jpeg` → rechazada: la compresión hace de verificación (la librería no la puede abrir).
- QA-AR-03 🚫 **Validación de prefijo**: el alta intenta apuntar la foto de la tarjeta a `workers/document/…` → rechazado. Sin esto, un documento fiscal se filtra a la lista que ve más gente.
- QA-AR-04 ✅ La URL firmada **caduca en 1 h** y jamás se persiste (se guarda el path): la respuesta trae URL con expiración; una nueva petición firma de nuevo. (La lección de `photo_path`/`photo_ref`: si deja de servir con el tiempo, es credencial, no dato.)
- QA-AR-05 ✅ Resiliencia: si no se puede firmar, la foto va en `null` **y el listado responde completo** — una foto no tumba la lista.
- QA-AR-06 ✅ El documento fiscal corre el negocio: SUBIR el SSN/ITIN levanta la suspensión del día 5 (QA-C-03); **cargar ≠ verificar** — la retención del 16% la levanta la VERIFICACIÓN, no la carga (y por D-27, mientras el cifrado no se conecte, aplica a todos).
- QA-AR-07 ✅ Foto del ponche: **obligatoria en Entrada y Salida**, no en LUNCH ni en el ponche manual (D-32).
- QA-AR-08 🚫 Evidencia de Blacklist: obligatoria cuando `source = MANUAL` (`ck_blacklist_evidence`); sin archivo, el veto no entra.
- QA-AR-09 ⚠ Límite de tamaño: archivo enorme → rechazo limpio con mensaje, nunca un 500. Documentar el límite real (hoy no está asentado en ningún estándar).
- QA-AR-10 🚫 Fotos de hotel NO se suben: el DTO ya no acepta `photoUrl` del cliente (D-34) — mandarla debe ignorarse/rechazarse; la foto la resuelve el servidor vía Places.
- QA-AR-11 📋 Pendientes conocidos de D-30 que las pruebas mantienen visibles: la **retención del bucket** no está definida, y el **objeto huérfano** (subir un archivo y nunca asociar su path a nada) no se limpia.

## 4. Matriz cruzada mínima (la que más bugs caza)

Cada celda es "el rol de la fila intenta la acción de la columna". Se prueba
por API con los tokens de cada usuario; el resultado esperado es 403 salvo la
diagonal documentada arriba.

| ↓ intenta \ acción → | Aprobar horas | Autorizar requisición | Convertir a Naranja | Validar nómina | Autorizar nómina | Levantar veto |
|---|---|---|---|---|---|---|
| Supervisor | 403 | 403 | 403 | 403 | 403 | 403 |
| Manager de Área | ✅ su depto | ✅ | 403 | 403 | 403 | 403 |
| Manager General | ✅ todos | ✅ | 403 | 403 | 403 | 403 |
| Reclutadora | 403 | 403 | 403 | 403 | 403 | 403 |
| BD | 403 | 403 | 403 | 403 | 403 | 403 |
| BDC | 403 | 403 | ✅ | 403 | 403 | 403 |
| Contadora | 403 | 403 | 403 | ✅ | 403 | 403 |
| Mgr. Contabilidad | 403 | 403 | 403 | 403 | ✅ | 403 |
| Administrador | 403 | 403 | 403 | 403 | 403 | ✅ |

## 5. Carga masiva de usuarios (k6)

**Qué se carga y qué no.** La carga golpea la **API** (Cloud Run + Cloud SQL),
nunca la UI. Ambiente: **staging o dev — jamás producción**; datos sintéticos
sin PII real (Estándares de Desarrollo §8: prohibido dump de producción).

| # | Escenario | Cómo | Umbral inicial |
|---|---|---|---|
| K6-01 | **Alta masiva de personal** | Token de ROL-ADM-01 → `POST /users` (N usuarios sintéticos `qa+{i}@casacurtidor.com`) | p95 < 800 ms, 0 errores 5xx |
| K6-02 | **Alta masiva de colaboradores** | Token de Reclutadora → `POST /workers` Fase 1 | p95 < 800 ms; todos nacen BLANCO |
| K6-03 | **Login concurrente** | N sesiones `POST /auth/session` + `GET /me` | p95 < 1.5 s (incluye Firebase) |
| K6-04 | **Lectura bajo carga** | 100 VUs × 5 min sobre `GET /prospects`, `GET /workers`, `GET /requisitions` con tokens de rol mezclados | p95 < 500 ms; los 403 de la matriz §4 se mantienen bajo carga |
| K6-05 | **Self-Pick en estampida** | 50 VUs sobre los MISMOS slots libres | exactamente 1 ganador por slot; el resto 409; cero doble asignación (la garantía es de Postgres, no del servicio) |
| K6-06 | **Ponche en hora pico** | N colaboradores ponchan en la misma ventana (RR-05: `EXCLUDE` de rangos) | 0 solapes aceptados |
| K6-07 | **Subida de fotos en hora pico** | N ponches con foto multipart simultáneos (la compresión a WebP es CPU del servidor) | p95 < 2 s · 0 fotos perdidas · el listado nunca se cae por una firma fallida |

**Notas de implementación:** carpeta propuesta `apps/api/k6/` con un script por
escenario; los tokens se obtienen una vez en `setup()`; la limpieza borra por
prefijo `qa+`. Firebase tiene rate-limit de logins — para K6-03 en volumen alto
se usa el canje de sesión mock o un pool de tokens pre-emitidos.

## 6. Estado y siguientes pasos

1. **Ya existe:** Playwright montado con la suite de fugas de esquema (verde), 82 peticiones Postman con 7 tokens, 150 unitarias.
2. **Falta para ejecutar este plan:** crear usuarios de prueba de Inspección y QA; decidir si CS espera; instalar k6 y la carpeta `apps/api/k6/`; cablear la pantalla de Schedule al dato real (hoy fixtures) para que QA-OP-* pueda subir de API a UI.
3. **Revisión:** cada caso se revisa contra su regla fuente (RR/D citada) antes de automatizarlo; los marcados 📋 documentan huecos conocidos del sistema, no fallas de la prueba.

## Relacionado

- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] — fuente de verdad
- [[Español/Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]] — D-09, D-18, D-22, D-23, D-27, D-32, D-33
- [[Español/Arquitecturas/_Globales/Estándares de Desarrollo|Estándares de Desarrollo]] — §8 pruebas y datos sin PII
- [[Español/Arquitecturas/_Globales/Roles del Sistema|Roles del Sistema]]
- [[Español/Arquitecturas/Ventas/_Globales del Depto/06 - Matriz de Permisos|Matriz de Permisos de Ventas]]
