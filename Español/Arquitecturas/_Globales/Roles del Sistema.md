---
tags:
  - arquitectura
  - global
  - roles
aliases:
  - Roles del Sistema
  - Directorio de roles
  - Roles y delegados
---

# Roles del Sistema

Los 19 roles de la plataforma (catálogo de [[Decisiones de Arquitectura#D-18 · El catálogo de roles|D-18]]): a quién reporta cada uno, en quién delega o quién lo respalda, y qué trabajo le toca. Es el directorio; el detalle de cada rol vive en su nota.

> [!info] Cómo leer «Delega en / respaldo»
> **Delega en** es a quién le pasa la siguiente acción del proceso. **Respaldo** es quién puede hacer lo suyo si él no está. Ambos salen de [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] y de las Matrices de Permisos; ninguna delegación es automática por plazo — el respaldo siempre actúa a mano (D-09).

> [!info] Herencia por jerarquía (2026-09-14)
> Dentro de Ventas, Reclutamiento, Hotel e Inspección, **quien tiene subordinados hereda todas sus atribuciones** (permisos y transiciones): el BDC hace lo del BD, el Líder lo de la Reclutadora, el Manager de Reclutamiento lo de ambos, el Manager de Área lo del Supervisor, el Manager General lo de los dos, y el Coordinador lo del Inspector. El alcance no se hereda (D-09) y las exclusividades escritas se conservan (conversión del BDC, veto del Administrador, cierre de accidente por el Inspector asignado, D-32). Auditorías (solo el Supervisor), Contabilidad, QA, Customer Service y el Colaborador quedan fuera. Regla en [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]].

## Hotel

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-H-01` | [[Hotel/Supervisor\|Supervisor]] | Manager de Área | Envía la requisición al Manager de Área para su firma; el Stand-by (Rosa) lo comparte con él | Crea requisiciones (quedan en Borrador); revisa los ponches de su hotel y resuelve los anómalos; registra ponches manuales con motivo; deja la semana lista y la envía a aprobación; manda a Stand-by; audita presentación del personal y ambiente del hotel; reporta accidentes |
| `ROL-H-02` | [[Hotel/Manager de Área\|Manager de Área]] (alias *Hotel Manager*) | Manager General | Hace todo lo de un Supervisor de su departamento (herencia); respaldado por el Manager General en todo lo suyo | Crea y **autoriza** requisiciones de su departamento (o las devuelve con observaciones); **aprueba las horas** de su departamento; elimina requisiciones autorizadas de su departamento con motivo; reporta y manda a Stand-by; **invita cuentas de su hotel** (`users:invite_hotel`) y puede proponer un Manager General solo si el hotel no tiene uno activo |
| `ROL-H-03` | [[Hotel/Manager General\|Manager General]] | — (máxima autoridad del hotel) | Es el **respaldo manual** del Manager de Área: aprueba horas y autoriza o elimina requisiciones de cualquier departamento; en jerarquía simple es la misma persona con dos roles | Todo lo del Manager de Área, sin límite de departamento; ve el Timesheet global del hotel; **invita cuentas de su hotel** (`users:invite_hotel`) — si lo que propone es un rol gerencial, lo confirma Oranje |

## Reclutamiento

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-R-01` | [[Reclutamiento/Reclutadora\|Reclutadora]] | Líder de Grupo | Escala al Líder cuando el Self-Pick vence según la urgencia; **marca** la cobertura y el Líder la aprueba | Toma requisiciones de la bandeja (Self-Pick); busca en el Pool; consulta la Blacklist antes de reclutar; entrevista y da de alta al colaborador (Fase 1: posición, modalidad, inglés, experiencia); valida su alta y lo mueve a Disponible; asigna fijos y temporales; agrega a Blacklist |
| `ROL-R-02` | [[Reclutamiento/Líder de Grupo de Reclutadoras\|Líder de Grupo de Reclutadoras]] | Manager de Reclutamiento | Hace todo lo de una Reclutadora; es su respaldo | **Aprueba o rechaza el cierre** de cobertura que marca la Reclutadora (RF-05); reasigna requisiciones dentro de su grupo (RF-37); supervisa a su grupo y su cobertura por zona |
| `ROL-R-03` | [[Reclutamiento/Manager de Reclutamiento\|Manager de Reclutamiento]] | — (dirección de Reclutamiento) | Hace todo lo de un Líder y de una Reclutadora (herencia): toma requisiciones, asigna, valida; además balanceo entre grupos, Líder ausente, correcciones | Supervisa a los Líderes; gestiona Mi Equipo (altas, movimientos, bajas lógicas); revisa casos de Blacklist (no levanta vetos: eso es del Administrador); Dashboard global del departamento |

## Ventas

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-V-01` | [[Ventas/Roles/Business Developer\|Business Developer (BD)]] | BDC | Cuando el ciclo llega a Rosa, **la conversión es del BDC**; no reactiva Café ni Negro, no asigna territorio | Identifica prospectos en su territorio; da de alta el hotel (incluido su método de ponche: selfie o QR); visita en frío; registra intentos de contacto; elabora y envía la Propuesta Personalizada; negocia y mueve el Semáforo Onboarding hasta Rosa; archiva el ciclo; **invita cuentas de los hoteles de sus zonas** (`users:invite_hotel`), que entran directo |
| `ROL-V-02` | [[Ventas/Roles/Business Developer Coordinator\|Business Developer Coordinator (BDC)]] | — (dirección de Ventas) | Hace todo lo de un BD (herencia): identifica, perfila, propone y mueve el ciclo hasta Rosa; respalda a sus BD en estancamientos y renegociación; comparte `territory:assign` con el Administrador | **Convierte** el hotel Rosa → Naranja (RR-V-01, exclusiva) creando el Usuario del Hotel; valida y activa el Documento de T&C; supervisa territorios y rutas; gestiona relaciones pausadas; ve Mi Equipo, Conversión, Clientes Activos y Reportes; invita cuentas de hotel y **confirma las gerenciales que propone un hotel** (`users:approve_hotel`, que comparte con el Administrador) |

## Inspección

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-I-01` | [[Inspección/Inspector\|Inspector]] | Coordinador | Cierra un accidente **solo si es el `inspector_id` de la tarjeta** (más estrecho que el rol, D-32); decide las disputas sin escalar | Verifica la llegada del colaborador el Día 1; entrega el uniforme al Día 3+; supervisa en sitio; investiga los Rojo y resuelve (Negro o Disponible); da seguimiento médico y cierra accidentes con alta médica |
| `ROL-I-02` | [[Inspección/Coordinador\|Coordinador de Inspección]] | — (dirección de Inspección) | Hace todo lo de un Inspector (herencia), salvo cerrar un accidente que no tenga asignado (D-32); escala lo que requiere atención especial; enlace con Reclutamiento y Hotel | Asigna inspectores a zonas; supervisa el trabajo en campo; ve el Indicador de Lunch Extendido por zona |

## QA

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-Q-01` | [[QA/Operador de QA\|Operador de QA]] (5, uno por departamento) | Manager de QA | — · **QA observa y mide; no ejecuta la operación de nadie** | Monitorea los semáforos de su departamento; mide los KPIs definidos; emite observaciones formales; alimenta el Indicador de Calidad |
| `ROL-Q-02` | [[QA/Manager de QA\|Manager de QA]] | — (dirección de QA) | Escala a dirección cuando un departamento sigue en Rojo sin mejora | Define métricas y KPIs por departamento; supervisa a los 5 operadores; consolida la vista global de calidad; actualiza el Indicador de Calidad; presenta reportes |

## Customer Service

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-CS-01` | [[Customer Service/Agente de Customer Service\|Agente de Customer Service]] | CS Manager | Escala al CS Manager los casos de mayor nivel | Recibe y documenta solicitudes del hotel; responde en los tiempos de las Reglas de CS; coordina con los departamentos; cierra el caso |
| `ROL-CS-02` | [[Customer Service/Customer Service Manager\|Customer Service Manager]] | — (dirección de CS) | Coordina con el BDC (comercial) y con el Coordinador de Inspección (verificación en sitio) | Asigna casos por carga y especialidad; vigila los tiempos de respuesta; atiende los escalados |

> [!note] Customer Service no tiene módulo construido: sus roles existen en el catálogo con cero permisos sembrados.

## Contabilidad

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-CO-01` | [[Contabilidad/Contadora\|Contadora]] | Manager de Contabilidad | Pone la **primera firma** de la nómina (valida); el Manager pone la segunda y **no puede** validar | Revisa el Consolidado Semanal y valida el pre-payroll contra los Timesheets aprobados; configura deducciones y la retención del 16 %; gestiona rates internos; calcula vacaciones |
| `ROL-CO-02` | [[Contabilidad/Manager de Contabilidad\|Manager de Contabilidad]] | — (dirección de Contabilidad) | Segunda firma: **autoriza** la nómina y aprueba la factura al hotel | Aprueba el Consolidado antes del pago; autoriza overtime parcial; aprueba la Facturación al Hotel; libera los pagos; supervisa a la Contadora |

## Transversales

| Código | Rol | Reporta a | Delega en / respaldo | Qué hace |
|---|---|---|---|---|
| `ROL-C-01` | [[Colaborador/Colaborador\|Colaborador]] | Operativamente, al Supervisor del hotel asignado | Todo su alcance es propio (`_own`, RR-C-01) | Completa su alta (Fases 2 y 3) y sube SSN/ITIN en 3 días; poncha Entrada y Salida (selfie o QR del hotel, con GPS); se declara Disponible voluntario (Amarillo); reporta accidentes; consulta sus horas y pagos |
| `ROL-SYS-01` | Sistema | — | Actor de las transiciones automáticas (Día 1-2 → Día 3+ → Fijo, Morado por inasistencia, Negro a las 3 faltas, urgencia por tiempo) | **Jamás tiene login**; sus permisos existen para que el journal firme lo automático |
| `ROL-ADM-01` | Administrador | — | Comparte `territory:assign` con el BDC | **Único** que levanta vetos de Blacklist (`blacklist:lift`); da de alta y edita al personal del sistema (`users:manage`) y las cuentas del hotel —Supervisores, Managers de Área y Managers Generales adicionales— (`users:manage_hotel`; el primer Manager General nace con la conversión); **confirma las cuentas gerenciales que propone un hotel** (`users:approve_hotel`, que comparte con el BDC); administra los Catálogos (departamentos y posiciones, modalidades, niveles de inglés, reactivos de auditoría) |

## Cadena de un proceso, de mano en mano

- **Requisición**: Supervisor la crea → Manager de Área (o General) la autoriza → Reclutadora la toma y cubre → Líder de Grupo aprueba el cierre → Inspector verifica la llegada del Día 1.
- **Horas**: Colaborador poncha → Supervisor revisa y envía → Manager de Área (o General) aprueba → Contadora valida → Manager de Contabilidad autoriza y libera el pago.
- **Hotel nuevo**: BD prospecta y negocia hasta Rosa → BDC convierte a Naranja → el hotel ya puede pedir personal.
- **Accidente**: Supervisor reporta (el colaborador pasa a Gris) → Inspector de la zona da seguimiento y cierra con alta médica (vuelve a Disponible).

## Relacionado

- [[Decisiones de Arquitectura#D-18 · El catálogo de roles|D-18 · El catálogo de roles]]
- [[Plan de Pruebas QA por Rol]] — los mismos 19 roles con sus casos de QA y usuarios de prueba
- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]]
- [[Arquitecturas/Hotel/_Globales del Depto/06 - Matriz de Permisos|Matriz de Permisos · Hotel]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/06 - Matriz de Permisos|Reclutamiento]] · [[Arquitecturas/Ventas/_Globales del Depto/06 - Matriz de Permisos|Ventas]]
- [[Árbol de Reglas de Negocio]]
