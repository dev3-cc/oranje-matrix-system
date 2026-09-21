---
tags:
  - arquitectura
  - departamento/ventas
aliases:
  - Requerimientos por Módulo Ventas
---

# REQUERIMIENTOS POR MÓDULO — DEPARTAMENTO DE VENTAS

Agrupación de los RFs definidos en [[03 - Tabla de Requerimientos]] según el módulo del sidebar al que pertenecen.

---

## 📊 Dashboard

Vista de entrada del rol. Concentra KPIs personales (del BD) o consolidados del territorio (del BDC) y próximos seguimientos. No tiene RFs propios — los KPIs vienen de otros módulos.

| ID  | Requerimiento                                | Rol(es) | Prioridad |
| --- | -------------------------------------------- | ------- | --------- |
| —   | KPIs personales (BD: mi territorio)          | BD      | 🔴 Alta   |
| —   | KPIs del equipo y embudo de conversión (BDC) | BDC     | 🔴 Alta   |

---

## 📋 Pipeline (Prospectos)

Centro de operaciones del depto. Gestiona todos los prospectos a lo largo del Semáforo Onboarding (Gris → Azul Claro → Verde → Amarillo → Rosa → Naranja, más las ramas Rojo, Café y Negro). Incluye el ciclo completo de avance, rechazo, estancamiento y reactivación.

| ID      | Requerimiento                    | Rol(es)  | Prioridad |
| ------- | -------------------------------- | -------- | --------- |
| RF-V-01 | Identificar prospecto            | BD       | 🔴 Alta   |
| RF-V-02 | Crear perfil del hotel           | BD       | 🔴 Alta   |
| RF-V-03 | Registrar visita en frío         | BD       | 🟡 Media  |
| RF-V-06 | Registrar intentos de contacto   | BD       | 🟡 Media  |
| RF-V-07 | Avanzar a Amarillo (interés)     | BD       | 🔴 Alta   |
| RF-V-09 | Iniciar negociación (Rosa)       | BD + BDC | 🔴 Alta   |
| RF-V-15 | Gestionar rechazo (Rojo)         | BD       | 🟡 Media  |
| RF-V-16 | Reactivar prospecto desde Rojo   | BD       | 🟡 Media  |
| RF-V-17 | Marcar estancamiento (Café)      | BD / BDC | 🟡 Media  |
| RF-V-18 | Desbloquear estancamiento (Café) | BDC      | 🟡 Media  |
| RF-V-19 | Reactivar desde Café             | BDC      | 🟡 Media  |
| RF-V-20 | Marcar cliente Negro             | BDC      | 🟡 Media  |
| RF-V-21 | Reactivar desde Negro            | BDC      | 🟢 Baja   |
| RF-V-22 | Ver Pipeline                     | BD / BDC | 🔴 Alta   |

---

## 📝 Propuestas

Construcción y envío de la Propuesta Personalizada al hotel. Se elabora exclusivamente en status Verde (RR-V-09). Incluye plantillas, duplicado y envío formal por email.

| ID      | Requerimiento                    | Rol(es) | Prioridad |
| ------- | -------------------------------- | ------- | --------- |
| RF-V-04 | Elaborar Propuesta Personalizada | BD      | 🔴 Alta   |
| RF-V-05 | Enviar propuesta al hotel        | BD      | 🔴 Alta   |

---

## 📄 Contratos

Creación del Documento de Términos y Condiciones con los 5 campos obligatorios (Pay rate, Bill rate, Overtime, Festivos, Calendario — RR-V-10) y validación final por parte del BDC. Prerequisito para iniciar Rosa.

| ID      | Requerimiento          | Rol(es)  | Prioridad |
| ------- | ---------------------- | -------- | --------- |
| RF-V-08 | Crear Documento de T&C | BD / BDC | 🔴 Alta   |
| RF-V-10 | Validar T&C            | BDC      | 🔴 Alta   |

---

## ✅ Conversión *(exclusivo BDC)*

Cierre del onboarding. El BDC crea el Usuario del Hotel (precondición obligatoria — RR-V-02) y aprueba la conversión, lo que dispara el Trigger Automático y deja al hotel como cliente activo (Naranja).

| ID      | Requerimiento                | Rol(es) | Prioridad |
| ------- | ---------------------------- | ------- | --------- |
| RF-V-11 | Crear Usuario del Hotel      | BDC     | 🔴 Alta   |
| RF-V-12 | Aprobar conversión a cliente | BDC     | 🔴 Alta   |

---

## 🗺️ Mi Territorio *(exclusivo BD)*

Mapa interactivo de las rutas y zonas asignadas al BD, con pines de prospectos por status. Permite planear ruta del día, identificar nuevos prospectos en campo (con geolocalización automática desde mobile) y abrir el detalle de cualquier prospecto desde el mapa.

| ID      | Requerimiento     | Rol(es) | Prioridad |
| ------- | ----------------- | ------- | --------- |
| RF-V-23 | Ver Mi Territorio | BD      | 🔴 Alta   |

---

## 👥 Mi Equipo *(exclusivo BDC)*

Supervisión de los BDs a cargo del BDC. Incluye lista de BDs con métricas, detalle individual de desempeño y comunicación interna. Punto de entrada para reasignar prospectos o solicitar reportes específicos.

| ID      | Requerimiento                | Rol(es) | Prioridad |
| ------- | ---------------------------- | ------- | --------- |
| RF-V-24 | Ver Mi Equipo (BDs a cargo)  | BDC     | 🔴 Alta   |
| RF-V-25 | Métricas individuales por BD | BDC     | 🟡 Media  |

---

## 📈 Reportes *(exclusivo BDC)*

Reportes consolidados del territorio (Pipeline, Conversión, Desempeño, Café, Negro, Calidad, Ejecutivo) con vista previa, exportación CSV/PDF/Excel y envío recurrente a dirección.

| ID      | Requerimiento              | Rol(es) | Prioridad |
| ------- | -------------------------- | ------- | --------- |
| RF-V-26 | Generar reporte de Ventas  | BDC     | 🟡 Media  |
| RF-V-27 | Enviar reporte a dirección | BDC     | 🟡 Media  |

---

## 🏨 Clientes Activos

Vista post-Naranja del hotel. BD y BDC quedan como referentes comerciales (sin operación — RR-V-12). Incluye también la gestión del status Negro (cliente pausado) que es exclusiva del BDC.

| ID      | Requerimiento                              | Rol(es)  | Prioridad |
| ------- | ------------------------------------------ | -------- | --------- |
| RF-V-29 | Ver Clientes Activos (referente comercial) | BD / BDC | 🟡 Media  |

---

## ⚙️ Sistema (transversal — NO es módulo del sidebar)

Automatizaciones internas que el usuario no ve directamente pero ocurren detrás del flujo: el Trigger Automático de Conversión, el cambio de status a Naranja, la trazabilidad de cambios y las notificaciones.

| ID      | Requerimiento                     | Rol(es) | Prioridad |
| ------- | --------------------------------- | ------- | --------- |
| RF-V-13 | Trigger Automático de Conversión  | Sistema | 🔴 Alta   |
| RF-V-14 | Cambio automático a Naranja       | Sistema | 🔴 Alta   |
| RF-V-28 | Trazabilidad de cambios de status | Sistema | 🔴 Alta   |
| —       | Notificaciones automáticas        | Sistema | 🔴 Alta   |

---

## 🛠️ Configuración *(exclusivo Admin — EN PAUSA)*

> [!warning] Admin en pausa
> Estos requerimientos quedan documentados pero **no se aterrizan** en este alcance. Se retoman al final cuando las reglas de negocio del sistema estén estables.

| ID  | Requerimiento                        | Rol(es)       | Prioridad         |
| --- | ------------------------------------ | ------------- | ----------------- |
| —   | CRUD de usuarios del depto Ventas    | Administrador | ⏸️ Admin en pausa |
| —   | Editar catálogos (zonas, plantillas) | Administrador | ⏸️ Admin en pausa |

---

## ⚙️ No Funcionales (Transversal)

| ID       | Requerimiento              | Prioridad |
| -------- | -------------------------- | --------- |
| RNF-V-01 | Tiempo de respuesta < 2s   | 🔴 Alta   |
| RNF-V-02 | Disponibilidad 99.5%       | 🔴 Alta   |
| RNF-V-03 | Trazabilidad de acciones   | 🔴 Alta   |
| RNF-V-04 | Mobile prioritario para BD | 🔴 Alta   |

---

## 📜 Reglas de Negocio (resumen)

| ID      | Regla                                          | Prioridad |
| ------- | ---------------------------------------------- | --------- |
| RR-V-01 | Conversión exclusiva del BDC                   | 🔴 Alta   |
| RR-V-02 | Precondición — Usuario del Hotel               | 🔴 Alta   |
| RR-V-03 | Trigger Automático ejecuta 3 acciones          | 🔴 Alta   |
| RR-V-04 | Desbloqueo Café exclusivo del BDC              | 🔴 Alta   |
| RR-V-05 | Cliente Negro exclusivo del BDC                | 🔴 Alta   |
| RR-V-06 | Gestión de rechazo (Rojo) por el BD            | 🔴 Alta   |
| RR-V-07 | Reactivaciones siempre a Azul Claro            | 🔴 Alta   |
| RR-V-08 | Naranja habilita generación de requisiciones   | 🔴 Alta   |
| RR-V-09 | Propuesta Personalizada solo en Verde          | 🔴 Alta   |
| RR-V-10 | T&C — contenido obligatorio (5 campos)         | 🔴 Alta   |
| RR-V-11 | Trazabilidad de cambios de status              | 🔴 Alta   |
| RR-V-12 | Post-Naranja: referentes comerciales           | 🔴 Alta   |
| RR-V-15 | Contrato resulta del cierre exitoso en Rosa    | 🔴 Alta   |

> [!info]
> Ver el detalle completo de cada regla en [[07 - Reglas de Negocio]].

---

## 🔗 Integraciones

| ID      | Requerimiento                          | Prioridad |
| ------- | -------------------------------------- | --------- |
| RI-V-01 | Ventas ↔ Hotel                         | 🔴 Alta   |
| RI-V-02 | Ventas ↔ Reclutamiento                 | 🔴 Alta   |
| RI-V-03 | Ventas ↔ Inspección                    | 🔴 Alta   |
| RI-V-04 | Ventas ↔ Contrato                      | 🔴 Alta   |
| RI-V-05 | Ventas ↔ Email (Trigger de bienvenida) | 🔴 Alta   |
| RI-V-06 | Ventas ↔ QA                            | 🟡 Media  |
