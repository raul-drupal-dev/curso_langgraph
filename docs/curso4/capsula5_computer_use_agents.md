# 🖥️ Cápsula 5: LangGraph Computer Use Agents (CUA)

## 🎯 ¿Qué es y cómo funciona?

Los **Computer Use Agents (CUA)** son agentes capaces de interactuar con un ordenador como lo haría una persona: abrir el navegador, escribir en la barra de direcciones, hacer clic en botones y enlaces, completar formularios, desplazarse, instalar apps en una **VM**, etc. Este proyecto empaqueta un agente de este tipo sobre **LangGraph**, con soporte nativo para:

- 📡 **Streaming del estado** para visualizar la sesión en vivo.
- 🧠 **Memoria a corto y largo plazo** (integración con la capa de memoria de LangGraph).
- 🙋 **Human-in-the-loop** (intervenir cuando el agente lo necesite).
- 🔐 **Autenticación persistente (Auth States)** en sesiones de navegador.

Para ejecutar acciones “en el ordenador”, el **CUA** se apoya en un **entorno controlado** (p. ej. una **VM** en Scrapybara). El grafo orquesta pasos del agente (**percibir → razonar → actuar**) y llama a herramientas de “computer use” que realizan acciones atómicas (**click**, **type**, **open**, **screenshot**, **bash**, etc.). El **LLM** decide qué herramienta usar en cada iteración hasta cumplir el objetivo o alcanzar un límite de recursión.

!!! info "Fuentes que estudiamos" 
    - Repo oficial (Python): <https://github.com/langchain-ai/langgraph-cua-py>{target="\_blank"} 
    - Video demo: <https://www.youtube.com/watch?v=ndCFqT6xFQ4>{target="\_blank"} 
    - LangGraph (framework): <https://github.com/langchain-ai/langgraph>{target="\_blank"} 
    - LangGraph CUA (framework): <https://github.com/langchain-ai/langgraph-cua-py>{target="\_blank"}

---

## 🧭 ¿Para qué podemos usarlo? (Casos de uso)

- 🔎 **Navegación web autónoma**: buscar información, comparar precios, recopilar evidencia.
- 🛒 **Automatizar compras o reservas**: rellenar formularios, iniciar sesión, completar pasos.
- 🔧 **Operaciones en escritorio/VM**: instalar paquetes, ejecutar scripts, manipular archivos.
- 📄 **RPA ligero con razonamiento**: flujos con UI no estructurada, más allá de APIs.
- 🧪 **QA end-to-end** de interfaces web: explorar, verificar flujos y capturar resultados.
- 🔒 **Tareas autenticadas** con reuso de sesión (Auth States) sin re-login constante.

---

## 📦 Instalación

```bash
pip install -U langgraph-cua
```

Configura tus claves necesarias (ejemplo en bash):

```bash
export OPENAI_API_KEY="sk-..."
export SCRAPYBARA_API_KEY="sb-..."
```

Opcional: usa un fichero `.env` y carga variables con `python-dotenv`.

!!! tip "Entornos soportados" 
    - `environment: web` (por defecto, navegador en VM). 
    - `environment: ubuntu` o `windows` para tareas de SO.

---

## � Alternativas Open‑Source a Scrapybara

Si prefieres una solución open‑source para controlar navegadores/VMs, aquí tienes opciones compactas con sus pros y contras:

- Playwright (Python) — https://playwright.dev/python {target="\_blank"}
    - Pros: API moderna, multi‑navegador, fácil guardar/recuperar `storage_state`, buena documentación.
    - Contras: no ofrece por sí mismo streaming ni VMs gestionadas; debes desplegarlo en contenedores/VMs.

- Browserless (self‑hosted) — https://www.browserless.io/ {target="\_blank"}
    - Pros: servicio HTTP/WebSocket para controlar navegadores, imagen Docker lista, parecido a un servicio gestionado.
    - Contras: necesitas añadir persistencia de auth (storage) y/o una capa para streaming si la necesitas.

- Playwright en contenedores/VM (autogestionado)
    - Pros: control total (puedes ejecutar comandos de SO dentro del contenedor), ideal si necesitas una "VM" real.
    - Contras: más complejidad operativa (orquestar contenedores, limpieza, streaming).

- Selenium / Selenium Grid — https://www.selenium.dev/ {target="\_blank"}
    - Pros: muy maduro y soportado, fácil de escalar con Grid.
    - Contras: API menos moderna para SPAs comparado con Playwright.

!!! info "Pequeña recomendación"
    Para un balance entre API moderna y facilidad de despliegue, usa Browserless (self‑hosted) + Playwright como cliente; si necesitas ejecutar comandos a nivel SO, opta por Playwright en contenedores/VM.

---

## 🕹️ ¿Cómo se usa? (paso a paso)

A continuación explicamos el flujo típico y, sobre todo, cómo se maneja la memoria con la SDK y la configuración.

### 1) Crear el grafo CUA

```python
from langgraph_cua import create_cua
from dotenv import load_dotenv

load_dotenv()

cua_graph = create_cua(
    # Opcionalmente: parámetros de configuración
    # environment="web",               # "web" | "ubuntu" | "windows"
    # timeout_hours=2,                  # Vida de la VM
    # recursion_limit=100,              # Iteraciones máximas del agente
    # zdr_enabled=False,                # Zero Data Retention
    # auth_state_id=None,               # Reutilizar sesión autenticada
    # prompt="..."                      # Prompt del sistema recomendado
)
```

!!! info "Memoria y LangGraph"
    El grafo CUA está construido sobre LangGraph, que ya incluye memoria de corto plazo (historia del hilo) y puede integrarse con memoria de largo plazo mediante su Store. El agente pasa mensajes y tool-calls entre pasos; según la opción `zdr_enabled`, puede incluir todo el historial o usar `previous_response_id` para optimizar.

### 2) Invocar con mensajes y recibir streaming

```python
messages = [
    {
        "role": "system",
        "content": (
            "Eres un asistente avanzado de uso de ordenador. El navegador que estás utilizando "
            "ya está inicializado y visitando google.com."
        ),
    },
    {
        "role": "user",
        "content": (
            "¿Puedes encontrar el mejor precio para neumáticos nuevos para todas las estaciones que sean compatibles con mi Subaru Forester 2019?"
        ),
    },
]

async def main():
    async for update in cua_graph.astream({"messages": messages}, stream_mode="updates"):
        if "create_vm_instance" in update:
            stream_url = update["create_vm_instance"].get("stream_url")
            print("VM lista. Abre la URL para ver la sesión:", stream_url)

# Ejecutar con asyncio.run(main())
```

El stream emite eventos clave (p. ej., creación de VM) y el `stream_url` para observar la ejecución en vivo en el navegador.

### 3) Personalizar el comportamiento (config)

Puedes pasar `config` al crear el grafo o en cada invocación (vía `config={"configurable": {...}}`). Parámetros relevantes:

- **`environment`**: `web` | `ubuntu` | `windows`.
- **`timeout_hours`**: vida de la VM antes de apagar.
- **`recursion_limit`**: iteraciones máximas (más alto que 25 por la naturaleza de CUA).
- **`zdr_enabled`**: si tu cuenta OpenAI tiene Zero Data Retention.
- **`prompt`**: prompt de sistema. Scrapybara recomienda uno extenso con buenas prácticas (zoom, scroll, manejo de formularios, etc.).
- **`auth_state_id`**: id de un **Auth State** para reutilizar sesión autenticada del navegador.

!!! example "Prompt de sistema recomendado"
    Consulta el README del repo para un prompt detallado con buenas prácticas: <https://github.com/langchain-ai/langgraph-cua-py#system-prompts>{target="\_blank"}

### 4) Autenticación persistente (Auth States)

Los Auth States permiten guardar y reutilizar sesiones del navegador (por ejemplo, ya logueado en Amazon) para futuras ejecuciones.

- Pasa **`auth_state_id`** a `create_cua(...)`.
- El grafo almacenará ese id en el estado (`authenticated_id`).
- Si cambias `auth_state_id` más adelante, el grafo reautentica automáticamente.

Gestión con la SDK de Scrapybara:

```python
from scrapybara import Scrapybara

client = Scrapybara(api_key="sb-...")
instance = client.get("<instance_id>")
auth_state_id = instance.save_auth(name="example_site").auth_state_id

# Modificar un Auth State existente
instance.modify_auth(auth_state_id=auth_state_id, name="renamed_auth_state")

# Nota: Para forzar re-autenticación en un run activo, pon authenticated_id=None en el state.
```

### 5) Memoria: corto vs. largo plazo y ZDR

- **Corto plazo**: la historia del hilo fluye dentro del grafo (mensajes + tool calls). Si `zdr_enabled=False`, el agente puede usar `previous_response_id` y mandar solo el mensaje más reciente; si `True`, enviará todo el historial cada vez.
- **Largo plazo**: puedes combinar **CUA** con la capa de memoria persistente de **LangGraph (BaseStore)** y/o **LangMem** para recuerdos entre sesiones (preferencias, facts, etc.).

!!! tip "Cuándo activar ZDR"
    Activa `zdr_enabled=True` si tu política de privacidad exige no depender de `previous_response_id`. El coste puede ser mayor al enviar todo el historial, pero aumenta el aislamiento.

### 6) Entornos alternativos

Además de `web`, puedes usar entornos `ubuntu` o `windows` para tareas de SO. El agente usará herramientas apropiadas (p. ej., `bash`) para instalar y ejecutar comandos.

---

## ✅ Ventajas

- 🖱️ **Acceso a UI real**: el agente puede interactuar con páginas y apps como un humano.
- 🧩 **Orquestación sólida con LangGraph**: streaming, memoria, control de flujo, HITL.
- 🔁 **Reuso de sesiones (Auth States)** para tareas autenticadas.
- 🧱 **Aislamiento en VM**: más seguro y reproducible que tocar tu máquina local.
- 🔧 **Multi-entorno**: web, Ubuntu y Windows.

---

## 🧪 Lo aprendido

- **Qué es un Computer Use Agent** y cómo se integra con **LangGraph**.
- **Cómo instalar, configurar claves y levantar un grafo CUA**.
- **Cómo invocar con streaming y personalizar parámetros**.
- **Cómo funciona la memoria** (corto/largo plazo) y el impacto de **ZDR**.
- **Cómo mantener sesiones autenticadas con Auth States**.

---

## 🔎 Recursos externos

- 📦 Repo (Python): <https://github.com/langchain-ai/langgraph-cua-py>{target="\_blank"}
- 🎬 Demo en YouTube: <https://www.youtube.com/watch?v=ndCFqT6xFQ4>{target="\_blank"}
- 📘 Docs de memoria en LangGraph: <https://langchain-ai.github.io/langgraph/concepts/memory/>{target="\_blank"}
- 🧑‍💻 Streaming en LangGraph: <https://langchain-ai.github.io/langgraph/how-tos/#streaming>{target="\_blank"}
- 🔐 Auth States (Scrapybara): <https://docs.scrapybara.com/auth-states>{target="\_blank"}
- 🧰 TS version: <https://github.com/langchain-ai/langgraphjs/tree/main/libs/langgraph-cua>{target="\_blank"}
