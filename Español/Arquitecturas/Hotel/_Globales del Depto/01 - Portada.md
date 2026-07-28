---
tags:
  - arquitectura
  - departamento/hotel
aliases:
  - Portada Hotel
  - Departamento de Hotel (Portada)
---

# GESTIÓN DE ROLES – DEPARTAMENTO DE HOTEL

**Plantilla de Roles y Perfiles de Usuario**

---

| Campo            | Contenido                  |
| ---------------- | -------------------------- |
| **Documento**    | Plantilla de Roles – Hotel |
| **Departamento** | Hotel                      |
| **Relacionado**  | PRD-HOTEL-01 · HU-HOTEL-01 |
| **Versión**      | 1.0                        |
| **Estado**       | En definición              |

---

## ROLES DEFINIDOS EN ESTE DOCUMENTO

| ID           | Rol · Descripción breve                                                                                                                                                                                                                       |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ROL-H-01** | 🦺 **Supervisor (SUP)** — Crea las requisiciones de personal, pone Stand-by, reporta colaborador (Rojo) y reporta accidentes laborales. Subordinado del Manager de Área.                                                                      |
| **ROL-H-02** | 🧑‍💼 **Manager de Área / Gerente de Departamento** — Crea, autoriza o rechaza requisiciones, gestiona Schedule y Timesheet, genera QR, reporta colaboradores y accidentes.                                                                   |
| **ROL-H-03** | 🧑‍✈️ **Manager General (GM)** — Máxima autoridad del lado del hotel. **Siempre existe** (en simple opera también como Manager de Área; en extendida supervisa Managers de Área). Tiene operación + visibilidad global + reportes ejecutivos. |
| **ROL-H-04** | ⚙️ **Sistema** — Automatización interna: numeración de requisición, cálculo de urgencia, asignación de Inspector, semáforos, journals, notificaciones.                                                                                        |
| **ROL-H-05** | 🛠️ **Administrador** — Gestión total del sistema: usuarios, catálogos, permisos. *(En pausa hasta que se estabilicen reglas de negocio.)*                                                                                                    |

---

## Jerarquías soportadas

| Jerarquía | Estructura | Roles aplicables |
|---|---|---|
| **Simple** | Manager General (también opera como Manager de Área) → Supervisor → Colaboradores de Oranje | ROL-H-01, ROL-H-03 (con doble rol) |
| **Extendida** | Manager General → Manager de Área (uno por depto) → Supervisor(es) → Colaboradores | ROL-H-01, ROL-H-02, ROL-H-03 |

> [!info]
> En jerarquía simple, el **Manager General opera también como Manager de Área** (misma persona, dos roles). En jerarquía extendida, son personas distintas: el Manager General supervisa y un Manager de Área se asigna por cada departamento operativo (Housekeeping, Alimentos, Mantenimiento, Front Desk — ver [[Departamentos del Hotel]]).

> [!important]
> **Manager de Área = Gerente de Departamento** (mismo rol técnico, dos nombres). Las responsabilidades en plataforma son las mismas.
