# 🖥️ Tema 8: LangGraph Studio – Visualizando y Depurando Grafos  

## 🌟 ¿Qué es LangGraph Studio?  

[**LangGraph Studio**](https://github.com/langchain-ai/langgraph-studio){target="_blank"} es una herramienta interactiva que permite **visualizar, depurar y probar grafos** de manera intuitiva.  
Proporciona una interfaz gráfica donde podemos observar en tiempo real cómo fluye la información entre los nodos de nuestro grafo.  

LangGraph Studio nos permite:  
✅ **Inspeccionar la ejecución de los nodos** en tiempo real.  
✅ **Visualizar el flujo de datos** dentro del estado del grafo.  
✅ **Depurar errores y optimizar la lógica** de manera eficiente.  
✅ **Probar interacciones con el grafo** sin necesidad de escribir código adicional.  

🔹 **¿Por qué es útil?**  
En grafos complejos, donde múltiples nodos interactúan de manera simultánea, **LangGraph Studio nos ayuda a entender su comportamiento y a detectar posibles fallos** sin necesidad de imprimir logs manualmente.  

???+ warning "Atención"

    **Actualmente, LangGraph Studio solo está disponible para macOS.**


---

## 🖼️ ¿Cómo Se Ve LangGraph Studio?  

LangGraph Studio presenta una interfaz donde podemos visualizar nuestros grafos con:  
🔹 **Nodos y conexiones** representadas gráficamente.  
🔹 **Estados actualizados en tiempo real** tras cada ejecución.  
🔹 **Opciones de inspección** para ver los valores almacenados en cada paso del flujo.  

A continuación, mostramos una imagen de LangGraph Studio en acción:  

![LangGraph Studio](../assets/img/curso2/tema8/image.gif)  
📷 *Captura de pantalla de LangGraph Studio*  

---

## 🛠️ ¿Cómo Configurar LangGraph Studio?  

Para habilitar LangGraph Studio en nuestro entorno, simplemente debemos **iniciar el servidor local** y conectar nuestro grafo.  

La estructura final de nuestro proyecto debería ser algo así:

```bash title="Estrutura de archivos"
my-app/
├── my_agent # Todo el codigo de nuestro proyecto aquí
│   ├── utils # Las útiles de nuestro grafo
│   │   ├── __init__.py
│   │   ├── tools.py # Definimos nuestras herramientas aquí
│   │   ├── nodes.py # Definimos nuestros nodos aquí
│   │   └── state.py # Deinimos nuestros estados aquí
│   ├── requirements.txt # Agregamos nuestras dependencias aquí
│   ├── __init__.py
│   └── agent.py # El codigo de contrucción de nuestro agente aquí
├── .env # Vairables de entorno
└── langgraph.json # Archivo de configuración de LangGraph
```

---

## 🎯 Ejemplo Práctico: Visualizando un Grafo  

Veamos cómo podemos ejecutar un grafo con LangGraph Studio y analizar su comportamiento.  

📌 **Pasos:**  
1️⃣ **Definir un grafo sencillo** con algunos nodos básicos.  
2️⃣ **Conectarlo a LangGraph Studio** para ver su ejecución en tiempo real.  
3️⃣ **Ejecutar el grafo y analizar la visualización** en la interfaz.  

```yaml title="requirements.txt"
# Definimos todos los modulos de python que vamos a necesitar.
langgraph
langchain_core
langchain_openai
```

```yaml title=".env"
OPENAI_API_KEY=key # Ponemos nuestra clave de open AI aquí
```

```python title="agent.py"
# my_agent/agent.py
# Nuestro ejemplo de calculadora
from langchain_openai import ChatOpenAI
from langgraph.graph import MessagesState
from langchain_core.messages import HumanMessage, SystemMessage
from langgraph.graph import START, StateGraph, END
from langgraph.prebuilt import tools_condition
from langgraph.prebuilt import ToolNode

# Primero definimos nuestras herramientas:
def sumar(a, b):
  """Usamos esta función para sumar dos números
  Args:
    a (int): Primer número a sumar
    b (int): Segundo número a sumar
  Returns:
    Un int or float con la suma de los dos números
  """
  return  a + b

def restar(a, b):
  """Usamos esta función para restar dos números
  Args:
    a (int): Primer número a restar
    b (int): Segundo número a restar
  Returns:
    Un int or float con la resta de los dos números
  """
  return a - b

def multiplicar(a, b):
  """Usamos esta función para multiplicar dos números
  Args:
    a (int): Primer número a multiplicar
    b (int): Segundo número a multiplicar
  Returns:
    Un int or float con la multiplicación de los dos números
  """
  return a * b

def dividir(a, b):
  """Usamos esta función para dividir dos números
  Args:
    a (int): Primer número a dividir (Dividendo)
    b (int): Segundo número a dividir (Divisor)
  Returns:
    Un int or float con la división de los dos números
  """
  if b == 0:
    return "No se puede dividir por cero"
  return a / b


# Vinculamos las tools al modelo de lenguaje.
tools = [sumar, restar, multiplicar, dividir]
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools, parallel_tool_calls=False)

# Le definimos al LLM como queremos que evalue las operaciones.
sys_msg = """
  Resuelve la ecuación aplicando las siguientes reglas:
  1. Evalúa las operaciones dentro de paréntesis primero.
  2. Luego, evalúa multiplicaciones y divisiones de izquierda a derecha.
  3. Finalmente, evalúa sumas y restas de izquierda a derecha.
  4. Usa las herramientas proporcionadas: sumar, restar, multiplicar, dividir.
  """

# Creamos nuestro asistente que se encargará de llamar a las Tools necesarias
# tantas veces como sea necesario.
def assistant(state: MessagesState):
    return {"messages": [llm_with_tools.invoke([sys_msg] + state["messages"])]}


# Configuramos el flujo
builder = StateGraph(MessagesState)
# Añadimos nodos
builder.add_node("assistant", assistant)
builder.add_node("tools", ToolNode(tools))

# Definimos los edges y el flujo del grafo
builder.add_edge(START, "assistant")
builder.add_conditional_edges("assistant", tools_condition)
builder.add_edge("tools", "assistant")
graph = builder.compile()
```

```json title="langgraph.json"
{
  "dependencies": ["./my_agent"],
  "graphs": {
    "agent": "./my_agent/agent.py:graph"
  },
  "env": ".env"
}
```

Una vez configurado, podremos ver en LangGraph Studio cómo la información fluye entre los nodos y cómo se actualizan los datos en cada paso.

Para ello, simplemente:  
1️⃣ Abrimos la aplicación de LangGraph Studio.  
2️⃣ Iniciamos sesión con nuestra cuenta de LangSmith.  
3️⃣ Subimos la carpeta de nuestro proyecto para comenzar a visualizar la ejecución del grafo.  


---

## ✨ Conclusión  

LangGraph Studio es una herramienta esencial para cualquier desarrollador que trabaje con grafos en LangGraph.  

✅ Nos permite **visualizar la ejecución en tiempo real**, facilitando la depuración.  
✅ Mejora la **comprensión del flujo de datos** dentro del grafo.  
✅ Facilita la **optimización y el ajuste de la lógica** de los nodos.  

Si trabajamos con grafos complejos, **LangGraph Studio se convierte en una herramienta indispensable** para analizar, mejorar y depurar nuestros flujos. 🚀  

---

## 🧑‍🏫 ¿Qué Hemos Aprendido?  

- **Qué es LangGraph Studio y cómo nos ayuda en el desarrollo de grafos.**  
- **Cómo visualizar, depurar y probar la ejecución de nuestros flujos.**  
- **Cómo iniciar el servidor y conectar nuestros grafos a LangGraph Studio.**  

---

## 🌐 ¿Qué es lo Siguiente?  

En el próximo tema, exploraremos **LangSmith**, una herramienta avanzada para analizar y optimizar el comportamiento de nuestros grafos con métricas detalladas y mayor control sobre la ejecución.  
