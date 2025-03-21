# Lab 03. Desarrollo de un Blog con PHP y MongoDB

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

En este laboratorio se desarrolla una aplicación web PHP sobre una base de datos MongoDB. Se creará una API REST para interactuar con la base de datos.

**Objetivos**

* Familiarse con el driver MongoDB para PHP.
* Desarrollar una API REST en PHP sencilla sobre MongoDB.
* Crear una aplicación sencilla en PHP sobre MongoDB.

**Prerrequisitos**

* Tener configurado el entorno de desarrollo de la asignatura.

## Recursos

Puedes obtener más información sobre cómo trabajar con MongoDB siguiendo estos tutoriales:

* [MongoDB Tutorial de Geeks for Geeks](https://www.geeksforgeeks.org/mongodb-tutorial/?ref=lbp)
* [MongoDB Tutorial de Java T Point](https://www.javatpoint.com/mongodb-tutorial)

## Actividades

Desarrollar una aplicación web en PHP sobre una base de datos MongoDB como la que hay disponible en la URL facilitada en Aula Virtual. Se trata de un ejemplo de un Blog sencillo sobre información legislativa norteamericana. La aplicación ofrece las siguientes funciones básicas:

* En la página de inicio se muestran cinco posts (no se implementa la paginación de resultados).
* De cada post se muestra su título, fecha, persona que lo ha escrito, un extracto del cuerpo del post, las etiquetas, y todos los comentarios realizados, con un extracto del comentario y la persona que lo ha realizado.
* Se puede mostrar información completa sobre un post.
* Se pueden mostrar los posts (máximo 5) asociados a una etiqueta.
* La aplicación también muestra la lista de todas las personas que hayan realizado algún comentario en algún post. Se pueden mostrar los posts (máximo 5) comentados por una persona.
* La aplicación también implementa una API REST que permite lo siguiente:
  * Obtener toda la información de un post a partir de su `ObjectId`
  * Obtener todos los posts que tengan una etiqueta determinada
  * Crear un post
  * Añadir un comentario a un post

### Ejercicios propuestos

1. Importar el archivo JSON `posts.json` disponible en [SampleDatabases](https://github.com/ualmtorres/SampleDatabases) en una colección `posts` dentro de una base de datos denominada `blog`.
2. Hacer un fork de [https://gitlab.com/ualmtorres/MongoDBIncompleto.git](https://gitlab.com/ualmtorres/MongoDBIncompleto.git)
3. Modificar los scripts siguientes completando con el código necesario en los lugares en los que aparece `YOUR CODE HERE`
   * `connection.php`: Establece la conexión con MongoDB y selecciona la colección `posts`.
   * `showPosts.php`: Muestra cinco posts. Del cuerpo de cada post sólo se muestran los 300 primeros caracteres
   * `postHeader.php`: Muestra título, fecha y autor del post
   * `labels.php`: Muestra las etiquetas de un post
   * `comments.php`: Muestra los comentarios de un post (autor y 100 primeros caracteres del comentario)
   * `commentsAuthor.php`: Muestra la barra de derecha de autores de comentarios 
   * `showMore.php`: Muestra un post sin limitar a 300 los caracteres del texto del post
   * `showPostsByTag.php`: Muestra cinco posts que incluyan una etiqueta concreta
   * `showPostsCommentedByAuthor.php`: Muestra cinco posts en los que una persona concreta haya incluido un comentario
4. Modificar el script `api/index.php` para proporcionar una API REST que implemente los métodos siguientes

| Método  | URL                  | Descripción                                                                 | Uso                                                                                                                                                                                                 |
|---------|----------------------|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GET`   | `/api/id/{id}`       | Devuelve un post en JSON a partir de la clave proporcionada                 | `curl -i -X GET http://<host>/MongoDBBlog/api/api/id/50ab0f8bbcf1bfe2536dc3f8`                                                                                                                     |
| `GET`   | `/api/tag/{tag}`     | Devuelve los posts en JSON que incluyen la etiqueta proporcionada           | `curl -i -X GET http://<host>/MongoDBBlog/api/api/tag/trade`                                                                                                                                       |
| `POST`  | `/api`               | Crea un nuevo post con el documento proporcionado                           | `curl -i -X POST -d '{"body":"Lore ipsum", "permalink": "TqoHkbHyUgLyCKWgPLqm", "author": "machine", "title": "Lore ipsum", "tags": ["Lore", "ipsum"], "comments":[{"body": "Lore ipsum", "email": "john@doe.com", "author": "John Doe"}]}' http://<host>/MongoDBBlog/api/api` |
| `PUT`   | `/api/{id}`          | Modifica el documento especificado añadiéndole el comentario proporcionado  | `curl -i -X PUT -d '{"body":"Hello world!", "email": "foo@bar.com", "author": "Foo Bar"}' http://<host>/MongoDBBlog/api/api/50ab0f8bbcf1bfe2536dc3f8`                                               |
