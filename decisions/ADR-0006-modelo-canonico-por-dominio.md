# ADR-0006 - Definición de modelo canónico por dominio

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
| Datos |  |  |
| Seguridad |  |  |
| Observabilidad / Operación |  |  |
| Proveedor |  |  |

---

## Contexto

Durante el análisis de riesgos se identificó que las equivalencias entre sistemas pueden resolver diferencias inmediatas de integración, migración u homologación, pero no necesariamente resuelven el problema semántico de fondo.

Una equivalencia permite decir que un valor origen corresponde, bajo cierto contexto, a un valor destino. Sin embargo, esto no significa que exista una definición canónica, gobernada y compartida del dato dentro de la organización.

Cuando no existe un modelo canónico por dominio, cada sistema puede conservar su propia interpretación de artículos, proveedores, ubicaciones, unidades de medida, estados, clasificaciones, jerarquías, atributos o reglas de codificación. Esto genera diferencias semánticas que después deben resolverse mediante mapeos, transformaciones, reglas locales o tablas de equivalencias.

El riesgo principal es que la organización confunda traducir valores entre sistemas con definir un lenguaje común de datos.

Las equivalencias pueden ser necesarias para operar, migrar o integrar, pero no deben sustituir indefinidamente la definición de valores canónicos por dominio. El modelo canónico debe establecer el significado autorizado de los datos relevantes y servir como referencia para integraciones, MDM, Core, satélites, analítica y gobierno.

Este ADR define la necesidad de construir un modelo canónico por dominio y establecer su relación con equivalencias, MDM, integraciones y procesos de transformación.

---

## Pregunta arquitectónica

¿Debe la organización limitarse a traducir valores entre sistemas o debe definir valores canónicos gobernados por dominio?

---

## Decisión

Se decide que la organización debe definir un modelo canónico por dominio para los datos críticos que requieran consistencia semántica entre sistemas, procesos e integraciones.

Las equivalencias podrán seguir utilizándose como mecanismo de transición, migración, integración u homologación, pero no deberán considerarse sustituto permanente del modelo canónico.

El modelo canónico debe definir, por dominio, los valores, atributos, estructuras, significados, reglas de interpretación, ownership y criterios de publicación que serán reconocidos como referencia común en la arquitectura To-Be.

Las equivalencias deberán alinearse progresivamente al modelo canónico cuando este exista. Cuando no exista, las equivalencias podrán servir como insumo para identificar divergencias semánticas, valores duplicados, ambigüedades y necesidades de gobierno de datos.

La decisión establece que debe distinguirse entre:

- Valor local de sistema.
- Valor equivalente para integración o migración.
- Valor canónico de dominio.
- Dato maestro publicado.
- Regla de transformación.
- Excepción temporal.

---

## Justificación

La decisión busca evitar que la arquitectura To-Be dependa indefinidamente de traducciones punto a punto entre sistemas.

Una organización puede operar durante cierto tiempo con tablas de equivalencias, mapeos y transformaciones, pero si no define un modelo canónico, cada nuevo sistema, flujo o reporte seguirá requiriendo interpretaciones particulares.

Esto genera varios problemas:

- Duplicidad de reglas de transformación.
- Inconsistencias semánticas entre sistemas.
- Dificultad para integrar nuevas aplicaciones.
- Dificultad para gobernar datos maestros.
- Mayor dependencia de conocimiento tácito.
- Aumento de deuda técnica.
- Falta de claridad entre datos locales, equivalentes y canónicos.
- Reportes con interpretaciones distintas.
- Mayor costo de mantenimiento de integraciones.
- Mayor riesgo de errores operativos.

El modelo canónico no elimina la necesidad de equivalencias, especialmente en escenarios de transición, migración o convivencia con sistemas legacy. Sin embargo, permite que dichas equivalencias tengan un punto de referencia común.

La decisión posiciona el modelo canónico como una capacidad de gobierno de datos y arquitectura, no como una simple tabla técnica.

---

## Alternativas evaluadas

| Alternativa | Descripción | Ventajas | Desventajas | Resultado |
|---|---|---|---|---|
| Mantener equivalencias como solución permanente | La organización resuelve diferencias entre sistemas mediante tablas de equivalencias y mapeos continuos. | Permite avanzar rápidamente y resolver diferencias inmediatas. | Institucionaliza deuda técnica, mantiene divergencia semántica y dificulta integración futura. | Rechazada |
| Definir modelo canónico completo antes de avanzar | No se permite integración o migración hasta tener todos los dominios canónicos definidos. | Maximiza consistencia semántica desde el inicio. | Puede bloquear el avance del proyecto y ser poco práctico ante restricciones de tiempo. | Rechazada como condición absoluta |
| Definir modelo canónico progresivo por dominio | Se definen dominios prioritarios y se construye el modelo canónico de forma incremental. | Permite balancear avance del proyecto con gobierno semántico. | Requiere priorización, ownership y disciplina de actualización. | Aceptada |
| Usar el MDM como único modelo canónico implícito | Se asume que lo que vive en MDM es automáticamente el modelo canónico. | Simplifica el discurso y centraliza datos maestros. | Confunde herramienta con definición semántica; el MDM puede almacenar datos sin que exista consenso canónico explícito. | Rechazada |
| Dejar que cada sistema conserve su modelo local | Cada sistema administra su propio significado y las integraciones traducen cuando sea necesario. | Respeta autonomía de sistemas existentes. | Multiplica transformaciones, aumenta acoplamiento y dificulta consistencia organizacional. | Rechazada |
| Usar equivalencias como insumo para descubrir el modelo canónico | Las equivalencias existentes se analizan para identificar dominios, ambigüedades y candidatos canónicos. | Aprovecha trabajo existente y permite madurez incremental. | Requiere análisis semántico y validación de dominio. | Aceptada |

---

## Impacto APS

| Capa APS | Impacto |
|---|---|
| Experiencia / Cliente | Puede verse afectada si los datos mostrados o usados por canales no tienen significado consistente, por ejemplo disponibilidad, artículos, precios, estados o clasificaciones. |
| Gobierno / Políticas / Decisiones | Debe definir ownership, reglas de aceptación, criterios semánticos, aprobación y vigencia de valores canónicos. |
| Integraciones / Orquestación | Debe alinear transformaciones, contratos y equivalencias al modelo canónico cuando exista. |
| Core | Debe consumir o publicar datos alineados al modelo canónico cuando dichos datos sean parte de dominios críticos. |
| Satélites | Deben mapear sus valores locales hacia el modelo canónico cuando participen en procesos o integraciones transversales. |
| Seguridad | Debe controlar quién puede modificar definiciones canónicas, reglas de publicación o valores maestros asociados. |
| Observabilidad & Control | Debe permitir rastrear cuándo se aplicó una transformación hacia valor canónico, con qué versión y bajo qué regla. |
| Datos & Analítica | Se posiciona como capa crítica para administrar semántica, linaje, ownership, catálogo de datos, MDM y consistencia analítica. |

---

## Análisis

### Lógica

>La decisión define un lenguaje común de datos por dominio. Esto permite distinguir entre valores locales, equivalencias técnicas, valores canónicos y datos maestros publicados, evitando que cada sistema interprete los datos de forma aislada.

### Económica

>La decisión reduce el costo futuro de integración, mantenimiento, conciliación, corrección de datos y soporte. También reduce deuda técnica asociada a mapeos punto a punto y evita que cada nuevo flujo requiera resolver nuevamente diferencias semánticas ya conocidas.

### Logística

>La decisión ordena el movimiento de datos entre sistemas al establecer un punto común de interpretación. Los datos pueden seguir moviéndose desde múltiples orígenes, pero deben converger hacia definiciones canónicas por dominio cuando el proceso, integración o análisis lo requiera.

---

## Relación con riesgos

### Riesgos tratados por esta decisión

| ID Riesgo | Riesgo | Tipo de tratamiento | Explicación |
|---|---|---|---|
| 018-DAT-SEM | Divergencia semántica entre sistemas | Reducir / Controlar | La decisión reduce este riesgo al establecer que los dominios críticos deben contar con valores y definiciones canónicas, evitando que cada sistema conserve interpretaciones incompatibles sin referencia común. |
| 028-SIS-INT | Acoplamiento entre sistemas por reglas ocultas en integraciones | Reducir / Reposicionar | La decisión reduce este riesgo al evitar que las integraciones resuelvan diferencias semánticas de forma local y repetida, reposicionando la definición de significado en un modelo canónico gobernado. |
| 030-CAN-ARQ | Falta de modelo canónico por dominio | Controlar / Reducir | La decisión trata directamente este riesgo al establecer la necesidad de definir un modelo canónico progresivo por dominio, priorizando datos críticos para procesos, integraciones, MDM y analítica. |
| 034-TRA-MET | Confusión entre migración técnica y transformación gobernada | Controlar | La decisión controla este riesgo al distinguir entre mover datos técnicamente, mapear valores para transición y transformar datos hacia una definición canónica gobernada. |

---

### Riesgo antes de la decisión

Antes de esta decisión, las diferencias semánticas entre sistemas podían resolverse mediante equivalencias, mapeos, reglas locales o transformaciones técnicas sin que existiera una definición común por dominio.

Esto implicaba que cada integración, migración, reporte o proceso podía interpretar los datos según el sistema origen, el sistema destino o el criterio del equipo implementador.

El riesgo quedaba posicionado en integraciones, ETLs, scripts, reportes, tablas de equivalencias o conocimiento tácito, en lugar de estar gobernado por un modelo de datos común.

---

### Riesgo después de la decisión

Después de esta decisión, el riesgo se reposiciona hacia una capacidad gobernada de datos y arquitectura.

Las equivalencias y transformaciones siguen existiendo, pero deben alinearse al modelo canónico cuando este exista, o alimentar su definición cuando todavía no exista.

El riesgo no desaparece, pero queda en un entorno donde puede ser administrado mediante ownership, definiciones de dominio, reglas de publicación, linaje, versionamiento y validación semántica.

---

### Riesgo residual

Permanece el riesgo de que el modelo canónico no se defina con suficiente profundidad, no tenga owners claros o no sea adoptado por integraciones, MDM, Core, satélites o analítica.

También permanece el riesgo de que, por urgencia del proyecto, se sigan creando equivalencias o mapeos locales antes de validar si ya existe una definición canónica aplicable.

Adicionalmente, puede persistir el riesgo de que algunos dominios tengan ambigüedades de negocio que requieran decisiones funcionales, no solo técnicas.

---

## Controles requeridos

| Control ID | Control requerido | Objetivo | Responsable | Prioridad | Estado |
|---|---|---|---|---|---|
| CTRL-0055 | Identificar dominios de datos críticos para modelo canónico | Priorizar los dominios que requieren consistencia semántica por impacto en procesos, integraciones, MDM o analítica. | Arquitectura Empresarial / Gobierno de Datos / Negocio | Requerida | Pendiente |
| CTRL-0056 | Definir ownership por dominio canónico | Asegurar que cada dominio tenga responsable funcional o de datos para validar significado, reglas y cambios. | Gobierno de Datos / Negocio | Requerida | Pendiente |
| CTRL-0057 | Documentar definición canónica por dominio | Establecer significado, estructura, atributos, valores válidos, reglas de interpretación y criterios de uso. | Gobierno de Datos / Arquitectura de Datos | Requerida | Pendiente |
| CTRL-0058 | Relacionar equivalencias existentes con valores canónicos | Evitar que equivalencias operen indefinidamente sin alineación a una definición semántica común. | Datos / Integraciones / MDM | Prioritaria | Pendiente |
| CTRL-0059 | Clasificar valores locales, equivalentes, canónicos y maestros | Evitar confusión entre valores propios de sistema, mapeos técnicos, definiciones canónicas y golden record. | Gobierno de Datos / MDM / Arquitectura Empresarial | Requerida | Pendiente |
| CTRL-0060 | Definir reglas de versionamiento del modelo canónico | Permitir evolución controlada de definiciones, valores, atributos y estructuras por dominio. | Gobierno de Datos / Arquitectura de Datos | Prioritaria | Pendiente |
| CTRL-0061 | Validar contratos de integración contra modelo canónico | Asegurar que integraciones críticas utilicen definiciones canónicas cuando aplique. | Arquitectura de Solución / Integraciones / Datos | Prioritaria | Pendiente |
| CTRL-0062 | Definir criterio para crear nuevas equivalencias cuando ya exista valor canónico | Evitar duplicidad, divergencia semántica y creación innecesaria de mapeos locales. | Gobierno de Datos / Integraciones | Requerida | Pendiente |
| CTRL-0063 | Registrar linaje entre valor local, equivalencia, valor canónico y dato maestro | Permitir auditoría y reconstrucción del significado aplicado a través de sistemas. | Datos / Observabilidad / MDM | Prioritaria | Pendiente |
| CTRL-0064 | Incorporar el modelo canónico en revisiones arquitectónicas | Asegurar que nuevas soluciones, integraciones o migraciones no introduzcan divergencia semántica sin decisión explícita. | Arquitectura Empresarial / Arquitectura de Solución | Prioritaria | Pendiente |

---

## Impacto en procesos

| Proceso | Impacto | Nivel de criticidad |
|---|---|---|
| Procesos que consumen datos maestros | Requieren que los datos críticos tengan definición canónica o relación clara con el golden record. | Alto |
| Procesos de integración entre sistemas | Requieren mapear valores locales contra modelo canónico cuando aplique. | Alto |
| Procesos de migración de datos | Requieren distinguir migración técnica, equivalencia transitoria y transformación hacia valor canónico. | Alto |
| Procesos analíticos y de reporting | Requieren consistencia semántica para evitar métricas o interpretaciones contradictorias. | Alto |
| Procesos de gobierno de datos | Requieren owners, stewards, definiciones, reglas de cambio y versionamiento. | Alto |
| Procesos operativos con catálogos o clasificaciones | Requieren criterios comunes para interpretar estados, tipos, jerarquías y atributos. | Medio / Alto |

---

## Impacto en aplicaciones

| Aplicación | Rol en la decisión | Impacto |
|---|---|---|
| MDM | Administración de datos maestros | Debe alinearse al modelo canónico y publicar datos maestros consistentes con definiciones de dominio. |
| Core | Sistema central / ejecución operativa | Debe consumir, exponer o transformar datos críticos conforme al modelo canónico cuando aplique. |
| Satélites | Sistemas especializados | Deben mapear sus valores locales hacia valores canónicos si participan en procesos transversales. |
| Integraciones / APIs / ETL | Transporte y transformación | Deben usar el modelo canónico como referencia para contratos, mapeos y transformaciones. |
| Tabla de equivalencias | Mecanismo de transición o alineación | Debe relacionar valores locales con valores canónicos cuando existan. |
| Analítica / Reporting | Consumo y explotación de datos | Debe utilizar definiciones canónicas para evitar reportes inconsistentes. |
| Catálogo de datos | Gobierno y documentación | Debe registrar dominios, definiciones, owners, linaje y relaciones canónicas. |

---

## Impacto en datos

| Objeto de dato | Dueño | Impacto | Requiere gobierno |
|---|---|---|---|
| Dominio canónico | Gobierno de Datos / Data Owner | Debe tener definición, owner, reglas y criterios de cambio. | Sí |
| Valor canónico | Data Owner / Steward | Debe definirse como referencia común para sistemas y procesos. | Sí |
| Valor local de sistema | Sistema dueño | Debe mapearse hacia valor canónico cuando participe en procesos transversales. | Sí |
| Equivalencias | Gobierno de Datos / Integraciones | Deben relacionarse con valores canónicos cuando existan. | Sí |
| Golden record | MDM / Data Owner | Debe alinearse con el modelo canónico y reglas de publicación. | Sí |
| Atributos críticos | Data Owner / Dominio | Deben tener significado, formato, reglas de validez y linaje. | Sí |
| Catálogos y clasificaciones | Negocio / Gobierno de Datos | Deben normalizarse o mapearse a dominios canónicos. | Sí |

---

## Impacto en integraciones

| Integración / Flujo | Origen | Destino | Impacto | Acción requerida |
|---|---|---|---|---|
| Flujos entre sistemas con modelos locales distintos | Sistemas origen | Sistemas destino | Requieren referencia canónica para evitar transformaciones punto a punto inconsistentes. | Mapear valores locales contra modelo canónico. |
| APIs que exponen datos críticos | Core / Satélites / MDM | Consumidores | Deben exponer contratos alineados a definiciones canónicas cuando aplique. | Revisar contratos y nomenclaturas. |
| ETLs de migración | Sistemas legacy | Sistemas To-Be / MDM | Deben distinguir equivalencias transitorias de valores canónicos. | Registrar transformación y criterio semántico. |
| Flujos de analítica | Sistemas operativos | Data lake / BI / reporting | Deben usar definiciones comunes para evitar divergencias de métricas o catálogos. | Alinear modelo de datos y glosario. |
| Flujos con equivalencias | Sistemas origen | Sistemas destino | Deben dejar de depender únicamente de traducciones locales si ya existe valor canónico. | Relacionar equivalencia con dominio canónico. |

---

## Impacto en seguridad

La decisión requiere controlar quién puede crear, modificar, aprobar o retirar definiciones canónicas.

Debe considerarse:

- Control de acceso al catálogo de datos o repositorio de definiciones canónicas.
- Separación entre quien propone un cambio y quien lo aprueba.
- Restricción sobre cambios a valores canónicos usados por procesos críticos.
- Evidencia de aprobación por dominio.
- Bitácora de cambios sobre dominios, atributos y valores canónicos.
- Control de permisos sobre MDM, equivalencias e integraciones que consumen modelo canónico.
- Revisión de cambios que impacten procesos financieros, fiscales, logísticos, operativos o regulatorios.
- Auditoría de modificaciones realizadas por proveedor o equipos técnicos.

---

## Impacto en observabilidad

Debe poder observarse cuándo un dato es transformado, consumido o publicado conforme al modelo canónico.

Como mínimo, para dominios críticos se debe registrar:

- Identificador de dominio.
- Identificador de valor local.
- Identificador de valor canónico.
- Sistema origen.
- Sistema destino.
- Regla de transformación o equivalencia aplicada.
- Versión del modelo canónico.
- Fecha de vigencia.
- Resultado de validación.
- Error, rechazo o excepción.
- Usuario, sistema o proceso que aplicó la transformación.
- Correlación con integración, carga, proceso o transacción.
- Evidencia de aprobación para cambios al modelo canónico.

---

## Consecuencias

### Consecuencias positivas

- Reduce divergencia semántica entre sistemas.
- Disminuye dependencia de equivalencias punto a punto.
- Fortalece gobierno de datos por dominio.
- Mejora consistencia entre MDM, Core, satélites, integraciones y analítica.
- Facilita incorporación de nuevos sistemas.
- Reduce deuda técnica en integraciones.
- Mejora trazabilidad entre valores locales, equivalencias y golden record.
- Mejora calidad de reportes y analítica.
- Permite distinguir entre solución transitoria y arquitectura objetivo de datos.

### Consecuencias negativas

- Requiere esfuerzo de definición y gobierno por dominio.
- Puede exponer conflictos semánticos entre áreas o unidades de negocio.
- Puede requerir ajustes en contratos de integración existentes.
- Puede retrasar decisiones si no hay owners definidos.
- Puede requerir mantenimiento continuo del catálogo canónico.
- Puede generar fricción con sistemas que tienen modelos locales muy arraigados.

### Trade-offs aceptados

- Se acepta mayor esfuerzo inicial de gobierno de datos a cambio de reducir deuda técnica y divergencia semántica.
- Se acepta construir el modelo canónico de forma progresiva por dominio, sin bloquear todos los avances del proyecto.
- Se acepta que las equivalencias sigan existiendo como mecanismo transitorio o complementario, siempre que se alineen al modelo canónico cuando aplique.
- Se acepta que no todos los datos requieren canonización inmediata, pero los dominios críticos deben priorizarse explícitamente.

---

## Dependencias

| Dependencia | Tipo | Responsable | Estado |
|---|---|---|---|
| Identificación de dominios críticos | Datos / Negocio | Gobierno de Datos / Negocio | Abierta |
| Definición de owners y stewards | Gobierno | Gobierno de Datos / Negocio | Abierta |
| Catálogo de datos o repositorio de definiciones | Datos / Tecnología | Gobierno de Datos / Tecnología | Abierta |
| Inventario de equivalencias existentes | Datos / Integraciones | Integraciones / Datos | Abierta |
| Relación con MDM y golden record | Arquitectónica / Datos | MDM / Arquitectura Empresarial | Abierta |
| Revisión de contratos de integración | Técnica / Arquitectónica | Integraciones / Arquitectura de Solución | Abierta |
| Definición de reglas de versionamiento | Gobierno / Datos | Gobierno de Datos | Abierta |
| Observabilidad de transformaciones canónicas | Técnico / Operativo | Observabilidad / Datos | Abierta |

---

## Criterios de aceptación

La decisión podrá considerarse aceptada cuando:

- [ ] Se apruebe que debe existir un modelo canónico progresivo por dominio.
- [ ] Se identifiquen dominios críticos iniciales.
- [ ] Se definan owners o stewards para los dominios priorizados.
- [ ] Se establezca la diferencia entre valor local, equivalencia, valor canónico y golden record.
- [ ] Se defina cómo se relacionarán equivalencias existentes con valores canónicos.
- [ ] Se establezcan reglas mínimas de versionamiento y vigencia del modelo canónico.
- [ ] Se identifiquen integraciones o procesos que requieren alineación canónica.
- [ ] Se registre este ADR en el `decision-log.xlsx`.
- [ ] Se relacionen los riesgos correspondientes en el registro de riesgos.
- [ ] Se comuniquen las implicaciones a equipos de datos, integración, solución, negocio y proveedor.

---

## Relación con otros artefactos

| Artefacto | Liga / Referencia | Comentarios |
|---|---|---|
| `registers/decision-log.xlsx` |  | Registrar ADR, riesgos relacionados, controles y estado. |
| Registro de riesgos |  | Relacionar riesgos 018, 028, 030 y 034. |
| Catálogo de datos |  | Documentar dominios, definiciones, owners, atributos y valores canónicos. |
| MDM |  | Alinear golden record y reglas de publicación con modelo canónico. |
| Tabla de equivalencias |  | Relacionar valores locales y equivalentes contra valores canónicos cuando existan. |
| Inventario de integraciones |  | Identificar flujos que requieren alineación semántica o mapeo canónico. |
| Diagramas APS |  | Reflejar relación entre Datos & Analítica, Gobierno, Integraciones y Core. |
| Documento de solución |  | Incluir criterio de uso de modelo canónico por dominio. |

---

## Decisiones relacionadas

| ADR relacionado | Relación |
|---|---|
| ADR-0001 - Uso del RDA como fuente de verdad arquitectónica | Este ADR depende del RDA como mecanismo para documentar y gobernar decisiones. |
| ADR-0002 - Ubicación y gobierno de lógica de negocio variable | Complementa la definición de reglas y criterios gobernados por dominio. |
| ADR-0003 - Integraciones no dueñas de lógica de negocio | Refuerza que las integraciones no deben resolver significado de datos de forma local y oculta. |
| ADR-0004 - Gobierno mínimo de tabla de equivalencias | Este ADR complementa el uso de equivalencias como mecanismo gobernado, no como sustituto del modelo canónico. |
| ADR-0005 - Relación entre equivalencias, MDM y golden record | Complementa la separación entre equivalencias, modelo canónico y golden record. |
| ADR-0007 - Segregación entre captura, validación, decisión, aprobación y persistencia | Complementa esta decisión desde la perspectiva de gobierno y control del dato. |

---

## Revisión futura

| Fecha de revisión | Motivo | Responsable |
|---|---|---|
|  | Confirmar dominios críticos iniciales para canonización. | Arquitectura Empresarial / Gobierno de Datos |
|  | Revisar si las equivalencias existentes pueden agruparse por dominio. | Datos / Integraciones |
|  | Validar relación entre modelo canónico y MDM. | MDM / Gobierno de Datos |
|  | Confirmar si contratos de integración usan valores locales o canónicos. | Integraciones / Arquitectura de Solución |
|  | Evaluar avance del catálogo de datos y ownership por dominio. | Gobierno de Datos / Negocio |

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