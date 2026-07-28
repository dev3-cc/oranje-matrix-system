# CLAUDE.md — Contexto permanente del vault Oranje Matrix System

> Este archivo es el **contexto base** que Claude carga en cada sesión. Es la fuente
> de orientación, no la fuente de verdad del negocio (esa es
> [Reglas de Negocio](Core/Módulos/Reglas%20de%20Negocio.md)).
> **Mantenerlo vivo es obligatorio:** ver §11 *Protocolo de auto-actualización*.

---

## 1. Qué es esto

**Oranje Matrix System** es un **vault de Obsidian** (documentación, **no código**) que
modela un sistema de **gestión organizacional para staffing de hoteles**: documenta
roles, procesos, reglas de negocio, semáforos (máquinas de estado) y módulos
operativos, desde el reclutamiento del personal hasta su asignación y seguimiento en
hoteles clientes.

- **Naturaleza:** ~270 archivos `.md` en Obsidian Flavored Markdown. No hay build, no
  hay tests, no hay código ejecutable. El "producto" es la documentación misma.
- **Entidad central:** el **Colaborador** — la persona que se recluta, asigna y opera
  en los hoteles. Su ciclo de vida es el eje del sistema.
- **Punto de entrada:** [Home.md](Home.md) — índice maestro con todos los wikilinks.

## 2. Idioma y estilo

- **Todo el vault está en español.** Escribe y responde **en español**.
- Usa la **terminología exacta** del vault. No traduzcas, no renombres, no uses
  sinónimos (es `Semáforo de Posiciones de la Requisición`, nunca "Semáforo de
  Cobertura").
- **No agregues emojis** a las notas salvo que el usuario lo pida o el archivo
  hermano ya los use (las Arquitecturas sí usan emojis en headings; las notas
  conceptuales de `Core/`, `Hotel/`, etc. no).
- Descripciones cortas (1-2 líneas) bajo cada heading. Nada de relleno.

## 3. Mapa del vault

```
oranje-matrix-system/
├── Home.md                      # Índice maestro (empieza por aquí)
├── CLAUDE.md                    # Este archivo
│
├── Core/                        # Núcleo transversal del sistema
│   ├── Blueprint.md             # Patrón replicable del sistema organizacional
│   ├── Catálogos/               # Posiciones, Zonas, Niveles de Inglés,
│   │                            #   Departamentos del Hotel, Modalidades de Contratación
│   └── Módulos/
│       ├── Reglas de Negocio.md # ★ FUENTE DE VERDAD del negocio
│       ├── Semáforos/           # 7 máquinas de estado (ver §4)
│       ├── Requisicion/         # Requisición.md + Flujo de Requisición.md
│       ├── Accidente Laboral/   # Accidente Laboral.md + Flujo
│       ├── Blacklist.md · Contrato.md · Pool de Colaboradores.md
│       ├── Schedule.md · Timesheet.md
│
├── Colaborador/                 # Entidad central + sus reglas
├── Hotel/                       # Roles: Manager General, Manager de Área, Supervisor
├── Reclutamiento/               # Roles + Flujo de Reclutamiento + Self-Pick
├── Inspección/                  # Coordinador, Inspector
├── QA/                          # Manager de QA, Operador de QA, Dashboard, KPIs
├── Ventas/                      # BD, BDC + Onboarding-Hotel (flujo y conceptos)
├── Customer Service/            # CS Manager, Agente de CS + Flujo y Reglas de CS
├── Contabilidad/                # Manager de Contabilidad, Contadora + Flujo de Nómina,
│                                #   Consolidado, Deducciones, Facturación, Vacaciones
├── Simulaciones/                # Recorridos narrativos: 7 puntos de vista
│                                #   (Colaborador, Hotel, Reclutamiento, QA,
│                                #    Inspección, Ventas, Contabilidad)
│
├── Arquitecturas/               # Arquitectura de software por departamento
│   ├── _GUIA - Plantilla de Arquitectura de Departamento.md  # ★ Metodología (§7)
│   ├── _Globales/               # Convenciones, Estructura App, Roles del Sistema
│   ├── Hotel/ · Reclutamiento/ · Ventas/
│   │   ├── _Globales del Depto/ # 9 archivos compartidos
│   │   └── [Rol]/               # 12 archivos (00–11) + Casos de Uso/
│
└── .claude/
    ├── agents/                  # 3 subagentes especializados (ver §8.1)
    └── skills/                  # 12 skills: 6 de vault + 6 de software (ver §8.2)
```

Las carpetas conceptuales (`Hotel/`, `Reclutamiento/`, etc.) describen **qué hace** cada
rol/módulo en el negocio. `Arquitecturas/` describe **cómo se construye el software**
de cada departamento.

## 4. Modelo de dominio

### Departamentos y roles

| Departamento | Roles |
|---|---|
| **Reclutamiento** | Manager de Reclutamiento · Líder de Grupo de Reclutadoras · Reclutadora |
| **Hotel** | Manager General · Manager de Área · Supervisor |
| **Inspección** | Coordinador · Inspector |
| **QA** | Manager de QA · Operador de QA (5, uno por depto supervisado) |
| **Ventas** | Business Developer Coordinator (BDC) · Business Developer (BD) |
| **Customer Service** | Customer Service Manager · Agente de Customer Service |
| **Contabilidad** | Manager de Contabilidad · Contadora |

QA **observa, mide y retroalimenta**; no ejecuta la operación de ningún departamento.

### Semáforos (máquinas de estado) — `Core/Módulos/Semáforos/`

1. **Semáforo del Colaborador** — situación del colaborador (12 estados/colores).
2. **Semáforo de Requisición** — ciclo de vida de una requisición.
3. **Semáforo de Posiciones de la Requisición** — % de cobertura por posición.
4. **Semáforo de Urgencia de Requisición** — urgencia por tiempo restante.
5. **Semáforo Onboarding** — ciclo comercial del hotel (prospecto → cliente).
6. **Indicador de Calidad** — desempeño de un área supervisada por QA.
7. **Indicador de Cumplimiento del Timesheet** — horas reales vs. contractuales.

Regla de oro de semáforos: **un semáforo = una dimensión**; **un color = un
significado por semáforo** (los colores sí se reutilizan entre semáforos distintos).

### Flujos de proceso

- [Flujo de Requisición](Core/Módulos/Requisicion/Flujo%20de%20Requisición.md)
- [Flujo de Reclutamiento](Reclutamiento/Flujo%20de%20Reclutamiento.md) — continuo
- [Flujo de Onboarding](Ventas/Onboarding-Hotel/Flujo%20de%20Onboarding.md)
- [Flujo de Accidente Laboral](Core/Módulos/Accidente%20Laboral/Flujo%20de%20Accidente%20Laboral.md)
- [Flujo de Customer Service](Customer%20Service/Flujo%20de%20Customer%20Service.md)
- [Flujo de Nómina](Contabilidad/Flujo%20de%20Nómina.md)

## 5. Jerarquía documental (fuente de verdad)

```
Reglas de Negocio.md   ← define las reglas (RR). FUENTE DE VERDAD.
        │
        ├── Semáforos   ← implementan las reglas como estados/transiciones
        ├── Flujos      ← implementan las reglas como procesos
        └── Notas de rol / módulo  ← describen quién hace qué
                │
                └── Arquitecturas/  ← traducen todo lo anterior a software
```

Al haber un conflicto entre documentos, **gana `Core/Módulos/Reglas de Negocio.md`**.
Nunca modifiques la fuente de verdad sin autorización explícita del usuario.

## 6. Convenciones de escritura (Obsidian)

- **Wikilinks:** `[[Nota]]` o con alias `[[Ruta/Nota|Texto visible]]`. Conecta
  intensivamente; el grafo se construye solo. Verifica que el destino existe.
- **Frontmatter** en todo archivo:
  ```yaml
  ---
  tags:
    - modulo/<nombre>        # notas Core/conceptuales
    - arquitectura           # notas de Arquitecturas/
    - rol/<rol>              # archivos de un rol
  aliases:
    - Nombre principal
    - Variantes comunes
  ---
  ```
- **Callouts:** `> [!info]` (alcance), `> [!note]`, `> [!tip]`, `> [!important]`,
  `> [!warning]`.
- **Sección `## Relacionado`** al final de casi toda nota, con los wikilinks salientes.
- **No dupliques contenido entre archivos** — referencia con wikilink.
- **Nunca toques archivos `.pen`** con Read/Edit (están cifrados; usa MCP `pencil`
  si fuera necesario, o alerta al usuario).

## 7. Metodología de Arquitecturas

`Arquitecturas/` sigue una plantilla estricta documentada en
[_GUIA - Plantilla de Arquitectura de Departamento](Arquitecturas/_GUIA%20-%20Plantilla%20de%20Arquitectura%20de%20Departamento.md).
**Léela antes de crear o editar cualquier archivo bajo `Arquitecturas/`.**

- Cada depto: `_Globales del Depto/` con 9 archivos (`01 - Portada` … `09 -
  Dependencias`) + una carpeta por rol con 12 archivos (`00 - Arquitectura` … `11 -
  Respuestas del Sistema`) + subcarpeta `Casos de Uso/`.
- **IDs:** roles `ROL-[X]-[NN]` · requerimientos `RF/RNF/RR/RI-[X]-[NN]` ·
  PRDs `PRD-[DEPTO]-[NN]` · aceptación `AC-[X]-[NN]`.
  `X` = inicial del depto (R/H/V).
- Iterar = actualizar primero `07 - Reglas de Negocio`, luego propagar a Matriz de
  Permisos, archivos de rol y Casos de Uso.

## 8. Subagentes y skills (`.claude/`)

### 8.1 Subagentes (`.claude/agents/`)

Delega a estos agentes según la tarea — están afinados para este vault:

| Agente | Cuándo usarlo |
|---|---|
| **oraculo** | Consultar/analizar/validar **sin riesgo de inventar**. Solo lee y cita fuentes. Preguntas "¿cómo funciona X?", "¿quién hace Y?", "¿es correcto que Z?". |
| **cirujano** | Editar/crear/renombrar/reestructurar archivos del vault con propagación de wikilinks. "agrega", "modifica", "corrige", "mueve", "crea el módulo de X". |
| **semaforo-guardian** | Cualquier cambio en `Core/Módulos/Semáforos/` o que toque estados/colores/transiciones. Invocar proactivamente ante "semáforo", "status", un color usado como estado. |

`cirujano` y `oraculo` **no modifican semáforos**: eso es del `semaforo-guardian`
(excepción: añadir un enlace en `## Relacionado`).

### 8.2 Skills (`.claude/skills/`)

12 skills instaladas a nivel de proyecto: aplican a **todos** los colaboradores del
repo, no solo a quien las instaló. Se cargan solas cuando la petición coincide con su
`description`; también se invocan a mano con `/nombre-de-la-skill`.

**Nativas del vault** — son las que de verdad se usan aquí:

| Skill | Cuándo se activa |
|---|---|
| **obsidian-markdown** | Wikilinks, embeds, callouts, frontmatter, tags. La skill por defecto al escribir cualquier `.md`. |
| **obsidian-cli** | Operar el vault desde terminal (leer, crear, buscar notas, propiedades). Requiere Obsidian abierto. |
| **obsidian-bases** | Archivos `.base` — vistas tipo tabla/tarjetas, filtros y fórmulas sobre notas. |
| **json-canvas** | Archivos `.canvas` — mapas mentales, diagramas de flujo, lienzos visuales. |
| **file-organizer** | Reorganizar carpetas, detectar duplicados, reestructurar el vault. |
| **defuddle** | Leer una URL externa limpia (usar en lugar de WebFetch, salvo si la URL termina en `.md`). |

**De software** — para cuando `Arquitecturas/` pase de documentación a producto real:

| Skill | Cuándo se activa |
|---|---|
| **senior-architect** | Diseño de sistema, decisiones de stack, diagramas de arquitectura, trade-offs, patrones de integración. La más alineada con `Arquitecturas/`. |
| **ui-ux-pro-max** | Estilos, paletas, tipografía, accesibilidad, charts. Útil para los `00 - Arquitectura [Rol]` (wireframes) y los `12 - Mockup y Decisiones de UI`. |
| **frontend-design** | Construir interfaces reales (React, HTML/CSS) a partir de los wireframes. |
| **senior-fullstack** | Scaffolding de proyecto completo (React/Next + Node + Postgres). |
| **senior-backend** | APIs, queries, auth, performance. |
| **clean-code** | Estándares de código conciso, sin sobre-ingeniería. |

> [!warning]
> Las 6 skills de software esperan **código** (React, Node, Postgres, Go). Este vault
> no tiene código, así que **no se van a activar solas** mientras el trabajo sea
> documentación. Invócalas explícitamente con `/nombre` si las necesitas.

> Las skills **no sustituyen** las reglas de este archivo. Ante conflicto entre una
> skill genérica y el §2 (idioma, terminología, sin emojis) o el §7 (metodología de
> Arquitecturas), **gana CLAUDE.md**.

## 9. Reglas de oro al trabajar aquí

1. **Lee antes de editar.** Nunca edites un archivo (ni sus relacionados) sin leerlo
   completo primero.
2. **No inventes.** Estados, roles, flujos, reglas, módulos: si no está en el vault,
   no existe. Repórtalo como vacío, no lo rellenes.
3. **Cambio mínimo.** `Edit` para cambios puntuales; `Write` solo para archivos
   nuevos. No reformatees lo que no cambia.
4. **Propaga.** Si cambias un nombre/alias/regla, busca con Grep TODAS las
   referencias en el vault y actualízalas (wikilinks incluidos).
5. **Valida.** Tras editar: wikilinks no rotos, frontmatter consistente,
   `## Relacionado` al día.
6. **Replica patrones.** Antes de crear un archivo, lee un hermano del mismo
   tipo/carpeta y copia su estructura exacta.
7. **Cita fuentes** al responder consultas: `[Nombre](ruta/archivo.md)`.

## 10. Git y colaboración

- Rama principal: `main`. Ramas activas: `sebas-dev`, `sebas-nucleo`,
  `sebas-arquitectura`, `victor`, `desarrollo`, `AI-COLAB`, `AI-INFERENCE`.
- Varios colaboradores trabajan en paralelo (Sebastian, Victor, Hugo). **No
  commitees ni hagas push salvo que el usuario lo pida.** Si lo pide y estás en
  `main`, crea rama primero.
- `.obsidian/` puede aparecer modificado por uso normal de Obsidian (no es un
  cambio significativo a documentar).

## 11. Protocolo de auto-actualización de este archivo

**Este CLAUDE.md debe reflejar siempre el estado real del vault.** Al cerrar
cualquier sesión de trabajo que haya producido un **cambio estructural**, antes de
entregar el resultado:

1. **Evalúa si el cambio afecta a este archivo.** Disparadores típicos:
   - Se creó/eliminó/renombró un **departamento, rol, módulo, semáforo, flujo o
     catálogo** → actualizar §3 y/o §4.
   - Cambió la **fuente de verdad** o la jerarquía documental → §5.
   - Cambió una **convención** de escritura o de IDs → §6 / §7.
   - Se añadió/modificó un **subagente** en `.claude/agents/` → §8.
   - Cambió el **flujo de Git/ramas** → §10.
2. **Aplica la edición mínima** a la sección afectada (usa `Edit`, no reescribas
   todo el archivo).
3. **Registra siempre** una línea en la Bitácora (§12), aunque el cambio no haya
   tocado las secciones anteriores.
4. Si nada estructural cambió (solo redacción menor), **no toques** este archivo.

> No es necesario un hook ni automatización externa: este protocolo es la
> instrucción operativa. Si el usuario quiere forzarlo por hook (PostToolUse /
> Stop en `settings.json`), usar la skill `update-config` para configurarlo.

## 12. Bitácora de cambios

| Fecha | Cambio | Por |
|---|---|---|
| 2026-05-18 | Creación de CLAUDE.md tras análisis completo del vault (250 .md, 5 departamentos, 7 semáforos, 3 subagentes, metodología de Arquitecturas). | Claude |
| 2026-05-18 | Reestructurados los `07 - Feature Map` de Reclutamiento/Líder de Grupo y de los 3 roles de Hotel al formato PRIMARIAS/SECUNDARIAS del _GUIA. | Claude |
| 2026-05-18 | Eliminado el módulo Blacklist del depto Hotel (RF-H-29, RI-H-07 y referencias en 26 archivos de Arquitecturas/Hotel); arquitectura alineada con la fuente de verdad. `Core/` y Reclutamiento/Inspección sin cambios. | Claude |
| 2026-05-18 | Unificados los 20 Casos de Uso del depto Hotel para que toda línea `**Flujo:**` empiece con `Sidebar → [Módulo] → ...` (módulo del sidebar del rol). 16 archivos modificados; los 4 que ya lo cumplían se respetaron. | Claude |
| 2026-07-28 | Nueva nota `Español/Arquitecturas/_Globales/Índice de Arquitecturas.md`: índice completo de los 229 archivos de `Arquitecturas/` (globales, 12 archivos por rol, 93 Casos de Uso) + sección de desviaciones frente al _GUIA. 239 wikilinks, 0 rotos, cobertura 229/229. | Claude |
| 2026-07-28 | §8 reescrito como "Subagentes y skills": §8.1 agentes (sin cambios) + §8.2 nueva, con las 12 skills de `.claude/skills/` separadas en nativas del vault (6) y de software (6), y la regla de precedencia CLAUDE.md > skill. §3 actualizado. | Claude |
| 2026-07-28 | Nueva nota global `Español/Arquitecturas/_Globales/Árbol de Reglas de Negocio.md`: árbol consolidado de RR por departamento (Ventas, Hotel, Reclutamiento), handoffs entre deptos con su regla, semáforos/catálogos compartidos y huecos detectados. Sin cambios en fuentes. | Claude |
| 2026-05-21 | Vault: nuevo depto **Customer Service** (CS Manager + Agente + Flujo + Reglas); **Contabilidad** refactorizada (rol Contabilista eliminado → **Manager de Contabilidad** + **Contadora**, módulos Deducciones/Facturación/Vacaciones/Flujo de Nómina); nueva carpeta raíz **Simulaciones/** con 7 puntos de vista; nuevo **Core/Blueprint.md** (patrón replicable). CLAUDE.md sincronizado con el estado del vault (§1, §3, §4, §12). | Claude |

<!--
Mantén esta tabla en orden cronológico inverso o directo (directo aquí).
Una línea por sesión con cambio estructural. Fecha YYYY-MM-DD.
-->
