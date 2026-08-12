---
tags:
  - arquitectura
  - globales
aliases:
  - Árbol de Reglas de Negocio
  - Mapa de Reglas por Departamento
  - Árbol RR
---

# Árbol de Reglas de Negocio — Arquitecturas

> [!info] Alcance
> Vista consolidada de lo documentado en `Español/Arquitecturas/`: qué tiene cada departamento, qué reglas `RR` lo rigen y cómo se conecta con los demás. Construido a partir de `01 - Portada` (roles), `04 - Por Módulo` (módulos), `07 - Reglas de Negocio` (RR) y `09 - Dependencias` (conexiones) de cada departamento.

> [!warning] Esto no es fuente de verdad
> La fuente de verdad del negocio es [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]]. Este árbol es un índice de navegación: si algo contradice a la fuente de verdad, gana la fuente de verdad.

---

## 1. Árbol del sistema

El sistema tiene **un solo eje temporal**: un hotel entra por Ventas, se habilita como cliente, genera requisiciones, Reclutamiento las cubre con colaboradores, e Inspección vigila al colaborador ya en piso. QA observa a todos sin operar.

```
ORANJE MATRIX SYSTEM
│
├── VENTAS ─────────────── Convierte prospecto en cliente
│   │                      RR-V-01 … RR-V-15
│   │                      Roles: BD (ROL-V-01) · BDC (ROL-V-02)
│   │
│   └──[ status Naranja del Semáforo Onboarding ]──► habilita al hotel
│
├── HOTEL ──────────────── Genera y autoriza la demanda de personal
│   │                      RR-H-01 … RR-H-20
│   │                      Roles: Supervisor (ROL-H-01) · Manager de Área (ROL-H-02) · Manager General (ROL-H-03)
│   │
│   └──[ requisición Autorizada ]──► publica la demanda
│
├── RECLUTAMIENTO ──────── Cubre la demanda con colaboradores
│   │                      RR-01 … RR-16
│   │                      Roles: Reclutadora (ROL-01) · Líder de Grupo (ROL-02) · Manager (ROL-03)
│   │
│   └──[ colaborador asignado a posición ]──► entra a operación
│
├── INSPECCIÓN ─────────── Vigila al colaborador en piso (transversal)
│   │                      Sin RR propias documentadas en Arquitecturas
│   │                      Rol: Inspector
│   │
│   └──[ resuelve Rojo · cierra Gris · ejecuta veto Negro ]
│
├── COLABORADOR ────────── Entidad central; usuario final de la app
│                          ROL-C-01 · sin RR propias (las hereda de Reclutamiento y Hotel)
│
└── TRANSVERSALES ──────── Sistema (automatización) · Administrador (en pausa) · QA (observa, no opera)
```

Los tres departamentos con arquitectura completa reservan siempre dos IDs no-persona: **Sistema** (automatización) y **Administrador** (en pausa hasta que se estabilicen las reglas).

---

## 2. Qué tiene cada departamento

### 2.1 Ventas — onboarding comercial del hotel

Dueño del hotel **antes** de que sea cliente. Su columna vertebral es el [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]].

```
VENTAS
│
├── Roles
│   ├── ROL-V-01  Business Developer (BD) ......... ejecutor en campo, por territorio
│   ├── ROL-V-02  Business Developer Coordinator ... supervisa rutas; único que aprueba
│   ├── ROL-V-03  Sistema ......................... Trigger Automático de Conversión
│   └── ROL-V-04  Administrador ................... en pausa
│
├── Módulos del sidebar
│   ├── Dashboard · Pipeline (Prospectos) · Propuestas · Documentos T&C
│   ├── Conversión ....... exclusivo BDC
│   ├── Mi Territorio .... exclusivo BD
│   ├── Mi Equipo ........ exclusivo BDC
│   ├── Reportes ......... exclusivo BDC
│   └── Clientes Activos
│
└── Reglas de Negocio  →  [[Arquitecturas/Ventas/_Globales del Depto/07 - Reglas de Negocio|07 - Reglas de Negocio (Ventas)]]
    │
    ├── Quién decide
    │   ├── RR-V-01  Conversión exclusiva del BDC (Rosa → Naranja); el BD nunca aprueba
    │   ├── RR-V-04  Desbloqueo de estancamiento (Café) exclusivo del BDC
    │   ├── RR-V-05  Gestión de cliente pausado (Negro) exclusiva del BDC
    │   └── RR-V-06  Gestión de rechazo (Rojo) por el BD
    │
    ├── Precondiciones y artefactos
    │   ├── RR-V-02  Precondición de conversión — Usuario del Hotel creado
    │   ├── RR-V-09  Propuesta Personalizada se elabora solo en Verde
    │   ├── RR-V-10  T&C obligatorios: Pay rate, Bill rate, Overtime, Festivos, Calendario
    │   └── RR-V-15  Contrato resulta del cierre exitoso en Rosa
    │
    ├── Automatismos y trazabilidad
    │   ├── RR-V-03  Trigger Automático de Conversión (3 acciones en paralelo)
    │   ├── RR-V-07  Reactivar desde Rojo/Negro/Café siempre regresa a Azul Claro
    │   └── RR-V-11  Todo cambio de status con fecha, responsable y comentario
    │
    └── Frontera con otros deptos
        ├── RR-V-08  Naranja es el único status que habilita generar requisiciones
        ├── RR-V-12  Post-Naranja, BD y BDC quedan como referentes comerciales
        ├── RR-V-13  QA observa pero no opera
        └── RR-V-14  Escalamiento de Calidad a dirección
```

### 2.2 Hotel — origen de la demanda

Es el cliente operando dentro de la plataforma. Genera requisiciones, gestiona Schedule y Timesheet, y reporta incidencias del colaborador.

```
HOTEL
│
├── Roles
│   ├── ROL-H-01  Supervisor (SUP) ........ crea requisiciones; NO autoriza
│   ├── ROL-H-02  Manager de Área ......... = Gerente de Departamento; autoriza
│   ├── ROL-H-03  Manager General (GM) .... siempre existe; máxima autoridad del hotel
│   ├── ROL-H-04  Sistema ................. numeración, urgencia, Inspector, semáforos
│   └── ROL-H-05  Administrador ........... en pausa
│
├── Jerarquías soportadas (RR-H-13, RR-H-14)
│   ├── Simple ..... Manager General (opera también como Manager de Área) → Supervisor → Colaboradores
│   └── Extendida .. Manager General → Manager de Área (uno por depto) → Supervisor → Colaboradores
│
├── Módulos del sidebar
│   ├── Requisiciones · Schedule · Timesheet · Mi Personal · Accidentes Laborales · Dashboard
│   ├── Mi Equipo del Hotel ... exclusivo Manager General
│   └── Reportes .............. exclusivo Manager General
│
└── Reglas de Negocio  →  [[Arquitecturas/Hotel/_Globales del Depto/07 - Reglas de Negocio|07 - Reglas de Negocio (Hotel)]]
    │
    ├── Habilitación y acceso
    │   ├── RR-H-01  El hotel solo genera requisiciones en status Naranja
    │   └── RR-H-12  Acceso a requisiciones: GM, Manager de Área y Supervisor
    │
    ├── Requisición — autorización y ciclo de vida
    │   ├── RR-H-02  Autorización compartida: Manager de Área o Manager General (Supervisor bloqueado)
    │   ├── RR-H-03  Mínimo 1 posición registrada para autorizar
    │   ├── RR-H-04  Numeración automática AAAAMMDDHHMM + Homoclave
    │   ├── RR-H-07  Eliminación física de requisición sin posiciones (sin journal)
    │   ├── RR-H-08  Estado Morado transversal al eliminar (con journal por posición)
    │   └── RR-H-18  La posición no tiene fecha de fin; termina con Stand-by (Rosa)
    │
    ├── Automatismos al autorizar
    │   ├── RR-H-05  Cálculo de urgencia: >120h Verde fuerte · 72-120h Amarillo · <72h Rojo
    │   └── RR-H-06  Asignación automática de Inspector por zona
    │
    ├── Colaborador en piso — acciones compartidas por los 3 roles
    │   ├── RR-H-09  OBSOLETA — Generación de QR del Timesheet. El ponche ya no usa QR
    │   ├── RR-H-10  Reporte de colaborador (Rojo) → inicia investigación del Inspector
    │   ├── RR-H-11  Stand-by (Rosa) → sin Schedule ni Timesheet, no puede ponchar
    │   ├── RR-H-19  Reporte de accidentes (Escenario A y Escenario B)
    │   └── RR-H-20  Estado Gris (Accidentado) protege de la regla de 3 inasistencias
    │
    └── Tiempo y nómina
        ├── RR-H-15  Indicador de Lunch Extendido NO visible para el Hotel
        ├── RR-H-16  Deducción de Lunch: mínimo 30 min obligatorio
        └── RR-H-17  Jornada 8h · semana 40h bruto · 37.5h neto pagable
```

### 2.3 Reclutamiento — cobertura de la demanda

Opera bajo modelo **Self-Pick colaborativo**: nadie reparte requisiciones, se toman de la bandeja y varios reclutadores pueden trabajar la misma a la vez.

```
RECLUTAMIENTO
│
├── Roles
│   ├── ROL-01  Reclutadora ....................... recluta desde la requisición tomada
│   ├── ROL-02  Líder de Grupo de Reclutadoras .... opera como Reclutadora + supervisa grupo
│   ├── ROL-03  Manager de Reclutamiento .......... coordina; casos especiales y escalamientos
│   ├── ROL-04  Sistema
│   └── ROL-05  Administrador
│
├── Módulos del sidebar
│   ├── Dashboard · Reclutamiento · Requisición · Blacklist
│   ├── Mi Grupo ..... exclusivo Líder
│   ├── Reportes ..... Líder y Manager
│   ├── Mi Equipo .... exclusivo Manager
│   └── Incidencias .. exclusivo Manager
│
└── Reglas de Negocio  →  [[Arquitecturas/Reclutamiento/_Globales del Depto/07 - Reglas de Negocio|07 - Reglas de Negocio (Reclutamiento)]]
    │
    ├── Modelo Self-Pick colaborativo
    │   ├── RR-01  Las requisiciones autorizadas no se distribuyen: se toman libremente
    │   ├── RR-15  Varios reclutadores participantes por requisición; lock a nivel de POSICIÓN
    │   ├── RR-16  Historial inmutable con actor y timestamp por evento
    │   └── RR-10  Jerarquía: Manager → Líder de Grupo → Reclutadora
    │
    ├── Alta del colaborador
    │   ├── RR-06  Captura en 3 fases (entrevista · alta en app · datos de emergencia)
    │   ├── RR-07  Validación manual obligatoria tras la Fase 2
    │   └── RR-08  Habilitación de accesos automática en menos de 1 minuto
    │
    ├── Asignación — bloqueos duros
    │   ├── RR-02  Consulta obligatoria de Blacklist antes de asignar
    │   ├── RR-05  Exclusividad: un colaborador no puede estar activo en 2 hoteles
    │   └── RR-04  Cobertura 100% para poder cerrar como Cubierta
    │
    ├── Blacklist
    │   └── RR-03  Cualquier rol agrega (motivo + evidencia); veto Negro permanente;
    │              disputas las resuelve el Inspector de zona, no el Manager
    │
    └── Automatismos y control
        ├── RR-09  El Flujo de Reclutamiento es continuo (haya o no requisiciones)
        ├── RR-13  Auto-asignación del Inspector por zona
        ├── RR-14  Numeración automática de requisiciones
        ├── RR-12  Toda intervención excepcional del Manager va a log con motivo
        └── RR-11  Indicador de Lunch Extendido solo para roles internos de Oranje
```

### 2.4 Inspección y Colaborador — piezas transversales

Ambas tienen arquitectura en `Español/Arquitecturas/` pero **no** tienen `_Globales del Depto/`, por lo que no declaran reglas `RR` propias: heredan las de Hotel y Reclutamiento.

```
INSPECCIÓN  →  [[Arquitecturas/Inspección/00 - Arquitectura Inspector|00 - Arquitectura Inspector]]
│
└── Inspector (asignado por zona)
    ├── Verifica llegada Día 1 ................ Verde fuerte → Verde manzana
    ├── Entrega uniforme Día 3+ ............... Verde manzana → Azul claro
    ├── Investiga casos en estado Rojo ........ resuelve la disputa (RR-03)
    ├── Ejecuta entrada manual a Blacklist .... Rojo → Negro (permanente)
    ├── Reincorpora colaborador ............... Rojo → Verde fuerte
    ├── Cierra tarjeta de accidente ........... y gestiona Gris → Verde fuerte (alta médica)
    └── Consulta Indicador de Lunch Extendido . acceso exclusivo interno (RR-11, RR-H-15)

COLABORADOR (ROL-C-01)  →  [[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|00 - Arquitectura Colaborador]]
│
├── PRIMARIAS
│   ├── Onboarding ............ Fase 2 (alta en app) y Fase 3 (datos de emergencia) — RR-06
│   ├── Asistencia / Ponche ... 3 marcas por jornada con GPS y foto; aprueba el Manager de Área o el Manager General — RR-H-16
│   ├── Disponibilidad ......... toggle Amarillo (disponible voluntario)
│   └── Reportar Accidente ..... Escenario A del RR-H-19 → estado Gris (RR-H-20)
│
└── SECUNDARIAS
    └── Dashboard · Mi Schedule · Mi Timesheet · Mi Pago · Notificaciones · Mi Perfil
```

---

## 3. Cómo se conectan los departamentos

Cada conexión es un **handoff con disparador explícito** y una regla que lo gobierna. Ningún departamento pasa trabajo a otro por decisión informal.

| # | Origen | Destino | Disparador | Regla que lo gobierna |
|---|---|---|---|---|
| 1 | Ventas | Hotel | El hotel alcanza status **Naranja** en el Semáforo Onboarding | RR-V-01 · RR-V-08 · RR-H-01 |
| 2 | Ventas | Inspección | Al activarse el hotel, se asigna Inspector por zona | RR-H-06 |
| 3 | Ventas | Core / Contrato | Cierre exitoso en Rosa genera el Contrato desde el T&C validado | RR-V-10 · RR-V-15 |
| 4 | Hotel | Reclutamiento | Requisición **Autorizada** entra a la bandeja Self-Pick | RR-H-02 · RR-H-03 · RR-01 |
| 5 | Hotel | Inspección | Autorizar requisición asigna Inspector automáticamente por zona | RR-H-06 · RR-13 |
| 6 | Hotel | Inspección | Reporte de colaborador (**Rojo**) inicia investigación | RR-H-10 |
| 7 | Hotel | Inspección | Reporte de accidente (**Gris**) genera tarjeta y señal al Inspector | RR-H-19 · RR-H-20 |
| 8 | Reclutamiento | Hotel | Colaborador asignado a posición aparece en Schedule y Timesheet | RR-04 · RR-05 |
| 9 | Reclutamiento | Inspección | Disputa de Blacklist se escala al Inspector de zona | RR-03 |
| 10 | Reclutamiento | Ventas | Incidencia que afecta la relación comercial escala a BD/BDC | RR-V-12 |
| 11 | Hotel | Ventas | Escalamiento comercial del Manager General a BD/BDC | RR-V-12 |
| 12 | QA | Todos | Observa, mide y retroalimenta; nunca ejecuta operación | RR-V-13 |
| 13 | QA | Dirección | Indicador de Calidad en **Rojo** persistente escala a dirección | RR-V-14 |

### Vista de flujo

```
   VENTAS                HOTEL                RECLUTAMIENTO           INSPECCIÓN
     │                     │                        │                     │
  Prospecto                │                        │                     │
     │                     │                        │                     │
  [Naranja] ──────────────►│                        │                     │
     │                Requisición                   │                     │
     │                     │                        │                     │
     │              [Autorizada] ──────────────────►│                     │
     │                     │                   Self-Pick                  │
     │                     │                        │                     │
     │                     │◄─────────── Colaborador asignado             │
     │                     │                        │                     │
     │                     ├──── Rojo (reporte) ────┼────────────────────►│
     │                     ├──── Gris (accidente) ──┼────────────────────►│
     │                     │                        ├── disputa Blacklist►│
     │◄─── escalamiento ───┤                        │                     │
     │       comercial     │                        │                     │
     │                     │                        │                     │
                    ┌──────┴────────────────────────┴─────────────────────┴──┐
                    │  QA — observa, mide, retroalimenta. No opera. (RR-V-13) │
                    └────────────────────────────────────────────────────────┘
```

---

## 4. Lenguaje común entre departamentos

Los departamentos no se hablan directamente: se hablan a través de **semáforos y catálogos compartidos**. Un semáforo = una dimensión; un color = un significado dentro de ese semáforo.

| Semáforo | Quién lo mueve | Quién lo lee |
|---|---|---|
| [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | Ventas (BD y BDC) | Hotel — habilitación en Naranja |
| [[Semáforo de Requisición]] | Hotel (autoriza) · Reclutamiento (avanza) | Ambos + QA |
| [[Semáforo de Posiciones de la Requisición]] | Reclutamiento (asigna) | Hotel — cobertura |
| [[Semáforo de Urgencia de Requisición]] | Sistema (cálculo automático) | Reclutamiento — priorización |
| [[Semáforo del Colaborador]] | Hotel (Rojo, Rosa, Gris) · Inspección (resuelve) · Reclutamiento (alta) | Todos |
| [[Core/Módulos/Semáforos/Indicador de Cumplimiento del Timesheet\|Indicador de Cumplimiento del Timesheet]] | Sistema | Hotel · Contabilidad |
| [[Core/Módulos/Semáforos/Indicador de Calidad\|Indicador de Calidad]] | QA | Managers de cada depto · dirección |

**Catálogos compartidos:** [[Posiciones]] · [[Zonas]] (base de la asignación de Inspectores) · [[Modalidades de Contratación]] · [[Niveles de Inglés]] · [[Departamentos del Hotel]].

**Sistemas transversales:** App del Colaborador · App del Supervisor · App del BD · Sistema de Notificaciones · Sistema de Auditoría / Logs.

---

## 5. Huecos detectados en `Arquitecturas/`

No son errores a corregir aquí, sino ausencias reales frente a lo que el vault documenta como negocio.

| Hueco | Detalle |
|---|---|
| Departamentos sin arquitectura | **Inspección**, **QA**, **Customer Service** y **Contabilidad** existen como departamentos del negocio pero no tienen carpeta con `_Globales del Depto/`. Inspección solo tiene `00 - Arquitectura Inspector` + 1 caso de uso. |
| Reglas sin declarar | Inspección y Colaborador no declaran reglas `RR` propias: todo lo que ejecuta el Inspector se apoya en RR de Hotel y Reclutamiento. |
| IDs inconsistentes | Reclutamiento numera sus reglas `RR-01 … RR-16` (sin letra de departamento), mientras Hotel usa `RR-H-NN` y Ventas `RR-V-NN`. El [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento\|_GUIA]] exige `RR-[X]-[NN]`. Lo mismo ocurre con sus roles (`ROL-01` vs `ROL-H-01` / `ROL-V-01`). |
| Referencias cruzadas incompletas | `07 - Reglas de Negocio` de Reclutamiento no cierra con la sección **Referencias cruzadas** que sí tienen Hotel y Ventas. |
| Archivo vacío | [[Arquitecturas/_Globales/Roles del Sistema\|_Globales/Roles del Sistema]] existe pero no tiene contenido. |

---

## Relacionado

- [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento|_GUIA - Plantilla de Arquitectura de Departamento]]
- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] — fuente de verdad del sistema
- [[Arquitecturas/Ventas/_Globales del Depto/07 - Reglas de Negocio|Reglas de Negocio — Ventas]]
- [[Arquitecturas/Hotel/_Globales del Depto/07 - Reglas de Negocio|Reglas de Negocio — Hotel]]
- [[Arquitecturas/Reclutamiento/_Globales del Depto/07 - Reglas de Negocio|Reglas de Negocio — Reclutamiento]]
- [[Arquitecturas/Ventas/_Globales del Depto/09 - Dependencias|Dependencias — Ventas]]
- [[Arquitecturas/Hotel/_Globales del Depto/09 - Dependencias|Dependencias — Hotel]]
- [[Arquitecturas/Reclutamiento/_Globales del Depto/09 - Dependencias|Dependencias — Reclutamiento]]
- [[Arquitecturas/Inspección/00 - Arquitectura Inspector|Arquitectura Inspector]]
- [[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|Arquitectura Colaborador]]
