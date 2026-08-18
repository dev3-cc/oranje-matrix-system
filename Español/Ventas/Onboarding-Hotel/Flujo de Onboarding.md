---
tags:
  - modulo/onboarding-hotel
  - flujo
aliases:
  - Flujo de Onboarding Hotel
---

# Flujo de Onboarding

Proceso comercial paso a paso para incorporar un hotel como cliente activo de Oranje. Cada etapa corresponde a un status del [[Semáforo Onboarding]].

## Etapa 1 — [[Semáforo Onboarding#Gris — Hotel identificado|Gris]] Hotel identificado

**Responsable:** [[Business Developer]]
**Acción única:** el BD identifica el hotel dentro de su territorio como posible cliente.

**Avance →** cuando inicia contacto y recopilación de datos, mueve el status a [[Semáforo Onboarding#Azul Claro — Contacto y recopilación de datos|Azul Claro]].

## Etapa 2 — [[Semáforo Onboarding#Azul Claro — Contacto y recopilación de datos|Azul Claro]] Contacto y recopilación de datos

**Responsable:** [[Business Developer]]
**Acciones durante este status:**

- Crea el perfil del hotel en el sistema.
- Recopila datos: nombre del hotel, email, teléfono, **dirección y ubicación en el mapa**, nombre y cargo del contacto, necesidad del negocio.
- Realiza visita en frío al hotel.

**Avance →** al enviar la propuesta, el status cambia a [[Semáforo Onboarding#Verde — Propuesta enviada|Verde]].

## Etapa 3 — [[Semáforo Onboarding#Verde — Propuesta enviada|Verde]] Propuesta enviada

**Responsable:** [[Business Developer]]
**Acciones:**

- Elabora [[Propuesta Personalizada|propuesta personalizada]] (servicios, precios, condiciones).
- Adjunta y envía la propuesta al hotel.
- Registra intentos de contacto y respuestas.
- Hace seguimiento al hotel.

**Decisión: ¿El hotel responde con interés?**

- **SÍ →** pasa a [[Semáforo Onboarding#Amarillo — En seguimiento tras propuesta|Amarillo]].
- **NO →** pasa a [[Semáforo Onboarding#Rojo — Rechazo o no interés|Rojo]].

**Rama alterna:** si hay estancamiento, se activa [[Semáforo Onboarding#Café — Renegociación / desbloqueo|Café]] y el [[Business Developer Coordinator]] investiga y da solución para retomar la propuesta → regresa a [[Semáforo Onboarding#Azul Claro — Contacto y recopilación de datos|Azul Claro]].

## Etapa 4 — [[Semáforo Onboarding#Amarillo — En seguimiento tras propuesta|Amarillo]] En seguimiento tras propuesta

**Responsable:** [[Business Developer]] (con apoyo del [[Business Developer Coordinator]])
**Acciones durante este status:**

- BD o BDC crea el [[Documento de Términos y Condiciones]], que establece:
  - Pay rate
  - Bill rate
  - Overtime
  - Festivos
  - Calendario

**Avance →** al iniciar negociación formal, pasa a [[Semáforo Onboarding#Rosa — Negociación de términos|Rosa]].

## Etapa 5 — [[Semáforo Onboarding#Rosa — Negociación de términos|Rosa]] Negociación de términos

**Responsable:** [[Business Developer]] + [[Business Developer Coordinator]]

**Decisión: ¿Se cierra el acuerdo?**

- **NO →** vuelve al flujo (renegociación / [[Semáforo Onboarding#Café — Renegociación / desbloqueo|Café]] / Fin).
- **SÍ →** el BDC aprueba la conversión y se crea el [[Usuario del Hotel]] en el sistema.

**Al crearse el usuario** se dispara el [[Trigger Automático de Conversión]] (en paralelo):

- Sistema envía email de bienvenida al hotel.
- Sistema notifica al BD asignado.
- Hotel desaparece de la lista de prospectos.

**Avance →** pasa a [[Semáforo Onboarding#Naranja — Acuerdo firmado, hotel cliente activo|Naranja]].

## Etapa 6 — [[Semáforo Onboarding#Naranja — Acuerdo firmado, hotel cliente activo|Naranja]] Hotel cliente activo

**Responsable operativo:** [[Inspector]] + [[Reclutadora|Reclutadores]]
**Estado:** el hotel ya tiene [[Usuario del Hotel|usuario creado]] y está trabajando con Oranje.

- Pasa a responsabilidad de [[Reclutadora|Reclutadores]].
- Inicia ciclo operativo: [[Requisición|requisiciones]] → cobertura → [[Core/Módulos/Schedule|Schedule]] → [[Timesheet]].
- BD y BDC quedan como referentes comerciales.

**Decisión: ¿El hotel deja de operar?**

- **NO →** Fin (continúa activo).
- **SÍ →** pasa a [[Semáforo Onboarding#Negro — Cliente pausado o inactivo|Negro]].

## Etapa 7 — [[Semáforo Onboarding#Rojo — Rechazo o no interés|Rojo]] Hotel rechazó la propuesta / no interesado

**Responsable:** [[Business Developer]]

**Decisión: ¿Reactivar?**

- **NO →** Fin.
- **SÍ →** regresa a [[Semáforo Onboarding#Azul Claro — Contacto y recopilación de datos|Azul Claro]].

## Etapa 8 — [[Semáforo Onboarding#Negro — Cliente pausado o inactivo|Negro]] Cliente dejó de operar / relación pausada

**Responsable:** [[Business Developer Coordinator]]
**Motivos:** cierre del hotel, cambio de administración, pausa, disputa.

**Decisión: ¿Reactivar?**

- **NO →** Fin.
- **SÍ →** regresa a [[Semáforo Onboarding#Azul Claro — Contacto y recopilación de datos|Azul Claro]].

## Puntos clave

- Todo cambio de status queda registrado con fecha, responsable y comentario.
- [[Semáforo Onboarding#Naranja — Acuerdo firmado, hotel cliente activo|Naranja]] es el único status que habilita al hotel para generar [[Requisición|requisiciones]].
- El [[Usuario del Hotel]] se crea al aprobar la conversión en [[Semáforo Onboarding#Rosa — Negociación de términos|Rosa]], justo antes del [[Trigger Automático de Conversión|trigger automático]].
- [[Semáforo Onboarding#Rojo — Rechazo o no interés|Rojo]], [[Semáforo Onboarding#Negro — Cliente pausado o inactivo|Negro]] y [[Semáforo Onboarding#Café — Renegociación / desbloqueo|Café]] siempre reactivan hacia [[Semáforo Onboarding#Azul Claro — Contacto y recopilación de datos|Azul Claro]].
- La aprobación final de conversión la da únicamente el [[Business Developer Coordinator]].
