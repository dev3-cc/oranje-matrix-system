---
tags:
  - arquitectura
  - globales
  - indice
aliases:
  - Índice de Arquitecturas
  - Mapa de Arquitecturas
  - Home de Arquitecturas
---

# Índice de Arquitecturas

> [!info] Alcance
> Punto de entrada a los **229 archivos** de `Español/Arquitecturas/`. [[Home]] cubre el negocio (roles, módulos, semáforos, flujos); este índice cubre el **software**: qué documento existe, para qué rol y dónde está.

> [!tip] Cómo leerlo
> Empieza por el `00 - Arquitectura` del rol que te interesa (es el wireframe y el documento más referenciado). De ahí baja a los detalles. Si buscas reglas, ve al [[Arquitecturas/_Globales/Árbol de Reglas de Negocio|Árbol de Reglas de Negocio]].

---

## 1. Cómo está organizado

Todo sigue la [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento|_GUIA - Plantilla de Arquitectura de Departamento]]:

```
[Departamento]/
├── _Globales del Depto/    9 archivos compartidos por todo el depto
└── [Rol]/                 12 archivos numerados (00–11)
    └── Casos de Uso/       1 archivo por RF relevante
```

**Los 9 globales del departamento** — qué responde cada uno:

| # | Archivo | Responde |
|---|---|---|
| 01 | Portada | Qué roles existen y con qué ID |
| 02 | PRD - [Depto] | Objetivo y alcance del departamento |
| 03 | Tabla de Requerimientos | Todos los RF/RNF/RR/RI en una tabla |
| 04 | Por Módulo | Los RF agrupados por módulo del sidebar |
| 05 | Leyenda | Símbolos de la matriz de permisos |
| 06 | Matriz de Permisos | Quién puede hacer qué |
| 07 | Reglas de Negocio | Las reglas RR |
| 08 | Criterios de Aceptación | Qué se considera terminado |
| 09 | Dependencias | De qué depende el departamento |

**Los 12 archivos por rol** — qué responde cada uno:

| # | Archivo | Responde |
|---|---|---|
| 00 | Arquitectura [Rol] | Wireframe: login, header, sidebar, módulo por módulo |
| 01 | Ficha de Rol | Tarjeta resumen del rol |
| 02 | Ficha Detallada | Ficha extendida para Sheet |
| 03 | PRD | PRD específico del rol |
| 04 | Permisos Detallados | Permisos granulares módulo por módulo |
| 05 | Requerimientos | RF aplicables solo a este rol |
| 06 | Resumen Rápido | One-pager |
| 07 | Feature Map | Árbol de features (Primarias / Secundarias) |
| 08 | Acciones del Usuario | Acción → resultado |
| 09 | Campos del Formulario | Campos de cada formulario |
| 10 | Validaciones | Qué bloquea el sistema |
| 11 | Respuestas del Sistema | Evento → respuesta |

---

## 2. Mapa rápido

| Departamento | Roles documentados | Globales | Archivos de rol | Casos de Uso | Estado |
|---|---|---|---|---|---|
| **Hotel** | 3 (Supervisor · Manager de Área · Manager General) | 9 | 36 | 20 | Completo |
| **Reclutamiento** | 3 (Reclutadora · Líder de Grupo · Manager) | 9 | 39 | 39 | Completo (+3 extra) |
| **Ventas** | 2 (BD · BDC) | 9 | 24 | 13 | Completo |
| **Colaborador** | 1 (rol suelto, sin depto) | — | 13 | 10 | Sin globales |
| **Inspección** | 1 (Inspector) | — | 1 | 1 | Incompleto |
| **_Globales** | — | 5 | — | — | 1 archivo vacío |

---

## 3. Hotel

Portada y reglas: [[Arquitecturas/Hotel/_Globales del Depto/01 - Portada|01 Portada]] · [[Arquitecturas/Hotel/_Globales del Depto/02 - PRD - Hotel|02 PRD Hotel]] · [[Arquitecturas/Hotel/_Globales del Depto/03 - Tabla de Requerimientos|03 Tabla de Requerimientos]] · [[Arquitecturas/Hotel/_Globales del Depto/04 - Por Módulo|04 Por Módulo]] · [[Arquitecturas/Hotel/_Globales del Depto/05 - Leyenda|05 Leyenda]] · [[Arquitecturas/Hotel/_Globales del Depto/06 - Matriz de Permisos|06 Matriz de Permisos]] · [[Arquitecturas/Hotel/_Globales del Depto/07 - Reglas de Negocio|07 Reglas de Negocio]] · [[Arquitecturas/Hotel/_Globales del Depto/08 - Criterios de Aceptación|08 Criterios de Aceptación]] · [[Arquitecturas/Hotel/_Globales del Depto/09 - Dependencias|09 Dependencias]]

### Supervisor — ROL-H-01

Crea requisiciones, pone Stand-by, reporta colaborador y accidentes. No autoriza.

[[Arquitecturas/Hotel/Supervisor/00 - Arquitectura Supervisor|00 Arquitectura]] · [[Arquitecturas/Hotel/Supervisor/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Hotel/Supervisor/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Hotel/Supervisor/03 - PRD|03 PRD]] · [[Arquitecturas/Hotel/Supervisor/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Hotel/Supervisor/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Hotel/Supervisor/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Hotel/Supervisor/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Hotel/Supervisor/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Hotel/Supervisor/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Hotel/Supervisor/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Hotel/Supervisor/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (7):** [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-01 Crear requisicion|RF-H-01 Crear requisición]] · [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-02 Editar borrador|RF-H-02 Editar borrador]] · [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-03 Enviar a autorizacion|RF-H-03 Enviar a autorización]] · [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-17 Poner Stand-by|RF-H-17 Poner Stand-by]] · [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-19 Sugerir refuerzo|RF-H-19 Sugerir refuerzo]] · [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-20 Reportar accidente Escenario A|RF-H-20 Accidente Escenario A]] · [[Arquitecturas/Hotel/Supervisor/Casos de Uso/RF-H-21 Reportar accidente Escenario B|RF-H-21 Accidente Escenario B]]

### Manager de Área — ROL-H-02

Autoriza o rechaza requisiciones, gestiona Schedule y Timesheet. También llamado Gerente de Departamento.

[[Arquitecturas/Hotel/Manager de Área/00 - Arquitectura Manager de Área|00 Arquitectura]] · [[Arquitecturas/Hotel/Manager de Área/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Hotel/Manager de Área/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Hotel/Manager de Área/03 - PRD|03 PRD]] · [[Arquitecturas/Hotel/Manager de Área/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Hotel/Manager de Área/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Hotel/Manager de Área/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Hotel/Manager de Área/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Hotel/Manager de Área/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Hotel/Manager de Área/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Hotel/Manager de Área/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Hotel/Manager de Área/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (7):** [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-05 Autorizar requisicion|RF-H-05 Autorizar requisición]] · [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-06 Rechazar requisicion|RF-H-06 Rechazar requisición]] · [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-07 Eliminar requisicion con posiciones|RF-H-07 Eliminar requisición]] · [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-11 Generar QR|RF-H-11 Generar QR]] · [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-12 Editar Schedule semanal|RF-H-12 Editar Schedule]] · [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-14 Corregir ponche|RF-H-14 Corregir ponche]] · [[Arquitecturas/Hotel/Manager de Área/Casos de Uso/RF-H-18 Reportar colaborador|RF-H-18 Reportar colaborador]]

### Manager General — ROL-H-03

Máxima autoridad del hotel. Siempre existe. Operación + visibilidad global + reportes ejecutivos.

[[Arquitecturas/Hotel/Manager General/00 - Arquitectura Manager General|00 Arquitectura]] · [[Arquitecturas/Hotel/Manager General/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Hotel/Manager General/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Hotel/Manager General/03 - PRD|03 PRD]] · [[Arquitecturas/Hotel/Manager General/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Hotel/Manager General/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Hotel/Manager General/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Hotel/Manager General/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Hotel/Manager General/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Hotel/Manager General/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Hotel/Manager General/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Hotel/Manager General/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (6):** [[Arquitecturas/Hotel/Manager General/Casos de Uso/RF-H-22 Ver Schedule global|RF-H-22 Schedule global]] · [[Arquitecturas/Hotel/Manager General/Casos de Uso/RF-H-23 Ver Timesheet global|RF-H-23 Timesheet global]] · [[Arquitecturas/Hotel/Manager General/Casos de Uso/RF-H-24 Generar reporte ejecutivo|RF-H-24 Reporte ejecutivo]] · [[Arquitecturas/Hotel/Manager General/Casos de Uso/RF-H-25 Enviar reporte a direccion|RF-H-25 Enviar a dirección]] · [[Arquitecturas/Hotel/Manager General/Casos de Uso/RF-H-26 Comentar expediente|RF-H-26 Comentar expediente]] · [[Arquitecturas/Hotel/Manager General/Casos de Uso/RF-H-27 Escalar requisicion demorada|RF-H-27 Escalar requisición]]

---

## 4. Reclutamiento

Portada y reglas: [[Arquitecturas/Reclutamiento/_Globales del Depto/01 - Portada|01 Portada]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/02 - PRD - Reclutamiento|02 PRD Reclutamiento]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/03 - Tabla de Requerimientos|03 Tabla de Requerimientos]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/04 - Por Módulo|04 Por Módulo]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/05 - Leyenda|05 Leyenda]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/06 - Matriz de Permisos|06 Matriz de Permisos]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/07 - Reglas de Negocio|07 Reglas de Negocio]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/08 - Criterios de Aceptación|08 Criterios de Aceptación]] · [[Arquitecturas/Reclutamiento/_Globales del Depto/09 - Dependencias|09 Dependencias]]

### Reclutadora — ROL-01

Recluta desde la requisición tomada. Rol global dentro de Oranje.

[[Arquitecturas/Reclutamiento/Reclutadora/00 - Arquitectura Reclutadora|00 Arquitectura]] · [[Arquitecturas/Reclutamiento/Reclutadora/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Reclutamiento/Reclutadora/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Reclutamiento/Reclutadora/03 - PRD|03 PRD]] · [[Arquitecturas/Reclutamiento/Reclutadora/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Reclutamiento/Reclutadora/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Reclutamiento/Reclutadora/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Reclutamiento/Reclutadora/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Reclutamiento/Reclutadora/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Reclutamiento/Reclutadora/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Reclutamiento/Reclutadora/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Reclutamiento/Reclutadora/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (15):** [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-04 Tomar requisicion en proceso|RF-04 Tomar requisición]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-05 Marcar requisicion como cubierta|RF-05 Marcar como cubierta]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-06 Buscar candidatos en pool|RF-06 Buscar en pool]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-07 Crear colaborador|RF-07 Crear colaborador]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-08 Validar alta en app|RF-08 Validar alta en app]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-10 Registrar entrevista|RF-10 Registrar entrevista]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-11 Consultar blacklist|RF-11 Consultar Blacklist]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-15 Asignar colaborador a hotel|RF-15 Asignar a hotel]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-16 Asignar al Schedule|RF-16 Asignar al Schedule]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-17 Solicitar reasignacion|RF-17 Solicitar reasignación]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-18 Solicitar desasignacion|RF-18 Solicitar desasignación]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-26 Ver cobertura individual|RF-26 Cobertura individual]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-39 Unirse a requisicion ya tomada|RF-39 Unirse a requisición]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-40 Ver reclutadores activos de la requisicion|RF-40 Reclutadores activos]] · [[Arquitecturas/Reclutamiento/Reclutadora/Casos de Uso/RF-41 Ver historial de la requisicion|RF-41 Historial]]

### Líder de Grupo de Reclutadoras — ROL-02

Opera como Reclutadora y además supervisa a un grupo. Aprueba los cierres de cobertura.

> [!note] La carpeta se llama `Lider de Grupo de Reclutadoras` (sin acento).

[[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/00 - Arquitectura Lider de Grupo|00 Arquitectura]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/03 - PRD|03 PRD]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (12):** [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-05 Aprobar cierre de cobertura|RF-05 Aprobar cierre]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-22 Ver Reclutadoras del grupo|RF-22 Ver el grupo]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-23 Metricas individuales por Reclutadora|RF-23 Métricas individuales]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-24 Generar reporte del grupo|RF-24 Reporte del grupo]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-25 Enviar reporte al Manager|RF-25 Enviar al Manager]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-27 Ver cobertura por zona|RF-27 Cobertura por zona]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-36 Ver carga detallada de la Reclutadora|RF-36 Carga detallada]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-37 Reasignar requisicion a otra Reclutadora|RF-37 Reasignar requisición]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-38 Marcar disponibilidad de la Reclutadora|RF-38 Marcar disponibilidad]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-39 Unirse a requisicion ya tomada|RF-39 Unirse a requisición]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-40 Ver reclutadores activos de la requisicion|RF-40 Reclutadores activos]] · [[Arquitecturas/Reclutamiento/Lider de Grupo de Reclutadoras/Casos de Uso/RF-41 Ver historial de la requisicion|RF-41 Historial]]

### Manager de Reclutamiento — ROL-03

Coordina el departamento, supervisa a los Líderes y maneja casos especiales. **Único rol con 15 archivos** (ver sección 8).

[[Arquitecturas/Reclutamiento/Manager de Reclutamiento/00 - Arquitectura Manager|00 Arquitectura]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/03 - PRD|03 PRD]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/09 - Campos de formulario|09 Campos de formulario]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/11 - Respuestas del Sistema|11 Respuestas]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/12 - Mockup y Decisiones de UI|12 Mockup y UI]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/13 - Estados de Incidencia|13 Estados de Incidencia]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/14 - SLA de Incidencias|14 SLA de Incidencias]]

**Casos de Uso (12):** [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-12 Agregar a Blacklist|RF-12 Agregar a Blacklist]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-21 Forzar cambio de semaforo|RF-21 Forzar semáforo]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-23 Metricas individuales por Reclutadora|RF-23 Métricas individuales]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-24 Generar reporte global|RF-24 Reporte global]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-27 Ver cobertura por zona|RF-27 Cobertura por zona]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-28 Ver cobertura global|RF-28 Cobertura global]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-29 Gestion de Lideres y Reclutadoras|RF-29 Gestión de Líderes]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-30 Resolver incidencia|RF-30 Resolver incidencia]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-31 Escalar a comercial|RF-31 Escalar a comercial]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-EXC-01 Tomar requisicion personal|RF-EXC-01 Tomar requisición]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-EXC-02 Asignar requisicion a Reclutadora|RF-EXC-02 Asignar requisición]] · [[Arquitecturas/Reclutamiento/Manager de Reclutamiento/Casos de Uso/RF-EXC-03 Reasignar requisicion|RF-EXC-03 Reasignar requisición]]

> [!info] Los `RF-EXC-*` son las intervenciones excepcionales del Manager. Toda ejecución queda en log con motivo obligatorio (RR-12).

---

## 5. Ventas

Portada y reglas: [[Arquitecturas/Ventas/_Globales del Depto/01 - Portada|01 Portada]] · [[Arquitecturas/Ventas/_Globales del Depto/02 - PRD - Ventas|02 PRD Ventas]] · [[Arquitecturas/Ventas/_Globales del Depto/03 - Tabla de Requerimientos|03 Tabla de Requerimientos]] · [[Arquitecturas/Ventas/_Globales del Depto/04 - Por Módulo|04 Por Módulo]] · [[Arquitecturas/Ventas/_Globales del Depto/05 - Leyenda|05 Leyenda]] · [[Arquitecturas/Ventas/_Globales del Depto/06 - Matriz de Permisos|06 Matriz de Permisos]] · [[Arquitecturas/Ventas/_Globales del Depto/07 - Reglas de Negocio|07 Reglas de Negocio]] · [[Arquitecturas/Ventas/_Globales del Depto/08 - Criterios de Aceptación|08 Criterios de Aceptación]] · [[Arquitecturas/Ventas/_Globales del Depto/09 - Dependencias|09 Dependencias]]

### Business Developer — ROL-V-01

Ejecutor comercial en campo. Identifica prospectos, elabora propuestas, gestiona rechazos.

[[Arquitecturas/Ventas/Business Developer/00 - Arquitectura Business Developer|00 Arquitectura]] · [[Arquitecturas/Ventas/Business Developer/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Ventas/Business Developer/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Ventas/Business Developer/03 - PRD|03 PRD]] · [[Arquitecturas/Ventas/Business Developer/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Ventas/Business Developer/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Ventas/Business Developer/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Ventas/Business Developer/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Ventas/Business Developer/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Ventas/Business Developer/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Ventas/Business Developer/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Ventas/Business Developer/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (7):** [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-01 Identificar prospecto|RF-V-01 Identificar prospecto]] · [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-02 Crear perfil del hotel|RF-V-02 Crear perfil del hotel]] · [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-04 Elaborar Propuesta Personalizada|RF-V-04 Elaborar propuesta]] · [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-05 Enviar propuesta|RF-V-05 Enviar propuesta]] · [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-08 Crear Documento TC|RF-V-08 Crear Documento T&C]] · [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-15 Gestionar rechazo Rojo|RF-V-15 Gestionar rechazo (Rojo)]] · [[Arquitecturas/Ventas/Business Developer/Casos de Uso/RF-V-17 Marcar estancamiento Cafe|RF-V-17 Marcar estancamiento (Café)]]

### Business Developer Coordinator — ROL-V-02

Supervisa rutas y zonas. Valida términos, aprueba conversiones, gestiona estancamientos y clientes pausados.

[[Arquitecturas/Ventas/Business Developer Coordinator/00 - Arquitectura Business Developer Coordinator|00 Arquitectura]] · [[Arquitecturas/Ventas/Business Developer Coordinator/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Ventas/Business Developer Coordinator/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Ventas/Business Developer Coordinator/03 - PRD|03 PRD]] · [[Arquitecturas/Ventas/Business Developer Coordinator/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Ventas/Business Developer Coordinator/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Ventas/Business Developer Coordinator/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Ventas/Business Developer Coordinator/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Ventas/Business Developer Coordinator/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Ventas/Business Developer Coordinator/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Ventas/Business Developer Coordinator/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Ventas/Business Developer Coordinator/11 - Respuestas del Sistema|11 Respuestas]]

**Casos de Uso (6):** [[Arquitecturas/Ventas/Business Developer Coordinator/Casos de Uso/RF-V-10 Validar TC|RF-V-10 Validar T&C]] · [[Arquitecturas/Ventas/Business Developer Coordinator/Casos de Uso/RF-V-11 Crear Usuario del Hotel|RF-V-11 Crear Usuario del Hotel]] · [[Arquitecturas/Ventas/Business Developer Coordinator/Casos de Uso/RF-V-12 Aprobar conversion|RF-V-12 Aprobar conversión]] · [[Arquitecturas/Ventas/Business Developer Coordinator/Casos de Uso/RF-V-18 Desbloquear Cafe|RF-V-18 Desbloquear Café]] · [[Arquitecturas/Ventas/Business Developer Coordinator/Casos de Uso/RF-V-20 Marcar cliente Negro|RF-V-20 Marcar cliente Negro]] · [[Arquitecturas/Ventas/Business Developer Coordinator/Casos de Uso/RF-V-26 Generar reporte de Ventas|RF-V-26 Reporte de Ventas]]

---

## 6. Colaborador — ROL-C-01

Entidad central del sistema y usuario de la app móvil. Es un **rol suelto**: no tiene `_Globales del Depto/` porque no es un departamento.

[[Arquitecturas/Colaborador/00 - Arquitectura Colaborador|00 Arquitectura]] · [[Arquitecturas/Colaborador/01 - Ficha de Rol|01 Ficha]] · [[Arquitecturas/Colaborador/02 - Ficha Detallada|02 Ficha Detallada]] · [[Arquitecturas/Colaborador/03 - PRD|03 PRD]] · [[Arquitecturas/Colaborador/04 - Permisos Detallados|04 Permisos]] · [[Arquitecturas/Colaborador/05 - Requerimientos|05 Requerimientos]] · [[Arquitecturas/Colaborador/06 - Resumen Rápido|06 Resumen]] · [[Arquitecturas/Colaborador/07 - Feature Map|07 Feature Map]] · [[Arquitecturas/Colaborador/08 - Acciones del Usuario|08 Acciones]] · [[Arquitecturas/Colaborador/09 - Campos del Formulario|09 Campos]] · [[Arquitecturas/Colaborador/10 - Validaciones|10 Validaciones]] · [[Arquitecturas/Colaborador/11 - Respuestas del Sistema|11 Respuestas]] · [[Arquitecturas/Colaborador/12 - Mockup y Decisiones de UI|12 Mockup y UI]]

**Casos de Uso (10):** [[Arquitecturas/Colaborador/Casos de Uso/RF-C-01 Completar alta en la app|RF-C-01 Completar alta]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-02 Completar datos de emergencia|RF-C-02 Datos de emergencia]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-03 Ponchar via QR|RF-C-03 Ponchar vía QR]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-04 Activar disponibilidad voluntaria|RF-C-04 Disponibilidad voluntaria]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-05 Reportar accidente laboral desde la app|RF-C-05 Reportar accidente]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-06 Consultar Mi Schedule|RF-C-06 Mi Schedule]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-07 Consultar Mi Timesheet|RF-C-07 Mi Timesheet]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-08 Consultar Mi Pago semanal|RF-C-08 Mi Pago semanal]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-09 Consultar notificaciones|RF-C-09 Notificaciones]] · [[Arquitecturas/Colaborador/Casos de Uso/RF-C-10 Consultar Mi perfil y estado|RF-C-10 Mi perfil y estado]]

---

## 7. Inspección — incompleta

Solo existen 2 archivos. El Inspector ejecuta transiciones críticas del [[Semáforo del Colaborador]] sin arquitectura completa que las respalde.

[[Arquitecturas/Inspección/00 - Arquitectura Inspector|00 Arquitectura Inspector]] · [[Arquitecturas/Inspección/Casos de Uso/RF-13 Resolver disputa de colaborador|RF-13 Resolver disputa de colaborador]]

**Falta:** los 9 globales del depto, los 11 archivos de detalle del rol y la arquitectura del Coordinador.

---

## 8. Globales y metodología

| Archivo | Contenido |
|---|---|
| [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento\|_GUIA - Plantilla de Arquitectura de Departamento]] | Metodología completa: estructura, IDs, plantillas por archivo, checklist de calidad |
| [[Arquitecturas/_Globales/Árbol de Reglas de Negocio\|Árbol de Reglas de Negocio]] | Todas las RR por depto + handoffs entre departamentos |
| [[Arquitecturas/_Globales/Convenciones de Diseño\|Convenciones de Diseño]] | Convenciones visuales transversales |
| [[Arquitecturas/_Globales/Estructura General App\|Estructura General App]] | Estructura de la aplicación |
| [[Arquitecturas/_Globales/Plan de Implementación - Base de Datos\|Plan de Implementación - Base de Datos]] | Las 28 tareas para llegar a la primera migración, en 4 fases. Camino crítico: `catalogs` e `identity`, no Ventas |
| [[Arquitecturas/_Globales/README\|README]] | Notas de la carpeta `_Globales` |
| [[Arquitecturas/_Globales/Roles del Sistema\|Roles del Sistema]] | **Archivo vacío** |

---

## 9. Desviaciones de la plantilla

Diferencias reales entre lo que existe y lo que exige el [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento|_GUIA]].

| Desviación | Detalle |
|---|---|
| **Inspección incompleta** | 2 archivos de los 21 que exige la plantilla (9 globales + 12 de rol). Sin `_Globales del Depto/`. |
| **Colaborador sin globales** | 13 archivos de rol pero ningún global. Es un rol suelto, no un depto — la plantilla no cubre este caso. |
| **Manager de Reclutamiento con 15 archivos** | Añade `12 - Mockup y Decisiones de UI`, `13 - Estados de Incidencia` y `14 - SLA de Incidencias`. La plantilla llega hasta el 11. Colaborador también añade un `12`. |
| **`09` con nombre distinto** | El Manager de Reclutamiento tiene `09 - Campos de formulario`; todos los demás roles usan `09 - Campos del Formulario`. |
| **`RF-24` con dos significados** | La [[Arquitecturas/Reclutamiento/_Globales del Depto/03 - Tabla de Requerimientos\|Tabla de Requerimientos]] define RF-24 como *"Generar reporte del grupo"* (Líder). El Manager usa el mismo ID para *"Generar reporte global"*, que no está en la tabla maestra. Uno de los dos necesita ID propio. |
| **IDs sin letra de depto** | Reclutamiento numera `RF-04`, `RF-05`, `ROL-01`…; Hotel y Ventas usan `RF-H-NN` / `RF-V-NN` y `ROL-H-NN` / `ROL-V-NN`. El `_GUIA` exige la letra del depto. |
| **Carpeta sin acento** | `Lider de Grupo de Reclutadoras` — el rol se escribe *Líder* en toda la documentación. |
| **`Roles del Sistema` vacío** | Existe el archivo, no el contenido. |

> [!note] Lo que no es desviación
> **`RF-05` aparece en dos roles con nombres distintos** (*Marcar requisición como cubierta* en Reclutadora, *Aprobar cierre de cobertura* en Líder) y eso es **correcto**: la Tabla de Requerimientos define RF-05 como un solo requerimiento de dos mitades — cualquier participante solicita el cierre, el Líder lo aprueba. Igual pasa con `RF-23`, `RF-27`, `RF-39`, `RF-40` y `RF-41`, compartidos entre Reclutadora y Líder con el mismo nombre.

---

## Relacionado

- [[Home]] — índice del negocio (roles, módulos, semáforos, flujos)
- [[Arquitecturas/_GUIA - Plantilla de Arquitectura de Departamento|_GUIA - Plantilla de Arquitectura de Departamento]]
- [[Arquitecturas/_Globales/Árbol de Reglas de Negocio|Árbol de Reglas de Negocio]]
- [[Core/Módulos/Reglas de Negocio|Reglas de Negocio]] — fuente de verdad del negocio
