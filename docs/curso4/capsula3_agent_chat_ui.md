# 💬 Cápsula: Agent Chat UI

## 🎯 ¿Qué es Agent Chat UI?

**Agent Chat UI** es una aplicación web construida con **Next.js** que te permite chatear con cualquier agente de **LangGraph** (tanto en Python como en TypeScript) a través de una interfaz de chat moderna y lista para usar.

En pocas palabras, es el “frontend” de chat para tus agentes: conectas tu servidor de LangGraph y empiezas a interactuar con tu grafo/assistant sin tener que construir una UI desde cero. Soporta streaming, renderizado de artefactos en un panel lateral y opciones de producción con autenticación.


## 🧠 ¿Para qué podemos usarlo? (Casos de uso)

- 🧪 Prototipado rápido de agentes: prueba tus grafos sin desarrollar interfaz propia.
- 🧰 Demos y presentaciones: muestra tus agentes a usuarios o stakeholders de forma simple.
- 🛟 Soporte interno: interfaz para equipos que necesitan hablar con agentes internos.
- 🔬 Experimentos y evaluación: valida prompts, flujos y memoria sobre una UI estable.
- 🚀 Producción con autenticación: publica una UI de chat conectada a tu despliegue de LangGraph.


## 📦 ¿Cómo se instala?

!!! info "Requisitos previos"
    - Node.js 18+ (recomendado LTS)
    - pnpm instalado globalmente (o usa npm/yarn)

Tienes dos caminos principales:

### "Opción 1: Crear proyecto nuevo (recomendado)"

```bash
npx create-agent-chat-app
cd agent-chat-ui
pnpm install
pnpm dev
```

### "Opción 2: Clonar el repositorio oficial"

```bash
git clone https://github.com/langchain-ai/agent-chat-ui.git
cd agent-chat-ui
pnpm install
pnpm dev
```
La app quedará disponible en [http://localhost:3000](http://localhost:3000)

!!! tip "Sin instalación local"
    Puedes usar la demo desplegada: [https://agentchat.vercel.app/](https://agentchat.vercel.app/)


## 🕹️ ¿Cómo se usa?

Al abrir la app por primera vez (local o demo), verás un formulario de conexión donde debes indicar:

1. 🔗 Deployment URL: URL de tu servidor LangGraph (local o producción).
2. 🧭 Assistant/Graph ID: nombre del grafo o ID del assistant al que quieres chatear.
3. 🔐 LangSmith API Key: solo si te conectas a un LangGraph desplegado que requiera autenticación con LangSmith.

Después, pulsa «Continue» y entrarás al chat. A partir de ahí podrás enviar mensajes, ver respuestas en streaming, visualizar artefactos y controlar la sesión.

### 🚫 Saltar el formulario (variables de entorno)

Si lo prefieres, puedes precargar la configuración creando un archivo `.env` (copia `.env.example`) y definiendo por ejemplo:

```bash
NEXT_PUBLIC_API_URL=http://localhost:2024
NEXT_PUBLIC_ASSISTANT_ID=agent
```

Al iniciar, la UI usará estos valores y no mostrará el formulario.


## 🧩 Funciones clave útiles

### 1) 🔍 Ocultar mensajes en el chat

- Ocultar el streaming (render en vivo): añade la etiqueta `langsmith:nostream` a tu modelo de chat.
- Ocultar un mensaje por completo (ni en vivo ni al final): añade la etiqueta `langsmith:do-not-render` y guarda el mensaje con un `id` que empiece por `do-not-render-`.

#### "Python"

```python
# Evitar streaming en la UI
model = ChatAnthropic().with_config(config={"tags": ["langsmith:nostream"]})

# Ocultar por completo un mensaje
result = model.invoke([messages])
result.id = f"do-not-render-{result.id}"
return {"messages": [result]}
```

#### "TypeScript"

```ts
// Evitar streaming en la UI
const model = new ChatAnthropic().withConfig({ tags: ["langsmith:nostream"] });

// Ocultar por completo un mensaje
const result = await model.invoke([messages]);
result.id = `do-not-render-${result.id}`;
return { messages: [result] };
```

!!! note "Importante"
    Aunque ocultes el streaming, si el mensaje se guarda en el estado sin modificaciones, aparecerá al final salvo que uses el patrón `do-not-render-`.

### 2) 🧱 Renderizado de artefactos

La UI permite mostrar “artefactos” (contenido adicional) en un panel lateral. Desde tu grafo puedes establecer contexto en `thread.meta.artifact` y, en la UI, recuperar ese contexto con el hook `useArtifact` para renderizar componentes propios.


## 🏭 ¿Cómo llevarlo a producción?

Tienes dos enfoques principales:

### "Quickstart: API Passthrough (proxy)"

Usa el paquete de passthrough para proxy inverso y autenticación sencilla. Configura estas variables en `.env`:

```bash
# URL de tu despliegue de LangGraph (producción)
LANGGRAPH_API_URL="https://mi-agente.default.us.langgraph.app"
# URL pública de tu web + "/api" (el proxy)
NEXT_PUBLIC_API_URL="https://mi-sitio.com/api"
# ID del assistant/grafo a usar
NEXT_PUBLIC_ASSISTANT_ID="agent"
# API Key de LangSmith (solo en servidor, sin NEXT_PUBLIC_)
LANGSMITH_API_KEY="lsv2_..."
```

Con esto, el cliente habla con tu `/api`, y el servidor inyecta la clave de LangSmith al llamar a LangGraph.

### "Avanzado: Autenticación personalizada"

Implementa autenticación en tu servidor de LangGraph y pasa un token propio desde el cliente. Pasos típicos:

  1. Expón un endpoint en tu despliegue de LangGraph que devuelva un token de acceso para el usuario.
  2. En la UI, ajusta el hook de streaming para enviar el token en headers, por ejemplo:

```ts
const streamValue = useTypedStream({
  apiUrl: process.env.NEXT_PUBLIC_API_URL,
  assistantId: process.env.NEXT_PUBLIC_ASSISTANT_ID,
  defaultHeaders: {
    Authentication: `Bearer ${yourAuthToken}`,
  },
});
```


## ✅ Ventajas

- ⏱️ Ahorro de tiempo: UI de chat lista para conectar a tus grafos.
- 🧩 Integración directa con LangGraph (Python y TS) vía clave `messages`.
- 📡 Streaming en vivo y control fino de visibilidad de mensajes.
- 🖼️ Panel de artefactos para contenido enriquecido.
- 🏁 Lista para producción con opciones de autenticación y proxy.
- 🧪 Ideal para demos, pruebas, evaluación y uso interno.
- 🆓 Open Source (MIT), fácil de extender y personalizar.


## 🔎 Recursos

- [🎥 Video guía](https://www.youtube.com/watch?v=lInrwVnZ83o)
- [💻 Repositorio oficial](https://github.com/langchain-ai/agent-chat-ui)
- [🧪 Demo en vivo](https://agentchat.vercel.app/)
- 📘 Docs LangGraph (autenticación):
    - [Python](https://langchain-ai.github.io/langgraph/tutorials/auth/getting_started/)
    - [TypeScript](https://langchain-ai.github.io/langgraphjs/how-tos/auth/custom_auth/)


## 🧩 ¿Qué hemos aprendido?

- Qué es Agent Chat UI y cuándo usarlo.
- Cómo instalarlo localmente o usar la demo.
- Cómo conectarlo a tu servidor de LangGraph.
- Cómo ocultar mensajes, renderizar artefactos y preparar un despliegue de producción.