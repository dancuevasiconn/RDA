# ADR-0002 - Ubicación y gobierno de lógica de negocio variable

## Estado

Proposed

## Fecha

2026-02-16

## Resumen ejecutivo

Las reglas de negocio y la lógica de negocio en si debe de existir por fuera de las capas arquitectónicas relacionadas con la ejecución. La intención de separar la lógica de negocio y las decisiones en sí, es que de esa manera se encapsula y aisla de la operación, haciendo su administración, mas simple, eficiente y limitando el impacto en caso de fallas, a meramente una regla de negocio, y permitiendo que la correción sea mas sencilla. para conocer mas acerca de los impactos y riesgos, revisar la sección de riesgos relacionados.

## Contexto

Durante el análisis de riesgos se identificó que la lógica de negocio puede quedar fragmentada en distintos componentes de la arquitectura: integraciones, APIs, scripts, satélites, procesos manuales, configuraciones técnicas o componentes del Core.

Esta situación puede generar inconsistencias funcionales, pérdida de trazabilidad, deuda técnica, dependencia de conocimiento tácito y dificultad para evolucionar la solución To-Be.

La lógica de negocio variable incluye reglas, validaciones, excepciones, transformaciones con significado de negocio, criterios de aprobación, condiciones operativas y decisiones que pueden cambiar por dominio, proceso, unidad de negocio, proveedor, política, vigencia o condición operacional.

El problema arquitectónico no es únicamente técnico. La pregunta central es dónde debe vivir la responsabilidad de decidir, quién debe gobernarla y cómo debe ser consumida por el resto de la arquitectura.

## Pregunta arquitectónica

Formular la decisión como una pregunta clara.

> ¿Dónde deben residir las reglas de negocio variables y quién debe gobernarlas dentro de la arquitectura To-Be?

## Decisión

Se decide que la lógica de negocio variable no debe quedar embebida de forma dispersa en integraciones, scripts, APIs, satélites, procesos manuales o componentes técnicos sin gobierno explícito.

La lógica de negocio variable debe posicionarse en una capa gobernada de decisiones, o en un mecanismo mínimo equivalente, que permita ownership, trazabilidad, versionamiento, criterios de cambio, auditoría y consumo controlado por los componentes que la requieran.

La capa de Integraciones / Orquestación puede transportar datos, aplicar transformaciones técnicas, enrutar eventos y consumir el resultado de una decisión gobernada, pero no debe convertirse en dueña informal de la lógica de negocio.

El Core y los satélites pueden ejecutar capacidades funcionales, pero la lógica variable que represente política, excepción o decisión de negocio debe identificarse, documentarse y gobernarse de forma explícita.


## Justificación

La decisión busca evitar que la lógica de negocio quede distribuida en múltiples puntos de la arquitectura sin una estructura clara de gobierno.

Cuando las reglas se implementan donde resulta más práctico en el momento, la solución puede funcionar inicialmente, pero se incrementa el costo futuro de mantenimiento, pruebas, diagnóstico y evolución.

Esta decisión permite separar responsabilidades:

- Gobierno / Políticas / Decisiones define y administra reglas variables.
- Integraciones / Orquestación transporta, transforma técnicamente y consume decisiones.
- Core ejecuta transacciones o capacidades centrales.
- Satélites ejecutan capacidades especializadas.
- Datos & Analítica conserva trazabilidad, linaje y consistencia semántica.
- Observabilidad & Control permite auditar qué regla se aplicó, cuándo, por qué y con qué resultado.

La decisión no elimina todos los riesgos asociados a reglas de negocio, pero los reposiciona en un entorno donde pueden ser gobernados, observados, controlados y reducidos.


## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Mantener reglas en cada componente | Cada aplicación, integración o script implementa la regla que necesita. | Puede ser rápido para casos puntuales. | Fragmenta la lógica, dificulta trazabilidad, genera deuda técnica y aumenta inconsistencias. | Rechazada |
| Implementar reglas en integraciones | Las reglas se evalúan durante los flujos de integración. | Facilita acceso al dato en movimiento y puede acelerar implementación. | Mezcla transporte con decisión, convierte integraciones en dueñas de lógica de negocio y rompe autonomía de capas. | Rechazada |
| Implementar reglas directamente en el Core | Las reglas se incorporan en el sistema central. | Centraliza parte de la ejecución. | Puede rigidizar cambios, requerir desarrollo/despliegue y no cubrir reglas externas o transversales. | Parcialmente aceptada solo para reglas nativas del Core |
| Implementar reglas en satélites | Cada satélite administra la lógica de su dominio funcional. | Puede ser útil para capacidades especializadas. | Puede duplicar reglas, generar interpretaciones distintas y dificultar gobierno transversal. | Parcialmente aceptada solo para lógica propia del satélite |
| Usar capa gobernada de decisiones o mecanismo equivalente | Las reglas variables se documentan, versionan, gobiernan y consumen desde un punto controlado. | Mejora trazabilidad, control, reutilización y claridad de ownership. | Requiere gobierno, disciplina de mantenimiento, controles y observabilidad. | Aceptada |


## Impacto APS

| Capa APS | Impacto |
|---|---|
| Experiencia / Cliente | Puede verse afectada si las reglas impactan disponibilidad, precios, promociones, aprobaciones, excepciones o comportamiento visible al usuario. |
| Gobierno / Políticas / Decisiones | Se posiciona como la capa responsable de administrar reglas variables, políticas, criterios y excepciones. |
| Integraciones / Orquestación | Debe evitar convertirse en dueña informal de reglas de negocio. Puede consumir decisiones gobernadas, pero no definirlas localmente sin control. |
| Core | Debe ejecutar capacidades centrales, pero no absorber reglas variables que deban cambiar por política, excepción o dominio sin gobierno explícito. |
| Satélites | Pueden ejecutar lógica especializada, pero deben documentar y gobernar cualquier regla variable que tenga impacto transversal o de negocio. |
| Seguridad | Debe controlar quién puede modificar reglas, aprobar cambios y ejecutar decisiones sensibles. |
| Observabilidad & Control | Debe registrar aplicación de reglas, versiones, resultados, errores y eventos relevantes para auditoría y diagnóstico. |
| Datos & Analítica | Debe asegurar consistencia semántica, linaje, ownership y trazabilidad de los datos usados por reglas de negocio. |

## Análisis

### Lógica

¿Qué comportamiento habilita, restringe o modifica la decisión?
> La decisión separa la evaluación de reglas de negocio de la ejecución técnica o transaccional. Esto permite identificar qué reglas existen, dónde viven, quién las gobierna y cómo son consumidas por los componentes de la arquitectura.

### Económica

¿Qué costo, riesgo, deuda técnica, eficiencia o sustentabilidad se ve afectada?
> La decisión reduce el costo futuro de cambios al evitar reglas hardcodeadas o duplicadas en múltiples componentes. También reduce deuda técnica, retrabajo, defectos por cambios parciales y dependencia de conocimiento tácito.

### Logística

¿Cómo afecta el movimiento de datos, procesos, responsabilidades, integraciones, controles o dependencias?
> La decisión ordena el movimiento de responsabilidades dentro de la arquitectura: negocio gobierna reglas, la capa de decisiones evalúa criterios, integraciones transportan datos, el Core ejecuta transacciones y observabilidad registra evidencia de ejecución.

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 006-ARQ-ARQ | Fragmentación de la lógica de negocio en la arquitectura | Reposicionar / Reducir | La decisión reduce este riesgo al definir que la lógica de negocio variable debe identificarse y posicionarse en una capa gobernada de decisiones o mecanismo equivalente, evitando que quede distribuida sin control en múltiples componentes. |
| 007-GOB-GOB | Falta de entendimiento común sobre la lógica de negocio | Controlar | La decisión controla este riesgo al establecer una definición operativa de lógica de negocio variable y exigir que sus reglas, responsables y criterios de ubicación sean documentados de forma explícita. |
| 008-PRO-MET | Diseño sin criterio uniforme para ubicar reglas de negocio | Controlar / Reducir | La decisión establece un criterio arquitectónico uniforme para determinar dónde deben vivir reglas, validaciones, excepciones y decisiones variables, evitando que se implementen donde resulte más práctico en el momento. |
| 009-ARQ-DTE | Incremento de deuda técnica por dispersión de reglas | Reducir | La decisión reduce la probabilidad de deuda técnica al evitar reglas duplicadas, embebidas o dispersas en componentes técnicos, facilitando consolidación, mantenimiento y evolución. |
| 010-OPE-OPE | Mayor riesgo operativo por comportamiento inconsistente de la solución | Reducir / Controlar | La decisión reduce este riesgo al promover mecanismos homogéneos para aplicar reglas de negocio, disminuyendo la probabilidad de que distintos componentes ejecuten decisiones de manera diferente. |
| 011-DOC-TRA | Pérdida de trazabilidad de la lógica de negocio | Controlar | La decisión controla este riesgo al exigir que la ubicación, responsable, propósito y consumo de cada regla variable sean documentados y relacionados con procesos, componentes y riesgos. |
| 012-ARQ-TEC | Incumplimiento de autonomía de capas | Reposicionar / Controlar | La decisión protege la autonomía de capas al separar decisión, integración y ejecución, evitando que una capa asuma responsabilidades que no le corresponden. |
| 013-SOL-ARQ | Dificultad para convertir la lógica de negocio en capacidad consumible | Reposicionar / Reducir | La decisión habilita que la lógica de negocio sea tratada como una capacidad identificable, gobernable y potencialmente consumible por distintos procesos o componentes. |
| 020-ARQ-DTE | Incremento de deuda técnica por reglas embebidas | Reducir / Controlar | La decisión reduce este riesgo al establecer que las reglas variables no deben embebirse en integraciones, scripts, APIs o procesos manuales sin gobierno, sino externalizarse o documentarse bajo un mecanismo controlado. |
| 021-INT-ARQ | Integraciones convertidas en dueñas de lógica de negocio | Evitar / Reposicionar | La decisión evita que la capa de integración se convierta en dueña informal de decisiones de negocio. Las integraciones pueden consumir decisiones gobernadas, pero no definir reglas localmente sin control. |
| 029-ARQ-EXC | Crecimiento descontrolado de excepciones | Controlar / Reducir | La decisión reduce este riesgo al exigir que excepciones, reglas temporales y casos no contemplados sean tratados como reglas gobernadas, con responsable, vigencia, trazabilidad y criterio de retiro o formalización. |

> Agregar nuevos riesgos al final de la lista, sin importat la numeración.

### Riesgo antes de la decisión

Antes de esta decisión, la lógica de negocio variable podía quedar posicionada en cualquier componente de la arquitectura: integraciones, scripts, APIs, satélites, configuraciones técnicas, procesos manuales o incluso conocimiento tácito de personas o proveedores.

Esto hacía difícil identificar dónde vivía realmente una regla, quién era responsable de modificarla, qué sistemas eran impactados, qué versión estaba vigente y cómo reconstruir una decisión aplicada.

### Riesgo después de la decisión

Después de esta decisión, la lógica de negocio variable debe posicionarse en una capa gobernada de decisiones o mecanismo equivalente, con criterios explícitos de ownership, versionamiento, trazabilidad, aprobación y consumo.

El riesgo no desaparece, pero queda en un entorno más manejable, donde puede ser revisado, observado, auditado y controlado.

### Riesgo residual

Permanece el riesgo de que algunas reglas sigan implementándose localmente por urgencia, presión de proyecto, limitaciones de herramienta o falta de disciplina de gobierno.

También permanece el riesgo de que el mecanismo de decisiones se convierta en un nuevo punto de desorden si no se definen controles mínimos de ownership, versionamiento, aprobación, trazabilidad y observabilidad.

--- 

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Prioridad | Estado |
|---|---|---|---|---|---|
| CTRL-0011 | Definir criterio formal para identificar lógica de negocio variable | Distinguir reglas de negocio, validaciones técnicas, transformaciones, excepciones y configuraciones operativas. | Arquitectura Empresarial / Arquitectura de Solución / Negocio | Requerida | Pendiente |
| CTRL-0012 | Crear inventario mínimo de reglas de negocio variables | Identificar qué reglas existen, dónde viven, qué proceso impactan, quién las gobierna y qué componentes las consumen. | Arquitectura de Solución / Negocio / Integraciones | Requerida | Pendiente |
| CTRL-0013 | Definir ownership funcional y técnico por regla o dominio | Evitar ambigüedad sobre quién puede aprobar, modificar, retirar o escalar una regla de negocio. | Negocio / Gobierno de Datos / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0014 | Prohibir reglas de negocio embebidas en integraciones sin justificación arquitectónica | Evitar que integraciones se conviertan en dueñas informales de lógica de negocio. | Arquitectura Empresarial / Integraciones | Requerida | Pendiente |
| CTRL-0015 | Definir mecanismo de versionamiento para reglas variables | Permitir auditoría, rollback, análisis de impacto y control histórico de cambios. | Gobierno / Tecnología / Datos | Prioritaria | Pendiente |
| CTRL-0016 | Registrar trazabilidad de ejecución de reglas críticas | Conocer qué regla se aplicó, cuándo, sobre qué dato, bajo qué versión y con qué resultado. | Observabilidad / Integraciones / Datos | Prioritaria | Pendiente |
| CTRL-0017 | Definir flujo de aprobación para reglas nuevas, ambiguas o críticas | Evitar cambios no autorizados o reglas creadas sin revisión funcional, técnica o de riesgo. | Negocio / Gobierno / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0018 | Clasificar excepciones y reglas temporales con vigencia y criterio de retiro | Evitar que soluciones temporales o excepciones se institucionalicen como deuda técnica permanente. | Arquitectura de Solución / Negocio / Operación | Prioritaria | Pendiente |
| CTRL-0019 | Validar impacto por capa APS antes de implementar una regla | Asegurar que la regla sea ubicada en la capa correcta y no rompa autonomía de capas. | Arquitectura Empresarial / Arquitectura de Solución | Requerida | Pendiente |
| CTRL-0020 | Actualizar diagramas, inventarios y decision-log cuando se apruebe la decisión | Mantener consistencia entre ADR, documentación arquitectónica, riesgos, controles y artefactos relacionados. | Arquitectura Empresarial / Responsables de artefactos | Prioritaria | Pendiente |

---

### Impacto en procesos
| Proceso | Impacto | Nivel de criticidad |
|---|---|---|
| Procesos con reglas variables de negocio | Requieren identificar reglas, responsables, criterios de cambio y punto de ejecución/consumo. | Alto |
| Procesos con excepciones operativas | Requieren clasificación, vigencia, responsable y tratamiento formal. | Alto |
| Procesos soportados por integraciones | Requieren validar que la integración no contenga decisiones de negocio no gobernadas. | Alto |
| Procesos con impacto en datos maestros o transaccionales | Requieren trazabilidad de reglas y ownership de datos. | Alto |

### Impacto en aplicaciones
| Aplicación | Rol en la decisión | Impacto |
|---|---|---|
| Core | Ejecución transaccional / sistema central | Debe evitar absorber reglas variables que deban ser gobernadas externamente o por dominio. |
| Satélites | Ejecución especializada | Deben documentar reglas propias y evitar duplicar lógica transversal. |
| Integraciones / APIs / ETL | Transporte, orquestación y transformación técnica | No deben convertirse en dueñas de lógica de negocio. Deben consumir decisiones gobernadas cuando aplique. |
| Motor de decisiones / tablas gobernadas / mecanismo equivalente | Gobierno y evaluación de reglas | Debe proveer trazabilidad, ownership, versionamiento y control de cambios. |

### Impacto en datos

| Objeto de dato | Dueño | Impacto | Requiere gobierno |
|---|---|---|---|
| Reglas de negocio | Negocio / Gobierno | Deben tener responsable, versión, vigencia y criterio de aplicación. | Sí |
| Datos usados por reglas | Dueño de dominio | Deben tener calidad, significado y trazabilidad suficiente. | Sí |
| Resultados de evaluación de reglas | Tecnología / Operación / Observabilidad | Deben registrarse para auditoría y diagnóstico. | Sí |
| Excepciones | Negocio / Operación | Deben clasificarse y tener criterio de retiro o formalización. | Sí |

### Impacto en integraciones

| Integración / Flujo | Origen | Destino | Impacto | Acción requerida |
|---|---|---|---|---|
| Flujos que aplican reglas de negocio | Sistemas origen | Sistemas destino | Deben evitar lógica de negocio local no gobernada. | Identificar reglas embebidas y definir si deben externalizarse, documentarse o gobernarse. |
| APIs que transforman valores con significado de negocio | Consumidores / Productores | Core / Satélites / Datos | Pueden estar tomando decisiones implícitas. | Clasificar transformación técnica vs decisión de negocio. |
| ETLs o procesos batch con validaciones | Sistemas origen | Sistemas destino / Analítica | Pueden contener reglas ocultas o difíciles de auditar. | Documentar reglas, versión y criterio de aplicación. |

### Impacto en seguridad
La decisión requiere controlar quién puede crear, modificar, aprobar, ejecutar o retirar reglas de negocio variables.

Debe considerarse:

- Segregación entre quien propone una regla y quien la aprueba.
- Control de acceso a mecanismos de decisión.
- Bitácora de cambios.
- Evidencia de aprobación.
- Trazabilidad de ejecución.
- Restricción de cambios directos en producción.
- Auditoría sobre reglas críticas.

### Impacto en observabilidad
Debe poder observarse la aplicación de reglas críticas.

Como mínimo, para reglas relevantes se debe registrar:

- Identificador de operación.
- Regla aplicada.
- Versión de la regla.
- Resultado de evaluación.
- Sistema o componente que consumió la decisión.
- Fecha y hora.
- Datos clave usados en la evaluación.
- Excepción aplicada, si existe.
- Error o fallback, si ocurre.
- Correlación con proceso o transacción.

---

## Consecuencias y trade-offs

### Consecuencias positivas

- Mejora la trazabilidad de la lógica de negocio.
- Reduce reglas dispersas o duplicadas.
- Protege la autonomía de capas APS.
- Disminuye deuda técnica futura.
- Facilita auditoría y análisis de impacto.
- Permite tratar la lógica de negocio como capacidad gobernable.
- Reduce dependencia de conocimiento tácito. 

### Consecuencias negativas

- Requiere disciplina de gobierno.
- Puede incrementar esfuerzo inicial de documentación y clasificación.
- Puede requerir nuevas capacidades técnicas o administrativas.
- Puede generar resistencia si los equipos están acostumbrados a resolver reglas localmente.
- Puede requerir ajustes en flujos existentes.

### Trade-offs aceptados

- Se acepta mayor esfuerzo inicial de gobierno y documentación a cambio de reducir deuda técnica, mejorar trazabilidad y evitar que las reglas de negocio queden dispersas.
- Se acepta que algunas reglas puedan permanecer temporalmente en componentes existentes, siempre que sean identificadas, documentadas y tengan plan de revisión.
- Se acepta que no toda regla requiere un motor formal de decisiones, pero toda regla variable relevante sí requiere ownership, trazabilidad y criterio de gobierno.

---

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Definición de criterios de lógica de negocio variable | Metodológica | Arquitectura Empresarial / Negocio | Abierta |
| Inventario de reglas existentes | Documental / Arquitectónica | Arquitectura de Solución / Integraciones | Abierta |
| Definición de ownership por dominio | Gobierno | Negocio / Gobierno de Datos | Abierta |
| Mecanismo de versionamiento y trazabilidad | Técnico / Gobierno | Tecnología / Observabilidad | Abierta |
| Actualización de diagramas APS | Arquitectónica | Arquitectura Empresarial / Solución | Abierta |

## Criterios de aceptación
La decisión podrá considerarse aceptada cuando:

- [ ] Se apruebe la definición de lógica de negocio variable.
- [ ] Se confirme que las integraciones no serán dueñas informales de reglas de negocio.
- [ ] Se defina un criterio mínimo para ubicar reglas por capa APS.
- [ ] Se identifiquen los riesgos relacionados en el decision-log.
- [ ] Se definan controles mínimos de ownership, versionamiento, trazabilidad y aprobación.
- [ ] Se registre este ADR en el `decision-log.xlsx`.
- [ ] Se identifiquen artefactos arquitectónicos que deben actualizarse.
- [ ] Se comuniquen las implicaciones a equipos de integración, solución, negocio y proveedor.

---

## Artefactos relacionados

| Artefacto | Liga / Referencia | Comentarios |
|---|---|---|
| `registers/decision-log.xlsx` |  | Registrar ADR, riesgos relacionados, controles y estado. |
| Registro de riesgos |  | Relacionar riesgos 006, 007, 008, 009, 010, 011, 012, 013, 020, 021 y 029. |
| Diagramas APS |  | Reflejar separación entre Gobierno / Decisiones, Integraciones, Core y Satélites. |
| Inventario de integraciones |  | Identificar flujos con reglas embebidas o decisiones implícitas. |
| Inventario de reglas de negocio |  | Crear o actualizar cuando exista. |
| Documento de solución |  | Incluir criterio de ubicación de reglas de negocio variables. |

## Decisiones Relacionadas
| ADR relacionado | Relación |
|---|---|
| ADR-0001 - Uso del RDA como fuente de verdad arquitectónica | Este ADR depende del RDA como mecanismo para documentar y gobernar decisiones. |
| ADR-0003 - Integraciones no dueñas de lógica de negocio | Puede derivarse como decisión específica o extensión de este ADR. |
| ADR-0004 - Gobierno mínimo de tabla de equivalencias | Complementa esta decisión para el caso específico de equivalencias y transformación de datos. |

## Responsables y aprobación

| Rol | Nombre / Área | Decisión | Fecha |
|---|---|---|---|
| Arquitectura Empresarial | Roberto Mancha | Pendiente |  |
| Arquitectura de Solución | Daniel Cuevas  | Aprobado | 2026-07-07  |
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
| 2026-07-07 | Creación del ADR |  |
