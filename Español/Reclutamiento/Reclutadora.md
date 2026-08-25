---
tags:
  - modulo/reclutamiento
aliases:
  - Reclutadora
---

# Reclutadora

Rol operativo del módulo de [[Reclutamiento/Reclutamiento|Reclutamiento]]. Ejecuta el proceso de búsqueda, entrevista, validación y asignación de colaboradores a partir de una [[Requisición]].

## Responsabilidades

- Toma [[Requisición|requisiciones]] de la bandeja compartida, priorizada por el [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición|Semáforo de Urgencia]].
- Busca en la [[Pool de Colaboradores|pool]] a los colaboradores disponibles que cumplan la requisición.
- Consulta la [[Core/Módulos/Blacklist|Blacklist]] cuando un candidato se postula, para evitar volver a reclutar a alguien vetado.
- Realiza la entrevista inicial y captura la información básica del colaborador:
  - Edad
  - Género
  - Nombre completo
  - Domicilio
  - Teléfono
- Define en la entrevista las decisiones de Oranje sobre el perfil laboral del colaborador *(cambiado el 2026-08-22; antes las llenaba el propio colaborador en su alta)*:
  - Posición ([[Posiciones]])
  - Modalidad de contratación ([[Modalidades de Contratación]])
  - Nivel de inglés ([[Niveles de Inglés]])
  - Nivel de experiencia
- Valida y aprueba al colaborador después de que completa su alta en `/empleado`.
- Habilita el acceso del colaborador a los paneles.
- Consulta el [[Core/Módulos/Schedule|Schedule]] del hotel para ver las posiciones pendientes de cubrir.
- Asigna al colaborador al hotel y lo registra en su [[Core/Módulos/Schedule|Schedule]].
- Asigna temporalmente a colaboradores disponibles (transición amarillo → café en [[Semáforo del Colaborador]]).

## Relacionado

- [[Flujo de Reclutamiento]]
- [[Manager de Reclutamiento]]
- [[Líder de Grupo de Reclutadoras]]
- [[Requisición]]
- [[Pool de Colaboradores]]
- [[Core/Módulos/Blacklist|Blacklist]]
- [[Core/Módulos/Schedule|Schedule]]
- [[Semáforo del Colaborador]]
- [[Colaborador/Colaborador|Colaborador]]
- [[Posiciones]]
- [[Modalidades de Contratación]]
- [[Niveles de Inglés]]
