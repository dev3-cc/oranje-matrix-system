---
tags:
  - arquitectura
  - global
  - diseño
aliases:
  - Estructura General App
  - Layout Base
  - Estructura de la App
---

# Estructura General de la App

Define el layout base y las convenciones de navegación que comparten todos los roles de la plataforma Oranje web. La implementación visual de estos patrones vive en `oranje-seed.css` — ver [[Convenciones de Diseño]].

> [!info]
> Esta estructura aplica a todos los roles de gestión (web escritorio). El Colaborador la usa en su versión web; su versión móvil mantiene el mismo modelo de navegación adaptado a pantalla pequeña.

---

## Layout base

```
┌─────────────────────────────────────────────┐
│                  SIDEBAR (248px)  │  MAIN   │
│  Logo Oranje                      │ HEADER  │
│  ─────────────────                │ ─────── │
│  Módulo 1                         │ CONTENT │
│  Módulo 2                         │         │
│  Módulo 3                         │         │
│  ...                              │         │
│  ─────────────────                │         │
│  Cerrar sesión                    │         │
└─────────────────────────────────────────────┘
```

- **Sidebar** (`.sidebar`, `--sb: 248px`): columna izquierda fija con los módulos del rol.
- **Main** (`.main`): área derecha dividida en header sticky y content con scroll.
- **Header** (`.header`, `--hd: 64px`): franja superior permanente.
- **Content** (`.content`): área de scroll donde se renderiza la vista activa.

---

## Header (universal)

El header es idéntico en todos los roles. Se divide en dos zonas:

**Izquierda — breadcrumbs** (`.hd-crumbs`): indica la ruta de navegación actual (ej. `Inicio / Mi Schedule`).

**Derecha — acciones globales:**

| Elemento | Componente | Comportamiento |
|---|---|---|
| Ayuda | `.ic-btn` | Abre documentación o tooltip de ayuda |
| Campana | `.ic-btn` + `.badge` | Abre dropdown de **notificaciones** (`.dd`, `.notif-item`) |
| Avatar | `.hd-profile` + `.avatar` | Abre **menú de perfil** (`.prof-dd`, `.prof-menu`) |

**Menú de perfil contiene:** datos del usuario, estado actual, configuración, cerrar sesión.

> [!important]
> **Notificaciones y Mi Perfil NO son módulos del sidebar.** Se acceden exclusivamente desde el header. El sidebar solo lista los módulos funcionales del rol.

---

## Sidebar

- Logo Oranje en la parte superior.
- Lista de módulos del rol, agrupados por secciones cuando aplica (`.sb-item`, `.sb-label`).
- El ítem activo muestra una barra de acento naranja a la izquierda.
- Contadores de pendientes (`.count`) en los módulos que los requieren.
- Al fondo (`.sb-foot`): opción de **cerrar sesión**. Opcionalmente, según el rol, puede incluir un botón de soporte (`.sb-support`) o tarjeta de usuario (`.sb-user`). La identidad del usuario vive en el header (avatar + menú de perfil), por lo que el Colaborador no repite la tarjeta de usuario en el sidebar.

---

## Navegación

Cada módulo del sidebar es una vista que se renderiza en el área `content`. No hay navegación por pestañas del navegador: la app es de una sola página (SPA) con renderizado por sección.

---

## Dispositivo por rol

| Rol / Grupo | Dispositivo |
|---|---|
| Reclutamiento (Reclutadora, Líder de Grupo) | Web — escritorio |
| Hotel (Supervisor, Manager de Área, Manager General) | Web — escritorio |
| QA | Web — escritorio |
| Ventas / otros roles de gestión | Web — escritorio |
| **Colaborador** | **Web + Móvil** |

> [!important]
> El **Colaborador** accede desde web y móvil (responsive). El ponche (RF-C-03) es una acción exclusiva de móvil: requiere GPS dentro del perímetro del hotel y la cámara del dispositivo para la foto de Entrada y Salida. Ver RR-C-04 y [[00 - Arquitectura Colaborador]].

---

## Relacionado

- [[Convenciones de Diseño]]
- [[00 - Arquitectura Colaborador]]
- [[Roles del Sistema]]
