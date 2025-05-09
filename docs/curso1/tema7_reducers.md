# 🔄 Tema 7: Reducers

## 🚀 ¿Qué es un Reducer en LangGraph?  

Los **reducers** en LangGraph permiten consolidar y procesar datos provenientes de diferentes nodos o caminos.  
Cuando un grafo sigue **flujos paralelos o múltiples bifurcaciones**, los reducers **recogen, combinan o modifican** los resultados, facilitando una salida coherente y organizada.  

???+ Tip "Consejo"

    **Piensa en un reducer como un colector de datos** que une los resultados de distintas partes del grafo en un solo punto.  

---

### 🧠 ¿Por qué son Importantes los Reducers?  

- **Consolidación de Datos:** Reúne información de diferentes nodos y la almacena en el estado.  
- **Flujos Complejos:** Permite manejar y combinar respuestas de múltiples caminos paralelos.  
- **Evitan Sobrescrituras No Deseadas:** Sin un reducer, cada actualización sobrescribe el valor existente.  

---

## ⚙️ ¿Cómo Funciona un Reducer?  

Cuando un nodo devuelve datos parciales del estado, el reducer **decide cómo aplicar esos datos**:  
- **Sin Reducer:** El valor existente se **sobrescribe.**  
- **Con Reducer:** Los datos se **combinan o acumulan** siguiendo una lógica definida.  

👉 **Ejemplo:** Si tienes una lista de mensajes, un reducer puede añadir nuevos mensajes al final, en lugar de borrar el historial anterior.  

---

## 🔍 Ejemplo A: Sin Reducer (Sobrescritura Directa)  

En este caso, cada vez que un nodo devuelve un valor, sobrescribe el valor anterior:  

```python
from typing_extensions import TypedDict
from langchain_core.messages import AnyMessage

class State(TypedDict):
    messages: list[AnyMessage]  # Sin reducer (sobrescribe)
    response: str
    department: str
``` 

---

### 🔎 Explicación:  
- La clave `messages` no tiene un reducer asignado.  
- **Cada actualización de mensajes reemplaza por completo la lista anterior.**  
- El último mensaje será el único que quede en el estado.  

---

## 📋 Ejemplo B: Con Reducer (Acumulación de Mensajes)  

Usamos `Annotated` y `add_messages` para acumular mensajes en lugar de sobrescribirlos:  
 
```python hl_lines="5"
from typing import Annotated
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: Annotated[list[AnyMessage], add_messages]  # Con reducer (acumula)
    response: str
    department: str
``` 

- **`Annotated`** permite añadir metadatos a `messages` para definir un reducer.  
- **`add_messages`** acumula mensajes en el historial, evitando sobrescrituras.  
- Cada vez que un nodo añade un mensaje, este se agrega al final de la lista.  


### 🔍 El Papel del Reducer (add_messages)

Cuando definimos:

```python
messages: Annotated[list[AnyMessage], add_messages]
```

Estamos indicando que cuando un nodo devuelve una actualización parcial del estado, el reducer `add_messages` se encargará de combinar el valor nuevo con el existente.

Esto implica que:

- Si un nodo devuelve `{"messages": [...]}`, el reducer `add_messages` sumará los mensajes nuevos a la lista actual.
- Si no hay un reducer definido, el nuevo valor reemplazará al valor anterior.

???+ Info

    Los reducers **solo se activan** cuando un nodo devuelve una actualización parcial o el estado completo. Es importante tener esto en cuenta si planeamos realizar otras modificaciones manualmente, ya que los cambios directos en el estado **no pasarán por el reducer**.  


---

## 🛠️ Ejemplo Completo: Comparando con y sin Reducer  

Vamos a construir un grafo de chatbot que:  
1. Recibe el mensaje del usuario.  
2. Redirige al nodo correspondiente (soporte, ventas, general).  
3. Añade respuestas al historial de mensajes usando un reducer.  

Veremos cómo cambia el resultado al usar un reducer para `messages`.  

```python  hl_lines="5 10"
from langchain_core.messages import HumanMessage, AIMessage
from langgraph.graph import StateGraph, START, END

def user_input(state: State):
    print("--- Nodo 1: Entrada del Usuario ---")
    return {"messages": HumanMessage(content="Hola, tengo una pregunta.")}

def support_node(state: State):
    print("--- Nodo de Soporte Técnico ---")
    state["response"] = "Soporte"
    return {"messages": AIMessage(content="Te estamos transfiriendo a soporte.")}


def route_request(state: State) -> str:
    return "support_node"

builder = StateGraph(State)
builder.add_node("user_input", user_input)
builder.add_node("support_node", support_node)

builder.add_edge(START, "user_input")
builder.add_edge("user_input", "support_node")
builder.add_edge("support_node", END)

graph = builder.compile()
```  
![grafo reducers](../assets/img/curso1/tema7/image.png)

---

## 🚀 Invocando el Grafo  

Probamos el grafo sin reducer y luego con `add_messages` para observar la diferencia.  

```python
graph.invoke({"messages": [], "response": "", "department": ""})
``` 

```python
# Sin reducer (sobrescribe)
{'messages': [AIMessage(content="Te estamos transfiriendo a soporte.")],
 'response': 'Soporte',
 'department': ''}
``` 
```python
# Con reducer (acumula)
{'messages': [
  HumanMessage(content="Hola, tengo una pregunta."),
  AIMessage(content="Te estamos transfiriendo a soporte.")
 ],
 'response': 'Soporte',
 'department': ''}
``` 

- **Sin Reducer:** Solo el último mensaje aparece en el estado.  
- **Con Reducer:** El historial acumula todos los mensajes y respuestas.  

---

## ✨ ¿Cómo Crear un Reducer Personalizado?  

Si necesitas una lógica específica que `add_messages` no cubre, puedes crear tu propio reducer.  

A continuación, definimos un reducer personalizado que **cuenta cuántos mensajes se han procesado** y actualiza el estado con ese total:  
 
```python
from typing import Annotated, Union
from typing_extensions import TypedDict
from langchain_core.messages import AnyMessage, HumanMessage, AIMessage
from langgraph.graph.message import add_messages
from langgraph.graph import StateGraph, START, END

# Definir un reducer personalizado que cuenta los mensajes
def count_messages(existing: int, new: Union[list[AnyMessage], AnyMessage]) -> int:
    # Si 'new' es una lista, sumamos su longitud. Si es un solo mensaje, sumamos 1.
    if isinstance(new, list):
        return existing + len(new)
    return existing + 1

# Definir el estado con historial de mensajes y contador total
class StateCustomReducer(TypedDict):
    messages: Annotated[list[AnyMessage], add_messages]
    total_messages: Annotated[int, count_messages]
    response: str

# Nodo de entrada del usuario
def user_input(state: StateCustomReducer):
    print("--- Nodo 1: Entrada del Usuario ---")
    state["messages"].append(HumanMessage(content="Hola, tengo una consulta."))
    return state

# Nodo de respuesta de soporte
def support_node(state: StateCustomReducer):
    print("--- Nodo de Soporte Técnico ---")
    state["messages"].append(AIMessage(content="Te estamos transfiriendo a soporte técnico."))
    state["response"] = "Soporte"
    return state

# Nodo de ventas
def sales_node(state: StateCustomReducer):
    print("--- Nodo de Ventas ---")
    state["messages"].append(AIMessage(content="Te conectamos con el equipo de ventas."))
    state["response"] = "Ventas"
    return state

# Router que decide a qué nodo enviar
def route_request(state: StateCustomReducer) -> str:
    if "compra" in state["messages"][-1].content.lower():
        return "sales_node"
    return "support_node"

# Construcción del grafo
builder = StateGraph(StateCustomReducer)
builder.add_node("user_input", user_input)
builder.add_node("support_node", support_node)
builder.add_node("sales_node", sales_node)

builder.add_edge(START, "user_input")
builder.add_conditional_edges("user_input", route_request)
builder.add_edge("support_node", END)
builder.add_edge("sales_node", END)

# Compilar el grafo
graph_with_custom_reducer = builder.compile()

from IPython.display import Image, display
display(Image(graph_with_custom_reducer.get_graph().draw_mermaid_png()))
``` 

Ejecutamos el grafo y verificamos cómo el estado se actualiza con el número total de mensajes.  

```python
graph_with_custom_reducer.invoke({"messages": [], "total_messages": 0, "response": ""})
```  
 
```python title="Resultado"
{'messages': [
    HumanMessage(content="Hola, tengo una pregunta."),
    AIMessage(content="Te estamos transfiriendo a soporte.")
  ],
  'total_messages': 2,
  'response': 'Soporte'
}
``` 

El estado reflejará tanto el historial de `mensages` como el conteo total.  

---

## 🔎 Recursos:

- :simple-googlecolab: Ver notebook en [Google Colab](https://colab.research.google.com/drive/1F0rB1FALCbnOrSeJ6lF2zbb_YPqTjx4g?usp=sharing)
- :books: Definición [Reducers](https://langchain-ai.github.io/langgraph/concepts/low_level/?h=reducers#reducers)


---

## 🧑‍🏫 ¿Qué Hemos Aprendido?  

- **Reducers:** Controlan cómo se actualiza el estado durante el flujo del grafo.  
- **add_messages:** Permite acumular mensajes, evitando sobrescrituras.  
- **Reducers Personalizados:** Proporcionan flexibilidad para definir cualquier lógica de actualización.  

---

## 🌐 ¿Qué es lo Siguiente?  

En el próximo tema, exploraremos **Tools (Herramientas)**, viendo cómo integrar funciones externas y APIs dentro del grafo para ampliar sus capacidades.  



