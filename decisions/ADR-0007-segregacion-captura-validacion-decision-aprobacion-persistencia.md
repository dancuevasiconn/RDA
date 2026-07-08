# ADR-0007 - Segregación entre captura, validación, decisión, aprobación y persistencia

## Estado

Propuesta

## Fecha

2026-07-08

## Responsables

| Rol | Nombre | Área |
|---|---|---|
| Arquitectura Empresarial | Roberto Mancha | Arquitectura Empresarial y de TI |
| Arquitectura de Solución | Daniel Cuevas | Proyecto ADN |
| Negocio |  |  |
| Integraciones |  |  |
| Datos | René Laso de la Vega Baéz | Arquitecto de Datos |
| Seguridad |  |  |
| Observabilidad / Operación |  |  |
| Proveedor |  |  |

---

## Contexto

Durante el análisis de riesgos se identificó que distintos actores, sistemas, proveedores o componentes técnicos pueden participar en el ciclo de vida de un dato, regla, equivalencia o decisión operativa.

Sin embargo, no todos esos actores deben tener la misma autoridad.

Una persona, sistema o proveedor puede capturar información, proponer una equivalencia, ejecutar una carga, validar una estructura técnica o transportar datos entre sistemas. Pero eso no significa que tenga autoridad para aprobar, publicar, persistir o convertir dicha información en verdad operativa o dato maestro.

El riesgo aparece cuando la arquitectura no separa claramente las responsabilidades de captura, validación, decisión, aprobación y persistencia. En ese escenario, un componente técnico, una integración, una tabla, un proveedor o un usuario operativo puede terminar ejerciendo autoridad de negocio o de datos sin que exista gobierno explícito.

Esto puede generar aprobaciones implícitas, pérdida de ownership, contaminación de datos maestros, cambios sin trazabilidad, violaciones de segregación de funciones, dependencia del proveedor y materialización del comportamiento conocido como “closet de blancos”.

Este ADR define la necesidad de separar formalmente las etapas de captura, validación, decisión, aprobación y persistencia dentro de los flujos críticos de datos, reglas, equivalencias y datos maestros.

---

## Pregunta arquitectónica

¿Debe separarse la captura de datos de la decisión, aprobación y persistencia en MDM, sistemas destino o mecanismos gobernados?

---

## Decisión

Se decide que la arquitectura debe separar explícitamente las responsabilidades de captura, validación, decisión, aprobación y persistencia.

Un actor, sistema, proveedor o componente técnico puede capturar, proponer, transportar o validar información, pero no por ello obtiene autoridad para aprobarla, publicarla, persistirla como verdad operativa o modificar datos maestros.

La captura de información debe tratarse como una entrada o propuesta.

La validación técnica debe confirmar formato, estructura, obligatoriedad, consistencia básica o cumplimiento de contrato.

La decisión debe evaluar reglas de negocio, criterios de dominio, políticas o excepciones.

La aprobación debe quedar en manos del owner, steward, área funcional, gobierno de datos o autoridad correspondiente.

La persistencia o publicación debe ocurrir únicamente después de cumplir los criterios definidos para el dominio, sistema o proceso afectado.

Esta separación debe aplicarse especialmente en flujos relacionados con MDM, golden record, equivalencias, datos maestros, catálogos, reglas de negocio, excepciones, integraciones críticas y procesos con impacto operativo, financiero, fiscal, logístico o regulatorio.

---

## Justificación

La decisión busca evitar que la arquitectura mezcle responsabilidades que deben mantenerse separadas para preservar gobierno, trazabilidad, control interno y calidad de datos.

Cuando una misma persona, sistema, proveedor o componente puede capturar, validar, decidir, aprobar y persistir información, se incrementa el riesgo de errores, abuso de permisos, cambios no autorizados, decisiones implícitas y pérdida de control sobre datos críticos.

Separar estas responsabilidades permite:

- Evitar aprobaciones implícitas.
- Reducir dependencia del proveedor.
- Proteger el golden record.
- Fortalecer ownership de dominio.
- Asegurar segregación de funciones.
- Mejorar trazabilidad de decisiones.
- Diferenciar validación técnica de aprobación funcional.
- Controlar cambios sobre datos maestros.
- Evitar que una tabla o integración se convierta en autoridad informal.
- Reducir el riesgo de que componentes críticos funcionen como “closet de blancos”.

Esta decisión no busca burocratizar todos los flujos. Busca establecer que, cuando exista impacto arquitectónico, operativo o de datos críticos, la autoridad debe estar posicionada en la capa correcta y con los responsables adecuados.

---

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Permitir que quien captura también apruebe y publique | El mismo actor o sistema realiza todo el ciclo de vida del dato o regla. | Reduce pasos y acelera operación. | Genera riesgo alto de errores, cambios no autorizados, falta de control y pérdida de segregación de funciones. | Rechazada |
| Delegar aprobación al componente técnico | La integración, tabla, script o sistema define si un dato o regla puede persistirse. | Puede automatizar decisiones y simplificar implementación. | Mezcla validación técnica con decisión de negocio o datos, creando autoridad informal. | Rechazada |
| Delegar aprobación al proveedor | El proveedor decide qué datos, equivalencias o reglas son válidas para persistencia. | Puede acelerar el proyecto y reducir carga interna. | Transfiere autoridad de dominio a un tercero sin ownership organizacional. | Rechazada |
| Separar captura, validación, decisión, aprobación y persistencia | Cada etapa tiene responsabilidad y control diferenciado. | Fortalece gobierno, trazabilidad, seguridad y calidad. | Requiere definición de roles, controles, flujos y responsables. | Aceptada |
| Aplicar segregación solo a dominios críticos | La separación se aplica prioritariamente a datos, reglas o procesos de mayor impacto. | Permite enfoque pragmático y progresivo. | Requiere criterio claro de criticidad. | Aceptada |
| Automatizar aprobación bajo reglas gobernadas | Algunas decisiones pueden automatizarse si las reglas están aprobadas y versionadas. | Permite eficiencia sin perder gobierno. | Requiere reglas claras, trazabilidad y monitoreo. | Parcialmente aceptada |

---

## Impacto APS

| Capa APS | Impacto |
|---|---|
| Experiencia / Cliente | Puede verse afectada si datos, reglas o aprobaciones incorrectas impactan información visible, disponibilidad, precios, surtido, estados o atención al usuario. |
| Gobierno / Políticas / Decisiones | Debe definir roles, criterios de aprobación, ownership, flujos de decisión y reglas de autorización por dominio o proceso. |
| Integraciones / Orquestación | Puede transportar propuestas, validaciones o decisiones aprobadas, pero no debe asumir autoridad de aprobación o persistencia sin gobierno. |
| Core | Debe persistir o ejecutar cambios únicamente cuando provengan de flujos autorizados y con controles definidos. |
| Satélites | Deben distinguir entre captura operativa, propuesta de cambio, validación local y publicación autorizada. |
| Seguridad | Debe controlar permisos y segregación de funciones entre captura, validación, aprobación y publicación. |
| Observabilidad & Control | Debe registrar el ciclo de vida completo: propuesta, validación, decisión, aprobación, rechazo, persistencia y publicación. |
| Datos & Analítica | Debe asegurar linaje, ownership, estados, vigencia, calidad y trazabilidad de datos o reglas a lo largo del ciclo de vida. |

---

## Análisis

### Lógica

>La decisión separa las etapas del ciclo de vida de un dato, regla o equivalencia. Esto evita que la captura de información se confunda con autorización, que la validación técnica se confunda con decisión de negocio y que la persistencia se ejecute sin aprobación correspondiente.

### Económica

>La decisión reduce el costo futuro de correcciones, reprocesos, auditorías, incidentes y pérdida de control sobre datos críticos. También reduce la deuda técnica y operativa generada por flujos donde la autoridad queda implícita o concentrada en actores técnicos o externos.

### Logística

>La decisión ordena el movimiento de datos y responsabilidades: un dato puede ser capturado por un actor, validado por un componente, decidido por una regla gobernada, aprobado por un owner y persistido por un sistema autorizado. Cada paso tiene responsable, estado y evidencia.

---

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 023-GOB-OWN | Aprobaciones implícitas sin ownership claro | Controlar / Reducir | La decisión reduce este riesgo al exigir que toda aprobación relevante tenga owner, steward, área funcional o autoridad explícita, evitando que la aprobación quede implícita en una carga, tabla, integración o decisión técnica. |
| 024-PRV-GOB | Proveedor tomando decisiones de datos sin autoridad de dominio | Controlar / Reducir | La decisión controla este riesgo al separar la capacidad del proveedor para ejecutar, configurar o cargar información de la autoridad para aprobar datos, reglas o equivalencias de negocio. |
| 032-GOB-SEG | Falta de segregación entre captura, decisión y persistencia | Controlar | La decisión trata directamente este riesgo al definir que captura, validación, decisión, aprobación y persistencia deben ser responsabilidades separadas, especialmente en dominios críticos. |
| 033-MDM-CON | Pérdida de control sobre datos maestros | Controlar / Reducir | La decisión reduce este riesgo al impedir que datos capturados o equivalencias propuestas se publiquen en MDM o golden record sin validación, aprobación y trazabilidad de dominio. |

---

### Riesgo antes de la decisión

Antes de esta decisión, un mismo actor, sistema, integración, tabla, proveedor o proceso podía concentrar varias responsabilidades del ciclo de vida del dato o regla.

Esto implicaba que la captura de información podía interpretarse como aprobación, que una validación técnica podía confundirse con decisión de negocio, o que una integración podía persistir cambios en sistemas destino sin evidencia clara de autoridad funcional.

El riesgo quedaba posicionado en flujos operativos o técnicos sin suficiente separación de responsabilidades, incrementando la probabilidad de cambios no autorizados, errores, pérdida de ownership y falta de trazabilidad.

---

### Riesgo después de la decisión

Después de esta decisión, el riesgo se reposiciona hacia un modelo gobernado de responsabilidades.

Cada etapa del ciclo de vida debe tener responsable, criterio, estado y evidencia. La captura se trata como propuesta, la validación técnica como control estructural, la decisión como evaluación de reglas, la aprobación como acto de autoridad de dominio y la persistencia como publicación controlada.

El riesgo no desaparece, pero queda en un entorno donde puede ser administrado mediante segregación de funciones, control de accesos, bitácora, trazabilidad y revisión periódica.

---

### Riesgo residual

Permanece el riesgo de que, por presión operativa, urgencia de proyecto o falta de owners definidos, algunos flujos concentren responsabilidades temporalmente.

También permanece el riesgo de que se automatice una decisión sin que las reglas hayan sido formalmente aprobadas, versionadas o auditadas.

Adicionalmente, puede persistir el riesgo de que algunos equipos confundan validación técnica con aprobación funcional, especialmente en cargas masivas, migraciones, equivalencias o integraciones hacia MDM.

---

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Prioridad | Estado |
|---|---|---|---|---|---|
| CTRL-0065 | Definir matriz RACI para captura, validación, decisión, aprobación y persistencia | Asegurar claridad sobre quién participa, quién decide, quién aprueba y quién ejecuta cada etapa. | Arquitectura Empresarial / Gobierno / PMO | Requerida | Pendiente |
| CTRL-0066 | Identificar dominios y procesos donde aplica segregación obligatoria | Priorizar datos, reglas, equivalencias y procesos críticos donde la falta de segregación representa mayor riesgo. | Arquitectura Empresarial / Negocio / Gobierno de Datos | Requerida | Pendiente |
| CTRL-0067 | Separar permisos de captura, aprobación y publicación | Evitar que un mismo usuario, sistema o proveedor pueda capturar y publicar cambios críticos sin control. | Seguridad / Tecnología / Gobierno de Datos | Requerida | Pendiente |
| CTRL-0068 | Definir flujo de aprobación para datos, reglas y equivalencias críticas | Asegurar que toda persistencia relevante cuente con autoridad funcional o de datos antes de publicarse. | Gobierno de Datos / Negocio / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0069 | Registrar bitácora de estados del ciclo de vida | Permitir auditoría desde captura/propuesta hasta validación, aprobación, rechazo, persistencia o retiro. | Observabilidad / Tecnología / Datos | Requerida | Pendiente |
| CTRL-0070 | Prohibir publicación directa a golden record sin aprobación de dominio | Proteger MDM y datos maestros contra cambios no validados o no autorizados. | MDM / Gobierno de Datos / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0071 | Definir criterios para aprobación automática bajo reglas gobernadas | Permitir automatización controlada cuando existan reglas aprobadas, versionadas y auditables. | Gobierno / Arquitectura Empresarial / Tecnología | Prioritaria | Pendiente |
| CTRL-0072 | Revisar accesos de proveedor sobre datos, reglas, equivalencias y MDM | Evitar que el proveedor tenga autoridad de aprobación o publicación sin mandato explícito. | Seguridad / PMO / Gobierno de Datos | Prioritaria | Pendiente |
| CTRL-0073 | Definir flujo de excepción para casos urgentes | Permitir atención operativa sin romper trazabilidad, aprobación posterior o regularización. | PMO / Operación / Arquitectura de Solución | Prioritaria | Pendiente |
| CTRL-0074 | Validar segregación de funciones en revisiones arquitectónicas | Asegurar que nuevas soluciones no concentren indebidamente captura, decisión, aprobación y persistencia. | Arquitectura Empresarial / Arquitectura de Solución | Prioritaria | Pendiente |

---

## Impacto en procesos

| Proceso | Impacto | Nivel de criticidad |
|---|---|---|
| Procesos de administración de datos maestros | Requieren separación entre captura, validación, aprobación y publicación al golden record. | Alto |
| Procesos de equivalencias | Requieren distinguir propuesta de equivalencia, validación semántica, aprobación y publicación. | Alto |
| Procesos de migración de datos | Requieren diferenciar carga técnica, validación, aceptación funcional y persistencia definitiva. | Alto |
| Procesos de integración hacia sistemas críticos | Requieren controles para evitar persistencia automática sin aprobación o autoridad definida. | Alto |
| Procesos de gobierno de reglas | Requieren separar propuesta de regla, validación, aprobación, versionamiento y activación. | Alto |
| Procesos operativos con excepciones | Requieren flujo de excepción, responsable, vigencia y regularización posterior. | Medio / Alto |

---

## Impacto en aplicaciones

| Aplicación | Rol en la decisión | Impacto |
|---|---|---|
| MDM | Persistencia y publicación de datos maestros | Debe publicar cambios únicamente después de validación y aprobación de dominio. |
| Tabla de equivalencias / repositorio gobernado | Captura o propuesta de transformación | No debe convertir propuestas en decisiones aprobadas sin flujo de gobierno. |
| Integraciones / APIs / ETL | Transporte, validación técnica y carga | No deben asumir aprobación funcional ni publicar cambios críticos sin control. |
| Core | Sistema central / ejecución operativa | Debe recibir datos o decisiones ya aprobadas cuando exista impacto funcional o maestro. |
| Satélites | Captura o ejecución especializada | Deben distinguir operación local de aprobación transversal o publicación autorizada. |
| Herramientas de workflow / aprobación | Gobierno operativo | Pueden soportar flujos de aprobación, estados, evidencias y escalaciones. |
| Herramientas de observabilidad | Auditoría y diagnóstico | Deben registrar estados, aprobaciones, rechazos, publicaciones y excepciones. |

---

## Impacto en datos

| Objeto de dato | Dueño | Impacto | Requiere gobierno |
|---|---|---|---|
| Datos capturados | Sistema origen / Usuario capturista | Deben considerarse propuestas o entradas hasta ser validados y aprobados. | Sí |
| Datos validados técnicamente | Tecnología / Integraciones | Deben distinguirse de datos aprobados funcionalmente. | Sí |
| Decisiones de negocio | Negocio / Gobierno | Deben tener reglas, owners, versión y evidencia de aplicación. | Sí |
| Aprobaciones | Owner / Steward / Área funcional | Deben registrarse con usuario, rol, fecha, motivo y alcance. | Sí |
| Datos persistidos | Sistema destino / Dominio | Deben publicarse solo bajo criterios autorizados. | Sí |
| Golden record | MDM / Data Owner | Debe protegerse contra publicaciones no aprobadas. | Sí |
| Excepciones | Operación / Negocio / Gobierno | Deben tener responsable, vigencia, justificación y regularización. | Sí |
| Bitácora de ciclo de vida | Observabilidad / Tecnología | Debe permitir reconstruir cada etapa del flujo. | Sí |

---

## Impacto en integraciones

| Integración / Flujo | Origen | Destino | Impacto | Acción requerida |
|---|---|---|---|---|
| Flujos que capturan o reciben datos | Sistemas origen / usuarios / proveedor | Staging / integración / destino | Deben tratar los datos como entrada o propuesta, no como dato aprobado. | Registrar origen, responsable y estado inicial. |
| Flujos que validan estructura | Integraciones / ETL | Sistemas destino | Pueden validar formato, pero no aprobar significado de negocio. | Separar validación técnica de aprobación funcional. |
| Flujos que publican hacia MDM | Staging / equivalencias / sistemas origen | MDM | Pueden impactar golden record. | Exigir aprobación de dominio antes de publicación. |
| Flujos con aprobación automatizada | Motor de reglas / workflow | Sistemas destino | Pueden ser válidos si las reglas están gobernadas. | Registrar regla, versión, resultado y evidencia. |
| Flujos de excepción | Operación / PMO / soporte | Sistemas críticos | Pueden saltar controles normales por urgencia. | Registrar justificación, responsable, vigencia y regularización. |

---

## Impacto en seguridad

La decisión requiere controles de seguridad y segregación de funciones sobre todo el ciclo de vida del dato, regla o equivalencia.

Debe considerarse:

- Separación de permisos entre captura, validación, aprobación y publicación.
- Restricción de cambios directos en producción.
- Control de accesos para proveedor.
- Revisión periódica de usuarios con permisos privilegiados.
- Evidencia de aprobación antes de persistir datos críticos.
- Control de cambios masivos.
- Bitácora inalterable o auditable.
- Reglas de excepción con aprobación posterior.
- Matriz de segregación de funciones.
- Revisión de accesos sobre MDM, tablas de equivalencias, integraciones, workflows y sistemas destino.
- Alertas sobre cambios realizados fuera del flujo autorizado.

---

## Impacto en observabilidad

Debe poder observarse el ciclo completo de captura, validación, decisión, aprobación y persistencia.

Como mínimo, para flujos críticos se debe registrar:

- Identificador de operación.
- Sistema o usuario que capturó la información.
- Fecha y hora de captura.
- Resultado de validación técnica.
- Regla o criterio de decisión aplicado.
- Versión de regla, si aplica.
- Usuario, rol o sistema que aprobó.
- Fecha y hora de aprobación.
- Resultado de aprobación o rechazo.
- Sistema donde se persistió el dato.
- Fecha y hora de persistencia.
- Excepción aplicada, si existe.
- Justificación de excepción.
- Usuario o sistema que ejecutó publicación.
- Correlación con integración, carga, proceso o transacción.
- Evidencia suficiente para auditoría y diagnóstico.

---

## Consecuencias

### Consecuencias positivas

- Fortalece segregación de funciones.
- Reduce aprobaciones implícitas.
- Protege datos maestros y golden record.
- Reduce dependencia del proveedor como autoridad informal.
- Mejora trazabilidad del ciclo de vida de datos y reglas.
- Evita que integraciones o tablas se conviertan en mecanismos informales de decisión.
- Mejora control interno y auditabilidad.
- Clarifica responsabilidades entre negocio, datos, tecnología y operación.
- Reduce riesgo de errores masivos por cambios no autorizados.

### Consecuencias negativas

- Requiere mayor disciplina operativa y documental.
- Puede aumentar tiempos de aprobación en dominios críticos.
- Requiere definición clara de owners y stewards.
- Puede requerir cambios en permisos, workflows, integraciones o procesos existentes.
- Puede generar resistencia en equipos acostumbrados a operar con permisos amplios.
- Puede revelar falta de gobierno o roles no definidos.

### Trade-offs aceptados

- Se acepta mayor control y posible fricción operativa a cambio de proteger datos, reglas y decisiones críticas.
- Se acepta aplicar segregación de forma priorizada en dominios críticos, sin necesariamente bloquear procesos de bajo impacto.
- Se acepta que algunas aprobaciones puedan automatizarse solo si las reglas están gobernadas, versionadas y auditadas.
- Se acepta que las excepciones urgentes puedan existir, pero deben quedar documentadas, justificadas y regularizadas.

---

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Definición de dominios críticos | Datos / Negocio | Gobierno de Datos / Negocio | Abierta |
| Definición de owners y stewards | Gobierno | Gobierno de Datos / Negocio | Abierta |
| Matriz RACI del ciclo de vida | Gobierno / Proceso | Arquitectura Empresarial / PMO | Abierta |
| Matriz de accesos y segregación de funciones | Seguridad / Gobierno | Seguridad / Tecnología | Abierta |
| Flujo de aprobación para datos y reglas críticas | Proceso / Gobierno | Gobierno de Datos / Negocio | Abierta |
| Bitácora de estados y evidencias | Observabilidad / Tecnología | Observabilidad / Tecnología | Abierta |
| Revisión de permisos de proveedor | Seguridad / PMO | Seguridad / PMO | Abierta |
| Revisión de integraciones que publican datos críticos | Técnica / Arquitectónica | Integraciones / Arquitectura de Solución | Abierta |

---

## Criterios de aceptación

La decisión podrá considerarse aceptada cuando:

- [ ] Se apruebe la separación entre captura, validación, decisión, aprobación y persistencia.
- [ ] Se definan dominios o procesos donde la segregación es obligatoria.
- [ ] Se identifiquen owners o stewards para los dominios críticos.
- [ ] Se defina matriz RACI para el ciclo de vida de datos, reglas o equivalencias críticas.
- [ ] Se establezcan controles de acceso y segregación de funciones.
- [ ] Se defina bitácora mínima de estados, aprobaciones y publicaciones.
- [ ] Se establezca criterio para aprobación automática bajo reglas gobernadas.
- [ ] Se defina flujo de excepción y regularización.
- [ ] Se registre este ADR en el `decision-log.xlsx`.
- [ ] Se relacionen los riesgos correspondientes en el registro de riesgos.
- [ ] Se comuniquen las implicaciones a equipos de datos, integración, seguridad, operación, negocio y proveedor.

---

## Relación con otros artefactos

| Artefacto | Liga / Referencia | Comentarios |
|---|---|---|
| `registers/decision-log.xlsx` |  | Registrar ADR, riesgos relacionados, controles y estado. |
| Registro de riesgos |  | Relacionar riesgos 023, 024, 032 y 033. |
| Matriz RACI |  | Documentar responsabilidades por captura, validación, decisión, aprobación y persistencia. |
| Matriz de accesos |  | Controlar permisos por rol, sistema, proveedor y ambiente. |
| MDM |  | Aplicar segregación antes de publicar cambios al golden record. |
| Tabla de equivalencias |  | Separar propuesta de equivalencia, validación, aprobación y publicación. |
| Inventario de integraciones |  | Identificar flujos que capturan, validan o publican datos críticos. |
| Diagramas APS |  | Reflejar separación entre Gobierno, Datos, Integraciones, Seguridad y Core. |
| Documento de solución |  | Incluir criterio de segregación de responsabilidades y flujos de aprobación. |

---

## Decisiones relacionadas

| ADR relacionado | Relación |
|---|---|
| ADR-0001 - Uso del RDA como fuente de verdad arquitectónica | Este ADR depende del RDA como mecanismo para documentar y gobernar decisiones. |
| ADR-0002 - Ubicación y gobierno de lógica de negocio variable | Complementa la definición de ownership, aprobación y gobierno de reglas variables. |
| ADR-0003 - Integraciones no dueñas de lógica de negocio | Refuerza que integraciones no deben aprobar ni decidir reglas de negocio de forma informal. |
| ADR-0004 - Gobierno mínimo de tabla de equivalencias | Complementa la separación de responsabilidades en la creación, validación y aprobación de equivalencias. |
| ADR-0005 - Relación entre equivalencias, MDM y golden record | Refuerza que equivalencias no deben actualizar golden record sin aprobación de dominio. |
| ADR-0006 - Definición de modelo canónico por dominio | Complementa esta decisión al requerir owners y gobierno sobre definiciones canónicas. |

---

## Revisión futura

| Fecha de revisión | Motivo | Responsable |
|---|---|---|
|  | Confirmar dominios críticos donde aplica segregación obligatoria. | Arquitectura Empresarial / Gobierno de Datos |
|  | Revisar si existen flujos que concentran captura, aprobación y persistencia. | Arquitectura de Solución / Integraciones |
|  | Validar permisos actuales de proveedor sobre MDM, equivalencias o integraciones críticas. | Seguridad / PMO |
|  | Revisar si existen aprobaciones automáticas sin reglas gobernadas. | Gobierno / Tecnología |
|  | Evaluar avance de matriz RACI y controles de acceso. | Arquitectura Empresarial / Seguridad |

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