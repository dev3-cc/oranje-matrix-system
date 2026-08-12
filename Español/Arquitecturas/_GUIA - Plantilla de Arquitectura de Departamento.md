---
tags:
  - arquitectura
  - guia
aliases:
  - Guía Plantilla Arquitectura
  - Plantilla de Arquitectura de Departamento
---

# 📘 GUÍA — Plantilla de Arquitectura de Departamento

> **Propósito:** documentar la metodología de arquitectura de software por departamento que se usa para diseñar, ordenar y comunicar productos internos. Aplica a cualquier proyecto donde existan **roles operativos** trabajando con **módulos de software** dentro de un **flujo de negocio** definido.

---

## 1. Filosofía de la metodología

La arquitectura no es solo el wireframe visual. Es el **conjunto completo** de documentación que responde:

| Pregunta | Documento que la responde |
|---|---|
| ¿Quiénes usan el sistema? | Ficha de Rol · Ficha Detallada |
| ¿Qué hacen exactamente? | Acciones del Usuario · Casos de Uso |
| ¿Qué pueden ver/tocar/aprobar? | Permisos Detallados · Matriz de Permisos |
| ¿Qué pantallas usan? | Arquitectura wireframe |
| ¿Qué reglas rigen el sistema? | Reglas de Negocio |
| ¿Qué requerimientos cumple? | Tabla de Requerimientos · Por Módulo |
| ¿Qué se considera "terminado"? | Criterios de Aceptación |
| ¿De qué depende? | Dependencias |
| ¿Qué información captura? | Campos del Formulario |
| ¿Qué bloquea? | Validaciones |
| ¿Qué responde el sistema? | Respuestas del Sistema |

---

## 2. Estructura de carpetas

```
Arquitecturas/
└── [Nombre del Departamento]/
    │
    ├── _Globales del Depto/         ← 9 archivos compartidos por todo el depto
    │   ├── 01 - Portada.md
    │   ├── 02 - PRD - [Depto].md
    │   ├── 03 - Tabla de Requerimientos.md
    │   ├── 04 - Por Módulo.md
    │   ├── 05 - Leyenda.md
    │   ├── 06 - Matriz de Permisos.md
    │   ├── 07 - Reglas de Negocio.md
    │   ├── 08 - Criterios de Aceptación.md
    │   └── 09 - Dependencias.md
    │
    └── [Cada rol]/                  ← carpeta por cada rol con 12 archivos + casos de uso
        ├── 00 - Arquitectura [Rol].md
        ├── 01 - Ficha de Rol.md
        ├── 02 - Ficha Detallada.md
        ├── 03 - PRD.md
        ├── 04 - Permisos Detallados.md
        ├── 05 - Requerimientos.md
        ├── 06 - Resumen Rápido.md
        ├── 07 - Feature Map.md
        ├── 08 - Acciones del Usuario.md
        ├── 09 - Campos del Formulario.md
        ├── 10 - Validaciones.md
        ├── 11 - Respuestas del Sistema.md
        └── Casos de Uso/
            ├── RF-XX [Nombre].md
            └── ...
```

---

## 3. Convenciones

### 3.1 Numeración de archivos

Cada archivo lleva un **prefijo numérico** (`00 -`, `01 -`, `02 -`, ...) para forzar un orden de lectura específico. El número 0 es el wireframe principal; del 1 al 11 son archivos de detalle.

### 3.2 IDs de roles

Formato: `ROL-[X]-[NN]` donde:
- `X` = letra inicial del departamento (R = Reclutamiento, H = Hotel, V = Ventas).
- `NN` = número correlativo (01, 02, 03...).

Reservar siempre:
- Un ID para **Sistema** (automatización, no es persona).
- Un ID para **Administrador** (gestión técnica, suele quedar en pausa).

### 3.3 IDs de requerimientos

Formato: `[Tipo]-[X]-[NN]`

| Prefijo | Tipo | Define |
|---|---|---|
| **RF** | Requerimiento Funcional | QUÉ hace el sistema (acciones, flujos) |
| **RNF** | Requerimiento No Funcional | CÓMO lo hace (rendimiento, seguridad, usabilidad) |
| **RR** | Requerimiento de Negocio | POR QUÉ existe (reglas, políticas, restricciones) |
| **RI** | Requerimiento de Integración | CON QUÉ conecta (otros módulos, sistemas externos) |

Ejemplos: `RF-V-01`, `RR-H-05`, `RNF-V-02`, `RI-H-04`.

### 3.4 IDs de PRDs

Formato: `PRD-[DEPTO]-[NN]`
- `PRD-[DEPTO]-01` = PRD del depto completo.
- `PRD-[DEPTO]-02`, `03`, ... = PRD por rol.

### 3.5 IDs de criterios de aceptación

Formato: `AC-[X]-[NN]` (Aceptación). Ejemplo: `AC-V-01`.

### 3.6 Frontmatter de cada archivo

```yaml
---
tags:
  - arquitectura
  - departamento/[nombre]      # solo en _Globales del Depto
  - rol/[nombre-del-rol]       # solo en archivos de rol
aliases:
  - [Alias corto del documento]
---
```

### 3.7 Wikilinks (si usas Obsidian)

```markdown
[[Ruta/Archivo|Alias visible]]
```

Conecta cada documento con todo lo que referencia (semáforos, otros roles, módulos, reglas). El grafo se construye solo.

---

## 4. Plantillas por archivo

### 📂 _Globales del Depto

#### 01 - Portada.md

**Función:** índice del depto. Lista los roles, los IDs, las jerarquías y los nombres.

**Estructura:**
- Cabecera con datos del documento (versión, estado, relacionado a PRD).
- Tabla "ROLES DEFINIDOS EN ESTE DOCUMENTO" con: ID, Rol, Descripción breve.
- Sección de Jerarquías (si aplica): tabla con cada jerarquía + roles aplicables.
- Callout informativo con notas clave.

---

#### 02 - PRD - [Depto].md

**Función:** Product Requirements Document del depto completo.

**Estructura:**
- Cabecera: ID PRD, HU, Departamento, Funcionalidad, Actor Principal, Dispositivo, Estado, Versión.
- **Objetivo** (1 párrafo).
- **Alcance:** Incluye / Fuera del alcance.
- **Flujo General** (1 línea + diagrama opcional).
- **Actores:** tabla con Actor, Tipo, Responsabilidad principal.
- **Restricciones / Reglas aplicables:** referencia a 07 - Reglas de Negocio.
- **Integraciones (RI):** tabla con ID, Integración, Descripción.
- **Dependencias:** referencia a 09 - Dependencias.

---

#### 03 - Tabla de Requerimientos.md

**Función:** todos los RFs, RNFs, RRs, RIs del depto en una tabla grande para Sheet/Excel.

**Estructura:**
- Cabecera del documento.
- Tipos de Requerimiento (leyenda RF/RNF/RR/RI).
	- Prioridad y Estado (leyenda 🔴/🟡/🟢/⬜/🔵/✅).
- Tabla maestra:

| ID | Tipo | Módulo | Requerimiento | Descripción Detallada | Rol(es) | Criterio de Aceptación | Prioridad | Estado | Notas |
|---|---|---|---|---|---|---|---|---|---|

---

#### 04 - Por Módulo.md

**Función:** agrupar los RFs por **módulo del sidebar**. Cada módulo tiene su descripción corta + tabla de RFs.

**Estructura por módulo:**
```markdown
## 📋 [Nombre del Módulo]

Descripción corta de 1-2 líneas que explica qué hace el módulo.

| ID | Requerimiento | Rol(es) | Prioridad |
|---|---|---|---|
| RF-X-NN | ... | ... | 🔴 Alta |
```

Cerrar con secciones para:
- Configuración (Admin en pausa)
- No Funcionales (Transversal)
- Reglas de Negocio (resumen con enlace a 07)
- Integraciones

---

#### 05 - Leyenda.md

**Función:** explicar los símbolos de la matriz de permisos.

**Estructura:**
- Tabla de Símbolos / Significado / Aplica a.
- Tabla de Roles definidos en el documento.

Símbolos típicos:
- ✅ CRUD · ➕ Crear · 👁️ Ver · 📝 C · E · ✓ Aprobar · ✗ Rechazar · 🔍 Investigar · 🚨 Reportar · ⚙️ Auto · — Sin permiso · ⏸️ En pausa

---

#### 06 - Matriz de Permisos.md

**Función:** quién puede hacer qué, módulo por módulo, acción por acción.

**Estructura:**

| Módulo | Funcionalidad / Acción | ROL-1 | ROL-2 | ROL-3 | ... |
|---|---|---|---|---|---|
| **NOMBRE** | Acción 1 | Crear | Ver | — | ... |
| | Acción 2 | — | C · E | Auto | ... |

Cerrar con sección "Notas clave" con las reglas de oro del depto.

---

#### 07 - Reglas de Negocio.md

**Función:** reglas RR-X-NN consolidadas con ID, regla, descripción.

**Estructura:**

| ID | Regla | Descripción |
|---|---|---|
| RR-X-01 | Nombre corto | Descripción detallada |

Cerrar con sección "Referencias cruzadas" enlazando a documentos relacionados.

---

#### 08 - Criterios de Aceptación.md

**Función:** condiciones que se deben cumplir para considerar el sistema "terminado".

**Estructura:**

| # | Criterio |
|---|---|
| AC-X-01 | El sistema cumple X cuando ocurre Y bajo Z condiciones |

Cada criterio referencia la regla RR/RF que cubre. Son verificables (sí/no).

---

#### 09 - Dependencias.md

**Función:** mapear qué necesita el depto para funcionar.

**Estructura:** 5 secciones:
1. **Dependencias internas** (otros módulos del sistema).
2. **Dependencias externas** (apps, sistemas, integraciones).
3. **Dependencias de catálogos** (configuración del sistema).
4. **Dependencias de roles externos** (roles de otros deptos que intervienen).
5. **Dependencias semafóricas** (estados visuales del flujo).

Cada sección es una tabla con Dependencia / Descripción.

---

### 📂 Carpeta de cada rol

#### 00 - Arquitectura [Rol].md

**Función:** **wireframe principal del rol**. Es el documento más visual y referenciado.

**Estructura obligatoria:**
1. **N0 — Inicio**: diagrama del flujo de login.
2. **HEADER** (presente en toda la app):
   - 👤 Perfil del usuario
   - 🔍 Buscador
   - 🔔 Notificaciones
3. **N1 — SIDEBAR**: lista de módulos a los que tiene acceso.
4. **Detalle por módulo** (uno por uno):
   - Sub-vistas
   - Filtros
   - Detalle
   - Acciones
   - Callouts informativos / importantes / warning
   - **🔄 Cómo se usa este módulo** (flujo narrativo de cómo lo opera el rol día a día)
5. **Diferencias clave vs [otros roles]**: tabla comparativa.
6. **Diagrama ASCII general** del wireframe completo.
7. **Relacionado**: lista de wikilinks.

---

#### 01 - Ficha de Rol.md

**Función:** tarjeta resumen del rol en formato compacto.

**Estructura:** tabla con: ID Rol, Nombre, Tipo, Departamento, Reporta a, Supervisa a.

Secciones cortas:
- Descripción (1 párrafo)
- Objetivo en el sistema
- Acciones principales (bullets)
- Permisos clave
- Nivel de acceso
- Dispositivo
- Frecuencia de uso

---

#### 02 - Ficha Detallada.md

**Función:** ficha extendida con todos los campos para Sheet/Excel.

**Estructura:** una tabla larga con todos los campos de 01 + adicionales:
- Módulos que usa
- Permisos CRUD
- Dolor / Necesidad actual
- Notas UX / Recomendaciones

---

#### 03 - PRD.md

**Función:** PRD específico del rol.

**Estructura:**
- Cabecera (ID PRD, HU, Funcionalidad, Actor, Dispositivo, Estado, Versión).
- Objetivo
- Flujo General
- Casos de Uso (tabla con ID, Caso, Prioridad)
- **Reglas de Negocio Aplicables** (tabla con ID, Regla, Descripción, Prioridad)
- **Restricciones / Permisos** (tabla con #, Acción que NO puede hacer, Por qué, Quién SÍ puede)

> **Tip:** estas dos últimas secciones se hacen como tablas para que sean copiables a Google Sheets.

---

#### 04 - Permisos Detallados.md

**Función:** detalle granular de permisos del rol, módulo por módulo.

**Estructura:**

| Módulo | Funcionalidad | Permiso | Descripción |
|---|---|---|---|
| **Modulo X** | Acción 1 | ➕ Crear | Detalle de la acción |

---

#### 05 - Requerimientos.md

**Función:** lista de RFs/RRs/RNFs aplicables a este rol específicamente (filtrado de 03 - Tabla de Requerimientos).

**Estructura:**

| ID | Requerimiento | Prioridad |
|---|---|---|

Agrupar por categoría si tiene sentido (Operativas / Ejecutivas / etc.).

---

#### 06 - Resumen Rápido.md

**Función:** one-pager con lo esencial.

**Estructura:** tabla compacta con Rol, Objetivo, Permisos Clave, Dispositivo, Acciones Principales, Nivel.

---

#### 07 - Feature Map.md

**Función:** mapa visual de todas las features del rol agrupadas por módulo + clasificación primaria/secundaria.

**Estructura:** árbol con 2 ramas principales:

```
[DEPARTAMENTO] — [ROL]
│
├── PRIMARIAS
│   │
│   ├── Módulo 1
│   │   └── Features core
│   └── Módulo 2
│       └── Features core
│
└── SECUNDARIAS
    │
    ├── Análisis
    │   └── Métricas, dashboards
    └── Utilidades
        └── Exportar, descargar, soporte
```

**No usar emojis 🔴🟡 para clasificar.** La estructura del árbol clasifica. Lo que cae bajo "Primarias" son los módulos core del sidebar; lo que cae bajo "Secundarias" son features transversales (análisis, utilidades, integraciones).

---

#### 08 - Acciones del Usuario.md

**Función:** tabla de acción → resultado.

**Estructura:**

| Acción | Resultado |
|---|---|
| Click en X | Sistema ejecuta Y · Notifica a Z · Cambia estado a W |

---

#### 09 - Campos del Formulario.md

**Función:** campos de cada formulario que el rol llena.

**Estructura por formulario:**

```markdown
## A) Formulario "Nombre del formulario" (RF-X-NN)

| Campo | Tipo de Input | Obligatorio | Validación | Descripción |
|---|---|---|---|---|
```

Repetir por cada formulario (A, B, C, ...).

---

#### 10 - Validaciones.md

**Función:** casos de validación y qué hace el sistema.

**Estructura:**

| Caso | Comportamiento del Sistema |
|---|---|
| Campo vacío | Bloquea envío; muestra "..." |
| Email inválido | Bloquea envío; muestra "..." |

---

#### 11 - Respuestas del Sistema.md

**Función:** qué responde el sistema ante eventos.

**Estructura:**

| Evento | Respuesta del Sistema |
|---|---|
| Usuario X hace Y | Sistema cambia Z · Notifica a W · Registra log |

---

### 📂 Casos de Uso/ (subcarpeta dentro del rol)

#### RF-X-NN [Nombre del caso].md

**Función:** detalle narrativo de un caso de uso específico, listo para copiar al Sheet.

**Estructura fija:**

```markdown
---
tags:
  - arquitectura
  - rol/[rol]
  - caso-de-uso
aliases:
  - CU RF-X-NN
---

# 🪪 ID: RF-X-NN
🏷️ **Nombre:** [Nombre del caso]

**Historia:**
[2-4 líneas narrando el contexto y por qué importa]

**Criterios de aceptación:**
[1 párrafo con los criterios verificables]

**Documentación:**
- PRD: PRD-[DEPTO]-NN [Rol]
- Flow: [Nombre del flow]
- Prototipo: (link de Figma)

**Flujo:**
`Estado/Origen` → MANUAL → `Acción` → AUTOMATICO → `Resultado`
```

---

## 5. Pasos para aplicar la metodología en un proyecto nuevo

### Paso 1 — Definir el alcance
Lista los **departamentos** que tendrá la plataforma (ej. Ventas, Operaciones, Soporte, Finanzas).

### Paso 2 — Por cada departamento, identificar los **roles**
Mínimo 2 roles operativos + Sistema + Administrador. Para cada rol define:
- Nombre técnico (corto)
- Nombre comercial / alias
- Jerarquía (a quién reporta, a quién supervisa)
- Si es operativo, supervisor o ejecutivo

### Paso 3 — Crear `_Globales del Depto/`
Empezar SIEMPRE por aquí. Estos 9 archivos son la base que el resto referencia:
1. Portada (roles e IDs)
2. PRD del depto (objetivo + alcance)
3. **Tabla de Requerimientos** (los RFs/RRs/RNFs/RIs)
4. Por Módulo (RFs agrupados)
5. Leyenda
6. Matriz de Permisos
7. Reglas de Negocio (RRs)
8. Criterios de Aceptación
9. Dependencias

### Paso 4 — Por cada rol, crear su carpeta con 12 archivos
Empezar por:
- **00 - Arquitectura** (el wireframe — es lo más visible)
- **01 - Ficha de Rol** (resumen)
- **04 - Permisos Detallados** (qué puede hacer)
- **05 - Requerimientos** (RFs aplicables al rol)

Luego completar:
- 02 Ficha Detallada
- 03 PRD del rol
- 06 Resumen Rápido
- 07 Feature Map
- 08 Acciones del Usuario
- 09 Campos del Formulario
- 10 Validaciones
- 11 Respuestas del Sistema

### Paso 5 — Casos de Uso
Por cada RF importante del rol, crear un archivo en `Casos de Uso/` con el formato narrativo (Historia / Criterios / Flujo).

### Paso 6 — Iterar
La arquitectura es viva. Cuando cambia una regla de negocio:
1. Actualizar `07 - Reglas de Negocio.md` primero.
2. Propagar a Matriz de Permisos.
3. Ajustar archivos por rol (Permisos, Acciones, Validaciones).
4. Actualizar Casos de Uso que dependen de la regla.

---

## 6. Buenas prácticas

### Lo que SÍ hacer
- ✅ Usar wikilinks intensivamente para conectar todo.
- ✅ Tablas para Sheet/Excel: estructurar pensando en copiar-pegar.
- ✅ Descripciones cortas (1-2 líneas) bajo cada heading de módulo.
- ✅ Cada cambio de status / acción crítica con regla RR-X-NN asociada.
- ✅ Frontmatter consistente para que el grafo se vea bien.
- ✅ Casos de uso con número RF para trazabilidad.

### Lo que NO hacer
- ❌ No duplicar contenido entre archivos. Usar referencias.
- ❌ No usar ASCII art complejo si no se necesita (mejor narrativa).
- ❌ No clasificar features con colores en árbol (la estructura clasifica).
- ❌ No mezclar reglas de negocio (RR) con criterios de aceptación (AC).
- ❌ No olvidar el rol "Sistema" (automatización) y "Administrador" (en pausa).
- ❌ No empezar por wireframes sin haber definido roles + reglas + RFs.

---

## 7. Checklist de calidad

Antes de considerar la arquitectura de un depto "terminada":

- [ ] Existen los 9 archivos de `_Globales del Depto/`.
- [ ] Cada rol tiene sus 12 archivos + Casos de Uso.
- [ ] Cada RF en la tabla de requerimientos tiene un caso de uso o está cubierto por la matriz.
- [ ] Cada acción de la matriz de permisos tiene una RR que la justifica si es exclusiva.
- [ ] Los wikilinks no están rotos (`[[X]]` que no existe).
- [ ] Frontmatter consistente en todos los archivos.
- [ ] La matriz de permisos cubre TODAS las acciones del wireframe.
- [ ] Las dependencias externas están explícitas.
- [ ] Los casos de uso siguen el formato Historia + Criterios + Flujo.
- [ ] La Portada lista todos los roles correctamente con ID y descripción.

---

## 8. Cómo exportar este documento a PDF (Obsidian)

1. Abre este archivo en Obsidian.
2. Haz click en los **3 puntos** arriba a la derecha (More options).
3. Selecciona **"Export to PDF..."**.
4. Elige opciones:
   - **Paper size:** A4 o Letter
   - **Include footer**: opcional
   - **Open after export**: ✅
5. Guarda donde quieras.

> **Alternativa con Pandoc** (si no tienes Obsidian):
> ```
> pandoc "_GUIA - Plantilla de Arquitectura de Departamento.md" -o guia.pdf
> ```

---

## 9. Glosario

| Término | Significado |
|---|---|
| **PRD** | Product Requirements Document |
| **HU** | Historia de Usuario |
| **RF** | Requerimiento Funcional |
| **RNF** | Requerimiento No Funcional |
| **RR** | Requerimiento de Negocio (Regla) |
| **RI** | Requerimiento de Integración |
| **AC** | Criterio de Aceptación |
| **CU** | Caso de Uso |
| **Wireframe** | Mockup visual de baja fidelidad de las pantallas |
| **Sidebar** | Menú lateral con los módulos del rol |
| **Self-Pick** | Modelo donde los usuarios toman libremente del bandeja en vez de que alguien les asigne |
| **Trigger Automático** | Conjunto de acciones que ejecuta el sistema automáticamente al ocurrir un evento |
| **Semáforo** | Indicador visual de estado (típicamente Verde / Amarillo / Rojo) |

---

> **Esta guía es plantilla.** Aplícala a cualquier proyecto, ajustando los nombres de departamentos, roles y reglas según el dominio. La metodología es la misma.
