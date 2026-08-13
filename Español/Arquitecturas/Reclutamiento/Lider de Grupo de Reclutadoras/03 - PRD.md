---
tags:
  - arquitectura
  - rol/lider-de-grupo
aliases:
  - PRD Líder de Grupo
---

# DOCUMENTO DE REQUISITOS DE PRODUCTO
## PRD – LÍDER DE GRUPO DE RECLUTADORAS

**Sistema de Gestión de Personal · Rol Líder de Grupo**

---

| Campo                   | Contenido                                                        |
| ----------------------- | ---------------------------------------------------------------- |
| **ID del PRD**          | PRD-RECL-03                                                      |
| **Historia de Usuario** | HU-RECL-03                                                       |
| **Departamento**        | Reclutamiento                                                    |
| **Funcionalidad**       | Operar como Reclutadora + supervisar grupo + reportar al Manager |
| **Actor Principal**     | Líder de Grupo de Reclutadoras                                   |
| **Dispositivo**         | Web – Desktop / Tablet                                           |
| **Estado**              | En definición                                                    |
| **Versión**             | 1.0                                                              |

---

## Objetivo

Permitir al Líder de Grupo cubrir requisiciones (igual que una Reclutadora) y simultáneamente supervisar el desempeño de las Reclutadoras a su cargo, monitorear su carga, atender incidencias de primer nivel y enviar reportes formales al Manager.

---

## Flujo General

**Tomar requisición → Cubrir como Reclutadora → Supervisar grupo → Generar reporte → Enviar al Manager**

---

## Alcance

### Qué incluye este rol

- Tomar requisiciones de la bandeja mediante el modelo Self-Pick colaborativo (RR-01 / RR-15).
- Unirse a una requisición ya tomada por otra Reclutadora sin desplazarla (RF-39).
- Ejecutar el flujo operativo completo de Reclutadora: entrevistar candidatos, crear colaborador (Fase 1), validar alta en app (Fase 2), asignar a hotel y asignar al Schedule.
- Consultar la Blacklist antes de asignar (RF-11) y agregar a Blacklist cuando corresponde (RF-12).
- Aprobar el cierre de cobertura de una requisición cuando el porcentaje alcanza el 100 % (RF-05).
- Ver las Reclutadoras de su grupo con métricas individuales (RF-22 / RF-23).
- Ver la carga detallada de cada Reclutadora del grupo (RF-36).
- Reasignar una requisición de una Reclutadora del grupo a otra (RF-37).
- Marcar disponibilidad de las Reclutadoras del grupo (activa / vacaciones) (RF-38).
- Ver cobertura individual (RF-26) y cobertura por zona (RF-27).
- Ver los reclutadores activos en una requisición (RF-40) y el historial / timeline de la requisición (RF-41).
- Generar el reporte del grupo (RF-24) y enviarlo formalmente al Manager (RF-25).
- Atender incidencias de primer nivel del grupo y escalar al Manager cuando superan su alcance.

### Qué NO incluye este rol

- No supervisa el departamento completo — solo el grupo de Reclutadoras que le fue asignado.
- No resuelve disputas de Blacklist — eso corresponde al Inspector de Blacklist.
- No remueve colaboradores de la Blacklist — esa acción requiere autorización del Manager.
- No genera reportes globales del departamento — reporta únicamente sobre su grupo al Manager.
- No escala casos directamente a Dirección — el canal de escalamiento es siempre el Manager.
- No asigna ni reasigna Reclutadoras entre grupos — eso es atribución del Manager.
- No hay funcionalidad de chat o mensajería directa en la plataforma — la comunicación se canaliza mediante notificaciones, solicitudes estructuradas y el log de la requisición.

---

## Restricciones

| Restricción | Descripción |
| ----------- | ----------- |
| **Blacklist — solo consulta y agrega** | El Líder puede consultar la Blacklist y agregar a colaboradores, pero no puede remover ni resolver disputas. Las disputas las gestiona el Inspector de Blacklist; las remociones, el Manager. |
| **Veto vigente** | Un colaborador con un veto **vigente** en Blacklist no puede ser asignado bajo ninguna circunstancia, independientemente de la urgencia de la requisición. Solo un Administrador puede levantarlo. |
| **Sin estado "Liberada"** | El modelo colaborativo no usa estado "Liberada". El flujo es: entrar a la requisición / salir de ella. Si salen todos los reclutadores, la requisición vuelve a "Autorizada". |
| **Aprobación de cierre solo con 100 %** | La acción RF-05 (marcar como cubierta) solo está disponible cuando la requisición alcanza el 100 % de cobertura. |
| **Reasignación solo dentro del grupo** | RF-37 permite reasignar una requisición únicamente entre las Reclutadoras del grupo propio del Líder. Mover requisiciones entre grupos requiere intervención del Manager. |
| **Reporte dirigido al Manager** | El único destinatario del reporte formal (RF-25) es el Manager de Reclutamiento. El Líder no envía reportes directamente a Dirección. |
| **Sin chat en la plataforma** | No existe funcionalidad de mensajería directa. Las incidencias y escalamientos se registran mediante notificaciones y notas en el log de la requisición. |
| **Dispositivo** | Desktop / Tablet. Sin interfaz de operación en móvil en esta fase. |

---

## Criterios de Éxito / Métricas

| Métrica | Descripción |
| ------- | ----------- |
| **Cobertura del grupo** | Porcentaje de requisiciones asignadas al grupo que alcanzaron el 100 % de cobertura en el periodo. |
| **Tiempo promedio de cobertura** | Tiempo medio desde que una requisición pasa a "En proceso" hasta que se aprueba su cierre (RF-05). |
| **Carga por Reclutadora** | Número de requisiciones activas por cada Reclutadora del grupo. Debe mantenerse equilibrada. |
| **Casos escalados al Manager** | Número de incidencias que el Líder no pudo resolver en primer nivel y escaló al Manager. Meta: minimizar escalamientos innecesarios. |
| **Reportes enviados al Manager** | Frecuencia y puntualidad del envío del reporte del grupo (RF-25). |
| **Métricas individuales** | Requisiciones cubiertas, tiempo promedio y tasa de asignación exitosa por Reclutadora del grupo (RF-23). |
| **Reasignaciones realizadas** | Número de requisiciones reasignadas (RF-37) como indicador de gestión de carga activa. |

---

## Historias de Usuario

### HU-LG-01 — Aprobar cierre de cobertura (RF-05)

**Como** Líder de Grupo,  
**quiero** poder marcar una requisición como cubierta cuando su porcentaje llega al 100 %,  
**para** aprobar el cierre formal del proceso y notificar al Manager sin necesidad de su intervención directa.

**Criterios de aceptación:**
- La acción solo aparece disponible cuando la cobertura es exactamente 100 %.
- Al confirmar, la requisición cambia de estado a "Cubierta" y se genera una notificación al Manager.
- El evento queda registrado en el historial / timeline de la requisición con fecha y actor.

---

### HU-LG-02 — Ver y gestionar el grupo de Reclutadoras (RF-22 / RF-36 / RF-38)

**Como** Líder de Grupo,  
**quiero** ver la lista de Reclutadoras de mi grupo con su estado y carga actual, acceder al detalle de cada una y marcar su disponibilidad,  
**para** tener visibilidad consolidada del equipo y redistribuir trabajo cuando sea necesario.

**Criterios de aceptación:**
- Vista de lista con nombre, estado (Activa / Vacaciones) y número de requisiciones activas por Reclutadora.
- Al hacer clic en una Reclutadora se accede al detalle: KPIs, carga y historial.
- RF-36: modal con las requisiciones en proceso de la Reclutadora seleccionada.
- RF-38: acción para cambiar el estado de la Reclutadora (Activa ↔ Vacaciones).

---

### HU-LG-03 — Métricas individuales por Reclutadora (RF-23)

**Como** Líder de Grupo,  
**quiero** ver métricas de desempeño individuales de cada Reclutadora de mi grupo,  
**para** identificar quién necesita apoyo, quién está sobrecargada y preparar el reporte para el Manager.

**Criterios de aceptación:**
- Métricas visibles: requisiciones cubiertas, tiempo promedio de cobertura, tasa de asignación exitosa.
- Los datos corresponden al periodo definido por el filtro activo.
- La vista es de solo lectura; no permite editar los registros de la Reclutadora.

---

### HU-LG-04 — Reasignar requisición a otra Reclutadora (RF-37)

**Como** Líder de Grupo,  
**quiero** poder mover una requisición de una Reclutadora de mi grupo a otra,  
**para** equilibrar la carga del equipo cuando una Reclutadora está saturada o no disponible.

**Criterios de aceptación:**
- Solo se puede reasignar entre Reclutadoras del grupo propio.
- El evento de reasignación queda registrado en el historial de la requisición con actor y fecha.
- La Reclutadora origen pierde la requisición y la Reclutadora destino la recibe en su bandeja.
- No retrocede el semáforo de la requisición.

---

### HU-LG-05 — Unirse a una requisición ya tomada (RR-15 / RF-39)

**Como** Líder de Grupo,  
**quiero** poder unirme como reclutador participante a una requisición que otra Reclutadora ya tomó,  
**para** apoyar la cobertura colaborativa sin desplazar a los reclutadores existentes ni reiniciar el proceso.

**Criterios de aceptación:**
- Al unirme quedo registrado como participante en la requisición.
- No se desplaza a ningún reclutador previo ni retrocede el semáforo.
- Puedo salir de la requisición en cualquier momento; si soy el último en salir, vuelve a "Autorizada".
- El evento queda en el timeline de la requisición.

---

### HU-LG-06 — Ver cobertura por zona (RF-27)

**Como** Líder de Grupo,  
**quiero** ver el estado de cobertura de las requisiciones agrupadas por zona o hotel,  
**para** identificar zonas con baja cobertura y priorizar la asignación de Reclutadoras donde más se necesita.

**Criterios de aceptación:**
- Vista de cobertura desagregada por zona / hotel para las requisiciones activas del grupo.
- Indicador visual del porcentaje de cobertura por zona.
- Navegable desde el dashboard del grupo.

---

### HU-LG-07 — Generar y enviar reporte al Manager (RF-24 / RF-25)

**Como** Líder de Grupo,  
**quiero** generar un reporte consolidado del desempeño de mi grupo y enviarlo formalmente al Manager,  
**para** cumplir con la cadena de reporte y dar visibilidad del estado del equipo sin requerir que el Manager lo consulte manualmente.

**Criterios de aceptación:**
- RF-24: vista previa del reporte con métricas del grupo y gráficos antes de enviarlo.
- RF-25: acción de envío formal que genera una notificación al Manager con el reporte adjunto.
- El reporte es exportable en CSV o PDF.
- Solo el Manager de Reclutamiento es destinatario del envío formal.
