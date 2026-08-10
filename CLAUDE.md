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
│   ├── _Globales/               # Convenciones de Diseño, Estructura App, Roles del
│   │                            #   Sistema, Índice y Árbol de Reglas, + estándares
│   │                            #   de ingeniería (estructura de proyecto y desarrollo)
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
| **Hotel** | Manager General · Manager de Área (alias **Hotel Manager**) · Supervisor |
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
| 2026-08-10 | **El PDF de marca contradijo al sistema de diseño en dos puntos, y ganó el PDF.** Al listar las dependencias del front salieron tres huecos sin librería (router, gráficas, mapa) y una tipografía sin origen. El *Portfolio Oranje 2024* —analizado por sus fuentes y colores embebidos, no de vista— resolvió los dos que eran de diseño: **la tipografía es Montserrat, no Poppins** (siete pesos, Light a Black, en toda la maqueta; el Mark Pro que también embebe es solo la columna de anotaciones de quien armó el documento, residuo del template de referente), y **`--o-500` es #FF8000, no #FF8E00** (806 apariciones, el naranja dominante de la pieza; el valor viejo se había *derivado* porque `oranje-seed.css` nunca estuvo en disco). Corregir el primario obligó a recalcular lo que dependía de él: la regla 1 de contraste pasa a **7.4:1 con `--ink`** y **2.5:1 con blanco** —sigue reprobando AA, la regla se sostiene, y el Portfolio llega solo a la misma solución usando cafés #824613 sobre naranja—; y la separación `--st-naranja` vs `--o-500` se estrechó de **ΔE 16.7 a 11.0**, que todavía distingue pero ya no sobra. Nueva **D-17** con las tres librerías: **React Router 8 en *data mode*** (paquete `react-router`, no `react-router-dom`; framework mode contradice D-04 porque la web no tiene servidor), **Recharts** (el único con embudo nativo — Chart.js necesita un plugin comunitario justo para esa gráfica, ECharts pesa 3×) y **`@vis.gl/react-google-maps`**, cuya API key es **pública por diseño** y se restringe por referrer: excepción explícita a D-07, más abrir `maps.googleapis.com` en la CSP. **Enmienda a D-12:** Tailwind 4 eliminó la config en JS y NativeWind estable todavía exige la 3, así que el *"preset compartido"* se parte en **dos emisiones de un mismo `tokens.ts`** —`@theme` en CSS para el web, objeto JS para el móvil— hasta que NativeWind 5 sea estable; lo que la decisión protegía se conserva, el formato no. **Pendientes nuevos en `Convenciones de Diseño`:** `--o-50` y `--o-700` siguen derivados del primario viejo; #FBE7A0 (el crema de las cifras grandes sobre naranja) no es token y da 2.0:1, así que no puede ser texto; el **Figma quedó desactualizado** con el hex y la tipografía anteriores; y los colores de gráfica del Portfolio (#945BFF, #00BEA5, #E12BFF) **son del template Randstad, no de Oranje** — se descartan, así que una gráfica de más de dos series todavía no tiene con qué pintarse. | Claude |
| 2026-08-10 | **La app ya se conecta a la base, y de paso se destaparon tres cosas rotas.** El objetivo era dejarle la conexión lista al dev que va a hacer los CRUD; el hallazgo fue que **no había conexión posible**: Prisma 7 exige un *driver adapter* y no había ninguno instalado, ni `PrismaService`, ni nada registrado en `AppModule` — las migraciones corrían pero la app no. Ahora hay `PrismaService` (`@prisma/adapter-pg`, pool y timeouts por entorno, `application_name` para que `pg_stat_activity` diga de quién es la consulta), `PrismaModule` global, y `GET /api/v1/health` + `/health/db`, verificado contra la instancia real. Lo roto: **(1)** el historial de migraciones **no se podía reproducir desde cero** —`ventas_pipeline` ordenaba primero por su timestamp pero se había aplicado tercero, así que en una base nueva tocaba `commercial` antes de que el esquema existiera—; renombrada a la hora en que realmente corrió y sincronizado `_prisma_migrations`, con el replay completo verificado en la shadow database. **(2)** El **índice espacial de la geocerca estaba borrado desde el 2026-08-08**: Prisma genera un `DROP` de todo índice ausente del datamodel y este caía sobre una columna `Unsupported`; ahora está declarado con `type: Gist` y Prisma lo reconoce como suyo. **(3)** `deleteOutDir` con el cache incremental fuera de `dist` daba builds a medias que decían *Done*. Nueva migración con **los 7 esquemas vacíos de D-01** —existían en dev pero ninguna migración los creaba, así que staging y producción nacían sin ellos—. Además: `ValidationPipe` → **`ZodValidationPipe`** (Estándares §6 pide Zod, y `ValidationPipe` exige `class-validator`, que no estaba instalado: la app ni arrancaba), filtro con la forma de error de §4, Jest configurado (`pnpm test` fallaba en CI por falta de config) y la regla `no-restricted-imports` corregida —prohibía **todo** import hacia arriba, lo que hace imposible que un módulo de negocio use `infra/prisma`—. Nuevo `docs/base-de-datos.md` en el repo. **Pendiente:** el `seed.ts` sigue lanzando error a propósito; sin estados ni transiciones sembrados el semáforo no camina, y eso va **antes** que cualquier CRUD que dependa de estados. | Claude |
| 2026-08-08 | **Maquetado v1.0 de Ventas en Figma**, vía el MCP oficial de Figma. Archivo `Oranje` del proyecto de equipo — `fileKey` **g1cYWZisiq6jbXvULvUVOw**. Tres páginas: *Portada · Versiones* (una página por versión; el sistema de diseño queda fuera del versionado), *Sistema de Diseño* (34 variables + `SemaforoBadge` de 12 variantes + `Button` de 8) y *v1.0 · Ventas* con las **8 pantallas** del `Prompt - Maquetado Figma de Ventas`: Pipeline kanban de 9 columnas, Ficha del prospecto con la timeline de `prospect_state_history`, Modal de transición, Mi Territorio (mapa + lista, layout inspirado en un tracking de logística), Registrar intento, Propuesta, Dashboard con embudo y Conversión del BDC. La asimetría BD/BDC quedó visible: el modal del BD **no ofrece** la conversión a Naranja y el botón Aprobar del BDC está bloqueado hasta que exista el Usuario del Hotel. El prompt se corrigió para usar los tokens asentados —7 de sus 9 hex del semáforo diferían— y su advertencia sobre el naranja se invirtió: el choque ya está resuelto por color (`--st-naranja` ≠ `--o-500`), no por forma. **Límite operativo:** la cuenta es asiento **View** en plan **Starter**, con un tope mensual de llamadas al MCP. ⚠️ **Corregido el 2026-08-10: las llamadas de *escritura* SÍ consumen cuota** —esta línea afirmaba lo contrario y se agotó el tope a mitad de una resincronización—. Presupuestar todas las llamadas por igual, agrupar el trabajo en el menor número de scripts posible y hacer la verificación visual con `node.screenshot()` dentro del propio script. Starter tampoco publica librerías de equipo: por eso componentes y pantallas viven en el mismo archivo. **Pendiente:** el mapa es una abstracción, no tiles reales; y `oranje-seed.css` sigue sin estar en disco para contrastar los tokens derivados. | Claude |
| 2026-08-08 | **La base de Oranje ya existe y corrió su primera migración.** Instancia `oranje` (Postgres 15) en el proyecto **`oranjeapp-gcp`** —no `e-ministry-oranje`, que es otro sistema—, base `oranje`, usuario `oranje_dev`, accedida por **Cloud SQL Auth Proxy en `localhost:5433`**. Las tres migraciones de hoy (`ventas_pipeline`, `catalogs_y_roles`, `identity_y_hotel`) crearon **19 tablas**: 9 en `catalogs`, 7 en `commercial` —exactamente las de D-15— y 3 en `identity`. Nueva **§13 de `Estándares de Base de Datos`** con el acceso local: datos de la instancia, comando del proxy, las cuatro herramientas gratis (Prisma Studio, DBeaver Community, Cloud SQL Studio, `psql`) y el aviso de que nada vive en `public`, así que sin calificar el esquema o fijar `search_path` las consultas fallan. Renumeradas §13→§14 (checklist) y §14→§15 (pendientes), con las dos referencias externas actualizadas (`Estándares de Desarrollo` y D-11). **Queda desactualizado el prerrequisito 1 de D-15** (*"la instancia de Cloud SQL"*): ya está hecho. | Claude |
| 2026-08-08 | **Sistema de Diseño Oranje completado: se acabaron los `—`.** `Convenciones de Diseño` tenía 9 neutrales, 4 radios/curva, 4 sombras y **los 12 colores del semáforo sin un solo hex** — imposible maquetar sin inventar color. Derivados y asentados: rampa de neutrales cálida (`--surface-2` … `--ink-4`) con su contraste medido, radios 8/12/18, sombras tintadas con `rgba(26,17,8,…)` en vez de negro puro, y la escala de 12 estados donde **5 reutilizan un token semántico** (`--red`, `--yellow`, `--green`, `--purple`, `--ink`) y 7 son propios. Nueva §*Reglas de contraste* con tres hallazgos medidos: sobre `--o-500` el texto va en **`--ink`** (blanco da 2.3:1, reprueba AA — y el botón primario es naranja, así que toca todas las pantallas); **`--st-naranja` (#F2711C) se separa deliberadamente de `--o-500`** para no romper *un color = un significado*; y el amarillo nunca lleva texto blanco (1.4:1). `--ink-4` queda **reprobando AA a propósito**, documentado como solo-decorativo. La escala del semáforo **no se mapea a variables de shadcn** (D-16) y sirve también a Requisición y Onboarding. **Pendiente:** `oranje-seed.css` —la fuente de verdad declarada en §14— **no está en disco**; el repo `oranje-mockups` no está clonado, así que estos valores se derivaron de los hex documentados y deben contrastarse contra la semilla real cuando aparezca. | Claude |
| 2026-08-08 | **D-16 · La librería de componentes se copia al repositorio:** shadcn/ui (sobre Radix) en `apps/web` y react-native-reusables (sobre NativeWind) en `apps/mobile`, más TanStack Table para las tablas. Ninguna entra como dependencia de runtime. **DaisyUI descartada** por dos razones concretas: trae su propio sistema de tema que habría que sincronizar con los tokens Oranje, y no existe en React Native —el móvil necesitaría otra librería y se pierde el "mismas clases en los dos lados" de D-12. **Resuelve una duplicación previa:** §4 y §6 de `Estructura de Proyecto` listaban `Button, Modal, Table, StatusChip` en dos rutas sin decir cuál mandaba; ahora lo reutilizable vive en `packages/ui` y `shared/components/` de la app queda para lo atado al store, la ruta o el permiso. Tres choques documentados con su resolución: las variables de shadcn se declaran **derivadas** de `tokens.ts` (nunca al revés), la iconografía sigue siendo **Material Icons** (se cambia el import de `lucide-react` al copiar) y el `kebab-case` de shadcn se conserva **solo** en `components/ui/` para no romper `shadcn diff`. Corregido de paso el callout de §1 que afirmaba que el stack del frontend no tenía ADR: D-12 lo cubre desde el 2026-08-06. **Pendiente nuevo (5):** el catálogo de react-native-reusables no se verificó contra las pantallas del ponche; si el hueco es grande, la salida es gluestack-ui v2 y eso reabre D-12. | Claude |
| 2026-05-18 | Creación de CLAUDE.md tras análisis completo del vault (250 .md, 5 departamentos, 7 semáforos, 3 subagentes, metodología de Arquitecturas). | Claude |
| 2026-05-18 | Reestructurados los `07 - Feature Map` de Reclutamiento/Líder de Grupo y de los 3 roles de Hotel al formato PRIMARIAS/SECUNDARIAS del _GUIA. | Claude |
| 2026-05-18 | Eliminado el módulo Blacklist del depto Hotel (RF-H-29, RI-H-07 y referencias en 26 archivos de Arquitecturas/Hotel); arquitectura alineada con la fuente de verdad. `Core/` y Reclutamiento/Inspección sin cambios. | Claude |
| 2026-05-18 | Unificados los 20 Casos de Uso del depto Hotel para que toda línea `**Flujo:**` empiece con `Sidebar → [Módulo] → ...` (módulo del sidebar del rol). 16 archivos modificados; los 4 que ya lo cumplían se respetaron. | Claude |
| 2026-07-29 | Nuevas notas en `Español/Arquitecturas/_Globales/`: `Modelo de Datos.md` (esquema relacional derivado de Reglas de Negocio: 5 entidades faltantes, semáforos como datos, 8 invariantes aplicados en BD) y `Arquitectura GCP - Propuesta.drawio` (diagrama de capas). 22 wikilinks, 0 rotos. | Claude |
| 2026-07-28 | Nueva nota `Español/Arquitecturas/_Globales/Índice de Arquitecturas.md`: índice completo de los 229 archivos de `Arquitecturas/` (globales, 12 archivos por rol, 93 Casos de Uso) + sección de desviaciones frente al _GUIA. 239 wikilinks, 0 rotos, cobertura 229/229. | Claude |
| 2026-07-28 | §8 reescrito como "Subagentes y skills": §8.1 agentes (sin cambios) + §8.2 nueva, con las 12 skills de `.claude/skills/` separadas en nativas del vault (6) y de software (6), y la regla de precedencia CLAUDE.md > skill. §3 actualizado. | Claude |
| 2026-07-28 | Nueva nota global `Español/Arquitecturas/_Globales/Árbol de Reglas de Negocio.md`: árbol consolidado de RR por departamento (Ventas, Hotel, Reclutamiento), handoffs entre deptos con su regla, semáforos/catálogos compartidos y huecos detectados. Sin cambios en fuentes. | Claude |
| 2026-07-29 | Dos notas nuevas en `Español/Arquitecturas/_Globales/`: **Estructura de Proyecto y Nomenclatura** (monorepo pnpm, NestJS + Prisma + React, nomenclatura, idioma del código) y **Estándares de Desarrollo** (Git, PRs, trazabilidad RF↔código, API, DB, seguridad, testing, DoD). Primer documento de stack del proyecto; destino Confluence. Índice de `_Globales` y §3 actualizados. Huecos detectados: `Roles del Sistema.md` vacío e IDs de rol inconsistentes en Reclutamiento (`ROL-01` vs `ROL-H-01`/`ROL-V-01`). | Claude |
| 2026-08-04 | **Horas sin aprobar** resuelto: si el Hotel Manager no aprueba antes del cierre semanal, las horas **quedan en espera** — sin pago, sin factura, sin aprobación automática y sin escalamiento. Asentado en `Reglas de Negocio`, `Timesheet`, `Hotel Manager` y D-09. Queda abierta la consecuencia derivada: qué pasa con las horas aprobadas *después* del cierre (¿reabren el Consolidado de su semana o entran al siguiente?), que altera el umbral semanal de overtime de D-10. | Claude |
| 2026-08-05 | Nueva nota `Español/Arquitecturas/_Globales/Estándares de Base de Datos.md`: convenciones de Postgres (esquema por módulo, nomenclatura, tipos canónicos, restricciones, índices, migraciones expandir/contraer, crecimiento, concurrencia, zona horaria, seguridad y checklist de PR). **Resuelve dos contradicciones** del §5 de `Estándares de Desarrollo`: tablas en singular calificadas por esquema (no plural) y semáforos como tablas de catálogo (no enum nativo de Postgres). El §5 se reduce a proceso y apunta a la nota nueva. Índice de `_Globales` y wikilinks actualizados. Huecos detectados: falta `zona_horaria` en `commercial.hotel`; retención de fotos del ponche sin definir. | Claude |
| 2026-08-04 | **Aprobación de horas: por departamento y con dos aprobadores.** El [[Manager de Área]] aprueba las de su departamento y el [[Manager General]] las de cualquiera; son los dos únicos roles con esa atribución — la misma pareja que ya autoriza requisiciones. El respaldo del Manager General es manual: puede aprobar lo pendiente, pero nadie le transfiere la jornada al vencer un plazo. Propagado a 12 archivos + D-09 (alcance del permiso: `hotel_id + departamento_id` para el GH, solo `hotel_id` para el GM) y `Modelo de Datos` (el departamento se deriva por `timesheet → asignacion → slot → posicion`, no se duplica en la marca). | Claude |
| 2026-08-04 | **"Hotel Manager" es el Manager de Área**, no un rol nuevo. Se revirtió la creación de `Hotel/Hotel Manager.md` (archivo eliminado) y `Hotel Manager` quedó como **alias** de [[Manager de Área]] (`ROL-H-02`), que gana la responsabilidad de **aprobar las horas trabajadas**. El Supervisor pasa de aprobar a **revisar** (resuelve anómalos, registra manuales, no aprueba); si el Manager de Área no aprueba, las horas **quedan en espera** sin pago, factura, escalamiento ni aprobación automática. Propagado a 13 archivos + D-09 y `Modelo de Datos` (estado `revisado` + invariantes 14-15). El depto Hotel sigue con **3 roles**. **Pendiente:** en jerarquía extendida hay un Manager de Área por departamento — falta confirmar si un colaborador que sirve a dos departamentos requiere dos aprobaciones. | Claude |
| 2026-08-04 | **Ponche en la capa conceptual** (autorizado por el usuario). `Reglas de Negocio`: mecanismo de 3 marcas con GPS y foto, nueva sección *Aprobación de ponches* (Supervisor del hotel de la requisición), umbral de overtime **por requisición** en vez de por hotel, y ponche manual por rechazo de ubicación. Propagado a `Timesheet`, `Supervisor`, `Manager General`, `Manager de Área`, `Hotel`, `Reglas del Hotel`, `Reglas del Colaborador`, `Colaborador`, `Home`, `Métricas y KPIs` (KPI 4 pasa de *Generación oportuna de QR* a *Aprobación oportuna de ponches*) y 4 Simulaciones. **Pendiente:** `Arquitecturas/Hotel/` y `Arquitecturas/Colaborador/` (43 archivos, incluidos `RF-H-11 Generar QR` y `RF-C-03 Ponchar via QR`) siguen con el mecanismo viejo. | Claude |
| 2026-08-04 | **Ponche rediseñado** (QR → captura móvil con GPS y foto). `Modelo de Datos`: nueva §5 *Ponche — evidencia de presencia* (`operations.ponche_marca`, geocerca del hotel con PostGIS, doble reloj dispositivo/servidor, política de fotos y volumen), 4 invariantes nuevos, renumeración §5-§11. `Decisiones de Arquitectura`: D-05 actualizada y nueva **D-08 · El ponche se valida en el servidor**. `Estructura de Proyecto`: `apps/mobile` con colas separadas de marcas y fotos. **No se tocó la fuente de verdad**: `Reglas de Negocio` y `Timesheet` siguen diciendo QR y 6 ponches, pendientes de autorización. | Claude |
| 2026-07-31 | `Estructura de Proyecto y Nomenclatura.md` alineada con `Decisiones de Arquitectura` (D-01…D-07): backend pasa de 18 módulos ad-hoc a los **10 módulos de D-01** con un esquema Postgres por módulo, se agrega `apps/mobile` (React Native + Firebase, D-05), identidad vía Firebase Auth con permisos en Postgres, tablas Postgres en singular calificadas por esquema, regla llamada-directa-vs-evento según el núcleo transaccional, nueva §10 de trazabilidad D-01…D-07 → estructura, y el anexo de dos repositorios pasa de opción abierta a variante descartada. Índice de `_Globales` completado con los 4 documentos que faltaban. 0 wikilinks rotos. Hueco abierto: D-01 no asigna módulo al expediente del Colaborador. | Claude |
| 2026-05-21 | Vault: nuevo depto **Customer Service** (CS Manager + Agente + Flujo + Reglas); **Contabilidad** refactorizada (rol Contabilista eliminado → **Manager de Contabilidad** + **Contadora**, módulos Deducciones/Facturación/Vacaciones/Flujo de Nómina); nueva carpeta raíz **Simulaciones/** con 7 puntos de vista; nuevo **Core/Blueprint.md** (patrón replicable). CLAUDE.md sincronizado con el estado del vault (§1, §3, §4, §12). | Claude |
| 2026-08-07 | **El código sale del vault: monorepo creado y subido.** `Documents/orange/oranje` → **github.com/dev3-cc/oranje**, 69 archivos, verificado corriendo (`lint`, `typecheck`, `build`, `format`, `prisma validate`). pnpm workspaces + NestJS con los 10 módulos de D-01 + Prisma 7 multiSchema + Zod al arranque + ESLint/Prettier compartidos + Husky + CI. **Cuatro ajustes que la realidad obligó** y quedaron documentados en el README del repo: TypeScript fijado en **6** (typescript-eslint no soporta TS 7 y el linter es obligatorio por §9), `eslint-plugin-import-x` en vez de `eslint-plugin-import` (truena con ESLint 10), la `url` de Prisma se mudó a `prisma.config.ts`, y TS 7 eliminó `baseUrl`. **`develop` → `staging`**: la rama se llama como el ambiente al que despliega, así que la §11 ya no tiene que traducir; §1 y §11 actualizadas. **Ramas protegidas** en GitHub (PR obligatorio, 1 aprobación, CI en verde, sin force push, historial lineal) + `CODEOWNERS` para las rutas sensibles. **Límite asentado en §2:** GitHub no puede exigir 2 aprobaciones solo por ruta —el contador es global por rama— y `enforce_admins` queda en `false` porque con un solo colaborador nadie podría aprobar su propio PR. Nota: el repo quedó **público**, advertido antes del push y confirmado por el usuario. | Claude |
| 2026-08-07 | **Los diez esquemas pasan a inglés: se acabó la única excepción de idioma.** `identidad → identity`, `comercial → commercial`, `demanda → demand`, `cobertura → coverage`, `operacion → operations`, `liquidacion → settlement`, `notificaciones → notifications`, `catalogos → catalogs`; `supervision` y `journal` no cambian. **242 reemplazos** en 10 archivos (los 6 globales, los 2 diagramas de Ventas, el Índice y este archivo), aplicados solo a identificadores calificados, backticks y rutas de carpeta — la prosa española quedó intacta. Reescritos los textos que **afirmaban** la excepción: el callout §2 de `Estándares de BD` (*"El esquema en español"* → *"Todo el identificador en inglés"*), la §8 de `Estructura de Proyecto`, la decisión de **D-11**, el `schemas = [...]` de Prisma, `modulos/ → modules/` en D-01, y la caja *"Por qué el esquema quedó en español"* del diagrama v2. Corregido de paso `identidad.permiso` → **`identity.role_permission`** en D-05. Consecuencia: desaparece el `demanda.requisition` mezclado, y con él el último lugar donde había que recordar qué mitad del identificador iba en cada idioma. | Claude |
| 2026-08-07 | **Dos hallazgos de una sesión nueva, corregidos.** (1) El diagrama declaraba `pgcrypto -- uuid` pero la §4 dice que el `id` lo genera la aplicación en **v7**, y `gen_random_uuid()` devuelve **v4**: comentario corregido a *cifrado de campo (§12)* y nuevo callout en §4 explicando que **no hay `DEFAULT` en la PK** y por qué. (2) `Modelo de Datos` no cubría el pipeline comercial, así que había dos fuentes para `commercial` sin decir cuál manda: nuevo callout tras el ER que declara **autoridad del `Ventas - Modelo de Datos v2.drawio` sobre `commercial`**, con las 7 tablas y el aviso de que el `Hotel` de ese ER ya no lleva estado de semáforo ni columnas de contacto. Se resolvió con puntero, no duplicando las tablas — dos copias son dos lugares que mantener. | Claude |
| 2026-08-07 | **Dos ADR nuevos para que una sesión nueva arranque con contexto.** **D-14** (cada semáforo guarda historia, no solo estado actual: tabla de historia + columna de estado como desnormalización deliberada; por qué el `journal` no la sustituye —particionado, `jsonb`, retención de 24 meses—; aplica a los **7 semáforos**, hoy solo existe la de `prospect`) y **D-15** (la primera migración es `catalogs` + `identity` + `commercial` juntos porque las FK cruzan; por qué se empieza por **Ventas**; los 5 prerrequisitos antes del primer `prisma migrate`; el modelo vigente son las **7 tablas** de `Ventas - Modelo de Datos v2.drawio` y los otros diagramas son borradores). **D-01** reafirmado: se evaluó el esquema plano y se descartó, dejando escrito el contraargumento real —con un solo usuario de conexión los esquemas no hacen cumplir nada, y `ALTER TABLE ... SET SCHEMA` hace la decisión reversible—; `multiSchema` de Prisma es **GA desde 6.13.0**. **Estándares de BD §5** gana la tercera opción (`text` + `CHECK`) con el criterio que decide: **quién agrega el valor**, no cuántos valores hay. | Claude |
| 2026-08-07 | `Semáforo Onboarding y Base de Datos.drawio` corregido: identificadores al inglés (esquemas y prosa en español) y los **códigos de estado del Semáforo Onboarding a inglés** — `GRAY`, `LIGHT_BLUE`, `GREEN`, `YELLOW`, `PINK`, `ORANGE`, `RED`, `BROWN`, `BLACK` — sin tocar los 9 estados, sus colores visibles ni las 12 transiciones. Se conserva porque tiene el **seed** que v2 no trae. La página 2 quedaba **contradiciendo a v2** (el semáforo en `hotel`, contacto como columnas, prospecto y cliente como un mismo registro): lleva ahora un aviso que la marca como superada y lista las 4 diferencias. Detectado de paso: `Propuesta de Base de Datos - Ventas.drawio` usa **`catalogs`** en vez de `catalogs` — error de traducir el nombre del esquema, que es justo lo que no se traduce; pendiente de borrar o corregir. | Claude |
| 2026-08-07 | **`Modelo de Datos` pasa a inglés** — el cuarto documento y el último con deuda de D-11. ~200 identificadores traducidos (entidades, columnas, valores de enum: `punch_mark`, `work_date`, `CLOCK_IN`/`LUNCH`/`CLOCK_OUT`, `weekly_consolidation`, `no_double_assignment`, `inside_geofence`), diagrama ER reescrito y realineado, y la prosa española intacta a propósito dentro de los bloques SQL (los comentarios `--` y los paréntesis explicativos se enmascararon antes del barrido). Los diez esquemas siguen en español. **Pendientes de §11: de 7 a 6** — se fue "idioma del esquema", cerrado por D-11. 0 wikilinks rotos. | Claude |
| 2026-08-07 | **Hueco de permisos de D-09 resuelto.** `Modelo de Datos` §9 reescrita: la capacidad vive en `identity.role_permission` (`role_id, module, action`, la Matriz pura) y **el alcance vive en la persona** — `identity.user.hotel_id` + `department_id` nulable, donde `NULL` significa *todos los departamentos de mi hotel* y es lo que separa al Manager General del Manager de Área. Tres formas de alcance documentadas (hotel+depto, hotel completo, zonas vía `user_zone`) más la consulta de autorización. **Se descartó** la tabla de concesiones individuales: el vault no delega la aprobación, el respaldo documentado es el Manager General y ya alcanza por `NULL`. D-09 actualizada — el efecto se conserva, el mecanismo cambia (el alcance en el permiso obligaba a una fila por hotel × depto × acción). **Deuda nueva:** §9 está en inglés y el resto de `Modelo de Datos` sigue en español; falta el barrido de D-11 en ese documento. | Claude |
| 2026-08-06 | **El Colaborador se llama `Worker` en el código** — decisión cerrada, y con ella el idioma completo. `Employee` descartado (el Colaborador no es empleado del hotel). Se evaluó y se descartó dejarlo en español como excepción: a diferencia de los esquemas —donde el español va como prefijo separado por un punto— `colaborador` viviría **dentro** de `colaborador_id`, `getColaboradorById`, `availableColaboradores`, o sea en la palabra más frecuente del sistema. En el vault, la UI y las juntas sigue siendo **Colaborador**; el glosario canónico es el puente. Actualizados D-11 y su lista de pendientes (de 5 a 4), §14.3 de `Estándares de Base de Datos` y §8 de `Estructura de Proyecto`. Verificado además que **`multiSchema` de Prisma es GA desde la 6.13.0**: los esquemas por módulo ya no dependen de un preview feature. | Claude |
| 2026-08-06 | `catalogs.contact_attempt_type` **eliminado** del modelo de Ventas: 3 valores fijos y ninguna pantalla del vault donde el negocio agregue un canal, así que `attempt_type` pasa a `text` + `CHECK`. Mismo tratamiento para **`outcome`**, que era texto libre y por eso no se podía agrupar el reporte por resultado — el hueco queda cerrado sin tabla nueva. Nueva restricción 9 en la página 3 con el criterio de §5 (catálogo solo si el negocio agrega valores sin avisar) y el camino de vuelta si algún día lo pide: sembrar la tabla desde el `CHECK` y pasar la columna a FK. Ventas se queda en **7 tablas** y ya no crea ningún catálogo. | Claude |
| 2026-08-06 | **Deuda de D-11 pagada: los tres estándares pasan a inglés.** `Estructura de Proyecto y Nomenclatura` §8 invertida (enunciado, ejemplos, *por qué*, glosario de 20 filas) + árbol de `packages/domain` y nombres de feature/pantalla; `Estándares de Base de Datos` §2 completa (`is_`/`has_`, `created_at`, `ux_slot_active_assignment`) con callout nuevo de por qué `demand.requisition` mezcla idiomas a propósito; `Estándares de Desarrollo` §4 (rutas, códigos de error, ramas) con la frontera nueva: **identificadores en inglés, textos que lee una persona en español**. Los códigos del Semáforo de Requisición pasan a inglés (`APPLE_GREEN`, `GREEN`, `LIGHT_BLUE`, `RED`, `PURPLE`) sin tocar estados ni transiciones. `Decisiones de Arquitectura` D-11 actualizada: la tabla de "documentos desactualizados" pasa a "qué cambió". **Único pendiente del idioma:** el nombre inglés del Colaborador — los tres documentos usan `Worker` como propuesta provisional. Corregido de paso `settlement.contrato` → **`commercial.contract`** en el diagrama de Ventas (el mapa de módulos de §3 asigna el Contrato a `commercial`). | Claude |
| 2026-08-06 | Nueva tabla **`commercial.proposal`** (una fila por versión, tarifas tentativas globales) en `Ventas - Modelo de Datos v2.drawio`; Ventas pasa a **7 tablas**. Corrige una lectura errónea del semáforo: la Propuesta Personalizada vive en **VERDE**, y lo que se negocia en **ROSA** es el Documento de T&C creado en **AMARILLO** — son dos documentos, no uno. **Aplazado con criterio:** `proposal_rate` (tarifa por posición, migración aditiva cuando el negocio lo pida) y `terms_and_conditions`, cuyos campos son los de `settlement.contrato` + `contrato_tarifa` — modelarlo antes de diseñar `settlement` es adivinar la forma del padre desde el hijo. Consecuencia asumida: **ROSA no tiene fila que lo respalde**. | Claude |
| 2026-08-06 | `Ventas - Modelo de Datos v2.drawio` corregido tras auditoría de las 18 relaciones: **`contact_attempt.hotel_id`** con dos FK compuestas (cerraba el diamante prospect/hotel_contact, que permitía citar el contacto de otro hotel) y **nueva tabla `commercial.prospect_state_history`** (el semáforo solo guardaba estado actual; sin ella no hay tiempo-en-estado ni motivo de transición fuera del cierre). Ventas pasa de 5 a **6 tablas**; desnormalizaciones deliberadas de 3 a 5. Huecos abiertos: `contact_attempt.outcome` es texto libre mientras `attempt_type` sí es catálogo, y no existe tabla `proposal` que sustente el estado ROSA. | Claude |
| 2026-08-06 | ADR: **D-11** (dominio del código en inglés; esquemas en español por D-01; `code` de semáforo en inglés y `name` en español), **D-12** (Redux Toolkit + RTK Query, Tailwind + NativeWind) y **D-13** (el ciclo comercial como entidad `prospect`, con el Semáforo Onboarding y un solo ciclo abierto por hotel). Cerrada la pendiente del idioma del esquema; abierta la del nombre inglés del Colaborador (`Employee` es inexacto). Alcance del ADR ampliado a stack y modelo. **Deuda:** §8 de Estructura de Proyecto y §14.3 de Estándares de Base de Datos siguen afirmando dominio en español. | Claude |
| 2026-08-05 | Nuevos diagramas en `Español/Arquitecturas/Ventas/`: `Semáforo Onboarding y Base de Datos.drawio` (máquina de estados corregida —faltaba Amarillo—, tablas y seed de estados/transiciones), `Propuesta de Base de Datos - Ventas.drawio` (mínimo esencial + ER + restricciones + glosario ES→EN) y `Ventas - Modelo de Datos v2.drawio` (5 tablas, cardinalidades 1:N y N:M, normalización y restricciones conforme a Estándares de Base de Datos). Huecos detectados: el Modelo de Datos no cubre el pipeline comercial (interacción, propuesta, T&C) y falta `time_zone` en `hotel` (§11 de Estándares de BD). | Claude |
| 2026-08-04 | `Estructura de Proyecto y Nomenclatura.md`: stack del frontend fijado en **Redux Toolkit + RTK Query** (un solo store; adiós TanStack Query y Zustand) y **Tailwind + NativeWind** (preset compartido web/móvil en `packages/ui`). Afecta §1, §2, §4, §5, §6, §7.1 y §7.5. Pendiente: estas cuatro elecciones **no tienen ADR** en `Decisiones de Arquitectura`. | Claude |
| 2026-08-04 | `Estructura de Proyecto y Nomenclatura.md`: propagados **D-09** (solo el ponche aprobado paga) y **D-10** (overtime por requisición) al mapa de módulos, §10 de trazabilidad (D-08…D-10, tabla reordenada) y glosario (`ConsolidadoDetalle`); nueva **§7.5 Nomenclatura de móvil** (React Native: `Screen`, `Navigator`, `.styles.ts`, colas `cola*`, sin CSS). Hueco registrado: ningún sidebar del vault define dónde el Supervisor aprueba las marcas. | Claude |
| 2026-07-29 | `Estándares de Desarrollo.md` §8 y §11 actualizados. §8: nuevo nivel de test de **Concurrencia**, los dos tests críticos (Self-Pick con `SKIP LOCKED` y golden test de nómina) y política de **datos de prueba sin PII real** (prohibido dump de producción a staging). §11: tabla de los 3 ambientes de D-06 con su disparador (`oranje-dev` sin CI), regla de **un solo artefacto** (build en `develop`, prod reutiliza digest, no reconstruye), tag en `main` como registro de rollback, paridad de staging y criterio para migrar a candidatos `-rc.N`. Modelo de promoción confirmado: `branches → staging → prod`. | Claude |

<!--
Mantén esta tabla en orden cronológico inverso o directo (directo aquí).
Una línea por sesión con cambio estructural. Fecha YYYY-MM-DD.
-->
