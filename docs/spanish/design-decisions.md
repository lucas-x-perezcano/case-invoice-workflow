# Decisiones de diseño

El problema del proceso fue el punto de partida: una persona tenía que interpretar un documento recibido, consultar información de productos, preparar un registro de inspección, mantener los archivos relacionados y responder. El diseño separa los pasos que requieren interpretación de los que deben seguir reglas explícitas. Estas decisiones sostienen un proceso funcional, aunque todavía faltan controles operativos importantes antes de la implementación interna.

## Usar IA para interpretar

Las facturas no comparten un solo formato ni una misma manera de expresar la información. Las posiciones fijas y las reglas rígidas de extracción pueden fallar cuando un dato aparece en otro lugar. Por eso, el servicio extrae el texto del PDF y pide a un modelo de lenguaje que clasifique el documento y proponga campos estructurados. Esa flexibilidad es útil, pero una respuesta estructurada del modelo sigue siendo una propuesta: no demuestra que cada valor coincida con el documento original.

## Usar reglas para verificar

La consulta de códigos de producto y la aritmética de cada partida tienen entradas definidas y resultados reproducibles. La lógica de la aplicación realiza estas verificaciones para que un mismo conjunto de valores extraídos se evalúe de forma predecible. Encargar al modelo la decisión de si existe un código en el catálogo o si cuadran los números dificultaría rastrear esas decisiones. Una consulta fallida o una discrepancia de cantidad produce una advertencia visible en vez de modificar silenciosamente el valor de la factura. La cobertura del catálogo y la calidad de los datos extraídos siguen limitando lo que estas verificaciones pueden demostrar.

## Mantener a las personas en el manejo de excepciones

Una cantidad inusual o un dato faltante del catálogo pueden requerir conocimiento de la operación. Se prevé que la persona encargada del flujo de facturas revise las advertencias; las fallas persistentes y los formatos inesperados también necesitan seguimiento humano. La responsabilidad formal de aprobación y la autorización final todavía no se han definido. En la implementación actual, una hoja de cálculo con advertencias se almacena y se envía sin esperar aprobación. Esta decisión permite que avance el flujo rutinario, pero deja una brecha de control que debe resolverse antes del uso organizacional.

## Conservar la relación entre documentos

El PDF original, la hoja de cálculo de inspección, las copias almacenadas y la respuesta pertenecen a un mismo caso operativo. Archivar juntos el original y el resultado, y responder en el hilo inicial del correo, ayuda a rastrear el resultado hasta su documento de origen y a encontrar la conversación relacionada. Es una forma práctica de trazabilidad, no una afirmación de que exista una pista de auditoría completa ni almacenamiento transaccional: una falla posterior todavía puede interrumpir la secuencia de acciones.

## Enrutar las excepciones según el resultado

Un documento rechazado recibe una respuesta explicativa sin hoja de cálculo. Una falla de procesamiento deja el mensaje disponible para un intento posterior; una falla del servicio también alerta a operaciones. Las advertencias acompañan a un resultado que sí se generó, para que la persona destinataria vea el campo cuestionable. Estas rutas evitan presentar todas las excepciones como facturas terminadas, pero todavía no garantizan que alguien haya revisado una advertencia.

## Compensaciones actuales

El flujo prioriza una separación clara entre interpretación, verificaciones y rutas de respuesta, en lugar de una decisión única tomada por IA. No exige aprobación humana antes de enviar resultados con advertencias, ni ha establecido precisión para diversos proveedores o una recuperación confiable ante todas las fallas parciales. Esos límites importan para el diseño operativo y forman parte del trabajo pendiente para la implementación interna.
