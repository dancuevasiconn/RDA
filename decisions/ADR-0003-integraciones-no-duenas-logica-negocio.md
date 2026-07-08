# ADR-0003 - Integraciones no dueñas de lógica de negocio

## Estado

Propuesto

## Fecha

2026-07-08

## Responsables

| Rol | Nombre | Área |
|---|---|---|
| Arquitectura Empresarial | Roberto Mancha | Arquitectura Empresarial y de TI |
| Arquitectura de Solución | Daniel Cuevas | Proyecto ADN |
| Negocio |  |  |
| Integraciones |  |  |
| Datos |  |  |
| Seguridad |  |  |
| Observabilidad / Operación |  |  |
| Proveedor |  |  |

---

## Contexto

Durante el análisis de riesgos se identificó que la capa de Integraciones / Orquestación puede convertirse en un punto donde se implemente lógica de negocio de manera informal.

Esto puede ocurrir porque las integraciones tienen visibilidad del dato en movimiento, conectan sistemas origen y destino, ejecutan transformaciones, validan estructuras, enrutan información y en ocasiones resuelven diferencias entre aplicaciones.

Sin embargo, que una integración vea pasar el dato no significa que deba ser dueña de la decisión de negocio asociada a ese dato.

Cuando las integraciones empiezan a contener reglas, excepciones, validaciones funcionales, decisiones operativas o criterios de negocio, se corre el riesgo de mezclar responsabilidades arquitectónicas: la integración deja de ser un mecanismo de comunicación y empieza a operar como una capa informal de gobierno, decisión o ejecución funcional.

Esto puede generar acoplamiento, baja trazabilidad, dificultad de mantenimiento, dependencia de conocimiento técnico, incremento de deuda técnica y pérdida de claridad sobre quién es dueño real de la lógica de negocio.

Este ADR complementa el ADR-0002, pero se enfoca específicamente en delimitar la responsabilidad de la capa de Integraciones / Orquestación.

---

## Pregunta arquitectónica

¿Las integraciones deben ejecutar lógica de negocio o limitarse a transportar, transformar técnicamente, enrutar y orquestar información?

---

## Decisión

Se decide que la capa de Integraciones / Orquestación no debe ser dueña de lógica de negocio.

Las integraciones pueden transportar datos, transformar estructuras técnicas, enrutar mensajes, coordinar secuencias, ejecutar validaciones técnicas, exponer contratos y consumir decisiones gobernadas, pero no deben definir ni administrar reglas de negocio variables de forma local, implícita o hardcodeada.

Cuando una integración requiera aplicar una regla de negocio, excepción, validación funcional o criterio operativo, dicha regla deberá estar documentada, gobernada y aprobada por la capa o mecanismo correspondiente de Gobierno / Políticas / Decisiones, o por el dominio funcional responsable.

La integración podrá consumir el resultado de una decisión gobernada, pero no deberá convertirse en la fuente informal de dicha decisión.

---

## Justificación

La decisión busca proteger la autonomía de capas dentro del modelo APS.

La capa de Integraciones / Orquestación cumple una función logística dentro de la arquitectura: mover datos, coordinar flujos, transformar estructuras técnicas y permitir comunicación entre componentes. Su función principal no es definir políticas de negocio ni administrar reglas funcionales variables.

Cuando la integración concentra lógica de negocio, se generan varios problemas:

- Las reglas quedan ocultas dentro de flujos técnicos.
- Negocio pierde visibilidad sobre criterios aplicados.
- Los cambios de política requieren desarrollo técnico.
- La auditoría se vuelve más compleja.
- Se incrementa la dependencia del equipo técnico o proveedor.
- Se dificulta identificar la fuente real de una decisión.
- Se rompe la separación entre decisión, transporte y ejecución.
- La integración puede convertirse en un “bus universal” que resuelve responsabilidades de otras capas.

Esta decisión no impide que existan transformaciones técnicas dentro de integraciones. Lo que establece es que debe distinguirse claramente entre transformación técnica y decisión de negocio.

Una transformación técnica cambia formato, estructura, protocolo, mapeo o contrato.

Una decisión de negocio aplica una regla, política, excepción, criterio funcional o validación que afecta el comportamiento operativo de la solución.

---

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Permitir lógica de negocio en integraciones | Las integraciones implementan reglas, validaciones o excepciones cuando tengan acceso al dato necesario. | Puede acelerar implementaciones puntuales y reducir dependencias inmediatas. | Mezcla transporte con decisión, oculta reglas, aumenta deuda técnica y dificulta trazabilidad. | Rechazada |
| Prohibir cualquier lógica dentro de integraciones | Las integraciones solo transportan datos sin ningún tipo de transformación o validación. | Máxima separación conceptual. | No es práctico, porque las integraciones requieren validaciones técnicas, mapeos, transformación de formatos y control de contratos. | Rechazada |
| Permitir únicamente lógica técnica en integraciones | Las integraciones pueden transformar, validar estructura, enrutar y orquestar, pero no administrar reglas de negocio variables. | Mantiene autonomía de capas y permite operación técnica realista. | Requiere criterio claro para distinguir lógica técnica de lógica de negocio. | Aceptada |
| Permitir excepciones documentadas | Algunas reglas pueden permanecer temporalmente en integraciones si existe justificación, vigencia, responsable y plan de salida. | Permite manejar restricciones reales de proyecto sin perder gobierno. | Puede volverse permanente si no existe seguimiento. | Parcialmente aceptada |
| Consumir decisiones gobernadas desde integración | La integración consume una decisión evaluada por un componente o mecanismo gobernado. | Permite orquestación sin que la integración sea dueña de la regla. | Requiere contratos, trazabilidad y disponibilidad del mecanismo de decisión. | Aceptada |

---

## Impacto APS

| Capa APS | Impacto |
|---|---|
| Experiencia / Cliente | Puede verse afectada indirectamente si una integración aplica reglas que impactan precios, disponibilidad, recepción, aprobación, rechazo o comportamiento visible al usuario. |
| Gobierno / Políticas / Decisiones | Debe ser la capa responsable de definir reglas, políticas, excepciones y criterios funcionales variables. |
| Integraciones / Orquestación | Se delimita su responsabilidad: transportar, transformar técnicamente, enrutar, orquestar y consumir decisiones, pero no ser dueña de lógica de negocio. |
| Core | Debe ejecutar capacidades centrales sin depender de reglas ocultas en integraciones que modifiquen comportamiento funcional sin gobierno. |
| Satélites | Deben evitar delegar decisiones funcionales a integraciones como solución rápida o sustituto de gobierno. |
| Seguridad | Debe controlar quién puede modificar flujos de integración, reglas asociadas, parámetros o condiciones que afecten decisiones de negocio. |
| Observabilidad & Control | Debe permitir identificar cuándo una integración ejecutó transformación técnica, consumió una decisión gobernada o aplicó una excepción autorizada. |
| Datos & Analítica | Debe distinguir entre mapeo técnico, transformación semántica, equivalencia y decisión de negocio, conservando linaje y trazabilidad. |

---

## Análisis

### Lógica

>[!Important]La decisión separa la función de comunicación y orquestación de la función de decisión de negocio. Las integraciones siguen siendo responsables de mover y adaptar datos entre sistemas, pero no de definir criterios funcionales variables que corresponden a gobierno, negocio o dominios específicos.

### Económica

>La decisión reduce deuda técnica al evitar que cambios de negocio dependan de modificaciones en flujos de integración. También disminuye el costo futuro de mantenimiento, pruebas, diagnóstico y auditoría, al impedir que las reglas queden distribuidas en múltiples integraciones difíciles de rastrear.

### Logística

>La decisión ordena el movimiento de datos y responsabilidades: las integraciones transportan y coordinan flujos, la capa de decisiones gobierna reglas, los sistemas destino ejecutan capacidades y observabilidad registra evidencia de operación. Esto permite que el dato se mueva sin que la decisión quede escondida en el punto de transporte.

---

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 012-ARQ-TEC | Incumplimiento de autonomía de capas | Reposicionar / Controlar | La decisión controla este riesgo al definir explícitamente que la capa de Integraciones / Orquestación no debe asumir responsabilidades de Gobierno / Políticas / Decisiones ni de ejecución funcional propia del Core o satélites. |
| 020-ARQ-DTE | Incremento de deuda técnica por reglas embebidas | Reducir / Controlar | La decisión reduce este riesgo al impedir que reglas de negocio variables se implementen como lógica hardcodeada dentro de flujos de integración, scripts o transformaciones técnicas no gobernadas. |
| 021-INT-ARQ | Integraciones convertidas en dueñas de lógica de negocio | Evitar / Reposicionar | La decisión trata directamente este riesgo al establecer que las integraciones pueden consumir decisiones gobernadas, pero no definir ni administrar reglas de negocio de forma local o informal. |
| 026-AUD-TRA | Falta de trazabilidad y auditoría sobre transformaciones o decisiones | Controlar / Reducir | La decisión reduce este riesgo al exigir que cualquier regla, excepción o decisión consumida por una integración sea trazable, versionada y distinguible de una transformación técnica. |
| 028-SIS-INT | Acoplamiento entre sistemas por reglas ocultas en integraciones | Reducir / Reposicionar | La decisión reduce este riesgo al evitar que las integraciones se conviertan en el lugar donde se resuelven diferencias funcionales entre sistemas mediante lógica oculta, favoreciendo contratos claros y decisiones gobernadas. |

---

### Riesgo antes de la decisión

Antes de esta decisión, la capa de Integraciones / Orquestación podía absorber reglas de negocio por conveniencia técnica, urgencia de proyecto o disponibilidad del dato en movimiento.

Esto implicaba que una integración podía ejecutar validaciones funcionales, excepciones, reglas de aceptación, criterios de transformación semántica o decisiones operativas sin que existiera claridad sobre el dueño funcional, vigencia, aprobación o trazabilidad de dichas reglas.

El riesgo quedaba posicionado en una capa técnica que no necesariamente cuenta con gobierno funcional, controles de negocio, versionamiento de reglas o mecanismos suficientes de auditoría.

---

### Riesgo después de la decisión

Después de esta decisión, el riesgo se reposiciona fuera de la capa de Integraciones / Orquestación.

La integración conserva la responsabilidad de transporte, transformación técnica, orquestación, ruteo y consumo de decisiones, pero la responsabilidad de definir reglas de negocio variables debe quedar en Gobierno / Políticas / Decisiones o en el dominio funcional correspondiente.

El riesgo no desaparece, pero queda en un entorno más gobernable, donde puede existir ownership, aprobación, versionamiento, trazabilidad y revisión periódica.

---

### Riesgo residual

Permanece el riesgo de que, por presión de tiempos, restricciones de herramientas o decisiones de implementación, se sigan incorporando reglas de negocio dentro de integraciones.

También permanece el riesgo de que se confunda transformación técnica con decisión de negocio, especialmente en casos de mapeos, equivalencias, normalizaciones, validaciones de datos o criterios de rechazo.

Este riesgo residual debe controlarse mediante criterios explícitos de clasificación, revisión arquitectónica y trazabilidad de excepciones.

---

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Prioridad | Estado |
|---|---|---|---|---|---|
| CTRL-0021 | Definir criterio para diferenciar transformación técnica de decisión de negocio | Evitar que reglas funcionales sean justificadas como simples transformaciones técnicas dentro de integraciones. | Arquitectura Empresarial / Integraciones / Datos | Requerida | Pendiente |
| CTRL-0022 | Revisar flujos de integración para identificar reglas embebidas | Detectar integraciones que ya contengan lógica de negocio, excepciones o validaciones funcionales no gobernadas. | Arquitectura de Solución / Integraciones | Requerida | Pendiente |
| CTRL-0023 | Exigir justificación arquitectónica para cualquier regla dentro de una integración | Evitar que se incorporen nuevas reglas de negocio en integraciones sin aprobación, vigencia y trazabilidad. | Arquitectura Empresarial / Arquitectura de Solución | Requerida | Pendiente |
| CTRL-0024 | Registrar excepciones autorizadas con vigencia y plan de salida | Permitir excepciones temporales sin que se conviertan en deuda técnica permanente. | Arquitectura de Solución / Integraciones / PMO | Prioritaria | Pendiente |
| CTRL-0025 | Documentar contratos de integración separando datos, transformaciones y decisiones consumidas | Asegurar que cada contrato distinga qué dato se mueve, qué transformación técnica se aplica y qué decisión externa se consume. | Integraciones / Arquitectura de Solución | Requerida | Pendiente |
| CTRL-0026 | Registrar trazabilidad cuando una integración consuma una decisión gobernada | Permitir auditoría sobre qué decisión se consumió, bajo qué versión, en qué flujo y con qué resultado. | Observabilidad / Integraciones | Prioritaria | Pendiente |
| CTRL-0027 | Validar impacto APS antes de aprobar nuevos flujos de integración | Confirmar que la integración no esté asumiendo responsabilidades de gobierno, decisión, Core o satélites. | Arquitectura Empresarial / Arquitectura de Solución | Requerida | Pendiente |
| CTRL-0028 | Incluir revisión de lógica embebida en el checklist de diseño de integraciones | Institucionalizar la revisión para evitar que la detección dependa de criterios individuales. | Integraciones / Arquitectura de Solución | Prioritaria | Pendiente |
| CTRL-0029 | Mantener inventario de integraciones con lógica técnica, reglas consumidas y excepciones | Facilitar trazabilidad, mantenimiento, auditoría y análisis de deuda técnica. | Integraciones / Arquitectura Empresarial | Prioritaria | Pendiente |
| CTRL-0030 | Actualizar decision-log y riesgos relacionados cuando se apruebe una excepción | Mantener trazabilidad entre decisión, riesgo residual, control y artefactos asociados. | Arquitectura Empresarial / Gestión de Riesgos | Prioritaria | Pendiente |

---

## Impacto en procesos

| Proceso | Impacto | Nivel de criticidad |
|---|---|---|
| Procesos soportados por integraciones críticas | Requieren validar que las integraciones no contengan reglas de negocio no gobernadas. | Alto |
| Procesos con transformaciones entre sistemas | Requieren distinguir transformación técnica, equivalencia, normalización y decisión funcional. | Alto |
| Procesos con excepciones operativas | Requieren que las excepciones no se implementen de forma oculta en integraciones. | Alto |
| Procesos con reglas de aceptación, rechazo o validación funcional | Requieren que la decisión esté gobernada y sea consumida por la integración cuando aplique. | Alto |

---

## Impacto en aplicaciones

| Aplicación | Rol en la decisión | Impacto |
|---|---|---|
| Integraciones / APIs / ETL | Transporte, transformación técnica, ruteo y orquestación | No deben ser dueñas de reglas de negocio. Deben documentar transformaciones y decisiones consumidas. |
| Core | Ejecución transaccional / sistema central | No debe depender de decisiones funcionales ocultas en integraciones para operar correctamente. |
| Satélites | Ejecución especializada | No deben delegar reglas propias o excepciones funcionales a integraciones sin gobierno. |
| Motor de decisiones / tablas gobernadas / mecanismo equivalente | Gobierno y evaluación de reglas | Debe proveer decisiones consumibles por integraciones cuando aplique. |
| Herramientas de observabilidad | Trazabilidad y control | Deben registrar eventos relevantes sobre consumo de decisiones, errores y excepciones. |

---

## Impacto en datos

| Objeto de dato | Dueño | Impacto | Requiere gobierno |
|---|---|---|---|
| Contratos de integración | Integraciones / Arquitectura | Deben distinguir datos transportados, transformaciones técnicas y decisiones consumidas. | Sí |
| Reglas consumidas por integraciones | Negocio / Gobierno | Deben tener versión, responsable, vigencia y criterio de aplicación. | Sí |
| Datos transformados técnicamente | Integraciones / Datos | Deben conservar linaje y criterio de transformación. | Sí |
| Excepciones aplicadas en flujos | Negocio / Operación / Integraciones | Deben registrarse con responsable, vigencia y justificación. | Sí |
| Resultados de decisiones consumidas | Observabilidad / Datos | Deben poder auditarse y correlacionarse con la operación. | Sí |

---

## Impacto en integraciones

| Integración / Flujo | Origen | Destino | Impacto | Acción requerida |
|---|---|---|---|---|
| Flujos con validaciones funcionales | Sistemas origen | Sistemas destino | Pueden estar ejecutando decisiones de negocio no gobernadas. | Identificar validaciones y clasificarlas como técnicas o funcionales. |
| Flujos con mapeos o equivalencias | Sistemas origen | Core / Satélites / Datos | Pueden contener transformación semántica o decisión implícita. | Documentar criterio de mapeo y definir si requiere gobierno. |
| APIs con lógica de aceptación o rechazo | Consumidores | Servicios destino | Pueden actuar como punto de decisión funcional. | Externalizar, documentar o justificar la regla aplicada. |
| ETLs o procesos batch | Sistemas origen | Datos / Analítica / Core | Pueden ocultar reglas de transformación o depuración con impacto de negocio. | Registrar reglas, transformaciones y excepciones. |
| Flujos con excepciones temporales | Sistemas origen | Sistemas destino | Pueden institucionalizar deuda técnica. | Registrar vigencia, responsable y plan de salida. |

---

## Impacto en seguridad

La decisión requiere controlar quién puede modificar flujos de integración cuando dichos cambios puedan alterar el comportamiento de negocio.

Debe considerarse:

- Control de acceso a herramientas de integración.
- Separación entre quien desarrolla un flujo y quien aprueba una regla funcional.
- Bitácora de cambios en integraciones críticas.
- Evidencia de aprobación para reglas o excepciones.
- Restricción de cambios directos en producción.
- Auditoría de parámetros que afecten decisiones.
- Revisión de permisos sobre scripts, jobs, APIs o configuraciones de integración.
- Trazabilidad de cambios realizados por proveedor o equipos internos.

---

## Impacto en observabilidad

Debe poder observarse cuándo una integración está transportando datos, aplicando una transformación técnica, consumiendo una decisión gobernada o ejecutando una excepción autorizada.

Como mínimo, para integraciones críticas se debe registrar:

- Identificador de operación o correlación.
- Nombre del flujo o integración.
- Sistema origen.
- Sistema destino.
- Transformación técnica aplicada.
- Decisión gobernada consumida, si aplica.
- Versión de regla consumida, si aplica.
- Excepción aplicada, si existe.
- Resultado de procesamiento.
- Error o rechazo.
- Fecha y hora.
- Componente ejecutor.
- Evidencia suficiente para auditoría y diagnóstico.

---

## Consecuencias

### Consecuencias positivas

- Protege la autonomía de capas APS.
- Reduce lógica de negocio oculta en integraciones.
- Mejora trazabilidad sobre decisiones aplicadas en flujos.
- Reduce deuda técnica asociada a reglas hardcodeadas.
- Facilita auditoría y análisis de impacto.
- Clarifica responsabilidades entre integración, gobierno, Core y satélites.
- Reduce dependencia de conocimiento tácito del equipo técnico o proveedor.
- Fortalece el diseño de contratos de integración.

### Consecuencias negativas

- Requiere mayor disciplina en diseño de integraciones.
- Puede incrementar el esfuerzo inicial de documentación.
- Puede requerir rediseñar flujos existentes con lógica embebida.
- Puede generar fricción con equipos acostumbrados a resolver reglas directamente en integración.
- Puede requerir mecanismos adicionales para consumir decisiones gobernadas.
- Puede revelar deuda técnica existente que no estaba documentada.

### Trade-offs aceptados

- Se acepta mayor esfuerzo de análisis y documentación en integraciones a cambio de reducir deuda técnica y mejorar trazabilidad.
- Se acepta que algunas reglas puedan permanecer temporalmente en integraciones si tienen justificación, responsable, vigencia y plan de salida.
- Se acepta que no toda transformación requiere gobierno funcional, pero toda decisión de negocio dentro de una integración debe ser identificada, justificada y controlada.
- Se acepta separar la rapidez de implementación inmediata de la sustentabilidad arquitectónica de largo plazo.

---

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Definición de criterio transformación técnica vs decisión de negocio | Metodológica | Arquitectura Empresarial / Integraciones | Abierta |
| Inventario de integraciones con lógica embebida | Arquitectónica / Documental | Arquitectura de Solución / Integraciones | Abierta |
| Contratos de integración documentados | Técnica / Arquitectónica | Integraciones | Abierta |
| Mecanismo de decisiones gobernadas | Gobierno / Técnico | Arquitectura Empresarial / Tecnología | Abierta |
| Observabilidad de flujos críticos | Técnico / Operativo | Observabilidad / Integraciones | Abierta |
| Registro de excepciones en integraciones | Gobierno / Operativo | Arquitectura de Solución / PMO | Abierta |

---

## Criterios de aceptación

La decisión podrá considerarse aceptada cuando:

- [ ] Se apruebe el principio de que integraciones no son dueñas de lógica de negocio.
- [ ] Se defina el criterio para distinguir transformación técnica de decisión de negocio.
- [ ] Se identifiquen riesgos relacionados en el decision-log.
- [ ] Se defina cómo documentar excepciones autorizadas.
- [ ] Se establezca que las nuevas integraciones deben pasar por revisión de lógica embebida.
- [ ] Se definan controles mínimos para contratos de integración.
- [ ] Se registre este ADR en el `decision-log.xlsx`.
- [ ] Se comuniquen las implicaciones a equipos de integración, solución, negocio y proveedor.
- [ ] Se identifiquen integraciones críticas candidatas a revisión.

---

## Relación con otros artefactos

| Artefacto | Liga / Referencia | Comentarios |
|---|---|---|
| `registers/decision-log.xlsx` |  | Registrar ADR, riesgos relacionados, controles y estado. |
| Registro de riesgos |  | Relacionar riesgos 012, 020, 021, 026 y 028. |
| Diagramas APS |  | Reflejar separación entre Integraciones / Orquestación y Gobierno / Políticas / Decisiones. |
| Inventario de integraciones |  | Identificar flujos con reglas embebidas, decisiones implícitas o excepciones. |
| Contratos de integración |  | Documentar datos, transformaciones técnicas, decisiones consumidas y excepciones. |
| Documento de solución |  | Incluir principio de que integraciones no son dueñas de lógica de negocio. |

---

## Decisiones relacionadas

| ADR relacionado | Relación |
|---|---|
| ADR-0001 - Uso del RDA como fuente de verdad arquitectónica | Este ADR depende del RDA como mecanismo para documentar y gobernar decisiones. |
| ADR-0002 - Ubicación y gobierno de lógica de negocio variable | Este ADR complementa y especifica la aplicación de ADR-0002 para la capa de Integraciones / Orquestación. |
| ADR-0004 - Gobierno mínimo de tabla de equivalencias | Complementa esta decisión en casos donde las integraciones usen equivalencias, mapeos o transformaciones semánticas. |
| ADR-0008 - Trazabilidad end-to-end y auditoría de decisiones de transformación | Puede complementar los controles de observabilidad y auditoría requeridos por esta decisión. |

---

## Revisión futura

| Fecha de revisión | Motivo | Responsable |
|---|---|---|
|  | Confirmar si existen integraciones críticas con reglas de negocio embebidas. | Arquitectura de Solución / Integraciones |
|  | Revisar si las excepciones autorizadas siguen vigentes o requieren plan de salida. | Arquitectura Empresarial / PMO |
|  | Validar que los contratos de integración distingan transformación técnica, decisión consumida y excepción. | Integraciones / Arquitectura de Solución |
|  | Evaluar si se requiere mecanismo formal para consumir decisiones gobernadas desde integraciones. | Arquitectura Empresarial / Tecnología |

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