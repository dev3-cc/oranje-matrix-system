---
tags:
  - arquitectura
  - departamento/hotel
aliases:
  - Leyenda Permisos Hotel
---

# LEYENDA – MATRIZ DE PERMISOS

---

## Símbolos / Colores

| Símbolo / Color   | Significado                                     | Aplica A                                          |
| ----------------- | ----------------------------------------------- | ------------------------------------------------- |
| ✅ **CRUD**        | Acceso completo: Crear, Ver, Editar, Eliminar   | Administrador                                     |
| ➕ **Crear**       | Solo puede crear registros nuevos               | Supervisor / Manager de Área                    |
| 👁️ **Ver**       | Solo lectura / consulta                         | Todos los roles                                   |
| 📝 **C · E**      | Crear y Editar (sin eliminar)                   | Supervisor / Manager de Área                    |
| ✓ **Aprobar**     | Puede aprobar la acción (autorizar requisición) | Manager de Área                                 |
| ✗ **Rechazar**    | Puede rechazar la acción                        | Manager de Área                                 |
| 🚨 **Reportar**   | Puede reportar colaborador o accidente          | Manager de Área (Rojo) / Supervisor (accidente) |
| 🔍 **Supervisar** | Visibilidad global con acción de escalamiento   | Manager General                                   |
| ⚙️ **Auto**       | Acción ejecutada automáticamente por el sistema | Sistema (backend)                                 |
| —                 | Sin permiso – acceso denegado                   | Según contexto                                    |
| ⏸️                | En pausa (Admin)                                | Administrador                                     |

---

## ROLES EN ESTE DOCUMENTO

| ID           | Rol                                                   |
| ------------ | ----------------------------------------------------- |
| **ROL-H-01** | 🦺 Supervisor (SUP)                                   |
| **ROL-H-02** | 🧑‍💼 Manager de Área / Gerente de Departamento       |
| **ROL-H-03** | 🧑‍✈️ Manager General (GM) — solo jerarquía extendida |
| **ROL-H-04** | ⚙️ Sistema                                            |
| **ROL-H-05** | 🛠️ Administrador *(en pausa)*                        |

---

## Notas

- **Manager de Área** y **Gerente de Departamento** son el mismo rol técnico. La diferencia es solo el alcance: en jerarquía simple cubre todo el hotel; en jerarquía extendida cubre solo su departamento (Housekeeping, Alimentos, Mantenimiento, Front Desk).
- El **Manager General** existe únicamente en jerarquía extendida. En jerarquía simple, el Manager de Área es la máxima autoridad del lado del hotel.
- Las reglas de negocio (RR-H-XX) están en [[07 - Reglas de Negocio]].
