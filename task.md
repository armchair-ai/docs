El archivo `task.md` funciona como la **memoria de progreso** del agente. Es un archivo Markdown dinámico que sirve de checklist para organizar y coordinar el trabajo en tareas complejas.

### 1. ¿Cómo funciona `task.md`?
El archivo utiliza una sintaxis de casillas de verificación para marcar el estado exacto de cada sub-tarea:

* `- [ ] Tarea pendiente`: Cosas que aún no se han empezado.
* `- [/] Tarea en progreso`: Lo que el agente está codificando en el turno actual.
* `- [x] Tarea completada`: Lo que ya se programó y verificó.

A medida que el agente avanza, va editando este archivo en el disco para actualizar el estado de los componentes.

---

### 2. ¿Para qué lo necesita el modelo Pro (o cualquier modelo en este flujo)?

En un flujo coordinado entre el modelo Pro y el modelo Low (Flash), el `task.md` es el **puente de comunicación y sincronización** por tres razones principales:

#### A. Evita la pérdida de contexto tras el cambio de modelo
Cuando cambias del modelo Pro al modelo Low (o viceversa), el nuevo modelo no "recuerda" la conversación anterior de la misma manera. Al leer el `task.md` al inicio de su turno, el modelo Low sabe instantáneamente:
* Qué ha diseñado el Pro.
* Qué tareas ya se completaron.
* Cuál es la siguiente línea de código exacta que debe escribir.

#### B. Permite la delegación estructurada
El modelo Pro es excelente dividiendo un problema grande en partes pequeñas y seguras. El Pro puede escribir un `task.md` muy detallado (por ejemplo, dividiendo un refactor en 5 pasos pequeños para no romper nada). Luego, el modelo Low solo tiene que leer ese checklist y dedicarse a ejecutarlo paso a paso, lo cual reduce la probabilidad de que el modelo Low cometa errores de diseño.

#### C. Control de calidad y retroceso
Si el modelo Low (Flash) se queda atascado o rompe un test en el paso 3, puedes cambiar de nuevo al modelo Pro. El modelo Pro leerá el `task.md`, verá que los pasos 1 y 2 están completados (`[x]`), que el paso 3 está en progreso (`[/]`), y podrá concentrarse exclusivamente en diagnosticar el problema del paso 3 sin tener que analizar de nuevo todo lo que ya funciona.