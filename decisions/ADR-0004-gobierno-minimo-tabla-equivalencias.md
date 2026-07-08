# ADR-0004 - Gobierno mínimo de tabla de equivalencias

## Estado

Proposed

## Fecha

2026-07-08

## Responsables

| Rol | Nombre | Área |
|---|---|---|
| Arquitectura Empresarial | Roberto Mancha | Arquitectura Empresairal y de TI |
| Arquitectura de Solución | Daniel Cuevas | Proyecto ADN |
| Negocio |  |  |
| Integraciones |  |  |
| Datos |  |  |
| Seguridad |  |  |
| Observabilidad / Operación |  |  |
| Proveedor |  |  |

---

## Contexto

Durante el análisis de riesgos se identificó la necesidad de utilizar una tabla o mecanismo de equivalencias para resolver diferencias entre valores, códigos, catálogos, atributos o estructuras provenientes de distintos sistemas.

La tabla de equivalencias puede ser útil como mecanismo técnico para migración, integración, homologación o transición entre sistemas. Sin embargo, si no se gobierna adecuadamente, puede convertirse en una capa informal de decisión que determina el significado operativo de los datos sin ownership, trazabilidad, versionamiento, aprobación ni criterios claros de vigencia.

El riesgo principal no está en la existencia de una tabla de equivalencias, sino en que dicha tabla empiece a operar como si fuera fuente de verdad, motor de decisión, sustituto del MDM o mecanismo informal de aprobación de datos maestros.

Esto puede afectar procesos críticos relacionados con artículos, proveedores, unidades de medida, clasificaciones, estados, precios, inventario, recepción, disponibilidad, surtido, reporting, analítica y operación logística.

Por ello, este ADR define el gobierno mínimo requerido para que una tabla de equivalencias pueda ser utilizada sin convertirse en un “closet de blancos” o en una deuda técnica institucionalizada.

---

## Pregunta arquitectónica

¿La tabla de equivalencias debe tratarse como un insumo técnico temporal o como un artefacto gobernado de decisión y transformación de datos?

---

## Decisión

Se decide que la tabla de equivalencias no debe operar como una estructura técnica informal ni como fuente de verdad no gobernada.

Toda tabla o mecanismo de equivalencias que impacte procesos, datos maestros, integraciones, transformación semántica, migración o consumo operativo debe tratarse como un artefacto gobernado.

Como mínimo, cada equivalencia relevante debe contar con ownership, vigencia, versionamiento, criterio de aplicación, trazabilidad de origen, justificación, estado, responsable de aprobación y mecanismos de revisión.

La tabla de equivalencias puede ser utilizada como mecanismo transitorio o permanente, pero su naturaleza debe declararse explícitamente. Si es transitoria, debe existir un plan de retiro. Si es permanente, debe contar con gobierno operativo formal.

La tabla de equivalencias no debe actualizar automáticamente el golden record del MDM ni sustituir la definición de valores canónicos por dominio. Cualquier promoción hacia datos maestros debe pasar por validación, ownership y aprobación correspondiente.

---

## Justificación

La decisión busca evitar que una herramienta práctica para resolver diferencias entre sistemas se convierta en una capa oculta de decisión, gobierno o verdad de datos.

Una equivalencia puede parecer una simple relación entre valor origen y valor destino, pero en muchos casos representa una interpretación de negocio. Por ejemplo, dos valores pueden parecer equivalentes técnicamente, pero tener significados distintos según dominio, unidad de negocio, proveedor, vigencia, artículo, proceso o contexto operativo.

Cuando una tabla de equivalencias no tiene gobierno, pueden aparecer problemas como:

- Propagación de valores incorrectos al MDM.
- Divergencia semántica entre sistemas.
- Cambios sin trazabilidad.
- Aprobaciones implícitas.
- Falta de ownership.
- Reglas embebidas en integraciones.
- Excepciones acumuladas.
- Errores operativos por mapeos ambiguos.
- Dificultad para auditar decisiones de transformación.
- Deuda técnica permanente disfrazada de solución temporal.

Esta decisión no elimina la necesidad de equivalencias. Lo que establece es que las equivalencias deben operar dentro de un marco mínimo de gobierno, trazabilidad y control.

---

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Usar tabla de equivalencias como insumo técnico simple | La tabla se administra como correspondencia directa entre valores origen y destino. | Fácil de implementar y rápida para migraciones o integraciones iniciales. | No controla ambigüedad, vigencia, ownership, auditoría ni impacto semántico. | Rechazada |
| Resolver equivalencias directamente en integraciones | Cada flujo aplica mapeos, reglas o conversiones según su necesidad. | Puede resolver casos puntuales rápidamente. | Duplica reglas, oculta decisiones, aumenta deuda técnica y dificulta auditoría. | Rechazada |
| Cargar equivalencias directamente al MDM | Los valores equivalentes actualizan o alimentan datos maestros sin gobierno adicional. | Puede acelerar carga o sincronización inicial. | Riesgo alto de contaminar el golden record con valores no validados. | Rechazada |
| Usar tabla de equivalencias gobernada | La tabla tiene ownership, vigencia, versión, aprobación, trazabilidad y controles mínimos. | Permite uso práctico sin perder control, auditoría ni responsabilidad. | Requiere disciplina de gobierno y mantenimiento. | Aceptada |
| Definir modelo canónico completo antes de cualquier equivalencia | No se usan equivalencias hasta tener catálogo canónico aprobado. | Maximiza control semántico. | Puede ser inviable para fases tempranas, migraciones o escenarios transitorios. | Rechazada como condición absoluta |
| Usar equivalencias transitorias con plan de retiro | La tabla se permite como mecanismo temporal con fecha, métricas y ruta de salida. | Permite avanzar sin institucionalizar deuda técnica. | Requiere seguimiento para evitar que lo temporal se vuelva permanente. | Parcialmente aceptada |

---

## Impacto APS

| Capa APS | Impacto |
|---|---|
| Experiencia / Cliente | Puede verse afectada si equivalencias incorrectas impactan disponibilidad, precios, surtido, estados, clasificaciones o información visible al usuario. |
| Gobierno / Políticas / Decisiones | Debe definir ownership, reglas de aprobación, vigencia, criterios de ambigüedad y tratamiento de excepciones. |
| Integraciones / Orquestación | Puede consumir equivalencias, pero no debe convertirlas en reglas locales no gobernadas dentro de flujos, APIs, ETLs o scripts. |
| Core | Puede recibir datos transformados por equivalencias, por lo que requiere confianza sobre la validez, vigencia y trazabilidad de dichos valores. |
| Satélites | Pueden depender de equivalencias para operar, consumir o interpretar datos provenientes de otros sistemas. |
| Seguridad | Debe controlar quién puede crear, modificar, aprobar, publicar o retirar equivalencias. |
| Observabilidad & Control | Debe registrar cambios, versiones, reglas aplicadas, excepciones, errores y eventos relevantes para auditoría y diagnóstico. |
| Datos & Analítica | Debe asegurar consistencia semántica, linaje, versión, estado, vigencia y relación con valores canónicos o dominios de datos. |

---

## Análisis

### Lógica

>La decisión distingue entre una equivalencia técnica y una decisión de significado. Una tabla de equivalencias no debe limitarse a mapear valores, sino que debe explicar bajo qué criterio un valor origen puede transformarse en un valor destino y en qué contexto dicha transformación es válida.

### Económica

>La decisión reduce el costo futuro de corrección, retrabajo, auditoría e incidentes causados por mapeos incorrectos, duplicados o ambiguos. También evita que una solución temporal se convierta en deuda técnica permanente sin que la organización lo haya aceptado explícitamente.

### Logística

>La decisión ordena el movimiento de datos entre sistemas al establecer que las equivalencias deben tener responsable, vigencia, estado, versión y criterio de aplicación. Esto permite mover datos de un sistema a otro sin perder control sobre el significado, la trazabilidad y la responsabilidad de la transformación.

---

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 016-GOB-GOB | Tabla de equivalencias convertida en capa informal de decisión | Reposicionar / Controlar | La decisión controla este riesgo al establecer que la tabla de equivalencias debe operar como artefacto gobernado, con ownership, reglas explícitas, vigencia, validación y ruta de aprobación. |
| 017-MDM-GOB | Propagación de valores no gobernados al golden record | Controlar / Reducir | La decisión reduce este riesgo al establecer que una equivalencia técnica no puede actualizar automáticamente el MDM ni promover valores al golden record sin validación y aprobación explícita. |
| 018-DAT-SEM | Divergencia semántica entre sistemas | Reducir / Controlar | La decisión controla este riesgo al exigir que las equivalencias documenten significado, dominio, contexto, valor canónico cuando aplique y criterio de interpretación. |
| 019-GOB-TRA | Pérdida de trazabilidad de equivalencias y cambios | Controlar | La decisión controla este riesgo al requerir bitácora, versión, vigencia, solicitante, aprobador, motivo, estado y sistemas impactados por cada equivalencia relevante. |
| 020-ARQ-DTE | Incremento de deuda técnica por reglas embebidas | Reducir / Reposicionar | La decisión reduce este riesgo al evitar que las reglas de conversión se dispersen en integraciones, scripts, APIs o procesos manuales, posicionándolas en un mecanismo gobernado. |
| 022-OPE-OPE | Errores operativos por mapeos ambiguos | Reducir / Controlar | La decisión reduce este riesgo al exigir clasificación de ambigüedad, criticidad y contexto de aplicación antes de permitir equivalencias automáticas. |
| 023-GOB-OWN | Aprobaciones implícitas sin ownership claro | Controlar | La decisión controla este riesgo al establecer que la existencia de una equivalencia no equivale a aprobación de negocio; debe existir owner, steward o responsable formal por dominio o atributo. |
| 025-DAT-VER | Falta de vigencia y versionamiento de equivalencias | Controlar | La decisión controla este riesgo al requerir fecha de inicio, fecha de fin, versión, estado, motivo de cambio y criterio de retiro para equivalencias relevantes. |
| 026-AUD-TRA | Dificultad para auditar decisiones de transformación | Controlar / Reducir | La decisión reduce este riesgo al exigir evidencia suficiente para explicar por qué un valor fuente fue transformado en un valor objetivo, bajo qué regla, versión y aprobación. |
| 029-ARQ-EXC | Crecimiento descontrolado de excepciones | Reducir / Controlar | La decisión reduce este riesgo al establecer que excepciones deben clasificarse, aprobarse, versionarse y contar con vigencia, responsable y criterio de retiro o formalización. |
| 032-GOB-SEG | Falta de segregación entre captura, decisión y persistencia | Controlar | La decisión controla este riesgo al separar responsabilidades entre captura/propuesta, validación, decisión, aprobación, persistencia y publicación. |
| 033-MDM-CON | Pérdida de control sobre datos maestros | Controlar / Reducir | La decisión reduce este riesgo al asegurar que todo dato que llegue al MDM desde equivalencias tenga trazabilidad de origen, regla, aprobación, vigencia y responsable. |
| 035-ARQ-RET | Dificultad para retirar una solución supuestamente temporal | Controlar / Reducir | La decisión controla este riesgo al exigir declarar si la tabla de equivalencias es transitoria o permanente; si es transitoria, debe contar con plan de retiro, métricas y fecha o condición de salida. |

---

### Riesgo antes de la decisión

Antes de esta decisión, la tabla de equivalencias podía operar como una herramienta técnica sin gobierno explícito.

Esto implicaba que una equivalencia podía ser creada, modificada, utilizada o interpretada sin claridad sobre quién la aprobó, desde cuándo aplica, qué sistemas impacta, si representa un valor canónico, si es temporal o permanente, o si puede afectar datos maestros.

El riesgo quedaba posicionado en una estructura técnica que podía terminar tomando decisiones de negocio y datos sin contar con ownership, segregación de funciones, versionamiento ni trazabilidad suficiente.

---

### Riesgo después de la decisión

Después de esta decisión, la tabla de equivalencias queda posicionada como un artefacto gobernado.

Las equivalencias pueden seguir utilizándose para migración, integración, transición o soporte operativo, pero deben contar con controles mínimos de ownership, vigencia, versión, trazabilidad, aprobación, clasificación de ambigüedad y criterio de retiro.

El riesgo no desaparece, pero se reposiciona en un entorno donde puede ser administrado, auditado, observado y reducido.

---

### Riesgo residual

Permanece el riesgo de que algunas equivalencias sean creadas o modificadas de forma urgente sin seguir el flujo de gobierno definido.

También permanece el riesgo de que la tabla de equivalencias se convierta en una solución permanente sin que exista una decisión explícita sobre su permanencia.

Adicionalmente, puede persistir el riesgo de que ciertas equivalencias sean semánticamente ambiguas y requieran intervención humana, validación de dominio o definición de un valor canónico.

---

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Prioridad | Estado |
|---|---|---|---|---|---|
| CTRL-0031 | Definir modelo mínimo de datos para la tabla de equivalencias | Asegurar que cada equivalencia tenga campos de origen, destino, dominio, vigencia, versión, estado, justificación, responsable y sistemas impactados. | Datos / Arquitectura de Solución | Requerida | Pendiente |
| CTRL-0032 | Definir ownership por dominio, atributo o tipo de equivalencia | Evitar aprobaciones implícitas y asegurar que cada equivalencia tenga responsable funcional o de datos. | Negocio / Gobierno de Datos | Requerida | Pendiente |
| CTRL-0033 | Establecer flujo de aprobación para equivalencias nuevas, ambiguas o críticas | Evitar que equivalencias con impacto operativo o maestro sean publicadas sin validación correspondiente. | Gobierno de Datos / Negocio / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0034 | Incorporar vigencia, versión y estado por equivalencia | Permitir auditoría histórica, rollback, retiro de reglas y aplicación correcta por contexto temporal. | Datos / Tecnología | Requerida | Pendiente |
| CTRL-0035 | Clasificar equivalencias por criticidad y ambigüedad | Identificar qué equivalencias pueden aplicarse automáticamente y cuáles requieren revisión o aprobación manual. | Datos / Negocio / Operación | Requerida | Pendiente |
| CTRL-0036 | Registrar bitácora de cambios de equivalencias | Mantener evidencia de quién cambió qué, cuándo, por qué, con qué aprobación y qué sistemas fueron impactados. | Datos / Observabilidad / Tecnología | Requerida | Pendiente |
| CTRL-0037 | Prohibir actualización automática del golden record desde equivalencias no aprobadas | Evitar contaminación del MDM con valores sin validación de dominio, autoridad o vigencia. | MDM / Gobierno de Datos / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0038 | Definir criterios para equivalencias transitorias y permanentes | Evitar que una solución temporal se vuelva permanente sin decisión arquitectónica explícita. | Arquitectura Empresarial / Datos / PMO | Prioritaria | Pendiente |
| CTRL-0039 | Documentar tratamiento de excepciones | Evitar crecimiento descontrolado de columnas, reglas locales, parches o mapeos especiales sin gobierno. | Arquitectura de Solución / Datos / Integraciones | Prioritaria | Pendiente |
| CTRL-0040 | Validar equivalencias contra valor canónico cuando exista | Asegurar alineación semántica con dominios de datos y evitar divergencia entre sistemas. | Gobierno de Datos / MDM | Prioritaria | Pendiente |
| CTRL-0041 | Registrar consumo de equivalencias por integraciones o procesos | Permitir trazabilidad sobre qué flujos, APIs, ETLs, migraciones o procesos dependen de cada equivalencia. | Integraciones / Datos / Observabilidad | Prioritaria | Pendiente |
| CTRL-0042 | Definir plan de retiro si la tabla es transitoria | Evitar institucionalizar deuda técnica y establecer una ruta hacia el modelo canónico o solución permanente. | Arquitectura Empresarial / PMO / Datos | Prioritaria | Pendiente |

---

## Impacto en procesos

| Proceso | Impacto | Nivel de criticidad |
|---|---|---|
| Procesos de migración de datos | Requieren equivalencias con vigencia, versión, trazabilidad y criterio de validación. | Alto |
| Procesos de integración entre sistemas | Requieren consumir equivalencias gobernadas y no reglas locales no documentadas. | Alto |
| Procesos de administración de datos maestros | Requieren validación antes de promover equivalencias hacia MDM o golden record. | Alto |
| Procesos operativos con catálogos, estados o clasificaciones | Requieren controlar ambigüedad y contexto de aplicación de equivalencias. | Alto |
| Procesos de auditoría y soporte | Requieren reconstruir por qué y cómo se transformó un valor. | Alto |
| Procesos con excepciones o casos no contemplados | Requieren clasificación, aprobación, vigencia y criterio de retiro. | Medio / Alto |

---

## Impacto en aplicaciones

| Aplicación | Rol en la decisión | Impacto |
|---|---|---|
| MDM | Fuente de verdad / datos maestros | No debe recibir valores derivados de equivalencias sin validación, aprobación y trazabilidad. |
| Core | Sistema central / ejecución operativa | Puede consumir datos transformados, por lo que requiere confianza en la validez de equivalencias. |
| Satélites | Sistemas consumidores o productores | Deben alinearse a equivalencias gobernadas cuando intercambien datos con otros sistemas. |
| Integraciones / APIs / ETL | Transporte y transformación técnica | Pueden consumir equivalencias, pero no administrarlas como reglas locales no gobernadas. |
| Tabla de equivalencias / repositorio gobernado | Artefacto de gobierno de transformación | Debe contar con ownership, vigencia, versión, estado, aprobación y trazabilidad. |
| Herramientas de observabilidad | Auditoría y diagnóstico | Deben registrar cambios, uso, errores y excepciones relacionadas con equivalencias. |

---

## Impacto en datos

| Objeto de dato | Dueño | Impacto | Requiere gobierno |
|---|---|---|---|
| Equivalencias | Gobierno de Datos / Dominio funcional | Deben tener owner, vigencia, versión, estado, justificación y criterio de aplicación. | Sí |
| Valores origen | Sistema dueño / Dominio | Deben conservar trazabilidad hacia el sistema y significado original. | Sí |
| Valores destino | Sistema destino / Dominio | Deben validarse contra valor canónico o criterio autorizado. | Sí |
| Valores canónicos | MDM / Gobierno de Datos | Deben distinguirse de equivalencias técnicas o transitorias. | Sí |
| Golden record | MDM / Data Owner | No debe actualizarse por equivalencias no aprobadas. | Sí |
| Excepciones | Negocio / Operación / Datos | Deben clasificarse, aprobarse y tener vigencia o plan de retiro. | Sí |
| Bitácora de cambios | Datos / Tecnología / Observabilidad | Debe permitir auditoría y reconstrucción de decisiones. | Sí |

---

## Impacto en integraciones

| Integración / Flujo | Origen | Destino | Impacto | Acción requerida |
|---|---|---|---|---|
| Flujos que consumen equivalencias | Sistemas origen | Sistemas destino | Deben consumir equivalencias gobernadas, no reglas locales dispersas. | Documentar dependencia, versión y criterio de aplicación. |
| Flujos que actualizan MDM | Sistemas origen / tablas de equivalencia | MDM | Pueden impactar el golden record. | Validar ownership, aprobación y trazabilidad antes de publicar. |
| ETLs de migración | Sistemas legacy | Sistemas To-Be / datos | Requieren equivalencias con vigencia y bitácora. | Registrar versión, estado y justificación de transformación. |
| APIs de homologación | Consumidores | Servicios destino | Pueden ocultar decisiones semánticas. | Separar transformación técnica de equivalencia gobernada. |
| Flujos con excepciones | Sistemas origen | Core / Satélites / Datos | Pueden generar deuda técnica si no se controlan. | Registrar responsable, vigencia y plan de salida. |

---

## Impacto en seguridad

La decisión requiere controlar quién puede crear, modificar, aprobar, publicar o retirar equivalencias.

Debe considerarse:

- Segregación entre captura, validación, aprobación, persistencia y publicación.
- Control de acceso a la tabla o mecanismo de equivalencias.
- Restricción de cambios directos en producción.
- Bitácora de cambios obligatoria.
- Evidencia de aprobación para equivalencias críticas.
- Control de permisos para proveedor, equipo técnico y usuarios de negocio.
- Revisión de cambios con impacto en MDM o sistemas críticos.
- Auditoría de equivalencias que afecten procesos financieros, fiscales, logísticos u operativos.

---

## Impacto en observabilidad

Debe poder observarse el ciclo de vida y consumo de equivalencias.

Como mínimo, para equivalencias relevantes se debe registrar:

- Identificador de equivalencia.
- Dominio o atributo afectado.
- Valor origen.
- Valor destino.
- Valor canónico relacionado, si existe.
- Versión.
- Estado.
- Fecha de inicio y fin de vigencia.
- Usuario o sistema que creó la equivalencia.
- Usuario o rol que aprobó la equivalencia.
- Motivo de creación o cambio.
- Sistema o flujo que consumió la equivalencia.
- Resultado de aplicación.
- Excepción aplicada, si existe.
- Error, rechazo o envío a revisión manual.
- Correlación con operación, carga, integración o proceso.

---

## Consecuencias

### Consecuencias positivas

- Reduce el riesgo de que la tabla de equivalencias se convierta en una capa informal de decisión.
- Mejora trazabilidad de transformaciones entre sistemas.
- Protege el golden record del MDM.
- Reduce divergencia semántica entre sistemas.
- Permite auditar cambios y decisiones de transformación.
- Mejora control sobre excepciones.
- Evita que reglas de conversión queden dispersas en integraciones.
- Facilita transición hacia modelos canónicos por dominio.
- Permite distinguir soluciones transitorias de permanentes.

### Consecuencias negativas

- Requiere mayor esfuerzo inicial de gobierno y documentación.
- Puede hacer más lento el alta de nuevas equivalencias críticas.
- Requiere ownership de negocio o datos que puede no estar formalmente definido.
- Puede exponer ambigüedades semánticas que antes estaban ocultas.
- Puede requerir ajustes en integraciones o procesos existentes.
- Puede generar resistencia si los equipos están acostumbrados a resolver equivalencias localmente.

### Trade-offs aceptados

- Se acepta mayor disciplina de gobierno a cambio de reducir errores operativos, divergencia semántica y deuda técnica.
- Se acepta que algunas equivalencias puedan iniciar como transitorias, siempre que tengan plan de retiro o formalización.
- Se acepta que no toda equivalencia requiere el mismo nivel de aprobación, pero toda equivalencia crítica o ambigua debe tener ownership, vigencia y trazabilidad.
- Se acepta que la tabla de equivalencias puede ser una solución pragmática, pero no debe sustituir indefinidamente al modelo canónico ni al gobierno de datos maestros.

---

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Definición del modelo mínimo de equivalencias | Datos / Gobierno | Gobierno de Datos / Arquitectura de Solución | Abierta |
| Identificación de dominios y atributos críticos | Datos / Negocio | Negocio / Gobierno de Datos | Abierta |
| Definición de owners y stewards | Gobierno | Negocio / Gobierno de Datos | Abierta |
| Definición de flujo de aprobación | Gobierno / Proceso | Gobierno de Datos / PMO | Abierta |
| Relación con MDM y golden record | Arquitectónica / Datos | MDM / Arquitectura Empresarial | Abierta |
| Identificación de integraciones consumidoras | Técnica / Arquitectónica | Integraciones / Arquitectura de Solución | Abierta |
| Mecanismo de bitácora y versionamiento | Técnico / Observabilidad | Tecnología / Observabilidad | Abierta |
| Definición de plan de retiro, si aplica | Arquitectónica / PMO | Arquitectura Empresarial / PMO | Abierta |

---

## Criterios de aceptación

La decisión podrá considerarse aceptada cuando:

- [ ] Se apruebe que la tabla de equivalencias debe tratarse como artefacto gobernado.
- [ ] Se defina el modelo mínimo de datos para registrar equivalencias.
- [ ] Se definan ownership y responsables por dominio o atributo.
- [ ] Se establezca flujo de aprobación para equivalencias nuevas, críticas o ambiguas.
- [ ] Se defina cómo se manejarán vigencia, versión, estado y retiro.
- [ ] Se establezca que equivalencias no aprobadas no pueden actualizar el golden record.
- [ ] Se identifiquen integraciones, procesos o cargas que consumirán equivalencias.
- [ ] Se definan controles mínimos de trazabilidad y bitácora.
- [ ] Se registre este ADR en el `decision-log.xlsx`.
- [ ] Se relacionen los riesgos correspondientes en el registro de riesgos.
- [ ] Se defina si la tabla será transitoria o permanente.
- [ ] Se comuniquen las implicaciones a equipos de datos, integración, solución, negocio y proveedor.

---

## Relación con otros artefactos

| Artefacto | Liga / Referencia | Comentarios |
|---|---|---|
| `registers/decision-log.xlsx` |  | Registrar ADR, riesgos relacionados, controles y estado. |
| Registro de riesgos |  | Relacionar riesgos 016, 017, 018, 019, 020, 022, 023, 025, 026, 029, 032, 033 y 035. |
| Inventario de integraciones |  | Identificar flujos que consumen, generan o modifican equivalencias. |
| Catálogo de datos / MDM |  | Distinguir valores canónicos, equivalencias técnicas y reglas de promoción al golden record. |
| Diagramas APS |  | Reflejar la tabla de equivalencias como artefacto gobernado entre Datos, Gobierno e Integraciones. |
| Documento de solución |  | Incluir criterio de uso, gobierno y trazabilidad de equivalencias. |
| Bitácora de cambios |  | Registrar creación, modificación, aprobación, vigencia y retiro de equivalencias. |

---

## Decisiones relacionadas

| ADR relacionado | Relación |
|---|---|
| ADR-0001 - Uso del RDA como fuente de verdad arquitectónica | Este ADR depende del RDA como mecanismo para documentar y gobernar decisiones. |
| ADR-0002 - Ubicación y gobierno de lógica de negocio variable | Este ADR complementa la definición de dónde deben vivir reglas variables y decisiones de negocio. |
| ADR-0003 - Integraciones no dueñas de lógica de negocio | Este ADR complementa la restricción de no embebir reglas de equivalencia directamente en integraciones. |
| ADR-0005 - Relación entre equivalencias, MDM y golden record | Puede derivarse como decisión específica sobre promoción de valores hacia datos maestros. |
| ADR-0006 - Definición de modelo canónico por dominio | Complementa esta decisión al definir el destino semántico deseado de largo plazo. |
| ADR-0007 - Segregación entre captura, validación, decisión, aprobación y persistencia | Complementa esta decisión desde el punto de vista de control interno y ownership. |

---

## Revisión futura

| Fecha de revisión | Motivo | Responsable |
|---|---|---|
|  | Confirmar si la tabla de equivalencias será transitoria o permanente. | Arquitectura Empresarial / Datos |
|  | Validar si ya existen equivalencias críticas, ambiguas o many-to-one. | Gobierno de Datos / Negocio |
|  | Revisar si alguna equivalencia impacta MDM o golden record. | MDM / Gobierno de Datos |
|  | Confirmar si integraciones están usando reglas locales de equivalencia. | Integraciones / Arquitectura de Solución |
|  | Evaluar avance hacia modelo canónico por dominio. | Arquitectura Empresarial / Gobierno de Datos |

---

## Aprobación

| Nombre | Rol | Decisión | Fecha |
|---|---|---|---|
|  | Arquitectura Empresarial | Pendiente |  |
|  | Arquitectura de Solución | Pendiente |  |
|  | Negocio | Pendiente |  |
|  | Integraciones | Pendiente |  |
|  | Datos | Pendiente |  |
|  | Seguridad | Pendiente |  |
|  | Observabilidad / Operación | Pendiente |  |