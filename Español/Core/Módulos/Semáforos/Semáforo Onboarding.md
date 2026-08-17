---
tags:
  - modulo/onboarding-hotel
aliases:
  - Semáforo Onboarding
  - Semáforo de Onboarding Hotel
  - Status Onboarding Gris
  - Status Onboarding Azul Claro
  - Status Onboarding Café
  - Status Onboarding Verde
  - Status Onboarding Amarillo
  - Status Onboarding Rosa
  - Status Onboarding Naranja
  - Status Onboarding Rojo
  - Status Onboarding Negro
---

# Semáforo Onboarding

Sistema de estados que representa el seguimiento de la negociación comercial con un hotel, desde su identificación como prospecto hasta su activación como cliente (o su pausa/rechazo).

> [!info]
> Este semáforo aplica únicamente al módulo de [[Onboarding-Hotel|Onboarding Hotel]]. Una vez el hotel alcanza [[#Naranja — Acuerdo firmado, hotel cliente activo|Naranja]], su operación se rige por los semáforos del módulo [[Hotel/Hotel|Hotel]] ([[Semáforo de Requisición]], [[Semáforo del Colaborador]], etc.).

## Estados

| Color      | Estado                                                                                      | Responsable                                                          |
| ---------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| Gris       | [[#Gris — Hotel identificado\|Hotel identificado]]                                          | [[Business Developer\|BD]]                                           |
| Azul claro | [[#Azul Claro — Contacto y recopilación de datos\|Contacto y recopilación de datos]]        | [[Business Developer\|BD]]                                           |
| Café       | [[#Café — Renegociación / desbloqueo\|Renegociación / desbloqueo]]                          | [[Business Developer Coordinator\|BDC]]                              |
| Verde      | [[#Verde — Propuesta enviada\|Propuesta enviada]]                                           | [[Business Developer\|BD]]                                           |
| Amarillo   | [[#Amarillo — En seguimiento tras propuesta\|En seguimiento tras propuesta]]                | [[Business Developer\|BD]]                                           |
| Rosa       | [[#Rosa — Negociación de términos\|Negociación de términos]]                                | [[Business Developer\|BD]] + [[Business Developer Coordinator\|BDC]] |
| Naranja    | [[#Naranja — Acuerdo firmado, hotel cliente activo\|Acuerdo firmado, hotel cliente activo]] | [[Business Developer Coordinator\|BDC]]                              |
| Rojo       | [[#Rojo — Rechazo o no interés\|Rechazo o no interés]]                                      | [[Business Developer\|BD]]                                           |
| Negro      | [[#Negro — Cliente pausado o inactivo\|Cliente pausado o inactivo]]                         | [[Business Developer Coordinator\|BDC]]                              |

---

## Gris — Hotel identificado

**Responsable:** [[Business Developer]]

El BD identifica el hotel dentro de su territorio como posible cliente.

**Avance →** cuando inicia contacto y recopilación de datos, pasa a [[#Azul Claro — Contacto y recopilación de datos|Azul Claro]].

---

## Azul Claro — Contacto y recopilación de datos

**Responsable:** [[Business Developer]]

### Acciones durante este status

- Crea el perfil del hotel en el sistema.
- Recopila datos: nombre del hotel, email, teléfono, nombre y cargo del contacto, necesidad del negocio.
- Realiza visita en frío al hotel.

**Avance →** cuando termina la recopilación de datos y empieza a elaborar la [[Propuesta Personalizada|propuesta]], pasa a [[#Verde — Propuesta enviada|Verde]].

> También se llega aquí por reactivación desde [[#Rojo — Rechazo o no interés|Rojo]], [[#Negro — Cliente pausado o inactivo|Negro]] o [[#Café — Renegociación / desbloqueo|Café]].

---

## Verde — Propuesta enviada

**Responsable:** [[Business Developer]]

### Acciones durante este status

- Elabora [[Propuesta Personalizada|propuesta personalizada]] (servicios, precios, condiciones).
- Adjunta y envía la propuesta al hotel.
- Registra intentos de contacto y respuestas.
- Hace seguimiento al hotel.

> [!important] La propuesta se elabora **y** se envía dentro de este status
> El nombre del status describe cómo se sale de él, no cómo se entra. De Verde no
> se avanza a ningún lado sin haber enviado la propuesta: Amarillo, Rojo y Café
> suponen que el hotel ya la vio.

### Decisión

**¿El hotel responde con interés?**

- **SÍ →** [[#Amarillo — En seguimiento tras propuesta|Amarillo]].
- **NO →** [[#Rojo — Rechazo o no interés|Rojo]].

**Rama alterna:** si la negociación se estanca → [[#Café — Renegociación / desbloqueo|Café]] (gestionado por [[Business Developer Coordinator]]).

---

## Amarillo — En seguimiento tras propuesta

**Responsable:** [[Business Developer]] (con apoyo del [[Business Developer Coordinator]])

### Acciones durante este status

- BD o BDC crea el [[Documento de Términos y Condiciones]], que establece:
  - Pay rate
  - Bill rate
  - Overtime
  - Festivos
  - Calendario

**Avance →** al iniciar negociación formal, pasa a [[#Rosa — Negociación de términos|Rosa]].

---

## Rosa — Negociación de términos

**Responsable:** [[Business Developer]] + [[Business Developer Coordinator]]

### Decisión

**¿Se cierra el acuerdo?**

- **NO →** vuelve al flujo (renegociación / [[#Café — Renegociación / desbloqueo|Café]] / Fin).
- **SÍ →** el [[Business Developer Coordinator|BDC]] aprueba la conversión y se crea el [[Usuario del Hotel]] en el sistema.

### Al aprobarse la conversión

Se dispara el [[Trigger Automático de Conversión]] (en paralelo):

- Sistema envía email de bienvenida al hotel.
- Sistema notifica al [[Business Developer|BD]] asignado.
- Hotel desaparece de la lista de prospectos.

**Avance →** pasa a [[#Naranja — Acuerdo firmado, hotel cliente activo|Naranja]].

---

## Naranja — Acuerdo firmado, hotel cliente activo

**Responsable operativo:** [[Inspector]] + [[Reclutadora|Reclutadores]]
**Responsable comercial:** [[Business Developer Coordinator]] (referente) · [[Business Developer]] (asignado)

> [!important]
> **Naranja es el único status que habilita al hotel para generar [[Requisición|requisiciones]].**

### Estado operativo

- El hotel ya tiene [[Usuario del Hotel|usuario creado]] en el sistema.
- Pasa a responsabilidad de [[Reclutadora|Reclutadores]].
- Inicia ciclo operativo: [[Requisición|requisiciones]] → cobertura → [[Core/Módulos/Schedule|Schedule]] → [[Timesheet]].
- BD y BDC quedan como referentes comerciales de la cuenta.

### Decisión

**¿El hotel deja de operar?**

- **NO →** Fin (continúa activo).
- **SÍ →** pasa a [[#Negro — Cliente pausado o inactivo|Negro]].

---

## Café — Renegociación / desbloqueo

**Responsable:** [[Business Developer Coordinator]]

> [!info]
> Café no es un status terminal: es un **puente de desbloqueo** operado por el BDC cuando una negociación se estanca.

### Acciones durante este status

- El BDC investiga la causa del estancamiento (precio, condiciones, competencia, momento del hotel).
- El BDC da solución o ajuste para retomar la propuesta y avanzar a la contratación.

**Avance →** con propuesta ajustada, regresa a [[#Azul Claro — Contacto y recopilación de datos|Azul Claro]].

---

## Rojo — Rechazo o no interés

**Responsable:** [[Business Developer]]

El hotel rechazó la propuesta o no está interesado.

### Decisión

**¿Reactivar?**

- **NO →** Fin (hotel archivado como no viable).
- **SÍ →** regresa a [[#Azul Claro — Contacto y recopilación de datos|Azul Claro]] para reiniciar el contacto.

---

## Negro — Cliente pausado o inactivo

**Responsable:** [[Business Developer Coordinator]]

Cliente dejó de operar o se pausó la relación.

### Motivos frecuentes

- Cierre del hotel.
- Cambio de administración.
- Pausa temporal de operación.
- Disputa comercial.

### Decisión

**¿Reactivar?**

- **NO →** Fin (archivado como cliente inactivo).
- **SÍ →** regresa a [[#Azul Claro — Contacto y recopilación de datos|Azul Claro]] para renegociar condiciones de reactivación.

---

## Relacionado

- [[Onboarding-Hotel]]
- [[Flujo de Onboarding]]
- [[Business Developer]]
- [[Business Developer Coordinator]]
