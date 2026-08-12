---
tags:
  - modulo/colaborador
aliases:
  - Colaborador
---

# Colaborador

Entidad central del sistema Oranje. Representa a la persona que es reclutada, asignada y gestionada operativamente en los hoteles. Su registro vive en el [[Pool de Colaboradores]] y su ciclo de vida se refleja en el [[Semáforo del Colaborador]].

> [!info]
> El Colaborador no es solo un registro estático: su estado cambia a lo largo del tiempo según el [[Semáforo del Colaborador]], que define en qué fase operativa se encuentra en cada momento.

## Datos del Colaborador

Los datos del colaborador se capturan en tres fases.

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
> SSN e ITIN son **opcionales**. Si el colaborador no proporciona ni SSN ni ITIN, el sistema activa automáticamente la **retención del 16%** sobre su pago (reembolsable). Ver [[Reglas del Colaborador]] y [[Deducciones]].

### Fase 3 — Datos de emergencia

Completada por el propio Colaborador desde la app:

| Campo | Descripción |
|---|---|
| Contacto de emergencia — nombre | Persona a contactar en caso de emergencia |
| Contacto de emergencia — teléfono | Teléfono del contacto |
| Contacto de emergencia — parentesco | Relación con el colaborador |
| Tipo de sangre | Grupo sanguíneo |
| Alergias o condiciones médicas | Información médica relevante |

## Ciclo de vida

El estado del Colaborador se gestiona a través del [[Semáforo del Colaborador]], que define 12 estados posibles desde su ingreso al [[Pool de Colaboradores]] hasta su eventual salida o bloqueo en la [[Core/Módulos/Blacklist|Blacklist]].

## Roles que interactúan con el Colaborador

- [[Reclutadora]] — lo recluta, valida documentos y lo asigna a un hotel.
- [[Manager de Reclutamiento]] — supervisa el proceso y revisa casos de Blacklist.
- [[Hotel/Manager de Área|Manager de Área]] — gestiona descansos y reportes.
- [[Inspector]] — verifica su llegada el Día 1, entrega uniforme en Día 3+, investiga casos Rojo.

## Operación diaria

- Su asignación semanal se registra en el [[Core/Módulos/Schedule|Schedule]].
- Sus horas trabajadas se registran en el [[Timesheet]] mediante tres ponches por jornada desde su teléfono, con GPS dentro del perímetro del hotel y foto en Entrada y Salida: Entrada, Comida, Salida. Los revisa el [[Hotel/Supervisor|Supervisor]] y los aprueba el [[Hotel/Manager de Área|Manager de Área]] de su departamento o el [[Hotel/Manager General|Manager General]].

## Relacionado

- [[Reglas del Colaborador]]
- [[Pool de Colaboradores]]
- [[Semáforo del Colaborador]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Timesheet]]
- [[Reclutamiento/Flujo de Reclutamiento|Flujo de Reclutamiento]]
- [[Core/Módulos/Requisicion/Requisición|Requisición]]
- [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]
- [[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|Arquitectura Colaborador]]
