---
tags:
  - arquitectura
  - departamento/ventas
aliases:
  - Matriz de Permisos Ventas
---

# MATRIZ DE PERMISOS — DEPARTAMENTO DE VENTAS

**PRD-VENTAS-01 · Sistema de Gestión de Personal**

> [!info]
> El rol **Administrador (ROL-V-04)** se encuentra **en pausa** mientras se estabilizan las reglas de negocio.
> **Dos excepciones confirmadas:**
> 1. **(2026-08-19)** el CRUD de usuarios del sistema (`users:manage`), implementado en `identity` de `apps/api`. Gestionar cuentas es administración del sistema, no una regla del negocio.
> 2. **(2026-08-20)** asignar territorio a un BD (`territory:assign`), que comparte con el BDC por ser su superior jerárquico.
>
> El resto de CONFIGURACIÓN sigue por confirmar.

---

| Módulo                               | Funcionalidad / Acción                 | ROL-V-01 BD | ROL-V-02 BDC | ROL-V-03 Sistema | ROL-V-04 Admin   |
| ------------------------------------ | -------------------------------------- | ----------- | ------------ | ---------------- | ---------------- |
| **PIPELINE**                         | 👁️ Ver Pipeline (mi territorio)       | Ver         | Ver (todo)   | Auto             | ⏸️               |
|                                      | 👁️ Ver Pipeline global                | —           | Ver          | Auto             | ⏸️               |
|                                      | ➕ Identificar prospecto (Gris)         | Crear       | Crear        | —                | ⏸️               |
|                                      | 📝 Crear perfil del hotel (Azul Claro) | C · E       | C · E        | —                | ⏸️               |
|                                      | 📥 Descargar e imprimir el QR de ponche del hotel | Ver | Ver | — | ⏸️ |
|                                      | ➕ Regenerar el QR de ponche del hotel | Crear | Crear | — | ⏸️ |
|                                      | ➕ Registrar visita en frío             | Crear       | Crear        | —                | ⏸️               |
|                                      | ➕ Registrar intentos de contacto       | Crear       | Crear        | —                | ⏸️               |
|                                      | 📝 Avanzar a Amarillo (interés)        | C · E       | C · E        | —                | ⏸️               |
|                                      | 📝 Iniciar negociación (Rosa)          | C · E       | C · E        | —                | ⏸️               |
|                                      | 📝 Marcar rechazo (Rojo)               | C · E       | —            | —                | ⏸️               |
|                                      | 📝 Reactivar desde Rojo                | C · E       | —            | —                | ⏸️               |
|                                      | 📝 Marcar estancamiento (Café)         | C · E       | C · E        | —                | ⏸️               |
|                                      | 🔍 Desbloquear estancamiento           | —           | Investigar   | —                | ⏸️               |
|                                      | 📝 Reactivar desde Café                | —           | C · E        | —                | ⏸️               |
|                                      | 📝 Marcar cliente Negro                | —           | C · E        | —                | ⏸️               |
|                                      | 📝 Reactivar desde Negro               | —           | C · E        | —                | ⏸️               |
|                                      | 🗄️ Archivar ciclo comercial            | C · E       | C · E        | —                | ⏸️               |
| **PROPUESTAS**                       | 👁️ Ver propuestas                     | Ver         | Ver          | Auto             | ⏸️               |
|                                      | ➕ Elaborar Propuesta Personalizada     | Crear       | —            | —                | ⏸️               |
|                                      | 📤 Enviar propuesta al hotel           | C · E       | —            | —                | ⏸️               |
| **DOCUMENTOS T&C**                   | 👁️ Ver Documentos T&C                 | Ver         | Ver          | Auto             | ⏸️               |
|                                      | ➕ Crear Documento de T&C               | Crear       | Crear        | —                | ⏸️               |
|                                      | 📝 Editar Documento de T&C             | C · E       | C · E        | —                | ⏸️               |
|                                      | ✓ Validar T&C                          | —           | Aprobar      | —                | ⏸️               |
| **CONVERSIÓN**                       | ➕ Crear Usuario del Hotel              | —           | Crear        | —                | ⏸️               |
|                                      | ✓ Aprobar conversión (Rosa → Naranja)  | —           | Aprobar      | —                | ⏸️               |
|                                      | 🤖 Trigger Automático de Conversión    | —           | —            | Auto             | ⏸️               |
|                                      | 🤖 Cambio automático a Naranja         | —           | —            | Auto             | ⏸️               |
| **MI TERRITORIO** *(BD)*             | 👁️ Ver mi territorio / rutas          | Ver         | —            | Auto             | ⏸️               |
|                                      | 👁️ Ver prospectos por ruta            | Ver         | Ver (todos)  | Auto             | ⏸️               |
|                                      | 🗺️ Asignar territorio a un BD          | —           | Asignar      | —                | Asignar          |
| **MI EQUIPO** *(BDC)*                | 👁️ Ver BDs a cargo                    | —           | Ver          | —                | ⏸️               |
|                                      | 👁️ Ver métricas individuales por BD   | —           | Ver          | Auto             | ⏸️               |
|                                      | 📝 Comunicar con BD                    | —           | C · E        | —                | ⏸️               |
| **CLIENTES ACTIVOS**                 | 👁️ Ver clientes activos (referente)   | Ver         | Ver          | Auto             | ⏸️               |
| **REPORTES** *(BDC)*                 | ➕ Generar reporte de Ventas            | —           | Crear        | Auto             | ⏸️               |
|                                      | 📤 Enviar a dirección                  | —           | Crear        | —                | ⏸️               |
|                                      | 📅 Programar envío recurrente          | —           | C · E        | —                | ⏸️               |
|                                      | 👁️ Ver histórico de reportes          | —           | Ver          | Auto             | ⏸️               |
| **DASHBOARD**                        | 👁️ Ver KPIs personales                | Ver         | Ver          | Auto             | ⏸️               |
|                                      | 👁️ Ver KPIs globales del depto        | —           | Ver          | Auto             | ⏸️               |
| **SISTEMA** *(transversal)*          | 👁️ Recibir notificación               | Ver         | Ver          | Auto             | ⏸️               |
|                                      | ⚙️ Trazabilidad de cambios de status   | —           | —            | Auto             | ⏸️               |
|                                      | ⚙️ Enviar notificación automática      | —           | —            | Auto             | ⏸️               |
| **CONFIGURACIÓN** *(Admin EN PAUSA)* | 👥 CRUD de usuarios (`users:manage`)   | —           | —            | —                | Gestionar        |
|                                      | 🏨 Cuentas del hotel (`users:manage_hotel`) | —      | Crear (ficha del hotel) | — | Gestionar        |
|                                      | ⏸️ Editar catálogos                    | —           | —            | —                | ⏸️ Por confirmar |

---

## Notas clave

- **Conversión (RR-V-01):** SOLO el BDC aprueba. El BD nunca tiene acceso a esta acción.
- **Precondición de conversión (RR-V-02):** El sistema bloquea "Aprobar conversión" si no existe el Usuario del Hotel previamente creado.
- **Trigger Automático (RR-V-03):** 3 acciones en paralelo ejecutadas por Sistema, no por BD ni BDC.
- **Café y Negro (RR-V-04, RR-V-05):** exclusivos del BDC.
- **Rojo (RR-V-06):** gestión por BD.
- **Reactivaciones (RR-V-07):** siempre regresan a Azul Claro, no a Gris.
- **Archivar ≠ marcar Rojo o Negro:** Rojo, Negro y Café son estados reactivables; archivar es el acto explícito que cierra el ciclo y libera al hotel para uno nuevo. Un hotel en Naranja no se archiva: es cliente activo.
- **Asignación de territorio (confirmado 2026-08-20):** la hacen el **BDC** —manager directo del BD— y el **Administrador**, que está por encima del BDC. El vault decía que el BD tiene «su territorio asignado» y que el BDC «supervisa a los BD en su territorio y rutas asignadas», pero no decía quién lo asigna.
- **Post-Naranja (RR-V-12):** BD y BDC quedan como referentes comerciales sin permisos de operación en el módulo Hotel.
