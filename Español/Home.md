---
tags:
  - modulo/core
aliases:
  - Oranje Matrix System
  - Home
---
# Oranje Matrix System

Sistema de gestión organizacional para staffing de hoteles. Documenta los roles, procesos, reglas de negocio y módulos operativos que estructuran la operación de Oranje, desde el reclutamiento de personal hasta su asignación y seguimiento en hoteles clientes.

## Departamentos y Roles

### [[Reclutamiento/Reclutamiento|Reclutamiento]]

Búsqueda, entrevista y asignación de personal a hoteles.

- [[Manager de Reclutamiento]] — monitorea la operación de reclutamiento e interviene solo en casos excepcionales: balanceo entre grupos, líder ausente o corrección de error de asignación.
- [[Líder de Grupo de Reclutadoras]] — recluta y supervisa el desempeño de un grupo de reclutadoras.
- [[Reclutadora]] — busca, entrevista, valida y asigna colaboradores.

### [[Hotel/Hotel|Hotel]]

Operación diaria y gestión de personal en hoteles clientes.

- [[Hotel/Manager General|Manager General]] — máxima autoridad en hoteles con jerarquía extendida. Aprueba horas de cualquier departamento.
- [[Hotel/Manager de Área|Manager de Área]] — aprueba requisiciones, **aprueba las horas trabajadas** y gestiona el schedule semanal. También llamado Hotel Manager; en jerarquía extendida: Gerente de Departamento.
- [[Hotel/Supervisor|Supervisor]] — crea requisiciones de personal, revisa ponches, reporta accidentes laborales y audita presentación del personal y ambiente del hotel.

### [[Inspección/Inspección|Inspección]]

Supervisión de colaboradores en sitio durante sus primeros días de asignación.

- [[Coordinador]] — asigna inspectores a zonas y actúa como enlace con otros departamentos.
- [[Inspector]] — supervisa colaboradores en sitio, verifica llegadas y cierra tarjetas de accidente.

### [[QA/QA|QA]]

Control de calidad sobre todos los departamentos de Oranje.

- [[Manager de QA]] — define métricas y KPIs, supervisa operadores y presenta reportes a dirección.
- [[Operador de QA]] — monitorea semáforos, mide métricas y emite observaciones por departamento.

### [[Ventas/Ventas|Ventas]]

Desarrollo de negocio y onboarding de nuevos hoteles clientes.

- [[Business Developer Coordinator]] — supervisa BDs en su territorio y da el visto bueno para convertir prospectos en clientes.
- [[Business Developer]] — gestiona el ciclo comercial con hoteles prospectos.

Proceso: [[Onboarding-Hotel]]

### [[Customer Service/Customer Service|Customer Service]]

Atención post-onboarding al hotel cliente activo: consultas, quejas, seguimiento de incidencias y coordinación interdepartamental.

- [[Customer Service/Customer Service Manager|Customer Service Manager]] — supervisa agentes, gestiona escalamientos y reporta métricas de satisfacción.
- [[Customer Service/Agente de Customer Service|Agente de Customer Service]] — recibe, documenta y da seguimiento a solicitudes del hotel.

### Contabilidad

Validación financiera semanal, gestión de pagos al colaborador y facturación al hotel.

- [[Manager de Contabilidad]] — supervisa, aprueba y autoriza los procesos financieros semanales.
- [[Contadora]] — ejecuta la validación, configuración y conciliación del ciclo de nómina.

Proceso: [[Contabilidad/Flujo de Nómina|Flujo de Nómina]]

## Entidad Central

- [[Colaborador/Colaborador|Colaborador]] — la persona que se recluta, asigna y opera en los hoteles. Su ciclo de vida es el eje del sistema. Ver arquitectura de su app: [[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|Arquitectura Colaborador]].

## Módulos Core

### Procesos

- [[Requisición]] — solicitud formal de personal por parte de un hotel. Ver [[Flujo de Requisición]].
- [[Contrato]] — acuerdo legal entre Oranje y el colaborador.
- [[Flujo de Reclutamiento]] — proceso de captación y alta de colaboradores nuevos.
- [[Flujo de Onboarding]] — proceso de incorporación de un hotel nuevo como cliente.

### Operación

- [[Core/Módulos/Schedule|Schedule]] — asignación semanal de colaboradores a hoteles.
- [[Timesheet]] — registro de horas trabajadas mediante ponches con GPS y evidencia de presencia (selfie o QR del hotel), aprobados por el [[Hotel/Manager de Área|Manager de Área]] o el [[Hotel/Manager General|Manager General]].
- [[Pool de Colaboradores]] — repositorio de colaboradores disponibles para asignación.

### Control

- [[Core/Módulos/Blacklist|Blacklist]] — registro de colaboradores bloqueados.
- [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]] — gestión de incidentes en sitio. Ver [[Flujo de Accidente Laboral]].
- [[Auditoría de Presentación y Ambiente]] — auditorías semanales del Supervisor sobre presentación del personal y condiciones del hotel; registro sin consecuencia en semáforos.
- [[Core/Módulos/Notificaciones/Catálogo de Notificaciones|Catálogo de Notificaciones]] — los 57 avisos del sistema: qué evento avisa a quién.
- [[Reglas de Negocio]] — restricciones y condiciones operativas del sistema.

### Contabilidad

- [[Consolidado Semanal del Colaborador]] — resumen semanal de timesheets y cálculo de pago.
- [[Deducciones]] — descuentos automáticos al cheque del colaborador.
- [[Facturación al Hotel]] — documento fiscal semanal de cobro al hotel.
- [[Vacaciones]] — cálculo de pago vacacional basado en promedio de 52 semanas.

## Catálogos

- [[Posiciones]] — tipos de puesto que puede ocupar un colaborador.
- [[Zonas]] — áreas geográficas de operación.
- [[Niveles de Inglés]] — escala de competencia lingüística de colaboradores.
- [[Departamentos del Hotel]] — áreas operativas internas del hotel.
- [[Modalidades de Contratación]] — formas de contrato disponibles.
- [[Reactivos de Auditoría]] — reactivos de las auditorías de presentación personal y ambiente.

## Semáforos

Indicadores de estado que reflejan la situación operativa de cada entidad en tiempo real.

- [[Semáforo del Colaborador]] — estado del colaborador en su ciclo de vida.
- [[Semáforo de Requisición]] — estado de una requisición en su proceso de cobertura.
- [[Semáforo de Urgencia de Requisición]] — nivel de urgencia de una requisición abierta.
- [[Semáforo de Posiciones de la Requisición]] — estado de cada posición dentro de una requisición.
- [[Semáforo Onboarding]] — estado de la negociación con un hotel prospecto.
- [[Indicador de Calidad]] — nivel de desempeño de un área supervisada por QA.
- [[Indicador de Cumplimiento del Timesheet]] — comparación entre horas trabajadas y horas contractuales del colaborador.
- [[Semáforo de Revisión del Día]] — si un día del timesheet ya fue revisado por el Supervisor.
- [[Semáforo de Aprobación de la Semana]] — ciclo de aprobación de un timesheet semanal completo.

## Simulaciones

Recorridos narrativos que ilustran cómo opera el sistema desde la perspectiva de cada departamento.

- [[Simulación - Punto de Vista de Reclutamiento]]
- [[Simulación - Ciclo de Vida del Colaborador]]
- [[Simulación - Punto de Vista del Hotel]]
- [[Simulación - Punto de Vista de Inspección]]
- [[Simulación - Punto de Vista de QA]]
- [[Simulación - Punto de Vista de Ventas]]
- [[Simulación - Punto de Vista de Contabilidad]]
