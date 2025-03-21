# Desarrollo de una API REST para gestión de productos y valoraciones con Slim Framework y MongoDB

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

Este proyecto tiene como objetivo desarrollar una API REST eficiente y escalable para la gestión de productos y valoraciones en un entorno de comercio electrónico. Utilizando Slim Framework y MongoDB, se busca proporcionar una solución flexible y robusta que permita a los desarrolladores crear aplicaciones modernas y de alto rendimiento. MongoDB, con su capacidad para manejar datos no estructurados y su flexibilidad en el esquema, es especialmente adecuado para este tipo de aplicaciones, permitiendo una gestión eficiente de productos y valoraciones. La implementación de esta API no solo facilitará la gestión de productos y categorías, sino que también mejorará la experiencia del usuario al permitir la consulta y gestión de valoraciones y comentarios. Con un entorno de desarrollo configurado mediante Docker y Docker Compose, este proyecto ofrece una base sólida para futuras expansiones y mejoras.

* Desarrollar una API REST utilizando Slim Framework y MongoDB para gestionar productos, categorías y valoraciones.
* Configurar un entorno de desarrollo utilizando Docker y Docker Compose.
* Implementar endpoints para la creación, consulta, actualización y eliminación de productos, categorías y valoraciones.
* Proporcionar ejemplos prácticos de uso de la extensión MongoDB para PHP.
* Realizar pruebas de la API utilizando Postman.

:::{tip}

Disponible el [repositorio de GitHub](https://github.com/ualmtorres/SlimMongoDBAPIProductosValoraciones.git) con el código fuente de la API REST.
:::

## Introducción

En el contexto del comercio electrónico, la experiencia del usuario no solo depende de la disponibilidad de productos, sino también de la calidad de la información y las opiniones de otros compradores. Las valoraciones y comentarios juegan un papel crucial en la toma de decisiones de los clientes, ayudando a generar confianza y a mejorar los productos ofrecidos.

MongoDB, como base de datos NoSQL orientada a documentos, permite modelar eficientemente este tipo de información, almacenando productos y sus valoraciones de forma flexible y escalable. En este tutorial se desarrollará una API REST que gestione productos, categorías y valoraciones en un entorno de comercio electrónico.

## Descrición del problema

En este proyecto se va a desarrollar una API REST utilizando MongoDB para gestionar productos y valoraciones en un entorno de comercio electrónico utilizando Slim Framework, MongoDB y la extensión MongoDB para PHP. La API debe permitir:

* La gestión de productos, incluyendo su creación, actualización, eliminación y consulta.
* La organización de productos en categorías con una estructura flexible.
* La gestión de valoraciones y comentarios de los usuarios sobre los productos.
* La consulta eficiente de productos con sus valoraciones agregadas.

## Configuración del entorno de desarrollo

Para configurar el entorno de desarrollo de la API REST, utilizaremos Docker y Docker Compose. El archivo `docker-compose.yml` define los servicios necesarios para ejecutar la aplicación, incluyendo MongoDB, PHP con Slim y Nginx.

A continuación se muestra el contenido del archivo `docker-compose.yml`:

```yaml
version: "3.8"

services:
  mongo:
    container_name: mongo
    image: mongo:6.0.4
    restart: always
    ports:
      - 27017:27017
    volumes:
      - "./mongo-data/:/data/db"
    environment:
      MONGO_INITDB_ROOT_USERNAME: "mongouser"
      MONGO_INITDB_ROOT_PASSWORD: "mongopassword"
  mongo-express:
    container_name: mongo-express
    image: mongo-express:1.0.2
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: "mongouser"
      ME_CONFIG_MONGODB_ADMINPASSWORD: "mongopassword"
      ME_CONFIG_MONGODB_URL: "mongodb://mongouser:mongopassword@mongo:27017"
      ME_CONFIG_BASICAUTH_USERNAME: "user"
      ME_CONFIG_BASICAUTH_PASSWORD: "password"
  php:
    container_name: slim_php
    build:
      context: ./docker/php
    ports:
      - "9000:9000"
    volumes:
      - .:/var/www/slim_app

  nginx:
    container_name: slim_nginx
    image: nginx:stable-alpine
    ports:
      - "8084:80"
    volumes:
      - .:/var/www/slim_app
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - php
```

### Servicios

* ***mongo***: Servicio de bases de datos de documentos que se utiliza para almacenar los productos y las valoraciones. Se expone en el puerto 27017 y utiliza un volumen para almacenar los datos de la base de datos que mapea el directorio `mongo-data` al directorio `/data/db` en el contenedor.
* ***mongo-express***: Interfaz web para administrar la base de datos de MongoDB. Se expone en el puerto 8081 y se conecta al servicio de MongoDB utilizando las credenciales indicadas.
* ***PHP***: Servicio que ejecuta la aplicación Slim. Se construye a partir del contexto `./docker/php` y se expone en el puerto `9000`. Incluye un volumen que mapea el directorio actual al directorio de la aplicación Slim. El código fuente de la aplicación Slim se encuentra en el directorio `./public`.
* ***Nginx***: Servidor web que sirve la aplicación Slim. Se expone en el puerto `8080` y depende del servicio PHP.

**Configuración de la extensión MongoDB para PHP**

Para interactuar con la base de datos de MongoDB desde PHP, es necesario instalar la extensión `mongodb` en el contenedor PHP. A continuación se muestra el contenido del archivo `docker/php/Dockerfile`:

```docker
FROM php:8.1-fpm

RUN apt update \
    && apt install -y zlib1g-dev g++ git libicu-dev zip libzip-dev zip libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install intl opcache pdo pdo_pgsql \
    && pecl install apcu \
    && yes '' | pecl install mongodb \ ①
    && docker-php-ext-enable apcu \
    && docker-php-ext-configure zip \
    && docker-php-ext-install zip \
    && docker-php-ext-enable mongodb ②

WORKDIR /var/www/slim_app

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
```
1. Instala la extensión `mongodb` de PECL.
2. Habilita la extensión `mongodb` en PHP.

Además, es necesario incluir la dependencia de la extensión `mongodb` en el archivo `composer.json` de la aplicación Slim. Desde el contenedor PHP, ejecuta el siguiente comando para instalar la extensión `mongodb`:

```shell
composer require mongodb/mongodb
```

### Instrucciones de instalación

Para instalar y ejecutar la aplicación, sigue estos pasos:

1. Clona el repositorio:

   ```bash
   git clone https://github.com/ualmtorres/SlimMongoDBAPIProductosValoraciones.git
   cd SlimMongoDBAPIProductosValoraciones
   ```
2. Ejecuta Docker Compose para levantar los servicios:

   ```bash
   docker-compose up -d
   ```
3. Accede a la documentación de la API REST en `http://localhost:8080`.
4. Accede a los endpoints de la API REST en `http://localhost:8080/api`.

Con estos pasos, tendrás el entorno de desarrollo configurado y la API REST en funcionamiento.

## Uso básico de la extensión MongoDB para PHP

La extensión MongoDB para PHP proporciona una API orientada a objetos para interactuar con una base de datos de MongoDB. A continuación se muestra cómo conectarse a una base de datos de MongoDB, realizar operaciones básicas como insertar, consultar, actualizar y eliminar documentos, y manejar errores.

### Conexión a MongoDB

Para conectarse a una base de datos de MongoDB, primero necesitamos crear una instancia de la clase `MongoDB\Client` con la URL de conexión de MongoDB. Aquí hay un ejemplo de cómo conectarse a una base de datos local:

```php
<?php
require 'vendor/autoload.php'; // incluir la librería de MongoDB

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
?>
```

### Operaciones básicas

#### Insertar documentos

Para insertar documentos en una colección, utilizamos el método `insertOne` o `insertMany`. Aquí hay un ejemplo de cómo insertar un solo documento:

```php
<?php
require 'vendor/autoload.php'; // incluir la librería de MongoDB

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$result = $collection->insertOne([
    'name' => 'Alice',
    'email' => 'alice@example.com',
    'age' => 25
]);

echo "Inserted with Object ID '{$result->getInsertedId()}'";
?>
```

Para insertar múltiples documentos, utilizamos el método `insertMany`:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$result = $collection->insertMany([
    [
        'name' => 'Bob',
        'email' => 'bob@example.com',
        'age' => 30
    ],
    [
        'name' => 'Charlie',
        'email' => 'charlie@example.com',
        'age' => 35
    ]
]);

echo "Inserted {$result->getInsertedCount()} documents";
?>
```

#### Consultar documentos

Para consultar documentos en una colección, utilizamos el método `find` o `findOne`. Aquí hay un ejemplo de cómo consultar todos los documentos en una colección:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$cursor = $collection->find();

foreach ($cursor as $document) {
    var_dump($document);
}
?>
```

Para consultar un solo documento, utilizamos el método `findOne`:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$document = $collection->findOne(['name' => 'Alice']);
var_dump($document);
?>
```

#### Actualizar documentos

Para actualizar documentos en una colección, utilizamos el método `updateOne` o `updateMany`. Aquí hay un ejemplo de cómo actualizar un solo documento:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$result = $collection->updateOne(
    ['name' => 'Alice'],
    ['$set' => ['age' => 26]]
);

echo "Matched {$result->getMatchedCount()} document(s)";
echo "Modified {$result->getModifiedCount()} document(s)";
?>
```

Para actualizar múltiples documentos, utilizamos el método `updateMany`:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$result = $collection->updateMany(
    ['age' => ['$gte' => 30]],
    ['$set' => ['status' => 'senior']]
);

echo "Matched {$result->getMatchedCount()} document(s)";
echo "Modified {$result->getModifiedCount()} document(s)";
?>
```

#### Eliminar documentos

Para eliminar documentos en una colección, utilizamos el método `deleteOne` o `deleteMany`. Aquí hay un ejemplo de cómo eliminar un solo documento:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$result = $collection->deleteOne(['name' => 'Alice']);

echo "Deleted {$result->getDeletedCount()} document(s)";
?>
```

Para eliminar múltiples documentos, utilizamos el método `deleteMany`:

```php
<?php
require 'vendor/autoload.php';

$client = new MongoDB\Client('mongodb://<user>:<password>@mongo:27017');
$collection = $client->test->users;

$result = $collection->deleteMany(['age' => ['$gte' => 30]]);

echo "Deleted {$result->getDeletedCount()} document(s)";
?>
```

### Manejo de errores

Es importante manejar los errores al trabajar con Redis. Puedes utilizar bloques `try-catch` para capturar excepciones:

```php
<?php
...
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid category ID'
        ]);
    }
...
?>
```

## Desarrollo de la API

En esta sección vamos a crear la API REST para gestionar productos, categorías y valoraciones utilizando Slim Framework y MongoDB. Seguiremos un enfoque incremental, comenzando con la configuración general y luego desarrollando cada endpoint paso a paso. Antes, vamos a describir los endpoints disponibles en la API REST.

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

### Crear la aplicación Slim

Crea un archivo `public/index.php` y añade el siguiente código para configurar la aplicación Slim y conectar a Redis:

```php
<?php

require dirname(__DIR__) . '/vendor/autoload.php';

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;
use Slim\Factory\AppFactory;

$app = AppFactory::create();

$mongo = new MongoDB\Client('mongodb://youruser:yourpassword@mongo:27017/admin');
$database = $mongo->selectDatabase('ecommerce');
$products = $database->selectCollection('products');
$categories = $database->selectCollection('categories');
$reviews = $database->selectCollection('reviews');

$prefix = '/api';

$app->addBodyParsingMiddleware();

function createJsonResponse(ResponseInterface $response, array $data): ResponseInterface
{
    // Establecer el tipo de contenido de la respuesta
    $response = $response->withHeader('Content-Type', 'application/json; charset=utf-8');

    // Escribir la respuesta
    $response->getBody()->write(json_encode($data));

    // Devolver la respuesta
    return $response;
}

$app->get("/", function (RequestInterface $request, ResponseInterface $response, array $args) {
    echo file_get_contents('./index.html'); <1>

    return $response;
});

/*
 **************************************************************************************
 * Aquí se implementarán los endpoints de la API REST
 **************************************************************************************
 */

$app->run();
```
<1> Devuelve el contenido del archivo `index.html` en la ruta raíz. Este archivo se utiliza para mostrar la documentación de la API REST.

:::{note}

El valor de `mongo` en la función `connect` se refiere al nombre del servicio MongoDB definido en el archivo `docker-compose.yml`. Este es el mismo nombre que se utiliza en el archivo `docker-compose.yml`.
:::

La figura siguiente muestra algunos endpoints de la API organizados en categorías, productos y comentarios:

![slim-api-redis-api-structure](images/slim-api-redis-api-structure.png)

### Endpoint de prueba

Añade el siguiente código para definir un endpoint de prueba `GET /api/test` que devuelve un mensaje de prueba:

```php
$app->get($prefix . '/test', function (RequestInterface $request, ResponseInterface $response) {
    $data = [
        'status' => 200,
        'message' => 'API is working'
    ];

    return createJsonResponse($response, $data);
});
```

### Endpoints de categorías

En esta sección vamos a implementar los endpoints para gestionar categorías en la API REST. Los endpoints incluyen la creación, consulta, actualización y eliminación de categorías.

En el archivo `public/routes/categories.php`, añade el siguiente código antes de la definición de las rutas de categorías:

```php
<?php

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;
use MongoDB\BSON\ObjectId;

// Los endpoints de categorías se definen aquí
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

```php
// Endpoint para crear una nueva categoría
$app->post($prefix . '/categories', function (RequestInterface $request, ResponseInterface $response) use ($categories) {
    $data = $request->getParsedBody();

    // Validar los datos de entrada
    if (!array_key_exists('name', $data) || !array_key_exists('description', $data) || !array_key_exists('parentId', $data)) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Insertar la nueva categoría en la base de datos
    $result = $categories->insertOne([
        'name' => $data['name'],
        'description' => $data['description'],
        'parentId' => $data['parentId']
    ]);

    // Devolver la respuesta con el ID de la nueva categoría
    return createJsonResponse($response->withStatus(201), [
        'status' => 201,
        'message' => 'Category created',
        'categoryId' => (string) $result->getInsertedId()
    ]);
});
```

#### Endpoint para obtener todas las categorías incluyendo la jerarquía

Añade el siguiente código para definir un endpoint `GET /api/categories/tree` que permite obtener la jerarquía completa de categorías. Este endpoint consulta todas las categorías de la colección de categorías de MongoDB y construye la jerarquía de categorías en forma de árbol. Para ello, usa una función auxiliar recursiva que recorre las categorías y sus subcategorías. El endpoint devuelve la jerarquía de categorías en formato JSON. La operación MongoDB que se usaría para consultar todas las categorías en la colección de categorías sería similar a la siguiente:

```
db.categories.find();
```

```php
// Función para construir la jerarquía de categorías
function buildCategoryTree($categories, $parentId = null)
{
    $branch = [];
    foreach ($categories as $category) {
        if ($category['parentId'] == $parentId) {
            $children = buildCategoryTree($categories, (string) $category['_id']);
            if ($children) {
                $category['children'] = $children;
            }
            $branch[] = $category;
        }
    }
    return $branch;
}

// Endpoint para obtener la jerarquía completa de categorías
$app->get($prefix . '/categories/tree', function (RequestInterface $request, ResponseInterface $response) use ($categories) {
    $result = $categories->find()->toArray();

    // Convertir ObjectId a string
    $categoriesArray = array_map(function ($category) {
        $category['_id'] = (string) $category['_id'];
        $category['parentId'] = (string) $category['parentId'];
        return $category;
    }, $result);

    $categoryTree = buildCategoryTree($categoriesArray);

    return createJsonResponse($response, [
        'status' => 200,
        'data' => $categoryTree
    ]);
});
```

**Conflictos en la definición de rutas o _route shadowing_**

Este endpoint `GET /api/categories/tree` se debe colocar antes que el endpoint `GET /api/categories` para que no haya conflictos en la definición de rutas. Estos conflictos pueden ocurrir si las rutas tienen el mismo prefijo y solo difieren en un parámetro opcional. En ese caso, colocaremos primero la ruta más específica y luego la más general, ya que Slim Framework evalúa las rutas en el orden en que se definen y selecciona la primera coincidencia encontrada.
Así evitaremos lo que se conoce como "route shadowing", que consiste en que una ruta más específica sea ignorada en favor de una ruta más general porque se definió primero.

#### Endpoint para obtener todas las categorías

Añade el siguiente código para definir un endpoint `GET /api/categories` que permite obtener todas las categorías. Este endpoint consulta todas las categorías de la colección de categorías de MongoDB y las devuelve en formato JSON. La operación MongoDB que se usaría para consultar todas las categorías en la colección de categorías sería similar a la siguiente:

```
db.categories.find();
```

```php
// Endpoint para obtener todas las categorías
$app->get($prefix . '/categories', function (RequestInterface $request, ResponseInterface $response) use ($categories) {
    $result = $categories->find()->toArray();

    // Convertir ObjectId a string
    $categoriesArray = array_map(function ($category) {
        $category['_id'] = (string) $category['_id'];
        return $category;
    }, $result);

    return createJsonResponse($response, [
        'status' => 200,
        'data' => $categoriesArray
    ]);
});
```

#### Endpoint para obtener una categoría por ID

Añade el siguiente código para definir un endpoint `GET /api/categories/{id}` que permite obtener una categoría por su ID. Este endpoint recibe el ID de la categoría como parámetro en la URL y busca la categoría correspondiente en la colección de categorías de MongoDB. Si la categoría existe, se devuelve su información en formato JSON. Si la categoría no existe, se devuelve un mensaje de error. La operación MongoDB que se usaría para buscar una categoría en la colección de categorías sería similar a la siguiente:

```
db.categories.findOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para obtener una categoría por ID
$app->get($prefix . '/categories/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($categories) {
    $id = $args['id'];

    // Validar el ID
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid category ID'
        ]);
    }

    // Buscar la categoría en la base de datos
    $category = $categories->findOne(['_id' => new ObjectId($id)]);

    if (!$category) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Category not found'
        ]);
    }

    // Convertir ObjectId a string
    $category['_id'] = (string) $category['_id'];

    return createJsonResponse($response, [
        'status' => 200,
        'data' => $category
    ]);
});
```

#### Endpoint para actualizar una categoría

Añade el siguiente código para definir un endpoint `PUT /api/categories/{id}` que permite actualizar una categoría por su ID. Este endpoint recibe el ID de la categoría como parámetro en la URL y los nuevos datos de la categoría en formato JSON en el cuerpo de la solicitud. Los datos de la categoría a actualizar deben incluir los campos `name`, `description` y `parentId`. El endpoint actualiza la categoría correspondiente en la colección de categorías de MongoDB. Si la categoría se actualiza correctamente, se devuelve un mensaje de éxito. Si la categoría no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para actualizar una categoría en la colección de categorías sería similar a la siguiente:

```
db.categories.updateOne(
    { _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") },
    { $set: { name: "Nueva categoría", description: "Nueva descripción", parentId: ObjectId("60c72b2f9b1d8b3a4c8b4567") } }
);
```

```php
// Endpoint para actualizar una categoría por ID
$app->put($prefix . '/categories/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($categories) {
    $id = $args['id'];
    $data = $request->getParsedBody();

    // Validar el ID
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid category ID'
        ]);
    }

    // Validar los datos de entrada
    if (!array_key_exists('name', $data) || !array_key_exists('description', $data) || !array_key_exists('parentId', $data)) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Actualizar la categoría en la base de datos
    $result = $categories->updateOne(
        ['_id' => new ObjectId($id)],
        ['$set' => [
            'name' => $data['name'],
            'description' => $data['description'],
            'parentId' => $data['parentId']
        ]]
    );

    if ($result->getMatchedCount() === 0) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Category not found'
        ]);
    }

    return createJsonResponse($response, [
        'status' => 200,
        'message' => 'Category updated'
    ]);
});
```

#### Endpoint para eliminar una categoría

Añade el siguiente código para definir un endpoint `DELETE /api/categories/{id}` que permite eliminar una categoría por su ID. Este endpoint recibe el ID de la categoría como parámetro en la URL y elimina la categoría correspondiente de la colección de categorías de MongoDB. Si la categoría se elimina correctamente, se devuelve un mensaje de éxito. Si la categoría no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para eliminar una categoría en la colección de categorías sería similar a la siguiente:

```
db.categories.deleteOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para eliminar una categoría por ID
$app->delete($prefix . '/categories/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($categories) {
    $id = $args['id'];

    // Validar el ID
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid category ID'
        ]);
    }

    // Eliminar la categoría de la base de datos
    $result = $categories->deleteOne(['_id' => new ObjectId($id)]);

    if ($result->getDeletedCount() === 0) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Category not found'
        ]);
    }

    return createJsonResponse($response, [
        'status' => 200,
        'message' => 'Category deleted'
    ]);
});
```

### Endpoints de productos

En esta sección, vamos a implementar los endpoints para gestionar productos en la API REST. Los endpoints incluyen la creación, consulta, actualización y eliminación de productos.

En el archivo `public/routes/products.php`, añade el siguiente código antes de la definición de las rutas de productos:

```php
<?php

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;
use MongoDB\BSON\ObjectId;

// Los endpoints de productos se definen aquí
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

```php
// Endpoint para crear un nuevo producto
$app->post($prefix . '/products', function (RequestInterface $request, ResponseInterface $response) use ($products) {
    $data = $request->getParsedBody();

    // Validar los datos de entrada
    if (!isset($data['name'], $data['description'], $data['price'], $data['categoryId'])) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Insertar el nuevo producto en la base de datos
    $result = $products->insertOne([
        'name' => $data['name'],
        'description' => $data['description'],
        'price' => $data['price'],
        'categoryId' => $data['categoryId']
    ]);

    // Devolver la respuesta con el ID del nuevo producto
    return createJsonResponse($response->withStatus(201), [
        'status' => 201,
        'message' => 'Product created',
        'productId' => (string) $result->getInsertedId()
    ]);
});
```

#### Endpoint para obtener detalles de un producto

Añade el siguiente código para definir un endpoint `GET /api/products/{id}` que permite obtener los detalles de un producto por su ID. Este endpoint recibe el ID del producto como parámetro en la URL y busca el producto correspondiente en la colección de productos de MongoDB. Si el producto existe, se devuelve su información en formato JSON. Si el producto no existe, se devuelve un mensaje de error. La operación MongoDB que se usaría para buscar un documento en la colección de productos sería similar a la siguiente:

```
db.products.findOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para obtener un producto por ID
$app->get($prefix . '/products/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($products) {
    $id = $args['id'];

    // Validar el ID
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid product ID'
        ]);
    }

    // Buscar el producto en la base de datos
    $product = $products->findOne(['_id' => new ObjectId($id)]);

    if (!$product) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Product not found'
        ]);
    }

    // Convertir ObjectId a string
    $product['_id'] = (string) $product['_id'];

    return createJsonResponse($response, [
        'status' => 200,
        'data' => $product
    ]);
});
```

#### Endpoint para consultar productos

Añade el siguiente código para definir un endpoint `GET /api/products` que permite consultar productos. Este endpoint admite parámetros de consulta opcionales para filtrar los productos por categoría (`categoryId`) o por nombre (`name`). Si se proporciona el parámetro `categoryId`, se filtran los productos por la categoría especificada. Si se proporciona el parámetro `name`, se filtran los productos por el nombre que coincida con la cadena especificada. Se pueden combinar ambos parámetros para realizar búsquedas más específicas. Esta operación devuelve una lista de productos en formato JSON. La operación MongoDB que se usaría para consultar productos en la colección de productos sería similar a la siguiente:

```
db.products.find({ categoryId: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para obtener todos los productos o los productos de una categoría o por nombre
$app->get($prefix . '/products', function (RequestInterface $request, ResponseInterface $response) use ($products) {
    $queryParams = $request->getQueryParams();
    $filter = [];

    if (isset($queryParams['categoryId'])) {
        // Validar el ID de la categoría
        try {
            new ObjectId($queryParams['categoryId']);
            $filter['categoryId'] = $queryParams['categoryId'];
        } catch (Exception $e) {
            return createJsonResponse($response->withStatus(400), [
                'status' => 400,
                'message' => 'Invalid category ID'
            ]);
        }
    }

    if (isset($queryParams['name'])) {
        $filter['name'] = new \MongoDB\BSON\Regex($queryParams['name'], 'i');
    }

    $result = $products->find($filter)->toArray();

    // Convertir ObjectId a string
    $productsArray = array_map(function ($product) {
        $product['_id'] = (string) $product['_id'];
        return $product;
    }, $result);

    return createJsonResponse($response, [
        'status' => 200,
        'data' => $productsArray
    ]);
});
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

```php
// Endpoint para actualizar un producto por ID
$app->put($prefix . '/products/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($products) {
    $id = $args['id'];
    $data = $request->getParsedBody();

    // Validar el ID
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid product ID'
        ]);
    }

    // Validar los datos de entrada
    if (!isset($data['name'], $data['description'], $data['price'], $data['categoryId'])) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Actualizar el producto en la base de datos
    $result = $products->updateOne(
        ['_id' => new ObjectId($id)],
        ['$set' => [
            'name' => $data['name'],
            'description' => $data['description'],
            'price' => $data['price'],
            'categoryId' => $data['categoryId']
        ]]
    );

    if ($result->getMatchedCount() === 0) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Product not found'
        ]);
    }

    return createJsonResponse($response, [
        'status' => 200,
        'message' => 'Product updated'
    ]);
});
```

#### Endpoint para eliminar un producto

Añade el siguiente código para definir un endpoint `DELETE /api/products/{id}` que permite eliminar un producto por su ID. Este endpoint recibe el ID del producto como parámetro en la URL y elimina el producto correspondiente de la colección de productos de MongoDB. También elimina los comentarios asociados al producto. Si el producto se elimina correctamente, se devuelve un mensaje de éxito. Si el producto no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para eliminar un documento en la colección de productos sería similar a la siguiente:

```
db.products.deleteOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para eliminar un producto por ID
$app->delete($prefix . '/products/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($products, $reviews) {
    $id = $args['id'];

    // Validar el ID
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid product ID'
        ]);
    }

    // Eliminar el producto de la base de datos
    $result = $products->deleteOne(['_id' => new ObjectId($id)]);

    if ($result->getDeletedCount() === 0) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Product not found'
        ]);
    }

    // Eliminar los comentarios asociados al producto
    $reviews->deleteMany(['productId' => new ObjectId($id)]);

    return createJsonResponse($response, [
        'status' => 200,
        'message' => 'Product and associated reviews deleted'
    ]);
});
```

### Endpoints de comentarios

En esta sección, vamos a implementar los endpoints para gestionar comentarios en la API REST. Los endpoints incluyen la creación, consulta, actualización y eliminación de comentarios.

En el archivo `public/routes/reviews.php`, añade el siguiente código antes de la definición de las rutas de comentarios:

```php
<?php

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;
use MongoDB\BSON\ObjectId;
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

```php
// Endpoint para añadir un comentario a un producto
$app->post($prefix . '/reviews', function (RequestInterface $request, ResponseInterface $response) use ($reviews) {
    $data = $request->getParsedBody();

    // Validar los datos de entrada
    if (!isset($data['productId'], $data['userId'], $data['username'], $data['rating'], $data['comment'])) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Validar el ID del producto
    try {
        new ObjectId($data['productId']);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid product ID'
        ]);
    }

    // Añadir el comentario a la colección de reviews
    $result = $reviews->insertOne([
        'productId' => new ObjectId($data['productId']),
        'userId' => new ObjectId($data['userId']),
        'username' => $data['username'],
        'rating' => $data['rating'],
        'comment' => $data['comment'],
        'createdAt' => new \MongoDB\BSON\UTCDateTime()
    ]);

    return createJsonResponse($response->withStatus(201), [
        'status' => 201,
        'message' => 'Comment added',
        'commentId' => (string) $result->getInsertedId()
    ]);
});
```

#### Endpoint para obtener los comentarios de un producto o por usuario

Añade el siguiente código para definir un endpoint `GET /api/reviews` que permite obtener los comentarios de un producto o por usuario. Este endpoint admite parámetros de consulta opcionales para filtrar los comentarios por producto (`productId`) o por usuario (`userId`). Si se proporciona el parámetro `productId`, se filtran los comentarios por el producto especificado. Si se proporciona el parámetro `userId`, se filtran los comentarios por el usuario especificado. Se pueden combinar ambos parámetros para realizar búsquedas más específicas. Esta operación devuelve una lista de comentarios en formato JSON. La operación MongoDB que se usaría para consultar comentarios en la colección de comentarios sería similar a la siguiente:

```
db.reviews.find({ productId: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para obtener los comentarios de un producto o por usuario
$app->get($prefix . '/reviews', function (RequestInterface $request, ResponseInterface $response) use ($reviews) {
    $queryParams = $request->getQueryParams();
    $filter = [];

    if (isset($queryParams['productId'])) {
        // Validar el ID del producto
        try {
            new ObjectId($queryParams['productId']);
            $filter['productId'] = new ObjectId($queryParams['productId']);
        } catch (Exception $e) {
            return createJsonResponse($response->withStatus(400), [
                'status' => 400,
                'message' => 'Invalid product ID'
            ]);
        }
    }

    if (isset($queryParams['userId'])) {
        // Validar el ID del usuario
        try {
            new ObjectId($queryParams['userId']);
            $filter['userId'] = new ObjectId($queryParams['userId']);
        } catch (Exception $e) {
            return createJsonResponse($response->withStatus(400), [
                'status' => 400,
                'message' => 'Invalid user ID'
            ]);
        }
    }

    // Buscar los comentarios en la colección de reviews
    $reviews = $reviews->find($filter)->toArray();

    // Convertir ObjectId a string y formatear fechas en los comentarios
    $reviewsArray = array_map(function ($comment) {
        $comment['_id'] = (string) $comment['_id'];
        $comment['productId'] = (string) $comment['productId'];
        $comment['userId'] = (string) $comment['userId'];
        $comment['createdAt'] = $comment['createdAt']->toDateTime()->format('Y-m-d\TH:i:s\Z');
        if (isset($comment['updatedAt'])) {
            $comment['updatedAt'] = $comment['updatedAt']->toDateTime()->format('Y-m-d\TH:i:s\Z');
        }
        return $comment;
    }, $reviews);

    return createJsonResponse($response, [
        'status' => 200,
        'data' => $reviewsArray
    ]);
});
```

#### Endpoint para actualizar un comentario

Añade el siguiente código para definir un endpoint `PUT /api/reviews/{id}` que permite actualizar un comentario por su ID. Este endpoint recibe el ID del comentario como parámetro en la URL y los nuevos datos del comentario en formato JSON en el cuerpo de la solicitud. Los datos del comentario a actualizar deben incluir los campos `userId`, `rating` y `comment`. El endpoint actualiza el comentario correspondiente en la colección de comentarios de MongoDB. Como medida de seguridad se verifica que el usuario que intenta actualizar el comentario sea el mismo que lo creó. Si el comentario se actualiza correctamente, se devuelve un mensaje de éxito. Si el comentario no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para actualizar un documento en la colección de comentarios sería similar a la siguiente:

```
db.reviews.updateOne(
    { _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") },
    { $set: {
        userId: ObjectId("60c72b2f9b1d8b3a4c8b4568"),
        rating: 4.0,
        comment: "Muy buen producto, la batería dura bastante.",
        updatedAt: new Date()
    }}
);
```

```php
$app->put($prefix . '/reviews/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($reviews) {
    $id = $args['id'];
    $data = $request->getParsedBody();

    // Validar el ID del comentario
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid comment ID'
        ]);
    }

    // Validar que el ID del usuario sea un ObjectId
    try {
        new ObjectId($data['userId']);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid user ID'
        ]);
    }

    // Validar los datos de entrada
    if (!isset($data['userId'], $data['rating'], $data['comment'])) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Verificar que el comentario pertenece al usuario
    $comment = $reviews->findOne(['_id' => new ObjectId($id), 'userId' => new ObjectId($data['userId'])]);
    if (!$comment) {
        return createJsonResponse($response->withStatus(403), [
            'status' => 403,
            'message' => 'Forbidden: You can only update your own reviews'
        ]);
    }

    // Actualizar el comentario en la base de datos
    $result = $reviews->updateOne(
        ['_id' => new ObjectId($id)],
        ['$set' => [
            'rating' => $data['rating'],
            'comment' => $data['comment'],
            'updatedAt' => new \MongoDB\BSON\UTCDateTime()
        ]]
    );

    if ($result->getMatchedCount() === 0) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Comment not found'
        ]);
    }

    return createJsonResponse($response, [
        'status' => 200,
        'message' => 'Comment updated'
    ]);
});
```

#### Endpoint para eliminar un comentario

Añade el siguiente código para definir un endpoint `DELETE /api/reviews/{id}` que permite eliminar un comentario por su ID. Este endpoint recibe el ID del comentario como parámetro en la URL y elimina el comentario correspondiente de la colección de comentarios de MongoDB. Como medida de seguridad se verifica que el usuario que intenta eliminar el comentario sea el mismo que lo creó. Si el comentario se elimina correctamente, se devuelve un mensaje de éxito. Si el comentario no se encuentra, se devuelve un mensaje de error. La operación MongoDB que se usaría para eliminar un documento en la colección de comentarios sería similar a la siguiente:

```
db.reviews.deleteOne({ _id: ObjectId("60c72b2f9b1d8b3a4c8b4567") });
```

```php
// Endpoint para eliminar un comentario
$app->delete($prefix . '/reviews/{id}', function (RequestInterface $request, ResponseInterface $response, array $args) use ($reviews) {
    $id = $args['id'];
    $data = $request->getParsedBody();

    // Validar el ID del comentario
    try {
        new ObjectId($id);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid comment ID'
        ]);
    }

    // Validar el ID del usuario
    if (!isset($data['userId'])) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid input'
        ]);
    }

    // Validar que el ID del usuario sea válido
    try {
        new ObjectId($data['userId']);
    } catch (Exception $e) {
        return createJsonResponse($response->withStatus(400), [
            'status' => 400,
            'message' => 'Invalid user ID'
        ]);
    }

    // Verificar que el comentario pertenece al usuario
    $comment = $reviews->findOne(['_id' => new ObjectId($id), 'userId' => new ObjectId($data['userId'])]);
    if (!$comment) {
        return createJsonResponse($response->withStatus(403), [
            'status' => 403,
            'message' => 'Forbidden: You can only delete your own reviews'
        ]);
    }

    // Eliminar el comentario de la base de datos
    $result = $reviews->deleteOne(['_id' => new ObjectId($id)]);

    if ($result->getDeletedCount() === 0) {
        return createJsonResponse($response->withStatus(404), [
            'status' => 404,
            'message' => 'Comment not found'
        ]);
    }

    return createJsonResponse($response, [
        'status' => 200,
        'message' => 'Comment deleted'
    ]);
});
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

Una vez que hemos desarrollado la API REST con Slim Framework, podemos probar los endpoints utilizando Postman. A continuación se muestran algunos ejemplos de peticiones a la API REST:

* Crear una nueva categoría:
  * Método: `POST`
  * URL: `http://localhost:8080/api/categories`
  * Cuerpo: `{"name": "Electrónica", "description": "Categoría de productos electrónicos", "parentId": null}`
  * Respuesta esperada: `{"status": 201, "message": "Category created", "categoryId": "67c200a4953f04997706efbc"}`

    ![postman-create-category](images/postman-create-category.png)
* Obtener todas las categorías:
  * Método: `GET`
  * URL: `http://localhost:8080/api/categories`
  * Respuesta esperada: Lista de categorías en formato JSON

    ![postman-get-categories](images/postman-get-categories.png)
* Crear un nuevo producto:
  * Método: `POST`
  * URL: `http://localhost:8080/api/products`
  * Cuerpo: `{"name": "Smartphone", "description": "Teléfono inteligente", "price": 500, "categoryId": "67c200a4953f04997706efbc"}`
  * Respuesta esperada: `{"status": 201, "message": "Product created", "productId": "67c2015e953f04997706efbd"}`
* Obtener todos los productos de una categoría:
  * Método: `GET`
  * URL: `http://localhost:8080/api/products?categoryId=67c200a4953f04997706efbc`
  * Respuesta esperada: Lista de productos en formato JSON

    ![postman-get-products](images/postman-get-products.png)
* Añadir un comentario a un producto:
  * Método: `POST`
  * URL: `http://localhost:8080/api/reviews`
  * Cuerpo: `{"productId": "67c2015e953f04997706efbd", "userId": "60c72b2f9b1d8b3a4c8b4569", "username": "usuario123", "rating": 4.5, "comment": "Muy buen producto, la batería dura bastante."}`
  * Respuesta esperada: `{"status": 201, "message": "Comment added", "commentId": "67c2023d953f04997706efbe"}`

    ![postman-add-review](images/postman-add-review.png)
* Obtener los comentarios de un producto:
  * Método: `GET`
  * URL: `http://localhost:8080/api/reviews?productId=60c72b2f9b1d8b3a4c8b4568`
  * Respuesta esperada: Lista de comentarios en formato JSON

    ![postman-get-reviews](images/postman-get-reviews.png)
* Actualizar un comentario:
  * Método: `PUT`
  * URL: `http://localhost:8080/api/reviews/67c2023d953f04997706efbe`
  * Cuerpo: `{"userId": "60c72b2f9b1d8b3a4c8b4569", "rating": 4.0, "comment": "Muy buen producto, la batería dura bastante."}`
  * Respuesta esperada: `{"status": 200, "message": "Comment updated"}`

    ![postman-update-review](images/postman-update-review.png)
* Eliminar un comentario:
  * Método: `DELETE`
  * URL: `http://localhost:8080/api/reviews/67c2023d953f04997706efbe`
  * Cuerpo: `{"userId": "60c72b2f9b1d8b3a4c8b4569"}`
  * Respuesta esperada: `{"status": 200, "message": "Comment deleted"}`

    ![postman-delete-review](images/postman-delete-review.png)
* Eliminar un producto:
  * Método: `DELETE`
  * URL: `http://localhost:8080/api/products/67c2015e953f04997706efbd`
  * Respuesta esperada: `{"status": 200, "message": "Product and associated reviews deleted"}`

    ![postman-delete-product](images/postman-delete-product.png)
* Eliminar una categoría:
  * Método: `DELETE`
  * URL: `http://localhost:8080/api/categories/67c200a4953f04997706efbc`
  * Respuesta esperada: `{"status": 200, "message": "Category deleted"}`

    ![postman-delete-category](images/postman-delete-category.png)

## Conclusiones

En este tutorial, se ha desarrollado una API REST para la gestión de productos, categorías y comentarios de una tienda en línea utilizando Slim Framework y MongoDB. A lo largo del proceso, hemos configurado un entorno de desarrollo con Docker y Docker Compose, implementado los endpoints necesarios para la API REST y probado la API con Postman. 

MongoDB es una excelente opción para almacenar datos no estructurados o semiestructurados, como se ha observado en los comentarios de los usuarios. Además, su capacidad de escalar horizontalmente y su flexibilidad en el esquema de datos hacen que sea una base de datos ideal para aplicaciones modernas y escalables. La combinación de Slim Framework y MongoDB permite desarrollar aplicaciones web rápidas y eficientes con una arquitectura RESTful.

Este proyecto no sólo proporciona una solución funcional para la gestión de productos, categorías y comentarios, sino que también sirve como punto de partida para desarrollar aplicaciones web más complejas y escalables. Se pueden añadir más funcionalidades a la API, como la autenticación de usuarios, la gestión de pedidos y la integración con pasarelas de pago.

## Licencia

Licencia CC BY-NC-ND 4.0

Copyright (c) 2025 [Manuel Torres - Departamento de Informática - Universidad de Almería]

Este proyecto está licenciado bajo la Licencia CC BY-NC-ND 4.0. Esto significa que puedes compartir el proyecto siempre que cites al autor, no lo uses para fines comerciales y no realices obras derivadas.
