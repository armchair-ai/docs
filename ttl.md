### 1. ¿Cuánto tiempo dura la sesión del agente? (TTL / Expiración)

A nivel de almacenamiento y persistencia de datos de la API de Google (el backend que procesa y recuerda los mensajes):
* **Persistencia en la Nube:** Las conversaciones persistidas mediante `conversation_id` están asociadas al servicio backend de Google. La política de retención estándar en las APIs de desarrollo y agentes suele ser de **hasta 30 días** (o configurable si usas Google Cloud empresarial). Por lo tanto, **si pasan dos semanas, tu `conversation_id` debería seguir funcionando** perfectamente, siempre y cuando no se haya purgado manualmente el ID o tu API Key/autenticación haya expirado.
* **Persistencia Local:** Los metadatos de configuración locales se guardan en tu máquina (en la ruta de configuración global `~/.gemini/antigravity-cli/` o en los volúmenes montados de Docker). Estos no expiran por tiempo y permanecen en disco hasta que los borres manualmente.

---

### 2. ¿Es posible cerrarla o terminarla de manera imperativa?

Sí, puedes finalizar la sesión de manera controlada de tres formas:

#### A. Desde el código (SDK de Python)
Si estás programando tu propio backend o script de automatización, la clase `Agent` y su objeto interno `Conversation` disponen de métodos de ciclo de vida:

1. **Liberar recursos y desconectar:** 
   El SDK utiliza bloques `async with` para automatizar esto. Al salir del bloque, el agente invoca el método `__aexit__` que limpia la sesión activa. Si necesitas hacerlo manualmente:
   ```python
   # Cierra la conexión y libera los recursos de transporte en segundo plano
   await agent.conversation.disconnect()
   ```
2. **Limpiar el historial en memoria:**
   Si la sesión sigue activa pero quieres liberar espacio eliminando el historial de turnos acumulados en el buffer:
   ```python
   agent.conversation.clear_history()
   ```
3. **Cancelar una tarea en progreso:**
   Si el agente se queda pegado ejecutando un bucle o procesando código:
   ```python
   await agent.conversation.cancel()
   ```