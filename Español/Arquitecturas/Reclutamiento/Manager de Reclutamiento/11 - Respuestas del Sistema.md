---
tags:
  - arquitectura
  - rol/manager-reclutamiento
aliases:
  - Respuestas Sistema Manager
---

# 7. RESPUESTAS DEL SISTEMA — MANAGER

---

| Evento                                        | Respuesta del Sistema                                                           |
| --------------------------------------------- | ------------------------------------------------------------------------------- |
| Manager toma requisición personal             | Registra intervención excepcional en log; mueve a "Mis Requisiciones"           |
| Manager asigna manualmente a Reclutadora      | Notifica a la Reclutadora con contexto; queda en log con motivo                 |
| Manager fuerza cambio de semáforo             | Aplica cambio con log auditable; notifica a involucrados                        |
| Manager agrega reclutador a requisición       | Lo suma como participante sin desplazar a otros; notifica a los participantes activos; registra el evento en el Historial |
| Reclutador participante asigna colaborador a una posición | Marca la posición como cubierta (lock de posición/slot); registra la asignación como evento del timeline con autor y fecha |
| Reclutador participante desasigna colaborador | Libera la posición para reasignar; registra la desasignación como evento del timeline con autor y fecha |
| Reclutador sale de la requisición             | Lo retira solo a él; sigue "En proceso" si quedan otros reclutadores; vuelve a "Autorizada" solo al salir el último; registra el evento en el Historial |
| Manager agrega a Blacklist                    | Colaborador pasa a Negro; notifica al colaborador; bloquea futuras asignaciones mientras el veto siga vigente |
| Manager da de alta Líder                      | Crea cuenta; envía credenciales por email; aparece en Mi Equipo                 |
| Manager da de alta Reclutadora                | Crea cuenta y asigna al Líder; notifica al Líder asignado                       |
| Manager mueve Reclutadora a otro Líder        | Notifica a ambos Líderes; actualiza relación                                    |
| Manager marca usuario como inactivo           | Bloquea acceso del usuario; preserva historial                                  |
| Manager resuelve incidencia                   | Cierra caso; notifica a partes involucradas                                     |
| Manager escala a Dirección                    | Envía caso completo al Director; queda registro                                 |
| Manager genera reporte global                 | Compila datos; muestra vista previa                                             |
| Manager exporta reporte                       | Descarga archivo solicitado                                                     |
| Manager recibe alerta de requisición varada   | Aparece en Bandeja de Acción del Dashboard                                      |
| Manager recibe escalamiento de Líder          | Notifica con contexto y abre el caso en Incidencias                             |
| Manager recibe escalamiento de Inspector      | Notifica con investigación adjunta                                              |
| Sistema detecta requisición urgente sin tomar | Alerta automática al Manager tras N horas configuradas                          |
| Sistema detecta sobrecarga de Reclutadora     | Alerta al Manager y al Líder correspondiente                                    |
