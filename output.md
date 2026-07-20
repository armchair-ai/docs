### 1. Session Persistence (Persistencia de Sesión)
**¿Qué es?**
Permite que un agente "recuerde" lo que pasó en interacciones anteriores. En lugar de iniciar una conversación desde cero (donde el agente no tiene contexto del historial), puedes reanudar una sesión existente utilizando su identificador único (`conversation_id`).

**¿Cómo funciona en código?**
Cuando creas un agente por primera vez, el SDK genera un `conversation_id`. Para continuar la conversación más adelante, le pasas ese mismo ID al configurar el agente:

```python
conversation_id = agent.connection.conversation_id 
```

---

### 2. Structured Output (Salida Estructurada)
**¿Qué es?**
Por defecto, los LLMs devuelven texto plano libre (Markdown, explicaciones, etc.). Si quieres integrar la respuesta del agente directamente en tu aplicación (por ejemplo, para guardar datos en una base de datos o renderizar una interfaz), necesitas que devuelva datos estructurados (como JSON) con un formato estrictamente validado.

**¿Cómo funciona en código?**
Puedes definir un esquema usando una clase de **Pydantic** (o un diccionario / JSON Schema). El agente se verá obligado a responder cumpliendo exactamente con esa estructura, y podrás acceder a los datos directamente tipados con `response.structured_output()`.

```python
# Definimos la estructura exacta que queremos recibir
class FileAnalysis(BaseModel):
    filename: str = Field(description="Nombre del archivo")
    language: str = Field(description="Lenguaje de programación")
    has_errors: bool = Field(description="Si tiene errores de sintaxis")
    summary: str = Field(description="Breve resumen del archivo")

config = LocalAgentConfig()
async with Agent(config) as agent:
    # Le indicamos al agente que la respuesta debe seguir el esquema de FileAnalysis
    response = await agent.chat(
        "Analiza el archivo app/main.py",
        response_schema=FileAnalysis
    )
    
    # Obtenemos el objeto de Python ya validado y tipado
    data: FileAnalysis = response.structured_output()
    print(data.filename)    # "main.py"
    print(data.has_errors)  # False
```