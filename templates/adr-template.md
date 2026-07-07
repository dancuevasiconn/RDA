# ADR-0000 - Título de la decisión arquitectónica

## Estado

Proposed | Accepted | Rejected | Superseded | Deprecated | Under Review

## Fecha

AAAA-MM-DD

## Resumen ejecutivo

Descripción breve de la decisión en lenguaje claro. Debe poder entenderse sin revisar todo el documento.

## Contexto

Describir la situación que origina la decisión.

Incluir, cuando aplique:

- Situación actual.
- Problema, riesgo o tensión arquitectónica identificada.
- Restricciones conocidas.
- Dependencias relevantes.
- Consecuencias de no tomar una decisión.

## Pregunta arquitectónica

Formular la decisión como una pregunta clara.

Ejemplo:

> ¿Dónde debe residir la lógica de validación de reglas de negocio: en Core, en Integraciones, en Satélites o en una capa gobernada de Decisiones?

## Decisión

Describir la decisión tomada de forma clara y directa.

Ejemplo:

> Se decide que las reglas de negocio variables deberán residir en la capa de Gobierno / Políticas / Decisiones, evitando implementarlas como lógica embebida en Integraciones.

## Justificación

Explicar por qué esta decisión es adecuada.

Considerar:

- Alineación con principios arquitectónicos.
- Reducción, reposicionamiento o control de riesgos.
- Sustentabilidad operativa.
- Trazabilidad y auditabilidad.
- Impacto económico.
- Viabilidad técnica.

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Alternativa 1 |  |  |  | Aceptada / Rechazada |
| Alternativa 2 |  |  |  | Aceptada / Rechazada |
| Alternativa 3 |  |  |  | Aceptada / Rechazada |

## Impacto APS

| Capa APS | Impacto | Responsabilidad posicionada |
|---|---|---|
| 1. Experiencia / Cliente |  |  |
| 2. Gobierno / Políticas / Decisiones |  |  |
| 3. Integraciones / Orquestación |  |  |
| 4. Core |  |  |
| 5. Satélites |  |  |
| 6. Seguridad |  |  |
| 7. Observabilidad & Control |  |  |
| 8. Datos & Analítica |  |  |

## Análisis LEM

### Lógica

¿Qué comportamiento habilita, restringe o modifica la decisión?

### Económica

¿Qué costo, riesgo, deuda técnica, eficiencia o sustentabilidad se ve afectada?

### Logística

¿Cómo afecta el movimiento de datos, procesos, responsabilidades, integraciones, controles o dependencias?

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| RSK-0000 |  | Reducir / Reposicionar / Controlar / Aceptar / Transferir / Escalar |  |

### Riesgo antes de la decisión

¿Dónde estaba posicionado el riesgo antes de esta decisión?

### Riesgo después de la decisión

¿Dónde queda posicionado el riesgo después de esta decisión?

### Riesgo residual

¿Qué riesgo permanece aun después de aceptar esta decisión?

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Estado |
|---|---|---|---|---|
| CTRL-0000 |  |  |  | Pendiente / En curso / Implementado |

## Consecuencias y trade-offs

### Consecuencias positivas

- 

### Consecuencias negativas

- 

### Trade-offs aceptados

- 

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
|  | Técnica / Negocio / Proveedor / Datos / Seguridad / Operación |  | Abierta / En curso / Cerrada |

## Artefactos relacionados

| Artefacto | Referencia | Acción requerida |
|---|---|---|
| Diagrama APS |  |  |
| Diagrama de proceso |  |  |
| Inventario de integraciones |  |  |
| Matriz de riesgos |  |  |
| Backlog / Epic / Ticket |  |  |
| Documento de solución |  |  |

## Responsables y aprobación

| Rol | Nombre / Área | Decisión | Fecha |
|---|---|---|---|
| Arquitectura Empresarial |  | Aprobado / Rechazado / Pendiente |  |
| Arquitectura de Solución |  | Aprobado / Rechazado / Pendiente |  |
| Negocio |  | Aprobado / Rechazado / Pendiente |  |
| Tecnología |  | Aprobado / Rechazado / Pendiente |  |
| Seguridad |  | Aprobado / Rechazado / Pendiente |  |
| Datos |  | Aprobado / Rechazado / Pendiente |  |
| Operación |  | Aprobado / Rechazado / Pendiente |  |

## Revisión futura

| Fecha de revisión | Motivo | Responsable | Resultado |
|---|---|---|---|
|  |  |  |  |

## Historial de cambios

| Fecha | Cambio | Autor |
|---|---|---|
| AAAA-MM-DD | Creación del ADR |  |
