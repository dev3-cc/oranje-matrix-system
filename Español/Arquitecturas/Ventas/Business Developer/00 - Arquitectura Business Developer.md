---
tags:
  - arquitectura
  - modulo/ventas
aliases:
  - Arquitectura Business Developer
  - Wireframe Business Developer
  - Arquitectura BD
---

# Arquitectura — Business Developer (BD)

Wireframe de la plataforma Oranje para el rol [[Ventas/Roles/Business Developer|Business Developer]]. Define el flujo de entrada, header global, sidebar de módulos y el detalle de cada módulo al que tiene acceso.

> [!info]
> El Business Developer es el **rol operativo base** del depto Ventas. Ejecuta el ciclo comercial: identifica prospectos, hace visitas en frío, elabora propuestas, da seguimiento y gestiona rechazos. Su BDC valida los términos y aprueba la conversión final.

> [!important]
> El BD **NUNCA** aprueba la conversión a cliente — esa acción es **exclusiva del BDC** (regla RR-V-01). El BD prepara, propone y negocia, pero el "sí final" lo da el BDC.

## N0 — Inicio

```
PLATAFORMA ORANGE
        │
        ▼
LOGIN / AUTORIZACIÓN
        │
        ▼
ROL IDENTIFICADO / BUSINESS DEVELOPER (BD)
        │
        ▼
MÓDULOS / SIDEBAR
```

---

## HEADER (presente en toda la app)

### 👤 PERFIL DEL USUARIO (dropdown)

**Mi información**
- Nombre completo + foto
- Correo + teléfono
- Rol: Business Developer (BD)

**Mi BDC asignado**
- Nombre del BDC al que reporta
- Zona / rutas a cargo

**Mi territorio**
- Rutas asignadas
- Zonas operativas
- Cantidad de prospectos activos

**Mis métricas (mes en curso)**
- Prospectos identificados
- Propuestas enviadas
- Conversiones (clientes activos)
- Tasa de conversión personal
- Rechazos gestionados

**Configuración**
- Cambiar contraseña
- Preferencias de notificación

🚪 Cerrar sesión

### 🔍 BUSCADOR (global)

**Qué se puede buscar**
- Hoteles prospecto por nombre / ciudad / zona
- Hoteles cliente activos
- Propuestas enviadas
- Contratos

**Cómo funciona**
- Resultados en vivo agrupados por tipo
- Atajo: `/` o `Ctrl+K`

### 🔔 NOTIFICACIONES (campanita con badge)

**Por tipo de evento**
- 📩 Hotel respondió a la propuesta
- ✅ El BDC aprobó la conversión de mi prospecto → cliente activo
- 🔴 Hotel rechazó la propuesta
- ☕ Mi BDC desbloqueó un caso Café
- ⚫ Cliente activo pasó a Negro (pausado)
- 📊 Solicitud de reporte por parte del BDC
- 🎯 Recordatorio de seguimiento programado

**Estados**
- 🟠 Sin leer (cuenta en el badge)
- 🟢 Leída

---

## N1 — SIDEBAR (Módulos del Business Developer)

```
SIDEBAR
   ├─ DASHBOARD
   ├─ PIPELINE             (Mis prospectos por status)
   ├─ MI TERRITORIO        (rutas + zonas + mapa)
   ├─ PROPUESTAS           (Propuesta Personalizada)
   ├─ CONTRATOS            (creación con BDC)
   └─ CLIENTES ACTIVOS     (referente comercial post-Naranja)
```

> [!note]
> El BD **NO** tiene módulo "Mi Equipo", "Reportes ejecutivos", ni acción de "Conversión". Esos son del BDC.

---

## 📊 Módulo DASHBOARD

### KPIs personales del mes
- Prospectos en cada status del Semáforo Onboarding
- Propuestas enviadas (Verde)
- Hoteles en negociación (Amarillo / Rosa)
- Conversiones cerradas (Naranja)
- Tasa de conversión personal
- Rechazos del mes
- Visitas en frío realizadas

### Vistas resumen
- **Mis prospectos por status:** mini-pipeline con conteo por etapa.
- **Próximos seguimientos:** lista de prospectos que requieren contacto (basado en último contacto y status).
- **Alertas:** prospectos sin actividad >X días.

### Acciones rápidas
- ➕ Identificar nuevo prospecto
- ➕ Registrar visita en frío
- ➕ Elaborar nueva Propuesta
- 📞 Registrar intento de contacto

### 🔄 Cómo se usa este módulo

Al iniciar sesión, el BD llega aquí. Lo primero que mira son los KPIs personales del mes para tener contexto de cómo va su desempeño (cuántos prospectos identificó, cuántas propuestas envió, cuántas se convirtieron, su tasa de conversión).

Luego revisa **"Próximos seguimientos"** — el sistema le muestra los prospectos que requieren contacto hoy (basado en su último contacto y el status). Si ve alertas rojas de prospectos sin actividad >7 días, entra directo al detalle desde aquí.

Si no hay nada urgente, decide qué hacer con las **acciones rápidas**: identificar un nuevo prospecto, registrar un intento de contacto que hizo offline, o empezar a elaborar una propuesta. El Dashboard es su **punto de entrada diario** — desde aquí se mueve a Pipeline, Mi Territorio o Propuestas según lo que necesite.

---

## 📋 Módulo PIPELINE (Mis Prospectos)

### Sub-vistas (por status del [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]])

- ⚪ **Gris** — Identificados (sin contacto aún)
- 🔵 **Azul Claro** — Contacto + datos recopilados
- 🟢 **Verde** — Propuesta enviada
- 🟡 **Amarillo** — En seguimiento (T&C en construcción)
- 🩷 **Rosa** — Negociación de términos
- 🟠 **Naranja** — Cliente activo (post-conversión)
- 🔴 **Rojo** — Rechazo
- ⚫ **Negro** — Cliente pausado / inactivo
- 🟤 **Café** — Estancamiento (gestiona el BDC)
- 📂 **Toda** — Vista completa de mi territorio

### Filtros
- Status (uno o varios)
- Ruta / Zona
- Fecha de identificación (rango)
- Días sin contacto
- Buscar por nombre / ciudad

### Detalle del prospecto
- **Cabecera:** nombre, foto, dirección, contacto principal, status actual, días en status.
- **Datos del hotel:** email, teléfono, contacto, cargo, necesidad del negocio.
- **Histórico de contactos:** llamadas, emails, visitas, resultados.
- **Documentos asociados:** Propuesta enviada, T&C borrador.
- **Línea de tiempo (timeline):** cambios de status con fecha, autor, comentario.

### Acciones
- ➕ **Identificar prospecto** *(crea registro en Gris)*
- ✏️ **Editar perfil del hotel** *(Azul Claro)*
- ➕ **Registrar visita en frío**
- ➕ **Registrar intento de contacto**
- ✏️ **Avanzar status** (Gris → Azul Claro → Verde → Amarillo → Rosa)
- 🔴 **Marcar rechazo (Rojo)** con motivo
- ↩️ **Reactivar desde Rojo** (vuelve a Azul Claro)
- ☕ **Marcar estancamiento (Café)** *(BDC se encarga del desbloqueo)*

> [!warning]
> El BD **NO** puede aprobar conversión, validar T&C, crear Usuario del Hotel, desbloquear Café, marcar/reactivar Negro. Todo eso es del BDC.

### 🔄 Cómo se usa este módulo

Pipeline es el **centro de operaciones** del BD. Aquí gestiona todos sus prospectos a lo largo del Semáforo Onboarding.

**Cuando el prospecto es nuevo (Gris):** el BD entra al detalle del hotel y llena el perfil — email, teléfono, contacto, cargo, necesidad del negocio. Al guardar, el sistema cambia el status a **Azul Claro** automáticamente y notifica al BDC. La línea de tiempo registra el cambio con autor y fecha.

**Cuando el prospecto está en Azul Claro:** el BD registra visitas en frío e intentos de contacto cada vez que llama o visita al hotel. Cada intento queda en el histórico de contactos del prospecto. Cuando ya tiene suficiente información y el contacto está caliente, sale del Pipeline y se mueve al módulo Propuestas para elaborar la Propuesta Personalizada.

**Cuando el prospecto está en Verde (propuesta enviada):** el BD espera respuesta del hotel y hace seguimiento. Si el hotel responde con interés, click **"Avanzar a Amarillo"** → ingresa motivo y comentario obligatorio → el status cambia y notifica al BDC.

**Si el hotel rechaza:** click **"Marcar Rojo"** → selecciona motivo del catálogo (No le interesó / No tiene presupuesto / Otra empresa / Otro) + comentario obligatorio mín. 30 caracteres. Puede marcar "Reactivar más adelante" si cree que el contacto puede revivirse. Más tarde, desde la sub-vista Rojo, puede hacer click en **"Reactivar"** → el status regresa automáticamente a Azul Claro (regla RR-V-07, NUNCA vuelve a Gris).

**Si el prospecto se estanca** (sin respuesta tras múltiples intentos, cambio de contacto, indecisión): click **"Marcar Café"** → motivo + notas para el BDC (mín. 30 caracteres, son el contexto que el BDC necesita para investigar). A partir de ahí, **el BD pierde el control** del prospecto: el caso pasa a la bandeja Café del BDC, que investiga, da solución y reactiva (regresa a Azul Claro). Solo entonces el BD recupera el caso.

**Cuando llega a Rosa:** el BD ya envió el T&C y está negociando junto al BDC. Si el BDC aprueba la conversión, el prospecto pasa a Naranja y desaparece del Pipeline (entra a Clientes Activos).

> [!note]
> Cada cambio de status registra **fecha, responsable y comentario** en la línea de tiempo del prospecto (regla RR-V-11). Nada se mueve "en silencio".

---

## 🗺️ Módulo MI TERRITORIO

### Vista principal
- **Mapa interactivo** con mis rutas y zonas asignadas.
- **Pines por status** del prospecto en cada ubicación.
- **Lista lateral** de prospectos por ruta.

### Filtros
- Ruta
- Zona
- Status
- Buscar por nombre

### Acciones
- 👁️ Ver detalle del prospecto
- ➕ Identificar nuevo prospecto en el mapa
- 📍 Marcar ruta del día (planeación de visitas)

### 🔄 Cómo se usa este módulo

Mi Territorio es el módulo que el BD usa **cuando sale a campo**. Lo abre principalmente desde mobile.

Lo primero que ve es el **mapa con sus rutas y zonas asignadas**, con pines de colores según el status del prospecto en cada ubicación (Gris = recién identificado, Azul Claro = contacto en curso, Verde = propuesta enviada, etc.).

**Al inicio del día** el BD usa **"Marcar ruta del día"** para planear las visitas: selecciona los prospectos que va a visitar y el sistema le sugiere un orden eficiente de recorrido. Esto le ayuda a optimizar el tiempo y no olvidarse de ninguno.

**Durante la visita**, si detecta un **hotel nuevo** que no estaba registrado, hace click directo en el mapa en esa ubicación → el sistema captura **geolocalización automáticamente** y abre el formulario de "Identificar prospecto" con la zona pre-llenada. Llena nombre y datos mínimos → el prospecto queda en Gris listo para ser trabajado luego.

**Si quiere ver el detalle** de un prospecto existente (para refrescar la información antes de tocar la puerta), hace click en su pin → entra al detalle del prospecto desde Pipeline. Desde ahí puede registrar el intento de contacto que va a hacer en ese momento.

> [!info]
> Mi Territorio y Pipeline están sincronizados: cualquier cambio de status hecho desde Pipeline se refleja en el mapa, y cualquier prospecto identificado en el mapa aparece en Pipeline.

---

## 📝 Módulo PROPUESTAS

### Sub-vistas
- ✏️ **Borradores** — propuestas en construcción
- 📤 **Enviadas** — esperando respuesta del hotel
- ✅ **Aceptadas** — el hotel mostró interés (avanzaron a Amarillo)
- ❌ **Rechazadas** — el hotel rechazó (Rojo)
- 📂 **Histórico**

### Acciones
- ➕ **Elaborar Propuesta Personalizada** (RF-V-04 — Verde)
- 📤 **Enviar al hotel** (RF-V-05)
- ✏️ **Editar borrador**
- 📥 **Exportar PDF**
- 📋 **Duplicar** (usar como plantilla para otro prospecto)

### Detalle
- Datos del hotel destino
- Servicios propuestos
- Precios y condiciones
- Vigencia de la propuesta
- Estado (borrador / enviada / respondida)
- Histórico de envíos

> [!info]
> La Propuesta Personalizada se elabora exclusivamente en status **Verde** (RR-V-09). Si el prospecto está estancado (Café), el BDC tomará el caso para desbloquear y habilitar una nueva propuesta.

### 🔄 Cómo se usa este módulo

Cuando un prospecto está listo (Azul Claro con perfil completo y contactos previos hechos), el BD se mueve a Propuestas para construir la oferta comercial formal.

**Crear la propuesta:** click **"➕ Elaborar Propuesta Personalizada"** → selecciona el hotel destino de la lista de sus prospectos en Azul Claro (los hoteles en otros status no aparecen) → llena los servicios propuestos del catálogo → fija precios por servicio → escribe condiciones generales (mín. 100 caracteres con los términos comerciales) → define vigencia (fecha futura). Puede adjuntar PDFs o documentos de soporte. Guarda como **borrador** y puede editarlo libremente cuantas veces quiera.

**Enviar al hotel:** cuando el BD está conforme con la propuesta, click **"📤 Enviar al hotel"** → el sistema valida que esté completa (servicios + precios + condiciones + vigencia) → envía email al hotel con la propuesta adjunta → cambia el status del prospecto a **Verde** automáticamente → bloquea esa versión de la propuesta para edición (queda como histórico) → notifica al BD y al BDC.

**Reutilizar propuestas:** si el BD quiere armar una propuesta similar para otro hotel, usa **"📋 Duplicar"** sobre una existente. El sistema crea una copia con los datos pre-llenados, y el BD solo ajusta lo que sea diferente para el nuevo prospecto. Esto acelera muchísimo el trabajo cuando hay propuestas estándar.

**Si el hotel responde con interés:** ese flujo se gestiona desde Pipeline (avanzar a Amarillo). En Propuestas el BD solo ve que la propuesta cambió de estado a "Aceptada".

**Si el hotel rechaza:** desde Pipeline el BD marca Rojo. En Propuestas la propuesta queda como "Rechazada" en el histórico — puede consultarse o duplicarse si se reactiva el prospecto más adelante.

---

## 📄 Módulo CONTRATOS

### Sub-vistas
- ✏️ **Borradores** — T&C en construcción
- ⏳ **Pendientes de validación BDC**
- ✅ **Validados** (listos para Rosa)
- 📂 **Histórico**

### Acciones
- ➕ **Crear Documento de T&C** *(BD o BDC, en status Amarillo)*
- ✏️ **Editar borrador** del T&C
- 📤 **Enviar al BDC para validación**
- 👁️ **Ver feedback del BDC**

### Campos obligatorios del T&C (RR-V-10)

| Campo      |
| ---------- |
| Pay rate   |
| Bill rate  |
| Overtime   |
| Festivos   |
| Calendario |

> [!warning]
> El BD **NO** valida ni aprueba el T&C. Solo lo crea y edita. La validación final es del BDC (RR-V-15).

### 🔄 Cómo se usa este módulo

Una vez el hotel responde con interés a la Propuesta (status Amarillo), el BD construye el documento formal que sienta las bases legales y económicas del acuerdo: el Documento de Términos y Condiciones.

**Crear el T&C:** click **"➕ Crear Documento de T&C"** → llena los **5 campos obligatorios** (regla RR-V-10):
1. **Pay rate** — tarifa de pago al colaborador
2. **Bill rate** — tarifa de facturación al hotel
3. **Overtime** — reglas de tiempo extra
4. **Festivos** — días festivos pagados
5. **Calendario** — inicio y fin de semana laboral

Opcionalmente: vigencia, renovación, adjuntos. El T&C queda como **borrador editable** mientras el BD lo construye. Si tiene dudas, le puede pedir **apoyo al BDC** desde el detalle del documento (el BDC entra y co-edita).

**Enviar al BDC para validación:** cuando el T&C está completo, click **"📤 Enviar al BDC para validación"** → el sistema valida que los 5 campos obligatorios estén llenos → cambia el documento a estado "Pendiente de validación BDC" → **bloquea la edición del BD** mientras está pendiente → notifica al BDC en su bandeja.

**Esperar decisión del BDC:**
- **Si el BDC valida:** el BD recibe notificación *"Tu T&C fue validado"* → el documento queda como "Validado" → el BD ya puede avanzar el prospecto a **Rosa** (negociación formal) desde Pipeline.
- **Si el BDC rechaza con observaciones:** el BD recibe notificación con las observaciones del BDC → el T&C vuelve a editable → el BD corrige según el feedback y lo reenvía.

> [!important]
> Sin T&C validado, el BD NO puede iniciar Rosa, y por lo tanto el prospecto NO puede llegar a conversión. El T&C validado es **prerequisito obligatorio** para todo el flujo de cierre.

---

## 🏨 Módulo CLIENTES ACTIVOS (referente comercial)

### Vista principal
- Lista de clientes activos (status Naranja) en mi territorio.
- Datos comerciales (no operativos).
- Histórico del onboarding (cuándo se convirtió, T&C aplicable, vigencia).

### Filtros
- Ruta / Zona
- Fecha de conversión
- Buscar por nombre

### Acciones
- 👁️ **Ver detalle del cliente** (datos comerciales)
- 📞 **Registrar contacto comercial** (visita de cortesía, follow-up)

> [!important]
> Post-Naranja, BD y BDC son **referentes comerciales** sin permisos operativos (RR-V-12). NO crean requisiciones, NO editan el hotel, NO gestionan personal — eso es del depto Hotel y Reclutamiento.

### 🔄 Cómo se usa este módulo

Después de que el BDC aprueba la conversión (Rosa → Naranja), el prospecto desaparece del Pipeline del BD y **aparece automáticamente en Clientes Activos**. A partir de ahí el hotel ya NO es prospecto: es cliente y opera con Reclutamiento, Inspección y el depto Hotel. El BD pasa a ser **referente comercial** — el contacto humano de Oranje con el cliente.

**Qué hace el BD aquí:**
- **Consultar el cliente:** ve datos comerciales (cuándo se convirtió, T&C aplicable, vigencia del contrato, BD originario, histórico del onboarding). Solo lectura del lado operativo.
- **Registrar contacto comercial:** click **"📞 Registrar contacto comercial"** → documenta visitas de cortesía, llamadas de follow-up o reuniones para mantener relación. Queda en histórico del cliente.

**Qué NO puede hacer (RR-V-12):**
- ❌ Crear requisiciones (lo hace el Supervisor del hotel)
- ❌ Editar el hotel (lo hace el Manager de Área)
- ❌ Gestionar personal asignado (lo hace Reclutamiento)
- ❌ Autorizar nada

**Si el cliente cae a Negro:** si el BDC marca el cliente como Negro (cierre, pausa, disputa), el BD recibe notificación: *"Tu cliente X pasó a Negro"*. **El BD no interviene en la gestión** — eso es exclusivo del BDC (RR-V-05). Solo se entera.

**Si el cliente se reactiva desde Negro:** vuelve al ciclo desde Azul Claro como prospecto (RR-V-07). Si el BDC le reasigna ese caso, el BD lo recupera en su Pipeline y vuelve a empezar el ciclo de Ventas.

---

## Diferencias clave vs BDC

| Aspecto | BD | BDC |
|---|---|---|
| Identificar prospecto (Gris) | ✅ | — |
| Crear perfil (Azul Claro) | ✅ | — |
| Elaborar Propuesta Personalizada | ✅ exclusivo | — |
| Crear Documento de T&C | ✅ | ✅ apoyo |
| Validar Documento de T&C | ❌ | ✅ exclusivo |
| Negociar (Rosa) | ✅ | ✅ |
| Crear Usuario del Hotel | ❌ | ✅ exclusivo |
| Aprobar conversión | ❌ | ✅ exclusivo |
| Gestionar Rojo | ✅ | — |
| Desbloquear Café | ❌ | ✅ exclusivo |
| Gestionar Negro | ❌ | ✅ exclusivo |
| Mi Equipo (BDs a cargo) | ❌ | ✅ exclusivo |
| Reportes ejecutivos | ❌ | ✅ exclusivo |
| Visibilidad | Mi territorio | Toda la zona / rutas a cargo |

---

## Diagrama ASCII general del wireframe

```
╔══════════════════════════════════════════════════════════════╗
║                       PLATAFORMA ORANGE                       ║
║                              │                                ║
║                              ▼                                ║
║                   LOGIN / AUTORIZACIÓN                        ║
║                              │                                ║
║                              ▼                                ║
║                   ROL: BUSINESS DEVELOPER                     ║
╚══════════════════════════════════════════════════════════════╝
                                │
                                ▼
┌──────────────────────────────────────────────────────────────┐
│ HEADER:  [👤 Perfil]   [🔍 Buscador]   [🔔 Notificaciones]   │
└──────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────┬───────────────────────────────────────────────┐
│  SIDEBAR     │              ÁREA DE TRABAJO                  │
│              │                                               │
│ 📊 Dashboard │  KPIs personales · Próximos seguimientos      │
│ 📋 Pipeline  │  Mis prospectos por status                    │
│ 🗺️ Territorio│  Mapa · Rutas · Pines por status              │
│ 📝 Propuesta │  Borradores · Enviadas · Histórico            │
│ 📄 Contratos │  Borradores · Validación BDC                  │
│ 🏨 Clientes  │  Activos (referente comercial)                │
└──────────────┴───────────────────────────────────────────────┘
```

---

## Relacionado

- [[Ventas/Roles/Business Developer|Business Developer]] (definición del rol)
- [[Ventas/Roles/Business Developer Coordinator|Business Developer Coordinator]]
- [[Ventas/Ventas|Ventas]]
- [[Ventas/Reglas de Ventas|Reglas de Ventas]]
- [[Ventas/Onboarding-Hotel/Onboarding-Hotel|Onboarding-Hotel]]
- [[Ventas/Onboarding-Hotel/Flujo de Onboarding|Flujo de Onboarding]]
- [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]]
- [[Ventas/Onboarding-Hotel/Conceptos/Propuesta Personalizada|Propuesta Personalizada]]
- [[Ventas/Onboarding-Hotel/Conceptos/Documento de Términos y Condiciones|Documento de Términos y Condiciones]]
- [[Hotel/Hotel|Hotel]] (destino post-Naranja)
