---
tags:
  - arquitectura
  - departamento/reclutamiento
aliases:
  - Matriz de Permisos Reclutamiento
---

# MATRIZ DE PERMISOS — DEPARTAMENTO DE RECLUTAMIENTO

**PRD-RECL-01 · Sistema de Gestión de Personal**

> [!info]
> El rol **Administrador (ROL-05)** se encuentra **en pausa** mientras se estabilizan las reglas de negocio. Los permisos del Admin se completarán al final.

> [!info] Herencia por jerarquía (2026-09-14)
> El **Líder de Grupo** hereda todo lo de la **Reclutadora** y el **Manager** hereda lo de ambos: tomar o unirse a una requisición, marcarla en proceso, asignar al Self-Pick, validar al colaborador. Se retira la restricción de que el Manager solo supervisa y no toma requisiciones. El cierre de cobertura (RF-05) no cambia: la Reclutadora solicita y el Líder aprueba. Las celdas con «—» para el jefe donde el subordinado sí tiene la acción quedan **superadas por la herencia**: el jefe la tiene, con su propio alcance (D-09). Regla en [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]]; la tabla de herencia vive junto a los permisos en `apps/api/prisma/permissions.ts`.

---

| Módulo | Funcionalidad / Acción | ROL-01 Reclutadora | ROL-02 Líder de Grupo | ROL-03 Manager | ROL-04 Sistema | ROL-05 Admin |
|---|---|---|---|---|---|---|
| **REQUISICIÓN** | 👁️ Ver cola de Autorizadas | Ver | Ver | Ver | Auto | ⏸️ |
| | 🎯 Tomar requisición (Self-Pick colaborativo) | Crear (agrega participante) | Crear (agrega participante) | Crear | Auto | ⏸️ |
| | 🤝 Tomar/Unirse a requisición ya tomada | Crear | Crear | — | Auto | ⏸️ |
| | 📝 Salir de requisición tomada | C · E | C · E | C · E | Auto | ⏸️ |
| | 👁️ Ver reclutadores activos | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver Historial de la requisición | Ver | Ver | Ver | Auto | ⏸️ |
| | 📝 Marcar requisición en proceso | C · E | C · E | — | Auto | ⏸️ |
| | ✅ Marcar requisición como cubierta | C · E (solicita) | ✓ Aprobar (cierre) | Ver | Auto | ⏸️ |
| | 👁️ Ver vista global | — | Ver | Ver | Auto | ⏸️ |
| | ⚠️ Asignar manualmente (excepción) | — | — | C · E | — | ⏸️ |
| | 🚦 Calcular Semáforo de Urgencia | — | — | — | Auto | ⏸️ |
| | 🚦 Calcular Semáforo de Posiciones | — | — | — | Auto | ⏸️ |
| | 📝 Forzar cambio de semáforo | — | — | C · E | Auto | ⏸️ |
| **RECLUTAMIENTO** | 👁️ Ver Pool de Colaboradores | Ver | Ver | Ver | Auto | ⏸️ |
| | 🔍 Buscar / filtrar candidatos | Ver | Ver | Ver | Auto | ⏸️ |
| | ➕ Crear colaborador (alta Fase 1) | Crear | Crear | Crear | — | ⏸️ |
| | 📝 Editar colaborador | C · E | C · E | C · E | — | ⏸️ |
| | 🗑️ Eliminar colaborador del Pool | Eliminar | Eliminar | Eliminar | — | ⏸️ |
| | 📝 Validar alta en app (Fase 2) | C · E | C · E | — | Auto | ⏸️ |
| | ➕ Habilitar accesos | Crear | Crear | — | Auto | ⏸️ |
| | ➕ Registrar entrevista | Crear | Crear | — | Auto | ⏸️ |
| | 👁️ Ver historial de entrevistas | Ver | Ver | Ver | Auto | ⏸️ |
| | ➕ Asignar colaborador a hotel | Crear | Crear | C · E | Auto | ⏸️ |
| | ➕ Asignar al Schedule | Crear | Crear | C · E | Auto | ⏸️ |
| | 📝 Reasignar colaborador | C · E | C · E | C · E | — | ⏸️ |
| | 📝 Desasignar colaborador | C · E | C · E | C · E | — | ⏸️ |
| **BLACKLIST** | 👁️ Consultar Blacklist | Ver | Ver | Ver | Auto | ⏸️ |
| | ➕ Agregar a Blacklist | Crear | Crear | Crear | — | ⏸️ |
| | 🔍 Resolver disputa | — | — | — (Inspector de zona) | — | ⏸️ |
| | 📝 Remover de Blacklist | — | — | — (solo el Administrador) | — | ⏸️ |
| **MI GRUPO** *(Líder)* | 👁️ Ver Reclutadoras del grupo | — | Ver | — | — | ⏸️ |
| | 👁️ Ver métricas individuales | — | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver carga detallada de Reclutadora | — | Ver | — | — | ⏸️ |
| | 📝 Reasignar requisición a Reclutadora | — | C · E | — | — | ⏸️ |
| | 📝 Marcar disponibilidad de Reclutadora | — | C · E | — | — | ⏸️ |
| **MI EQUIPO** *(Manager)* | 👁️ Ver Líderes + Reclutadoras | — | — | Ver | — | ⏸️ |
| | ➕ Dar de alta Líder/Reclutadora | — | — | Crear | — | ⏸️ |
| | 📝 Editar usuario del depto | — | — | C · E | — | ⏸️ |
| | 📝 Mover Reclutadora a otro Líder | — | — | C · E | — | ⏸️ |
| **INCIDENCIAS** *(Manager)* | ✓ Resolver incidencia | — | — | Aprobar | — | ⏸️ |
| | 🚨 Escalar a comercial | — | C · E | C · E | — | ⏸️ |
| **REPORTES** | ➕ Generar reporte del grupo | — | Crear | Crear | Auto | ⏸️ |
| | ✅ Enviar reporte al Manager | — | Aprobar | — | — | ⏸️ |
| | 👁️ Ver cobertura individual | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver cobertura por zona | — | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver cobertura global | — | — | Ver | Auto | ⏸️ |
| **DASHBOARD** | 👁️ Ver KPIs personales | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver KPIs de grupo | — | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver KPIs globales | — | — | Ver | Auto | ⏸️ |
| **SISTEMA** *(transversal)* | 👁️ Recibir notificación | Ver | Ver | Ver | Auto | ⏸️ |
| | ⚙️ Enviar notificación automática | — | — | — | Auto | ⏸️ |
| **CONFIGURACIÓN** *(Admin EN PAUSA)* | ⏸️ Configurar alertas | — | — | — | — | ⏸️ Por confirmar |
| | ⏸️ CRUD de usuarios | — | — | — | — | ⏸️ Por confirmar |
| | ⏸️ Editar catálogos | — | — | — | — | ⏸️ Por confirmar |

---

## Cambios respecto a la versión anterior

- **Requisición colaborativa** — `Tomar requisición (Self-Pick colaborativo)` ahora agrega un reclutador participante (no es exclusivo) y `Liberar` pasa a ser `Salir`. Se añaden `Tomar/Unirse a requisición ya tomada` (Reclutadora/Líder), `Ver reclutadores activos` y `Ver Historial de la requisición` (RR-15 y RR-16).
- **Blacklist** — La columna `Agregar a Blacklist` es `Crear` para los 3 roles del depto (Reclutadora, Líder, Manager). **Remover NO es de este departamento:** solo un perfil de Administrador (`ROL-ADM-01`) levanta el veto *(cambiado el 2026-08-13; antes no existía la acción)*. Las disputas (estado Rojo) las resuelve el **Inspector de zona**, no el Manager. Las filas "Resolver disputa" y "Remover de Blacklist" quedan sin permiso para los tres roles del depto (RR-03 actualizado).
- **Submódulos reorganizados** — Pool, Entrevistas y Asignación quedan dentro del módulo **RECLUTAMIENTO**. La toma de requisición y los semáforos quedan dentro del módulo **REQUISICIÓN**.
- **Notificaciones** ya no es módulo del sidebar — pasó a sección "Sistema (transversal)".
- **Schedule** ya no es módulo aparte — es vista contextual dentro del proceso de asignar.
- **Admin en pausa** — todas las celdas del ROL-05 marcadas con ⏸️.
