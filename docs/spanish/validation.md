# Validación y estado actual

## Alcance de la validación

La evidencia disponible reúne una revisión de la implementación, algunas pruebas automatizadas, notas del proyecto y mi conjunto actual de validación. Estas fuentes muestran que existen los componentes de procesamiento y correo, y que se han ejercitado rutas específicas. No constituyen un estudio de desempeño en producción. En particular, las pruebas con respuestas simuladas muestran el comportamiento esperado en esas condiciones; no comprueban la entrega real de correos, el almacenamiento de archivos ni la adopción dentro de la organización.

## Capacidades demostradas

El servicio de procesamiento extrae texto de archivos PDF, solicita a un modelo de lenguaje campos estructurados de la factura, aplica consultas de catálogo y verificaciones aritméticas, y genera una hoja de cálculo de inspección. El flujo de correo selecciona mensajes elegibles sin leer y contempla rutas para documentos procesados, rechazos y fallas. Las pruebas básicas del servicio cubren solicitudes simples y su estado de funcionamiento; las pruebas del flujo cubren rutas seleccionadas de selección de mensajes, archivos adjuntos, carga de archivos y enrutamiento mediante simulaciones. Son comprobaciones acotadas, no una cobertura completa de todas las facturas o integraciones.

El flujo de correo funciona a través de mi propia cuenta. Hay cinco facturas reales en mi conjunto actual de validación, pero todavía se están documentando los criterios para considerar exitosa cada factura. Los registros disponibles no establecen cinco ejecuciones completas y verificadas desde el correo hasta el resultado, ni una tasa de éxito de cinco de cinco. Las notas internas del proyecto también describen comparaciones repetidas con cuatro ejemplos distintos de facturas; los documentos originales, los resultados de referencia y los registros de cada ejecución no están disponibles aquí para una auditoría independiente. Las repeticiones de esos ejemplos no deben interpretarse como cobertura de formatos independientes.

## Mediciones de desempeño

Se habían comunicado promedios de pruebas controladas sin suficientes registros de respaldo para establecer el tamaño de muestra y los límites de medición; esas cifras siguen omitidas. La comparación de octubre de 2026 que aparece a continuación es un experimento distinto y documentado. Su tiempo de procesamiento dentro del servicio y su costo variable estimado no representan el tiempo completo desde el correo hasta el almacenamiento ni el costo operativo total.

## Comparación controlada de desempeño — octubre de 2026

Un informe interno de indicadores compara Haiku 4.5 y Haiku 5.5 con **cuatro ejemplos distintos de facturas usados previamente**. Cada factura se procesó cinco veces por modelo: **20 solicitudes al servicio por modelo, 40 en total**, probadas el mismo día. Las facturas formaban parte del conjunto usado durante el desarrollo; no eran datos de prueba inéditos. Se compararon los códigos, cantidades y fechas extraídos con resultados de referencia revisados por una persona; una solicitud exitosa significa que el servicio de procesamiento devolvió un resultado, no que concluyeron el flujo de correo o una aprobación humana.

| Métrica | Haiku 4.5 | Haiku 5.5 |
| --- | ---: | ---: |
| Tiempo promedio de procesamiento | 16.1 s | 6.9 s |
| Costo variable estimado por factura | $0.01766 | $0.00233 |
| Resultados correctos de código y cantidad | 510/510 | 510/510 |
| Fechas correctas de factura | 19/20 | 20/20 |
| Solicitudes exitosas | 20/20 | 20/20 |

El tiempo de procesamiento se midió **dentro del manejador de solicitudes del servicio**, desde la lectura del PDF hasta la interpretación con el modelo, las consultas de catálogo y la generación de la hoja de cálculo. Excluye la orquestación del correo y las demoras previas al inicio del procesamiento. El costo variable estimado combina los cargos de la API del modelo con una estimación del cómputo usado para procesar cada solicitud. Excluye el proceso de correo, almacenamiento, compilaciones, registros, cargos por inactividad o por instancias mínimas y otros costos operativos; no es una factura del proveedor de nube ni una proyección de retorno de inversión.

En estos ejemplos, Haiku 5.5 tuvo un tiempo promedio de procesamiento aproximadamente **57 % menor** y un costo variable estimado aproximadamente **87 % menor**. Ambos modelos devolvieron códigos y cantidades correctos en todas las evaluaciones de partidas calificadas, por lo que no se observó un retroceso en esa medida dentro de la muestra. El modelo más reciente cometió **un error menos en la fecha de factura** (20/20 frente a 19/20). Son observaciones repetidas sobre cuatro facturas, no estimaciones de desempeño con nuevos proveedores o formatos.

La cifra **510/510** corresponde a **102 partidas distintas evaluadas cinco veces por modelo**, no a 510 facturas independientes. No se probó ningún formato de factura inédito. La capitalización del nombre del proveedor cambió en algunas ejecuciones, lo que puede afectar el encabezado o el nombre del archivo generado incluso cuando se identificó al proveedor. No se emitieron advertencias automáticas en estas ejecuciones; esas verificaciones no detectan todos los tipos de error de extracción. La comparación no volvió a ejecutar la orquestación posterior del correo y no establece precisión generalizable, confiabilidad en producción, ahorro de tiempo del personal ni retorno de inversión. El informe resume las mediciones, mientras que las facturas originales y los registros de cada ejecución permanecen privados y no se incluyen en este repositorio.

Una **prueba de esfuerzo de un solo caso con entrada degradada** usó deliberadamente una disposición desordenada del texto de una factura empleada anteriormente. En cinco ejecuciones por configuración, el modelo anterior obtuvo 83/130 evaluaciones correctas de código y cantidad, y las configuraciones probadas del modelo más reciente obtuvieron 130/130. Fueron llamadas directas a la API del modelo con una entrada alterada, no el flujo normal del servicio ni evidencia sobre formatos inéditos. Con la entrada normal de dos disposiciones de texto, el modelo anterior también obtuvo 130/130 en esa factura.

## Límites y estado de implementación

El trabajo actual no establece precisión generalizable de la extracción, confiabilidad o volumen de procesamiento en producción, adopción, tasa de intervención humana, tiempo de trabajo manual ahorrado, reducción de errores ni retorno de inversión. No existe una medición de referencia del tiempo manual que permita afirmar ahorros. Una hoja de cálculo generada puede contener advertencias, y el flujo envía ese resultado antes de una autorización humana; su existencia por sí sola no demuestra que la factura estuviera lista para uso operativo.

El sistema funciona a través de mi propia cuenta. Siguen pendientes la configuración del buzón organizacional y la implementación interna. También falta definir formalmente quién revisará y aprobará las advertencias en el proceso implementado. Este caso de estudio no describe el flujo como puesto en producción.

## Próximos pasos de validación

El trabajo siguiente consiste en definir criterios de éxito para cada factura y registrar el resultado de cada documento, incluidos rechazos, advertencias, reintentos e intervención manual. Las pruebas deben incorporar formatos de factura más variados e inéditos y comparar el resultado generado con el documento original. Deben conservarse los datos por ejecución sobre tiempo, uso de tokens y costo para poder reproducir de forma independiente la comparación de octubre y extender la medición al flujo completo de correo. Antes de la implementación interna, el equipo debe establecer la responsabilidad de revisar excepciones, probar la ruta del buzón organizacional y confirmar cómo pueden utilizarse los resultados con advertencias. Estos son pasos propuestos, no resultados ya obtenidos.
