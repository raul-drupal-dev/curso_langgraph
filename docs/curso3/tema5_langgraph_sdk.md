# 🖥️ Tema 5: Uso de la SDK de LangGraph  

## 🌟 Introducción  

En el tema anterior, aprendimos a usar **LangGraph CLI** para iniciar y administrar nuestros grafos de manera local o en contenedores Docker.  
Ahora, daremos el siguiente paso: **usar la SDK de LangGraph** para interactuar con nuestro grafo de manera programática.  

📌 **¿Por qué usar la SDK de LangGraph?**  
- Permite **conectarse a grafos remotos** y ejecutarlos desde código.  
- Facilita la **gestión de ejecuciones (runs) y sesiones (threads)**.  
- Proporciona herramientas para **recuperar y almacenar datos** dentro de LangGraph.  
- Nos da acceso a características avanzadas como **streaming, Human-in-the-loop y persistencia de datos**.  

🔗 **Referencia oficial**: [Documentación de la SDK](https://langchain-ai.github.io/langgraph/cloud/reference/sdk/python_sdk_ref/)  

```python
pip install langgraph-sdk
```

---

## 🛠️ **1. Remote Graph – Ejecutar un Grafo de Forma Remota**  

LangGraph nos permite ejecutar nuestros grafos en un **servidor remoto**, en lugar de hacerlo localmente.  
Para lograrlo, podemos optar por dos métodos:  
1️⃣ **Usar la SDK con `get_client()`**  
2️⃣ **Utilizar la clase `RemoteGraph`** para interactuar con el grafo remoto directamente.  

📌 **Ejemplo de conexión a un grafo remoto:**  


```python title="Opción 1: client"
from langgraph_sdk import get_client

# Connect via SDK
url_for_cli_deployment = "http://localhost:8123"
client = get_client(url=url_for_cli_deployment)
```


```python title="Opción 2: RemoteGraph"
from langgraph.client import RemoteGraph

# Especificamos que grafo queremos usar
graph_name = <NOMBRE_DEL_GRAFO>

# Conectamos con el grafo remoto en LangGraph Cloud o servidor propio
graph = RemoteGraph(graph_name, url="http://localhost:8080")

# Definimos la entrada para el grafo
input_data = {"messages": ["Hola, ¿cómo estás?"]}

# Ejecutamos el grafo y obtenemos la respuesta
response = graph.invoke(input_data)

# Mostramos la respuesta generada
print(response)
```

🔹 En este ejemplo:  
1. Nos conectamos a un **servidor remoto** que ejecuta nuestro grafo.  
2. Enviamos una **entrada** y obtenemos una **respuesta**.  
3. Podemos utilizarlo de la misma manera que un `StateGraph` local.  

---

## 🔄 **2. Runs – Gestión de Ejecuciones en LangGraph**  

Un **run** es una instancia de ejecución de un grafo.  
Cada vez que ejecutamos un grafo, se crea un nuevo **run**, que podemos gestionar mediante la SDK.  

Un **background run** se ejecuta en segundo plano, permitiendo consultar su estado más adelante. existen dos tipos:  
- Fire and forget - Ejecutamos un run background y no esperamos a que acabe.  
- Esperamos la respuesta (blocking or polling) - Ejecutamos un run y esperamos a que responda.  

📌 **Ejemplo: Crear y recuperar un background run en LangGraph**  

```python
from langgraph_sdk import get_client
from langchain_core.messages import HumanMessage

# Connect via SDK
url_for_cli_deployment = "http://localhost:8123"
client = get_client(url=url_for_cli_deployment)

# Creamos un nuevo thread
thread = await client.threads.create()

# Iniciamos un nuevo run dentro del thread
user_input = "Realizame esta operación 5*2."
config = {"configurable": {"user_id": "Test"}}
graph_name = "calculator" 
run = await client.runs.create(thread["thread_id"], graph_name, input={"messages": [HumanMessage(content=user_input)]}, config=config)

# Mostramos el estado de ejecuciones en el thread
print(await client.runs.get(thread["thread_id"], run["run_id"]))
```

```json title="Resultado"
{
   "run_id":"1efedf2f-724e-60a1-92b6-19db6d246e4a",
   "thread_id":"8cb32afa-c3a9-4640-9e07-fedce27cc892",
   "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5",
   "created_at":"2025-02-18T12:22:05.633972+00:00",
   "updated_at":"2025-02-18T12:22:05.633972+00:00",
   "metadata":{
      "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5"
   },
   "status":"running",
   "kwargs":{
      "input":{
         "messages":[
            {
               "id":"None",
               "name":"None",
               "type":"human",
               "content":"Realizame esta operación 5*2.",
               "example":false,
               "additional_kwargs":{
                  
               },
               "response_metadata":{
                  
               }
            }
         ]
      },
      "config":{
         "metadata":{
            "created_by":"system",
            "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5"
         },
         "configurable":{
            "run_id":"1efedf2f-724e-60a1-92b6-19db6d246e4a",
            "user_id":"Test",
            "graph_id":"calculator",
            "thread_id":"8cb32afa-c3a9-4640-9e07-fedce27cc892",
            "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5",
            "langgraph_auth_user":"None",
            "langgraph_auth_user_id":"",
            "langgraph_auth_permissions":[
               
            ]
         }
      },
      "command":"None",
      "webhook":"None",
      "subgraphs":false,
      "temporary":false,
      "stream_mode":[
         "values"
      ],
      "feedback_keys":"None",
      "interrupt_after":"None",
      "interrupt_before":"None"
   },
   "multitask_strategy":"reject"
}
```

???+ Note "Nota"

    Si el estado del run aparece como `pending` o `running`, significa que el grafo aún está en ejecución.  

📌 **Si queremos esperar el resultado del run antes de continuar:**  

En caso de quisieramos esperar a que el run termine (blocking or polling) para mostrar el resultado podemos usar `client.runs.join`.

```python
# Esperamos a que termine y mostramos el output
await client.runs.join(thread["thread_id"], run["run_id"])
print(await client.runs.get(thread["thread_id"], run["run_id"]))
```

```json
{
   "run_id":"1efedf2f-724e-60a1-92b6-19db6d246e4a",
   "thread_id":"8cb32afa-c3a9-4640-9e07-fedce27cc892",
   "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5",
   "created_at":"2025-02-18T12:22:05.633972+00:00",
   "updated_at":"2025-02-18T12:22:05.633972+00:00",
   "metadata":{
      "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5"
   },
   "status":"success",
   "kwargs":{
      "input":{
         "messages":[
            {
               "id":"None",
               "name":"None",
               "type":"human",
               "content":"Realizame esta operación 5*2.",
               "example":false,
               "additional_kwargs":{
                  
               },
               "response_metadata":{
                  
               }
            }
         ]
      },
      "config":{
         "metadata":{
            "created_by":"system",
            "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5"
         },
         "configurable":{
            "run_id":"1efedf2f-724e-60a1-92b6-19db6d246e4a",
            "user_id":"Test",
            "graph_id":"calculator",
            "thread_id":"8cb32afa-c3a9-4640-9e07-fedce27cc892",
            "assistant_id":"0d1ff216-6054-5926-941a-33f750dff0a5",
            "langgraph_auth_user":"None",
            "langgraph_auth_user_id":"",
            "langgraph_auth_permissions":[
               
            ]
         }
      },
      "command":"None",
      "webhook":"None",
      "subgraphs":false,
      "temporary":false,
      "stream_mode":[
         "values"
      ],
      "feedback_keys":"None",
      "interrupt_after":"None",
      "interrupt_before":"None"
   },
   "multitask_strategy":"reject"
}
```

---

## 🔀 **3. Streaming Runs – Recibir Resultados en Tiempo Real**  

En algunos casos, queremos **recibir respuestas parciales** mientras el grafo se ejecuta.  
Para ello, LangGraph SDK nos permite hacer **streaming de runs**.  

📌 **Ejemplo de streaming de un run:**  

```python
# Creamos una ejecución en streaming
user_input = "Realizame esta operación ((4+2)*2)/2."
async for chunk in client.runs.stream(thread["thread_id"], 
                                      graph_name, 
                                      input={"messages": [HumanMessage(content=user_input)]},
                                      config=config,
                                      stream_mode="messages-tuple"):

    # Filtramos que el evento sea "messages" y que el tipo de dato sera "AIMessageChunk" que es la respuesta de la ia
    if chunk.event == "messages":
        print("".join(
            data_item['content'] 
            for data_item in chunk.data 
            if data_item.get("type") == "AIMessageChunk" and 'content' in data_item
            ), end="", flush=True)
```

```python title="Resultado"
La operación \(((4+2) \times 2) / 2\) es igual a \(6\).
```

🔹 **Ventajas del streaming:**  
✅ Nos permite **visualizar resultados en tiempo real**.  
✅ Es útil en **asistentes conversacionales** que generan respuesta progresivamente.  
✅ Reduce la latencia en respuestas largas.  

???+ Note Nota

    Para obtener los token en stream debemos de usar **`stream_mode="messages-tuple"`** 

---

## 🧵 **4. Threads – Organización de Conversaciones y Runs**  

Un **Thread** en LangGraph agrupa múltiples **runs** dentro de una misma conversación o contexto.  

📌 **Ejemplo: Crear y administrar un Thread en LangGraph**  

```python
from langchain_core.messages import convert_to_messages

thread_state = await client.threads.get_state(thread['thread_id'])
for m in convert_to_messages(thread_state['values']['messages']):
    m.pretty_print()
```

```python title="Resultado"
================================ Human Message =================================

Realizame esta operación ((4+2)*2)/2.
================================== Ai Message ==================================
Tool Calls:
  sumar (call_p75b81d9KwOL75B1MLBCqfDF)
 Call ID: call_p75b81d9KwOL75B1MLBCqfDF
  Args:
    a: 4
    b: 2
================================= Tool Message =================================
Name: sumar

6
================================== Ai Message ==================================
Tool Calls:
  multiplicar (call_iiFyJDKknNcSp7RmRL38Elld)
 Call ID: call_iiFyJDKknNcSp7RmRL38Elld
  Args:
    a: 6
    b: 2
================================= Tool Message =================================
Name: multiplicar

12
================================== Ai Message ==================================
Tool Calls:
  dividir (call_UzMKbkQoMkx3j7m5IjDvEewc)
 Call ID: call_UzMKbkQoMkx3j7m5IjDvEewc
  Args:
    a: 12
    b: 2
================================= Tool Message =================================
Name: dividir

6.0
================================== Ai Message ==================================

La operación \(((4+2) \times 2) / 2\) es igual a \(6\).
```

📌 **Ejemplo: Listar Threads en LangGraph**  

```python
# Listamos todos los threads disponibles
threads = await client.threads.search()

for t in threads:
    print(t)
```

📌 **Ejemplo: ELiminar Threads en LangGraph**  

```python
await client.threads.delete(
    thread_id="<THREAD_ID>"
)
```

🔹 **Diferencia clave entre threads y runs:**  
- Un **thread** agrupa múltiples ejecuciones relacionadas.  
- Un **run** es una única ejecución dentro de un thread.  

🔹 **Casos de uso:**  
✅ **Chatbots** que necesitan agrupar interacciones de un usuario en una misma sesión.  
✅ **Flujos conversacionales complejos** donde varios runs deben estar conectados.  

---

## 👩‍💻 **5. Human in the Loop – Integración de Intervención Humana**  

Como vimos en el **[Curso 2, Tema 4: Human in the Loop](../curso2/tema4_human_loop.md)**, LangGraph permite pausar el flujo del grafo y solicitar intervención humana.  

📌 **Ejemplo: Usar Human in the Loop con la SDK**  

```python
# Una vez detenido por la iterrupción del grafo, volveriamos a ejecutarlo de la siguiente manera 
async for chunk in client.runs.stream(thread["thread_id"], 
                                      graph_name, 
                                      input=Command(resume="SI"),
                                      config=config,                                      
                                      stream_mode="messages-tuple"):

    if chunk.event == "messages":
        print("".join(data_item['content'] for data_item in chunk.data if 'content' in data_item), end="", flush=True)
```

🔹 **Casos de uso:**  
✅ **Validar respuestas antes de ejecutarlas**.  
✅ **Solicitar confirmación de un usuario** antes de tomar una decisión crítica.  

---

## 🗄️ **6. Manejo del Store con la SDK**  

Hemos explorado **LangGraph Store** en los **Temas 1, 2 y 3** de este curso.  
Ahora veremos cómo interactuar con estos datos mediante la SDK.  

📌 **Ejemplo: Almacenar datos en el Store**  

```python
from uuid import uuid4

# Almacenamos los datos
await client.store.put_item(
    ("testing", "Test"),
    key=str(uuid4()),
    value={"todo": "Probando añadir desde la SDK"},
)
```

📌 **Ejemplo: Buscar información en la base de datos de LangGraph**  

```python
# Buscamos todos los valores en un namespace específico
items = await client.store.search_items(
    ("testing", "Test"),
    limit=5,
    offset=0
)

# Mostramos los resultados de la búsqueda
for item in items['items']:
    print(item)
```

???+ Note "Nota"

    Tal y como vimos en los temas sobre `Store`, puedes consultar más métodos en la documentación oficial:  
    **[StoreClient](https://langchain-ai.github.io/langgraph/cloud/reference/sdk/python_sdk_ref/#langgraph_sdk.client.StoreClient)**  


---

## ✨ **Conclusión**  

La SDK de LangGraph nos permite **conectar, ejecutar y gestionar grafos remotos** de manera eficiente.  

✅ Podemos ejecutar grafos alojados en **LangGraph Cloud** o **Docker**.  
✅ Podemos administrar **ejecuciones (runs) y sesiones (threads)**.  
✅ Podemos utilizar **streaming** para recibir resultados en tiempo real.  
✅ Podemos integrar **Human in the Loop** y manipular datos almacenados en **LangGraph Store**.  

🚀 **Con estos conocimientos, podemos integrar LangGraph en cualquier aplicación de manera flexible y escalable.**  

---

## 🧑‍🏫 **¿Qué Hemos Aprendido?**  

- Cómo conectarnos a grafos remotos usando **RemoteGraph**.  
- Cómo gestionar ejecuciones mediante **background runs**.  
- Cómo recibir datos en tiempo real con **streaming runs**.  
- Cómo organizar interacciones con **threads**.  
- Cómo usar **Human in the Loop** dentro de la SDK.  
- Cómo acceder a **datos almacenados en LangGraph Store**.  

---

## 🔎 Recursos:

- :simple-googlecolab: Ver notebook en [Google Colab](https://colab.research.google.com/drive/15axod42AkitpQzDsB_IF_i9sHLslLZ7L?usp=sharing)
- :books: Concept: [LangGraph SDK](https://langchain-ai.github.io/langgraph/concepts/sdk/)
- :books: How-to-guide: [Runs](https://langchain-ai.github.io/langgraph/how-tos/#runs)
- :books: How-to-guide: [RemoteGraph](https://langchain-ai.github.io/langgraph/how-tos/use-remote-graph/)
- :books: How-to-guide: [stream_mode](https://langchain-ai.github.io/langgraph/cloud/how-tos/stream_messages/#stream-graph-in-messages-mode)
- :books: How-to-guide: [Check status of your threads](https://langchain-ai.github.io/langgraph/cloud/how-tos/check_thread_status/)
- :books: API Reference: [Langgraph SDK](https://langchain-ai.github.io/langgraph/cloud/reference/sdk/python_sdk_ref/)
- :books: Class: [StoreClient](https://langchain-ai.github.io/langgraph/cloud/reference/sdk/python_sdk_ref/#langgraph_sdk.client.StoreClient)
- :books: Ejemplos: [Background Runs](https://langchain-ai.github.io/langgraph/cloud/how-tos/background_run/)

---

## 🌐 **¿Qué es lo Siguiente?**  

En el próximo tema, exploraremos **Doble-Texting**, una técnica para manejar múltiples mensajes simultáneos en un grafo.  
Analizaremos diferentes estrategias como **reject, enqueue, interrupt y rollback** para gestionar estos eventos correctamente.  