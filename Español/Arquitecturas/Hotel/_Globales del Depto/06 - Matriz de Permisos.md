---
tags:
  - arquitectura
  - departamento/hotel
aliases:
  - Matriz de Permisos Hotel
---

# MATRIZ DE PERMISOS — DEPARTAMENTO DE HOTEL

**PRD-HOTEL-01 · Sistema de Gestión de Personal**

> [!info]
> El rol **Administrador (ROL-H-05)** se encuentra **en pausa** mientras se estabilizan las reglas de negocio. Los permisos del Admin se completarán al final.

> [!note]
> El **Manager General (ROL-H-03)** existe **siempre** (jerarquía simple y extendida). En jerarquía simple, opera también como Manager de Área (misma persona, dos roles).

> [!info] Herencia por jerarquía (2026-09-14)
> El **Manager de Área** hereda todo lo del **Supervisor** (acotado a su departamento) y el **Manager General** hereda lo de ambos (todo el hotel): revisar ponches, marcas manuales, enviar la semana, reportar accidentes. **AUDITORÍAS queda fuera de la herencia**: solo el Supervisor audita, corrige y ve; sus Managers no la ven (decisión de Hugo, 2026-09-14). Las celdas con «—» para el jefe donde el subordinado sí tiene la acción quedan **superadas por la herencia**: el jefe la tiene, con su propio alcance (D-09). Regla en [[Core/Módulos/Reglas de Negocio#Herencia por jerarquía|Reglas de Negocio · Herencia por jerarquía]]; la tabla de herencia vive junto a los permisos en `apps/api/prisma/permissions.ts`.

---

| Módulo | Funcionalidad / Acción | ROL-H-01 Supervisor | ROL-H-02 Manager de Área | ROL-H-03 Manager General | ROL-H-04 Sistema | ROL-H-05 Admin |
|---|---|---|---|---|---|---|
| **REQUISICIONES** | 👁️ Ver mis requisiciones | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver vista global del hotel | — | Solo su depto | Ver global | Auto | ⏸️ |
| | ➕ Crear requisición | Crear | Crear | Crear | — | ⏸️ |
| | 📝 Editar borrador | C · E | C · E | C · E | — | ⏸️ |
| | 📤 Enviar a autorización | Crear | Crear | Crear | — | ⏸️ |
| | ✓ Autorizar requisición | — | Aprobar | Aprobar | — | ⏸️ |
| | ✗ Rechazar con observaciones | — | Rechazar | Rechazar | — | ⏸️ |
| | 🗑️ Eliminar requisición vacía | Crear | Crear | Crear | Auto | ⏸️ |
| | 🗑️ Eliminar su borrador (con posiciones) | C · E | C · E | C · E | — | ⏸️ |
| | 🗑️ Eliminar requisición Autorizada / En proceso / Cubierta parcial | — | C · E (su depto) | C · E (cualquiera) | Auto | ⏸️ |
| | 🚦 Calcular Semáforo de Urgencia | — | — | — | Auto | ⏸️ |
| | 🤖 Asignar Inspector por zona | — | — | — | Auto | ⏸️ |
| | 💬 Comentar al expediente | — | — | C · E | — | ⏸️ |
| | 🚨 Escalar requisición demorada | — | — | Supervisar | — | ⏸️ |
| **SCHEDULE** | 👁️ Ver Schedule del depto | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver Schedule global del hotel | — | — | Ver | Auto | ⏸️ |
| | 📝 Editar Schedule semanal | — | C · E | C · E | — | ⏸️ |
| | 🔄 Reflejar posiciones autorizadas | — | — | — | Auto | ⏸️ |
| | 🔔 Sugerir refuerzo de personal | Crear | Crear | — | — | ⏸️ |
| | 📥 Exportar Schedule | Ver | Ver | Ver | — | ⏸️ |
| **AUDITORÍAS** | ✅ Auditar presentación o ambiente de mi hotel | Crear | — | — | — | ⏸️ |
| | ✏️ Corregir una auditoría ya guardada | C · E | — | — | — | ⏸️ |
| | 👁️ Ver auditorías de mi hotel | Ver | — | — | Auto | ⏸️ |
| **TIMESHEET** | 👁️ Ver Timesheet del depto | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver Timesheet global del hotel | — | — | Ver | Auto | ⏸️ |
| | 👁️ Revisar ponches y resolver anómalos | Revisar | Revisar | Revisar | Auto | ⏸️ |
| | ✓ Aprobar horas trabajadas | — | Aprobar | Aprobar | — | ⏸️ |
| | 📝 Corregir ponche | — | C · E | C · E | Auto | ⏸️ |
| | ➕ Registrar ponche manual | Crear | Crear | Crear | — | ⏸️ |
| | 📥 Descargar e imprimir el QR de ponche (su hotel) | Ver | Ver | Ver | — | ⏸️ |
| | ➕ Regenerar el QR de ponche (su hotel) | Crear | Crear | Crear | — | ⏸️ |
| | 🚦 Calcular Indicador de Cumplimiento | — | — | — | Auto | ⏸️ |
| | ⏰ Ver Indicador de Lunch Extendido | — | — | — | Auto | ⏸️ |
| | 📥 Exportar Timesheet | Ver | Ver | Ver | — | ⏸️ |
| **MI PERSONAL** | 👁️ Ver colaboradores asignados | Ver | Ver | Ver | Auto | ⏸️ |
| | 🩷 Poner en Stand-by (Rosa) | C · E | C · E | C · E | — | ⏸️ |
| | 🔴 Reportar colaborador (Rojo) | Reportar | Reportar | Reportar | — | ⏸️ |
| | 👁️ Ver historial del colaborador | Ver | Ver | Ver | Auto | ⏸️ |
| **ACCIDENTES** | 👁️ Ver accidentes del depto | Ver | Ver | Ver | Auto | ⏸️ |
| | ➕ Crear tarjeta de accidente — Escenario A | Crear | Crear | Crear | — | ⏸️ |
| | ➕ Crear tarjeta de accidente — Escenario B | Crear | Crear | Crear | — | ⏸️ |
| | 📷 Capturar evidencia presencial | C · E | C · E | C · E | — | ⏸️ |
| | 🤖 Notificar al Inspector | — | — | — | Auto | ⏸️ |
| **MI EQUIPO DEL HOTEL** *(GM)* | 👁️ Ver Gerentes de Departamento | — | — | Ver | — | ⏸️ |
| | 👁️ Ver Supervisores del hotel | — | — | Ver | — | ⏸️ |
| | 💬 Comunicar con Gerente / SUP | — | — | C · E | — | ⏸️ |
| | 📊 Solicitar reporte específico | — | — | Crear | — | ⏸️ |
| **REPORTES** *(GM)* | ➕ Generar reporte ejecutivo | — | — | Crear | Auto | ⏸️ |
| | 📤 Enviar a dirección | — | — | Crear | — | ⏸️ |
| | 📅 Programar envío recurrente | — | — | C · E | — | ⏸️ |
| | 👁️ Ver histórico de reportes | — | — | Ver | Auto | ⏸️ |
| **DASHBOARD** | 👁️ Ver KPIs personales | Ver | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver KPIs del depto | — | Ver | Ver | Auto | ⏸️ |
| | 👁️ Ver KPIs globales del hotel | — | — | Ver | Auto | ⏸️ |
| **SISTEMA** *(transversal)* | 👁️ Recibir notificación | Ver | Ver | Ver | Auto | ⏸️ |
| | ⚙️ Enviar notificación automática | — | — | — | Auto | ⏸️ |
| | 🔢 Numeración automática de requisiciones | — | — | — | Auto | ⏸️ |
| **CUENTAS DEL HOTEL** | 🏨 Invitar cuentas de su hotel (`users:invite_hotel`) | — | Crear | Crear | — | Crear |
| **CONFIGURACIÓN** *(Admin EN PAUSA)* | ⏸️ Configurar alertas | — | — | — | — | ⏸️ Por confirmar |
| | ⏸️ CRUD de usuarios | — | — | — | — | ⏸️ Por confirmar |
| | ⏸️ Editar catálogos | — | — | — | — | ⏸️ Por confirmar |

---

## Notas clave

- **Crear requisición:** ahora pueden los **3 roles** (Supervisor, Manager de Área, Manager General).
- **Autorización (RR-H-02):** Manager de Área **o** Manager General. El Supervisor NO puede autorizar.
- **Eliminar requisición (RR-H-08):** en Borrador (Verde manzana) la elimina **quien la creó** (cualquiera de los 3 roles), sin motivo. En Autorizada / En proceso / Cubierta parcial, la elimina el **Manager de Área** (solo las de su departamento) o el **Manager General** (cualquiera del hotel), con **motivo obligatorio** y solo sin asignaciones activas. Cubierta totalmente no se elimina.
- **Aprobación de horas (D-09):** el **Manager de Área** aprueba las de **su** departamento y el **Manager General** las de **cualquiera** — la misma pareja que autoriza requisiciones. El **Supervisor revisa y resuelve anómalos, pero no aprueba**: son dos pasos con responsables distintos. Sin aprobación, las horas **quedan en espera**: sin pago, sin factura, sin escalamiento y sin aprobación automática.
- **Ponche manual:** los **3 roles** pueden registrarlo, para el caso en que el GPS rechace la ubicación.
- **Reportar colaborador / Rojo (RR-H-10):** los **3 roles** pueden reportar (compartido).
- **Stand-by / Rosa (RR-H-11):** compartido entre los **3 roles**.
- **Reportar accidentes (RR-H-19):** los **3 roles** pueden reportar (Escenarios A y B).
- **Indicador de Lunch Extendido (RR-H-15):** restringido para todos los roles del Hotel — solo lo ven Inspector, Coordinador de Inspección y Manager de Reclutamiento.
- **Manager General:** tiene **operación + supervisión**. Comparte todas las acciones operativas con Manager de Área, más funciones exclusivas ejecutivas (visibilidad global, reportes a dirección, supervisión de Managers de Área).
- **En jerarquía simple:** Manager General opera también como Manager de Área (misma persona, dos roles).
- **Invitar cuentas del hotel:** el **Manager de Área** y el **Manager General** invitan gente de su hotel; el **Supervisor no invita**, y por eso `users:invite_hotel` **no se hereda** (si lo hiciera, el jefe tomaría la regla de alcance del subordinado). Lo que el hotel propone para un **rol gerencial** queda **pendiente** de que Oranje lo confirme (Administrador o BDC); el Manager de Área solo puede proponer un Manager General si el hotel **no tiene uno activo** — el caso de la rotación. Regla en [[Core/Módulos/Reglas de Negocio#Cuentas del hotel|Reglas de Negocio · Cuentas del hotel]].
