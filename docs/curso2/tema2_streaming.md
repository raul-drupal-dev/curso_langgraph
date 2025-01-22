# 🌊 Tema 3: Streaming – Procesamiento en Tiempo Real  

## 🚀 ¿Qué es el Streaming en LangGraph?  

El **streaming** en LangGraph es una técnica que permite procesar datos **en tiempo real**, emitiendo resultados de manera progresiva mientras el grafo se ejecuta.  
A diferencia de los flujos tradicionales, donde el resultado se genera solo al finalizar, el streaming proporciona una **respuesta parcial y continua**, lo que mejora la interacción con el usuario y permite manejar flujos dinámicos de datos.  

---

## 🧠 ¿Por Qué es Importante el Streaming?  

1. **Interactividad Inmediata:** Mejora la experiencia del usuario al ofrecer resultados parciales en lugar de esperar a que termine todo el flujo.  
2. **Optimización del Tiempo:** Permite iniciar acciones mientras el grafo sigue trabajando.  
3. **Escenarios Complejos:** Ideal para aplicaciones en tiempo real como chatbots, procesamiento de grandes volúmenes de datos, y más.  

El streaming convierte un grafo en una herramienta más **reactiva y adaptable**.  

---

## 🌟 Streaming Graph Outputs  

El **streaming de outputs** permite procesar y emitir los resultados del grafo de manera progresiva, a medida que los nodos generan información.  

LangGraph proporciona cinco modos principales de streaming:  

- **`stream_mode='updates'`:** Emite actualizaciones parciales del estado a medida que se procesan los nodos.  
- **`stream_mode='values'`:** Emite los valores completos del estado resultante después de cada nodo o al finalizar el grafo.  
- **`stream_mode='custom'`:** Emite datos personalizados definidos dentro de los nodos del grafo, proporcionando flexibilidad para transmitir información específica según las necesidades del flujo.  
- **`stream_mode='messages'`:** Transmite los tokens generados por el modelo LLM junto con los metadatos asociados al nodo en el que se invoca el LLM.  
- **`stream_mode='debug'`:** Proporciona información detallada de depuración, transmitiendo la mayor cantidad de datos posible durante la ejecución del grafo.  

???+ Note "Nota"

    Todos los modos son accesibles mediante la función `stream` o `astream`, que facilita la captura y manejo de estos resultados en tiempo real.  

---

### 🛠️ Ejemplo Práctico  

Vamos a utilizar la calculadora que creamos en el [tema8: tools](../curso1/tema8_tools.md) y probaremos algunos modos de streaming.  

#### Ejemplo 1: **`stream_mode='updates'`**  

En este modo, el grafo emite **actualizaciones parciales del estado** mientras procesa cada nodo.  

```python
config1 = {"configurable": {"thread_id": "1"}}
for chunk in calculadora.stream({"messages": [HumanMessage(content="Me puedes resolver esta ecuación: ((3+2)*2)/5?")]}, config1, stream_mode="updates"):
    print(chunk)
```

```json title="Resultado"
{'assistant': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'id': 'call_gn7dz3d2ZttNgSvzIHgknjqI', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}], 'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 19, 'prompt_tokens': 394, 'total_tokens': 413, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c', 'finish_reason': 'tool_calls', 'logprobs': None}, id='run-972d7d8b-8307-476f-9366-93930f33f304-0', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_gn7dz3d2ZttNgSvzIHgknjqI', 'type': 'tool_call'}], usage_metadata={'input_tokens': 394, 'output_tokens': 19, 'total_tokens': 413, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})]}}
{'tools': {'messages': [ToolMessage(content='5', name='sumar', id='c64de46f-9490-4a2d-83dc-e6eb9afe4b80', tool_call_id='call_gn7dz3d2ZttNgSvzIHgknjqI')]}}
{'assistant': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'id': 'call_OMRcNRCNCtoNSqF5HGvY9c0V', 'function': {'arguments': '{"a":5,"b":2}', 'name': 'multiplicar'}, 'type': 'function'}], 'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 20, 'prompt_tokens': 421, 'total_tokens': 441, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c', 'finish_reason': 'tool_calls', 'logprobs': None}, id='run-2235a802-d120-4162-98dd-3b8bd8985f87-0', tool_calls=[{'name': 'multiplicar', 'args': {'a': 5, 'b': 2}, 'id': 'call_OMRcNRCNCtoNSqF5HGvY9c0V', 'type': 'tool_call'}], usage_metadata={'input_tokens': 421, 'output_tokens': 20, 'total_tokens': 441, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})]}}
{'tools': {'messages': [ToolMessage(content='10', name='multiplicar', id='065ec0a2-f4e6-4b15-8933-257a145f63a3', tool_call_id='call_OMRcNRCNCtoNSqF5HGvY9c0V')]}}
{'assistant': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'id': 'call_QUPAVI0aEgBjZGCXU0l223H8', 'function': {'arguments': '{"a":10,"b":5}', 'name': 'dividir'}, 'type': 'function'}], 'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 19, 'prompt_tokens': 449, 'total_tokens': 468, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c', 'finish_reason': 'tool_calls', 'logprobs': None}, id='run-e9604c6a-3947-43a7-9d97-f54bc686bd89-0', tool_calls=[{'name': 'dividir', 'args': {'a': 10, 'b': 5}, 'id': 'call_QUPAVI0aEgBjZGCXU0l223H8', 'type': 'tool_call'}], usage_metadata={'input_tokens': 449, 'output_tokens': 19, 'total_tokens': 468, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})]}}
{'tools': {'messages': [ToolMessage(content='2.0', name='dividir', id='eae662a1-3ea0-4cb0-b26f-72ff75fa44cf', tool_call_id='call_QUPAVI0aEgBjZGCXU0l223H8')]}}
{'assistant': {'messages': [AIMessage(content='La solución de la ecuación \\(((3+2)*2)/5\\) es \\(2.0\\).', additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 25, 'prompt_tokens': 478, 'total_tokens': 503, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c', 'finish_reason': 'stop', 'logprobs': None}, id='run-d7e4b5a5-15b8-43fc-a3fc-1c55cf939620-0', usage_metadata={'input_tokens': 478, 'output_tokens': 25, 'total_tokens': 503, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})]}}
``` 

#### Ejemplo 2: **`stream_mode='values'`**  

En este modo, el grafo emite **valores completos** del estado resultante después de cada nodo o al final del flujo.  

```python 
config2 = {"configurable": {"thread_id": "2"}}
for chunk in calculadora.stream({"messages": [HumanMessage(content="Me puedes resolver esta ecuación: ((3+2)*2)/5?")]}, config2, stream_mode="values"):
    for m in chunk['messages']:
        m.pretty_print()
    print("//"*40)
``` 

```python title="Resultado"
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
================================= Tool Message =================================
Name: sumar

5
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
================================= Tool Message =================================
Name: sumar

5
================================== Ai Message ==================================
Tool Calls:
  multiplicar (call_lqhLe5Qq8avnfzcACBCWEALs)
 Call ID: call_lqhLe5Qq8avnfzcACBCWEALs
  Args:
    a: 5
    b: 2
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
================================= Tool Message =================================
Name: sumar

5
================================== Ai Message ==================================
Tool Calls:
  multiplicar (call_lqhLe5Qq8avnfzcACBCWEALs)
 Call ID: call_lqhLe5Qq8avnfzcACBCWEALs
  Args:
    a: 5
    b: 2
================================= Tool Message =================================
Name: multiplicar

10
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
================================= Tool Message =================================
Name: sumar

5
================================== Ai Message ==================================
Tool Calls:
  multiplicar (call_lqhLe5Qq8avnfzcACBCWEALs)
 Call ID: call_lqhLe5Qq8avnfzcACBCWEALs
  Args:
    a: 5
    b: 2
================================= Tool Message =================================
Name: multiplicar

10
================================== Ai Message ==================================
Tool Calls:
  dividir (call_sGwYH4gwnjh5ii9WBc2TkXPB)
 Call ID: call_sGwYH4gwnjh5ii9WBc2TkXPB
  Args:
    a: 10
    b: 5
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
================================= Tool Message =================================
Name: sumar

5
================================== Ai Message ==================================
Tool Calls:
  multiplicar (call_lqhLe5Qq8avnfzcACBCWEALs)
 Call ID: call_lqhLe5Qq8avnfzcACBCWEALs
  Args:
    a: 5
    b: 2
================================= Tool Message =================================
Name: multiplicar

10
================================== Ai Message ==================================
Tool Calls:
  dividir (call_sGwYH4gwnjh5ii9WBc2TkXPB)
 Call ID: call_sGwYH4gwnjh5ii9WBc2TkXPB
  Args:
    a: 10
    b: 5
================================= Tool Message =================================
Name: dividir

2.0
////////////////////////////////////////////////////////////////////////////////
================================ Human Message =================================

Me puedes resolver esta ecuación: ((3+2)*2)/5?
================================== Ai Message ==================================
Tool Calls:
  sumar (call_QIY2zprLQo9zC3eu7hnLK4up)
 Call ID: call_QIY2zprLQo9zC3eu7hnLK4up
  Args:
    a: 3
    b: 2
================================= Tool Message =================================
Name: sumar

5
================================== Ai Message ==================================
Tool Calls:
  multiplicar (call_lqhLe5Qq8avnfzcACBCWEALs)
 Call ID: call_lqhLe5Qq8avnfzcACBCWEALs
  Args:
    a: 5
    b: 2
================================= Tool Message =================================
Name: multiplicar

10
================================== Ai Message ==================================
Tool Calls:
  dividir (call_sGwYH4gwnjh5ii9WBc2TkXPB)
 Call ID: call_sGwYH4gwnjh5ii9WBc2TkXPB
  Args:
    a: 10
    b: 5
================================= Tool Message =================================
Name: dividir

2.0
================================== Ai Message ==================================

La solución de la ecuación \(((3+2)*2)/5\) es \(2.0\).
////////////////////////////////////////////////////////////////////////////////
```

#### Ejemplo 3: **`stream_mode='debug'`**  

En este modo, el grafo proporciona **información detallada de depuración** del estado resultante durante la ejecución del grafo.  

```python
config3 = {"configurable": {"thread_id": "3"}}
for chunk in calculadora.stream({"messages": [HumanMessage(content="Me puedes resolver esta ecuación: ((3+2)*2)/5?")]}, config1, stream_mode="debug"):
    print(chunk)
```

```json
{'type': 'task', 'timestamp': '2025-01-16T11:21:54.720593+00:00', 'step': 1, 'payload': {'id': '8ecbef52-7a8c-0647-27d6-cdb28051ea9e', 'name': 'node1', 'input': {'messages': [HumanMessage(content='---inicio---', additional_kwargs={}, response_metadata={}, id='7774d4e0-11f5-4a3d-a898-be405bf9599a')]}, 'triggers': ['start:node1']}}
{'type': 'task_result', 'timestamp': '2025-01-16T11:21:54.722972+00:00', 'step': 1, 'payload': {'id': '8ecbef52-7a8c-0647-27d6-cdb28051ea9e', 'name': 'node1', 'error': None, 'result': [('messages', '---node1---')], 'interrupts': []}}
{'type': 'task', 'timestamp': '2025-01-16T11:21:54.724961+00:00', 'step': 2, 'payload': {'id': '9adfad56-9c9c-9795-8d42-6c329e9fbcc2', 'name': 'node2', 'input': {'messages': [HumanMessage(content='---inicio---', additional_kwargs={}, response_metadata={}, id='7774d4e0-11f5-4a3d-a898-be405bf9599a'), HumanMessage(content='---node1---', additional_kwargs={}, response_metadata={}, id='5995d43e-6f2b-41a3-b12c-8ff9bb090d4f')]}, 'triggers': ['node1']}}
{'type': 'task_result', 'timestamp': '2025-01-16T11:21:54.726201+00:00', 'step': 2, 'payload': {'id': '9adfad56-9c9c-9795-8d42-6c329e9fbcc2', 'name': 'node2', 'error': None, 'result': [('messages', '---node2---')], 'interrupts': []}}
{'type': 'task', 'timestamp': '2025-01-16T11:21:54.726641+00:00', 'step': 3, 'payload': {'id': 'c8066a74-accd-fe92-2ffa-af9112d97a26', 'name': 'node3', 'input': {'messages': [HumanMessage(content='---inicio---', additional_kwargs={}, response_metadata={}, id='7774d4e0-11f5-4a3d-a898-be405bf9599a'), HumanMessage(content='---node1---', additional_kwargs={}, response_metadata={}, id='5995d43e-6f2b-41a3-b12c-8ff9bb090d4f'), HumanMessage(content='---node2---', additional_kwargs={}, response_metadata={}, id='bf320179-1488-4700-ac3c-e6b69c3a821c')]}, 'triggers': ['node2']}}
{'type': 'task_result', 'timestamp': '2025-01-16T11:21:54.727115+00:00', 'step': 3, 'payload': {'id': 'c8066a74-accd-fe92-2ffa-af9112d97a26', 'name': 'node3', 'error': None, 'result': [('messages', '---node3---')], 'interrupts': []}}
```

--- 

## 🎯 Eventos en Streaming  

### 🔍 ¿Qué son los Eventos en Streaming?  

Los eventos en streaming son **notificaciones generadas durante la ejecución del grafo**. Estos eventos proporcionan información detallada sobre:  
- Qué nodos se están ejecutando.  
- Qué tipo de actividad está ocurriendo (inicio, finalización, error, etc.).  
- Detalles adicionales como el nombre del nodo y metadatos relevantes.  

LangGraph gestiona los eventos mediante la función `aStream_events`, que permite capturarlos y procesarlos en tiempo real.  

---

### 🛠️ Ejemplo1: Capturando Eventos en Streaming  

Usaremos el siguiente ejemplo para observar cómo los eventos nos permiten monitorear el flujo del grafo:  

```python
config5 = {"configurable": {"thread_id": "5"}}
async for event in calculadora.astream_events({"messages": [HumanMessage(content="Me puedes resolver esta ecuación: 3+2?")]}, config5, version="v2"):
    print(event)
```
```python
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'name': 'LangGraph', 'tags': [], 'run_id': 'e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'metadata': {'thread_id': '5'}, 'parent_ids': []}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'name': '__start__', 'tags': ['graph:step:0', 'langsmith:hidden'], 'run_id': '983889a7-ca3a-47c5-bb09-67339220dfee', 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'name': '_write', 'tags': ['seq:step:1', 'langsmith:hidden', 'langsmith:hidden'], 'run_id': 'a43d3bf9-9f5f-4c6d-8f5b-839bf690cb91', 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '983889a7-ca3a-47c5-bb09-67339220dfee']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}, 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'run_id': 'a43d3bf9-9f5f-4c6d-8f5b-839bf690cb91', 'name': '_write', 'tags': ['seq:step:1', 'langsmith:hidden', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '983889a7-ca3a-47c5-bb09-67339220dfee']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'name': '_write', 'tags': ['seq:step:3', 'langsmith:hidden', 'langsmith:hidden'], 'run_id': '240b22ae-0f65-49bd-b93f-74713ccb190b', 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '983889a7-ca3a-47c5-bb09-67339220dfee']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}, 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'run_id': '240b22ae-0f65-49bd-b93f-74713ccb190b', 'name': '_write', 'tags': ['seq:step:3', 'langsmith:hidden', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '983889a7-ca3a-47c5-bb09-67339220dfee']}
{'event': 'on_chain_stream', 'run_id': '983889a7-ca3a-47c5-bb09-67339220dfee', 'name': '__start__', 'tags': ['graph:step:0', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'data': {'chunk': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}, 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={})]}}, 'run_id': '983889a7-ca3a-47c5-bb09-67339220dfee', 'name': '__start__', 'tags': ['graph:step:0', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 0, 'langgraph_node': '__start__', 'langgraph_triggers': ['__start__'], 'langgraph_path': ('__pregel_pull', '__start__'), 'langgraph_checkpoint_ns': '__start__:6df9b3fd-426d-a817-b365-da3423177628'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825')]}}, 'name': 'assistant', 'tags': ['graph:step:1'], 'run_id': '1f41f0f9-d14a-4563-895b-f525f385255b', 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chat_model_start', 'data': {'input': {'messages': [[HumanMessage(content='\n  Resuelve la ecuación aplicando las siguientes reglas:\n  1. Evalúa las operaciones dentro de paréntesis primero.\n  2. Luego, evalúa multiplicaciones y divisiones de izquierda a derecha.\n  3. Finalmente, evalúa sumas y restas de izquierda a derecha.\n  4. Usa las herramientas proporcionadas: sumar, restar, multiplicar, dividir.\n  ', additional_kwargs={}, response_metadata={}), HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825')]]}}, 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}], tool_call_chunks=[{'name': 'sumar', 'args': '', 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': '{"', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': '', 'args': {}, 'id': None, 'type': 'tool_call'}], tool_call_chunks=[{'name': None, 'args': '{"', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': 'a', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': 'a', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': 'a', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': '":', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': '":', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': '":', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': '3', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': '3', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': '3', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': ',"', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': ',"', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': ',"', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': 'b', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': 'b', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': 'b', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': '":', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': '":', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': '":', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': '2', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': '2', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': '2', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': None, 'function': {'arguments': '}', 'name': None}, 'type': None}]}, response_metadata={}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', invalid_tool_calls=[{'name': None, 'args': '}', 'id': None, 'error': None, 'type': 'invalid_tool_call'}], tool_call_chunks=[{'name': None, 'args': '}', 'id': None, 'index': 0, 'type': 'tool_call_chunk'}])}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742')}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chat_model_end', 'data': {'output': AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), 'input': {'messages': [[HumanMessage(content='\n  Resuelve la ecuación aplicando las siguientes reglas:\n  1. Evalúa las operaciones dentro de paréntesis primero.\n  2. Luego, evalúa multiplicaciones y divisiones de izquierda a derecha.\n  3. Finalmente, evalúa sumas y restas de izquierda a derecha.\n  4. Usa las herramientas proporcionadas: sumar, restar, multiplicar, dividir.\n  ', additional_kwargs={}, response_metadata={}), HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825')]]}}, 'run_id': '683ffa42-bf54-4c94-ab20-02074c2b2742', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'name': '_write', 'tags': ['seq:step:2', 'langsmith:hidden'], 'run_id': '3cc6f127-fbe2-46b3-bdc1-ec6ba62e1db3', 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}, 'input': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'run_id': '3cc6f127-fbe2-46b3-bdc1-ec6ba62e1db3', 'name': '_write', 'tags': ['seq:step:2', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'name': 'tools_condition', 'tags': ['seq:step:4'], 'run_id': 'd5595dbf-ca33-4c79-a9b0-0bab58b24309', 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chain_end', 'data': {'output': 'tools', 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'run_id': 'd5595dbf-ca33-4c79-a9b0-0bab58b24309', 'name': 'tools_condition', 'tags': ['seq:step:4'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', '1f41f0f9-d14a-4563-895b-f525f385255b']}
{'event': 'on_chain_stream', 'run_id': '1f41f0f9-d14a-4563-895b-f525f385255b', 'name': 'assistant', 'tags': ['graph:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'data': {'chunk': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}, 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825')]}}, 'run_id': '1f41f0f9-d14a-4563-895b-f525f385255b', 'name': 'assistant', 'tags': ['graph:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 1, 'langgraph_node': 'assistant', 'langgraph_triggers': ['start:assistant'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:0e210a7f-649d-0ecb-f539-bc2d3f479962'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_stream', 'run_id': 'e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'name': 'LangGraph', 'tags': [], 'metadata': {'thread_id': '5'}, 'data': {'chunk': {'assistant': {'messages': [AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}}, 'parent_ids': []}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'name': 'tools', 'tags': ['graph:step:2'], 'run_id': 'f5703885-cb8c-48f6-ac33-76855b10a7a1', 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_tool_start', 'data': {'input': {'a': 3, 'b': 2}}, 'name': 'sumar', 'tags': ['seq:step:1'], 'run_id': 'bdbf1166-b132-40fc-8844-1252dc7f5510', 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304', 'checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'f5703885-cb8c-48f6-ac33-76855b10a7a1']}
{'event': 'on_tool_end', 'data': {'output': ToolMessage(content='5', name='sumar', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N'), 'input': {'a': 3, 'b': 2}}, 'run_id': 'bdbf1166-b132-40fc-8844-1252dc7f5510', 'name': 'sumar', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304', 'checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'f5703885-cb8c-48f6-ac33-76855b10a7a1']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [ToolMessage(content='5', name='sumar', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}}, 'name': '_write', 'tags': ['seq:step:2', 'langsmith:hidden'], 'run_id': '76877fe7-cb89-4c03-b2b1-b3e9e280c379', 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'f5703885-cb8c-48f6-ac33-76855b10a7a1']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [ToolMessage(content='5', name='sumar', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}, 'input': {'messages': [ToolMessage(content='5', name='sumar', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}}, 'run_id': '76877fe7-cb89-4c03-b2b1-b3e9e280c379', 'name': '_write', 'tags': ['seq:step:2', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'f5703885-cb8c-48f6-ac33-76855b10a7a1']}
{'event': 'on_chain_stream', 'run_id': 'f5703885-cb8c-48f6-ac33-76855b10a7a1', 'name': 'tools', 'tags': ['graph:step:2'], 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'data': {'chunk': {'messages': [ToolMessage(content='5', name='sumar', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [ToolMessage(content='5', name='sumar', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}, 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}])]}}, 'run_id': 'f5703885-cb8c-48f6-ac33-76855b10a7a1', 'name': 'tools', 'tags': ['graph:step:2'], 'metadata': {'thread_id': '5', 'langgraph_step': 2, 'langgraph_node': 'tools', 'langgraph_triggers': ['branch:assistant:tools_condition:tools'], 'langgraph_path': ('__pregel_pull', 'tools'), 'langgraph_checkpoint_ns': 'tools:69de5831-7838-8d27-6c04-b9760fd3e304'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_stream', 'run_id': 'e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'name': 'LangGraph', 'tags': [], 'metadata': {'thread_id': '5'}, 'data': {'chunk': {'tools': {'messages': [ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}}}, 'parent_ids': []}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}}, 'name': 'assistant', 'tags': ['graph:step:3'], 'run_id': 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593', 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chat_model_start', 'data': {'input': {'messages': [[HumanMessage(content='\n  Resuelve la ecuación aplicando las siguientes reglas:\n  1. Evalúa las operaciones dentro de paréntesis primero.\n  2. Luego, evalúa multiplicaciones y divisiones de izquierda a derecha.\n  3. Finalmente, evalúa sumas y restas de izquierda a derecha.\n  4. Usa las herramientas proporcionadas: sumar, restar, multiplicar, dividir.\n  ', additional_kwargs={}, response_metadata={}), HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]]}}, 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='La', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' solución', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' de', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' la', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' ecu', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='ación', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' \\(', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='3', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' +', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' ', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='2', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='\\', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=')', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' es', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=' \\(', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='5', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='\\', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content=').', additional_kwargs={}, response_metadata={}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_stream', 'data': {'chunk': AIMessageChunk(content='', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chat_model_end', 'data': {'output': AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f'), 'input': {'messages': [[HumanMessage(content='\n  Resuelve la ecuación aplicando las siguientes reglas:\n  1. Evalúa las operaciones dentro de paréntesis primero.\n  2. Luego, evalúa multiplicaciones y divisiones de izquierda a derecha.\n  3. Finalmente, evalúa sumas y restas de izquierda a derecha.\n  4. Usa las herramientas proporcionadas: sumar, restar, multiplicar, dividir.\n  ', additional_kwargs={}, response_metadata={}), HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]]}}, 'run_id': '181383ba-5725-4032-a38c-566544bdfb3f', 'name': 'ChatOpenAI', 'tags': ['seq:step:1'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a', 'ls_provider': 'openai', 'ls_model_name': 'gpt-4o-mini', 'ls_model_type': 'chat', 'ls_temperature': None}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}, 'name': '_write', 'tags': ['seq:step:2', 'langsmith:hidden'], 'run_id': '68069bcd-789b-4bb7-9d9d-a1d26ea92d68', 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}, 'input': {'messages': [AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}, 'run_id': '68069bcd-789b-4bb7-9d9d-a1d26ea92d68', 'name': '_write', 'tags': ['seq:step:2', 'langsmith:hidden'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chain_start', 'data': {'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N'), AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}, 'name': 'tools_condition', 'tags': ['seq:step:4'], 'run_id': 'fedc83ad-8c10-4114-9841-0c7702d68c06', 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chain_end', 'data': {'output': '__end__', 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N'), AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}, 'run_id': 'fedc83ad-8c10-4114-9841-0c7702d68c06', 'name': 'tools_condition', 'tags': ['seq:step:4'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593']}
{'event': 'on_chain_stream', 'run_id': 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593', 'name': 'assistant', 'tags': ['graph:step:3'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'data': {'chunk': {'messages': [AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_end', 'data': {'output': {'messages': [AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}, 'input': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N')]}}, 'run_id': 'c5d7ff7e-92c9-4e2a-a13a-7d54665d2593', 'name': 'assistant', 'tags': ['graph:step:3'], 'metadata': {'thread_id': '5', 'langgraph_step': 3, 'langgraph_node': 'assistant', 'langgraph_triggers': ['tools'], 'langgraph_path': ('__pregel_pull', 'assistant'), 'langgraph_checkpoint_ns': 'assistant:f500bf2c-1e64-bbf4-69d5-1d004e945c1a'}, 'parent_ids': ['e30d2fbb-d70f-41cb-a5fc-3248a9991118']}
{'event': 'on_chain_stream', 'run_id': 'e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'name': 'LangGraph', 'tags': [], 'metadata': {'thread_id': '5'}, 'data': {'chunk': {'assistant': {'messages': [AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}}, 'parent_ids': []}
{'event': 'on_chain_end', 'data': {'output': {'messages': [HumanMessage(content='Me puedes resolver esta ecuación: 3+2?', additional_kwargs={}, response_metadata={}, id='da59875b-a82e-47f8-b9ba-0bc091583825'), AIMessage(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'function': {'arguments': '{"a":3,"b":2}', 'name': 'sumar'}, 'type': 'function'}]}, response_metadata={'finish_reason': 'tool_calls', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-683ffa42-bf54-4c94-ab20-02074c2b2742', tool_calls=[{'name': 'sumar', 'args': {'a': 3, 'b': 2}, 'id': 'call_L4kVl8jzeGdxBc763RLp934N', 'type': 'tool_call'}]), ToolMessage(content='5', name='sumar', id='1b626575-b877-4adb-ae08-1e38cb1b76ac', tool_call_id='call_L4kVl8jzeGdxBc763RLp934N'), AIMessage(content='La solución de la ecuación \\(3 + 2\\) es \\(5\\).', additional_kwargs={}, response_metadata={'finish_reason': 'stop', 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab54c'}, id='run-181383ba-5725-4032-a38c-566544bdfb3f')]}}, 'run_id': 'e30d2fbb-d70f-41cb-a5fc-3248a9991118', 'name': 'LangGraph', 'tags': [], 'metadata': {'thread_id': '5'}, 'parent_ids': []}
```

### 🛠️ Ejemplo2: Capturando Eventos en Streaming  

Usaremos el siguiente ejemplo para observar cómo los eventos nos permiten monitorear el flujo del grafo:  

```python
config = {"configurable": {"thread_id": "5"}}
async for event in calculadora.astream_events({"messages": [HumanMessage(content="Me puedes resolver esta ecuación: 3+2?")]}, config, version="v2"):
    # Get chat model tokens from a particular node 
    if event["event"] == "on_chat_model_stream" and event['metadata'].get('langgraph_node','') == "assistant":
        data = event["data"]
        print(data["chunk"].content, end="|")
```

```python title="Resultado"
||||||||||||La| solución| de| la| ecu|ación| \(|3| +| |2|\|)| es| \(|5|\|).||
```

---

## 📋 Conclusión  

El streaming en LangGraph transforma la forma en que manejamos los flujos, proporcionando:  
- **Resultados en Tiempo Real:** Respuestas inmediatas y progresivas.  
- **Control y Flexibilidad:** Capacidad para monitorear y reaccionar a eventos en tiempo real.  
- **Mejor Experiencia de Usuario:** Interacciones más fluidas y dinámicas.  

---

## 🔎 Recursos:

- :simple-googlecolab: Ver notebook en [Google Colab](https://colab.research.google.com/drive/1YBgaFCg2xXd7Pyu-0FeTH0wtnvwSRndI?usp=sharing)
- :simple-googlecolab: Más ejemplos: [Google Colab](https://colab.research.google.com/github/langchain-ai/langchain-academy/blob/main/module-3/streaming-interruption.ipynb)
- :books: Definición del concepto: [Streaming](https://langchain-ai.github.io/langgraph/concepts/streaming/)
- :books: How-to-guide: [Streaming](https://langchain-ai.github.io/langgraph/how-tos/#streaming)

---

## 🧑‍🏫 ¿Qué Hemos Aprendido?  

- **Streaming Graph Outputs:** Cómo emitir resultados progresivamente usando `stream_mode='updates'` y `stream_mode='values'`.  
- **Eventos en Streaming:** Cómo capturar y manejar eventos durante la ejecución del grafo con `aStream_events`.  

---

## 🌐 ¿Qué es lo Siguiente?  

En el próximo tema, exploraremos los **Breakpoints**, que nos permiten pausar el flujo del grafo en puntos específicos para validar o intervenir en tiempo real.  
