# 🗄️ Tema 3: Store en LangGraph – Almacenamiento Persistente  

## 🌟 Introducción  

Hasta ahora, hemos explorado diferentes tipos de memoria en LangGraph:  

- **Memoria a Corto Plazo (Short-Term Memory)** con `MemorySaver`, útil para almacenar información dentro de una misma sesión.  
- **Memoria a Largo Plazo (Long-Term Memory)** con `BaseStore`, que nos permite recordar datos entre sesiones.  
- **Memory Schema**, que nos ayuda a estructurar mejor los datos almacenados en la memoria a largo plazo.  

Ahora, profundizaremos en el **Store**, un sistema de almacenamiento flexible en LangGraph que permite guardar, recuperar y gestionar datos de manera persistente.  

## 🧠 ¿Qué es `BaseStore` y por qué es importante?  

`BaseStore` es una clase base en LangGraph que define una interfaz común para almacenar y recuperar datos. Es clave para aplicaciones que necesitan **persistencia de datos**, ya que permite:  

✅ **Guardar información entre sesiones**, facilitando la memoria a largo plazo.  
✅ **Organizar datos de manera estructurada** usando namespaces y claves (`key-value`).  
✅ **Realizar operaciones avanzadas** como búsquedas o manipulación en lote.  

Este sistema es útil en escenarios como:  

- **Chatbots con memoria persistente** (recordar usuarios y sus preferencias).  
- **Asistentes virtuales** que necesitan retener información a largo plazo.  
- **Aplicaciones empresariales** que requieren almacenamiento de datos estructurado.  

???+ quote "Comparación con `MemorySaver`"  
    - **`MemorySaver`** almacena datos **dentro de una única sesión** y se pierde al finalizar.  
    - **`BaseStore`** permite **persistencia entre sesiones**, almacenando datos de forma permanente en bases de datos o archivos.  

---

## 🛠️ Métodos Principales de `BaseStore`  

LangGraph proporciona varios métodos esenciales para gestionar el almacenamiento:  

### 📌 `put(namespace, key, value)` – Guardar Datos  
Guarda un valor en un espacio de nombres (`namespace`) con una clave única (`key`).  

```python
# Guardamos un dato en el Store
store.put(namespace=("usuarios", "user_123"), key="perfil", value={"nombre": "Raúl", "edad": 30})
```

---

### 📌 `get(namespace, key)` – Recuperar Datos  
Obtiene un valor almacenado a partir de su `namespace` y `key`.  

```python
# Recuperamos un dato almacenado
perfil = store.get(namespace=("usuarios", "user_123"), key="perfil")
print(perfil.dict())  # {'nombre': 'Raúl', 'edad': 30}
``` 

---

### 📌 `search(namespace, query)` – Búsqueda de Datos  
Permite buscar valores dentro de un `namespace` utilizando una consulta específica.  

```python
# Buscamos información en el Store
resultados = store.search(namespace=("usuarios", "user_123"), query="Raúl")
print(resultados)
```  

---

### 📌 `batch(namespace, operations)` – Operaciones en Lote  
Ejecuta múltiples operaciones (`put`, `get`, `delete`) en una sola transacción.  

```python
# Realizamos operaciones en lote
operaciones = [
    ("put", "user_123", {"nombre": "Raúl", "edad": 30}),
    ("put", "user_456", {"nombre": "Elena", "edad": 25}),
    ("get", "user_123"),
    ("delete", "user_456"),
]
store.batch(namespace="usuarios", operations=operaciones)
``` 

---

### 📌 `delete(namespace, key)` – Eliminar Datos  
Elimina un valor almacenado a partir de su `namespace` y `key`.  

```python
# Eliminamos un dato del Store
store.delete(namespace=("usuarios", "user_123"), key="perfil")
```

---

## 🏗️ **Ejemplo Práctico: Implementando `BaseStore` en un Grafo**  

Veamos cómo podemos utilizar `BaseStore` en un flujo real dentro de LangGraph.  

📌 **Objetivo:**  
- Guardar información de usuario en el Store.  
- Recuperar y actualizar datos en futuras ejecuciones.  

```python
from langgraph.store.memory import InMemoryStore

# Creamos un almacenamiento en memoria
store = InMemoryStore()

# Definimos un ID de usuario
user_id = "user_123"

# Guardamos datos en el Store
store.put(namespace=("usuarios", user_id), key="perfil", value={"nombre": "Raúl", "edad": 30})

# Recuperamos los datos
perfil = store.get(namespace=("usuarios", user_id), key="perfil")
print(perfil.dict())  # {'nombre': 'Raúl', 'edad': 30}
```

Al ejecutar el flujo, podemos observar cómo el Store **mantiene los datos entre sesiones**, permitiendo un comportamiento más dinámico y personalizado.  

---

## 🛢️ **Integración de `BaseStore` con una Base de Datos Externa**  

Hasta ahora, hemos visto cómo utilizar `BaseStore` con almacenamiento en memoria (`InMemoryStore`). Sin embargo, en aplicaciones del mundo real, es común **persistir los datos en bases de datos externas** para asegurar que la información se mantenga disponible incluso después de reinicios del sistema.  

LangGraph permite integrar `BaseStore` con diferentes tipos de almacenamiento externo, como:  

✅ **Bases de datos SQL** (PostgreSQL, MySQL, SQLite).  
✅ **Bases de datos NoSQL** (MongoDB, Redis, Firebase).  
✅ **Sistemas de almacenamiento en la nube** (Amazon S3, Google Cloud Storage).  

Esto es ideal para **chatbots empresariales, asistentes virtuales y cualquier aplicación que necesite recordar información a lo largo del tiempo**.  

???+ Note "Ventajas de usar una base de datos externa"  
    - **Mayor persistencia:** Los datos no se pierden entre sesiones o reinicios.  
    - **Escalabilidad:** Capacidad para manejar grandes volúmenes de datos.  
    - **Acceso desde múltiples instancias:** Ideal para sistemas distribuidos o aplicaciones web.  

---

## 🔗 **Ejemplo: Conectando `BaseStore` con PostgreSQL**  

A continuación, veremos cómo integrar `BaseStore` con **PostgreSQL** utilizando `psycopg2` como cliente de base de datos.  

📌 **Pasos a seguir:**  
1️⃣ **Configurar la conexión a la base de datos.**  
2️⃣ **Implementar una clase `PostgresStore` que extienda `BaseStore`.**  
3️⃣ **Utilizar `put()`, `get()`, `delete()` y otros métodos con PostgreSQL.**  

???+ Warning "Atención"

    Asegurate de tener instalada la extensión **`langgraph-checkpoint-postgres`** de lo contrario no funcionará.
    ```python
    pip install langgraph-checkpoint-postgres
    ```

```python title="Alternativa 1"
from langgraph.store.postgres import PostgresStore
from psycopg import Connection

conn_string = "postgresql://user:pass@localhost:5432/dbname"

# Usando conexión directa
with Connection.connect(conn_string) as conn:
    store = PostgresStore(conn)
    store.setup() # Ejecutar migraciones. Se realiza una sola vez
```

```python title="Alternativa 2"
from langgraph.store.postgres import PostgresStore

conn_string = "postgresql://user:pass@localhost:5432/dbname"

with PostgresStore.from_conn_string(conn_string) as store:
    store.setup()
```

???+ Warning "Precuación"

    Asegurate de llamar a **`setup()`** antes del primer uso para crear las tablas y los índices necesarios. La extensión pgvector debe estar disponible para utilizar la búsqueda vectorial.

Ahora, en lugar de guardar datos en memoria, estaremos almacenando y recuperando la información directamente desde PostgreSQL.  

---

## 🎯 **Ejemplo de Uso: Guardar y Recuperar Datos en PostgreSQL**  

Ahora que tenemos nuestra implementación de `PostgresStore`, podemos integrarla en un grafo y realizar operaciones básicas como:  

✅ **Guardar información del usuario en la base de datos.**  
✅ **Recuperar información previamente almacenada.**  
✅ **Eliminar registros de la base de datos.**  

```python
# Guardamos un dato en PostgreSQL
store.put(("users", "123"), "prefs", {"theme": "dark"})

# Recuperamos el dato
item = store.get(("users", "123"), "prefs")
print(item)
```

```python title="Resultado"
Item(namespace=['users', '123'], key='prefs', value={'theme': 'dark'}, created_at='2025-02-08T17:46:23.643568+00:00', updated_at='2025-02-08T17:46:23.643568+00:00')
```

Este enfoque nos permite **combinar la flexibilidad de LangGraph con la robustez de una base de datos externa**, asegurando que los datos sean accesibles en múltiples sesiones y dispositivos.  

---

## ✨ **Conclusión**  

El **Store en LangGraph** es una herramienta clave para **gestionar y persistir datos** en nuestros grafos.  

✅ **Permite almacenamiento a largo plazo**, evitando la pérdida de información entre sesiones.  
✅ **Facilita la recuperación y manipulación de datos** con operaciones avanzadas.  
✅ **Se puede integrar con bases de datos externas**, brindando escalabilidad y persistencia real.  

Si queremos que nuestros sistemas recuerden información a largo plazo de manera eficiente, **usar `BaseStore` con una base de datos externa es la mejor opción**. 🚀  

---

## 🧑‍🏫 **¿Qué Hemos Aprendido?**  

- **Cómo funciona `BaseStore` y sus métodos clave (`put`, `get`, `search`, `batch`, `delete`).**  
- **Diferencias entre `MemorySaver`, `InMemoryStore` y `BaseStore`.**  
- **Cómo integrar `BaseStore` con una base de datos externa como PostgreSQL.**  
- **Ventajas de usar almacenamiento persistente para chatbots, asistentes y sistemas empresariales.**  

---

## 🔎 Recursos:

- :simple-googlecolab: Ver notebook en [Google Colab](https://colab.research.google.com/drive/1aj-o4HJ6-BDF9XPEZt9F2r1Zoi2krviP?usp=sharing)
- :books: Definición: [Store](https://langchain-ai.github.io/langgraph/reference/store)
- :books: Definición: [BaseStore](https://langchain-ai.github.io/langgraph/reference/store/#langgraph.store.base.BaseStore)
- :books: Definición: [PostgresStore](https://langchain-ai.github.io/langgraph/reference/store/#langgraph.store.postgres.PostgresStore)
- :books: Definición: [Memory-store](https://langchain-ai.github.io/langgraph/concepts/persistence/#memory-store)
- :books: Definición: [Semantic-search](https://langchain-ai.github.io/langgraph/how-tos/memory/semantic-search)
- :books: Definición: [Semantic-memory](https://langchain-ai.github.io/langgraph/concepts/memory/#semantic-memory)

---

## 🌐 **¿Qué es lo Siguiente?**  

En el próximo tema, exploraremos **cómo integrar bases de datos externas con Store**, optimizando aún más el almacenamiento en nuestros grafos.  
