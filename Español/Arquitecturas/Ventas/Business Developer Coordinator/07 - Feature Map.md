---
tags:
  - arquitectura
  - rol/bdc
aliases:
  - Feature Map BDC
---

# FEATURE MAP — BUSINESS DEVELOPER COORDINATOR

---

```
DEPARTAMENTO DE VENTAS — BUSINESS DEVELOPER COORDINATOR
│
├── PRIMARIAS
│   │
│   ├── 📊 Dashboard
│   │   ├── Embudo de conversión
│   │   ├── Heatmap por zona
│   │   ├── Ranking de BDs
│   │   ├── Casos críticos (T&C pendientes, Café, alertas calidad)
│   │   └── Acciones rápidas
│   │
│   ├── 📋 Pipeline (vista global del territorio)
│   │   ├── Ver Pipeline global de todos los BDs
│   │   ├── Filtros por BD / ruta / zona / status / días en status
│   │   ├── Comentar al expediente del prospecto
│   │   ├── Reasignar prospecto a otro BD
│   │   ├── Bandeja Café (sub-vista dedicada)
│   │   ├── Desbloquear estancamiento (Café)
│   │   ├── Reactivar desde Café (a Azul Claro)
│   │   └── Marcar Café (compartido con BD)
│   │
│   ├── 📄 Contratos
│   │   ├── Validar T&C (sí final)
│   │   ├── Rechazar T&C con observaciones
│   │   ├── Crear Documento de T&C (apoyo al BD)
│   │   ├── Comentar al BD sobre el T&C
│   │   └── Ver pendientes / validados / rechazados / histórico
│   │
│   ├── ✅ Conversión
│   │   ├── Ver listos para conversión (Rosa + T&C validado)
│   │   ├── Crear Usuario del Hotel (precondición)
│   │   ├── Aprobar conversión (dispara Trigger Automático)
│   │   └── Ver convertidos (últimos 30 días)
│   │
│   ├── 👥 Mi Equipo
│   │   ├── Ver BDs a cargo con métricas
│   │   ├── Ver métricas individuales por BD
│   │   ├── Comunicar con BD (chat / nota)
│   │   └── Solicitar reporte específico a un BD
│   │
│   └── 🏨 Clientes Activos
│       ├── Ver clientes activos (referente comercial)
│       ├── Marcar cliente Negro
│       ├── Reactivar desde Negro (a Azul Claro)
│       └── Ver sub-vista Negro (pausados / inactivos)
│
└── SECUNDARIAS
    │
    ├── 📈 Análisis
    │   ├── Métricas del territorio (cobertura, conversión, tiempos)
    │   ├── Embudo y heatmap (vista ejecutiva)
    │   ├── Ranking de desempeño de BDs
    │   └── Comparativa contra periodo anterior
    │
    ├── 📊 Reportes
    │   ├── Generar reporte (Pipeline / Conversión / Desempeño / Café / Negro / Calidad / Ejecutivo)
    │   ├── Enviar a dirección
    │   ├── Programar envío recurrente
    │   └── Ver histórico de envíos
    │
    ├── 🛠️ Utilidades
    │   ├── Exportar reportes (CSV / PDF / Excel)
    │   ├── Exportar Pipeline (Excel)
    │   ├── Notificaciones (recibir)
    │   └── Soporte
    │
    └── ⚫ Blacklist
        └── Consultar Blacklist (solo lectura)
```
