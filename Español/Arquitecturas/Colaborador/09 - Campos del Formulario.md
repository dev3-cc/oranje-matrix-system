---
tags:
  - arquitectura
  - rol/colaborador
aliases:
  - Campos Formulario Colaborador
---

# 3. CAMPOS DEL FORMULARIO — COLABORADOR

---

## A) Formulario de Alta — Fase 2 (RF-C-01)

Completado por el propio Colaborador desde `/empleado`. Fuente: [[Colaborador#Fase 2 — Alta en la plataforma (/empleado)]]

| Campo                  | Tipo de Input | Obligatorio | Validación                                                                          | Descripción                                     |
| ---------------------- | ------------- | ----------- | ----------------------------------------------------------------------------------- | ----------------------------------------------- |
| SSN                    | Text          | No (opcional) | Formato XXX-XX-XXXX · Enmascarar tras ingreso · Cifrado en almacenamiento           | Número de Seguro Social                         |
| ITIN                   | Text          | No (opcional) | Formato 9XX-XX-XXXX · Enmascarar tras ingreso · Cifrado en almacenamiento           | Individual Taxpayer Identification Number       |
| Tipo de transporte     | Select        | SÍ          | Opciones: Propio / Transporte público / Otro                                        | Cómo se traslada al hotel                       |
| Documento de SSN/ITIN  | File (JPG/PNG/PDF) | No (opcional · recomendado si hay SSN/ITIN) | Máx. 10 MB · Se habilita al ingresar SSN/ITIN · Almacenamiento seguro (RNF-C-03)   | Foto/escaneo de la tarjeta de SSN o documento ITIN |

> [!note]
> SSN e ITIN son opcionales. Si el colaborador no proporciona ninguno (ni SSN ni ITIN/TaxID), se activa automáticamente la **retención del 16%** sobre su pago (reembolsable) — ver [[Deducciones]]. Si proporciona ITIN, no se aplica la retención (ITIN es un TaxID). Tiene **3 días desde su alta** para cargarlo — ver [[Reglas del Colaborador#Plazo de SSN/ITIN]].
>
> **Posición, Nivel de inglés, Nivel de experiencia y Modalidad se movieron a la Fase 1** *(cambiado el 2026-08-22)*: los define la [[Reclutadora]] en la entrevista, no el colaborador en este formulario.

---

## B) Formulario de Datos de Emergencia — Fase 3 (RF-C-02)

Completado por el propio Colaborador. Fuente: [[Colaborador#Fase 3 — Datos de emergencia]]

| Campo                              | Tipo de Input | Obligatorio | Validación                              | Descripción                                                    |
| ---------------------------------- | ------------- | ----------- | --------------------------------------- | -------------------------------------------------------------- |
| Contacto de emergencia — nombre    | Text          | SÍ          | Mín. 3 caracteres                       | Nombre completo de la persona a contactar en caso de emergencia |
| Contacto de emergencia — teléfono  | Tel           | SÍ          | Formato de teléfono válido              | Número de teléfono del contacto                                |
| Contacto de emergencia — parentesco | Select       | SÍ          | Opciones: Madre / Padre / Cónyuge / Hermano/a / Hijo/a / Amigo/a / Otro | Relación con el colaborador           |
| Tipo de sangre                     | Select        | SÍ          | Opciones: A+, A–, B+, B–, AB+, AB–, O+, O–, No sé | Grupo sanguíneo                                   |
| Alergias o condiciones médicas     | Textarea      | NO          | Máx. 500 caracteres                     | Información médica relevante para emergencias                  |

---

## C) Formulario de Reporte de Accidente (RF-C-05)

Escenario A: el Colaborador reporta primero. Fuente: [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]]

| Campo                        | Tipo de Input | Obligatorio | Validación                              | Descripción                                                    |
| ---------------------------- | ------------- | ----------- | --------------------------------------- | -------------------------------------------------------------- |
| Fecha del accidente          | Date          | SÍ          | Fecha actual o pasada (no futura)       | Cuándo ocurrió                                                 |
| Hora del accidente           | Time          | SÍ          | Formato HH:MM (24h)                     | A qué hora ocurrió                                             |
| Descripción del accidente    | Textarea      | SÍ          | Mín. 50 caracteres                      | Descripción de cómo ocurrió el accidente desde su perspectiva  |
| Evidencia (fotos)            | File          | NO          | JPG/PNG, máx. 10 MB                     | Fotos del incidente o de la lesión                             |
| Geolocalización (mobile)     | Auto          | NO          | Auto desde el dispositivo               | Ubicación al momento de abrir el reporte (si el colaborador permite acceso) |

> [!info]
> La información presencial (ubicación exacta, circunstancias detalladas, testigos, atención inmediata) es capturada por el [[Hotel/Supervisor|Supervisor]] que acude físicamente. El Colaborador solo genera la señal inicial y describe brevemente el incidente. Ver [[Core/Módulos/Accidente Laboral/Accidente Laboral|Accidente Laboral]] para la estructura completa de la tarjeta.

---

## D) Toggle de Disponibilidad — Activar Amarillo (RF-C-04)

No es un formulario extenso; es un control de activación con confirmación.

| Campo                | Tipo de Input | Obligatorio | Validación                                              | Descripción                                               |
| -------------------- | ------------- | ----------- | ------------------------------------------------------- | --------------------------------------------------------- |
| Toggle disponibilidad | Toggle (On/Off) | SÍ (acción explícita) | Solo disponible si el colaborador está en Verde fuerte, Naranja o Rosa | Activa el estado Amarillo en el [[Semáforo del Colaborador]] |
| Confirmación         | Modal         | SÍ          | Botón "Confirmar disponibilidad" requerido              | El sistema solicita confirmación antes de cambiar el estado |

> [!important]
> Activar Amarillo es la **única acción autónoma de cambio de estado** del Colaborador (RR-C-02). No requiere aprobación de ningún otro rol. El sistema ejecuta el cambio inmediatamente al confirmar.

---

## E) Filtros y vistas de solo lectura

### Mi Schedule (RF-C-06)

| Campo       | Tipo de Input | Descripción                                    |
| ----------- | ------------- | ---------------------------------------------- |
| Semana      | Selector      | Navegar entre semana en curso y siguiente      |
| Vista       | Toggle        | Vista lista / Vista calendario                 |

### Mi Timesheet (RF-C-07)

| Campo       | Tipo de Input | Descripción                                    |
| ----------- | ------------- | ---------------------------------------------- |
| Semana      | Selector      | Semana en curso y semanas anteriores           |

### Mi Pago (RF-C-08)

Vista de solo lectura. Muestra el historial de pagos ya liberados; la semana en curso aparece como "En cálculo" sin monto (RR-C-05).

| Columna        | Tipo          | Descripción                                                          |
| -------------- | ------------- | -------------------------------------------------------------------- |
| Semana         | Texto         | Etiqueta de la semana (ej. "Semana 24 · 9–15 jun")                  |
| Hotel(es)      | Texto         | Hotel(es) en los que trabajó esa semana                              |
| Horas          | Numérico      | Horas netas trabajadas en la semana                                  |
| Monto pagado   | Moneda        | Monto recibido (visible solo si el pago fue liberado)                |
| Fecha de pago  | Fecha         | Fecha en que Contabilidad liberó el pago                             |
| Estado         | Etiqueta      | "Pagado" para pagos liberados; "En cálculo" para la semana en curso  |
