# Bases de datos clave-valor

Manuel Torres

Máster en Ingeniería Informática. Universidad de Almería

---

Redis es una de las bases de datos NoSQL más utilizadas en aplicaciones que requieren alta velocidad y eficiencia en el acceso a los datos. Su naturaleza en memoria y su versatilidad lo convierten en una solución ideal para múltiples casos de uso, como almacenamiento en caché, sistemas de colas, gestión de sesiones y análisis en tiempo real. En este tutorial se introducen los fundamentos de Redis, desde su funcionamiento como base de datos clave-valor hasta su soporte para estructuras de datos avanzadas como listas, conjuntos y hashes. También se tratan otras características interesantes como el modelo de publicación-suscripción, los espacios de nombres para organizar los datos y las transacciones para garantizar operaciones atómicas.

A lo largo del recorrido, hemos visto cómo estas funcionalidades permiten construir sistemas eficientes y escalables, aprovechando la rapidez y flexibilidad de Redis en escenarios como comercio electrónico, sistemas de mensajería y redes sociales, entre otros. Con este conocimiento, se propone la incorporación de Redis en aplicaciones para mejorar el rendimiento y la disponibilidad de los datos en entornos exigentes.

* Conocer los fundamentos de las bases de datos clave-valor y su aplicación en sistemas distribuidos.
* Aprender a utilizar Redis como base de datos clave-valor en aplicaciones web y móviles.
* Explorar las características avanzadas de Redis, como listas, conjuntos, hashes y conjuntos ordenados.
* Comprender el modelo de publicación-suscripción de Redis y su uso en sistemas de mensajería y notificaciones en tiempo real.
* Conocer las mejores prácticas para el diseño y la implementación de aplicaciones con Redis, incluyendo la gestión de sesiones, la caché de datos y la gestión de colas.

:::{tip}

Enlaces de interés:

* [Tutorial sobre uso de Redis en una API REST en Express](https://github.com/ualmtorres/bdge-express-test)
* [Archivo `docker-compose.yml` para instalación de bases de datos NoSQL (Redis, MongoDB, Neo4j)](https://github.com/ualmtorres/bdge-entorno-desarrollo-docker/blob/master/docker-compose.yml)
:::

## Introducción a las bases de datos clave-valor

Las bases de datos clave-valor son un tipo de almacenamiento de datos NoSQL que utilizan una estructura simple de pares clave-valor. Cada clave es única y se asocia con un valor, que puede ser cualquier tipo de dato, desde una cadena de texto hasta un objeto complejo.

Una de las principales características de las bases de datos clave-valor es su capacidad para ofrecer un rendimiento excepcionalmente alto. Esto se debe a que todas las operaciones de acceso a los datos se realizan mediante la clave primaria, lo que permite búsquedas rápidas y eficientes. Además, su simplicidad las hace fácilmente escalables, lo que es ideal para aplicaciones que requieren manejar grandes volúmenes de datos. Una de las razones por las que las bases de datos clave-valor pueden escalar horizontalmente es su simplicidad en la estructura de datos. Al utilizar pares clave-valor, es fácil distribuir los datos entre múltiples nodos en un clúster. Cada nodo puede manejar una parte del espacio de claves, lo que permite repartir la carga de trabajo y aumentar la capacidad de almacenamiento y procesamiento.

Además, muchas bases de datos clave-valor, como Redis, ofrecen mecanismos integrados para la replicación y particionamiento de datos. La replicación permite mantener copias de los datos en múltiples nodos para garantizar la disponibilidad y la tolerancia a fallos. El particionamiento, por otro lado, divide los datos en fragmentos que se distribuyen entre los nodos del clúster, permitiendo un acceso más rápido y eficiente a los datos.

Otra característica importante de las bases de datos clave-valor es su flexibilidad en el esquema de datos. Al no tener un esquema fijo, las bases de datos clave-valor permiten almacenar cualquier tipo de información sin restricciones. Esto es especialmente útil en entornos donde la estructura de los datos puede variar o evolucionar con el tiempo.

En una base de datos clave-valor, el valor es un blob (Binary Large Object) que la base de datos almacena sin preocuparse por su contenido. Es responsabilidad de las aplicaciones comprender y manejar los datos almacenados. Esta flexibilidad permite a los desarrolladores almacenar cualquier tipo de información sin restricciones de esquema.

Las bases de datos clave-valor son especialmente útiles en escenarios donde se requiere un acceso rápido a los datos y donde la estructura de los datos puede variar. Algunos ejemplos de uso incluyen:

* **Caché de datos**: Almacenar resultados de consultas frecuentes para mejorar el rendimiento de las aplicaciones.
* **Sesiones de usuario**: Guardar información de sesión de usuarios en aplicaciones web.
* **Almacenamiento de configuraciones**: Mantener configuraciones de aplicaciones que pueden cambiar dinámicamente.

En resumen, las bases de datos clave-valor son una solución poderosa y flexible para el almacenamiento de datos en aplicaciones modernas, ofreciendo un equilibrio entre simplicidad, rendimiento y escalabilidad.

:::{note}

[Ranking de las bases de datos clave-valo](https://db-engines.com/en/ranking/key-value+store) en DB-Engines.com 
:::

## Redis como base de datos clave-valor

> Redis is an open source (BSD licensed), **in-memory data structure store used as a database, cache, message broker, and streaming engine**. Redis provides data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes, and streams. Redis has **built-in replication**, Lua scripting, LRU eviction, transactions, and **different levels of on-disk persistence** via Redis Sentinel and **automatic partitioning** with Redis Cluster.
> (https://redis.io/docs/about/)

:::{note}

Para probar Redis en un tutorial interactivo ir a [Tutorial Try Redis](https://try.redis.io/).
:::

### Instalación

* [Instalación de Redis en Ubuntu](https://www.server-world.info/en/note?os=Ubuntu_22.04&p=redis6&f=1)
* [Configuración de Sentinel para alta disponibidad](https://facsiaginsa.com/redis/setup-redis-ha-using-sentinel)
* [Instalación standalone con Docker Compose (incluye RedisInsight - Web GUI)](https://gist.github.com/ualmtorres/3cb1c0536fb691577c78bfa8402135e0)
* [Cluster Redis con Docker Compose](https://raw.githubusercontent.com/bitnami/containers/main/bitnami/redis-cluster/docker-compose.yml)

### Configuración de Redis

La configuración de Redis se realiza mediante el archivo `redis.conf`, que está muy bien documentado y permite ajustar numerosos parámetros para adaptar el comportamiento de Redis a las necesidades específicas de cada aplicación. Algunos de los parámetros más importantes que se pueden configurar son:

* `daemonize` (ejecución como daemon)
* `port` (puerto de escucha)
* `bind` (IPs a las que se escucha)
* `timeout` (corte a clientes inactivos)
* `databases` (número de bases de datos)
* `save` (retraso en almacenamiento en disco)
* `dbfilename` (nombre del archivo de la BD. En /src)
* `slaveof` (para Replicación Maestro-Esclavo)

Destacar, que cada servidor tiene su propio `redis.conf``

### Snapshotting

Redis no almacena directamente en disco, sino que lo hace de forma periódica o a petición del cliente. Esto puede provocar pérdida de datos en caso de fallo. Para evitarlo, Redis dispone de un archivo `appendonly.aof` que guarda un registro para cada operación de escritura. Si el servidor cae antes de escribir en disco, cuando vuelva a estar disponible incluirá lo que esté pendiente. En `redis.conf` se especifica el número de cambios ocurridos antes de almacenar en disco. Los valores predeterminados son:

```bash
save 900 1 &lt;1>
save 300 10 &lt;2>
save 60 10000 &lt;3>
```
1. Almacenar después de 900 segundos si hay al menos 1 cambio
2. Almacenar después de 300 segundos si hay al menos 10 cambios
3. Almacenar después de 60 segundos si hay al menos 10.000 cambios

También es posible no almacenar nada en disco con `appendfsync no` o hacerlo siempre con `appendfsync always`.

* `appendfsync no` es útil en entornos donde la velocidad es crítica y la pérdida de datos es aceptable, como en sistemas de caché donde los datos pueden ser regenerados fácilmente.
* `appendfsync always` es ideal para aplicaciones donde la consistencia de los datos es crucial y no se puede permitir la pérdida de datos, como en sistemas financieros o de transacciones críticas.

### Archivo Append-Only

Redis es persistente eventualmente de forma predeterminada, lo que significa que escribe los datos en disco a intervalos regulares o a petición del cliente mediante el comando `SAVE`. Sin embargo, esta forma de persistencia puede no ser suficiente en aplicaciones donde la consistencia de los datos es crucial. Para abordar esta necesidad, Redis ofrece el archivo `appendonly.aof`, que guarda un registro de cada operación de escritura realizada en la base de datos.

El archivo `appendonly.aof` actúa como un log que registra todas las operaciones de escritura en el orden en que se realizaron. Esto asegura que, en caso de que el servidor Redis se caiga antes de escribir los datos en disco, cuando vuelva a estar disponible, podrá reproducir todas las operaciones pendientes y restaurar el estado exacto de la base de datos.

Para activar el archivo `appendonly.aof`, es necesario modificar el archivo de configuración `redis.conf` y establecer el parámetro `appendonly` a `yes`:

```bash
appendonly yes
```

Una vez activado, Redis comenzará a registrar todas las operaciones de escritura en el archivo `appendonly.aof`. Sin embargo, es importante encontrar un equilibrio entre la consistencia de los datos y el rendimiento del sistema. Esto se puede ajustar mediante el parámetro `appendfsync` en `redis.conf`, que controla la frecuencia con la que Redis sincroniza el archivo `appendonly.aof` con el disco. Los valores posibles son:

* `appendfsync always`: Redis sincroniza el archivo `appendonly.aof` con el disco después de cada operación de escritura. Este valor garantiza la máxima consistencia de los datos, pero puede afectar significativamente el rendimiento.
* `appendfsync everysec`: Redis sincroniza el archivo `appendonly.aof` con el disco cada segundo. Este es el valor más habitual, ya que ofrece un buen equilibrio entre consistencia y rendimiento.
* `appendfsync no`: Redis no sincroniza el archivo `appendonly.aof` con el disco. Este valor maximiza el rendimiento, pero puede provocar pérdida de datos en caso de fallo del servidor.

```bash
# appendfsync always
appendfsync everysec
# appendfsync no
```

Además, Redis ofrece la posibilidad de realizar una reescritura del archivo `appendonly.aof` para reducir su tamaño y mejorar el rendimiento. La reescritura se realiza en segundo plano y consiste en crear un nuevo archivo `appendonly.aof` que contiene solo las operaciones necesarias para reconstruir el estado actual de la base de datos. Esto elimina las operaciones redundantes y reduce el tamaño del archivo.

En resumen, el archivo `appendonly.aof` es una herramienta poderosa que permite a Redis ofrecer una persistencia más robusta y consistente, adecuada para aplicaciones críticas donde la pérdida de datos no es aceptable. Sin embargo, es importante configurar adecuadamente los parámetros de sincronización para encontrar el equilibrio adecuado entre consistencia y rendimiento.

### Monitorización del rendimiento

Para monitorizar el rendimiento de Redis, existe una utilidad llamada `redis-benchmark` que permite realizar pruebas de rendimiento y medir la capacidad de respuesta del servidor en diferentes escenarios. Algunos de los parámetros que se pueden ajustar en `redis-benchmark` son:

* Número de peticiones
* Número de clientes paralelos
* Operaciones a realizar (SET, GET, INCR, LPUSH, LPOP, SADD, SPOP, LRANGE, MSET, etc.)

De forma predeterminada, `redis-benchmark` se conecta al puerto 6379 y realiza 10.000 peticiones con 50 clientes paralelos. Sin embargo, estos valores se pueden ajustar según las necesidades de la prueba. Por ejemplo, para cambiar el número de peticiones, se puede utilizar el siguiente comando:

```bash
redis-benchmark -n &lt;numeroPeticiones>
```

### Clientes

Redis es una base de datos muy popular y cuenta con una amplia variedad de clientes en diferentes lenguajes de programación. Estos clientes permiten a los desarrolladores interactuar con Redis desde sus aplicaciones de forma sencilla y eficiente. Algunos de los clientes más populares son C, C#, Dart, Go, Java, Node.js, Perl, PHP, Python, Ruby, ... Para más información, se puede consultar la página oficial de [clientes de Redis](http://redis.io/clients).

## Operaciones básicas

Redis ofrece una amplia variedad de comandos para interactuar con la base de datos. Algunos de los comandos más comunes son:

### `SET` y `GET`

* `SET key value`: Establece el valor de una clave.
* `GET key`: Obtiene el valor de una clave.
* `MSET key value [key value ...]`: Establece múltiples claves y valores.
* `MGET key [key ...]`: Obtiene los valores de múltiples claves.
* `DEL key`: Elimina una clave.

```bash
SET ual "https://www.ual.es"
"OK"

GET ual
"https://www.ual.es"

MSET ugr "https://www.ugr.es" uma "https://www.uma.es"
"OK"

MGET ugr uma
1) "https://www.ugr.es"
2) "https://www.uma.es"

DEL uma
(integer) 1

GET uma
"(nil)"
```

Como ejemplo de uso de SET y GET, se puede utilizar Redis para

* Almacenar y recuperar URLs acortadas. Por ejemplo, se puede almacenar la URL original como valor y la URL corta como clave. De esta forma, al recuperar la URL corta, se obtiene la URL original. Este es un caso de uso común en aplicaciones web que requieren acortar URLs para compartir enlaces de forma más sencilla. 
* Caché de llamadas a APIs. Por ejemplo, se puede almacenar la URL de la llamada a la API como clave y el contenido de la respuesta como valor. De esta forma, si se realiza una llamada a la misma URL, se puede recuperar la respuesta desde la caché en lugar de realizar la llamada de nuevo.

### Operaciones `INCR` y `DECR`

* `INCR key`: Incrementa el valor de una clave numérica.
* `DECR key`: Decrementa el valor de una clave numérica.
* `INCRBY key increment`: Incrementa el valor de una clave numérica en una cantidad específica.
* `DECRBY key decrement`: Decrementa el valor de una clave numérica en una cantidad específica.

```bash
SET count 1
"OK"

INCR count
(integer) 2

GET count
"2"

INCRBY count 10
(integer) 12

DECRBY count 5
(integer) 7

GET count
"7"

SET cadena "Hola"
"OK"

INCR cadena
"ERR value is not an integer or out of range"
```

Como ejemplo de uso de `INCR` y `DECR`, se puede utilizar Redis para

* Contar el número de clics en un enlace (p.e. de un producto en una tienda online). Por ejemplo, se puede almacenar el número de clics como un valor numérico en una clave y utilizar `INCR` para incrementar el número de clics cada vez que alguien hace clic en el enlace.
* Controlar el stock de productos en un almacén. Por ejemplo, se puede almacenar la cantidad de productos disponibles como un valor numérico en una clave y utilizar `DECR` para decrementar la cantidad cada vez que se realiza una venta.

### Operaciones sobre cadenas

* `APPEND key value`: Añade un valor al final de una cadena.

```bash
EXISTS myKey
"(nil)"

APPEND myKey "Hello"
(integer) 5

APPEND myKey " World!!"
(integer) 13

GET myKey
"Hello World!!"
```

Como ejemplo de uso de `APPEND`, se puede utilizar Redis para

* Almacenar y concatenar mensajes de chat en tiempo real. Por ejemplo, se puede almacenar cada mensaje como un valor en una clave y utilizar `APPEND` para añadir nuevos mensajes al final de la cadena. De esta forma, se puede mantener un historial de mensajes en una conversación de chat.
* Generar un log de eventos en una aplicación. Por ejemplo, se puede almacenar cada evento como un valor en una clave y utilizar `APPEND` para añadir nuevos eventos al final de la cadena. De esta forma, se puede mantener un registro de todos los eventos ocurridos en la aplicación.

### Operaciones sobre listas

Las listas son colecciones ordenadas de elementos que actúan como pilas y colas. Permiten también la inserción en cualquier parte y pueden contener valores repetidos.

* `LPUSH key value`: Añade un valor al principio de una lista.
* `RPUSH key value`: Añade un valor al final de una lista.
* `LPOP key`: Elimina y obtiene el primer elemento de una lista.
* `RPOP key`: Elimina y obtiene el último elemento de una lista.
* `LRANGE key start stop`: Obtiene un rango de elementos de una lista.
* `LLEN key`: Obtiene la longitud de una lista.

```bash
LPUSH colores amarillo
(integer) 1

LPUSH colores rojo
(integer) 2

RPUSH colores verde
(integer) 3

LRANGE colores 0 -1
1) "rojo"
2) "amarillo"
3) "verde"

LRANGE colores 1 2
1) "amarillo"
2) "verde"

LPOP colores
"rojo"

LRANGE colores 0 -1
1) "amarillo"
2) "verde"

LSET colores 0 rojo
"OK"

LRANGE colores 0 -1
1) "rojo"
2) "verde"

LINDEX colores 1
"verde"

LINSERT colores before verde amarillo
(integer) 3

LRANGE colores 0 -1
1) "rojo"
2) "amarillo"
3) "verde"

LLEN colores
(integer) 3
```

Como ejemplo de uso de listas, se puede utilizar Redis para

* Mantener un historial de eventos en una aplicación. Por ejemplo, se puede almacenar cada evento como un valor en una lista y utilizar `RPUSH` para añadir nuevos eventos al final de la lista y `LRANGE` para obtener un rango de eventos.
* Implementar un sistema de notificaciones en tiempo real. Por ejemplo, se puede almacenar cada notificación como un valor en una lista y utilizar `LPUSH` para añadir nuevas notificaciones al principio de la lista y `LRANGE` para obtener un rango de notificaciones.
* Almacenar y recuperar mensajes en una aplicación de chat. Por ejemplo, se puede almacenar cada mensaje como un valor en una lista y utilizar `RPUSH` para añadir nuevos mensajes al final de la lista y `LRANGE` para obtener un rango de mensajes.
* Almacenamiento de los sitios preferidos de usuarios. Por ejemplo, se puede almacenar cada sitio web como un valor en una lista y utilizar `RPUSH` para añadir nuevos sitios al final de la lista y `LRANGE` para obtener un rango de sitios.

### Listas bloqueadas

Las listas bloqueadas son una forma de comunicación entre procesos en Redis. Un cliente suscriptor se suscribe a una lista con un tiempo de espera específico utilizando los comandos `BRPOP` o `BLPOP`. El cliente queda bloqueado esperando hasta que haya un valor disponible en la lista o hasta que se produzca el timeout. Mientras tanto, un grupo de usuarios publicadores pueden escribir (push) en la lista, y el cliente suscriptor sacará (pop) los elementos de la lista cuando estén disponibles.

Este mecanismo es útil en escenarios donde se necesita una comunicación asíncrona y eficiente entre diferentes procesos. Por ejemplo, en una aplicación de mensajería, los mensajes pueden ser publicados en una lista por los usuarios y los suscriptores pueden recibirlos en tiempo real. Otro caso de uso es en sistemas de procesamiento de tareas, donde las tareas son encoladas por diferentes productores y procesadas por consumidores en el orden en que fueron añadidas a la lista.

El uso de listas bloqueadas en Redis permite implementar patrones de diseño como colas de trabajo y sistemas de notificación en tiempo real, aprovechando la alta disponibilidad y el rendimiento de Redis.

|     |     |
| --- | --- |
| **Suscriptor** | **Publicador** |
| `www.xxx.yyy.zzz:6379> echo "Soy el suscriptor"` `"Soy el suscriptor"` `BRPOP comments 300` _Bloqueado hasta publicación o timeout_ |  |
|  |  `echo "Soy el productor de elementos"` `"Soy el productor de elementos"` `> LPUSH comments "Mi comentario"` `(integer) 1` |
| _Desbloqueado por la publicación_ `1) "comments"` `2) "Mi comentario"` `(220.57s)` &lt;- _Tiempo transcurrido para la publicación por parte del suscriptor_ |  |

### Operaciones sobre conjuntos

Los conjuntos son colecciones no ordenadas de elementos únicos.

* `SADD key member`: Añade un miembro a un conjunto.
* `SREM key member`: Elimina un miembro de un conjunto.
* `SMEMBERS key`: Obtiene todos los miembros de un conjunto.
* `SCARD key`: Obtiene el número de miembros de un conjunto.

```bash
GET semaforo
"(nil)"

SADD semaforo rojo amarillo verde
(integer) 3

SMEMBERS semaforo
1) "amarillo"
2) "verde"
3) "rojo"

SREM semaforo amarillo
(integer) 1

SPOP semaforo
"verde"

SMEMBERS semaforo
1) "rojo"

SADD semaforo amarillo verde
(integer) 2

SCARD semaforo
(integer) 3

SADD semaforo amarillo
(integer) 1

SMEMBERS semaforo
1) "amarillo"
2) "verde"
3) "rojo"
```

Como ejemplo de uso de conjuntos, se puede utilizar Redis para

* Almacenar y recuperar etiquetas de artículos en un blog. Por ejemplo, se puede almacenar las etiquetas de cada artículo como miembros de un conjunto y utilizar `SADD` para añadir nuevas etiquetas al conjunto y `SMEMBERS` para obtener todas las etiquetas de un artículo.
* Implementar un sistema de votación en una aplicación. Por ejemplo, se puede almacenar los votos de los usuarios como miembros de un conjunto y utilizar `SADD` para añadir nuevos votos al conjunto y `SCARD` para obtener el número total de votos.
* `SUNION key [key ...]`: Añade múltiples conjuntos.
* `SDIFF key [key ...]`: Resta múltiples conjuntos.
* `SINTER key [key ...]`: Intersecta múltiples conjuntos.
* `SISMEMBER key member`: Determina si un valor dado es miembro de un conjunto.

```bash
SADD arcoIris rojo naranja amarillo verde azul añil violeta
(integer) 7

SUNION semaforo arcoIris
1) "azul"
2) "a\xc3\xb1il"
3) "naranja"
4) "verde"
5) "rojo"
6) "amarillo"
7) "member"
8) "violeta"

SDIFF arcoIris semaforo
1) "azul"
2) "a\xc3\xb1il"
3) "naranja"
4) "violeta"

SINTER arcoIris semaforo
1) "verde"
2) "rojo"
3) "amarillo"

SISMEMBER semaforo naranja
"(nil)"
```

Como ejemplo de uso de las operaciones de unión, intersección y diferencia entre conjuntos, se puede utilizar Redis para

* Contar el número de visitas a un sitio web. Por ejemplo, se puede almacenar las direcciones IP de los visitantes de cada página como miembros de un conjunto y utilizar `SADD` para añadir nuevas direcciones IP al conjunto. Luego, se pueden combinar conjuntos de direcciones IP para obtener un conjunto de direcciones IP únicas y contar el número total de visitas.
* Gestionar listas de amigos en redes sociales. Por ejemplo, se puede utilizar Redis para almacenar los amigos de cada usuario como miembros de un conjunto y utilizar `SINTER` para encontrar amigos en común entre dos usuarios.
* Implementar un sistema de recomendaciones. Por ejemplo, se puede utilizar Redis para almacenar los productos comprados por cada usuario como miembros de un conjunto y utilizar `SDIFF` para recomendar productos que otros usuarios han comprado pero el usuario actual no.

### Conjuntos ordenados

Los conjuntos ordenados son colecciones de elementos únicos ordenados por un score asociado a cada elemento. Los conjuntos ordenados son útiles para almacenar datos que requieren un orden específico, como clasificaciones, puntuaciones, fechas, etc. Como son conjuntos, los elementos son únicos, y a su vez se comportan como una lista en tanto que los elementos están ordenados.

* `ZADD key score member`: Añade un miembro a un conjunto ordenado.
* `ZREM key member`: Elimina un miembro de un conjunto ordenado.
* `ZCOUNT key min max`: Obtiene el número de miembros en un rango de scores.
* `ZINCRBY key increment member`: Incrementa el score de un miembro en un conjunto ordenado.
* `ZSCORE key member`: Obtiene el score de un miembro en un conjunto ordenado.
* `ZRANGE key start stop [WITHSCORES]`: Obtiene un rango de miembros en un conjunto ordenado.
* `ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]`: Obtiene un rango de miembros en un conjunto ordenado por score.
* `ZRANK key member`: Obtiene el índice de un miembro en un conjunto ordenado.
* `ZREMRANGEBYRANK key start stop`: Elimina todos los miembros en un rango de índices en un conjunto ordenado.
* `ZREMRANGEBYSCORE key min max`: Elimina todos los miembros en un rango de scores en un conjunto ordenado.

```bash
ZADD productos 27 "Champu"
(integer) 1

ZADD productos 12 "Lejia"
(integer) 1

ZADD productos 34 "Suavizante"
(integer) 1

ZADD productos 6 "Detergente"
(integer) 1

ZADD productos 15 "Antical" 18 "Quitamanchas" 4 "Activador lavado" 3 "Agua destilada"
(integer) 4

ZCARD productos
(integer) 8

ZCOUNT productos 0 5
(integer) 2

ZINCRBY productos -1 "Champu"
"26"

ZSCORE productos "Champu"
"26"

ZRANGE productos 0 5 WITHSCORES
1) "Agua destilada"
2) "3"
3) "Activador lavado"
4) "4"
5) "Detergente"
6) "6"
7) "Lejia"
8) "12"
9) "Antical"
10) "15"
11) "Quitamanchas"
12) "18"

ZRANGEBYSCORE productos 0 5 WITHSCORES
1) "Agua destilada"
2) "3"
3) "Activador lavado"
4) "4"

ZRANK productos "Activador lavado"
(integer) 1

ZINCRBY productos -3 "Activador lavado"
"1"

ZRANK productos "Activador lavado"
(integer) 0

ZRANGE productos 0 -1 WITHSCORES
1) "Activador lavado"
2) "1"
3) "Agua destilada"
4) "3"
5) "Detergente"
6) "6"
7) "Lejia"
8) "12"
9) "Antical"
10) "15"
11) "Quitamanchas"
12) "18"

ZREM productos "Quitamanchas"
(integer) 1

ZREMRANGEBYSCORE productos 15 50
(integer) 1

ZRANGE productos 0 -1
1) "Activador lavado"
2) "Agua destilada"
3) "Detergente"
4) "Lejia"

ZREMRANGEBYRANK productos 2 -1
(integer) 2

ZRANGE productos 0 -1
1) "Activador lavado"
2) "Agua destilada"
```

Como ejemplo de uso de conjuntos ordenados, se puede utilizar Redis para

* Mantener una clasificación de productos en una tienda online. Por ejemplo, se puede almacenar los productos como miembros de un conjunto ordenado y utilizar `ZADD` para añadir nuevos productos al conjunto con un score asociado a su puntuación o clics. Luego, se pueden utilizar comandos como `ZRANGE` para obtener un rango de productos ordenados por score.
* Almacenar y recuperar puntuaciones de usuarios en un juego. Por ejemplo, se puede almacenar los usuarios como miembros de un conjunto ordenado y utilizar `ZINCRBY` para incrementar la puntuación de un usuario en el conjunto. Luego, se pueden utilizar comandos como `ZRANK` para obtener la posición de un usuario en la clasificación.

### Hashes

Los hashes son colecciones de pares campo-valor en Redis. Los hashes son útiles para representar objetos o estructuras de datos complejas en Redis, ya que permiten almacenar múltiples campos y valores en una sola clave. Los campos y valores en un hash son únicos, lo que significa que no puede haber dos campos con el mismo nombre en un hash. Son útiles para almacenar objetos o estructuras de datos complejas en Redis. Como restricción, no se pueden anidar.

* `HSET key field value`: Establece el valor de un campo en un hash.
* `HGET key field`: Obtiene el valor de un campo en un hash.
* `HMSET key field value [field value ...]`: Establece múltiples campos y valores en un hash.
* `HMGET key field [field ...]`: Obtiene los valores de múltiples campos en un hash.
* `HGETALL key`: Obtiene todos los campos y valores de un hash.
* `HDEL key field [field ...]`: Elimina uno o más campos de un hash.
* `HEXISTS key field`: Determina si un campo existe en un hash.
* `HINCRBY key field increment`: Incrementa el valor de un campo en un hash.
* `HKEYS key`: Obtiene todos los campos de un hash.
* `HVALS key`: Obtiene todos los valores de un hash.

```bash
HSET user:1000 nombre "Manuel Torres"
(integer) 1

HSET user:1000 email "mtorres@ual.es"
(integer) 1

HSET user:1000 universidad "Universidad de Almeria"
(integer) 1

HSET user:1000 asignaturas 5
(integer) 1

HGET user:1000 email
"mtorres@ual.es"

HMSET user:1001 nombre "Antonio Corral" email "acorral@ual.es" asignaturas 7
"OK"

HGETALL user:1000
1) "nombre"
2) "Manuel Torres"
3) "email"
4) "mtorres@ual.es"
5) "universidad"
6) "Universidad de Almeria"
7) "asignaturas"
8) "5"

HDEL user:1001 asignaturas
(integer) 1

HEXISTS user:1001 asignaturas
"(nil)"

HINCRBY user:1000 asignaturas 1
(integer) 6

HKEYS user:1000
1) "nombre"
2) "email"
3) "universidad"
4) "asignaturas"

HVALS user:1000
1) "Manuel Torres"
2) "mtorres@ual.es"
3) "Universidad de Almeria"
4) "6"

HVALS user:1001
1) "Antonio Corral"
2) "acorral@ual.es"

HKEYS user:1001
1) "nombre"
2) "email"
```

Como ejemplo de uso de hashes, se puede utilizar Redis para

* Gestionar carritos de compra en una tienda online. Por ejemplo, se puede almacenar los productos y cantidades en un hash con el ID del usuario como clave y los productos y cantidades como campos y valores en el hash. Luego, se pueden utilizar comandos como `HSET` para añadir productos al carrito y `HGET` para obtener los productos del carrito.
* Gestionar el inventario de productos en una tienda online. Por ejemplo, se puede almacenar los productos y cantidades disponibles en un hash con el ID del producto como clave y la cantidad disponible como valor en el hash. Luego, se pueden utilizar comandos como `HSET` para añadir productos al inventario y `HGET` para obtener la cantidad disponible de un producto.
* Seguidores de un usuario en una red social. Por ejemplo, se puede almacenar los seguidores de un usuario en un hash con el ID del usuario como clave y los IDs de los seguidores como campos y valores en el hash. Luego, se pueden utilizar comandos como `HSET` para añadir seguidores al usuario y `HGET` para obtener los seguidores de un usuario.

## Expiración de pares clave-valor

Redis es especialmente útil como caché para almacenar datos que son costosos de obtener o calcular. Utilizando el tiempo de vida (TTL) de las claves, podemos evitar que la caché crezca sin control. Los pares clave-valor se eliminan automáticamente después de un tiempo especificado, asegurando que la caché se mantenga eficiente y actualizada.

* `EXPIRE key seconds`: Establece el tiempo de vida de una clave en segundos.
* `SETEX key seconds value`: Establece el valor y el tiempo de vida de una clave en segundos.
* `EXPIREAT key timestamp`: Establece el tiempo de vida de una clave como una marca de tiempo UNIX.
* `TTL key`: Obtiene el tiempo de vida de una clave.
* `EXISTS key`: Determina si una clave existe.
* `PERSIST key`: Elimina el tiempo de vida de una clave antes de que expire.

```bash
SET "https://www.bdge.com" "<html><body> ... </body></html>"
"OK"

EXPIRE "https://www.bdge.com" 60
(integer) 1

SETEX "https://www.bdge.com" 60 "<html><body> ... </body></html>"
"OK"

TTL "https://www.bdge.com"
(integer) 53

EXISTS "https://www.bdge.com"
(integer) 1

PERSIST "https://www.bdge.com"
(integer) 1

TTL "https://www.bdge.com"
(integer) -1
```

Como ejemplo de uso de la expiración de pares clave-valor, se puede utilizar Redis para

* Almacenar datos temporales en una aplicación. Por ejemplo, se puede almacenar los resultados de una consulta a una API externa en una clave con un tiempo de vida de 5 minutos. De esta forma, los datos se mantienen actualizados y se eliminan automáticamente después de 5 minutos.
* Implementar una caché de consultas a una base de datos. Por ejemplo, se puede almacenar los resultados de una consulta a una base de datos en una clave con un tiempo de vida de 1 hora. De esta forma, las consultas se almacenan en caché y se eliminan automáticamente después de 1 hora.

## Espacios de nombres

Las claves residen en un espacio de nombres. Redis los llama bases de datos y se identifican por un número (0, 1, ...). Se puede cambiar de espacio de nombres con el comando `SELECT`. Redis permite hasta 16 bases de datos por defecto, pero se pueden configurar más si es necesario. Los espacios de nombres son útiles para separar diferentes conjuntos de datos y evitar colisiones de claves. Por ejemplo, se puede utilizar un espacio de nombres para cada idioma en una aplicación de internacionalización. 

* `SELECT index`: Cambia al espacio de nombres con el índice especificado.

```bash
SET saludo "Hola"
OK

GET saludo
"Hola"

SELECT 1
OK

GET saludo
(nil)

SET saludo "Hello" 
OK

GET saludo
"Hello"

SELECT 0
OK

GET saludo
"Hola"
```

## Publicación-Suscripción

Redis ofrece un mecanismo de publicación-suscripción que permite a los clientes enviar y recibir mensajes en tiempo real. Los clientes pueden suscribirse a canales y recibir mensajes publicados en esos canales por otros clientes. Este mecanismo es útil para implementar sistemas de mensajería, notificaciones en tiempo real, actualizaciones en tiempo real, etc.

* `SUBSCRIBE channel [channel ...]`: Se suscribe a uno o más canales.
* `UNSUBSCRIBE [channel [channel ...]]`: Se desuscribe de uno o más canales.
* `PUBLISH channel message`: Publica un mensaje en un canal.

| **Suscriptor 1** | **Suscriptor 2** | **Publicador** |
| --- | --- | --- |
| `SUBSCRIBE comments Reading messages... (press Ctrl-C to quit) 1) "subscribe" 2) "comments" 3) (integer) 1` _Esperando_ |  |  |
|  | `SUBSCRIBE comments Reading messages... (press Ctrl-C to quit) 1) "subscribe" 2) "comments" 3) (integer) 1` _Esperando_ |  |
|  |  | `PUBLISH comments "Esto es importante"` |
| `1) "message" 2) "comments" 3) "Esto es importante"`` _Esperando_ | `1) "message" 2) "comments" 3) "Esto es importante"`` _Esperando_ |  |

Como ejemplo de uso de la publicación-suscripción, se puede utilizar Redis para

* Implementar un sistema de notificaciones en tiempo real. Por ejemplo, se puede utilizar Redis para enviar notificaciones a los usuarios en tiempo real cuando se producen eventos importantes en la aplicación.
* Implementar un sistema de chat en tiempo real. Por ejemplo, se puede utilizar Redis para enviar mensajes de chat entre usuarios en tiempo real.

## Transacciones

Redis permite ejecutar un bloque de sentencias de forma similar a las transacciones. Las operaciones son encoladas para poder ejecutarse en secuencia. Las transacciones son cancelables si siguen encoladas.

* `MULTI`: Inicia una transacción.
* `EXEC`: Ejecuta una transacción.
* `DISCARD`: Cancela una transacción.

```bash

MULTI
"OK"

SET uja "https://www.uja.es"
"QUEUED"

SET count 1
"QUEUED"

EXEC
1) "OK"
2) "OK"
```

## Conclusiones

Redis es una base de datos clave-valor en memoria que ofrece una alta velocidad y rendimiento. Es especialmente útil para caché, almacenamiento de sesiones, colas de mensajes, y otras aplicaciones donde la velocidad y la eficiencia son críticas. Redis ofrece una amplia variedad de comandos para interactuar con la base de datos, incluyendo operaciones básicas como `SET` y `GET`, operaciones sobre listas, conjuntos, conjuntos ordenados, hashes, y más. También ofrece características avanzadas como la expiración de pares clave-valor, espacios de nombres, publicación-suscripción, transacciones, y más. Redis es una herramienta poderosa y versátil que se puede utilizar en una amplia variedad de aplicaciones y escenarios.

Sin embargo, Redis no es una base de datos relacional y no es adecuada para todas las aplicaciones. Normalmente, se utiliza en combinación con otras bases de datos para complementar sus funcionalidades y mejorar el rendimiento. Es importante tener en cuenta las limitaciones de Redis, como la capacidad de almacenamiento limitada por la cantidad de RAM disponible, la persistencia asincrónica, y la necesidad de configurar adecuadamente los parámetros de sincronización y persistencia para garantizar la consistencia de los datos. Es un complemento ideal para aplicaciones que requieren una alta velocidad y rendimiento, como aplicaciones web, juegos en línea, sistemas de mensajería, y más. Además, su capacidad de escalar horizontalmente y su amplia variedad de clientes en diferentes lenguajes de programación hacen de Redis una herramienta poderosa y versátil para desarrolladores y administradores de sistemas.


