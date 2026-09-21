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

Capturada por la [[Reclutadora]] durante el primer contacto. Además de la identidad del candidato, define las decisiones de Oranje sobre su perfil laboral *(cambiado el 2026-08-22; ver [[Reglas del Colaborador]])*:

| Campo | Capturado por |
|---|---|
| Nombre completo | Reclutadora |
| Edad | Reclutadora |
| Género | Reclutadora |
| Domicilio | Reclutadora |
| Teléfono | Reclutadora |
| Fotografía | Reclutadora |
| Posición ([[Posiciones]]) | Reclutadora |
| Modalidad ([[Modalidades de Contratación]]) | Reclutadora |
| Nivel de inglés ([[Niveles de Inglés]]) | Reclutadora |
| Nivel de experiencia | Reclutadora |

### Fase 2 — Alta en la plataforma (/collaborator)

Completada por el propio Colaborador:

| Campo | Catálogo asociado |
|---|---|
| SSN | — |
| ITIN | — |
| Tipo de transporte | — |

> [!info] Plazo de SSN/ITIN
> El colaborador tiene **3 días desde su alta** para cargar SSN o ITIN. Al día 4 se le muestra un aviso interceptor y al día 5 se le **suspende el acceso** hasta que contacte a Oranje. Detalle completo en [[Reglas del Colaborador#Plazo de SSN/ITIN]].

> [!info] La fotografía es del Pool, no del expediente
> La captura la Reclutadora en la entrevista y sirve para identificar a la
> persona en el [[Core/Módulos/Pool de Colaboradores|Pool]] y en la tarjeta que
> ve quien asigna. Es **opcional**: no bloquea la validación.
>
> No confundir con las **fotos del ponche**, que se toman en el momento al
> entrar y salir y son evidencia de presencia — una por marca y por jornada.
> Tampoco es un documento del expediente: ahí van el SSN/ITIN, la
> identificación y el comprobante de domicilio (opcional).

> [!info]
> SSN e ITIN son **opcionales**. Si el colaborador no proporciona ni SSN ni ITIN, el sistema activa automáticamente la **retención del 16%** sobre su pago (reembolsable). Ver [[Reglas del Colaborador]] y [[Deducciones]].

### Fase 3 — Datos de emergencia

Completada por el propio Colaborador desde `/collaborator`:

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
- Su presentación personal es evaluada semanalmente por el [[Hotel/Supervisor|Supervisor]] mediante la [[Auditoría de Presentación y Ambiente]]; el score de su última auditoría se muestra en su perfil.

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
- [[Auditoría de Presentación y Ambiente]]
- [[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|Arquitectura Colaborador]]
