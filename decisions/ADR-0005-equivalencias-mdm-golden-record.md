# ADR-0005 - Relación entre equivalencias, MDM y golden record

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
| Datos | René Laso de la Vega Baéz | Arquitectura de Datos |
| Seguridad |  |  |
| Observabilidad / Operación |  |  |
| Proveedor |  |  |

---

## Contexto

Durante el análisis de riesgos se identificó que las equivalencias entre valores de distintos sistemas pueden llegar a impactar datos maestros y, potencialmente, el golden record administrado por el MDM.

Una equivalencia puede surgir como mecanismo de migración, integración, homologación, transición o corrección operativa. Sin embargo, una equivalencia técnica no necesariamente representa una verdad de negocio ni un valor maestro aprobado.

El riesgo aparece cuando una tabla de equivalencias, mapeo, transformación o regla de homologación se utiliza para actualizar, corregir, enriquecer o sustituir datos maestros sin pasar por un proceso formal de validación, ownership, aprobación y publicación.

El MDM debe proteger la integridad, autoridad y trazabilidad de los datos maestros. Por lo tanto, no debe ser actualizado automáticamente por equivalencias que no cuenten con gobierno explícito, validación de dominio y control de linaje.

Este ADR define la relación entre equivalencias, MDM y golden record, aclarando que las equivalencias pueden apoyar procesos de transformación o integración, pero no deben sustituir el gobierno formal de datos maestros.

---

## Pregunta arquitectónica

¿Puede una equivalencia técnica actualizar directamente el golden record del MDM?

---

## Decisión

Se decide que una equivalencia técnica no debe actualizar directamente el golden record del MDM.

Toda equivalencia que pueda impactar datos maestros debe pasar por validación, ownership de dominio, aprobación funcional o de datos, trazabilidad de origen, clasificación de impacto y control de publicación antes de modificar un registro maestro.

La tabla de equivalencias puede utilizarse como insumo para proponer, transformar, validar o enriquecer datos, pero no debe operar como autoridad final sobre el dato maestro.

El MDM conserva la responsabilidad de administrar el golden record. Las equivalencias pueden apoyar el proceso, pero no reemplazan las reglas de gobierno, survivorship, stewardship, aprobación, linaje y publicación del MDM.

Cuando una equivalencia represente una posible corrección, homologación o promoción hacia dato maestro, deberá tratarse como una propuesta sujeta a revisión, no como una actualización automática.

---

## Justificación

La decisión busca proteger la integridad del golden record y evitar que una estructura técnica de equivalencias se convierta en una fuente informal de verdad.

El golden record representa la versión gobernada, confiable y autorizada de un dato maestro. Si una equivalencia técnica puede modificarlo sin validación formal, se corre el riesgo de contaminar datos críticos con valores incompletos, ambiguos, transitorios, incorrectos o válidos únicamente para un contexto específico.

Una equivalencia puede ser correcta para una integración, una migración o una unidad de negocio, pero no necesariamente correcta para todo el dominio maestro.

Esta decisión permite separar responsabilidades:

- La tabla de equivalencias propone o transforma valores bajo un contexto.
- El MDM valida, consolida y publica datos maestros.
- Gobierno de Datos define ownership, reglas, linaje y aprobación.
- Integraciones consumen o transportan datos, pero no promueven valores al golden record sin gobierno.
- Observabilidad registra evidencia de cambios, validaciones y publicaciones.
- Seguridad controla quién puede modificar equivalencias, aprobar datos maestros o publicar cambios.

La decisión no impide que las equivalencias alimenten procesos del MDM. Lo que impide es que se conviertan en un mecanismo automático, no gobernado y opaco para modificar la fuente de verdad.

---

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Permitir actualización automática del MDM desde equivalencias | Las equivalencias aprobadas técnicamente actualizan directamente datos maestros. | Acelera cargas, migraciones o correcciones masivas. | Riesgo alto de contaminar el golden record, perder ownership y publicar valores no validados. | Rechazada |
| Usar equivalencias solo como referencia técnica | Las equivalencias se usan únicamente para integración o migración, sin tocar MDM. | Reduce riesgo sobre golden record. | Puede limitar aprovechamiento de equivalencias válidas para mejorar calidad de datos maestros. | Parcialmente aceptada |
| Tratar equivalencias como propuestas hacia MDM | Las equivalencias pueden sugerir cambios, pero requieren validación, aprobación y publicación gobernada. | Protege golden record y permite aprovechar equivalencias útiles. | Requiere flujo de revisión, ownership y trazabilidad. | Aceptada |
| Delegar al proveedor la decisión de promoción al MDM | El proveedor define qué equivalencias son válidas para datos maestros. | Puede acelerar la ejecución del proyecto. | Transfiere autoridad de datos a un actor no dueño del dominio y aumenta dependencia externa. | Rechazada |
| Definir reglas de promoción por dominio | Cada dominio define qué equivalencias pueden promoverse y bajo qué condiciones. | Permite gobierno diferenciado por criticidad y dominio. | Requiere madurez de gobierno de datos y participación de owners. | Aceptada |

---

## Impacto APS

| Capa APS | Impacto |
|---|---|
| Experiencia / Cliente | Puede verse afectada si el golden record contiene datos incorrectos que impactan información visible, disponibilidad, precios, surtido, identificación de productos o proveedores. |
| Gobierno / Políticas / Decisiones | Debe definir reglas de promoción, aprobación, ownership, stewardship y criterios de aceptación para cambios a datos maestros. |
| Integraciones / Orquestación | Puede transportar equivalencias o resultados de validación, pero no debe promover valores al MDM sin decisión gobernada. |
| Core | Puede consumir datos maestros publicados por MDM, por lo que depende de la calidad y autoridad del golden record. |
| Satélites | Pueden consumir datos maestros o equivalencias, pero deben distinguir entre dato maestro aprobado y valor transformado para contexto específico. |
| Seguridad | Debe controlar quién puede crear equivalencias, aprobar cambios, modificar datos maestros y publicar valores al golden record. |
| Observabilidad & Control | Debe registrar propuestas, validaciones, aprobaciones, rechazos, publicaciones y cambios al golden record. |
| Datos & Analítica | Debe asegurar linaje, calidad, semántica, ownership y relación entre valores origen, equivalencias, valores canónicos y golden record. |

---

## Análisis

### Lógica

>La decisión separa la función de equivalencia técnica de la función de autoridad maestra. Una equivalencia puede sugerir una relación entre valores, pero solo el MDM, bajo gobierno de datos, puede publicar o consolidar el golden record.

### Económica

>La decisión reduce el costo futuro de corrección, reproceso, auditoría e incidentes asociados a datos maestros contaminados. También evita que una solución rápida de homologación genere deuda técnica y operativa en procesos críticos que dependen del golden record.

### Logística

>La decisión ordena el flujo de datos desde sistemas origen, equivalencias, validación de dominio, aprobación y publicación en MDM. Esto permite mover datos hacia el golden record sin perder control sobre quién propuso el cambio, quién lo validó, bajo qué regla se aprobó y cuándo se publicó.

---

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 017-MDM-GOB | Propagación de valores no gobernados al golden record | Controlar / Reducir | La decisión reduce este riesgo al prohibir que equivalencias técnicas actualicen automáticamente el golden record sin validación, ownership, aprobación y trazabilidad. |
| 018-DAT-SEM | Divergencia semántica entre sistemas | Reducir / Controlar | La decisión controla este riesgo al distinguir entre valor equivalente para un contexto, valor canónico y dato maestro publicado, evitando que equivalencias contextuales se traten como verdad universal. |
| 023-GOB-OWN | Aprobaciones implícitas sin ownership claro | Controlar | La decisión controla este riesgo al exigir que toda promoción hacia MDM tenga owner, steward o responsable de dominio, evitando que la aprobación quede implícita en una tabla técnica o en una carga automatizada. |
| 024-PRV-GOB | Proveedor tomando decisiones de datos sin autoridad de dominio | Controlar / Reducir | La decisión reduce este riesgo al establecer que el proveedor puede apoyar en cargas, análisis o implementación, pero no tiene autoridad para aprobar cambios al golden record sin validación del dueño de datos. |
| 028-SIS-INT | Acoplamiento entre sistemas por reglas ocultas en integraciones | Reducir / Reposicionar | La decisión reduce este riesgo al impedir que integraciones o mapeos ocultos actualicen datos maestros, reposicionando la decisión de promoción en MDM y gobierno de datos. |
| 030-CAN-ARQ | Falta de modelo canónico por dominio | Reposicionar / Controlar | La decisión controla este riesgo al establecer que las equivalencias no sustituyen el modelo canónico; pueden servir como insumo para identificarlo, pero la definición canónica debe gobernarse por dominio. |
| 033-MDM-CON | Pérdida de control sobre datos maestros | Controlar / Reducir | La decisión reduce este riesgo al mantener al MDM como autoridad sobre el golden record y exigir controles antes de publicar cambios derivados de equivalencias. |
| 034-TRA-MET | Confusión entre migración técnica y transformación gobernada | Controlar | La decisión controla este riesgo al distinguir entre usar equivalencias para mover datos durante una migración y promover valores al MDM como datos maestros gobernados. |

---

### Riesgo antes de la decisión

Antes de esta decisión, una equivalencia técnica podía interpretarse como suficiente para actualizar, corregir o publicar un valor en el MDM.

Esto implicaba que una tabla de equivalencias, un mapeo de integración, una carga de migración o una transformación técnica podía terminar afectando el golden record sin claridad sobre la autoridad de dominio, la validación de negocio, la vigencia, el linaje o la aprobación correspondiente.

El riesgo quedaba posicionado en mecanismos técnicos de transformación, migración o integración, donde podía modificarse el dato maestro sin el nivel adecuado de gobierno.

---

### Riesgo después de la decisión

Después de esta decisión, las equivalencias quedan posicionadas como insumos o propuestas, no como autoridad final sobre datos maestros.

El MDM conserva la responsabilidad de publicar el golden record, mientras que Gobierno de Datos y los dueños de dominio definen las reglas para aprobar, rechazar o escalar cambios derivados de equivalencias.

El riesgo no desaparece, pero se reposiciona en un entorno donde puede existir stewardship, aprobación, linaje, trazabilidad, versionamiento y control de publicación.

---

### Riesgo residual

Permanece el riesgo de que, por presión de migración, tiempos de proyecto o dependencia del proveedor, se intenten cargar equivalencias directamente al MDM sin seguir el flujo de gobierno.

También permanece el riesgo de que no existan owners claramente definidos para algunos dominios, lo que puede retrasar aprobaciones o generar decisiones provisionales.

Adicionalmente, puede persistir el riesgo de que algunas equivalencias sean promovidas como datos maestros sin haberse resuelto completamente la ambigüedad semántica.

---

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Prioridad | Estado |
|---|---|---|---|---|---|
| CTRL-0043 | Definir regla explícita de no actualización automática del golden record desde equivalencias | Proteger el MDM de cambios derivados de mapeos técnicos no aprobados. | Arquitectura Empresarial / MDM / Gobierno de Datos | Requerida | Pendiente |
| CTRL-0044 | Definir flujo de promoción de equivalencia a dato maestro | Establecer cómo una equivalencia puede convertirse en propuesta, validarse, aprobarse y publicarse en MDM. | Gobierno de Datos / MDM / Negocio | Requerida | Pendiente |
| CTRL-0045 | Identificar data owner o steward por dominio impactado | Evitar aprobaciones implícitas y asegurar autoridad funcional sobre cambios al golden record. | Gobierno de Datos / Negocio | Requerida | Pendiente |
| CTRL-0046 | Clasificar equivalencias según impacto en datos maestros | Determinar qué equivalencias pueden usarse solo para integración y cuáles requieren revisión de MDM. | Datos / MDM / Arquitectura de Solución | Requerida | Pendiente |
| CTRL-0047 | Registrar linaje entre valor origen, equivalencia, valor destino y golden record | Permitir auditoría y reconstrucción de cómo un valor fue propuesto, validado y publicado. | Datos / Observabilidad / MDM | Prioritaria | Pendiente |
| CTRL-0048 | Exigir evidencia de aprobación antes de publicar cambios al MDM | Evitar que cambios de datos maestros se publiquen sin autorización del dominio correspondiente. | Gobierno de Datos / MDM | Requerida | Pendiente |
| CTRL-0049 | Registrar rechazos o ambigüedades de equivalencias propuestas | Evitar que equivalencias no aprobadas reaparezcan como decisiones técnicas posteriores. | Gobierno de Datos / Datos / Negocio | Prioritaria | Pendiente |
| CTRL-0050 | Separar permisos de captura, validación, aprobación y publicación | Mantener segregación de funciones en el ciclo de vida del dato maestro. | Seguridad / MDM / Gobierno de Datos | Requerida | Pendiente |
| CTRL-0051 | Definir reglas de excepción para cargas masivas o migración | Permitir escenarios transitorios sin perder trazabilidad, aprobación y plan de regularización. | Arquitectura Empresarial / MDM / PMO | Prioritaria | Pendiente |
| CTRL-0052 | Revisar integraciones o ETLs que actualizan MDM desde equivalencias | Detectar flujos que puedan estar modificando datos maestros sin pasar por gobierno formal. | Arquitectura de Solución / Integraciones / MDM | Requerida | Pendiente |
| CTRL-0053 | Mantener bitácora de publicaciones al golden record | Registrar quién publicó qué dato, cuándo, bajo qué aprobación, con qué origen y con qué versión. | MDM / Observabilidad / Tecnología | Requerida | Pendiente |
| CTRL-0054 | Relacionar equivalencias con modelo canónico cuando exista | Evitar que la equivalencia sustituya indefinidamente la definición canónica de dominio. | Gobierno de Datos / Arquitectura Empresarial | Prioritaria | Pendiente |

---

## Impacto en procesos

| Proceso | Impacto | Nivel de criticidad |
|---|---|---|
| Procesos de administración de datos maestros | Requieren validar que cambios derivados de equivalencias pasen por ownership, aprobación y publicación gobernada. | Alto |
| Procesos de migración de datos | Requieren distinguir carga técnica de migración contra publicación formal en golden record. | Alto |
| Procesos de integración hacia MDM | Requieren controles para impedir actualización automática desde equivalencias no aprobadas. | Alto |
| Procesos de gobierno de datos | Requieren definir owners, stewards, criterios de aprobación y tratamiento de ambigüedad. | Alto |
| Procesos operativos consumidores de datos maestros | Dependen de la confiabilidad del golden record publicado por MDM. | Alto |
| Procesos de auditoría y soporte | Requieren reconstruir el origen, validación y aprobación de cambios al golden record. | Alto |

---

## Impacto en aplicaciones

| Aplicación | Rol en la decisión | Impacto |
|---|---|---|
| MDM | Autoridad de datos maestros / golden record | Conserva la responsabilidad de validar, consolidar y publicar datos maestros. No debe aceptar actualizaciones automáticas desde equivalencias no aprobadas. |
| Tabla de equivalencias / repositorio gobernado | Insumo de transformación o propuesta | Puede sugerir o soportar cambios, pero no es autoridad final sobre el dato maestro. |
| Integraciones / APIs / ETL | Transporte y carga de datos | No deben promover valores al MDM sin pasar por reglas de gobierno, validación y aprobación. |
| Core | Consumidor de datos maestros | Debe consumir datos maestros publicados y confiables, no valores equivalentes no gobernados. |
| Satélites | Consumidores o productores de datos | Deben distinguir entre valores locales, equivalencias y datos maestros publicados. |
| Herramientas de observabilidad | Auditoría y diagnóstico | Deben registrar propuestas, aprobaciones, publicaciones, errores y rechazos asociados a cambios en MDM. |

---

## Impacto en datos

| Objeto de dato | Dueño | Impacto | Requiere gobierno |
|---|---|---|---|
| Golden record | MDM / Data Owner | No debe modificarse por equivalencias técnicas sin validación y aprobación. | Sí |
| Equivalencias | Gobierno de Datos / Dominio funcional | Pueden servir como insumo o propuesta, pero requieren clasificación de impacto. | Sí |
| Valores origen | Sistema dueño / Dominio | Deben conservar linaje hacia el sistema fuente. | Sí |
| Valores destino | Sistema destino / Dominio | Deben validarse antes de promoverse a dato maestro. | Sí |
| Valores canónicos | Gobierno de Datos / MDM | Deben distinguirse de equivalencias contextuales o transitorias. | Sí |
| Propuestas de cambio a MDM | Data Steward / Data Owner | Deben tener estado, justificación, aprobación o rechazo. | Sí |
| Bitácora de publicación | MDM / Observabilidad | Debe registrar cambios al golden record y su trazabilidad. | Sí |

---

## Impacto en integraciones

| Integración / Flujo | Origen | Destino | Impacto | Acción requerida |
|---|---|---|---|---|
| Flujos que cargan datos a MDM | Sistemas origen / equivalencias | MDM | Pueden modificar datos maestros si no existen controles. | Validar que pasen por flujo de aprobación y publicación gobernada. |
| ETLs de migración | Sistemas legacy | MDM / staging | Pueden confundir carga técnica con publicación del golden record. | Separar staging, validación, aprobación y publicación. |
| APIs de actualización de datos maestros | Consumidores / aplicaciones | MDM | Pueden permitir cambios sin ownership si no se controlan permisos. | Revisar controles de acceso y autorización. |
| Flujos de homologación | Sistemas origen | Tabla de equivalencias / MDM | Pueden proponer equivalencias útiles para MDM. | Clasificar impacto y enviar a revisión de dominio. |
| Flujos con correcciones masivas | Archivos / cargas / proveedor | MDM | Pueden introducir errores a escala. | Requerir evidencia, aprobación, bitácora y rollback. |

---

## Impacto en seguridad

La decisión requiere controlar estrictamente los permisos sobre equivalencias y MDM.

Debe considerarse:

- Separación entre quien captura una equivalencia y quien aprueba un cambio al MDM.
- Restricción de actualización directa al golden record.
- Control de acceso a APIs, ETLs, cargas o pantallas de mantenimiento de datos maestros.
- Evidencia de aprobación antes de publicar.
- Bitácora de cambios obligatoria.
- Control de cambios masivos.
- Revisión de accesos de proveedor.
- Auditoría de usuarios o sistemas con capacidad de modificación.
- Segregación entre ambientes de staging, validación y producción.
- Mecanismos de rollback o reversión controlada.

---

## Impacto en observabilidad

Debe poder observarse el ciclo completo desde equivalencia propuesta hasta publicación, rechazo o descarte.

Como mínimo, se debe registrar:

- Identificador de equivalencia.
- Identificador de propuesta hacia MDM.
- Dominio o atributo afectado.
- Valor origen.
- Valor destino.
- Valor canónico relacionado, si existe.
- Golden record impactado.
- Sistema fuente.
- Usuario o sistema que propuso el cambio.
- Data owner o steward responsable.
- Estado de validación.
- Aprobación o rechazo.
- Fecha de publicación.
- Versión publicada.
- Error, rechazo o excepción.
- Correlación con carga, integración o proceso.
- Evidencia para auditoría y diagnóstico.

---

## Consecuencias

### Consecuencias positivas

- Protege la integridad del golden record.
- Reduce riesgo de contaminación de datos maestros.
- Clarifica que las equivalencias no son fuente final de verdad.
- Mejora trazabilidad entre valores origen, equivalencias y datos maestros.
- Fortalece ownership y stewardship de datos.
- Reduce dependencia del proveedor para decisiones de datos.
- Permite aprovechar equivalencias como insumo sin perder gobierno.
- Facilita auditoría de cambios al MDM.
- Distingue migración técnica de publicación gobernada.

### Consecuencias negativas

- Requiere flujo adicional de validación y aprobación.
- Puede ralentizar cargas masivas o migraciones si no hay owners definidos.
- Puede exponer falta de gobierno de datos por dominio.
- Puede requerir ajustes en integraciones existentes hacia MDM.
- Puede generar trabajo adicional para documentar linaje y evidencias.
- Puede provocar fricción con equipos que esperaban automatización directa.

### Trade-offs aceptados

- Se acepta mayor control y posible esfuerzo adicional a cambio de proteger el golden record.
- Se acepta que las equivalencias puedan alimentar propuestas hacia MDM, pero no publicar cambios automáticamente.
- Se acepta que algunos escenarios de migración requieran mecanismos transitorios, siempre que tengan trazabilidad, aprobación y plan de regularización.
- Se acepta que la velocidad de carga no debe prevalecer sobre la autoridad y confiabilidad del dato maestro.

---

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Definición de owners y stewards por dominio | Gobierno / Datos | Gobierno de Datos / Negocio | Abierta |
| Flujo de promoción de equivalencias hacia MDM | Proceso / Gobierno | MDM / Gobierno de Datos | Abierta |
| Reglas de publicación al golden record | Datos / Arquitectónica | MDM / Arquitectura Empresarial | Abierta |
| Modelo de staging, validación y publicación | Técnico / Datos | MDM / Tecnología | Abierta |
| Bitácora y trazabilidad de cambios al MDM | Observabilidad / Auditoría | Observabilidad / MDM | Abierta |
| Revisión de integraciones que actualizan MDM | Técnica / Arquitectónica | Integraciones / Arquitectura de Solución | Abierta |
| Modelo canónico por dominio | Datos / Gobierno | Gobierno de Datos / Arquitectura Empresarial | Abierta |
| Matriz de accesos y segregación de funciones | Seguridad / Gobierno | Seguridad / MDM | Abierta |

---

## Criterios de aceptación

La decisión podrá considerarse aceptada cuando:

- [ ] Se apruebe que equivalencias técnicas no pueden actualizar automáticamente el golden record.
- [ ] Se defina el flujo de promoción de equivalencia a dato maestro.
- [ ] Se identifiquen owners o stewards para dominios impactados.
- [ ] Se establezca cómo se validan, aprueban, rechazan o publican propuestas hacia MDM.
- [ ] Se definan controles para cargas masivas o migraciones.
- [ ] Se identifiquen integraciones, ETLs o APIs que actualizan MDM.
- [ ] Se definan controles de seguridad y segregación de funciones.
- [ ] Se establezca bitácora mínima de publicaciones al golden record.
- [ ] Se registre este ADR en el `decision-log.xlsx`.
- [ ] Se relacionen los riesgos correspondientes en el registro de riesgos.
- [ ] Se comuniquen las implicaciones a equipos de MDM, datos, integración, negocio y proveedor.

---

## Relación con otros artefactos

| Artefacto | Liga / Referencia | Comentarios |
|---|---|---|
| `registers/decision-log.xlsx` |  | Registrar ADR, riesgos relacionados, controles y estado. |
| Registro de riesgos |  | Relacionar riesgos 017, 018, 023, 024, 028, 030, 033 y 034. |
| Catálogo de datos / MDM |  | Documentar dominios, owners, valores canónicos y reglas de publicación. |
| Tabla de equivalencias |  | Clasificar equivalencias que pueden impactar datos maestros. |
| Inventario de integraciones |  | Identificar flujos que cargan, proponen o modifican datos en MDM. |
| Matriz de accesos |  | Controlar permisos sobre equivalencias, staging y MDM. |
| Bitácora de cambios |  | Registrar propuestas, aprobaciones, rechazos y publicaciones al golden record. |
| Documento de solución |  | Incluir separación entre equivalencias técnicas y datos maestros gobernados. |

---

## Decisiones relacionadas

| ADR relacionado | Relación |
|---|---|
| ADR-0001 - Uso del RDA como fuente de verdad arquitectónica | Este ADR depende del RDA como mecanismo para documentar y gobernar decisiones. |
| ADR-0002 - Ubicación y gobierno de lógica de negocio variable | Complementa la separación entre reglas variables, datos maestros y mecanismos técnicos. |
| ADR-0003 - Integraciones no dueñas de lógica de negocio | Refuerza que integraciones no deben promover valores al MDM mediante reglas ocultas. |
| ADR-0004 - Gobierno mínimo de tabla de equivalencias | Este ADR especializa la relación entre equivalencias y MDM. |
| ADR-0006 - Definición de modelo canónico por dominio | Complementa esta decisión al establecer la autoridad semántica deseada para largo plazo. |
| ADR-0007 - Segregación entre captura, validación, decisión, aprobación y persistencia | Complementa esta decisión desde la perspectiva de control interno y seguridad. |

---

## Revisión futura

| Fecha de revisión | Motivo | Responsable |
|---|---|---|
|  | Confirmar si existen cargas actuales al MDM basadas en equivalencias. | MDM / Integraciones |
|  | Revisar si los dominios impactados cuentan con owner o steward definido. | Gobierno de Datos / Negocio |
|  | Validar si existen equivalencias propuestas para golden record. | MDM / Gobierno de Datos |
|  | Revisar si hay flujos de migración que requieran excepción transitoria. | Arquitectura Empresarial / PMO |
|  | Evaluar avance en definición de modelo canónico por dominio. | Arquitectura Empresarial / Gobierno de Datos |

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