# 💬 Cápsula 1: Messages

## 💬 Qué son los Messages y para qué sirven

En LangGraph (y en LangChain en general), los `Messages` son objetos que representan piezas de conversación o interacción entre el humano, el modelo de lenguaje (IA), el sistema o herramientas externas.

Estos mensajes encapsulan el contenido del diálogo y ayudan a estructurar correctamente las interacciones para que los modelos entiendan el contexto de forma clara y ordenada.

!!! tip "Piensa en los `Messages` como los actores de una conversación"
    Cada uno tiene su rol definido: algunos hablan, otros dan contexto, y otros entregan resultados de herramientas.

---

## 🧭 ¿Cuándo se recomienda su uso y por qué? 

Usar `Messages` te permite construir flujos conversacionales **más claros, escalables y reutilizables**. Son especialmente útiles cuando trabajamos con:

- 🔁 Conversaciones multi-turno (más de una interacción)
- 🧠 Modelos que necesitan mantener el contexto
- 🔧 Herramientas (como `ToolMessage`) en agentes o workflows

!!! note "Ventajas de los Messages"
    - Claridad: cada parte del mensaje tiene un tipo específico.
    - Compatibilidad: son totalmente compatibles con los modelos de LangChain.
    - Escalabilidad: fáciles de componer, almacenar y modificar.

---

## 🧱 Tipos de Messages

En LangChain, disponemos de varios tipos de mensajes. Cada uno tiene una función distinta, y todos heredan de la clase base `BaseMessage`.

### 🧑‍💻 `HumanMessage`

Representa lo que dice el usuario humano.

**Atributos**:  
- `content`: el texto que envía el humano  
- `name`: (opcional) útil si hay múltiples usuarios

**Ejemplo**:

```python
from langchain_core.messages import HumanMessage

msg = HumanMessage(content="Hola, ¿me puedes ayudar con un resumen?")
print(msg)
```

---

### 🤖 `AIMessage`

Representa la respuesta generada por el modelo de IA.

**Atributos**:  
- `content`: la respuesta de la IA  
- `name`: (opcional) nombre de la IA si se desea especificar  
- `tool_calls`: (opcional) lista de herramientas llamadas por la IA  
- `function_call`: (opcional) si se usó una función  

**Ejemplo**:

```python
from langchain_core.messages import AIMessage

msg = AIMessage(content="Claro, ¿sobre qué tema necesitas el resumen?")
print(msg)
```

---

### 🧰 `ToolMessage`

Representa la respuesta de una herramienta que ha sido llamada por el modelo.

**Atributos**:  
- `tool_call_id`: el ID que relaciona esta respuesta con su llamada  
- `content`: lo que responde la herramienta

**Ejemplo**:

```python
from langchain_core.messages import ToolMessage

msg = ToolMessage(tool_call_id="tool-1", content="La temperatura actual es 21°C.")
print(msg)
```

---

### 🛠️ `FunctionMessage` *(deprecated en algunos casos, revisar compatibilidad)*

Usado para representar mensajes derivados de funciones específicas.

**Atributos**:  
- `name`: nombre de la función  
- `content`: resultado textual  

**Ejemplo**:

```python
from langchain_core.messages import FunctionMessage

msg = FunctionMessage(name="get_weather", content="Hoy hace sol con 21°C.")
print(msg)
```

---

### ⚙️ `SystemMessage`

Proporciona instrucciones al modelo. No lo ve el usuario, solo el modelo.

**Atributos**:  
- `content`: las instrucciones del sistema  
- `name`: (opcional)  

**Ejemplo**:

```python
from langchain_core.messages import SystemMessage

msg = SystemMessage(content="Eres un asistente útil y conciso.")
print(msg)
```

---

## 🔎 Recursos:

- 📚 Definición: [Messages](https://python.langchain.com/docs/concepts/messages/)

---

## 🧠 Resumen

Hemos aprendido que:

- Los `Messages` son piezas clave para estructurar interacciones.
- Usarlos mejora la claridad y mantenimiento de nuestro flujo conversacional.
- Hay distintos tipos con roles bien definidos: usuario, IA, sistema y herramientas.
- Cada mensaje tiene atributos específicos y se puede crear fácilmente con clases de LangChain.

---

## 🔍 Consideraciones finales y ayudas

!!! warning "¡Cuidado con el orden!"
    El orden en que se agregan los `Messages` importa mucho. El modelo interpreta la conversación como un historial cronológico, ¡no lo confundas!

Si estás trabajando con agentes o LangGraph, esta forma de modelar la conversación se vuelve esencial. ¡Dominar los `Messages` te dará control total sobre cómo interactúan los nodos de tu grafo!

---
