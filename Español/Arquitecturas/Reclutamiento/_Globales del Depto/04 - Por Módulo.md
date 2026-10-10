---
tags:
  - arquitectura
  - departamento/reclutamiento
aliases:
  - Por Módulo Reclutamiento
---

# REQUERIMIENTOS AGRUPADOS POR MÓDULO

---

## 📊 Dashboard

| ID  | Requerimiento                                                            | Prioridad | Estado |
| --- | ------------------------------------------------------------------------ | --------- | ------ |
| —   | (KPIs y vistas resumen — sin RFs propios; todos vienen de otros módulos) | —         | —      |

---

## 🧑‍🤝‍🧑 Reclutamiento

Incluye Pool de Colaboradores, creación/entrevista de nuevos colaboradores, validación, asignación y manejo del Schedule del hotel.

| ID    | Requerimiento                           | Prioridad | Estado      |
| ----- | --------------------------------------- | --------- | ----------- |
| RF-06 | Buscar candidatos en Pool               | 🔴 Alta   | ⬜ Pendiente |
| RF-07 | Crear colaborador (Fase 1 — Entrevista) | 🔴 Alta   | ⬜ Pendiente |
| RF-08 | Validar alta en app (Fase 2)            | 🔴 Alta   | ⬜ Pendiente |
| RF-09 | Habilitar accesos                       | 🟡 Media  | ⬜ Pendiente |
| RF-10 | Registrar entrevista                    | 🟡 Media  | ⬜ Pendiente |
| RF-15 | Asignar colaborador a hotel             | 🔴 Alta   | ⬜ Pendiente |
| RF-16 | Asignar al Schedule                     | 🔴 Alta   | ⬜ Pendiente |
| RF-17 | Reasignar colaborador                   | 🟡 Media  | ⬜ Pendiente |
| RF-18 | Desasignar colaborador                  | 🟡 Media  | ⬜ Pendiente |

---

## 📋 Requisición

Incluye recepción, toma libre (Self-Pick), gestión del estado de la requisición y los semáforos asociados (Urgencia, Posiciones).

| ID    | Requerimiento                    | Prioridad | Estado      |
| ----- | -------------------------------- | --------- | ----------- |
| RF-01 | Recibir requisición entrante     | 🔴 Alta   | ⬜ Pendiente |
| RF-02 | Tomar requisición (Self-Pick)    | 🔴 Alta   | ⬜ Pendiente |
| RF-03 | Liberar requisición              | 🟡 Media  | ⬜ Pendiente |
| RF-04 | Marcar requisición en proceso    | 🔴 Alta   | ⬜ Pendiente |
| RF-05 | Marcar requisición como cubierta | 🔴 Alta   | ⬜ Pendiente |
| RF-19 | Calcular Semáforo de Urgencia    | 🔴 Alta   | ⬜ Pendiente |
| RF-20 | Calcular Semáforo de Posiciones  | 🔴 Alta   | ⬜ Pendiente |
| RF-21 | Forzar cambio de semáforo        | 🟢 Baja   | ⬜ Pendiente |
| RF-39 | Tomar / Unirse a requisición ya tomada (colaborativo) | 🔴 Alta   | ⬜ Pendiente |
| RF-40 | Ver reclutadores activos de la requisición | 🟡 Media  | ⬜ Pendiente |
| RF-41 | Ver Historial de la requisición  | 🔴 Alta   | ⬜ Pendiente |

---

## ⚫ Blacklist

| ID    | Requerimiento                                   | Prioridad | Estado      |
| ----- | ----------------------------------------------- | --------- | ----------- |
| RF-11 | Consultar Blacklist                             | 🔴 Alta   | ⬜ Pendiente |
| RF-12 | Agregar a Blacklist (todos los roles del depto) | 🔴 Alta   | ⬜ Pendiente |
| RF-13 | Resolver disputa de colaborador (Inspector de zona) | 🟡 Media  | ⬜ Pendiente — ver [[RF-13 Resolver disputa de colaborador]] en Arquitecturas/Inspección |
| RF-14 | Remover de Blacklist — **existe desde el 2026-08-13**; desde el 2026-10-10 la ejecutan los tres roles de Reclutamiento y el Administrador, con motivo obligatorio. El colaborador regresa a Blanco | — | Del depto |

---

## 👥 Mi Grupo *(exclusivo Líder)*

| ID    | Requerimiento                         | Prioridad | Estado      |
| ----- | ------------------------------------- | --------- | ----------- |
| RF-22 | Ver Reclutadoras del grupo            | 🔴 Alta   | ⬜ Pendiente |
| RF-23 | Métricas individuales por Reclutadora | 🟡 Media  | ⬜ Pendiente |

---

## 📈 Reportes *(Líder y Manager)*

| ID    | Requerimiento             | Prioridad | Estado      |
| ----- | ------------------------- | --------- | ----------- |
| RF-24 | Generar reporte del grupo | 🟡 Media  | ⬜ Pendiente |
| RF-25 | Enviar reporte al Manager | 🟡 Media  | ⬜ Pendiente |
| RF-26 | Ver cobertura individual  | 🟢 Baja   | ⬜ Pendiente |
| RF-27 | Ver cobertura por zona    | 🟡 Media  | ⬜ Pendiente |
| RF-28 | Ver cobertura global      | 🟡 Media  | ⬜ Pendiente |

---

## 👨‍💼 Mi Equipo *(exclusivo Manager)*

| ID    | Requerimiento                     | Prioridad | Estado      |
| ----- | --------------------------------- | --------- | ----------- |
| RF-29 | Gestión de Líderes y Reclutadoras | 🔴 Alta   | ⬜ Pendiente |

---

## ⚠️ Incidencias *(exclusivo Manager)*

| ID    | Requerimiento       | Prioridad | Estado      |
| ----- | ------------------- | --------- | ----------- |
| RF-30 | Resolver incidencia | 🟡 Media  | ⬜ Pendiente |
| RF-31 | Escalar a comercial | 🟡 Media  | ⬜ Pendiente |

---

## ⚙️ Sistema (transversal — NO es módulo del sidebar)

| ID    | Requerimiento                     | Prioridad | Estado      |
| ----- | --------------------------------- | --------- | ----------- |
| RF-32 | Enviar notificaciones automáticas | 🔴 Alta   | ⬜ Pendiente |

---

## 🛠️ Configuración *(exclusivo Admin — EN PAUSA)*

> [!warning] Admin en pausa
> Estos requerimientos quedan documentados pero **no se aterrizan** en este alcance. Se retoman al final cuando las reglas de negocio estén estables.

| ID | Requerimiento | Prioridad | Estado |
|---|---|---|---|
| RF-33 | Configurar alertas | 🟢 Baja | ⏸️ Admin en pausa |
| RF-34 | CRUD de usuarios | 🔴 Alta | ⏸️ Admin en pausa |
| RF-35 | Editar catálogos | 🟡 Media | ⏸️ Admin en pausa |

---

## ⚙️ No Funcionales (Transversal)

| ID     | Requerimiento                    | Prioridad | Estado      |
| ------ | -------------------------------- | --------- | ----------- |
| RNF-01 | Tiempo de respuesta < 2s         | 🔴 Alta   | ⬜ Pendiente |
| RNF-02 | Disponibilidad 99.5%             | 🔴 Alta   | ⬜ Pendiente |
| RNF-03 | Cifrado de datos sensibles       | 🔴 Alta   | ⬜ Pendiente |
| RNF-04 | Compatibilidad multi-dispositivo | 🟡 Media  | ⬜ Pendiente |

---

## 📜 Reglas de Negocio

| ID    | Requerimiento                                 | Prioridad | Estado      |
| ----- | --------------------------------------------- | --------- | ----------- |
| RR-01 | Modelo Self-Pick colaborativo                 | 🔴 Alta   | ⬜ Pendiente |
| RR-02 | Consulta obligatoria de Blacklist             | 🔴 Alta   | ⬜ Pendiente |
| RR-03 | Cualquier rol agrega y levanta en Blacklist; el Administrador también | 🔴 Alta   | ⬜ Pendiente |
| RR-04 | Cobertura 100% para cierre                    | 🔴 Alta   | ⬜ Pendiente |
| RR-15 | Modelo colaborativo de requisición            | 🔴 Alta   | ⬜ Pendiente |
| RR-16 | Historial / trazabilidad de la requisición    | 🔴 Alta   | ⬜ Pendiente |

---

## 🔗 Integraciones

| ID    | Requerimiento                       | Prioridad | Estado      |
| ----- | ----------------------------------- | --------- | ----------- |
| RI-01 | Integración con módulo Hotel        | 🔴 Alta   | ⬜ Pendiente |
| RI-02 | Integración con Schedule            | 🔴 Alta   | ⬜ Pendiente |
| RI-03 | Integración con Timesheet           | 🟡 Media  | ⬜ Pendiente |
| RI-04 | Integración con app del colaborador | 🔴 Alta   | ⬜ Pendiente |
| RI-05 | Integración con Onboarding-Hotel    | 🟡 Media  | ⬜ Pendiente |
