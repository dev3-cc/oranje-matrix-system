---
tags:
  - arquitectura
  - departamento/ventas
aliases:
  - Tabla de Requerimientos Ventas
---

# TABLA DE REQUERIMIENTOS — DEPARTAMENTO DE VENTAS

**Sistema de Gestión de Personal · PRD-VENTAS-01 · PASO 5 DEL DISCOVERY**

---

| Campo               | Contenido                                                        |
| ------------------- | ---------------------------------------------------------------- |
| **Documento**       | Tabla de Requerimientos – Ventas                                 |
| **Paso en proceso** | 5 de 6 · Discovery → Requerimientos                              |
| **Relacionado a**   | PRD-VENTAS-01 · Roles (ROL-V-01 a ROL-V-04) · Matriz de Permisos |
| **Departamento**    | Ventas                                                           |
| **Versión**         | 1.0                                                              |
| **Estado**          | En definición                                                    |

---

## TIPOS DE REQUERIMIENTO

| Prefijo | Tipo | Descripción |
|---|---|---|
| **RF** | Requerimiento Funcional | Define QUÉ hace el sistema |
| **RNF** | Requerimiento No Funcional | Define CÓMO lo hace — rendimiento, seguridad, usabilidad |
| **RR** | Requerimiento de Negocio | Define POR QUÉ existe — reglas, políticas |
| **RI** | Requerimiento de Integración | Define CON QUÉ conecta |

---

## TABLA DE REQUERIMIENTOS

| ID       | Tipo | Módulo            | Requerimiento                              | Descripción Detallada                                                                  | Rol(es)                          | Criterio de Aceptación                                      | Prioridad | Estado      | Notas             |
| -------- | ---- | ----------------- | ------------------------------------------ | -------------------------------------------------------------------------------------- | -------------------------------- | ----------------------------------------------------------- | --------- | ----------- | ----------------- |
| RF-V-01  | RF   | Pipeline          | Identificar prospecto                      | El BD identifica un hotel en su territorio y crea un registro inicial.                 | BD                               | Hotel queda en status Gris con datos mínimos.               | 🔴 Alta   | ⬜ Pendiente | Status Gris       |
| RF-V-02  | RF   | Pipeline          | Crear perfil del hotel                     | El BD recopila datos: nombre, email, teléfono, contacto, necesidad. Pasa a Azul Claro. | BD                               | Perfil completo con datos obligatorios.                     | 🔴 Alta   | ⬜ Pendiente | Status Azul Claro |
| RF-V-03  | RF   | Pipeline          | Registrar visita en frío                   | El BD documenta su visita al hotel (fecha, contacto, resultado).                       | BD                               | Registro queda en histórico del prospecto.                  | 🟡 Media  | ⬜ Pendiente | —                 |
| RF-V-04  | RF   | Propuestas        | Elaborar Propuesta Personalizada           | El BD crea la propuesta con servicios, precios, condiciones (RR-V-09).                 | BD                               | Propuesta queda como borrador.                              | 🔴 Alta   | ⬜ Pendiente | Status Verde      |
| RF-V-05  | RF   | Propuestas        | Enviar propuesta al hotel                  | El BD adjunta y envía la propuesta. Pasa a Verde.                                      | BD                               | Notificación al BD; envío registrado.                       | 🔴 Alta   | ⬜ Pendiente | Status Verde      |
| RF-V-06  | RF   | Pipeline          | Registrar intentos de contacto             | El BD documenta cada intento (llamada, email, visita) con resultado.                   | BD                               | Histórico de contactos visible en el detalle del prospecto. | 🟡 Media  | ⬜ Pendiente | —                 |
| RF-V-07  | RF   | Pipeline          | Avanzar a Amarillo (interés del hotel)     | Al recibir interés, BD pasa el prospecto a Amarillo.                                   | BD                               | Status cambia a Amarillo con comentario obligatorio.        | 🔴 Alta   | ⬜ Pendiente | RR-V-11           |
| RF-V-08  | RF   | Contratos         | Crear Documento de T&C                     | BD o BDC crea el documento con: Pay rate, Bill rate, Overtime, Festivos, Calendario.   | BD / BDC                         | Documento con campos obligatorios completos (RR-V-10).      | 🔴 Alta   | ⬜ Pendiente | Status Amarillo   |
| RF-V-09  | RF   | Pipeline          | Iniciar negociación (Rosa)                 | BD + BDC inician negociación formal con el hotel.                                      | BD + BDC                         | Status cambia a Rosa.                                       | 🔴 Alta   | ⬜ Pendiente | —                 |
| RF-V-10  | RF   | Contratos         | Validar T&C                                | El BDC revisa el T&C y da el sí final.                                                 | BDC                              | Validación queda en log con autor y fecha.                  | 🔴 Alta   | ⬜ Pendiente | RR-V-15           |
| RF-V-11  | RF   | Conversión        | Crear Usuario del Hotel                    | Precondición obligatoria antes de aprobar conversión.                                  | BDC                              | Usuario creado con datos básicos.                           | 🔴 Alta   | ⬜ Pendiente | RR-V-02           |
| RF-V-12  | RF   | Conversión        | Aprobar conversión a cliente               | El BDC da el sí final y aprueba; dispara el Trigger Automático.                        | BDC                              | Status pasa a Naranja; Trigger ejecuta 3 acciones.          | 🔴 Alta   | ⬜ Pendiente | RR-V-01, RR-V-03  |
| RF-V-13  | RF   | Sistema (transv.) | Trigger Automático de Conversión           | 3 acciones en paralelo: email de bienvenida, notif al BD, hotel sale de prospectos.    | Sistema                          | Las 3 acciones ejecutan en <1 min.                          | 🔴 Alta   | ⬜ Pendiente | RR-V-03           |
| RF-V-14  | RF   | Sistema (transv.) | Cambio automático a Naranja                | Tras Trigger, el sistema marca al hotel como activo (Naranja).                         | Sistema                          | Hotel habilitado para generar requisiciones.                | 🔴 Alta   | ⬜ Pendiente | RR-V-08           |
| RF-V-15  | RF   | Pipeline          | Gestionar rechazo (Rojo)                   | Cuando el hotel rechaza, BD marca como Rojo con motivo.                                | BD                               | Status pasa a Rojo con comentario.                          | 🟡 Media  | ⬜ Pendiente | RR-V-06           |
| RF-V-16  | RF   | Pipeline          | Reactivar prospecto desde Rojo             | BD decide reactivar y vuelve a Azul Claro.                                             | BD                               | Status regresa a Azul Claro.                                | 🟡 Media  | ⬜ Pendiente | RR-V-07           |
| RF-V-17  | RF   | Pipeline          | Marcar estancamiento (Café)                | Cuando un prospecto se estanca, BD/BDC lo marca como Café.                             | BD / BDC                         | Status pasa a Café con motivo.                              | 🟡 Media  | ⬜ Pendiente | RR-V-04           |
| RF-V-18  | RF   | Pipeline          | Desbloquear estancamiento (Café)           | El BDC investiga y da solución para retomar.                                           | BDC                              | Acción queda en log.                                        | 🟡 Media  | ⬜ Pendiente | RR-V-04           |
| RF-V-19  | RF   | Pipeline          | Reactivar desde Café                       | El BDC reactiva y vuelve a Azul Claro.                                                 | BDC                              | Status regresa a Azul Claro.                                | 🟡 Media  | ⬜ Pendiente | RR-V-07           |
| RF-V-20  | RF   | Pipeline          | Marcar cliente Negro (pausa/inactivo)      | Cuando un cliente activo deja de operar, BDC lo marca como Negro.                      | BDC                              | Status pasa a Negro con motivo.                             | 🟡 Media  | ⬜ Pendiente | RR-V-05           |
| RF-V-21  | RF   | Pipeline          | Reactivar desde Negro                      | El BDC reactiva el cliente.                                                            | BDC                              | Status regresa a Azul Claro.                                | 🟢 Baja   | ⬜ Pendiente | RR-V-07           |
| RF-V-22  | RF   | Pipeline          | Ver Pipeline (todos los prospectos)        | Vista de los prospectos por status, con filtros.                                       | BD (mi territorio) / BDC (todos) | Lista paginada y filtrable.                                 | 🔴 Alta   | ⬜ Pendiente | —                 |
| RF-V-23  | RF   | Mi Territorio     | Ver Mi Territorio (BD)                     | El BD ve sus rutas y zonas asignadas con prospectos por status.                        | BD                               | Mapa o lista por ruta/zona.                                 | 🔴 Alta   | ⬜ Pendiente | —                 |
| RF-V-24  | RF   | Mi Equipo         | Ver Mi Equipo (BDC)                        | El BDC ve sus BDs a cargo con métricas.                                                | BDC                              | Lista con métricas individuales.                            | 🔴 Alta   | ⬜ Pendiente | Exclusivo BDC     |
| RF-V-25  | RF   | Mi Equipo         | Métricas individuales por BD               | Detalle de desempeño de cada BD.                                                       | BDC                              | Cobertura, conversión, tiempo promedio.                     | 🟡 Media  | ⬜ Pendiente | —                 |
| RF-V-26  | RF   | Reportes          | Generar reporte de Ventas                  | Reportes consolidados (pipeline, conversión, desempeño).                               | BDC                              | Exportable CSV/PDF/Excel.                                   | 🟡 Media  | ⬜ Pendiente | —                 |
| RF-V-27  | RF   | Reportes          | Enviar reporte a dirección                 | Envío formal del reporte ejecutivo.                                                    | BDC                              | Histórico de envíos.                                        | 🟡 Media  | ⬜ Pendiente | —                 |
| RF-V-28  | RF   | Sistema (transv.) | Trazabilidad de cambios de status          | Todo cambio queda registrado con fecha, responsable y comentario.                      | Sistema                          | Log auditable accesible desde el detalle del prospecto.     | 🔴 Alta   | ⬜ Pendiente | RR-V-11           |
| RF-V-29  | RF   | Clientes Activos  | Ver Clientes Activos (referente comercial) | Vista post-Naranja: BD/BDC son referentes, sin operación.                              | BD / BDC                         | Lista de clientes con datos comerciales.                    | 🟡 Media  | ⬜ Pendiente | RR-V-12           |
| RNF-V-01 | RNF  | Global            | Tiempo de respuesta                        | Búsquedas y consultas no superan 2s.                                                   | Todos                            | Test de carga.                                              | 🔴 Alta   | ⬜ Pendiente | —                 |
| RNF-V-02 | RNF  | Global            | Disponibilidad                             | 99.5% mensual.                                                                         | Todos                            | SLA documentado.                                            | 🔴 Alta   | ⬜ Pendiente | —                 |
| RNF-V-03 | RNF  | Seguridad         | Trazabilidad                               | Todas las acciones críticas en log.                                                    | Todos                            | Log con autor, fecha, motivo.                               | 🔴 Alta   | ⬜ Pendiente | RR-V-11           |
| RNF-V-04 | RNF  | Usabilidad        | Mobile para BD                             | BD opera desde mobile en visitas.                                                      | BD                               | Diseño responsive desde 5".                                 | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-01  | RR   | Negocio           | Conversión exclusiva del BDC               | Solo BDC aprueba conversión.                                                           | Sistema                          | Validación de permisos.                                     | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-02  | RR   | Negocio           | Precondición — Usuario del Hotel           | Sin Usuario del Hotel, no se puede aprobar conversión.                                 | Sistema                          | Bloqueo de botón.                                           | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-03  | RR   | Negocio           | Trigger Automático ejecuta 3 acciones      | Email + Notif + Salir de prospectos en paralelo.                                       | Sistema                          | Las 3 ejecutan <1 min.                                      | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-04  | RR   | Negocio           | Desbloqueo Café exclusivo del BDC          | Solo BDC desbloquea.                                                                   | Sistema                          | Validación de permisos.                                     | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-05  | RR   | Negocio           | Cliente Negro exclusivo del BDC            | Solo BDC gestiona.                                                                     | Sistema                          | Validación de permisos.                                     | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-07  | RR   | Negocio           | Reactivaciones a Azul Claro                | Siempre regresan a Azul Claro.                                                         | Sistema                          | Validación al cambiar de status.                            | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-08  | RR   | Negocio           | Naranja habilita requisiciones             | Único status que habilita el módulo Hotel.                                             | Sistema                          | Bloqueo en Hotel si no es Naranja.                          | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-10  | RR   | Negocio           | T&C — contenido obligatorio                | Pay/Bill/Overtime/Festivos/Calendario.                                                 | Sistema                          | Validación al crear T&C.                                    | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-11  | RR   | Negocio           | Trazabilidad de cambios                    | Todo queda en log.                                                                     | Sistema                          | Validación a nivel sistema.                                 | 🔴 Alta   | ⬜ Pendiente | —                 |
| RR-V-12  | RR   | Negocio           | Post-Naranja: referentes comerciales       | BD/BDC sin operación.                                                                  | Sistema                          | Validación de permisos en módulo Hotel.                     | 🔴 Alta   | ⬜ Pendiente | —                 |
| RI-V-01  | RI   | Integración       | Ventas ↔ Hotel                             | Habilitación al alcanzar Naranja.                                                      | Sistema                          | API documentada.                                            | 🔴 Alta   | ⬜ Pendiente | —                 |
| RI-V-02  | RI   | Integración       | Ventas ↔ Reclutamiento                     | Hotel cliente activo recibe Reclutadoras.                                              | Sistema                          | Notificación automática.                                    | 🔴 Alta   | ⬜ Pendiente | —                 |
| RI-V-03  | RI   | Integración       | Ventas ↔ Inspección                        | Asignación de Inspector al activarse.                                                  | Sistema                          | Por zona del hotel.                                         | 🔴 Alta   | ⬜ Pendiente | —                 |
| RI-V-04  | RI   | Integración       | Ventas ↔ Contrato                          | Cierre Rosa genera Contrato.                                                           | Sistema                          | Insumo: T&C validado.                                       | 🔴 Alta   | ⬜ Pendiente | —                 |
| RI-V-05  | RI   | Integración       | Ventas ↔ Email                             | Email de bienvenida automático.                                                        | Sistema                          | Plantilla configurada.                                      | 🔴 Alta   | ⬜ Pendiente | —                 |
| RI-V-06  | RI   | Integración       | Ventas ↔ QA                                | Operador de QA fijo.                                                                   | Sistema                          | Métricas e Indicador de Calidad.                            | 🟡 Media  | ⬜ Pendiente | —                 |
