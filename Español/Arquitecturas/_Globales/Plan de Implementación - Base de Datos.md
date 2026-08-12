---
tags:
  - arquitectura
  - global
  - ingenieria
  - plan
aliases:
  - Plan de Implementación
  - Plan de Base de Datos
  - Primera Migración
---

# Plan de Implementación — Base de Datos

Lista ordenada de tareas para llegar de la documentación a la primera base de datos corriendo. El alcance es **la primera migración**: los esquemas `catalogs`, `identity` y `commercial`, con Ventas como primer módulo funcional.

> [!info] Alcance
> Este documento es **qué hacer y en qué orden**. El *por qué* de cada decisión vive en [[Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]] — sobre todo **D-15**, que fija el orden de la primera migración. Las convenciones que cada tarea debe cumplir están en [[Estándares de Base de Datos]] y [[Estándares de Desarrollo]].

> [!important] El camino crítico no es Ventas
> Las 7 tablas de `commercial` están especificadas al detalle en `Ventas - Modelo de Datos v2.drawio`. Lo que **no** está especificado al mismo nivel son `catalogs` e `identity`, de los que Ventas depende por llave foránea. Las tareas 1, 9 y 10 son el cuello de botella real.

---

## Fase 0 · Decisiones que bloquean

Ninguna es técnica: son datos que el vault todavía no tiene y sin los que el seed no se puede escribir.

| # | Tarea | Qué bloquea | Dónde |
|---|---|---|---|
| 1 | **Unificar los IDs de rol** a `ROL-[X]-[NN]` y llenar `Roles del Sistema.md`, que hoy está **vacío** | Sin el catálogo de roles no se puede sembrar `identity.role`, y sin eso `status_light_transition.authorized_role_id` apunta a la nada. Reclutamiento usa `ROL-01…ROL-05` sin letra de departamento; Hotel y Ventas sí la usan | [[Estructura de Proyecto y Nomenclatura]] sección 9 |
| 2 | **Geocerca: punto + radio o polígono por hotel** | `hotel.coordinates` y `hotel.geofence_radius_m` salen en la migración #1. Cambiar la forma después es cambiar el tipo de una columna con PostGIS | [[Arquitecturas/_Globales/Modelo de Datos\|Modelo de Datos]] sección 11 |
| 3 | **Módulo del expediente del Colaborador** — `coverage/` o un `personal/` propio | No bloquea la #1, pero define si el proyecto tiene 10 u 11 esquemas, y eso se declara en `schema.prisma` desde el principio | [[Estructura de Proyecto y Nomenclatura]] sección 3 |

---

## Fase 1 · Infraestructura

| # | Tarea | Estándar que aplica |
|---|---|---|
| 4 | Monorepo **pnpm workspaces** con `apps/` y `packages/`, `tsconfig.base.json`, ESLint y Prettier compartidos en `packages/config`, Husky + lint-staged + commitlint | [[Estructura de Proyecto y Nomenclatura]] sección 2 y sección 6 · [[Estándares de Desarrollo]] sección 1 y sección 9 |
| 5 | **NestJS** en `apps/api` con los **10 módulos de D-01** como carpetas vacías, `common/`, `config/` e `infra/` | [[Estructura de Proyecto y Nomenclatura]] sección 3 |
| 6 | Proyecto GCP `oranje-dev` + instancia **Cloud SQL Postgres** | **D-06** — un proyecto por ambiente |
| 7 | Extensiones en `public`: `postgis`, `pgcrypto`, `btree_gist` | [[Estándares de Base de Datos]] sección 1 |
| 8 | **Prisma** con `multiSchema` y los esquemas declarados. Ya es GA desde la 6.13.0: no lleva flag de preview | **D-01** · [[Estándares de Base de Datos]] sección 1 |
| 9 | Librería de **uuid v7** en la aplicación | [[Estándares de Base de Datos]] sección 4 — Postgres no genera v7, y `gen_random_uuid()` devuelve v4 |
| 10 | Validación de variables de entorno con **Zod al arranque**: si falta una, la app no levanta | [[Estándares de Desarrollo]] sección 9 |

> [!warning] El código no vive en este vault
> `oranje-matrix-system` es documentación. La sección 10 de [[Estándares de Desarrollo]] es explícita: *"la documentación de negocio no se duplica en el repo de código"*. El monorepo va en su propio repositorio.

---

## Fase 2 · Migración #1 — el esquema

El orden importa: las llaves foráneas cruzan los tres esquemas.

```
catalogs  →  identity  →  commercial
```

| # | Tarea | Detalle |
|---|---|---|
| 11 | **Completar y crear `catalogs`** | Solo lo que Ventas necesita: `status_light`, `status_light_state`, `status_light_transition`, `status_change_reason`, `zone` y `hotel_department`. **`position`, `english_level` y `hiring_modality` NO van aquí** — son de la Requisición y del Colaborador, y llegan con el diagrama de su departamento |
| 12 | **Completar y crear `identity`** | `role`, `user` (con `hotel_id` + `department_id` nulable), `role_permission`. El diseño del alcance de permisos está en [[Arquitecturas/_Globales/Modelo de Datos\|Modelo de Datos]] sección 9 |
| 13 | **Crear `commercial`** | Las 7 tablas de `Ventas - Modelo de Datos v2.drawio`: `hotel`, `hotel_contact`, `prospect`, `prospect_state_history`, `contact_attempt`, `proposal`, `user_zone` |
| 14 | **`journal.journal` particionado por mes** | Desde esta migración, no después: retrofitearlo exige reescribir la tabla con la aplicación detenida ([[Estándares de Base de Datos]] sección 9) |
| 15 | Las **9 restricciones** | Página 3 del diagrama v2: índices únicos parciales, FK compuestas, CHECK de coherencia de fila |
| 16 | Los **índices** | Cada uno defendible con una consulta concreta ([[Estándares de Base de Datos]] sección 7) |
| 17 | Trigger de `updated_at` | Lo mantiene la base, no la aplicación ([[Estándares de Base de Datos]] sección 4) |
| 18 | La vista `commercial.vw_client` | Filtra por los estados ORANGE y BLACK del ciclo abierto |

> [!warning] El diagrama es la especificación, no el punto de partida
> El 2026-08-10 se crearon `position`, `english_level` y `hiring_modality` derivándolas del vault en vez del diagrama de Ventas, y se borraron en la migración `quita_catalogos_fuera_del_esquema`. Ninguna FK les apuntaba.
>
> **Regla:** si al implementar parece que falta una tabla, se pregunta antes de crearla. Una tabla que no está en el diagrama hace que el esquema real deje de coincidir con el documento que el equipo revisa, y nadie se entera hasta que alguien abre la base con un cliente.

---

## Fase 3 · Seed

Sin esto el esquema existe y el negocio no funciona: **las reglas del semáforo son filas, no código.**

| # | Tarea | Fuente |
|---|---|---|
| 19 | Los **7 semáforos** como filas de `status_light` | [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforos]] |
| 20 | Los **9 estados** y las **12 transiciones** del Onboarding, con su rol autorizado y su `requires_reason` | `Ventas/Semáforo Onboarding y Base de Datos.drawio`, página 3 |
| 21 | `status_change_reason` | `09 - Dependencias` de Ventas |
| 22 | Los catálogos que Ventas usa: [[Zonas]] y [[Departamentos del Hotel]]. [[Posiciones]], [[Niveles de Inglés]] y [[Modalidades de Contratación]] se siembran con su departamento | `Core/Catálogos/` |
| 23 | La **Matriz de Permisos de Ventas** en `role_permission` | `06 - Matriz de Permisos` de Ventas |
| 24 | Un BD y un BDC de prueba | **Sin PII real**, en ningún ambiente que no sea producción ([[Estándares de Desarrollo]] sección 8) |

> [!important]
> Sin las transiciones sembradas, el servicio de transición devuelve `409` a todo y el semáforo no camina. El seed no es un extra del final: es parte de la migración.

---

## Fase 4 · Verificar que las reglas muerden

El principio rector de [[Estándares de Base de Datos]] es que toda regla que pueda vivir como restricción, vive ahí. Esta fase comprueba que de verdad vive ahí.

| # | Tarea |
|---|---|
| 25 | Tests que **intenten violar** cada restricción y esperen el error del motor: dos ciclos abiertos para el mismo hotel, un intento que cite el contacto de otro hotel, un prospecto tomando un estado de otro semáforo, dos contactos principales |
| 26 | El **servicio de transición** con sus 5 pasos y sus 3 errores: `409` si la transición no existe, `403` si el rol no coincide, `422` si falta el motivo |
| 27 | `REVOKE UPDATE, DELETE ON journal.journal FROM app_user` — RR-16 |
| 28 | Los 3 usuarios de base de datos: `app_user` (solo DML), `migrator` (DDL, solo en despliegue), `analytics_ro` (solo lectura) |

---

## Lo que este plan deja fuera a propósito

| Qué | Por qué |
|---|---|
| `commercial.terms_and_conditions` | Sus campos son los de `commercial.contract` + `contract_rate`: es su borrador. Se modela cuando se diseñe el Contrato. Consecuencia asumida: **el estado ROSA no tiene fila que lo respalde** — el estado sí se trackea en `prospect_state_history`, los términos negociados no |
| `commercial.proposal_rate` | Hoy la propuesta lleva un par de tarifas tentativas globales. Cuando el negocio cotice por posición, la tabla hija se agrega sin tocar `proposal`: migración aditiva |
| Los otros 6 semáforos | Cada uno necesita su tabla de historia (**D-14**). Solo existe la de `prospect` |
| El resto de los esquemas | `demand`, `coverage`, `operations`, `settlement`, `supervision`, `notifications` llegan con sus módulos |

---

## Relacionado

- [[Arquitecturas/_Globales/Decisiones de Arquitectura|Decisiones de Arquitectura]] — **D-15** el orden de la migración · **D-14** la historia de los semáforos · **D-13** el ciclo comercial
- [[Arquitecturas/_Globales/Modelo de Datos|Modelo de Datos]] — entidades, relaciones e invariantes
- [[Estándares de Base de Datos]] — nomenclatura, tipos, restricciones, índices, migraciones
- [[Estándares de Desarrollo]] — Git, PRs, testing, ambientes y Definition of Done
- [[Estructura de Proyecto y Nomenclatura]] — monorepo, módulos y glosario canónico
- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] — fuente de verdad del negocio
