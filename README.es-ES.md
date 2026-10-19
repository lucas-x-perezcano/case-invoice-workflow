# Flujo de facturas asistido por IA

[English](README.md) · [Español](README.es-ES.md)

Rediseño de un proceso recurrente de facturas mediante interpretación, verificaciones definidas y revisión humana.

**Lucas Perezcano**<br>
Ingeniería Industrial | Tecnológico de Monterrey

## El problema

Una persona atendía las facturas recibidas mediante una secuencia completamente manual: abrir el correo, descargar el documento, leer y organizar su información, consultar un catálogo maestro de productos, preparar una hoja de cálculo estandarizada para inspección, guardar los documentos y responder o dar seguimiento. Era una secuencia recurrente de decisiones en varios lugares, no solo una tarea de captura de datos.

Cada factura requería mantener relacionados el documento correcto, la información de productos, la hoja de cálculo y la respuesta. Un formato inusual o una cantidad cuestionable exigían atención sin perder el seguimiento del resto del trabajo. Identifiqué una oportunidad para hacer más consistentes los pasos rutinarios y más visibles las excepciones. No he medido el tiempo ni la tasa de errores del proceso original.

## Antes y después

**Antes:** Una persona descargaba e interpretaba cada factura, organizaba la información, consultaba los productos, preparaba el documento de inspección, guardaba los archivos y respondía.

**Después:** El flujo implementado lee correos elegibles sin leer, envía cada PDF a procesamiento, prepara una hoja de cálculo de inspección, la almacena junto con el documento original y responde en el hilo inicial del correo. Los documentos que no pueden procesarse siguen rutas separadas de rechazo o reintento. Las advertencias permanecen visibles para su revisión.

![Flujo de facturas antes y después](assets/visual-1-before-after.png)

## Por qué lo construí

Observé personalmente este flujo de trabajo e identifiqué la oportunidad de mejorarlo. Nadie me asignó un proyecto de IA o automatización. Decidí investigar el proceso porque era recurrente y tenía suficiente estructura para considerar la automatización de algunos pasos.

Se convirtió en mi primer proyecto serio de IA y automatización de flujos de trabajo. Aprendí lo necesario para mapear la tarea, construir el procesamiento y el flujo de correo, y probar el resultado. El objetivo era atender de forma consistente los pasos rutinarios y mantener visibles las excepciones para la persona responsable de resolverlas.

## Mi enfoque

Como estudiante de Ingeniería Industrial, abordé el proyecto como un problema de diseño de procesos. Mapeé cómo llega una factura, qué necesita el formato de inspección, qué puede confirmar el catálogo y qué debe ocurrir cuando un valor es cuestionable. Después asigné cada paso a la interpretación, a una regla fija o a una persona. **Usar IA donde se requiere interpretación. Usar lógica determinística donde la respuesta debe ser predecible. Mantener a las personas donde importa el criterio.**

Esto importa porque una factura puede ser legible y aun así contener un valor que amerita revisión. Un sistema que llena una hoja de cálculo sin mostrar la incertidumbre daría la impresión de que el proceso terminó antes de tiempo. Por eso, el diseño mantiene separados los datos extraídos, las verificaciones mediante reglas y las advertencias.

## IA, reglas y criterio humano

**La IA interpreta.** Primero, el software extrae texto del PDF. Después, un modelo de lenguaje interpreta distintos formatos de factura y propone campos estructurados, incluida la clasificación de si el documento es una factura. Su respuesta es una propuesta para las verificaciones posteriores, no una conclusión definitiva de que todos los valores sean correctos.

**Las reglas verifican y enrutan.** El código consulta códigos de producto en un catálogo privado, compara la cantidad con el precio unitario y el importe de la partida, genera la hoja de cálculo y elige una ruta de respuesta. Una consulta fallida o una discrepancia aritmética produce una advertencia en lugar de una corrección silenciosa.

**Las personas resuelven excepciones.** Los datos faltantes del catálogo, las cantidades cuestionables, las fallas persistentes y los formatos inesperados de archivos adjuntos requieren atención humana y una resolución final. Se prevé que la persona responsable del flujo de facturas revise las advertencias. La responsabilidad formal de aprobación y autorización final aún debe definirse; el flujo actual almacena y responde con una hoja de cálculo marcada con advertencias sin esperar una aprobación.

![IA, reglas y criterio humano](assets/visual-3-ai-rules-human.png)

## La solución

El flujo conecta un servicio de procesamiento de documentos con un proceso de correo. Para un mensaje elegible, lee el PDF adjunto, prepara información estructurada de la factura, verifica los valores que tienen reglas explícitas y crea una hoja de cálculo de inspección. Después archiva juntos el PDF original y la hoja de cálculo, y responde en el mismo hilo con enlaces.

Un documento rechazado recibe una respuesta. Una falla de procesamiento deja el mensaje disponible para un reintento y alerta a operaciones cuando el servicio devuelve una respuesta. Estas rutas hacen visibles tanto el resultado como la necesidad de dar seguimiento.

La factura y la hoja de cálculo de ejemplo usan nombres, códigos e importes inventados. Ilustran el flujo y una advertencia sobre cantidad sin revelar una factura ni un catálogo reales.

![Ejemplo de factura completamente sintética](assets/visual-2-synthetic-invoice.png)

![Representación conceptual y sintética de la hoja de inspección](assets/visual-4-synthetic-output.png)

## Arquitectura general

Un servicio de procesamiento lee el texto del PDF, solicita a un modelo de lenguaje campos estructurados, aplica consultas de catálogo y verificaciones aritméticas, y prepara la hoja de cálculo. Un flujo de correo independiente selecciona mensajes elegibles, llama al servicio, almacena el documento original y el resultado, y enruta respuestas o alertas según corresponda.

### Lecturas complementarias

Para conocer con mayor detalle la implementación y sus límites:

**Español**

- [Arquitectura del sistema](docs/spanish/architecture.md)
- [Decisiones de diseño](docs/spanish/design-decisions.md)
- [Validación y estado actual](docs/spanish/validation.md)

**English**

- [System Architecture](docs/english/architecture.md)
- [Design Decisions](docs/english/design-decisions.md)
- [Validation and Current Status](docs/english/validation.md)

## Validación

**Demostrado a la fecha.** El servicio de procesamiento de documentos y el flujo de correo están construidos, y este último funciona a través de mi propia cuenta. **Hay cinco facturas reales en mi conjunto actual de validación; se están documentando los criterios para evaluar sus resultados.** Las pruebas con simulaciones y las pruebas básicas cubren rutas seleccionadas de solicitudes y enrutamiento. Las pruebas controladas también han medido el tiempo de procesamiento y el costo de la API de IA por factura, pero aún se están confirmando el tamaño de la muestra y los límites de medición. Son resultados de validación del proyecto, no resultados de producción.

**Aún en validación.** Estoy reuniendo más facturas reales y resultados. El conjunto actual no establece la precisión para distintos proveedores o formatos, la tasa de intervención humana, la reducción de errores ni el tiempo ahorrado frente a una medición de referencia del trabajo manual. Las notas internas describen comparaciones repetidas de campos con cuatro ejemplos distintos de facturas, pero los documentos de referencia, los resultados de cada ejecución y las definiciones de discrepancia no están disponibles para este caso de estudio. Todavía no hay fundamento para publicar un porcentaje de precisión, volumen de procesamiento en producción, adopción o retorno de inversión.

![Validación y límites pendientes](assets/visual-5-validation.png)

## Estado actual

El servicio de procesamiento y el flujo de correo funcionan a través de mi propia cuenta. La configuración del buzón organizacional y la puesta en marcha interna son los pasos pendientes antes del uso oficial.

## Lo que construí y mi contribución

Construí el servicio de procesamiento de documentos y el flujo de correo que lo rodea. Ese trabajo incluye convertir el texto de facturas en campos estructurados, verificar códigos de producto y la aritmética de cada partida, preparar la hoja de cálculo de inspección y enrutar documentos y respuestas según el resultado.

Mi principal contribución fue conectar esas partes con la tarea original del negocio. El flujo debe conservar la relación entre un documento recibido, su resultado y la respuesta, además de hacer comprensibles las fallas y advertencias para la persona responsable del proceso.

## Lo que aprendí

El punto de partida útil para automatizar es describir con claridad el trabajo y sus excepciones. Una vez que pude hacerlo, fue más fácil decidir qué correspondía a la interpretación, a reglas fijas y a la revisión humana.

También aprendí que generar un resultado es solo una parte de la preparación operativa. Se prevé que la persona responsable del flujo de facturas revise las advertencias, mientras que la responsabilidad formal de aprobación, los reintentos y el uso seguro del resultado aún deben definirse para la puesta en marcha. En el diseño actual, los resultados marcados con advertencias se envían antes de una autorización final.

## Resumen técnico

El sistema utiliza un servicio de procesamiento de documentos en Python, extracción de texto de PDF, un modelo de lenguaje para la interpretación, consultas de catálogo y verificaciones aritméticas determinísticas, generación de hojas de cálculo y un flujo de correo para archivar y responder.

## Privacidad y confidencialidad

Todos los detalles de facturas y datos de productos que aparecen en este caso de estudio público son sintéticos. Las facturas reales, la información de proveedores y de la empresa, el catálogo privado y la implementación operativa permanecen privados. Los ejemplos públicos explican el flujo; no son copias de documentos de la empresa ni afirman que la hoja de cálculo ilustrada reproduzca exactamente el formato privado.
