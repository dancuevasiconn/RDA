# RDA - Repositorio de Decisiones Arquitectónicas

## Propósito

Este repositorio tiene como objetivo documentar, centralizar y dar trazabilidad a las decisiones arquitectónicas relevantes del proyecto.

Una decisión arquitectónica es cualquier definición que impacta la estructura, comportamiento, operación, gobierno, evolución, sustentabilidad o riesgo de una solución.

El RDA busca evitar que las decisiones arquitectónicas queden dispersas en correos, minutas, chats, presentaciones o acuerdos informales. Cada decisión relevante debe quedar registrada con su contexto, justificación, alternativas evaluadas, riesgos relacionados, consecuencias y responsables.

---

## Principio base

El ADR es la fuente de verdad de cada decisión arquitectónica.

El Excel maestro es la fuente de navegación, trazabilidad y seguimiento.

Los riesgos, controles, revisiones y artefactos relacionados complementan la decisión, pero no la sustituyen.

---

## Objetivos del repositorio

Este repositorio permite:

- Documentar decisiones arquitectónicas de forma estructurada.
- Explicar por qué se tomó una decisión y qué alternativas fueron evaluadas.
- Relacionar decisiones con riesgos arquitectónicos, operativos, técnicos o de negocio.
- Identificar riesgos tratados, reposicionados, aceptados o residuales.
- Dar seguimiento a controles derivados de decisiones arquitectónicas.
- Mantener evidencia histórica de decisiones aceptadas, reemplazadas o descartadas.
- Facilitar revisiones arquitectónicas periódicas.
- Alinear decisiones con procesos, aplicaciones, integraciones, datos, seguridad, observabilidad y gobierno.
- Conectar las decisiones con los modelos APS y LEM.

---

## Relación con APS

Las decisiones documentadas en este repositorio deben indicar, cuando aplique, qué capas del modelo APS son impactadas.

Capas APS de referencia:

1. Experiencia / Cliente
2. Gobierno / Políticas / Decisiones
3. Integraciones / Orquestación
4. Core
5. Satélites
6. Seguridad
7. Observabilidad & Control
8. Datos & Analítica

El objetivo es identificar dónde debe posicionarse una responsabilidad, capacidad, riesgo o control dentro de la arquitectura.

---

## Gestión de riesgos arquitectónicos

La gestión de riesgos en este repositorio no se entiende como la eliminación total del riesgo.

Un riesgo es probabilístico. Si la situación ya ocurrió o es determinística, deja de ser un riesgo y debe tratarse como problema, incidente, defecto, deuda técnica materializada o restricción.

Las decisiones arquitectónicas buscan posicionar los riesgos en entornos donde puedan ser:

- Observados.
- Gobernados.
- Controlados.
- Reducidos.
- Reposicionados.
- Transferidos.
- Aceptados de manera explícita.

Por ello, un ADR puede relacionarse con uno o varios riesgos, y un riesgo puede requerir una o varias decisiones arquitectónicas.

---

## Estructura del repositorio

```text
RDA/
│
├── README.md
├── .gitignore
│
├── decisions/
│   └── Registros individuales de decisiones arquitectónicas.
│
├── templates/
│   └── Plantillas para ADRs, riesgos, controles y revisiones.
│
├── registers/
│   └── Archivos maestros de trazabilidad, incluyendo el decision log.
│
├── risks/
│   └── Documentación complementaria de riesgos arquitectónicos.
│
├── controls/
│   └── Controles derivados de decisiones o riesgos.
│
├── reviews/
│   └── Revisiones periódicas del repositorio y sus decisiones.
│
├── assets/
│   ├── diagrams/
│   │   └── Diagramas de apoyo.
│   └── references/
│       └── Documentos o referencias complementarias.
│
└── archive/
    ├── superseded/
    │   └── Decisiones reemplazadas por decisiones posteriores.
    └── deprecated/
        └── Decisiones que ya no se recomiendan, pero se conservan como evidencia histórica.

