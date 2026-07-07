# ADR-0001 — Uso del RDA como fuente de verdad para decisiones arquitectónicas

## Estado

Propuesto

## Fecha

2026-02-16

## Resumen ejecutivo

Definir una fuente única de verdad de las decisiones arquitectónicas que se manejen dentro del proyecto ADN.

## Contexto

El proyecto ADN requiere una fuente consultable y parametrizable de las decisiones de negocio que se están tomando que impacten el proyecto en su diseño y su operación.

Actualmente el proyecto no lleva un registro de las decisiones y por ello no se tiene una línea clara de los impactos o potenciales riesgos que se están generando por cada decisión.

El RDA servirá dos funciones: un repositorio para documentar, parametrizar y posteriormente relacionar las decisiones que se toman como arquitectura de soluciones, y adicionalmente servirá como caja negra para realizar diagnóstico sobre efectos de decisiones que se hayan tomado.

## Pregunta arquitectónica

> ¿Debe existir un repositorio formal, versionado y trazable para documentar decisiones arquitectónicas, riesgos relacionados y controles derivados?

## Decisión

> Se determina que el RDA deberá de existir y ser gestionado por el arquitecto de soluciones para el proyecto ADN, con la validación del arquitecto empresarial.

## Justificación

Se debe de tener una herramienta para registro de decisiones arquitectónicas. Se propone Github y el uso de herramientas Git para poder gestionar diferentes versiones de una decisión y que se evaluén de manera independiente.

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Alternativa 1 |  |  |  | Aceptada / Rechazada |
| Alternativa 2 |  |  |  | Aceptada / Rechazada |
| Alternativa 3 |  |  |  | Aceptada / Rechazada |

## Impacto APS

| Capa APS | Impacto | Responsabilidad posicionada |
|---|---|---|
| 1. Experiencia / Cliente | La interfaz GitKraken y GitBash serán usadas para interactuar con el respositorio. La versión gráfica (GitKraken) será usada por personas menos técnicas, mientras que GitBash será usada para automatización. | Servirá de interfaz con el respositorio para personas que tengan un rol requerido.   |
| 2. Gobierno / Políticas / Decisiones | La decisión que se registré deberá de existir en esta capa y será consumida/revisada/relacionada según los procesos lo requieran | Gobernar, versionar y aprobar decisiones que se estén tomando en esta capa.  |
| 3. Integraciones / Orquestación | Cuando exista la necesidad de revisar/consumir/validar una regla almacenada en el repositorio, se podrá hacer mediante integraciones| Transportat datos pertinentes de la decisión arquitectónica. |
| 4. Core | Cualquier decisión de diseño que impacte un sistema Core, referenciará un ADR. | Referenciar ADR que corresponda para decisiones de diseño  |
| 5. Satélites | Cualquier decisión de diseño que impacte un sistema satélite, referenciará un ADR | Referenciar ADR que corresponda para decisiones de Diseño |
| 6. Seguridad | Sin impacto  | NA  |
| 7. Observabilidad & Control | Cuando se implemente funcionalidades de YAML, se implementará bitacora y validación vía contrato de datos | Herramienta de control de contratos de datos y de versionamiento  |
| 8. Datos & Analítica | Sin impacto | NA  |

## Análisis

### Lógica

¿Qué comportamiento habilita, restringe o modifica la decisión?
> Registró y documentación de las decisiones que se toman por el negocio, así como la capacidad de relacionar las decisiones contra los requerimientos del negocio. 

### Económica

¿Qué costo, riesgo, deuda técnica, eficiencia o sustentabilidad se ve afectada?
> Se documenta la decisión y con ello los potenciales efectos y posibles riesgos que puede generar y/o mitigar, en corto, mediano y largo plazo, así como permite evitar le perdida de trazabilidad de las decisiones de diseño de la arquitectura.
### Logística

¿Cómo afecta el movimiento de datos, procesos, responsabilidades, integraciones, controles o dependencias?
> Permite documentar el principio rector o decisión para el diseño de la arquitectura, lo que impacta en la capacidad de transmisión de datos.

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 001-DOC-DOC | Falta de fuente única de verdad documental | Controlar |  El ADR-0001 establece el RDA como repositorio oficial, versionado y trazable para documentar decisiones arquitectónicas. Con esto, las decisiones dejan de depender de correos, minutas, chats o documentos dispersos, y quedan centralizadas en un punto de referencia común. El riesgo no desaparece por completo, pero queda controlado mediante una fuente documental explícita, con estructura, responsables y reglas de actualización. |
|002-DES-MET | Documentación generada desde la lógica del desarrollo | Controlar / Reducir | El ADR-0001 controla este riesgo al establecer que las decisiones arquitectónicas deben documentarse desde una estructura propia de arquitectura, no únicamente desde la dinámica del desarrollo o del proveedor. El RDA permite separar la decisión arquitectónica del entregable técnico, reduciendo la probabilidad de documentación incompleta, reactiva o sesgada al desarrollo. |
| 003-PRV-GOB | Dependencia del proceso del proveedor para documentar correctamente | Controlar | El ADR-0001 controla este riesgo al definir un repositorio interno donde la organización conserva la trazabilidad de sus propias decisiones arquitectónicas. Aunque el proveedor siga generando documentación técnica, las decisiones relevantes quedan registradas bajo una estructura controlada por arquitectura, reduciendo la dependencia del proceso documental del tercero. |
| 004-ARQ-DTE | Incremento de deuda técnica por falta de diseño integral formal | Reducir / Controlar | El ADR-0001 reduce este riesgo al obligar a documentar decisiones, alternativas, consecuencias y riesgos residuales antes de que queden absorbidos como cambios aislados o parches técnicos. El RDA no elimina la deuda técnica, pero permite identificar cuándo una decisión la genera, la acepta o la reduce, facilitando su seguimiento. |
| 005-GOB-TRA | Pérdida de trazabilidad entre requerimiento y solución | Controlar | El ADR-0001 controla este riesgo al vincular cada decisión arquitectónica con su contexto, necesidad, riesgos relacionados, controles y artefactos asociados. Esto permite reconstruir por qué se tomó una decisión y cómo se conecta con la solución, evitando que la relación entre requerimiento, diseño e implementación quede implícita o dispersa. |
| ID Riesgo	Riesgo	Tipo de tratamiento	Explicación
002-DES-MET	Documentación generada desde la lógica del desarrollo	Controlar / Reducir	El ADR-0001 controla este riesgo al establecer que las decisiones arquitectónicas deben documentarse desde una estructura propia de arquitectura, no únicamente desde la dinámica del desarrollo o del proveedor. El RDA permite separar la decisión arquitectónica del entregable técnico, reduciendo la probabilidad de documentación incompleta, reactiva o sesgada al desarrollo.
003-PRV-GOB	Dependencia del proceso del proveedor para documentar correctamente	Controlar	El ADR-0001 controla este riesgo al definir un repositorio interno donde la organización conserva la trazabilidad de sus propias decisiones arquitectónicas. Aunque el proveedor siga generando documentación técnica, las decisiones relevantes quedan registradas bajo una estructura controlada por arquitectura, reduciendo la dependencia del proceso documental del tercero.
004-ARQ-DTE	Incremento de deuda técnica por falta de diseño integral formal	Reducir / Controlar	El ADR-0001 reduce este riesgo al obligar a documentar decisiones, alternativas, consecuencias y riesgos residuales antes de que queden absorbidos como cambios aislados o parches técnicos. El RDA no elimina la deuda técnica, pero permite identificar cuándo una decisión la genera, la acepta o la reduce, facilitando su seguimiento.
005-GOB-TRA	Pérdida de trazabilidad entre requerimiento y solución	Controlar	El ADR-0001 controla este riesgo al vincular cada decisión arquitectónica con su contexto, necesidad, riesgos relacionados, controles y artefactos asociados. Esto permite reconstruir por qué se tomó una decisión y cómo se conecta con la solución, evitando que la relación entre requerimiento, diseño e implementación quede implícita o dispersa.
014-PRV-GOB | Dependencia futura de conocimiento tácito de personas o equipos | Reducir / Controlar | El ADR-0001 reduce este riesgo al convertir decisiones, responsables, justificaciones y consecuencias en documentación explícita y versionada. Aunque siempre puede existir conocimiento operativo no documentado, el RDA disminuye la dependencia de individuos específicos al dejar evidencia formal de las decisiones arquitectónicas clave. |
| 031-PRV-TAC | Dependencia de conocimiento tácito | Reducir / Controlar | El ADR-0001 reduce este riesgo al establecer que las reglas, criterios, decisiones y responsables relevantes deben quedar documentados en una estructura gobernada. Esto evita que la interpretación arquitectónica dependa únicamente de correos, conversaciones, archivos externos o experiencia del proveedor/equipo interno.|

### Riesgo antes de la decisión

¿Dónde estaba posicionado el riesgo antes de esta decisión?
> El riesgo quedaba dentro de las capas Core, Satélite, Seguridad, Datos y de interacción. 
### Riesgo después de la decisión

¿Dónde queda posicionado el riesgo después de esta decisión?
> Riesgo queda posicionado dentro de la capa de gobierno. 
### Riesgo residual

¿Qué riesgo permanece aun después de aceptar esta decisión?
> Persiste el riesgo de que algunos equipos tomen decisiones fuera del RDA o que existan documentos derivados no sincronizados con la fuente oficial.
## Controles requeridos

| Control ID | Control requerido                                                               | Objetivo                                                                                                                                                                                                 | Responsable                                                              | Prioridad   | Estado    |
| ---------- | ------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ----------- | --------- |
| CTRL-0001  | Mantener el ADR en Markdown como fuente oficial de la decisión                  | Evitar que existan múltiples versiones contradictorias de una decisión arquitectónica. Cuando exista una versión Word, Excel o presentación, el Markdown del ADR debe prevalecer como fuente oficial.    | Arquitectura Empresarial / Arquitectura de Solución                      | Requerida   | En curso  |
| CTRL-0002  | Registrar cada ADR en el `decision-log.xlsx`                                    | Asegurar que cada decisión pueda ser localizada, filtrada y relacionada con riesgos, controles, responsables, estado y artefactos asociados.                                                             | Arquitectura Empresarial                                                 | Requerida   | Pendiente |
| CTRL-0003  | Relacionar cada ADR con riesgos aplicables                                      | Mantener trazabilidad entre riesgos identificados y decisiones arquitectónicas, permitiendo explicar qué riesgos son controlados, reducidos, reposicionados, aceptados o introducidos por cada decisión. | Arquitectura Empresarial / Gestión de Riesgos / Arquitectura de Solución | Requerida   | Pendiente |
| CTRL-0004  | Definir estado formal para cada ADR                                             | Evitar ambigüedad sobre si una decisión está propuesta, aceptada, rechazada, en revisión, deprecada o reemplazada.                                                                                       | Arquitectura Empresarial                                                 | Requerida   | En curso  |
| CTRL-0005  | Mantener versiones Word sincronizadas con el ADR Markdown                       | Asegurar que los documentos Word distribuidos a audiencias no técnicas no contradigan la fuente oficial del repositorio.                                                                                 | Arquitectura Empresarial / Documentación / PMO                           | Prioritaria | Pendiente |
| CTRL-0006  | Revisar periódicamente ADRs abiertos o en revisión                              | Evitar que decisiones en estado `Proposed` o `Under Review` permanezcan indefinidamente sin resolución.                                                                                                  | Arquitectura Empresarial / Comité de Arquitectura                        | Prioritaria | Pendiente |
| CTRL-0007  | No documentar decisiones arquitectónicas únicamente en correos, minutas o chats | Reducir la dependencia de conocimiento tácito o disperso, obligando a que toda decisión relevante tenga una entrada formal en el RDA.                                                                    | Todos los equipos participantes / Arquitectura Empresarial               | Requerida   | En curso  |
| CTRL-0008  | Usar convención única de nombres e IDs para ADRs                                | Evitar duplicidad, pérdida de trazabilidad o reutilización de identificadores en decisiones arquitectónicas.                                                                                             | Arquitectura Empresarial                                                 | Requerida   | En curso  |
| CTRL-0009  | Registrar responsables y áreas involucradas en cada ADR                         | Reducir ambigüedad sobre ownership de la decisión y facilitar seguimiento, aprobación o revisión futura.                                                                                                 | Arquitectura Empresarial / Arquitectura de Solución / Negocio            | Prioritaria | Pendiente |
| CTRL-0010  | Actualizar artefactos relacionados cuando un ADR sea aceptado                   | Asegurar que diagramas, matrices, inventarios, documentos de solución, riesgos y controles reflejen la decisión tomada.                                                                                  | Arquitectura de Solución / Responsables de artefactos                    | Prioritaria | Pendiente |


## Consecuencias y trade-offs

### Consecuencias positivas

- Registro documentado de decisiones.
- Idexamiento de decisiones.

### Consecuencias negativas

- Creación de un pipeline documental que requiere atención y trabajo manual.

### Trade-offs aceptados

- Tiempo invertido en mantener la documentación al día, contra costos de recursos económicos y de tiempo presentes y futuros invertidos en documentar y revisar las decisiones de diseño.

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Sin dependencia existente | Negocio | NA | Abierta |

## Artefactos relacionados

| Artefacto | Referencia | Acción requerida |
|---|---|---|
| Diagrama APS | NA | NA  |
| Diagrama de proceso | Diagrama de proceso de creación de nuevos ADR | Diagramado |
| Inventario de integraciones | NA | NA |
| Matriz de riesgos | Referencia los riesgos relacionados con este ADR | Referencia interna validada  |
| Backlog / Epic / Ticket | NA | NA |
| Documento de solución | Documento ADR en word | Crear versión word  |

## Responsables y aprobación

| Rol | Nombre / Área | Decisión | Fecha |
|---|---|---|---|
| Arquitectura Empresarial | Roberto Mancha | Pendiente |  |
| Arquitectura de Solución | Daniel Cuevas |  Aprobado | 2026-07-07  |
| Negocio |  |  Pendiente |  |
| Tecnología |  | Pendiente |  |
| Seguridad |  | Pendiente |  |
| Datos |  | Pendiente |  |
| Operación |  |  Pendiente |  |

## Revisión futura

| Fecha de revisión | Motivo | Responsable | Resultado |
|---|---|---|---|
|  |  |  |  |

## Historial de cambios

| Fecha | Cambio | Autor |
|---|---|---|
| 2026-07-07 | Creación del ADR |  |
