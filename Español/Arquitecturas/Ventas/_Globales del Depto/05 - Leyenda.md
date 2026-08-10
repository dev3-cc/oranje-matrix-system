---
tags:
  - arquitectura
  - departamento/ventas
aliases:
  - Leyenda Permisos Ventas
---

# LEYENDA – MATRIZ DE PERMISOS

---

## Símbolos / Colores

| Símbolo / Color   | Significado                                       | Aplica A          |
| ----------------- | ------------------------------------------------- | ----------------- |
| ✅ **CRUD**        | Acceso completo: Crear, Ver, Editar, Eliminar     | Administrador     |
| ➕ **Crear**       | Solo puede crear registros nuevos                 | BD / BDC          |
| 👁️ **Ver**       | Solo lectura / consulta                           | Todos los roles   |
| 📝 **C · E**      | Crear y Editar (sin eliminar)                     | BD / BDC          |
| ✓ **Aprobar**     | Puede aprobar la acción (validar T&C, conversión) | BDC               |
| ✗ **Rechazar**    | Puede rechazar la acción                          | BDC               |
| 🔍 **Investigar** | Puede revisar y resolver casos / estancamientos   | BDC               |
| ⚙️ **Auto**       | Acción ejecutada automáticamente por el sistema   | Sistema (backend) |
| —                 | Sin permiso – acceso denegado                     | Según contexto    |
| ⏸️                | En pausa (Admin)                                  | Administrador     |

---

## ROLES EN ESTE DOCUMENTO

| ID           | Rol                                        |
| ------------ | ------------------------------------------ |
| **ROL-V-01** | 🤝 Business Developer (BD)                 |
| **ROL-V-02** | 🧑‍💼 Business Developer Coordinator (BDC) |
| **ROL-V-03** | ⚙️ Sistema                                 |
| **ROL-V-04** | 🛠️ Administrador *(en pausa)*             |
