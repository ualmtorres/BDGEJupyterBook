# Desarrollo de una API REST para gestión de productos y valoraciones con FastAPI y MongoDB

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

## Resumen
Este proyecto tiene como objetivo desarrollar una API REST eficiente y escalable para la gestión de productos y valoraciones en un entorno de comercio electrónico. Utilizando el framework FastAPI y MongoDB, se busca proporcionar una solución flexible y robusta que permita a los desarrolladores crear aplicaciones modernas y de alto rendimiento. MongoDB, con su capacidad para manejar datos no estructurados y su flexibilidad en el esquema, es especialmente adecuado para este tipo de aplicaciones, permitiendo una gestión eficiente de productos y valoraciones. La implementación de esta API no solo facilitará la gestión de productos y categorías, sino que también mejorará la experiencia del usuario al permitir la consulta y gestión de valoraciones y comentarios. Con un entorno de desarrollo configurado mediante Docker y Docker Compose, este proyecto ofrece una base sólida para futuras expansiones y mejoras.

* Desarrollar una API REST utilizando el framework FastAPI y MongoDB para gestionar productos, categorías y valoraciones.
* Configurar un entorno de desarrollo utilizando Docker y Docker Compose.
* Implementar endpoints para la creación, consulta, actualización y eliminación de productos, categorías y valoraciones.
* Proporcionar ejemplos prácticos de uso de MongoDB con Python y FastAPI.
* Realizar pruebas de la API utilizando Postman.

:::{tip}

Disponible el [repositorio de GitHub](https://github.com/ualmtorres/FastAPIMongoDBAPIProductosValoraciones.git) con el código fuente de la API REST.
:::

## Introducción

En el contexto del comercio electrónico, la experiencia del usuario no solo depende de la disponibilidad de productos, sino también de la calidad de la información y las opiniones de otros compradores. Las valoraciones y comentarios juegan un papel crucial en la toma de decisiones de los clientes, ayudando a generar confianza y a mejorar los productos ofrecidos.

MongoDB, como base de datos NoSQL orientada a documentos, permite modelar eficientemente este tipo de información, almacenando productos y sus valoraciones de forma flexible y escalable. En este tutorial se desarrollará una API REST que gestione productos, categorías y valoraciones en un entorno de comercio electrónico.

## Descrición del problema

En este proyecto se va a desarrollar una API REST utilizando MongoDB para gestionar productos y valoraciones en un entorno de comercio electrónico utilizando el framework de Python [FastAPI](https://fastapi.tiangolo.com/) y MongoDB. La API debe permitir:

* La gestión de productos, incluyendo su creación, actualización, eliminación y consulta.
* La organización de productos en categorías con una estructura flexible.
* La gestión de valoraciones y comentarios de los usuarios sobre los productos.
* La consulta eficiente de productos con sus valoraciones agregadas.

## Configuración del entorno de desarrollo

Para configurar el entorno de desarrollo de la API REST, utilizaremos Docker y Docker Compose. El archivo `docker-compose.yml` define los servicios necesarios para ejecutar la aplicación, incluyendo MongoDB, Mongo Express y un contenedor de Python para ejecutar la API REST.

A continuación se muestra el contenido del archivo `docker-compose.yml`:

```yaml
version: "3"
services:
  mongo:
    container_name: mongo
    image: mongo:6.0.4
    restart: always
    ports:
      - 27017:27017
    volumes:
      - "../data/mongo-data/:/data/db"
    environment:
      MONGO_INITDB_ROOT_USERNAME: "${MONGO_INITDB_ROOT_USERNAME}"
      MONGO_INITDB_ROOT_PASSWORD: "${MONGO_INITDB_ROOT_PASSWORD}"
  mongo-express:
    container_name: mongo-express
    image: mongo-express:1.0.0-alpha
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: ${ME_CONFIG_MONGODB_ADMINUSERNAME}
      ME_CONFIG_MONGODB_ADMINPASSWORD: ${ME_CONFIG_MONGODB_ADMINPASSWORD}
      ME_CONFIG_MONGODB_URL: ${ME_CONFIG_MONGODB_URL}
  python:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - "../:/app"
    ports:
      - "8000:8000"
```

Se utiliza un archivo `.env` para definir las variables de entorno necesarias para la configuración de MongoDB y Mongo Express. A continuación se muestra el contenido del archivo `.env`:

```bash
MONGO_INITDB_ROOT_USERNAME=example
MONGO_INITDB_ROOT_PASSWORD=example

# Mongo Express
ME_CONFIG_MONGODB_ADMINUSERNAME=example
ME_CONFIG_MONGODB_ADMINPASSWORD=example
ME_CONFIG_MONGODB_URL=mongodb://example:example@mongo:27017/
```

El archivo `Dockefile` se utiliza para construir el contenedor de Python con las dependencias necesarias para ejecutar la API REST. A continuación se muestra el contenido del archivo `Dockerfile`:

```docker
FROM python:3.9-slim

WORKDIR /app

COPY ./requirements.txt /tmp/requirements.txt

RUN pip install -r /tmp/requirements.txt

COPY ../ ./

EXPOSE 8000

CMD [ "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--reload" ]
```

Como archivo de requisitos, se utiliza `requirements.txt` para instalar las dependencias necesarias para la API REST. A continuación se muestra el contenido del archivo `requirements.txt`:

```bash
fastapi==0.95.2     # Framework para construir APIs REST
uvicorn==0.22.0     # Servidor ASGI para ejecutar aplicaciones FastAPI
pymongo==4.5.0      # Driver de MongoDB para Python
dotenv==0.9.9       # Carga de variables de entorno desde un archivo .env
pydantic==1.10.7    # Biblioteca de validación de datos y serialización de objetos
pytz==2024.1        # Biblioteca para manejar zonas horarias
```

### Servicios

* ***mongo***: Servicio de bases de datos de documentos que se utiliza para almacenar los productos y las valoraciones. Se expone con el nombre `mongo` en el puerto 27017 y utiliza un volumen para almacenar los datos de la base de datos que mapea el directorio `data/mongo-data` al directorio `/data/db` en el contenedor. Se configura con un nombre de usuario y una contraseña para la autenticación.
* ***mongo-express***: Interfaz web para administrar la base de datos de MongoDB. Se expone en el puerto 8081 y se conecta al servicio de MongoDB utilizando las credenciales indicadas.
* ***python***: Contenedor de Python para ejecutar la API REST. Se construye a partir de un archivo `Dockerfile,` que instala las dependencias necesarias y expone el puerto 8000. Se monta la carpeta de la aplicación en el contenedor para facilitar el desarrollo.

### Instrucciones de instalación

Para instalar y ejecutar la aplicación, sigue estos pasos:

1. Clona el repositorio:

   ```bash
   git clone https://github.com/ualmtorres/FastAPIMongoDBAPIProductosValoraciones.git
   cd FastAPIMongoDBAPIProductosValoraciones
   ```
2. Ejecuta Docker Compose para levantar los servicios:

   ```bash
   docker-compose up -d
   ```
3. Accede a la documentación de la API REST en `http://localhost:8000/docs` (Swagger) o `http://localhost:8000/redoc` (Redoc).
4. Accede a los endpoints de la API REST en `http://localhost:8000/api`.

Con estos pasos, tendrás el entorno de desarrollo configurado y la API REST en funcionamiento.

## Desarrollo de la API

En esta sección vamos a crear la API REST para gestionar productos, categorías y valoraciones utilizando el framework FastAPI y MongoDB. Seguiremos un enfoque incremental, comenzando con la configuración general y luego desarrollando cada endpoint paso a paso. Antes, vamos a describir los endpoints disponibles en la API REST.

### Especificación de los endpoints de la API

* Productos
  * `POST /api/products`: Crear un nuevo producto.
  * `GET /api/products`: Obtener todos los productos. Parámetros opcionales de filtrado: `categoryId` y `name`
  * `GET /api/products/{id}`: Obtener un producto por ID.
  * `PUT /api/products/{id}`: Actualizar un producto por ID.
  * `DELETE /api/products/{id}`: Eliminar un producto por ID. También se eliminan los comentarios asociados.
* Categorías
  * `POST /api/categories`: Crear una nueva categoría.
  * `GET /api/categories`: Obtener todas las categorías.
  * `GET /api/categories/tree`: Obtener la jerarquía completa de categorías.
  * `GET /api/categories/{id}`: Obtener una categoría por ID.
  * `PUT /api/categories/{id}`: Actualizar una categoría por ID.
  * `DELETE /api/categories/{id}`: Eliminar una categoría por ID.
* Comentarios
  * `POST /api/reviews`: Añadir un comentario a un producto.
  * `GET /api/reviews`: Obtener todos los comentarios. Parámetros opcionales de filtrado: `productId` y `userId`
  * `PUT /api/reviews/{id}`: Actualizar un comentario (solo por el usuario que lo creó).
  * `DELETE /api/reviews/{id}`: Eliminar un comentario por ID (solo por el usuario que lo creó).

#### Ejemplo de JSON de un producto

```json
{
    "name": "Producto 1",
    "description": "Descripción del producto 1",
    "price": 100,
    "categoryId": "60c72b2f9b1d8b3a4c8b4567"
}
```

#### Ejemplo de JSON de una categoría

```json
{
    "name": "Categoría 1",
    "description": "Descripción de la categoría 1",
    "parentId": "60c72b2f9b1d8b3a4c8b4567"
}
```

#### Ejemplo de JSON de un comentario

```json
{
    "productId": "60c72b2f9b1d8b3a4c8b4567",
    "userId": "60c72b2f9b1d8b3a4c8b4567",
    "username": "usuario123",
    "rating": 4.5,
    "comment": "Muy buen producto, la batería dura bastante."
}
```

### Crear la aplicación FastAPI

FastAPI es un framework moderno y de alto rendimiento para construir una APIs REST utilizando Python. Está diseñado para ser fácil de usar, rápido y eficiente, aprovechando las características más recientes de Python, como las anotaciones de tipo (_type hints_). Algunas de sus características principales incluyen:

* Alto rendimiento: Basado en Starlette y Pydantic, lo que lo hace extremadamente rápido.
* Validación automática: Utiliza las anotaciones de tipo para validar automáticamente las entradas y salidas.
* Documentación automática: Genera automáticamente documentación interactiva para las APIs. Genera documentación [Swagger](https://swagger.io/) y [ReDoc](https://redoc.ly/).
* Asincronía: Soporta programación asíncrona con `async` y `await`.

Para instalar FastAPI es necesario Python 3.7 o superior. A continuación se indican los pasos para instalar FastAPI y un servidor ASGI (por ejemplo, Uvicorn) y verificar la instalación. Un servidor ASGI es un servidor web que puede manejar conexiones asíncronas y es necesario para ejecutar aplicaciones FastAPI.

:::{note}

***Instalación de FastAPI***

Para crear una API REST con FastAPI es necesario instalar el framework y un servidor ASGI. El entorno de desarrollo que estamos usando en este tutorial ya lo tiene configurado, pero si no estuvieran instalados bastaría instalarlos con

```bash
pip install "fastapi[standard]"
pip install uvicorn
```
:::

Para comenzar a desarrollar la API REST con FastAPI, basta con crear un archivo `main.py` con el siguiente código y ejecutar el servidor Uvicorn, si no se ha hecho ya (En el caso de usar el entorno con Docker del tutorial, el servidor Uvicorn ya se está ejecutando en el contenedor de Python):

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def read_root():
    return {"message": "Hello, World!"}
```

Si se opta por ejecutar el servidor Uvicorn en local, se pone en marcha con el siguiente comando:

```bash
uvicorn main:app --reload 
```

El servidor Uvicorn se estaría ejecutando en modo de recarga automática. Por tanto, cada vez que se realice un cambio en el código, el servidor se reiniciará automáticamente y se aplicarán los cambios. Si se abre un navegador y se accede a `http://localhost:8000`, se debería ver el mensaje "Hello, World!".

![Hello](images/hello-world.png)

El servidor Uvicorn se ejecuta en el puerto 8000 por defecto. Si se desea usar otro puerto, se puede especificar con el parámetro `--port`. El siguiente comando ejecuta el servidor Uvicorn en el puerto 8000 con recarga automática.

```bash
uvicorn main:app --reload --port 8000
```

### Swagger y ReDoc

[Swagger](https://swagger.io/) y [ReDoc](https://redoc.ly/) son herramientas de documentación de APIs que permiten visualizar y probar las rutas de la API de forma interactiva. FastAPI genera automáticamente documentación interactiva para las APIs en formato Swagger y ReDoc. La documentación se genera automáticamente a partir de las anotaciones de tipo y los esquemas definidos en el código. Los esquemas de datos se utilizan para validar las entradas y salidas de las rutas de la API y los definiremos utilizando [Pydantic](https://pydantic-docs.helpmanual.io/). Pydantic es una biblioteca de validación de datos y serialización de objetos que se integra perfectamente con FastAPI.

La documentación Swagger generada está disponible en la ruta `/docs` de la aplicación FastAPI. La figura siguiente muestra la página de documentación de Swagger.

![Swagger UI](images/swagger-ui.png)

Es posible cambiar el título y la descripción de la documentación de Swagger utilizando los parámetros `title` y `description` al crear la aplicación FastAPI. Por ejemplo, se pueden hacer los siguientes cambios en el archivo `main.py`:

```python
app = FastAPI(
    title="Products and Reviews API",
    description="API for managing products and reviews using FastAPI and MongoDB",
    version="1.0.0"
)
```

Después de este cambio, la documentación de Swagger mostrará el título y la descripción personalizados, como ilustra la figura siguiente.

Por otro lado, ReDoc es otra herramienta de documentación que presenta las rutas de la API de manera más estructurada y profesional. Está disponible en la ruta `/redoc`. La figura siguiente muestra la página de documentación de ReDoc.

![ReDoc](images/redoc.png)

Ambas herramientas son generadas automáticamente por FastAPI a partir de las anotaciones de tipo y los esquemas definidos en el código. Esto elimina la necesidad de escribir documentación manualmente y asegura que siempre esté sincronizada con la implementación de la API.

### Organización del código

Para mantener el código organizado y facilitar el desarrollo, se recomienda dividir la aplicación en módulos y archivos separados. En FastAPI, se pueden definir las rutas y la lógica de negocio en módulos separados, y luego importarlos en el archivo principal de la aplicación. Esto facilita la gestión de rutas y la escalabilidad de la aplicación. A continuación se muestra una propuesta de organización de código en FastAPI.

```
FastAPIMongoDBAPIProductosValoraciones/
│
├── __init__.py         # Archivo de inicialización. Puede estar vacío. Permite que Python trate el directorio como un paquete.
├── .env                # Archivo de variables de entorno
├── main.py             # Archivo principal de la aplicación
├── requirements.txt    # Archivo de dependencias
├── database.py         # Configuración de la base de datos
├── models.py           # Definición de modelos Pydantic
├── start.sh            # Script de inicio
├── routes/             # Directorio de rutas
│   ├── products.py     # Rutas para productos
│   ├── categories.py   # Rutas para categorías
│   ├── reviews.py      # Rutas para valoraciones
│   ├── test.py         # Rutas de prueba
```

### Primeros archivos

A continuación se muestra el contenidos de los primeros archivos con los que arrancaremos la API. Eliminaremos la ruta (`/`) que hemos creado anteriormente a modo de prueba y añadiremos una nueva ruta de prueba (`/test`), pero ya en un archivo separado y en la carpeta `routes`.

***Archivo `.env`***

```python
MONGO_USER = example
MONGO_PASSWORD = example
MONGO_HOST = mongo
```

:::{note}

En este caso el valor de `mongo` se refiere al nombre del servicio MongoDB definido en el archivo `docker-compose.yml`. Este es el mismo nombre que se utiliza en el archivo `docker-compose.yml`. Si se cuenta con un servidor MongoDB en una URL específica, se puede cambiar el valor de `MONGO_HOST` por la URL del servidor.
:::

***Archivo `database.py`***

```python
from pymongo import MongoClient
import os
from dotenv import load_dotenv

load_dotenv()

username = os.environ.get('MONGO_USER')
password = os.environ.get('MONGO_PASSWORD')
host = os.environ.get('MONGO_HOST')
uri = f"mongodb://{username}:{password}@{host}:27017/?authSource=admin"

client = MongoClient(uri)
db = client["mydatabase"]
products_collection = db["products"]
reviews_collection = db["reviews"]
categories_collection = db["categories"]
```

:::{note}

La conexión a MongoDB se hace a través del método `MongoClient` de la biblioteca `pymongo`. Se utilizan las variables de entorno definidas en el archivo `.env` para la configuración de la conexión. Una vez establecida la conexión, se accede a la base de datos como si fuera un diccionario y se obtienen las colecciones de productos, valoraciones y categorías.
:::

***Archivo `models.py`***

Definimos aquí las clases Pydantic que representan los modelos de datos de la aplicación. Estos modelos se utilizan para validar los datos de entrada y salida de las rutas de la API. Por ejemplo, el modelo `Product` se utiliza para validar los datos de un producto y el modelo `ProductResponse` se utiliza para devolver los datos de un producto con su ID. Para definir las clases usaremos la especificación de los JSON que se ha hecho en la sección [Especificación de los endpoints de la API](#Especificación de los endpoints de la API).

Definiremos también clases de respuesta para los modelos de datos, que incluyen el ID del documento en MongoDB y las fechas de creación y actualización.

```python
from pydantic import BaseModel
from typing import Optional, List

class Product(BaseModel):
    name: str
    description: str
    price: float
    categoryId: str

class ProductResponse(Product):
    id: str

class Category(BaseModel):
    name: str
    description: str
    parentId: Optional[str] = None

class CategoryResponse(Category):
    id: str

class Review(BaseModel):
    productId: str
    userId: str
    username: str
    rating: float
    comment: str

class ReviewResponse(Review):
    id: str
    createdAt: str
    updatedAt: Optional[str] = None
````

***Archivo `routes/test.py`***

```python
from fastapi import APIRouter

router = APIRouter()

# Define a test API endpoint
@router.get("/")
async def test_api():
    return {"message": "API is working"}
```

:::{note}

En este archivo definimos un endpoint de prueba `GET /api/test` que devuelve un mensaje de prueba. Este endpoint se utilizará para comprobar que la API está funcionando correctamente. El código del endpoint se define en una función asíncrona que devuelve un diccionario con el mensaje "API is working".
:::

***Archivo `main.py`***

```python
from fastapi import FastAPI
from routes import test

app = FastAPI()

app.include_router(test.router, prefix="/api/test", tags=["test"])
```

:::{note}

En este archivo creamos la aplicación FastAPI y añadimos el router de prueba que definimos anteriormente. El router se incluye en la aplicación con el prefijo `/api/test` y se le asigna la etiqueta `test`. Esto significa que el endpoint de prueba estará disponible en la ruta `/api/test` y se mostrará en la documentación de la API con la etiqueta `test`.
:::

Tras esto, la API REST debería estar funcionando correctamente. Se puede probar accediendo a la URL `http://localhost:8000/api/test` en un navegador o utilizando una herramienta como Postman. También se puede probar la documentación de la API en `http://localhost:8000/docs` o `http://localhost:8000/redoc`. La respuesta debería ser un mensaje JSON con el texto "API is working". La figura siguiente ilustra la llamada al endpoint de prueba desde Swagger.

![Endpoint de prueba](images/test-api.png)

### Endpoints de categorías

En esta sección vamos a implementar los endpoints para gestionar categorías en la API REST. Los endpoints incluyen la creación, consulta, actualización y eliminación de categorías.

En el archivo `routes/categories.py` añade el siguiente código antes de la definición de las rutas de categorías:

```python
from fastapi import APIRouter, HTTPException
from bson import ObjectId
from typing import List
from models import Category, CategoryResponse
from database import categories_collection

router = APIRouter()

// Los endpoints de categorías se definen aquí
```

#### Añadir el enrutado de categorías a `main.py`

Modificar el archivo `main.py` para incluir el enrutado de categorías en la aplicación FastAPI. Se harán modificaciones para importar el router de categorías y añadirlo a la aplicación con el prefijo `/api/categories` y la etiqueta `categories`.
```python
from fastapi import FastAPI
from routes import test, categories # Importar el router de categorías

app = FastAPI(
    title="Products and Reviews API",
    description="API for managing products and reviews using FastAPI and MongoDB",
    version="1.0.0"
)

app.include_router(test.router, prefix="/api/test", tags=["test"])
app.include_router(categories.router, prefix="/api/categories", tags=["categories"])    # Añadir el router de categorías
```

#### Endpoint para crear una categoría

Añade el siguiente código para definir un endpoint `POST /api/categories` que permite crear una nueva categoría. Este endpoint recibe los datos de la categoría en formato JSON en el cuerpo de la solicitud y los inserta en la colección de categorías de MongoDB. En el cuerpo de la solicitud se deben proporcionar los siguientes campos: `name`, `description` y `parentId`. El endpoint devuelve un mensaje con el ID de la nueva categoría creada. La operación MongoDB que se usaría para insertar un documento en la colección de categorías sería similar a la siguiente:

```
db.categories.insertOne({
    name: "Categoría 1",
    description: "Descripción de la categoría 1",
    parentId: ObjectId("60c72b2f9b1d8b3a4c8b4567")
});
```

```python
# Route to create a new category
@router.post("/", response_model=CategoryResponse, status_code=201)
async def create_category(category: Category):
    category_dict = category.dict()
    result = categories_collection.insert_one(category_dict)
    return {**category_dict, "id": str(result.inserted_id)}
```

#### Endpoint para obtener todas las categorías incluyendo la jerarquía

Añade el siguiente código para definir un endpoint `GET /api/categories/tree` que permite obtener la jerarquía completa de categorías. Este endpoint consulta todas las categorías de la colección de categorías de MongoDB y construye la jerarquía de categorías en forma de árbol. Para ello, usa una función auxiliar recursiva que recorre las categorías y sus subcategorías. El endpoint devuelve la jerarquía de categorías en formato JSON. La operación MongoDB que se usaría para consultar todas las categorías en la colección de categorías sería similar a la siguiente:

```
db.categories.find();
```

```python
# Define a function to build a category tree
def build_category_tree(categories, parent_id=None):
    branch = []
    for category in categories:
        if category["parentId"] == parent_id:
            children = build_category_tree(categories, category["id"])
            if children:
                category["children"] = children
            branch.append(category)
    return branch

# Route to get the category tree
@router.get("/tree", response_model=List[CategoryResponse])
async def get_category_tree():
    categories = list(categories_collection.find())
    for category in categories:
        category["id"] = str(category["_id"])
        category["parentId"] = str(category["parentId"]) if category["parentId"] else None
        del category["_id"]
    return build_category_tree(categories)
```

**Conflictos en la definición de rutas o _route shadowing_**

Este endpoint `GET /api/categories/tree` se debe colocar antes que el endpoint `GET /api/categories` para que no haya conflictos en la definición de rutas. Estos conflictos pueden ocurrir si las rutas tienen el mismo prefijo y solo difieren en un parámetro opcional. En ese caso, colocaremos primero la ruta más específica y luego la más general, ya que el framework evalúa las rutas en el orden en que se definen y selecciona la primera coincidencia encontrada.
Así evitaremos lo que se conoce como "route shadowing", que consiste en que una ruta más específica sea ignorada en favor de una ruta más general porque se definió primero.

#### Endpoint para obtener todas las categorías

Añade el siguiente código para definir un endpoint `GET /api/categories` que permite obtener todas las categorías. Este endpoint consulta todas las categorías de la colección de categorías de MongoDB y las devuelve en formato JSON. La operación MongoDB que se usaría para consultar todas las categorías en la colección de categorías sería similar a la siguiente:

```
db.categories.find();
```

```python
# Route to get all categories
@router.get("/", response_model=List[CategoryResponse])
async def get_categories():
    categories = list(categories_collection.find())
    for category in categories:
        category["id"] = str(category["_id"])
        del category["_id"]
    return categories
```

#### Endpoint para obtener una categoría por ID

Añade el siguiente código para definir un endpoint `GET /api/categories/{id}` que permite obtener una categoría por su ID. Este endpoint recibe el ID de la categoría como parámetro en la URL y busca la categoría correspondiente en la colección de categorías de MongoDB. Si la categoría existe, se devuelve su información en formato JSON. Si la categoría no existe, se devuelve un mensaje de error. La operación MongoDB que se usaría para buscar una categoría en la colección de categorías sería similar a la siguiente:

```
db.categories.findOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to get a category by ID
@router.get("/{id}", response_model=CategoryResponse)
async def get_category(id: str):
    try:
        category = categories_collection.find_one({"_id": ObjectId(id)})
        if not category:
            raise HTTPException(status_code=404, detail="Category not found")
        category["id"] = str(category["_id"])
        del category["_id"]
        return category
    except Exception:
        raise HTTPException(status_code=400, detail="Invalid category ID")
```

#### Endpoint para actualizar una categoría

Añade el siguiente código para definir un endpoint `PUT /api/categories/{id}` que permite actualizar una categoría por su ID. Este endpoint recibe el ID de la categoría como parámetro en la URL y los nuevos datos de la categoría en formato JSON en el cuerpo de la solicitud. Los datos de la categoría a actualizar deben incluir los campos `name`, `description` y `parentId`. El endpoint actualiza la categoría correspondiente en la colección de categorías de MongoDB. Si la categoría se actualiza correctamente, se devuelve un mensaje de éxito. Si la categoría no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para actualizar una categoría en la colección de categorías sería similar a la siguiente:

```
db.categories.updateOne(
    { _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") },
    { $set: { name: "Nueva categoría", description: "Nueva descripción", parentId: ObjectId("60c72b2f9b1d8b3a4c8b4567") } }
);
```

```python
# Route to update a category by ID
@router.put("/{id}", response_model=CategoryResponse)
async def update_category(id: str, category: Category):
    try:
        result = categories_collection.update_one(
            {"_id": ObjectId(id)},
            {"$set": category.dict()}
        )
        if result.matched_count == 0:
            raise HTTPException(status_code=404, detail="Category not found")
        updated_category = categories_collection.find_one({"_id": ObjectId(id)})
        updated_category["id"] = str(updated_category["_id"])
        del updated_category["_id"]
        return updated_category
    except Exception:
        raise HTTPException(status_code=400, detail="Invalid category ID")
```

#### Endpoint para eliminar una categoría

Añade el siguiente código para definir un endpoint `DELETE /api/categories/{id}` que permite eliminar una categoría por su ID. Este endpoint recibe el ID de la categoría como parámetro en la URL y elimina la categoría correspondiente de la colección de categorías de MongoDB. Si la categoría se elimina correctamente, se devuelve un mensaje de éxito. Si la categoría no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para eliminar una categoría en la colección de categorías sería similar a la siguiente:

```
db.categories.deleteOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to delete a category by ID
@router.delete("/{id}", status_code=200)
async def delete_category(id: str):
    try:
        result = categories_collection.delete_one({"_id": ObjectId(id)})
        if result.deleted_count == 0:
            raise HTTPException(status_code=404, detail="Category not found")
        return {"message": "Category deleted"}
    except Exception:
        raise HTTPException(status_code=400, detail="Invalid category ID")
```

### Endpoints de productos

En esta sección vamos a implementar los endpoints para gestionar productos en la API REST. Los endpoints incluyen la creación, consulta, actualización y eliminación de productos.

En el archivo `routes/products.py` añade el siguiente código antes de la definición de las rutas de productos:

```python
from fastapi import APIRouter, HTTPException
from bson import ObjectId
from typing import List
from models import Product, ProductResponse
from database import products_collection, reviews_collection

router = APIRouter()

// Los endpoints de productos se definen aquí
```

#### Añadir el enrutado de productos a `main.py`

Modificar el archivo `main.py` para incluir el enrutado de productos en la aplicación FastAPI. Se harán modificaciones para importar el router de productos y añadirlo a la aplicación con el prefijo `/api/products` y la etiqueta `products`.

```python
from fastapi import FastAPI
from routes import test, products, categories   # Importar el router de productos

app = FastAPI(
    title="Products and Reviews API",
    description="API for managing products and reviews using FastAPI and MongoDB",
    version="1.0.0"
)

app.include_router(test.router, prefix="/api/test", tags=["test"])
app.include_router(products.router, prefix="/api/products", tags=["products"]) # Añadir el router de productos
app.include_router(categories.router, prefix="/api/categories", tags=["categories"])
```

#### Endpoint para crear un producto

Añade el siguiente código para definir un endpoint `POST /api/products` que permite crear un nuevo producto. Este endpoint recibe los datos del producto en formato JSON en el cuerpo de la solicitud y los inserta en la colección de productos de MongoDB. En el cuerpo de la solicitudo se deben proporcionar los siguientes campos: `name`, `description`, `price` y `categoryId`. El endpoint devuelve un mensaje con el ID del nuevo producto creado. La operación MongoDB que se usaría para insertar un documento en la colección de productos sería similar a la siguiente:

```
db.products.insertOne({
    name: "Producto 1",
    description: "Descripción del producto 1",
    price: 100,
    categoryId: ObjectId("60c72b2f9b1d8b3a4c8b4567")
});
```

```python
# Route to create a new product
@router.post("/", response_model=ProductResponse, status_code=201)
async def create_product(product: Product):
    product_dict = product.dict()
    result = products_collection.insert_one(product_dict)
    return {**product_dict, "id": str(result.inserted_id)}
```

#### Endpoint para obtener detalles de un producto

Añade el siguiente código para definir un endpoint `GET /api/products/{id}` que permite obtener los detalles de un producto por su ID. Este endpoint recibe el ID del producto como parámetro en la URL y busca el producto correspondiente en la colección de productos de MongoDB. Si el producto existe, se devuelve su información en formato JSON. Si el producto no existe, se devuelve un mensaje de error. La operación MongoDB que se usaría para buscar un documento en la colección de productos sería similar a la siguiente:

```
db.products.findOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to get a product by ID
@router.get("/{id}", response_model=ProductResponse)
async def get_product(id: str):
    try:
        product = products_collection.find_one({"_id": ObjectId(id)})
        if not product:
            raise HTTPException(status_code=404, detail="Product not found")
        product["id"] = str(product["_id"])
        del product["_id"]
        return product
    except Exception:
        raise HTTPException(status_code=400, detail="Invalid product ID")
```

#### Endpoint para consultar productos

Añade el siguiente código para definir un endpoint `GET /api/products` que permite consultar productos. Este endpoint admite parámetros de consulta opcionales para filtrar los productos por categoría (`categoryId`) o por nombre (`name`). Si se proporciona el parámetro `categoryId`, se filtran los productos por la categoría especificada. Si se proporciona el parámetro `name`, se filtran los productos por el nombre que coincida con la cadena especificada. Se pueden combinar ambos parámetros para realizar búsquedas más específicas. Esta operación devuelve una lista de productos en formato JSON. La operación MongoDB que se usaría para consultar productos en la colección de productos sería similar a la siguiente:

```
db.products.find({ categoryId: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to get all products
@router.get("/", response_model=List[ProductResponse])
async def get_products(categoryId: Optional[str] = None, name: Optional[str] = None):
    query = {}
    if categoryId:
        try:
            query["categoryId"] = categoryId
        except Exception:
            raise HTTPException(status_code=400, detail="Invalid category ID")
    if name:
        query["name"] = {"$regex": name, "$options": "i"}
    products = list(products_collection.find(query))
    for product in products:
        product["id"] = str(product["_id"])
        del product["_id"]
    return products
```

#### Endpoint para actualizar un producto

Añade el siguiente código para definir un endpoint `PUT /api/products/{id}` que permite actualizar un producto por su ID. Este endpoint recibe el ID del producto como parámetro en la URL y los nuevos datos del producto en formato JSON en el cuerpo de la solicitud. Los datos del producto a actualizar deben incluir los campos `name`, `description`, `price` y `categoryId`. El endpoint actualiza el producto correspondiente en la colección de productos de MongoDB. Si el producto se actualiza correctamente, se devuelve un mensaje de éxito. Si el producto no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para actualizar un documento en la colección de productos sería similar a la siguiente:

```
db.products.updateOne(
    { _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") },
    { $set: {
        name: "Producto 2",
        description: "Descripción del producto 2",
        price: 150,
        categoryId: ObjectId("60c72b2f9b1d8b3a4c8b4568")
    }}
);
```

```python
# Route to update a product by ID
@router.put("/{id}", response_model=ProductResponse)
async def update_product(id: str, product: Product):
    try:
        result = products_collection.update_one(
            {"_id": ObjectId(id)},
            {"$set": product.dict()}
        )
        if result.matched_count == 0:
            raise HTTPException(status_code=404, detail="Product not found")
        updated_product = products_collection.find_one({"_id": ObjectId(id)})
        updated_product["id"] = str(updated_product["_id"])
        del updated_product["_id"]
        return updated_product
    except Exception:
        raise HTTPException(status_code=400, detail="Invalid product ID")
```

#### Endpoint para eliminar un producto

Añade el siguiente código para definir un endpoint `DELETE /api/products/{id}` que permite eliminar un producto por su ID. Este endpoint recibe el ID del producto como parámetro en la URL y elimina el producto correspondiente de la colección de productos de MongoDB. También elimina los comentarios asociados al producto. Si el producto se elimina correctamente, se devuelve un mensaje de éxito. Si el producto no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para eliminar un documento en la colección de productos sería similar a la siguiente:

```
db.products.deleteOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to delete a product by ID
@router.delete("/{id}", status_code=200)
async def delete_product(id: str):
    try:
        result = products_collection.delete_one({"_id": ObjectId(id)})
        if result.deleted_count == 0:
            raise HTTPException(status_code=404, detail="Product not found")
        reviews_collection.delete_many({"productId": ObjectId(id)})
        return {"message": "Product and associated reviews deleted"}
    except Exception:
        raise HTTPException(status_code=400, detail="Invalid product ID")
```

### Endpoints de comentarios

En esta sección, vamos a implementar los endpoints para gestionar comentarios en la API REST. Los endpoints incluyen la creación, consulta, actualización y eliminación de comentarios.

En el archivo `routes/reviews.py` añade el siguiente código antes de la definición de las rutas de comentarios:

```python
from fastapi import APIRouter, HTTPException
from bson import ObjectId
from typing import List, Optional
from datetime import datetime
from models import Review, ReviewResponse
from database import reviews_collection
import pytz     # Importar la biblioteca pytz para trabajar con zonas horarias

router = APIRouter()

// Los endpoints de comentarios se definen aquí
```

#### Añadir el enrutado de comentarios a `main.py`

Modificar el archivo `main.py` para incluir el enrutado de comentarios en la aplicación FastAPI. Se harán modificaciones para importar el router de comentarios y añadirlo a la aplicación con el prefijo `/api/reviews` y la etiqueta `reviews`.

```python
from fastapi import FastAPI
from routes import test, products, categories, reviews  # Importar el router de comentarios

app = FastAPI(
    title="Products and Reviews API",
    description="API for managing products and reviews using FastAPI and MongoDB",
    version="1.0.0"
)

app.include_router(test.router, prefix="/api/test", tags=["test"])
app.include_router(products.router, prefix="/api/products", tags=["products"])
app.include_router(categories.router, prefix="/api/categories", tags=["categories"])
app.include_router(reviews.router, prefix="/api/reviews", tags=["reviews"])  # Añadir el router de comentarios
```

#### Endpoint para añadir un comentario a un producto

Añade el siguiente código para definir un endpoint `POST /api/reviews` que permite añadir un comentario a un producto. Este endpoint recibe los datos del comentario en formato JSON en el cuerpo de la solicitud y los inserta en la colección de comentarios de MongoDB. En el cuerpo de la solicitud se deben proporcionar los siguientes campos: `productId`, `userId`, `username`, `rating` y `comment`. El endpoint devuelve un mensaje con el ID del nuevo comentario creado. La operación MongoDB que se usaría para insertar un documento en la colección de comentarios sería similar a la siguiente:

```
db.reviews.insertOne({
    productId: ObjectId("60c72b2f9b1d8b3a4c8b4567"),
    userId: ObjectId("60c72b2f9b1d8b3a4c8b4568"),
    username: "usuario123",
    rating: 4.5,
    comment: "Muy buen producto, la batería dura bastante.",
    createdAt: new Date()
});
```

```python
# Route to create a new review
@router.post("/", response_model=ReviewResponse, status_code=201)
async def create_review(review: Review):
    try:
        review_dict = review.dict()
        review_dict["productId"] = ObjectId(review.productId)
        review_dict["userId"] = ObjectId(review.userId)
        review_dict["createdAt"] = datetime.now(pytz.UTC)
        
        result = reviews_collection.insert_one(review_dict)
        review_dict["id"] = str(result.inserted_id)
        review_dict["productId"] = str(review_dict["productId"])
        review_dict["userId"] = str(review_dict["userId"])
        review_dict["createdAt"] = review_dict["createdAt"].strftime("%Y-%m-%dT%H:%M:%SZ")
        
        return review_dict
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
```

#### Endpoint para obtener los comentarios de un producto o por usuario

Añade el siguiente código para definir un endpoint `GET /api/reviews` que permite obtener los comentarios de un producto o por usuario. Este endpoint admite parámetros de consulta opcionales para filtrar los comentarios por producto (`productId`) o por usuario (`userId`). Si se proporciona el parámetro `productId`, se filtran los comentarios por el producto especificado. Si se proporciona el parámetro `userId`, se filtran los comentarios por el usuario especificado. Se pueden combinar ambos parámetros para realizar búsquedas más específicas. Esta operación devuelve una lista de comentarios en formato JSON. La operación MongoDB que se usaría para consultar comentarios en la colección de comentarios sería similar a la siguiente:

```
db.reviews.find({ productId: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to get all reviews or filter by productId or userId
@router.get("/", response_model=List[ReviewResponse])
async def get_reviews(productId: Optional[str] = None, userId: Optional[str] = None):
    try:
        query = {}
        if productId:
            query["productId"] = ObjectId(productId)
        if userId:
            query["userId"] = ObjectId(userId)

        reviews = list(reviews_collection.find(query))
        for review in reviews:
            review["id"] = str(review.pop("_id"))
            review["productId"] = str(review["productId"])
            review["userId"] = str(review["userId"])
            review["createdAt"] = review["createdAt"].strftime("%Y-%m-%dT%H:%M:%SZ")
            if "updatedAt" in review:
                review["updatedAt"] = review["updatedAt"].strftime("%Y-%m-%dT%H:%M:%SZ")
        return reviews
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
```

#### Endpoint para actualizar un comentario

Añade el siguiente código para definir un endpoint `PUT /api/reviews/{id}` que permite actualizar un comentario por su ID. Este endpoint recibe el ID del comentario como parámetro en la URL y los nuevos datos del comentario en formato JSON en el cuerpo de la solicitud. Los datos del comentario a actualizar deben incluir los campos `userId`, `rating` y `comment`. El endpoint actualiza el comentario correspondiente en la colección de comentarios de MongoDB. Como medida de seguridad se verifica que el usuario que intenta actualizar el comentario sea el mismo que lo creó. Si el comentario se actualiza correctamente, se devuelve un mensaje de éxito. Si el comentario no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para actualizar un documento en la colección de comentarios sería similar a la siguiente:

```
db.reviews.updateOne(
    { _id: ObjectId("60c72b2f9b1d8b3a4c8b4567"), userId: ObjectId("60c72b2f9b1d8b3a4c8b4568") },
    { $set: {
        rating: 4.0,
        comment: "Muy buen producto, la batería dura bastante.",
        updatedAt: new Date()
    }}
);
```

```python
# Route to update a review by ID
@router.put("/{id}", response_model=ReviewResponse)
async def update_review(id: str, review: Review):
    try:
        existing_review = reviews_collection.find_one({
            "_id": ObjectId(id),
            "userId": ObjectId(review.userId)
        })
        
        if not existing_review:
            raise HTTPException(status_code=403, detail="You can only update your own reviews")
            
        update_data = {
            "rating": review.rating,
            "comment": review.comment,
            "updatedAt": datetime.now(pytz.UTC)
        }
        
        result = reviews_collection.update_one(
            {"_id": ObjectId(id)},
            {"$set": update_data}
        )
        
        if result.matched_count == 0:
            raise HTTPException(status_code=404, detail="Review not found")
            
        updated_review = reviews_collection.find_one({"_id": ObjectId(id)})
        updated_review["id"] = str(updated_review.pop("_id"))
        updated_review["productId"] = str(updated_review["productId"])
        updated_review["userId"] = str(updated_review["userId"])
        updated_review["createdAt"] = updated_review["createdAt"].strftime("%Y-%m-%dT%H:%M:%SZ")
        updated_review["updatedAt"] = updated_review["updatedAt"].strftime("%Y-%m-%dT%H:%M:%SZ")
        
        return updated_review
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
```

#### Endpoint para eliminar un comentario

Añade el siguiente código para definir un endpoint `DELETE /api/reviews/{id}` que permite eliminar un comentario por su ID. Este endpoint recibe el ID del comentario como parámetro en la URL y elimina el comentario correspondiente de la colección de comentarios de MongoDB. Como medida de seguridad se verifica que el usuario que intenta eliminar el comentario sea el mismo que lo creó. Si el comentario se elimina correctamente, se devuelve un mensaje de éxito. Si el comentario no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para eliminar un documento en la colección de comentarios sería similar a la siguiente:

```
db.reviews.deleteOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```python
# Route to delete a review by ID
@router.delete("/{id}")
async def delete_review(id: str, request_body: dict):
    try:
        userId = request_body.get("userId")
        if not userId:
            raise HTTPException(status_code=400, detail="userId is required in request body")

        existing_review = reviews_collection.find_one({
            "_id": ObjectId(id),
            "userId": ObjectId(userId)
        })
        
        if not existing_review:
            raise HTTPException(status_code=403, detail="You can only delete your own reviews")
            
        result = reviews_collection.delete_one({"_id": ObjectId(id)})
        
        if result.deleted_count == 0:
            raise HTTPException(status_code=404, detail="Review not found")
            
        return {"message": "Review deleted"}
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
```

## Indices para mejorar el rendimiento

Los índices son cruciales para mejorar el rendimiento de las consultas en una base de datos. Sin índices, MongoDB debe realizar un escaneo completo de la colección para encontrar los documentos que coinciden con la consulta, lo que puede ser muy ineficiente y lento, especialmente en colecciones grandes. Los índices permiten a MongoDB buscar de manera más eficiente, reduciendo el tiempo de respuesta de las consultas y mejorando el rendimiento general de la aplicación.

En el contexto de esta API, los índices pueden ayudar a acelerar las consultas que filtran productos por categoría, buscan productos por nombre, o filtran comentarios por producto o usuario. Al definir índices en los campos más consultados, se puede reducir significativamente el tiempo de respuesta de estos endpoints, proporcionando una mejor experiencia de usuario.

En MongoDB se pueden definir varios tipos de índices, cada uno con un propósito específico:

* ***Indice único***: Garantiza que los valores de un campo sean únicos en toda la colección.
* ***Indice compuesto***: Un índice en múltiples campos de un documento.
* ***Indice de texto***: Permite realizar búsquedas de texto completo en los campos indexados.
* ***Indice geoespacial***: Permite realizar consultas geoespaciales en los datos.
* ***Indice parcial***: Indexa solo los documentos que cumplen con una condición especificada.
* ***Indice TTL (Time To Live)***: Elimina automáticamente los documentos después de un período de tiempo especificado.

### Definir índices en MongoDB

Para definir índices en MongoDB, se utiliza el método `createIndex` en la colección correspondiente. A continuación se muestran algunos ejemplos de cómo definir diferentes tipos de índices:

* ***Indice en un solo campo***:

  ```bash
  db.products.createIndex({ categoryId: 1 })
  ```
* ***Indice compuesto***:

  ```bash
  db.products.createIndex({ categoryId: 1, name: 1 })
  ```

  :::{note}

  En un índice compuesto, el orden de los campos es importante. En el ejemplo anterior, el índice se crea en los campos `categoryId` y `name`, en ese orden. Esto significa que el índice se ordenará primero por `categoryId` y luego por `name`. Esto es útil para consultas que filtran por `categoryId` y ordenan por `name`. Sin embargo, si las consultas se realizaran en el orden contrario (filtrar por `name` y ordenar por `categoryId`), el índice no sería tan eficiente. Por tanto, los índices compuestos deben diseñarse cuidadosamente en función de las consultas que se realizarán.
  :::
* ***Indice de texto***:

  ```bash
  db.products.createIndex({ name: "text" })
  ```
* ***Indice único***:

  ```bash
  db.users.createIndex({ email: 1 }, { unique: true })
  ```
* ***Indice parcial***:

  ```bash
  db.orders.createIndex({ status: 1 }, { partialFilterExpression: { status: { $eq: "pending" } } })
  ```
* ***Indice TTL***:

  ```bash
  db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })
  ```

### Índices recomendados para la API

Definir los índices adecuados en MongoDB es una práctica esencial para optimizar el rendimiento de las consultas y garantizar que la API funcione de manera eficiente, incluso con grandes volúmenes de datos. Para mejorar el rendimiento de los endpoints, es recomendable definir los siguientes índices en MongoDB:

* ***Indices para la colección de productos***
  * Indice en el campo `categoryId` para filtrar productos por categoría:

    ```bash
    db.products.createIndex({ categoryId: 1 })
    ```
  * Indice en el campo `name` para buscar productos por nombre:

    ```bash
    db.products.createIndex({ name: "text" })
    ```
* ***Indices para la colección de categorías***
  * Indice en el campo `parentId` para construir la jerarquía de categorías:

    ```bash
    db.categories.createIndex({ parentId: 1 })
    ```
* ***Indices para la colección de comentarios***
  * Indice en el campo `productId` para filtrar comentarios por producto:

    ```bash
    db.reviews.createIndex({ productId: 1 })
    ```
  * Indice en el campo `userId` para filtrar comentarios por usuario:

    ```bash
    db.reviews.createIndex({ userId: 1 })
    ```

## Pruebas de la API

Una vez que hemos desarrollado la API REST con FastAPI, podemos probar los endpoints utilizando Postman. A continuación se muestran algunos ejemplos de peticiones a la API REST:

* Crear una nueva categoría:
  * Método: `POST`
  * URL: `http://localhost:8000/api/categories`
  * Cuerpo: `{"name": "Electrónica", "description": "Categoría de productos electrónicos", "parentId": null}`
  * Respuesta esperada: `{
          "name": "Electrónica",
          "description": "Categoría de productos electrónicos",
          "parentId": null,
          "id": "67e1a445567da5eeb6d61a2d"
          }`

    ![postman-create-category](images/postman-create-category.png)
* Obtener todas las categorías:
  * Método: `GET`
  * URL: `http://localhost:8000/api/categories`
  * Respuesta esperada: Lista de categorías en formato JSON

    ![postman-get-categories](images/postman-get-categories.png)
* Crear un nuevo producto:
  * Método: `POST`
  * URL: `http://localhost:8000/api/products`
  * Cuerpo: `{"name": "Smartphone", "description": "Teléfono inteligente", "price": 500, "categoryId": "67e1a445567da5eeb6d61a2d"}`
  * Respuesta esperada: `{
          "name": "Smartphone",
          "description": "Teléfono inteligente",
          "price": 500.0,
          "categoryId": "67e1a445567da5eeb6d61a2d",
          "id": "67e1a595567da5eeb6d61a2f"
      }`
* Obtener todos los productos de una categoría:
  * Método: `GET`
  * URL: `http://localhost:8000/api/products?categoryId=67e1a445567da5eeb6d61a2d`
  * Respuesta esperada: Lista de productos en formato JSON

    ![postman-get-products](images/postman-get-products.png)
* Añadir un comentario a un producto:
  * Método: `POST`
  * URL: `http://localhost:8000/api/reviews`
  * Cuerpo: `{"productId": "67e1a595567da5eeb6d61a2f", "userId": "60c72b2f9b1d8b3a4c8b4569", "username": "usuario123", "rating": 4.5, "comment": "Muy buen producto, la batería dura bastante."}`
  * Respuesta esperada: `{
          "productId": "67e1a595567da5eeb6d61a2f",
          "userId": "60c72b2f9b1d8b3a4c8b4569",
          "username": "usuario123",
          "rating": 4.5,
          "comment": "Muy buen producto, la batería dura bastante.",
          "id": "67e1a672567da5eeb6d61a30",
          "createdAt": "2025-03-24T18:37:38Z",
          "updatedAt": null
      }`

    ![postman-add-review](images/postman-add-review.png)
* Obtener los comentarios de un producto:
  * Método: `GET`
  * URL: `http://localhost:8000/api/reviews?productId=67e1a595567da5eeb6d61a2f`
  * Respuesta esperada: Lista de comentarios en formato JSON
* Actualizar un comentario:
  * Método: `PUT`
  * URL: `http://localhost:8000/api/reviews/67e1a672567da5eeb6d61a30`
  * Cuerpo: `{
          "productId": "67e1a595567da5eeb6d61a2f",
          "userId": "60c72b2f9b1d8b3a4c8b4569",
          "username": "usuario123",
          "rating": 4.0,
          "comment": "Muy buen producto, la batería dura bastante."
      }`
  * Respuesta esperada: `{
          "productId": "67e1a595567da5eeb6d61a2f",
          "userId": "60c72b2f9b1d8b3a4c8b4569",
          "username": "usuario123",
          "rating": 4.0,
          "comment": "Muy buen producto, la batería dura bastante.",
          "id": "67e1a6fa567da5eeb6d61a31",
          "createdAt": "2025-03-24T18:39:54Z",
          "updatedAt": "2025-03-24T18:42:32Z"
      }`

    ![postman-update-review](images/postman-update-review.png)
* Eliminar un comentario:
  * Método: `DELETE`
  * URL: `http://localhost:8000/api/reviews/67e1a672567da5eeb6d61a30`
  * Respuesta esperada: `{
          "message": "Review deleted"
      }`

    ![postman-delete-review](images/postman-delete-review.png)
* Eliminar un producto:
  * Método: `DELETE`
  * URL: `http://localhost:8000/api/products/67e1a595567da5eeb6d61a2f`
  * Respuesta esperada: `{"message": "Product and associated reviews deleted"}`

    ![postman-delete-product](images/postman-delete-product.png)
* Eliminar una categoría:
  * Método: `DELETE`
  * URL: `http://localhost:8000/api/categories/67e1a445567da5eeb6d61a2d`
  * Respuesta esperada: `{"message": "Category deleted"}`

    ![postman-delete-category](images/postman-delete-category.png)

## Documentación de la API

FastAPI proporciona una interfaz de documentación automática para la API REST, que se genera automáticamente a partir de los modelos de datos y las rutas definidas en la aplicación. La documentación de la API incluye información detallada sobre los endpoints, los parámetros de entrada, los códigos de respuesta y los modelos de datos utilizados. Esta documentación es muy útil para los desarrolladores que consumen la API, ya que les permite comprender rápidamente cómo interactuar con los diferentes endpoints y qué datos esperar en las respuestas. Además, la documentación de la API se actualiza automáticamente a medida que se modifican las rutas y los modelos de datos en la aplicación.

FastAPI utiliza Swagger UI y ReDoc para generar la documentación de la API. Swagger UI proporciona una interfaz interactiva para explorar y probar los endpoints de la API, mientras que ReDoc genera una documentación más legible y visualmente atractiva. Ambas interfaces son accesibles a través de un navegador web y permiten a los desarrolladores interactuar con la API de forma sencilla y eficiente.

Para acceder a la documentación de la API, abre un navegador web y navega a la URL `http://localhost:8000/docs` para Swagger UI o `http://localhost:8000/redoc` para ReDoc. A continuación se muestra la documentación de la API generada automáticamente para Swagger UI.

![swagger-ui-finished](images/swagger-ui-finished.png)

A continuación se muestra la documentación de la API generada automáticamente para ReDoc.

![redoc-finished](images/redoc-finished.png)

## Conclusiones

En este tutorial, se ha desarrollado una API REST para la gestión de productos, categorías y comentarios de una tienda en línea utilizando Slim Framework y MongoDB. A lo largo del proceso, hemos configurado un entorno de desarrollo con Docker y Docker Compose, implementado los endpoints necesarios para la API REST y probado la API con Postman. 

MongoDB es una excelente opción para almacenar datos no estructurados o semiestructurados, como se ha observado en los comentarios de los usuarios. Además, su capacidad de escalar horizontalmente y su flexibilidad en el esquema de datos hacen que sea una base de datos ideal para aplicaciones modernas y escalables. La combinación de FastAPI y MongoDB permite desarrollar aplicaciones web rápidas y eficientes con una arquitectura RESTful. Además, las características de validación de datos de Pydantic y la generación automática de documentación de FastAPI facilitan el desarrollo y la documentación de la API.

Este proyecto no sólo proporciona una solución funcional para la gestión de productos, categorías y comentarios, sino que también sirve como punto de partida para desarrollar aplicaciones web más complejas y escalables. Se pueden añadir más funcionalidades a la API, como la autenticación de usuarios, la gestión de pedidos y la integración con pasarelas de pago.

## Licencia

Licencia CC BY-NC-ND 4.0

Copyright (c) 2025 [Manuel Torres - Departamento de Informática - Universidad de Almería]

Este proyecto está licenciado bajo la Licencia CC BY-NC-ND 4.0. Esto significa que puedes compartir el proyecto siempre que cites al autor, no lo uses para fines comerciales y no realices obras derivadas.
