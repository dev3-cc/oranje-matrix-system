---
tags:
  - arquitectura
  - rol/lider-de-grupo
aliases:
  - Validaciones Líder de Grupo
---

# 4. VALIDACIONES — LÍDER DE GRUPO DE RECLUTADORAS

---

## Validaciones operativas (heredadas de Reclutadora)

| Caso                                                    | Comportamiento del Sistema                                  |
| ------------------------------------------------------- | ----------------------------------------------------------- |
| Campos obligatorios vacíos en formulario de colaborador | Bloquea envío y muestra mensaje junto a cada campo inválido |
| Documento de identidad ya existe                        | Bloquea creación                                            |
| Candidato en Blacklist                                  | Bloquea asignación con alerta visible                       |
| Asignar colaborador no validado                         | Bloquea asignación                                          |
| Asignar colaborador cuyo turno se **solapa** con otro ya asignado | Bloquea (regla RR-05). Si los turnos no se solapan, el mismo día en dos hoteles **sí se permite** |
| Marcar requisición cubierta sin 100%                    | Bloquea acción                                              |
| Tomar requisición ya tomada                             | No bloquea; pregunta "Esta requisición ya la trabajan N reclutadores. ¿Unirte como participante?" (RR-15) |
| Asignar un colaborador a una posición que otro reclutador ya cubrió | Bloquea solo ese slot; muestra "Posición ya cubierta" (gana la primera asignación; AC-23) |
| Unirse / asignar en una requisición ya cerrada (cubierta o parcial) | Bloquea acción; muestra "La requisición está cerrada"       |

---

## Validaciones exclusivas del Líder

| Caso                                      | Comportamiento del Sistema                                                   |
| ----------------------------------------- | ---------------------------------------------------------------------------- |
| Generar reporte sin rango de fechas       | Bloquea generación; muestra "Debe seleccionar rango de fechas"               |
| Enviar reporte sin métricas válidas       | Muestra "El reporte no contiene datos para el periodo seleccionado"          |
| Reasignar requisición a Reclutadora fuera del grupo | Bloquea acción; muestra "Solo puedes reasignar entre Reclutadoras de tu grupo" |
| Atender incidencia sin justificación      | Bloquea cierre; solicita comentario obligatorio                              |
| Acceso a métricas de otro grupo           | Bloquea acceso; muestra "Solo puedes ver métricas de tu grupo"               |
| Exportar reporte vacío                    | Bloquea exportación                                                          |
