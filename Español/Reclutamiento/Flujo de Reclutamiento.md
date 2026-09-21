---
tags:
  - modulo/reclutamiento
aliases:
  - Flujo de Reclutamiento
---

# Flujo de Reclutamiento

Proceso mediante el cual [[Reclutamiento]] **capta, valida y da de alta colaboradores nuevos** para alimentar la [[Pool de Colaboradores]]. Este flujo **no** cubre requisiciones directamente: su objetivo es que la pool tenga colaboradores disponibles para que el [[Flujo de Requisición]] pueda consumirlos.

> [!info] Punto de encuentro con el [[Flujo de Requisición]]
> La [[Pool de Colaboradores]] es el único punto donde ambos flujos se conectan. El Flujo de Reclutamiento **alimenta** la pool; el [[Flujo de Requisición]] la **consume**.

## Naturaleza del flujo

El Flujo de Reclutamiento es **continuo**: [[Reclutamiento]] **siempre está contratando**, haya o no requisiciones abiertas. El objetivo es mantener la [[Pool de Colaboradores]] sana y lista para cuando el [[Flujo de Requisición]] necesite consumirla.

No depende de un disparador externo — corre de forma permanente. Las requisiciones sin match pueden **acelerar** o **priorizar** ciertas posiciones/zonas, pero no son condición para iniciar el flujo.

## Fases

### Captura de datos del colaborador (Fase 1 — Fase 3)

El alta del colaborador se captura en tres fases, definidas en [[Core/Módulos/Reglas de Negocio#Captura de datos en 3 fases|Reglas de Negocio]]:

- **Fase 1 — Entrevista inicial:** la [[Reclutadora]] captura los datos básicos del candidato y define Posición, Modalidad de contratación, Nivel de inglés y Nivel de experiencia.
- **Fase 2 — Alta en la plataforma (`/collaborator`):** el propio Colaborador completa Tipo de transporte; SSN e ITIN son opcionales (con plazo de 3 días); puede subir también, de forma opcional, el documento de SSN/ITIN y su comprobante de domicilio como documentos del expediente.
- **Fase 3 — Datos de emergencia:** el Colaborador completa contacto de emergencia, tipo de sangre y alergias o condiciones médicas desde `/collaborator`.

### Fase 4 — Validación y aprobación

[[Reclutamiento]] revisa la información capturada y aprueba o rechaza al colaborador.

### Fase 5 — Habilitación de acceso

Al colaborador aprobado se le habilita el acceso a los paneles correspondientes.

## Salida

El colaborador aprobado **ingresa a la [[Pool de Colaboradores]]** con su [[Semáforo del Colaborador]] activo. A partir de ahí queda disponible para ser asignado a un hotel a través del [[Flujo de Requisición]].

## Relacionado

- [[Pool de Colaboradores]]
- [[Flujo de Requisición]]
- [[Manager de Reclutamiento]]
- [[Líder de Grupo de Reclutadoras]]
- [[Reclutadora]]
- [[Semáforo del Colaborador]]
- [[Posiciones]]
- [[Modalidades de Contratación]]
- [[Niveles de Inglés]]
