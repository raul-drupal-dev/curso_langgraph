# 🔄 Tema 7: Doble-Texting – Manejo de Mensajes Simultáneos en LangGraph  

## 🌟 Introducción  

Cuando un usuario interactúa con un chatbot o un sistema basado en LangGraph, puede ocurrir que **envíe múltiples mensajes antes de recibir una respuesta**.  
Este fenómeno se conoce como **doble-texting** y puede generar **inconsistencias en el flujo conversacional** si no se maneja correctamente.  

📌 **Ejemplo de problema con doble-texting:**  
1️⃣ Un usuario envía el mensaje: *"¿Cuánto cuesta el producto?"*  
2️⃣ Antes de que el chatbot responda, envía otro mensaje: *"Olvídalo, mejor dime sobre el envío."*  
3️⃣ Si el grafo no maneja el doble-texting, **puede responder primero a la pregunta del precio**, ignorando el segundo mensaje o procesándolo de forma desordenada.  

🔹 Para evitar estos problemas, LangGraph ofrece **cuatro estrategias** para manejar el doble-texting de forma efectiva:  
✅ **Reject (Rechazar)** – Ignorar el segundo mensaje.  
✅ **Enqueue (Encolar)** – Poner el mensaje en espera hasta que termine la respuesta actual.  
✅ **Interrupt (Interrumpir)** – Detener la ejecución y procesar el mensaje nuevo.  
✅ **Rollback (Revertir)** – Deshacer la ejecución y comenzar desde el nuevo mensaje.  

Cada una de estas estrategias se adapta a diferentes necesidades según el contexto del grafo.  

🔗 **Referencia oficial**: [Doble-Texting en LangGraph](https://langchain-ai.github.io/langgraph/concepts/double_texting/)  

---

## ❌ 1. Reject – Rechazar Mensajes Adicionales  

Con esta estrategia, **LangGraph ignora cualquier nuevo mensaje** si ya hay una ejecución en curso.  
Es útil cuando queremos **procesar solo una solicitud a la vez**, evitando interrupciones o cambios en el flujo.  

???+ warning "Atención"  
    Esta estrategia puede generar una mala experiencia de usuario si el segundo mensaje es más relevante que el primero.  

```python
from langgraph_sdk import get_client
url_for_cli_deployment = "http://localhost:8123"
client = get_client(url=url_for_cli_deployment)

import httpx
from langchain_core.messages import HumanMessage

# Creamos un thread
thread = await client.threads.create()

# Creamos las operaciones
input_1 = "Realizame esta suma 4+2."
input_2 = "perdon, era 4+3."
config = {"configurable": {"user_id": "Test-Double-Texting"}}
graph_name = "calculator" 

# Ejecutamos el primero Run
run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_1)]}, 
    config=config,
)
try:
    # Simulamos que ejecutamos otro Run de forma seguida.
    await client.runs.create(
        thread["thread_id"],
        graph_name,
        input={"messages": [HumanMessage(content=input_2)]}, 
        config=config,
        multitask_strategy="reject",
    )
except httpx.HTTPStatusError as e:
    print("Aún hay una ejecución en marcha:", e)
```

```python title="Resultado"
Aún hay una ejecución en marcha: Client error '409 Conflict' for url 'http://localhost:8123/threads/xxxxxxxxxxxxxxxxxx/runs'
For more information check: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/409
```

Como vemos en el código, al ejecutar el segundo *run*, obtenemos un error que indica que ya hay un proceso en ejecución.  
Esto significa que **el segundo mensaje no se procesará**, evitando conflictos en el flujo del grafo.  

???+ quote "Recomendación"

    Si se usa esta estrategia, es importante **informar al usuario** que debe esperar antes de enviar otro mensaje.  


---

## 📥 2. Enqueue – Encolar Mensajes en Espera  

En lugar de ignorar el nuevo mensaje, **LangGraph lo almacena en una cola** y lo procesa cuando termine la ejecución actual.  
Esta estrategia permite **mantener el orden de los mensajes sin perder información**.  

???+ note "Nota"  
    Con esta estrategia, el chatbot responderá **cada mensaje en el orden en que se recibió**, garantizando coherencia en la conversación.  

```python hl_lines="24"
from langchain_core.messages import HumanMessage, convert_to_messages

# Creamos un thread
thread = await client.threads.create()

# Creamos las operaciones
input_1 = "Realizame esta suma 4+2."
input_2 = "perdon, era 4+3."
config = {"configurable": {"user_id": "Test-Double-Texting"}}
graph_name = "calculator" 

first_run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_1)]}, 
    config=config,
)

second_run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_2)]}, 
    config=config,
    multitask_strategy="enqueue",
)

# Esperamos hasta que el segundo termine
await client.runs.join(thread["thread_id"], second_run["run_id"])

# Obtenemos el resultado del grafo
state = await client.threads.get_state(thread["thread_id"])
for m in convert_to_messages(state["values"]["messages"]):
    m.pretty_print()
```

```python title="Resultado"
================================ Human Message =================================

Realizame esta suma 4+2.
================================== Ai Message ==================================
Tool Calls:
  sumar (call_ptkHcyJZIEtBxedW0map6xxO)
 Call ID: call_ptkHcyJZIEtBxedW0map6xxO
  Args:
    a: 4
    b: 2
================================= Tool Message =================================
Name: sumar

6
================================== Ai Message ==================================

La suma de 4 + 2 es 6.
================================ Human Message =================================

perdon, era 4+3.
================================== Ai Message ==================================
Tool Calls:
  sumar (call_T4rPpzzVKsQPFz2Q7b4JRdtW)
 Call ID: call_T4rPpzzVKsQPFz2Q7b4JRdtW
  Args:
    a: 4
    b: 3
================================= Tool Message =================================
Name: sumar

7
================================== Ai Message ==================================

La suma de 4 + 3 es 7.
```

Aquí vemos que, aunque se envían dos mensajes consecutivos, LangGraph **almacena el segundo mensaje en la cola**  
y lo procesa **una vez que finaliza el primero**.  

???+ quote "Ventajas" 

    - **Garantiza respuestas en orden.**  
    - **Evita errores de ejecución simultánea.**  
    - **Mantiene el flujo de la conversación sin bloqueos.** 

---

## ⏹️ 3. Interrupt – Interrumpir la Ejecución en Curso  

Esta estrategia **detiene la ejecución actual y prioriza el nuevo mensaje**, asegurando que siempre se responda la última entrada del usuario.  

???+ example "Ejemplo"  
    Si un usuario pregunta por un producto y luego dice *"Olvídalo, mejor dime sobre el envío"*, el chatbot **interrumpirá la respuesta** y procesará la última solicitud.  

```python hl_lines="24"
from langchain_core.messages import HumanMessage, convert_to_messages

# Creamos un thread
thread = await client.threads.create()

# Creamos las operaciones
input_1 = "Realizame esta suma 4+2."
input_2 = "perdon, era 4+3."
config = {"configurable": {"user_id": "Test-Double-Texting"}}
graph_name = "calculator" 

interrupted_run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_1)]}, 
    config=config,
)

second_run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_2)]}, 
    config=config,
    multitask_strategy="interrupt",
)

# Esperamos hasta que el segundo termine
await client.runs.join(thread["thread_id"], second_run["run_id"])

# Obtenemos el resultado del grafo
state = await client.threads.get_state(thread["thread_id"])
for m in convert_to_messages(state["values"]["messages"]):
    m.pretty_print()
```

```python title="Resultado"
================================ Human Message =================================

Realizame esta suma 4+2.
================================ Human Message =================================

perdon, era 4+3.
================================== Ai Message ==================================
Tool Calls:
  sumar (call_y7Q6Og96OLpoVXZ4Xt92yE1B)
 Call ID: call_y7Q6Og96OLpoVXZ4Xt92yE1B
  Args:
    a: 4
    b: 3
================================= Tool Message =================================
Name: sumar

7
================================== Ai Message ==================================

La suma de 4 + 3 es igual a 7.
```

Como podemos ver, **la ejecución anterior se interrumpe** y el segundo mensaje se procesa inmediatamente.  
Sin embargo, **cualquier información generada hasta ese punto sigue almacenada**.  

???+ warning "Cuidado"  
    Esta estrategia no borra el estado del grafo antes de interrumpir la ejecución. Si la ejecución anterior había guardado datos parciales, estos seguirán disponibles.  

Podemos comprobar que el *run* ha sido interrumpido ejecutando lo siguiente: 

```python
# Confirmamos que el primer run se ha interrumpido y que el segundo ha terminado.
print((await client.runs.get(thread["thread_id"], interrupted_run["run_id"]))["status"])
print((await client.runs.get(thread["thread_id"], second_run["run_id"]))["status"])
```

```python title="Resultado"
interrupted
success
```

---

## 🔄 4. Rollback – Revertir la Ejecución y Procesar el Nuevo Mensaje  

Con esta estrategia, **LangGraph deshace la ejecución actual y vuelve al estado anterior** antes de procesar el nuevo mensaje.  
Es útil cuando queremos garantizar que cada respuesta tenga en cuenta la última entrada del usuario sin interrupciones abruptas.  

???+ quote "Ejemplo"  
    Si un usuario dice *"Quiero pedir una pizza"*, pero segundos después dice *"Mejor una hamburguesa"*, la solicitud anterior se **revierte** y se procesa solo la última.  

```python hl_lines="24"
from langchain_core.messages import HumanMessage, convert_to_messages

# Creamos un thread
thread = await client.threads.create()

# Creamos las operaciones
input_1 = "Realizame esta suma 4+2."
input_2 = "perdon, era 4+3."
config = {"configurable": {"user_id": "Test-Double-Texting"}}
graph_name = "calculator" 

rolled_back_run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_1)]}, 
    config=config,
)

second_run = await client.runs.create(
    thread["thread_id"],
    graph_name,
    input={"messages": [HumanMessage(content=input_2)]}, 
    config=config,
    multitask_strategy="rollback",
)

# Esperamos hasta que el segundo termine
await client.runs.join(thread["thread_id"], second_run["run_id"])

# Obtenemos el resultado del grafo
state = await client.threads.get_state(thread["thread_id"])
for m in convert_to_messages(state["values"]["messages"]):
    m.pretty_print()
``` 

```python title="Resultado"
================================ Human Message =================================

perdon, era 4+3.
================================== Ai Message ==================================
Tool Calls:
  sumar (call_O8oEtXiQ34q6K4MjhFsAkuP7)
 Call ID: call_O8oEtXiQ34q6K4MjhFsAkuP7
  Args:
    a: 4
    b: 3
================================= Tool Message =================================
Name: sumar

7
================================== Ai Message ==================================

La solución de la ecuación \( 4 + 3 \) es \( 7 \).
```

Aquí, observamos que **el primer mensaje se borra completamente del estado del grafo** y se procesa solo el último.

???+ Warning "Consideraciones"

    ✅ Esta estrategia garantiza **precisión en la respuesta**, pero...  
    ❌ Puede **eliminar información útil** que se haya procesado en el primer mensaje.

    Se recomienda usar Rollback solo cuando se tiene **un control claro** de los datos almacenados en el grafo.

---

## ✨ Conclusión  

El doble-texting es un desafío común en sistemas conversacionales, pero con LangGraph podemos **gestionar múltiples mensajes de manera estructurada**.  

✅ **Podemos ignorar mensajes adicionales (Reject).**  
✅ **Podemos encolarlos para responderlos en orden (Enqueue)(Recomendado).**  
✅ **Podemos interrumpir la ejecución para priorizar el último mensaje (Interrupt).**  
✅ **Podemos revertir la ejecución y comenzar desde el nuevo mensaje (Rollback).**  

🚀 **Con estas estrategias, podemos mejorar la fluidez y coherencia en las conversaciones con LangGraph.**  

---

## 🧑‍🏫 **¿Qué Hemos Aprendido?**  

- Qué es el **doble-texting** y por qué puede generar problemas en un grafo.  
- Cuáles son las **cuatro estrategias** para manejar múltiples mensajes.  
- Cuándo usar **Reject, Enqueue, Interrupt y Rollback** según el caso de uso.  

---

## 🔎 Recursos:

- :simple-googlecolab: Ver notebook en [Google Colab](https://colab.research.google.com/drive/1j3I46DOc_YUFsJlA8Rchh-mSB0U0vo8M?usp=sharing)
- :books: Concept: [Double Texting](https://langchain-ai.github.io/langgraph/concepts/double_texting/#double-texting)
- :books: How-to-guide: [Reject](https://langchain-ai.github.io/langgraph/cloud/how-tos/reject_concurrent/#reject)
- :books: How-to-guide: [Enqueue](https://langchain-ai.github.io/langgraph/cloud/how-tos/enqueue_concurrent/#enqueue)
- :books: How-to-guide: [Interrupt](https://langchain-ai.github.io/langgraph/cloud/how-tos/interrupt_concurrent/#interrupt)
- :books: How-to-guide: [Rollback](https://langchain-ai.github.io/langgraph/cloud/how-tos/rollback_concurrent/#rollback)

---

## 🌐 **¿Qué es lo Siguiente?**  

En el próximo tema, exploraremos **cómo exponer nuestro grafo como una API REST o GraphQL**, permitiendo integrarlo en cualquier aplicación externa.  
