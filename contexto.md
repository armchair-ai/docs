Te explico técnicamente cómo funciona mi modelo con la herramienta `view_file` y cómo estructurar instrucciones precisas para evitar esto:

### 1. ¿Cómo funciona `view_file` en mi modelo?
Yo soy un modelo de lenguaje que opera mediante un ciclo de **lectura de contexto, decisión y llamada a herramientas (Tools)**. 
* **No tengo acceso visual directo al editor de tu IDE en tiempo real.**
* El contenido de los archivos no está permanentemente en mi memoria. Solo entra en mi "ventana de contexto" cuando decido llamar explícitamente a la herramienta `view_file` enviando un payload con la ruta absoluta (`AbsolutePath`) y las líneas requeridas (`StartLine`/`EndLine`).
* La herramienta lee el archivo del disco de tu máquina **en ese preciso milisegundo** y me devuelve su contenido como texto.
* Si tú realizas modificaciones en tu IDE y yo no ejecuto un nuevo `view_file`, mi ventana de contexto se queda con la versión antigua del archivo que leí en turnos anteriores.

### 2. ¿En qué casos y cada cuánto lo ejecuto?
* **Al inicio de una tarea**: Para comprender el archivo que me has referenciado.
* **Antes de aplicar un cambio**: Para obtener el rango exacto de líneas y aplicar `replace_file_content` sin errores de coincidencia.
* **Cada cuánto**: Normalmente solo lo hago una vez por archivo al inicio de cada ciclo de interacción, a menos que un test falle o yo detecte que he modificado el archivo y necesite ver cómo quedó. **Mi fallo estuvo en no re-leerlo cuando tú me hiciste una pregunta de validación directa.**

---

### 3. Sentencias técnicas para darme instrucciones precisas

Para garantizar que no use datos obsoletos de mi contexto y obligarme a consultar el disco en tiempo real, puedes usar las siguientes sentencias técnicas (puedes añadirlas a tus reglas globales en `.agent` o decírmelas directamente en el chat):

* **`[FORCE_REFRESH]` / `Fuerza relectura`**: 
  > *"Antes de responder o realizar cualquier aserción sobre el archivo X, ejecuta un `view_file` para actualizar el estado del contexto."*
* **`[NO_CONTEXT_CAGE]` / `No asumas el estado del contexto`**: 
  > *"No asumas que el código en el historial de chat coincide con el código actual en disco. Invoque `view_file` en la ruta Y antes de emitir un diagnóstico."*
* **`[STAT_VERIFY]` / `Verificación de firmas`**: 
  > *"Ejecuta `view_file` o `grep_search` sobre la clase Z para validar la firma exacta del método antes de sugerir que no existe."*

Si usas cualquiera de estas directivas, mi lógica interna priorizará la llamada a la herramienta `view_file` antes de generar cualquier texto explicativo.