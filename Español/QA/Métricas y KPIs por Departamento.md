---
tags:
  - departamento/qa
aliases:
  - Métricas y KPIs por Departamento
  - KPIs de QA
  - Métricas de QA
---

# Métricas y KPIs por Departamento

Definición de las métricas concretas que cada [[Operador de QA]] monitorea en su departamento asignado. El [[Manager de QA]] es responsable de mantener actualizadas estas definiciones y ajustar los umbrales tras periodos de calibración.

> [!info]
> Todas las métricas se derivan de datos ya rastreados en el sistema: semáforos, timesheets, requisiciones, journals y catálogos. QA no genera datos nuevos; observa los existentes.

## Regla general de umbrales

Cada KPI tiene tres niveles de evaluación:

| Nivel         | Significado                                                                 |
| ------------- | --------------------------------------------------------------------------- |
| **Meta**      | Desempeño esperado; el departamento opera dentro de parámetros              |
| **En riesgo** | Desempeño por debajo de lo esperado; requiere atención y observación formal |
| **Crítico**   | Desempeño inaceptable; requiere acción correctiva inmediata                 |

Estos niveles alimentan el [[Indicador de Calidad]] existente:

- Si **cualquier KPI** alcanza nivel Crítico → el [[Operador de QA]] debe proponer que el [[Indicador de Calidad]] del departamento pase al menos a **Amarillo**.
- Si **2 o más KPIs** están en nivel Crítico, o la situación persiste sin mejora → proponer escalar el Indicador a **Rojo**.
- El [[Manager de QA]] valida y aprueba toda actualización del [[Indicador de Calidad]].

> [!note]
> Los umbrales documentados aquí son valores iniciales. El [[Manager de QA]] los ajusta tras un periodo de calibración basado en datos reales de operación.

## Inspección

Operador asignado: Operador 1. Departamento supervisado: [[Inspección/Inspección|Inspección]].

| # | KPI | Fórmula / cálculo | Fuente de datos | Meta | En riesgo | Crítico |
|---|-----|-------------------|-----------------|------|-----------|---------|
| 1 | **Tasa de verificación Día 1** | (Verificaciones realizadas en Día 1 / Total de colaboradores asignados en el periodo) × 100 | Transición Verde fuerte → Verde manzana en [[Semáforo del Colaborador]] | ≥ 95% | 85–94% | < 85% |
| 2 | **Tasa de entrega de uniforme Día 3** | (Uniformes entregados en Día 3 / Total de colaboradores que alcanzaron Día 3) × 100 | Transición Verde manzana → Azul claro en [[Semáforo del Colaborador]] | ≥ 95% | 85–94% | < 85% |
| 3 | **Tiempo promedio de resolución de reportes** | Promedio de días desde que el colaborador entra en Rojo hasta que el [[Inspector]] resuelve (a Negro o Verde fuerte) | Journal del [[Semáforo del Colaborador]] | ≤ 3 días | 4–5 días | > 5 días |
| 4 | **Tiempo promedio de cierre de accidente** | Promedio de días desde estado Gris hasta cierre de tarjeta (Gris → Verde fuerte) | Tarjetas de [[Core/Módulos/Accidente Laboral/Accidente Laboral\|Accidente Laboral]] | ≤ 7 días | 8–14 días | > 14 días |
| 5 | **Cobertura de zonas** | Zonas con [[Inspector]] asignado y activo / Total de [[Core/Catálogos/Zonas\|zonas]] (6) | Asignación de zonas por el [[Inspección/Coordinador\|Coordinador]] | 6/6 (100%) | 5/6 (83%) | ≤ 4/6 (67%) |

**Por qué estos KPIs:**
- KPIs 1–2 miden las obligaciones operativas centrales del Inspector (Día 1 y Día 3).
- KPI 3 mide la capacidad de respuesta ante disputas — el rol investigativo del Inspector.
- KPI 4 mide el ciclo de vida de accidentes laborales — responsabilidad final del Inspector.
- KPI 5 mide la cobertura estructural — sin Inspector en zona, las demás métricas se degradan.

## Hotel

Operador asignado: Operador 2. Departamento supervisado: [[Hotel/Hotel|Hotel]].

| # | KPI | Fórmula / cálculo | Fuente de datos | Meta | En riesgo | Crítico |
|---|-----|-------------------|-----------------|------|-----------|---------|
| 1 | **Autorización oportuna de requisiciones** | (Requisiciones autorizadas en ≤ 24h desde creación / Total de requisiciones creadas) × 100 | Timestamps del [[Core/Módulos/Semáforos/Semáforo de Requisición\|Semáforo de Requisición]] | ≥ 90% | 75–89% | < 75% |
| 2 | **Tasa de rechazo de requisiciones** | (Requisiciones rechazadas / Total de requisiciones enviadas a autorización) × 100 | Journal de [[Core/Módulos/Requisicion/Requisición\|Requisición]] | ≤ 10% | 11–25% | > 25% |
| 3 | **Cumplimiento de ponchado** | (Timesheets con las 2 marcas obligatorias completas / Total de timesheets del periodo) × 100 | [[Timesheet]] | ≥ 90% | 75–89% | < 75% |
| 4 | **Aprobación oportuna de ponches** | (Ponches aprobados antes del cierre semanal / Total de ponches del periodo) × 100 — aprueban [[Hotel/Manager de Área\|Manager de Área]] y [[Hotel/Manager General\|Manager General]] | [[Timesheet]] | ≥ 95% | 85–94% | < 85% |
| 5 | **Tasa de Stand-by prolongado** | (Colaboradores en Rosa por > 30 días / Total de colaboradores en Rosa) × 100 | [[Semáforo del Colaborador]], timestamps de estado Rosa | ≤ 10% | 11–25% | > 25% |

**Por qué estos KPIs:**
- KPI 1 evita que el hotel sea un cuello de botella en el flujo de requisiciones.
- KPI 2 es un indicador inverso de calidad — tasas altas de rechazo sugieren mala preparación por parte del [[Hotel/Supervisor|Supervisor]].
- KPI 3 mide la disciplina operativa del hotel en el registro de asistencia.
- KPI 4 vigila el cuello de botella del cierre semanal — sin aprobación, los ponches no llegan al Consolidado y la nómina no se puede calcular.
- KPI 5 detecta colaboradores olvidados o abandonados en estado de espera.

## Colaborador

Operador asignado: Operador 3. Ámbito supervisado: [[Colaborador/Colaborador|Colaborador]].

> [!note]
> Colaborador no es un departamento tradicional. El Operador de QA asignado monitorea métricas agregadas del pool y el ciclo de vida de la fuerza laboral, no el desempeño de un equipo interno.

| #   | KPI                         | Fórmula / cálculo                                                                                              | Fuente de datos                                                           | Meta  | En riesgo | Crítico |
| --- | --------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ----- | --------- | ------- |
| 1   | **Tasa de inasistencia**    | (Total de transiciones a Morado en el periodo / Total de jornadas programadas) × 100                           | [[Semáforo del Colaborador]], [[Core/Módulos/Schedule\|Schedule]]         | ≤ 5%  | 6–10%     | > 10%   |
| 2   | **Tasa de Blacklist**       | (Colaboradores que pasaron a Negro en el periodo / Total de colaboradores activos al inicio del periodo) × 100 | [[Semáforo del Colaborador]]                                              | ≤ 2%  | 3–5%      | > 5%    |
| 3   | **Salud del Pool**          | (Colaboradores en Verde fuerte + Amarillo / Total de colaboradores en el [[Pool de Colaboradores]]) × 100      | [[Pool de Colaboradores]], [[Semáforo del Colaborador]]                   | ≥ 60% | 40–59%    | < 40%   |
| 4   | **Tasa de Lunch Extendido** | (Jornadas con lunch > 30 min / Total de jornadas del periodo) × 100                                            | Indicador de Lunch Extendido en [[Timesheet]]                             | ≤ 10% | 11–20%    | > 20%   |
| 5   | **Completitud de datos**    | (Colaboradores con 3 fases de captura completas / Total de colaboradores activos) × 100                        | Datos de captura del [[Colaborador/Colaborador\|Colaborador]] (Fases 1–3) | ≥ 95% | 85–94%    | < 85%   |

**Por qué estos KPIs:**
- KPI 1 es la métrica fundamental de confiabilidad de la fuerza laboral.
- KPI 2 rastrea el desenlace más severo; una tasa creciente señala problemas sistémicos.
- KPI 3 mide si hay suficientes colaboradores desplegables para cubrir demanda.
- KPI 4 aprovecha el Indicador de Lunch Extendido ya existente en el sistema.
- KPI 5 asegura integridad de datos — perfiles incompletos generan brechas operativas.

## Ventas

Operador asignado: Operador 4. Departamento supervisado: [[Ventas/Ventas|Ventas]].

| # | KPI | Fórmula / cálculo | Fuente de datos | Meta | En riesgo | Crítico |
|---|-----|-------------------|-----------------|------|-----------|---------|
| 1 | **Tasa de conversión** | (Hoteles que alcanzaron Naranja / Total de hoteles prospectados en el periodo) × 100 | [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | ≥ 25% | 15–24% | < 15% |
| 2 | **Ciclo promedio de onboarding** | Promedio de días desde Gris hasta Naranja para hoteles convertidos | Timestamps del [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | ≤ 45 días | 46–75 días | > 75 días |
| 3 | **Tasa de estancamiento** | (Hoteles en Café / Total de hoteles activos en pipeline) × 100 | [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | ≤ 10% | 11–20% | > 20% |
| 4 | **Tasa de pérdida** | (Hoteles en Rojo o Negro sin reactivación / Total de hoteles gestionados en el periodo) × 100 | [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | ≤ 20% | 21–35% | > 35% |
| 5 | **Tasa de reactivación exitosa** | (Hoteles reactivados desde Rojo/Negro/Café que avanzaron al menos a Verde / Total de hoteles reactivados) × 100 | Journal del [[Core/Módulos/Semáforos/Semáforo Onboarding\|Semáforo Onboarding]] | ≥ 40% | 25–39% | < 25% |

**Por qué estos KPIs:**
- KPI 1 es la métrica principal de efectividad del equipo comercial.
- KPI 2 asegura que los deals no se arrastren indefinidamente.
- KPI 3 detecta bloqueos en el pipeline que el [[Ventas/Roles/Business Developer Coordinator|BDC]] debe resolver.
- KPI 4 rastrea pérdidas — una tasa creciente puede indicar desalineación de mercado o mala selección de prospectos.
- KPI 5 mide la capacidad del equipo para recuperar oportunidades estancadas.

## Reclutamiento

Operador asignado: Operador 5. Departamento supervisado: [[Reclutamiento/Reclutamiento|Reclutamiento]].

| #   | KPI                                        | Fórmula / cálculo                                                                                                                                   | Fuente de datos                                                                                         | Meta  | En riesgo | Crítico |
| --- | ------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- | ----- | --------- | ------- |
| 1   | **Cobertura total de requisiciones**       | (Requisiciones que terminaron en Azul claro / Total de requisiciones cerradas en el periodo) × 100                                                  | [[Core/Módulos/Semáforos/Semáforo de Requisición\|Semáforo de Requisición]]                             | ≥ 85% | 70–84%    | < 70%   |
| 2   | **Tiempo promedio de toma de requisición** | Promedio de horas desde autorización hasta toma por [[Reclutadora]]                                                                                 | Timestamps del [[Core/Módulos/Semáforos/Semáforo de Requisición\|Semáforo de Requisición]]              | ≤ 8h  | 9–24h     | > 24h   |
| 3   | **Tasa de auto-asignación por timeout**    | (Requisiciones auto-asignadas a las 24h / Total de requisiciones autorizadas) × 100                                                                 | Sistema de auto-asignación                                                                              | ≤ 5%  | 6–15%     | > 15%   |
| 4   | **Tasa de escalación por timeout**         | (Requisiciones escaladas al [[Reclutamiento/Líder de Grupo de Reclutadoras\|Líder de Grupo]] por timeout / Total de requisiciones en proceso) × 100 | Escalaciones según [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición\|Semáforo de Urgencia]] | ≤ 10% | 11–20%    | > 20%   |
| 5   | **Cumplimiento de consulta de Blacklist**  | (Reclutamientos con consulta de [[Core/Módulos/Blacklist\|Blacklist]] registrada / Total de reclutamientos del periodo) × 100                       | Log de consultas de [[Core/Módulos/Blacklist\|Blacklist]]                                               | 100%  | 95–99%    | < 95%   |
| 6   | **Tasa de ingreso al Pool**                | (Colaboradores aprobados e ingresados al [[Pool de Colaboradores]] / Total de candidatos entrevistados) × 100                                       | [[Core/Módulos/Pool de Colaboradores\|Pool de Colaboradores]], datos de entrevistas                     | ≥ 60% | 45–59%    | < 45%   |

**Por qué estos KPIs:**
- KPI 1 es el entregable central del departamento — cubrir requisiciones completamente.
- KPIs 2–3 miden la proactividad en la toma de requisiciones del modelo Self-Pick.
- KPI 4 detecta casos donde la [[Reclutadora]] no pudo encontrar match dentro de la ventana de urgencia.
- KPI 5 es una métrica de cumplimiento — la consulta de Blacklist es una obligación documentada.
- KPI 6 mide la salud del embudo de reclutamiento — valores muy bajos indican mala selección de fuentes o filtrado excesivo.

## Customer Service

Operador asignado: Operador 6. Departamento supervisado: [[Customer Service/Customer Service|Customer Service]].

> [!note]
> Los KPIs específicos de Customer Service se definirán tras un periodo inicial de operación. El [[Manager de QA]] establecerá los umbrales una vez que existan datos reales de volumen de casos, tiempos de resolución y satisfacción del hotel.

## Relacionado

- [[QA/QA|QA]]
- [[Manager de QA]]
- [[Operador de QA]]
- [[QA/Dashboard de QA|Dashboard de QA]]
- [[Core/Módulos/Semáforos/Indicador de Calidad|Indicador de Calidad]]
- [[Semáforo del Colaborador]]
- [[Core/Módulos/Semáforos/Semáforo de Requisición|Semáforo de Requisición]]
- [[Core/Módulos/Semáforos/Semáforo Onboarding|Semáforo Onboarding]]
- [[Core/Módulos/Semáforos/Semáforo de Urgencia de Requisición|Semáforo de Urgencia de Requisición]]
- [[Core/Módulos/Semáforos/Semáforo de Posiciones de la Requisición|Semáforo de Posiciones de la Requisición]]
- [[Reglas de Negocio]]
