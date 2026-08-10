---
tags:
  - arquitectura
  - ventas
  - diseno
  - prompt
aliases:
  - Prompt Figma Ventas
  - Maquetado Figma de Ventas
---

# Prompt — Maquetado en Figma del departamento Ventas



> [!info] Qué lo respalda
> Los datos vienen de `Ventas - Modelo de Datos v2.drawio` (las 7 tablas), del [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]] (los 9 estados), de los `00 - Arquitectura` de [[Business Developer|BD]] y [[Business Developer Coordinator|BDC]] (los sidebars) y de [[Convenciones de Diseño]] (los tokens).

---

## El prompt

````text
Diseña el maquetado de alta fidelidad del departamento de VENTAS de Oranje,
una plataforma web de gestión de staffing para hoteles.

## Contexto del producto

Oranje coloca personal (los "Colaboradores") en hoteles clientes. El
departamento de Ventas consigue esos hoteles: identifica prospectos, los visita,
manda propuestas, negocia y los convierte en clientes activos.

El idioma de TODA la interfaz es ESPAÑOL/INGLES. pero aqui en el maquetado usaremos español y en ell front pondremos las 2 opciones

## Los dos roles, y por qué importan

**Business Developer (BD)** — el ejecutor de campo. Identifica, visita, propone,
da seguimiento y gestiona rechazos.

**Business Developer Coordinator (BDC)** — su supervisor. Valida los términos y
es el ÚNICO que aprueba la conversión de prospecto a cliente. El BD nunca
aprueba: prepara, propone y negocia, pero el "sí final" es del BDC.

Esa asimetría tiene que verse en el diseño: el botón de aprobar conversión no
existe en la pantalla del BD.

## El semáforo: 9 estados que gobiernan toda la interfaz

Un hotel prospecto está siempre en exactamente uno de estos estados. El color es
parte del dato, no decoración:

| Estado    | Token                  | Hex     | Significa                        | Responsable |
| --------- | ---------------------- | ------- | -------------------------------- | ----------- |
| Gris      | `--st-gris`            | #9A9A9A | Hotel identificado               | BD          |
| Azul claro| `--st-azul-claro`      | #5BC0EB | Contacto y recopilación de datos | BD          |
| Verde     | `--st-verde`           | #1FA84A | Propuesta enviada                | BD          |
| Amarillo  | `--st-amarillo`        | #FFD500 | En seguimiento tras propuesta    | BD (+BDC)   |
| Rosa      | `--st-rosa`            | #FF6FA5 | Negociación de términos          | BD + BDC    |
| Naranja   | `--st-naranja`         | #F2711C | Acuerdo firmado, CLIENTE ACTIVO  | BDC         |
| Rojo      | `--st-rojo`            | #E11919 | Rechazo o no interés             | BD          |
| Café      | `--st-cafe`            | #8B5E34 | Renegociación / desbloqueo       | BDC         |
| Negro     | `--st-negro`           | #1A1108 | Cliente pausado o inactivo       | BDC         |

Usa el token, no el hex suelto: en Figma son las variables `Semaforo/st-*` y en
código salen de [[Convenciones de Diseño]]. El texto del chip va en `--ink`
salvo sobre Negro, Morado, Rojo, Verde y Café, donde va en blanco.

Reglas del semáforo que el diseño debe respetar:

- El avance normal es Gris → Azul claro → Verde → Amarillo → Rosa → Naranja.
- Rojo, Café y Negro son ramas, no pasos.
- **Azul claro es el único punto de reentrada:** desde Rojo, Café o Negro
  siempre se reactiva hacia Azul claro, nunca hacia Verde ni Rosa.
- Solo Naranja habilita al hotel para generar requisiciones. Es el estado meta.
- Algunas transiciones exigen MOTIVO obligatorio (pasar a Rojo, salir de Café).
  El diseño necesita un modal de transición con campo de motivo requerido.

EL CHOQUE DE COLOR YA ESTÁ RESUELTO EN EL SISTEMA: el naranja del semáforo es
`--st-naranja` (#F2711C) y el primario de la marca es `--o-500` (#FF8000). Son
deliberadamente distintos. Nunca uses `Marca/o-500` para un chip de estado ni
`Semaforo/st-naranja` para un botón.

OTRA REGLA DE CONTRASTE QUE APLICA AQUÍ: sobre `--o-500` el texto va en `--ink`,
nunca blanco — con blanco da 2.5:1 y reprueba AA. Como el botón primario es
naranja, esto toca todas las pantallas.

## Los datos reales que hay detrás

Estas son las tablas que existen. No inventes campos que no estén aquí.

**hotel** — solo lo permanente del edificio: nombre, teléfono general, zona,
coordenadas, radio de geocerca, zona horaria, fecha de activación como cliente.
NO tiene estado de semáforo ni datos de contacto.

**hotel_contact** — los contactos del hotel: nombre, puesto, teléfono, correo, y
uno marcado como principal. Un hotel puede tener varios (el de Compras y el de
Ama de Llaves a la vez).

**prospect** — EL CICLO COMERCIAL. Aquí vive el estado del semáforo. Tiene:
hotel, dueño (el BD asignado), descripción de la necesidad, estado actual, fecha
de apertura, fecha de cierre y motivo de cierre.
Un hotel puede tener VARIOS ciclos a lo largo del tiempo, pero solo uno abierto.

**prospect_state_history** — una fila por cada cambio de color: de qué estado, a
qué estado, motivo, quién lo movió y cuándo. Esto habilita una TIMELINE en la
ficha del prospecto y las métricas de tiempo en cada estado.

**contact_attempt** — cada intento de contacto: tipo (visita en frío, llamada,
correo), resultado (no contestó, interesado, no interesado, cita agendada),
cuándo y quién. Opcionalmente a qué contacto del hotel.

**proposal** — la Propuesta Personalizada: versión, servicios, tarifas
tentativas (pay rate y bill rate), quién la envió y cuándo. Varias versiones por
ciclo.

**user_zone** — las zonas asignadas a cada BD. Un BD cubre varias zonas.

LO QUE NO EXISTE TODAVÍA: el Documento de Términos y Condiciones. El estado Rosa
dice "negociando términos" pero no hay tabla que guarde esos términos. En el
maquetado, la pantalla de T&C va como PLACEHOLDER marcado, no inventes campos.

## Estructura de la aplicación

Layout fijo en toda la app: **header superior + sidebar izquierdo + área de
contenido**. El contenido cambia por sección; el header y el sidebar no.

**Header:** logo Oranje, buscador global (atajo Ctrl+K) que busca hoteles
prospecto, clientes activos y propuestas; campana de notificaciones con badge; y
avatar con menú de perfil.

**Sidebar del BD:**
- Dashboard
- Pipeline (mis prospectos por estado)
- Mi Territorio (rutas, zonas y mapa)
- Propuestas
- Documentos T&C
- Clientes Activos

**Sidebar del BDC:** lo mismo, más Conversión, Mi Equipo y Reportes. Sin "Mi
Territorio" — el BDC ve el territorio completo.

## Pantallas a diseñar

1. **Pipeline (la principal)** — Tablero kanban con una columna por estado del
   semáforo. Cada tarjeta es un prospecto: nombre del hotel, zona, días en el
   estado actual, último intento de contacto, dueño. Con vista alterna de tabla.
   Contador por columna. Filtros por zona y por dueño.

2. **Ficha del prospecto** — La pantalla más densa. Debe mostrar:
   - Encabezado con el hotel, el chip de estado y el botón de cambiar estado
   - Datos permanentes del hotel
   - Lista de contactos, con el principal destacado
   - **Timeline del semáforo** (de prospect_state_history): cada cambio con su
     color, fecha, quién y motivo. Es la pieza más valiosa de esta pantalla.
   - Bitácora de intentos de contacto, con tipo y resultado
   - Versiones de la propuesta

3. **Modal de cambio de estado** — Muestra solo las transiciones VÁLIDAS desde
   el estado actual, no todas. Campo de motivo, obligatorio en las que lo
   exigen. Si el rol no puede hacer esa transición, no aparece.

4. **Mi Territorio** — Mapa con los hoteles del territorio del BD, coloreados
   por estado del semáforo. Panel lateral con la lista por zona.

5. **Registrar intento de contacto** — Formulario corto: tipo, contacto,
   resultado, notas, fecha.

6. **Propuesta** — Constructor de la propuesta con servicios y tarifas
   tentativas, y el historial de versiones enviadas.

7. **Dashboard** — Embudo por estado, tasa de conversión, tiempo promedio de
   conversión, prospectos sin actividad reciente.

8. **Conversión (solo BDC)** — La pantalla de aprobar el paso a cliente activo.
   Debe exigir que exista el Usuario del Hotel antes de habilitar el botón.

## Sistema de diseño

Usa estos tokens exactos:

| Token       | Hex     | Uso                            |
| ----------- | ------- | ------------------------------ |
| --o-500     | #FF8000 | Primario, naranja Oranje       |
| --o-50      | #FFF6E8 | Tinte más claro                |
| --o-700     | #C85F00 | Variante oscura, hover         |
| --bg        | #F6F4F1 | Fondo de la aplicación         |
| --surface   | #FFFFFF | Cards, paneles, modales        |
| --surface-2 | #FBFAF8 | Hover y filas alternas         |
| --surface-3 | #EFEBE6 | Campos deshabilitados          |
| --line      | #E3DDD5 | Bordes y separadores           |
| --ink       | #1A1108 | Texto principal                |
| --ink-2     | #4A3F35 | Texto secundario               |
| --ink-3     | #7A6D60 | Metadatos, texto atenuado      |

Estilo: limpio usando los componentes de ui.shadcn segun documentacion,  Bordes de 1px con --line, esquinas suaves, usa el skill disponible si es necesario para esto 

La librería de componentes es **shadcn/ui sobre Tailwind**, así que usa sus
patrones: Card, Table, Badge, Dialog, Sheet, Select, Tabs, Command para el
buscador.

Accesibilidad: contraste mínimo AA. El estado del semáforo **nunca** se comunica
solo por color — siempre lleva su etiqueta de texto al lado.

## Entregable

Un archivo de Figma con las 8 pantallas en desktop (1440px), más los componentes
reutilizables como set: chip de estado del semáforo (9 variantes), tarjeta de
prospecto, fila de timeline, item de bitácora de contacto.
````

---

## Cómo usarlo

1