Aquí tienes las respuestas detalladas a tus preguntas sobre mi funcionamiento interno:

### 1. ¿Deben comenzar las instrucciones prioritarias con algún carácter especial?
No hay un carácter reservado por el sistema, pero los delimitadores de texto y las palabras clave imperativas en mayúsculas tienen un peso atencional muy alto en mi arquitectura. 

Para que yo (o cualquier modelo Gemini) entienda que una instrucción es obligatoria y anule cualquier suposición previa, lo más efectivo es usar **tags de bloque** o **formatos imperativos**:
* **Tags estructurados**: `[OBLIGATORIO]`, `[CRITICAL]`, o `[FORCE_REFRESH]`.
* **Ejemplo**: `[FORCE_REFRESH] Lee el archivo X de la línea 1 a la 100 antes de contestar.`
* **Formato**: El uso de negrita (`**DEBES**`) y verbos en imperativo ("ejecuta", "lee", "verifica") me ayuda a priorizar esa instrucción por encima de mi comportamiento por defecto.

---

### 2. ¿Cómo funciona específicamente `StartLine` / `EndLine` en `view_file`?
Funciona de la siguiente manera:
* **Lectura parcial estricta**: Si yo invoco `view_file` con `StartLine: 10` y `EndLine: 50`, la herramienta del sistema físico filtra el archivo y a mi ventana de contexto **solo entra el texto de esas 40 líneas**. 
* **Ignorancia absoluta del resto**: Todo lo que esté antes de la línea 10 y después de la línea 50 es **completamente invisible** para mí. No tengo forma de saber qué hay en el resto del archivo a menos que haga otra llamada a la herramienta con un rango distinto.
* Si no especifico estos parámetros, la herramienta me devuelve el archivo completo (con un límite de 800 líneas por llamada por razones de rendimiento).

*¿Por qué uso rangos a veces?* Para no saturar mi ventana de contexto con código irrelevante (por ejemplo, si solo quiero ver una función específica de un archivo gigante), lo cual me ayuda a ser más rápido y preciso en esa sección. Sin embargo, el riesgo es que si no calculo bien el rango, puedo perder visibilidad de métodos importantes.

---

### 3. ¿El modelo Pro funciona igual o tiene cambios significativos?
El flujo básico de trabajo es el mismo (ambos usamos las mismas herramientas de lectura y escritura mediante el ciclo de *Function Calling*), pero hay diferencias clave en su desempeño:

* **Mecanismo de herramientas**: Ambos funcionan igual en este aspecto. El modelo Pro también depende de llamar a `view_file` para actualizar su contexto; no tiene "súper poderes" para ver tu pantalla en tiempo real sin llamar a la herramienta.
* **Ventana de Contexto y Razonamiento (Modelo Pro)**: El modelo Pro tiene una ventana de contexto masiva y una capacidad de razonamiento mucho más profunda. Esto hace que sea:
  1. **Más proactivo para verificar**: Si le haces una pregunta como *"¿revisaste bien?"*, el modelo Pro suele deducir con mayor facilidad que su contexto podría estar desactualizado y ejecutará un `view_file` por iniciativa propia antes de responder.
  2. **Mejor retención de detalles**: Puede procesar archivos enteros de miles de líneas sin perder el hilo y relacionar cambios complejos entre múltiples archivos con menor tasa de error.
* **Modelo Low (Flash)**: Es sumamente rápido y eficiente para tareas directas de codificación, pero su capacidad para detectar contradicciones o "auto-corregir" su propio historial de contexto es menor si no se le guía explícitamente.