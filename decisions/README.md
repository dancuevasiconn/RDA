# Decisions - Registros de Decisiones Arquitectónicas

## Propósito

Esta carpeta contiene los ADRs del RDA.

Un ADR, o Architecture Decision Record, es el registro individual de una decisión arquitectónica relevante. Cada ADR debe documentar una decisión específica, su contexto, alternativas evaluadas, justificación, consecuencias, riesgos relacionados y controles requeridos.

El objetivo no es documentar opiniones técnicas, sino dejar evidencia clara de decisiones que afectan la arquitectura, los procesos, las aplicaciones, los datos, las integraciones, la seguridad, la operación, la observabilidad, el gobierno o la sustentabilidad de la solución.

---

## Regla principal

Una decisión arquitectónica debe documentarse cuando existía más de un camino posible y la elección de uno de esos caminos genera consecuencias relevantes.

El ADR debe responder:

```text
¿Qué se decidió?
¿Por qué se decidió?
¿Qué alternativas se evaluaron?
¿Qué riesgos trata, reposiciona o introduce?
¿Qué consecuencias se aceptan?
¿Qué controles o acciones quedan pendientes?
¿Qué artefactos deben actualizarse?
```
--- 

# Qué debe considerarse una decisión arquitectónica

Debe crearse un ADR cuando la decisión:

Define dónde vive una responsabilidad.
Define qué sistema, capa o componente será dueño de una capacidad.
Cambia la arquitectura To-Be.
Afecta procesos de negocio.
Afecta integraciones críticas.
Cambia ownership de datos.
Mueve lógica entre Core, satélites, integraciones o gobierno.
Define una herramienta, plataforma, patrón o estándar.
Afecta seguridad, accesos, auditoría o segregación de funciones.
Afecta observabilidad, monitoreo, trazabilidad o soporte operativo.
Genera, reduce, acepta o reposiciona riesgo.
Crea o reduce deuda técnica.
Tiene impacto económico, operativo o de sustentabilidad.
Puede ser cuestionada posteriormente y requiere evidencia.

---

# Qué no necesariamente requiere un ADR

No todo requiere una decisión arquitectónica formal.

Normalmente no requiere ADR:

Cambios menores de redacción.
Ajustes visuales en diagramas.
Correcciones de formato.
Tareas operativas sin impacto arquitectónico.
Decisiones reversibles de bajo impacto.
Actividades ya cubiertas por una decisión existente.
Ejecuciones técnicas que no cambian responsabilidades, riesgos, controles o estructura.

## Si existe duda, se debe preguntar:

¿Esta decisión cambia dónde vive una responsabilidad, cómo se mueve información, quién gobierna algo o qué riesgo queda aceptado?  

---

# Convención de nombres

Cada ADR debe tener un identificador único y consecutivo.

Formato del archivo:

ADR-0000-titulo-corto-en-kebab-case.md

Ejemplos:

ADR-0001-ubicacion-reglas-negocio.md
ADR-0002-estrategia-reemplazo-rods.md
ADR-0003-blue-yonder-como-intermediario.md
ADR-0004-baseline-observabilidad.md

## Formato del título dentro del archivo:

# ADR-0001 - Ubicación de reglas de negocio variables

El número del ADR no debe reutilizarse, aunque la decisión sea rechazada o reemplazada.

---

# Estados Permitidos

Todo ADR debe tener un estado.

| Estado       | Descripción                                                                       |
| ------------ | --------------------------------------------------------------------------------- |
| Proposed     | La decisión está en análisis o pendiente de aprobación.                           |
| Accepted     | La decisión fue aprobada y debe considerarse vigente.                             |
| Rejected     | La decisión fue evaluada y descartada.                                            |
| Superseded   | La decisión fue reemplazada por otra decisión posterior.                          |
| Deprecated   | La decisión sigue documentada, pero ya no se recomienda su uso.                   |
| Under Review | La decisión está siendo revisada por cambio de contexto, alcance o restricciones. |

---

# Ciclo de vida de una decisión

El flujo normal de una decisión arquitectónica es:

1. Se identifica una necesidad, riesgo, problema o tensión arquitectónica.
2. Se determina si requiere una decisión arquitectónica.
3. Se formula la pregunta arquitectónica.
4. Se crea un ADR en estado Proposed.
5. Se documenta el contexto.
6. Se identifican alternativas.
7. Se analiza impacto APS.
8. Se analiza impacto LEM.
9. Se relacionan riesgos tratados, reposicionados, introducidos o residuales.
10. Se define una decisión recomendada.
11. Se revisa con los responsables correspondientes.
12. Se acepta, rechaza o deja en revisión.
13. Se actualiza el decision log.
14. Se definen controles y acciones requeridas.
15. Se actualizan artefactos relacionados.
16. Se revisa nuevamente si cambia el contexto.

---

# Flujo visual resumido

Necesidad / Riesgo / Problema
        ↓
Pregunta arquitectónica
        ↓
Alternativas evaluadas
        ↓
Análisis APS + LEM
        ↓
Decisión propuesta
        ↓
Revisión con responsables
        ↓
Accepted / Rejected / Under Review
        ↓
Actualización de decision log
        ↓
Controles, riesgos y artefactos relacionados
        ↓
Revisión futura

---

# Pregunta arquitectónica

Todo ADR debe incluir una pregunta arquitectónica clara.

La pregunta arquitectónica debe formular el punto de decisión, no el problema completo.

Ejemplo débil:
Definir proceso de recepción.
Revisar integración con Blue Yonder.

Ejemplo correcto
¿Dónde debe residir la lógica para determinar si un proveedor puede operar bajo un esquema de recepción confiable?
¿Debe Blue Yonder funcionar como intermediario para ciertos flujos entre MFCS y SIMAS, en lugar de integrar ambos sistemas directamente?

Alternativas

Cada ADR debe documentar las alternativas razonables que fueron consideradas.

No basta con documentar la opción elegida. Se debe explicar qué otros caminos existían y por qué fueron descartados.

| Alternativa                           | Descripción                                                   | Ventajas                             | Desventajas                         | Resultado |
| ------------------------------------- | ------------------------------------------------------------- | ------------------------------------ | ----------------------------------- | --------- |
| Implementar en Core                   | La lógica se implementa directamente en el sistema principal. | Centraliza ejecución.                | Puede rigidizar cambios de negocio. | Rechazada |
| Implementar en Integración            | La lógica se evalúa durante el flujo de datos.                | Puede parecer rápido de implementar. | Mezcla transporte con decisión.     | Rechazada |
| Implementar en Gobierno de Decisiones | La regla se externaliza en una capa gobernada.                | Mejora trazabilidad y control.       | Requiere ownership y controles.     | Aceptada  |

---

# Relación con riesgos

Las decisiones arquitectónicas no necesariamente eliminan riesgos.

Una decisión puede:

Reducir un riesgo.
Reposicionar un riesgo.
Controlar un riesgo.
Aceptar un riesgo residual.
Transferir un riesgo.
Introducir un nuevo riesgo.
Escalar un riesgo.

El ADR debe evitar frases absolutas como:
Esta decisión elimina el riesgo.

Es preferible usar formulaciones como:
Esta decisión reduce la probabilidad de que el riesgo ocurra.
Esta decisión reposiciona el riesgo en una capa donde puede ser gobernado y observado.
Esta decisión deja un riesgo residual que debe ser aceptado o controlado.

---

# Diferencia entre riesgo y problema

Un riesgo es probabilístico.

Si la condición ya ocurrió o es observable, debe tratarse como problema, incidente, defecto, deuda técnica materializada o restricción.

Ejemplo
Riesgo:
Podrían implementarse reglas de negocio dentro de integraciones futuras.

Problema:
Ya existen reglas de negocio embebidas en integraciones productivas.

Incidente:
Una integración falló porque una regla hardcodeada fue modificada incorrectamente.

Deuda técnica:
Cambiar una regla requiere modificar múltiples flujos e impacta tiempos de despliegue.

---

# Riesgo antes y después de la decisión

Cuando un ADR esté relacionado con riesgos, debe explicar:
¿Dónde estaba posicionado el riesgo antes de la decisión?
¿Dónde queda posicionado después de la decisión?
¿Qué riesgo residual permanece?
¿Qué controles son necesarios?

Ejemplo
Antes de la decisión:
El riesgo estaba posicionado en la capa de Integraciones, donde las reglas podían quedar ocultas dentro de transformaciones o validaciones técnicas.

Después de la decisión:
El riesgo se reposiciona en Gobierno / Políticas / Decisiones, donde puede existir ownership, versionamiento, aprobación y auditoría.

Riesgo residual:
Permanece el riesgo de mala administración de reglas si no se implementan controles de cambio, observabilidad y segregación de funciones.

---

# Controles derivados

Una decisión aceptada puede requerir controles.

Ejemplos de controles:

- Ownership funcional.
- Control de cambios.
- Versionamiento.
- Auditoría.
- Observabilidad.
- Validaciones automáticas.
- Segregación de funciones.
- Revisión periódica.
- Evidencia de aprobación.
- Plan de rollback.
- Monitoreo de ejecución.
- Alertamiento.

La existencia de un ADR no significa que el riesgo queda cerrado. El riesgo puede permanecer abierto hasta que los controles requeridos estén implementados.

---

# Responsables

Todo ADR debe identificar responsables.

Roles sugeridos:
| Rol                        | Responsabilidad                                                            |
| -------------------------- | -------------------------------------------------------------------------- |
| Arquitectura Empresarial   | Valida alineación con principios, modelo objetivo y trazabilidad.          |
| Arquitectura de Solución   | Valida viabilidad de diseño e impacto técnico.                             |
| Negocio                    | Valida que la decisión responda al proceso y reglas operativas.            |
| Datos                      | Valida ownership, linaje, calidad y uso de datos.                          |
| Integraciones              | Valida impacto en flujos, orquestación y contratos.                        |
| Seguridad                  | Valida controles, accesos, segregación y auditoría.                        |
| Observabilidad / Operación | Valida monitoreo, soporte, alertas y continuidad.                          |
| Proveedor                  | Valida restricciones, capacidades o impactos contractuales cuando aplique. |

---

# Revisión futura

Todo ADR debe tener una condición de revisión.

Ejemplos de detonadores:

- Cambio de alcance.
- Cambio de proveedor.
- Cambio de herramienta.
- Cambio de proceso.
- Cambio regulatorio.
- Cambio de modelo operativo.
- Aparición de incidentes recurrentes.
- Nuevos riesgos relacionados.
- Reemplazo de una aplicación.
- Cambio de arquitectura To-Be.
- Evidencia de que el riesgo residual ya no es aceptable.

--- 

# Reglas de edición
No modificar el sentido histórico de una decisión aceptada sin dejar evidencia.
Si una decisión cambia sustancialmente, crear un nuevo ADR o marcar el anterior como Superseded.
Si una decisión ya no se recomienda, marcarla como Deprecated.
Si una decisión fue evaluada y descartada, conservarla como Rejected.
No borrar ADRs históricos.
No reutilizar IDs.
Actualizar el decision-log.xlsx cuando cambie el estado de un ADR.
Relacionar el ADR con riesgos y controles cuando aplique.
Mantener lenguaje claro y verificable.
Evitar justificar decisiones únicamente por preferencia técnica.

---

# Flujos para crear un nuevo ADR

1. Identificar la decisión pendiente.
2. Confirmar que realmente requiere ADR.
3. Asignar el siguiente ID disponible.
4. Copiar templates/adr-template.md.
5. Crear el archivo en decisions/.
6. Escribir el contexto y la pregunta arquitectónica.
7. Documentar alternativas.
8. Analizar impacto APS.
9. Analizar impacto LEM.
10. Relacionar riesgos.
11. Proponer decisión.
12. Definir consecuencias y trade-offs.
13. Identificar controles.
14. Definir responsables.
15. Registrar en decision-log.xlsx.
16. Someter a revisión.
17. Actualizar estado.

Ejemplo:
cp templates/adr-template.md decisions/ADR-0005-definicion-ownership-datos.md

--- 

# Flujo para aprobar un ADR

1. El ADR inicia en estado Proposed.
2. Se revisa con los responsables impactados.
3. Se ajusta el contenido según comentarios.
4. Se valida la decisión propuesta.
5. Se define si existen controles obligatorios.
6. Se actualiza el estado a Accepted, Rejected o Under Review.
7. Se actualiza el decision-log.xlsx.
8. Se comunican las implicaciones.
9. Se actualizan artefactos relacionados.

---

# Flujo para reemplazar una decisión

1. Crear un nuevo ADR con la nueva decisión.
2. Referenciar el ADR anterior.
3. Cambiar el estado del ADR anterior a Superseded.
4. Indicar qué ADR lo reemplaza.
5. Actualizar el decision-log.xlsx.
6. Actualizar artefactos relacionados.

Ejemplo:
ADR-0004 fue reemplazado por ADR-0012 debido a cambio de herramienta de observabilidad.

---

# Flujo para revisar una decisión
1. Cambiar estado a Under Review.
2. Documentar motivo de revisión.
3. Evaluar si la decisión sigue siendo válida.
4. Confirmar, ajustar, reemplazar o deprecar la decisión.
5. Actualizar decision-log.xlsx.
6. Actualizar riesgos, controles y artefactos relacionados.

---

# Criterios mínimos para aceptar un ADR

Antes de marcar un ADR como Accepted, debe cumplir al menos con lo siguiente:

 Tiene ID único.
 Tiene título claro.
 Tiene estado.
 Tiene fecha.
 Tiene contexto.
 Tiene pregunta arquitectónica.
 Tiene decisión explícita.
 Tiene alternativas evaluadas.
 Tiene justificación.
 Tiene consecuencias.
 Tiene impacto APS, cuando aplique.
 Tiene análisis LEM, cuando aplique.
 Tiene riesgos relacionados, cuando aplique.
 Tiene riesgos residuales, cuando aplique.
 Tiene controles requeridos, cuando aplique.
 Tiene responsables o áreas involucradas.
 Tiene artefactos relacionados.
 Está registrado en el decision-log.xlsx.

# Relación con el decision log
Cada ADR creado debe registrarse en:

registers/decision-log.xlsx

El decision log permite consultar:

ID del ADR.
Título.
Estado.
Dominio.
Capas APS impactadas.
Riesgos relacionados.
Controles derivados.
Responsable.
Fecha.
Última revisión.
Próxima revisión.
Artefactos relacionados.

El ADR sigue siendo la fuente de verdad de la decisión. El Excel es una herramienta de navegación y seguimiento.

---

Regla de cierre

Una buena decisión arquitectónica no solo define qué se hará.

También deja claro:
Qué se decidió no hacer.
Qué riesgo se está aceptando.
Qué responsabilidad se está posicionando.
Qué control será necesario.
Qué consecuencia futura se está asumiendo.

---