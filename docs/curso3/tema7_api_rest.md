???+ warning "En proceso"

    **Este documento aun no esta terminado.**


# 🌐 Tema 8: Creando una API REST/GraphQL para LangGraph  

## 🌟 Introducción  

A lo largo del curso, hemos aprendido a ejecutar grafos con **LangGraph CLI**, gestionar su ejecución con la **SDK de LangGraph** y conectarnos a ellos mediante **RemoteGraph**.  
Sin embargo, en algunos casos puede ser útil **exponer una API personalizada** para interactuar con nuestro grafo de manera más flexible.  

📌 **¿Por qué crear una API REST o GraphQL para LangGraph?**  
✅ Permite **control total sobre los endpoints** y la lógica de negocio.  
✅ Facilita la integración con **otras aplicaciones** que requieran consumir los datos del grafo.  
✅ Permite aplicar **autenticación, validaciones y permisos personalizados**.  
✅ Mejora la **escalabilidad**, separando la lógica del grafo de la API de acceso.  

En este tema, veremos **cómo construir una API personalizada** usando **FastAPI** y **GraphQL**, exponiendo nuestro grafo de manera estructurada.  

🔗 **Referencia oficial**: [LangGraph API](https://langchain-ai.github.io/langgraph/how-tos/deploy-self-hosted/)  

---

## 🏗️ **Estructura del Proyecto**  

Para organizar nuestra API de forma modular y escalable, usaremos la siguiente estructura de archivos:  

```bash
mi_proyecto/
├── app/                    # Carpeta principal de la API
│   ├── main.py             # Archivo principal de FastAPI
│   ├── config.py           # Configuración (API Keys, URL del grafo, etc.)
│   ├── routes/             # Carpeta de rutas
│   │   ├── rest.py         # Endpoints REST
│   │   ├── graphql.py      # Endpoints GraphQL
│   ├── services/           # Lógica de conexión con LangGraph
│   │   ├── graph_client.py # Cliente para ejecutar el grafo
│   ├── security/           # Autenticación y validación
│   │   ├── auth.py         # Validación de API Keys
├── .env                    # Variables de entorno
├── requirements.txt        # Dependencias del proyecto
└── Dockerfile              # Dockerización de la API
```

## 🚀 1. Creando una API REST con FastAPI  

Para exponer nuestro grafo como API, utilizaremos **FastAPI**, un framework rápido y eficiente para construir APIs en Python.  

📌 **Pasos para crear la API:**  
1️⃣ **Instalar dependencias necesarias.**  
2️⃣ **Configurar FastAPI con un endpoint para ejecutar el grafo.**  
3️⃣ **Exponer endpoints REST para enviar entradas y obtener respuestas del grafo.**  



📌 **Ejemplo: Creando una API REST con FastAPI**  

[api_rest_fastapi_placeholder]  

🔹 **Explicación del código:**  
- Definimos un **endpoint** `/execute` que recibe una solicitud con datos de entrada.  
- Llamamos al grafo de LangGraph y enviamos los datos al servidor.  
- Retornamos la **respuesta generada** por el grafo en formato JSON.  

???+ warning "Atención"  
    FastAPI puede ejecutarse en **modo asíncrono**, por lo que debemos asegurarnos de que LangGraph **soporte async** en nuestras llamadas.  

---

## 📊 2. Creando una API GraphQL para LangGraph  

En algunos casos, una API REST puede ser **demasiado rígida**, especialmente si necesitamos **consultas más dinámicas**.  
Para ello, podemos usar **GraphQL**, que permite a los clientes **solicitar solo los datos que necesitan**.  

📌 **Ventajas de GraphQL sobre REST:**  
✅ Permite **consultas más flexibles**, solicitando solo los datos necesarios.  
✅ Reduce la sobrecarga de múltiples requests, optimizando el rendimiento.  
✅ Es ideal para **frontends dinámicos** que requieren diferentes datos en cada consulta.  

📌 **Ejemplo: Creando una API GraphQL para LangGraph**  

[api_graphql_placeholder]  

🔹 **Explicación del código:**  
- Creamos un esquema **GraphQL** que expone operaciones sobre el grafo.  
- Definimos **mutaciones** para enviar entradas y ejecutar procesos en LangGraph.  
- Implementamos resolvers que **conectan GraphQL con nuestro grafo remoto**.  

???+ note "Nota"  
    GraphQL es ideal para aplicaciones que requieren **consultas dinámicas** y estructuras de datos flexibles.  

---

## 🛠️ 3. Comparación: API REST vs. GraphQL  

| **Característica**  | **REST** 🌐 | **GraphQL** 🛠️ |
|---------------------|------------|----------------|
| **Estructura**     | Endpoints fijos | Consultas dinámicas |
| **Flexibilidad**   | Datos predefinidos | Permite seleccionar qué datos obtener |
| **Uso recomendado** | Integraciones sencillas | Aplicaciones con múltiples consultas diferentes |
| **Eficiencia**     | Puede requerir múltiples requests | Reduce llamadas innecesarias |

???+ example "Ejemplo práctico"  
    - Si queremos una API sencilla para enviar mensajes al grafo, **REST** es suficiente.  
    - Si necesitamos hacer consultas **personalizadas** sobre el estado del grafo, **GraphQL** es mejor opción.  

---

## 🔐 4. Seguridad y Autenticación  

Cuando exponemos una API a internet, debemos **proteger el acceso** con autenticación y permisos.  
Algunas estrategias incluyen:  

✅ **API Keys:** Requieren que cada petición incluya una clave única de acceso.  
✅ **OAuth2/JWT:** Implementan autenticación con tokens seguros.  
✅ **Rate Limiting:** Limita el número de peticiones por usuario para evitar abusos.  

📌 **Ejemplo: Implementando autenticación con API Key**  

[api_auth_placeholder]  

🔹 **Explicación del código:**  
- Requerimos que cada petición incluya una **API Key válida** en los headers.  
- Si la clave no es válida, devolvemos un **error 401 (Unauthorized)**.  
- Podemos extender esto a sistemas más avanzados como **OAuth2 o JWT**.  

???+ warning "Atención"  
    **Nunca expongas tu API sin autenticación** si maneja información sensible o datos privados.  

---

## ✨ **Conclusión**  

Crear una API personalizada para LangGraph nos permite **flexibilizar la integración** con otros sistemas.  

✅ Podemos exponer nuestro grafo mediante **endpoints REST o GraphQL**.  
✅ Podemos optimizar las consultas usando **GraphQL** para reducir el consumo de datos.  
✅ Podemos aplicar **seguridad y autenticación** para controlar el acceso a nuestra API.  

🚀 **Con esta implementación, podemos integrar LangGraph en cualquier ecosistema sin depender solo de la CLI o la SDK.**  

---

## 🧑‍🏫 **¿Qué Hemos Aprendido?**  

- Cómo crear una **API REST** para LangGraph con FastAPI.  
- Cómo exponer consultas **GraphQL** para mayor flexibilidad.  
- Cuáles son las **diferencias entre REST y GraphQL**.  
- Cómo proteger nuestra API con **autenticación** y permisos.  

---

## 🌐 **¿Qué es lo Siguiente?**  

¡Con esto finalizamos el curso 3! 🚀  

En el **Curso 4**, exploraremos **estrategias avanzadas** para optimizar y escalar nuestros grafos en producción, incluyendo:  
✅ **Optimización de performance** en grafos grandes.  
✅ **Despliegue en entornos cloud** como AWS o GCP.  
✅ **Monitorización y logging** para depuración eficiente.  

📢 **¡Prepárate para llevar LangGraph al siguiente nivel!**  
