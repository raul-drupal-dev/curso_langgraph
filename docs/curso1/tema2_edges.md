# 🎯 Tema 2: Edges (Conexiones)

## 🧩 ¿Qué son los Edges?  
Los **edges** son las conexiones entre nodos que dirigen el flujo de datos de un nodo a otro. Estas conexiones pueden ser:  

- **Directas:** Conectan un nodo con el siguiente sin condiciones.  
- **Condicionales:** Deciden dinámicamente qué nodo visitar a continuación basándose en una función lógica.  

En este ejemplo, utilizaremos un flujo de decisión aleatorio para mostrar cómo se pueden agregar edges y bifurcaciones.  

---

## 🛠️ Ejemplo Práctico: Construyendo un Grafo con Edges  

Usando el código usado anteriormente con los nodos, mostramos el código de ejemplo que ilustra cómo agregar edges entre nodos:  

```python
def node_1(state):
    print("---Node 1---")
    return {"graph_state": state['graph_state'] + " Me gusta"}

def node_2(state):
    print("---Node 2---")
    return {"graph_state": state['graph_state'] + " programar!"}

def node_3(state):
    print("---Node 3---")
    return {"graph_state": state['graph_state'] + " salir en bici!"}
```

---

### 🔍 Explicación Paso a Paso  

#### 📌 Definición de Nodos  

```python
from langgraph.graph import StateGraph, START, END

builder = StateGraph(State)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)
```

- **`node_1`**: Añade al estado `"Me gusta"`.  
- **`node_2`**: Añade al estado `"programar!"`.  
- **`node_3`**: Añade al estado `"salir en bici!"`.  

En este caso, `node_1` actúa como punto de partida para las decisiones que se tomarán posteriormente.  

---

#### 🔄 Decisión con Condiciones  

```python
import random
from typing import Literal

def decide_hooby(state) -> Literal["node_2", "node_3"]:
    if random.random() < 0.5:
        return "node_2"
    return "node_3"
``` 


La función `decide_hooby` decide, de forma aleatoria, si el flujo debe continuar hacia `node_2` o `node_3`.  

- **50%** de probabilidad de ir a `node_2` (programar).  
- **50%** de probabilidad de ir a `node_3` (salir en bici).  

Este edge condicional permite que el grafo tenga múltiples rutas de ejecución.  

---

#### 🏗️ Construcción del Grafo  

```python hl_lines="1 2 3 4"
builder.add_edge(START, "node_1")
builder.add_conditional_edges("node_1", decide_hooby)
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()
```  

- **`add_edge`**: Conecta directamente `START` con `node_1`.  
- **`add_conditional_edges`**: Desde `node_1`, el flujo se bifurca condicionalmente hacia `node_2` o `node_3`.  
- **`add_edge` (Final)**: Ambos nodos (`node_2` y `node_3`) terminan en el nodo final `END`.  

---

### 📈 Visualizando el Grafo  

Una vez construido, podemos visualizar el grafo:  

```python
from IPython.display import Image, display
display(Image(graph.get_graph().draw_mermaid_png()))
``` 

---

## ⚙️ Invocando el Grafo  

Para ejecutar el grafo y ver su comportamiento, utilizamos:  

```python
graph.invoke({"graph_state" : "Hola, me llamo Raul."})
```
```python title="Resultado 1"
---Node 1---
---Node 3---
{'graph_state': 'Hola, me llamo Raul. Me gusta programar!'}
```
```python title="Resultado 2"
---Node 1---
---Node 3---
{'graph_state': 'Hola, me llamo Raul. Me gusta salir en bici!'}
```

---

## 🔎 Recursos:

- :books: Guía: [Edges](https://langchain-ai.github.io/langgraph/concepts/low_level/#edges)

---

## 🧩 ¿Qué Hemos Aprendido?  

- **Edges (Conexiones):** Son las rutas que conectan nodos en el grafo, permitiendo el flujo de información de un nodo a otro.  
- **Edges Condicionales:** Permiten bifurcaciones en el flujo, tomando decisiones dinámicas que afectan el camino que sigue el grafo.  
- **Flujo de Trabajo Dinámico:** Al conectar nodos de forma condicional, podemos crear grafos más complejos y adaptativos, generando diferentes resultados según la lógica aplicada.  
- **Construcción de Grafos:** Hemos aprendido a agregar nodos, edges y compilar el grafo usando `StateGraph` y `add_edge`.  

---

## 🌐 ¿Qué es lo Siguiente?  

En el siguiente tema, profundizaremos en **State Schema y Memoria**. Aprenderemos:  
- **Cómo almacenar y mantener el estado** a lo largo del flujo del grafo.  
- **Estrategias para preservar memoria** en LangGraph.  
- **Esquemas de estado personalizados** para que el grafo retenga y modifique información de manera continua.  

¡Nos vemos en el próximo tema! 🚀  


