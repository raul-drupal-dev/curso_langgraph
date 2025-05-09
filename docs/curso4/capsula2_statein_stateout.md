# Cápsula 4: State In & State Out

## 🧠 Entendiendo el `State` en LangGraph

Uno de los conceptos más importantes (y menos explorados a fondo hasta ahora) es el de los **`states`**. En LangGraph, cada ejecución del grafo trabaja sobre un `state`, que es simplemente un diccionario (o estructura similar) que contiene toda la información que fluye de un nodo a otro.

Este `state` puede ser:  
- Un **estado inicial** (`input`), que alimenta al primer nodo.  
- Un **estado interno**, que se va actualizando entre nodos (ej: flags, metadatos, historial).  
- Un **estado de salida** (`output`), que representa el resultado final del grafo.  

---

## 🧩 ¿Cómo definimos un `State`?

En este curso usamos `TypedDict` para definir nuestros `states`, lo cual nos permite describir su estructura con claridad sin necesidad de validaciones complejas.

!!! example "Ejemplo simple de TypedDict para un state"
    Un estado que guarda la entrada del usuario, el historial del chat y la respuesta final.

    ```
    from typing import TypedDict, List

    class ChatState(TypedDict):
        input: str
        history: List[str]
        response: str
    ```

Esto nos da una base clara para saber qué información fluye dentro del grafo.

---

## 🛠️ Separando nuestros `states`: entrada, internos y salida

Veamos cómo podemos **separar la lógica del `state` en distintas fases** para organizar mejor nuestros flujos:

### 1️⃣ `State In`: la entrada

Aquí solo necesitamos lo mínimo necesario para iniciar el flujo, por ejemplo, el mensaje del usuario.

```python
# State In (solo entrada)
from typing import TypedDict

class InputState(TypedDict):
    input: str

initial_state = InputState(input="¿Cuál es la capital de España?")
print(initial_state)
```

---

### 2️⃣ `State Interno`: gestionando datos temporales

Podemos usar campos como `metadata`, `flags`, o `intermediate_results` para compartir información entre nodos sin que necesariamente formen parte del resultado final.

```python
# State Interno (con campos extra para el flujo)
from typing import TypedDict, List

class InternalState(TypedDict):
    input: str
    metadata: str
    flag: str
    history: List[str]

internal_state = InternalState(
    input="¿Cuál es la capital de España?",
    metadata="interacción 1",
    flag="OK",
    history=["Usuario: ¿Cuál es la capital de España?"]
)
print(internal_state)
```

---

### 3️⃣ `State Out`: resultado final del grafo

Una vez que el grafo termina, podemos definir exactamente qué campos nos interesa exponer a la app como **resultado final**.

```python
# State Out (estado final con la respuesta y el historial)
from typing import TypedDict, List

class OutputState(TypedDict):
    response: str
    history: List[str]

output_state = OutputState(
    response="La capital de España es Madrid.",
    history=[
        "Usuario: ¿Cuál es la capital de España?",
        "IA: La capital de España es Madrid."
    ]
)
print(output_state)

```

---

## Ejemplo completo

```python hl_lines="21 26 30"
from typing import TypedDict, List
from langgraph.graph import StateGraph, END
from langgraph.graph.message import add_messages

# 1. Definimos los estados
class InputState(TypedDict):
    input: str

class InternalState(TypedDict):
    input: str
    metadata: str
    flag: str
    history: List[str]

class OutputState(TypedDict):
    response: str
    history: List[str]
# 2. Nodos del grafo

# Nodo de entrada: inicializa el historial
def nodo_entrada(state: InputState) -> InternalState:
    history = [f"Usuario: {state['input']}"]
    return {**state, "history": history}

# Nodo intermedio: agrega metadata y un flag
def nodo_interno(state: InternalState) -> InternalState:
    return {**state, "metadata": "proceso_intermedio", "flag": "OK"}

# Nodo de salida: genera respuesta y actualiza historial
def nodo_salida(state: InternalState) -> OutputState:
    respuesta = "La capital de España es Madrid."  # Supongamos una respuesta dummy
    new_history = state["history"] + [f"IA: {respuesta}"]
    return {
        "response": respuesta,
        "history": new_history
    }

# 3. Construimos el grafo
builder = StateGraph(InputState)

builder.add_node("entrada", nodo_entrada)
builder.add_node("interno", nodo_interno)
builder.add_node("salida", nodo_salida)

builder.set_entry_point("entrada")
builder.add_edge("entrada", "interno")
builder.add_edge("interno", "salida")
builder.add_edge("salida", END)

graph = builder.compile()

# 4. Ejecutamos el grafo
initial_state = {
    "input": "¿Cuál es la capital de España?"
}

final_state = graph.invoke(initial_state)
print(final_state)
```

```Python title="Resultado"
{
  'response': 'La capital de España es Madrid.',
  'history': [
    'Usuario: ¿Cuál es la capital de España?',
    'IA: La capital de España es Madrid.'
  ]
}
```

---

## 🧩 Ventajas de trabajar con estados divididos

Diseñar tu `state` dividiéndolo en entrada, internos y salida trae muchas ventajas:

- **Claridad**: Cada campo tiene un propósito específico. Sabes qué datos llegan, cuáles son temporales y cuáles se exponen.
- **Encapsulamiento**: Puedes mantener campos internos como `metadata`, `flags`, `steps`, etc., sin preocuparte de filtrarlos al final.
- **Simplicidad para la app**: El output es limpio. Solo contiene lo que tu aplicación necesita, sin basura técnica.
- **Reutilización**: Puedes adaptar el grafo fácilmente a distintos contextos cambiando solo la parte final del `state`.

!!! tip "Información privada y útil"
    Usar un `state` interno te permite almacenar datos sensibles, auxiliares o de control sin tener que incluirlos en el resultado final. ¡Perfecto para trabajar con contextos complejos de forma segura!

---

## 🧭 Buenas prácticas al diseñar tus `states`

!!! tip "Consejos útiles"
    - Empieza simple: define solo lo que necesitas.
    - Usa nombres claros: `input`, `output`, `metadata`, `flag`, etc.
    - No todo tiene que salir al final: los estados internos son solo para el flujo.
    - Usa `TypedDict` o `BaseModel` si necesitas validación, pero no compliques de más.

---

## ✅ Resumen

- El `state` es el **vehículo de datos** que recorre todo tu grafo.
- Puedes dividirlo mentalmente en tres partes: entrada, internos y salida.
- Esto te ayuda a estructurar flujos más claros y mantener tu lógica desacoplada.
- En esta cápsula vimos cómo definir estos estados con `TypedDict` y cómo usarlos dentro de un grafo.






