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

### E2E del ponche por QR — del alta del hotel a la marca (cambio 2026-09-07)

El método de ponche es un dato del **hotel** (Selfie por defecto, QR opcional),
se elige al alta en el Pipeline y aplica a todas sus requisiciones. El QR
sustituye a la **foto** como evidencia, nunca a la **geocerca** (D-08). Flujo
por etapas — A y B por UI (Playwright); C y D por API con el token del
Colaborador (el escaneo con cámara real queda como prueba manual: automatizarlo
exige video falso de Chromium y no paga lo que cuesta).

**Etapa A — Alta del hotel con su método (BD, Pipeline)**
- QA-QR-01 ✅ Alta de prospecto eligiendo **QR** en el radio → `punch_method = QR`; sin tocar el radio queda **Selfie** (el default).
- QA-QR-02 ✅ Un hotel en Selfie que se EDITA a QR genera su **primer código** en ese momento (versión 0 → 1).

**Etapa B — Gestión del QR (tarjeta «Ponche»)**
- QA-QR-03 ✅ `GET /hotels/:id/punch-qr` con `hotel:punch_qr` (BD, BDC, Supervisor y Managers). Negativa: un rol del hotel pide el QR de **OTRO** hotel → **403** (solo el suyo).
- QA-QR-04 ✅ Hoja imprimible `/hoteles/:id/qr-ponche` (fuera del shell): el QR se pinta y el **secreto NO aparece como texto** en el DOM.
- QA-QR-05 ✅ **Regenerar** (doble toque): versión 1 → 2, evento `HOTEL_PUNCH_QR_REGENERATED` en el journal, y el impreso anterior queda muerto — se comprueba en la Etapa D.

**Etapa C — El Colaborador poncha**
- QA-QR-06 ✅ `/schedules/me` trae `hotelPunchMethod = QR` → la app muestra el **lector**, no la cámara de selfie.
- QA-QR-07 ✅ CLOCK_IN con el QR vigente y dentro de geocerca → marca creada **sin foto**, con `qr_version` guardada (la 2, tras regenerar); `ck_punch_evidence` se satisface por el código.
- QA-QR-08 ✅ CLOCK_OUT igual; el día completo llega a la revisión del Supervisor mostrando la versión del QR como evidencia, no una foto.

**Etapa D — Negativas (por API)**
- QA-QR-09 🚫 Hotel QR, marca sin `qrCode` → **422 `QR_REQUIRED`**.
- QA-QR-10 🚫 QR de la versión ANTERIOR (el impreso viejo tras regenerar) → **422 `QR_INVALID`** — y el mensaje da salida: el impreso actual o el ponche manual del Supervisor, no un rechazo mudo.
- QA-QR-11 🚫 QR de **otro hotel** (el payload trae hotel + secreto y ambos deben coincidir con la asignación) → `QR_INVALID`.
- QA-QR-12 ✅ Fuera de geocerca CON QR válido → el QR no sustituye la geocerca (D-08): se ofrece el ponche manual.
- QA-QR-13 🚫 Ponchar la asignación de OTRO con un QR válido → **403 `NOT_YOUR_ASSIGNMENT`** — el QR no debilita D-32.

**Etapa E — Regresión del camino Selfie**
- QA-QR-14 ✅ Hotel en Selfie: marca sin foto → **422 `PHOTO_REQUIRED`**; un `qrCode` enviado ahí se ignora. QA-AR-07 sigue vigente tal cual para estos hoteles.
- QA-QR-15 📋 Limitación **asumida por escrito** (no es bug): el QR impreso no impide que un compañero escanee por otro — el candado real es geocerca + asignación propia; el QR dinámico en pantalla es mejora futura.

> [!note] Carga
> Un hotel en QR poncha **sin subir foto**: K6-07 (compresión WebP) aplica solo
> a hoteles Selfie; K6-06 gana una variante con mezcla de hoteles QR y Selfie.

### Cuentas del hotel, alcance por departamento y sesión en staging (cambio 2026-09-09)

Tres PR de la misma ronda (#38 API, #39 front, #40 API), ya en staging. Usuarios de
prueba: `admin@` (Administrador), `bdc@` (BDC), `hugo@` (BD), `supervisor-xcaret@` y
`mgr-area-xcaret@` (Hotel Xcaret, **Housekeeping**), `mgr-general-xcaret@` (ojo:
es el Manager General de **Hotel Xcaret México**, no de Xcaret). Contraseñas en
`~/Desktop/oranje-credenciales/credenciales.md`. Cada bloque va de la UI a la API:
lo que la pantalla ya no ofrece se prueba **también por API**, porque el 403 tiene
que venir del servidor, no solo del botón oculto.

**Etapa A — Cuentas del hotel las administra el Administrador** ([[Core/Módulos/Reglas de Negocio#Cuentas del hotel|Reglas de Negocio · Cuentas del hotel]])
- QA-CH-01 ✅ `admin@` → Usuarios → pestaña **Personal de hoteles** (`?ambito=hoteles`): lista con hotel, rol · departamento, jefe y estado de cuenta; filtros por hotel y rol y la búsqueda (nombre, correo u hotel) funcionan; «Quitar filtros» regresa a todos; Activos/Inactivos con conteo.
- QA-CH-02 ✅ Alta de un **Supervisor** en Hotel Xcaret, departamento Housekeeping: «Reporta a» solo ofrece a Paola (Manager de Área de Housekeeping) y al Manager General del hotel — nunca a un Supervisor ni a gente de otro hotel; con un solo candidato se propone solo. Al crear: panel «Invitación enviada a: correo», la fila aparece con chip **Invitación enviada**.
- QA-CH-03 ✅ Alta de un **Manager General** adicional: el campo Departamento **no aparece** y «Reporta a» dice «Nadie — la punta de la jerarquía del hotel».
- QA-CH-04 ✅ Alta de un **Manager de Área**: exige departamento; «Reporta a» solo ofrece Managers Generales del hotel.
- QA-CH-05 ✅ Editar: hotel, rol y correo **bloqueados**; nombre, departamento y jefe editables; **Reenviar invitación** visible solo mientras no haya entrado; «Dar de baja» en dos toques → pasa a Inactivos y ya no entra al sistema.
- QA-CH-06 ✅ El correo de invitación llega (Firebase); la persona establece contraseña, entra y su fila cambia a **Ya entró** (`hasAccount`).
- QA-CH-07 ✅ El BDC conserva su alta: `POST /hotels/:id/users` con `bdc@` sigue creando el primer Manager General en la Conversión (RR-V-02 intacta) y ahora también manda la invitación.
- QA-CH-08 🚫 Mismo correo dos veces → **409 `EMAIL_TAKEN`** con mensaje en español; Manager General con `departmentId` → **422 `DEPARTMENT_NOT_ALLOWED`**; Supervisor sin departamento → **422 `DEPARTMENT_REQUIRED`**.
- QA-CH-09 🚫 Por API, «Reporta a» fuera de regla: Supervisor → Manager de Área de OTRO departamento → **422 `REPORTS_TO_OTHER_DEPARTMENT`**; Manager de Área → Supervisor → **422 `REPORTS_TO_NOT_ALLOWED`**; jefe de otro hotel → **422 `SUPERVISOR_NOT_IN_HOTEL`**.
- QA-CH-10 🚫 `hugo@` (BD) o `supervisor-xcaret@` llaman `GET /hotel-users` o `PATCH /hotels/:id/users/:userId` → **403** (`users:manage_hotel` es solo del Administrador; el BDC solo crea).
- QA-CH-11 ✅ En **Personal Oranje** (la pestaña de siempre) no aparece ningún rol de hotel ni Colaborador, y el alta de personal sigue rechazando roles de hotel hacia su pestaña (`USE_HOTEL_USERS`).
- QA-CH-12 ✅ `admin@` ya puede listar hoteles (`GET /hotels`) para el selector; `GET /hotels/:id` de un hotel ajeno sigue en **403** para un rol de hotel.

**Etapa B — Alcance por departamento (D-09)** — Supervisor y Manager de Área solo tocan su departamento; el Manager General, todo el hotel
- QA-DEP-01 ✅ `supervisor-xcaret@` → Crear requisición: el departamento aparece **fijo en Housekeeping** (solo lectura, con la nota de que las de otro departamento las crea su Manager de Área o el General); las posiciones son las de Housekeeping; la requisición se crea.
- QA-DEP-02 ✅ `mgr-general-xcaret@` → Crear requisición: **elige** departamento; los que no tienen posiciones en el catálogo se ven apagados con «· sin posiciones en el catálogo» y no se pueden elegir.
- QA-DEP-03 🚫 Por API, `supervisor-xcaret@` crea con una posición de Alimentos → **403 `DEPARTMENT_OUT_OF_SCOPE`** y el mensaje dice «Solo puedes pedir posiciones de tu departamento», **no** «tu rol no puede crear».
- QA-DEP-04 ✅ Cola de autorización de `mgr-area-xcaret@`: solo requisiciones de Housekeeping; autoriza una → pasa a Autorizada.
- QA-DEP-05 🚫 `mgr-area-xcaret@` autoriza por API (`POST /requisitions/:id/authorize`) una requisición de Alimentos del mismo hotel → **403 `DEPARTMENT_OUT_OF_SCOPE`**; abrirla por URL (`/requisiciones/:id`) → la ficha dice que es de otro departamento, no «reintenta».
- QA-DEP-06 ✅ La misma requisición la autoriza el Manager General de ese hotel (respaldo D-09).
- QA-DEP-07 ✅ Schedule: `supervisor-xcaret@` y `mgr-area-xcaret@` ven solo turnos de Housekeeping (`GET /schedules/:id/entries`); el Manager General ve los de todo el hotel.
- QA-DEP-08 🚫 `mgr-area-xcaret@` planea un turno (`POST /schedules/:id/entries`) sobre una asignación de otro departamento → **403 `DEPARTMENT_OUT_OF_SCOPE`**; quitar uno ajeno → **403**; los propios sí.
- QA-DEP-09 ✅ Regresión: QA-H2-02/H2-03 (aprobar horas por departamento) y el eliminar de requisiciones por departamento siguen igual.

**Etapa C — Catálogos: departamentos y posiciones en una vista** (Administrador)
- QA-CAT-01 ✅ `/catalogos`: **Posiciones ya no es pestaña**; «Departamentos y posiciones» muestra cada departamento como sección con su conteo; el enlace viejo `?tab=positions` cae en la fusionada.
- QA-CAT-02 ✅ «Agregar posición» desde una sección abre el alta con ese departamento **ya elegido**; la posición nueva aparece en su sección y se ofrece en el alta de requisición de ese departamento.
- QA-CAT-03 ✅ La búsqueda entra por los dos lados: escribir el nombre de un departamento muestra su sección completa; escribir el de una posición muestra solo esa dentro de su sección.
- QA-CAT-04 ✅ Un departamento sin posiciones muestra su vacío con el porqué («mientras no tenga, nadie puede pedir personal de este departamento»); en el alta de requisición ese departamento se ve apagado (QA-DEP-02).
- QA-CAT-05 🚫 Eliminar un departamento con posiciones o una posición en uso → **409 `CATALOG_IN_USE`** con mensaje en español; renombrar sí.

**Etapa D — Sesión en staging** (`oranjeapp-gcp.web.app` contra el API en Cloud Run)
- QA-SES-01 ✅ Login → recargar la página (F5) en cualquier ruta: **sigue dentro**, en la misma ruta, sin pasar por `/login`. En DevTools → Application → Cookies del API: `oranje_refresh` con `SameSite=None`, `Secure`, `HttpOnly`, expira en 7 días.
- QA-SES-02 ✅ Cerrar la pestaña, abrir otra y entrar a la app → sigue dentro (la cookie es de 7 días, no de sesión del navegador).
- QA-SES-03 ✅ Dejar la app abierta más de 15 min y hacer una acción → sigue funcionando sin pedir login (el access token se renueva solo con un `POST /auth/refresh` → 200).
- QA-SES-04 ✅ Cerrar sesión → recargar → **sí** va a `/login` (la cookie se borró).
- QA-SES-05 ✅ En local (`localhost:5173`) todo lo anterior sigue igual con la cookie `Strict` (mismo sitio por el proxy de Vite).
- QA-SES-06 ✅ Entrar a la app mientras corren las suites de integración del API en dev: la app carga y no aparece la pantalla de error (antes: `Too many database connections`; la instancia pasó de 25 a 40 conexiones y los pools locales bajaron a 3).
- QA-SES-07 📋 Ruido conocido, no bug: el Administrador recibe **403 en `GET /notifications`** (su rol no tiene `system:receive_notification`); la campana lo tolera y no rompe nada.

**Etapa E — Contadores honestos** (cambio de la otra sesión en el #39)
- QA-CNT-01 ✅ Con más de 100 filas reales, los contadores de Cartera de Clientes, Mi Equipo, Mi Territorio, Requisiciones, Autorización, Propuestas, Self-Pick y Mi Personal coinciden con la base (`meta.total`), no con «cuántas cupieron en una página».

> [!note] Automatización
> A, B y C ya tienen su red en código: `hotel-users.spec.ts` (7), `schedule-departamento.spec.ts`,
> `requisicion-eliminar.spec.ts` (autorizar por departamento) y `UsersPage.spec.tsx` (10). Lo que
> este plan agrega es el recorrido humano por UI y las negativas por API con tokens reales.
> D no se automatiza en CI (necesita el dominio de Hosting): es un recorrido manual en staging
> después de cada deploy del API que toque `auth`.

### Alta de requisición al reabrir, calendario en diálogo y login del Colaborador (cambio 2026-09-10)

Tres correcciones del front en la misma tanda (sin PR todavía; probar en local o
cuando lleguen a staging). Usuarios de prueba: `supervisor-xcaret@` (Hotel Xcaret,
**Housekeeping**), `mgr-general-xcaret@` (elige departamento), `reclutadora@`
(Rocío, la que llena los slots) y `colaborador@` (Juan, ROL-C-01). Contraseñas en
`~/Desktop/oranje-credenciales/credenciales.md`.

**Etapa A — El departamento del paso 1 baja a las posiciones, también al reabrir**
- QA-REQ-01 ✅ `supervisor-xcaret@` → Crear requisición → Continuar (departamento fijo en Housekeeping) → llenar la posición → Continuar → **Cancelar**. Volver a Crear requisición y repetir: el paso 2 **no** dice «Falta el departamento»; se llega a la revisión y se guarda. Antes fallaba justo en el segundo pedido del día.
- QA-REQ-02 ✅ Con «Agregar otra posición», cada fila nueva nace con el departamento; la revisión (paso 3) muestra el departamento una sola vez, arriba de las posiciones.
- QA-REQ-03 ✅ `mgr-general-xcaret@` elige Housekeeping, avanza, regresa con **Atrás** y cambia a Alimentos: las posiciones de la fila se limpian y el selector ofrece solo las de Alimentos; al guardar, la requisición queda en Alimentos.
- QA-REQ-04 ✅ Lo guardado llega a quien llena los slots: `reclutadora@` → Self-Pick ve la requisición **solo después de autorizada** (Manager de Área o General), con «N slots libres», y asigna colaborador slot por slot en `/self-pick/:requisición/:posición`; la ficha de la requisición muestra el slot como ocupado y a quién.

**Etapa B — Calendario de shadcn en el alta (Popover + Calendar, `DateField`)**
- QA-CAL-01 ✅ En el paso 2, clic en **Inicio**: abre un calendario en el idioma activo (español: «septiembre 2026», semana empieza en lunes) **y se queda abierto** hasta elegir un día o hacer clic fuera. Antes el selector nativo se cerraba solo dentro del diálogo.
- QA-CAL-02 ✅ Elegir un día: el calendario se cierra, el campo muestra la fecha legible («18 sep 2026»; en inglés «Sep 18, 2026») y el diálogo sigue abierto en el paso 2. La requisición se guarda con esa fecha de inicio (`start_date`).
- QA-CAL-03 ✅ Sin fecha, Continuar marca el campo en rojo con «Falta la fecha de inicio»; con el campo marcado, el borde del botón de fecha se ve en rojo (aria-invalid).
- QA-CAL-04 ✅ Teclado: Tab llega al campo, Enter o Espacio abren el calendario, flechas mueven el día, Enter elige, Esc cierra el calendario **sin** cerrar el diálogo.
- QA-CAL-05 ✅ Cambiar el idioma a English desde el sidebar y volver a abrir: el calendario sale en inglés (meses y días) y la fecha elegida se relee en inglés.
- QA-CAL-06 📋 Pendiente conocido: los otros cinco diálogos con fecha (turno del Schedule, contrato T&C, alta de colaborador, asignación de slot, marca manual) siguen con el selector nativo y pueden reproducir el cierre solo. Se cambian al mismo campo en la siguiente tanda.

**Etapa C — Login del Colaborador (`/colaborador/login`)**
- QA-LOG-01 ✅ Abrir `/colaborador/login` sin sesión: misma tarjeta con «Entra a Oranje», «Tu turno, tu ponche y tus datos, en tu celular», el aviso «Tu Reclutadora te da de alta…» y el enlace «¿Trabajas en Oranje o en un hotel? Entra por aquí» → lleva a `/login`.
- QA-LOG-02 ✅ `/login` conserva sus textos de staff y gana el enlace «¿Eres colaborador? Entra por aquí» → `/colaborador/login`.
- QA-LOG-03 ✅ Sin sesión, abrir `/collaborator` o `/colaborador/ponchar` → redirige a **`/colaborador/login`** (no a `/login`); tras entrar con `colaborador@`, regresa a la ruta que pedía.
- QA-LOG-04 ✅ `colaborador@` entra por `/login` (la puerta del staff) → aterriza igual en `/collaborator`; `supervisor-xcaret@` entra por `/colaborador/login` → aterriza en su Dashboard. La puerta no decide el destino, el rol sí.
- QA-LOG-05 ✅ Cerrar sesión desde el menú del avatar del Colaborador → vuelve a `/colaborador/login` con sus textos, no a la puerta del staff.
- QA-LOG-06 ✅ En 390 px: la tarjeta ocupa el ancho, el collage se oculta, los campos abren el teclado de correo (`type=email`, `autocomplete=email`) y el navegador ofrece autollenar la contraseña.
- QA-LOG-07 ✅ En inglés (navegador en `en` o interruptor al pie): «Sign in to Oranje», «Your shift, your punches and your details, on your phone.», «No access yet? Your Recruiter sets you up…», «Do you work at Oranje or at a hotel? Sign in here».
- QA-LOG-08 🚫 Credenciales malas en `/colaborador/login` → el mismo mensaje único que en el staff («El correo o la contraseña no coinciden…»); no se revela qué mitad falló.

> [!note] Automatización
> A tiene red en código: `NewRequisitionDialog.department.spec.tsx` (reapertura con el
> departamento fijado por la sesión) y `NewRequisitionDialog.spec.tsx` ya eligen el día en el
> calendario nuevo. C no tiene spec propio todavía: es recorrido manual, verificado con
> Playwright en local el 2026-09-10.

### Alta de prospecto, semáforo del BDC y alta de usuario en staging (cambio 2026-09-11)

Reportes de Hugo probando staging. Usuarios: `bd@` (BD), `bdc@` (BDC), `admin@`.
El cuarto bloque es un pendiente de infraestructura, no del front.

**Etapa A — Alta de prospecto: el hotel se escribe una sola vez**
- QA-PRO-01 ✅ `bd@` → Pipeline → Nuevo prospecto → paso 1 «El edificio»: lo primero es **Busca el hotel en Google**; al elegir uno, nombre, dirección, teléfono, pin y foto se llenan solos y el campo «Nombre del hotel» queda editable con la nota «si lo llenó Google, puedes corregirlo».
- QA-PRO-02 ✅ Un hotel que no está en Google: se escribe el nombre a mano en el paso 1; en el paso 2 el mapa pide marcar el punto y «Continuar» no avanza sin pin (mensaje «Falta la ubicación» o equivalente en rojo).
- QA-PRO-03 ✅ Paso 2 «Ubicación» ya **no tiene buscador**: solo el mapa y la geocerca, con el texto «Sigue siendo {hotel}. Arrastra el mapa para dejar el pin en la entrada…». Arrastrar el mapa mueve el pin, el resumen dice «Pin ajustado a mano» y el nombre del hotel no cambia.
- QA-PRO-04 ✅ Con «Hotel ya registrado» el buscador de Google no aparece (el hotel ya tiene sus datos) y el nombre viene bloqueado.
- QA-PRO-05 ✅ **Ningún modal se cierra solo**: en el paso 3, doble clic rápido en «Continuar» → se llega al paso 4 con «Crear prospecto» apagado unos instantes y **no se crea nada** (cero `POST /prospects`); el botón se habilita solo y el diálogo sigue abierto hasta que la persona lo pulsa o cancela.
- QA-PRO-06 ✅ Enter dentro de un campo de los pasos 1–3 avanza de paso, nunca guarda; en el paso 4, Enter en «Qué necesita» **no guarda nunca**: el prospecto se crea solo con el botón «Crear prospecto».

**Etapa B — Quién mueve el Semáforo Onboarding** ([[Semáforo Onboarding]])
- QA-SEM-01 ✅ `bdc@` abre un prospecto en Gris, Azul claro, Verde o Amarillo → «Cambiar estado»: el diálogo no ofrece pasos y dice **quién sí** («Desde Gris tu rol no mueve el prospecto: lo mueve el BD»). No es un error: los primeros estados son del BD.
- QA-SEM-02 ✅ `bdc@` en Verde ve un único paso: Café (renegociación), con motivo obligatorio.
- QA-SEM-03 ✅ `bdc@` en Rosa: el diálogo explica que la conversión a Naranja se aprueba desde Conversión (QA-BDC-02); en Naranja puede pausar a Negro; en Café y Negro puede reactivar a Azul claro.
- QA-SEM-04 🚫 Por API, `bdc@` intenta `POST /prospects/:id/transitions` de Gris a Azul claro → **403/422** (la transición no está sembrada para su rol).

**Etapa C — Alta de usuario en staging (pendiente de infraestructura, no del front)**
- QA-USR-01 ✅ `admin@` → Usuarios → Crear en **staging**: responde 201, el panel dice «Invitación enviada a: correo» y la fila aparece con «Invitación enviada». (Hasta el 2026-09-11 respondía 503 «Firebase no respondió»: a Cloud Run le faltaba `FIREBASE_PROJECT_ID` y a `oranje-api@` el rol de Firebase Auth; corregido con el PR #46.)
- QA-USR-02 ✅ La persona recibe el correo de Firebase, establece su contraseña y entra; su fila cambia a «Ya entró».
- QA-USR-03 ✅ Cuentas del hotel en staging: `admin@` → Usuarios → Personal de hoteles → alta de un Manager General → la fila nace con «Invitación enviada» y el correo llega; «Reenviar invitación» responde 200. (Las creadas antes del PR #46 nacieron sin correo: reenviarles la invitación desde la fila lo resuelve.)

**Etapa D — Inspección entra a lo suyo**
- QA-INS-01 ✅ `inspector@` inicia sesión → aterriza en **Accidentes** (no en el dashboard de Ventas) y el menú lateral muestra **solo Accidentes**; ningún 403 salvo el ruido conocido de notificaciones.
- QA-INS-02 ✅ `coordinador-inspeccion@` igual: solo Accidentes. Mientras el módulo esté «en construcción», la pantalla lo dice; no hay otra cosa que el rol pueda hacer todavía (cero permisos sembrados).
- QA-INS-03 ✅ `admin@` → Usuarios → Crear con rol Inspector: «Reporta a» ofrece solo Coordinadores de Inspección; con rol Coordinador, solo al Administrador. Nunca Managers de hotel ([[Inspección/Inspector|Inspector]] reporta al Coordinador).

> [!note] Automatización
> A tiene red en `ProspectFormDialog.spec.tsx` (el buscador solo en el paso 1 y el botón de guardar
> armado); B en `ChangeStatusDialog` por mocks. C se prueba a mano en staging tras el deploy.

### Ponche del Colaborador: 15 minutos entre marcas, orden y éxito en verde (cambio 2026-09-12)

Usuario: `jdavid@oranjepeople.com` (Juan David, asignado en Fairfield con turno planeado; ver `credenciales.txt`).
La regla nueva está en [[Core/Módulos/Reglas de Negocio#Mecanismo de ponchado|Reglas de Negocio · Mecanismo de ponchado]].

- QA-PON-01 ✅ Tras ponchar la Entrada, la pantalla Ponchar deja el botón apagado **sin decir desde qué hora** (la espera no es un cronómetro); a los 15 minutos se habilita solo, sin recargar. Igual entre cada par de marcas.
- QA-PON-02 🚫 Por API, `POST /punches` con la siguiente marca antes de 15 minutos → **422 `PUNCH_TOO_SOON`** «Aún no puedes registrar la siguiente marca», sin hora ni cuenta regresiva; la app lo muestra igual. La primera marca del día no tiene espera.
- QA-PON-03 ✅ Cada marca se registra una sola vez por jornada y en orden (Entrada → Salida a lunch → Regreso de lunch → Salida); repetir una responde **409 `PUNCH_ALREADY_REGISTERED`** y la app no la ofrece. Al completar las cuatro, «Jornada completa».
- QA-PON-04 ✅ El Lottie de éxito (Entrada y Salida) es **verde** (`--green`); los de «registrando» y «verificando» siguen en naranja: el éxito se distingue del proceso.
- QA-PON-05 ✅ En un hotel con método QR, la marca pide escanear el código impreso; con el QR vigente se registra y guarda su versión; con uno viejo → **422 `QR_INVALID`**.
- QA-PON-06 ✅ La hoja imprimible del QR (ficha del hotel → Ponche → Imprimir) lleva una **liga** a `/colaborador/ponchar?qr=…` con el dominio del ambiente: escaneada con la cámara del teléfono abre la app en Ponchar (login del Colaborador si no hay sesión) y el botón dice «con el QR del acceso ya leído»; al tocar Ponchar se registra sin abrir el lector.
- QA-PON-07 ✅ El lector dentro de la app acepta la liga nueva y también una hoja vieja con el código pelón. Si el QR de la liga ya fue regenerado → **422 `QR_INVALID`** y el siguiente toque abre el lector para escanear la hoja vigente.

### La propuesta se cotiza por puesto, se imprime y se manda por correo (cambio 2026-09-14)

Regla en [[Core/Módulos/Reglas de Negocio#Propuesta Personalizada|Reglas de Negocio · Propuesta Personalizada]]. Cuentas: `bd@` y `bdc@`; el hotel debe estar en Verde o Café.

- QA-PRP-01 ✅ El editor pide un renglón por puesto (puesto del catálogo, pay y bill). «Agregar puesto» suma renglones y el basurero los quita.
- QA-PRP-02 🚫 Un renglón con bill por debajo del pay no deja guardar ni enviar, y lo dice en el renglón; por API, **422 `RATE_MARGIN_NEGATIVE`**. Bill igual a pay sí pasa (margen cero es válido).
- QA-PRP-03 🚫 Dos renglones del mismo puesto → el formulario lo marca; por API, **422 `RATE_DUPLICATED`**. Una tarifa en cero → **422 `RATE_NOT_POSITIVE`**.
- QA-PRP-04 🚫 Enviar sin ningún puesto cotizado → **422 `PROPOSAL_WITHOUT_RATES`**.
- QA-PRP-05 ✅ Guardar reemplaza el cuadro completo (quitar, cambiar y agregar en la misma edición quedan como se dejaron). Un PATCH que solo manda la nota de servicios **no** borra el cuadro.
- QA-PRP-06 ✅ Al abrir una versión nueva, el cuadro llega con los renglones de la anterior.
- QA-PRP-07 ✅ Sin puestos en el catálogo, el editor lo dice y remite al Administrador en Catálogos · Departamentos y posiciones, en vez de mostrar un selector vacío.
- QA-PRP-08 ✅ El icono de PDF abre la vista previa con el **acuerdo de servicio completo**: encabezado con las dos partes, las veinte cláusulas (Term, Duties, Service Fee, Early Termination, Restricted Activities, Proprietary Rights… hasta Entire Agreement), Notices, y el Exhibit «A» con el texto fijo y el cuadro de tarifas. «Imprimir o guardar como PDF» produce el documento entero, no solo lo que cabe en pantalla, y el Exhibit arranca en hoja nueva.
- QA-PRP-08b ✅ **Sale sin firmas**: las cuatro líneas (dos en el acuerdo, dos en el Exhibit) van en blanco, con el rótulo debajo. La app no firma ni guarda firmas.
- QA-PRP-09 ✅ «Enviar por correo» abre el cliente de correo de la persona con el destinatario (contacto principal del hotel), el asunto y el cuerpo con las tarifas ya escritos. El PDF lo adjunta quien envía: un `mailto` no lleva adjuntos. El semáforo lo sigue moviendo «Enviar propuesta», no el correo.
- QA-PRP-10 ✅ El historial resume cada versión como «N puestos · desde $X el bill»; las versiones anteriores al cuadro siguen mostrando su tarifa global.
- QA-PRP-11 ✅ En el alta de contrato, «Vigente desde» y «Vigente hasta» abren el calendario de shadcn dentro del diálogo y **no se cierra** al elegir el día; cada posición de tarifas va en su tarjeta con «Pago por hora» y «Factura por hora» etiquetados.

### Herencia por jerarquía: el jefe hace todo lo del subordinado (cambio 2026-09-14)

Regla en [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]]. Cuentas: `bdc@`, `lider-reclutadoras@`, `mgr-reclutamiento@`, `mgr-area-fairfield@`, `mgr-general-xcaret@`, `coordinador-inspeccion@` (ver `credenciales.txt`). Cada caso se prueba en pantalla y por API; la negativa (el subordinado NO gana lo del jefe) también por API.

- QA-HER-01 ✅ El **BDC** identifica un prospecto (Gris), lo perfila (Azul claro), registra intentos, abre y envía una Propuesta Personalizada y mueve el Semáforo Onboarding en los estados del BD (Gris, Azul claro, Verde, Amarillo, Rojo) desde el Pipeline y el editor de propuestas; el diálogo de estado ya no le dice «lo mueve el BD» sino «lo mueve el BD o el BDC».
- QA-HER-02 🚫 El **BD** sigue sin convertir (Rosa → Naranja), sin Café, Naranja ni Negro: por API **403** y el diálogo dice que es del BDC. La herencia no sube.
- QA-HER-03 ✅ El **Líder de Grupo** y el **Manager de Reclutamiento** ven Self-Pick en el menú, toman una requisición de la bandeja, se unen a una tomada, asignan al slot y validan un colaborador en Blanco (Verde fuerte); el expediente dice «lo valida Reclutamiento (la Reclutadora, su Líder de Grupo o el Manager)».
- QA-HER-04 📋 El cierre de cobertura (RF-05) no cambia con la herencia: la Reclutadora solicita, el Líder aprueba; el sistema sigue sin exigir esa aprobación (hueco de D-23 ya documentado en QA-R1-06).
- QA-HER-05 ✅ El **Manager de Área** de Fairfield revisa los ponches de su departamento, registra una marca manual, envía la semana a aprobación; el **Manager General** lo mismo en cualquier departamento.
- QA-HER-05b 🚫 **Auditorías queda fuera de la herencia**: el Manager de Área y el Manager General no ven «Auditorías» en el menú, `/auditorias` no les muestra nada, `GET/POST/PATCH /audits` responde **403**, y en Mi Personal no se les pinta «Presentación Personal» (el tablero carga igual). Solo el Supervisor audita, corrige y ve.
- QA-HER-06 🚫 El alcance no se hereda: el Manager de Área revisando o creando fuera de su departamento → **403 `DEPARTMENT_OUT_OF_SCOPE`**, igual que antes; el Supervisor sigue sin autorizar ni aprobar horas.
- QA-HER-07 ✅ El **Coordinador de Inspección** ve Accidentes, captura y da seguimiento médico como un Inspector.
- QA-HER-08 🚫 El Coordinador cierra un accidente **solo si es el Inspector asignado** a la tarjeta (D-32): en una tarjeta ajena → **403**, aunque tenga el permiso.
- QA-HER-09 🚫 Nada se hereda hacia Contabilidad ni del Colaborador: el Manager de Contabilidad sigue sin validar la nómina (firma de la Contadora) y ningún jefe puede ponchar por nadie (`POST /punches` resuelve el turno del token).
- QA-HER-10 ✅ Por base: tras sembrar, cada permiso de un subordinado existe también para su jefe (`identity.role_permission`), y cada transición sembrada para la Reclutadora, el Supervisor, el BD o el Inspector existe para su cadena; ninguna transición nueva aparece para el BD, la Reclutadora, el Supervisor o el Inspector.

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
- QA-AR-07 ✅ Foto del ponche: **obligatoria en Entrada y Salida** en hoteles con método **Selfie**, no en LUNCH ni en el ponche manual (D-32). En hoteles con método **QR** la evidencia es el código, no la foto (ver el E2E del ponche por QR).
- QA-AR-08 🚫 Evidencia de Blacklist: obligatoria cuando `source = MANUAL` (`ck_blacklist_evidence`); sin archivo, el veto no entra.
- QA-AR-09 ⚠ Límite de tamaño: archivo enorme → rechazo limpio con mensaje, nunca un 500. Documentar el límite real (hoy no está asentado en ningún estándar).
- QA-AR-10 🚫 Fotos de hotel NO se suben: el DTO ya no acepta `photoUrl` del cliente (D-34) — mandarla debe ignorarse/rechazarse; la foto la resuelve el servidor vía Places.
- QA-AR-11 📋 Pendientes conocidos de D-30 que las pruebas mantienen visibles: la **retención del bucket** no está definida, y el **objeto huérfano** (subir un archivo y nunca asociar su path a nada) no se limpia.

### cambio 2026-10-01 — el hotel invita a su gente y Oranje confirma la gerencia

Regla en [[Core/Módulos/Reglas de Negocio#Cuentas del hotel|Reglas de Negocio · Cuentas del hotel]].
Cada negativa se prueba **también por API**: el 403 tiene que venir del servidor,
no de un botón escondido.

**Quién invita y hasta dónde**
- QA-INV-01 Manager General de su hotel: invita un Supervisor de **su** hotel → entra **directo**, con invitación enviada.
- QA-INV-02 Manager de Área: invita un Supervisor de su hotel → directo. Intenta invitar en **otro** hotel (por API, cambiando el `hotelId`) → **403 `HOTEL_OUT_OF_SCOPE`**.
- QA-INV-03 **Supervisor**: no ve la tarjeta de invitar, y por API → **403**, porque su rol no invita.
- QA-INV-04 **BD**: invita en un hotel de **sus zonas** → directo. En un hotel fuera de sus zonas → **403 `HOTEL_OUT_OF_ZONE`**.
- QA-INV-05 **BDC** y **Administrador**: invitan en cualquier hotel, sin límite de zona.

**Lo gerencial propuesto por el hotel queda pendiente**
- QA-INV-06 Manager General invita un **Manager de Área** de su hotel → la cuenta nace **pendiente**, **inactiva**, y **no sale correo**.
- QA-INV-07 La misma alta hecha por el **Administrador** o el **BDC** → entra **directo**: Oranje no se aprueba a sí misma.
- QA-INV-08 Un **Supervisor** invitado por su gerente → directo, aunque lo proponga el hotel: el pendiente es solo para roles gerenciales.
- QA-INV-09 `admin@` y `bdc@` ven la **cola de pendientes** en su tablero; un rol sin `users:approve_hotel` no la ve **ni la consulta** (la pantalla no pide el endpoint).
- QA-INV-10 Al **confirmar**: la cuenta se activa, **ahí** sale la invitación por correo, y queda registrado quién confirmó. Confirmar **dos veces** no reenvía nada.
- QA-INV-11 Mientras está pendiente, la persona **no entra**: intentar iniciar sesión falla como cualquier cuenta inactiva.

**La excepción de la rotación**
- QA-INV-12 Manager de Área en un hotel **con** Manager General activo: proponer otro Manager General → **403 `HOTEL_HAS_GENERAL_MANAGER`**, con el mensaje que nombra a quién sí (él mismo, su BD o el Administrador).
- QA-INV-13 El mismo caso en un hotel **sin** Manager General activo → se puede proponer, y queda pendiente de confirmación.

**Herencia**
- QA-INV-14 `users:invite_hotel` y `users:approve_hotel` **no se heredan**: el Manager General no gana la regla de alcance del Supervisor, y el censo de excepciones del `herencia-jerarquia.spec` lista las cinco (las tres de Auditorías más estas dos).

**Notificaciones**
- QA-INV-15 El **Administrador** recibe el aviso de una cuenta pendiente — es el primer aviso que le llega, y no tiene hotel ni zona.
- QA-INV-16 **Tocar el aviso lleva a la acción**, no al inicio. La **foto** de quien lo provocó se ve, incluida la de un colaborador (vive en `personal.worker`, no en `identity.user`).

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
- [[Core/Módulos/Reglas de Negocio#Cuentas del hotel|Reglas de Negocio · Cuentas del hotel]] — la regla detrás de QA-CH-*
- [[Español/Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]] — D-09, D-18, D-22, D-23, D-27, D-32, D-33
- [[Español/Arquitecturas/_Globales/Estándares de Desarrollo|Estándares de Desarrollo]] — §8 pruebas y datos sin PII
- [[Español/Arquitecturas/_Globales/Roles del Sistema|Roles del Sistema]]
- [[Español/Arquitecturas/Ventas/_Globales del Depto/06 - Matriz de Permisos|Matriz de Permisos de Ventas]]
