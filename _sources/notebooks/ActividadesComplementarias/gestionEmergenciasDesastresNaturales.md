# Actividad complementaria. Gestión de emergencias y desastres naturales

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

La gestión de emergencias y desastres naturales requiere soluciones tecnológicas avanzadas que permitan una respuesta rápida y eficiente. Este caso de uso explora cómo aprovechar bases de datos modernas como MongoDB, Redis, Neo4j y Cassandra para registrar, consultar y coordinar incidentes y recursos en tiempo real. A través del diseño de APIs REST y la implementación de funcionalidades clave como indexación espacial, publicación-suscripción y cálculo de rutas óptimas, se busca optimizar la toma de decisiones en situaciones críticas.

**Objetivos**

* Diseñar una base de datos en MongoDB para registrar y consultar incidentes y recursos en tiempo real.
* Implementar una API REST para gestionar incidentes y recursos utilizando MongoDB.
* Utilizar Redis para gestionar la disponibilidad de recursos y enviar alertas en tiempo real mediante publicación-suscripción.
* Desarrollar una API REST para interactuar con Redis y gestionar datos de disponibilidad y alertas.
* Modelar la infraestructura vial en Neo4j para calcular rutas óptimas y coordinar unidades de emergencia.
* Crear una API REST para asignar unidades de emergencia y calcular rutas óptimas con Neo4j.
* Utilizar Cassandra para registrar incidentes y su historial de actualizaciones.
* Implementar una API REST para consultar y actualizar incidentes en Cassandra.

## Gestión de incidentes y recursos con MongoDB

En una situación de emergencia, es fundamental poder registrar y localizar rápidamente incidentes como terremotos, incendios o inundaciones. Asimismo, es crucial identificar los recursos de respuesta cercanos, como hospitales, estaciones de bomberos y refugios. Esta práctica se enfoca en diseñar una base de datos en MongoDB que permita almacenar y consultar incidentes en tiempo real utilizando indexación espacial. Además, se desarrollará una API REST para gestionar estos datos de manera eficiente. Como casos de uso clave, se tendrían:

* Registrar una nueva ruta de senderismo.
* Obtener rutas cercanas a una ubicación.
* Consultar los detalles de una ruta específica.
* Registrar una valoración para una ruta.
* Obtener valoraciones de una ruta específica.

### Diseño de la base de datos

Para este escenario, se crearán dos colecciones principales:

* ***incidents***: Almacena las incidencias creadas, su ubicación y estado.
* ***resources***: Registra los recursos de respuesta disponibles, como hospitales y estaciones de bomberos.

#### Estructura de la colección `incidents`

```json
{
  "incident_id": "inc123",
  "type": "earthquake",
  "severity": "high",
  "location": { "type": "Point", "coordinates": [-118.2437, 34.0522] },
  "reported_at": "2025-03-20T14:30:00Z",
  "status": "active"
}
```

#### Estructura de la colección `resources`

```json
{
  "resource_id": "res456",
  "type": "hospital",
  "name": "General Hospital",
  "capacity": 200,
  "location": { "type": "Point", "coordinates": [-118.2500, 34.0500] }
}
```

#### Creación de índices espaciales

Para almacenar y consultar información espacial, se utilizará el índice geoespacial de MongoDB. El índice se creará en el campo `location` de ambas colecciones.

```javascript
db.incidents.createIndex({ location: "2dsphere" });
db.resources.createIndex({ location: "2dsphere" });
```

### Desarrollo de la API REST

Se desarrollará una API REST con las siguientes rutas:

* `POST /incidents`: registra una nueva incidencia.
* `GET /incidents`: obtiene las incidencias cercanas a una ubicación.
* `POST /resources`: registra un nuevo recurso de respuesta.
* `GET /resources`: obtiene los recursos cercanos a una ubicación.
* `PUT /incidents/{incident_id}`: actualiza el estado de una incidencia.

#### Registrar una nueva incidencia

* ***Endpoint:*** `POST /incidents`
* ***Cuerpo de la petición:***

  ```json
  {
    "type": "fire",
    "severity": "medium",
    "location": { "type": "Point", "coordinates": [-118.255, 34.045] },
    "reported_at": "2025-03-20T15:00:00Z",
    "status": "active"
  }
  ```
* ***Consulta MongoDB:***

  ```javascript
  db.incidents.insertOne({
    type: "fire",
    severity: "medium",
    location: { type: "Point", coordinates: [-118.255, 34.045] },
    reported_at: ISODate("2025-03-20T15:00:00Z"),
    status: "active"
  });
  ```

#### Consultar incidentes cercanos

* ***Endpoint:*** `GET /incidents?lat=34.0500&lon=-118.2500&radius=5000`
* ***Consulta MongoDB:***

  ```javascript
  db.incidents.find({
    location: {
      $near: {
        $geometry: { type: "Point", coordinates: [-118.2500, 34.0500] },
        $maxDistance: 5000
      }
    }
  });
  ```

#### Registrar un recurso de emergencia

* ***Endpoint:*** `POST /resources`
* ***Cuerpo de la petición:***

  ```json
  {
    "type": "fire_station",
    "name": "Fire Station #1",
    "capacity": 5,
    "location": { "type": "Point", "coordinates": [-118.2600, 34.0480] }
  }
  ```
* ***Consulta MongoDB:***

  ```javascript
  db.resources.insertOne({
    type: "fire_station",
    name: "Fire Station #1",
    capacity: 5,
    location: { type: "Point", coordinates: [-118.2600, 34.0480] }
  });
  ```

#### Buscar recursos cercanos

* ***Endpoint:*** `GET /resources?lat=34.0500&lon=-118.2500&radius=5000`
* ***Consulta MongoDB:***

  ```javascript
  db.resources.find({
    location: {
      $near: {
        $geometry: { type: "Point", coordinates: [-118.2500, 34.0500] },
        $maxDistance: 5000
      }
    }
  });
  ```

#### Actualizar el estado de un incidente

* ***Endpoint:*** `PUT /incidents/{incident_id}`
* ***Cuerpo de la petición:***

  ```json
  {
    "status": "resolved"
  }
  ```
* ***Consulta MongoDB:***

  ```javascript
  db.incidents.updateOne(
    { _id: ObjectId("inc123") },
    { $set: { status: "resolved" } }
  );
  ```

## Disponibilidad de recursos y alertas en tiempo real con Redis

La rapidez y eficiencia en la gestión de emergencias pueden marcar la diferencia entre salvar o perder vidas. Los sistemas tradicionales de gestión de emergencias a menudo se ven limitados por su capacidad de respuesta en tiempo real y su escalabilidad. Redis, con su arquitectura en memoria y sus capacidades de publicación-suscripción, ofrece una solución ideal para manejar la coordinación de recursos de emergencia y la distribución de alertas en tiempo real. En esta práctica utilizaremos Redis para gestionar la disponibilidad de unidades de emergencia (ambulancias, bomberos y refugios) y enviar notificaciones en tiempo real ante situaciones de crisis. Se trata de usar Redis como caché para gestionar la disponibilidad de unidades de emergencia en tiempo real, implementar un sistema de publicación-suscripción (pub/sub) para alertar a las unidades y otros sistemas de emergencias sobre incidentes detectados, y desarrollar una API REST para interactuar con Redis y gestionar los datos de disponibilidad y alertas.

Hay que desarrollar una API REST que permita:

* Registrar la disponibilidad de una unidad de emergencia.
* Consultar la disponibilidad de una unidad específica.
* Obtener la lista de todas las unidades disponibles.
* Marcar una unidad como ocupada.
* Enviar alertas en tiempo real a través del sistema de publicación-suscripción de Redis.
* Suscribirse a alertas para recibir notificaciones en tiempo real.

Para ello, se utilizará Redis y su funcionalidad de listas y colas para almacenar y consultar información en tiempo real, así como su mecanismo de publicación/suscripción para notificar a los usuarios sobre alertas y evacuaciones.

### Diseño de la base de datos

Se utilizarán las siguientes estructuras de Redis:

* ***Claves-valor*** para almacenar la disponibilidad de cada unidad.
* Formato clave: `unit:<id>`
* Valor: JSON con estado y ubicación.

  ```bash
  SET unit:ambulance123 '{"status": "available", "location": [-118.25, 34.05]}'
  ```
* ***Conjuntos ordenados (Sorted Sets)*** para indexar unidades disponibles según su ubicación.
* Clave: `available_units`
* Miembros: IDs de unidades con coordenadas como score.

  ```bash
  ZADD available_units 34.05 ambulance123
  ```
* ***Canales de publicación-suscripción*** para gestionar alertas en tiempo real.
* Canal: `alerts`

  ```bash
  PUBLISH alerts "Incendio detectado en el distrito norte. Equipos de respuesta activados."
  ```

### Desarrollo de la API REST

Se desarrollará una API REST con las siguientes rutas:

* `POST /units`: registrar una nueva unidad de emergencia.
* `GET /units/{id}`: obtener la disponibilidad de una unidad específica.
* `GET /units/available`: obtener la lista de todas las unidades disponibles.
* `PUT /units/{id}/status`: marcar una unidad como ocupada.
* `POST /alerts`: enviar una alerta en tiempo real.
* Suscribirse a alertas mediante un cliente de Redis.

#### Registrar una unidad de emergencia

* ***Endpoint:*** `POST /units`
* ***Entrada:***

  ```json
  {
    "id": "ambulance123",
    "type": "ambulance",
    "status": "available",
    "location": [-118.25, 34.05]
  }
  ```
* ***Operación en Redis:***

  ```bash
  SET unit:ambulance123 '{"status": "available", "location": [-118.25, 34.05]}'
  ZADD available_units 34.05 ambulance123
  ```

#### Consultar la disponibilidad de una unidad

* ***Endpoint:*** `GET /units/{id}`
* ***Operación en Redis:***

  ```bash
  GET unit:ambulance123
  ```
* ***Salida esperada:***

  ```json
  {
    "status": "available",
    "location": [-118.25, 34.05]
  }
  ```

#### Obtener todas las unidades disponibles

* ***Endpoint:*** `GET /units/available`
* ***Operación en Redis:***

  ```bash
  ZRANGE available_units 0 -1
  ```
* ***Salida esperada:***

  ```json
  ```

#### Marcar una unidad como ocupada

* ***Endpoint:*** `PUT /units/{id}/status`
* ***Entrada:***

  ```json
  {
    "status": "occupied"
  }
  ```
* ***Operación en Redis:***

  ```bash
  SET unit:ambulance123 '{"status": "occupied", "location": [-118.25, 34.05]}'
  ZREM available_units ambulance123
  ```

#### Enviar una alerta en tiempo real

* ***Endpoint:*** `POST /alerts`
* ***Entrada:***

  ```json
  {
    "message": "Incendio detectado en el distrito norte. Equipos de respuesta activados."
  }
  ```
* ***Operación en Redis:***

  ```bash
  PUBLISH alerts "Incendio detectado en el distrito norte. Equipos de respuesta activados."
  ```

#### Suscribirse a alertas

Para suscribirse al canal de alertas en Redis, se utilizará un cliente de Redis con la siguiente operación:

```bash
SUBSCRIBE alerts
```

## Coordinación de unidades de emergencia y rutas óptimas con Neo4j

En situaciones de emergencia, la rapidez de respuesta es clave. Un sistema eficiente debe permitir asignar la unidad más cercana y calcular la ruta óptima hasta el lugar del incidente. En esta práctica, se utilizará Neo4j para modelar la infraestructura vial de una ciudad y optimizar el desplazamiento de ambulancias, bomberos y otras unidades de emergencia. Como casos de uso clave, se tendrían:

* Registrar una unidad de emergencia y su ubicación actual.
* Informar de un incidente y asignar la unidad más cercana.
* Calcular la ruta óptima desde una unidad hasta un incidente.
* Marcar un incidente como resuelto y liberar la unidad asignada.

### Diseño de la base de datos

La base de datos se estructurará en un ***grafo dirigido*** con los siguientes nodos y relaciones:

* Nodos
* ***Ubicaciones (`Location`)***: Representan intersecciones, hospitales, estaciones de bomberos, etc. Atributos: `id`, `name`, `type` (hospital, estación de bomberos, intersección), `coordinates` (latitud y longitud)
* ***Unidades de emergencia (`EmergencyUnit`)***: Representan ambulancias, camiones de bomberos, etc. Atributos: `id`, `type` (ambulancia, bomberos, policía), `status` (disponible, en servicio), `location_id` (ubicación actual)
* ***Incidentes (`Incident`)***: Ubicación donde se necesita asistencia. Atributos: `id`, `type` (incendio, accidente, emergencia médica), `location_id` (ubicación del incidente), `priority` (alta, media, baja)
* Relaciones
* `(:Location)-[:CONNECTED_TO {distance, traffic_level}]->(:Location)`: Define las conexiones entre ubicaciones con información de distancia y tráfico.
* `(:EmergencyUnit)-[:STATIONED_AT]->(:Location)`: Relaciona una unidad con su base.
* `(:EmergencyUnit)-[:RESPONDING_TO]->(:Incident)`: Indica que una unidad está en camino a un incidente.

### Desarrollo de la API REST

Se desarrollará una API REST con las siguientes rutas:

* `POST /units`: registrar una unidad de emergencia.
* `POST /incidents`: reportar un incidente.
* `GET /units/available`: obtener unidades disponibles cerca de un incidente.
* `PUT /units/assign`: asignar una unidad de emergencia a un incidente.
* `GET /routes`: calcular la ruta óptima hasta un incidente.
* `PUT /incidents/resolve`: marcar un incidente como resuelto.

#### Registrar una unidad de emergencia

* ***POST /units***

  ```json
  {
      "id": "unit123",
      "type": "ambulance",
      "status": "available",
      "location_id": "loc45"
  }
  ```
* ***Cypher Query:***

  ```cypher
  CREATE (u:EmergencyUnit {id: 'unit123', type: 'ambulance', status: 'available'})
  WITH u
  MATCH (l:Location {id: 'loc45'})
  CREATE (u)-[:STATIONED_AT]->(l);
  ```

#### Reportar un incidente

* ***POST /incidents***

  ```json
  {
      "id": "incident789",
      "type": "accident",
      "location_id": "loc89",
      "priority": "high"
  }
  ```
* ***Cypher Query:***

  ```cypher
  MATCH (l:Location {id: 'loc89'})
  CREATE (i:Incident {id: 'incident789', type: 'accident', priority: 'high'})-[:LOCATED_AT]->(l);
  ```

#### Obtener unidades disponibles cerca de un incidente

* ***GET /units/available?incident_id=incident789***
* ***Cypher Query:***

  ```cypher
  MATCH (i:Incident {id: 'incident789'})-[:LOCATED_AT]->(loc:Location)
  MATCH (u:EmergencyUnit {status: 'available'})-[:STATIONED_AT]->(uLoc:Location)
  WITH u, loc, uLoc, point.distance(point({latitude: loc.coordinates[0], longitude: loc.coordinates[1]}), point({latitude: uLoc.coordinates[0], longitude: uLoc.coordinates[1]})) AS distance
  RETURN u.id, u.type, distance ORDER BY distance ASC LIMIT 3;
  ```

#### Asignar unidad de emergencia a un incidente

* ***PUT /units/assign***

  ```json
  {
      "unit_id": "unit123",
      "incident_id": "incident789"
  }
  ```
  ***Cypher Query:***

  ```cypher
  MATCH (u:EmergencyUnit {id: 'unit123'}), (i:Incident {id: 'incident789'})
  MERGE (u)-[:RESPONDING_TO]->(i)
  SET u.status = 'en_route';
  ```

#### Calcular ruta óptima hasta un incidente

* ***GET /routes?unit_id=unit123&incident_id=incident789***
* ***Cypher Query:***

  ```cypher
  MATCH (u:EmergencyUnit {id: 'unit123'})-[:STATIONED_AT]->(start:Location),
        (i:Incident {id: 'incident789'})-[:LOCATED_AT]->(end:Location)
  CALL algo.shortestPath.stream(start, end, 'distance')
  YIELD nodeId, cost
  RETURN algo.getNodeById(nodeId) AS location, cost;
  ```

#### Marcar incidente como resuelto

* ***PUT /incidents/resolve***

  ```json
  {
      "incident_id": "incident789"
  }
  ```
* ***Cypher Query:***

  ```cypher
  MATCH (i:Incident {id: 'incident789'})
  DETACH DELETE i;
  MATCH (u:EmergencyUnit)-[r:RESPONDING_TO]->(i)
  SET u.status = 'available'
  DELETE r;
  ```

## Registro y seguimiento de incidentes con Cassandra

En esta práctica utilizaremos Cassandra para gestionar el almacenamiento de datos relacionados con incidentes de emergencia. Dado que Cassandra es altamente escalable y optimizado para escrituras rápidas, es ideal para almacenar eventos de incidentes y su historial de actualizaciones. Como casos de uso clave, se tendrían:

* Registrar un incidente con detalles como ubicación, tipo de incidente y estado actual.
* Obtener los incidentes activos ordenados por fecha de reporte.
* Actualizar el estado de un incidente (ejemplo: "En curso" → "Resuelto").
* Consultar el historial de cambios de un incidente para conocer su evolución.

### Diseño de la base de datos

Se utilizará Cassandra para almacenar los datos de incidentes y su historial de cambios. Se crearán dos tablas principales:

* `incidents_by_location`: Para almacenar incidentes activos indexados por su ubicación y fecha de reporte.
* `incident_history`: Para registrar el historial de cambios en el estado de cada incidente.

```sql
CREATE TABLE incidents_by_location (
    location TEXT,
    incident_id UUID,
    report_time TIMESTAMP,
    type TEXT,
    status TEXT,
    description TEXT,
    PRIMARY KEY ((location), report_time, incident_id)
) WITH CLUSTERING ORDER BY (report_time DESC);
```

```sql
CREATE TABLE incident_history (
    incident_id UUID,
    update_time TIMESTAMP,
    status TEXT,
    note TEXT,
    PRIMARY KEY (incident_id, update_time)
) WITH CLUSTERING ORDER BY (update_time DESC);
```

### Desarrollo de la API REST

Se desarrollará una API REST con las siguientes rutas:

* `POST /incidents`: registrar un nuevo incidente.
* `GET /incidents?location=<location>`: obtener los incidentes activos en una ubicación.
* `PUT /incidents/{incident_id}`: actualizar el estado de un incidente.
* `GET /incidents/{incident_id}/history`: consultar el historial de cambios de un incidente.

#### Registrar un incidente

* ***Endpoint:*** `POST /incidents`
* ***Cuerpo de la petición:***

  ```json
  {
      "location": "Centro",
      "type": "Incendio",
      "description": "Fuego en un edificio de oficinas"
  }
  ```
* ***Operaciones en Cassandra:***

  ```sql
  INSERT INTO incidents_by_location (location, incident_id, report_time, type, status, description)
  VALUES ('Centro', uuid(), toTimestamp(now()), 'Incendio', 'Activo', 'Fuego en un edificio de oficinas');
  ```

#### Obtener incidentes activos en una ubicación

* ***Endpoint:*** `GET /incidents?location=<location>`
* ***Respuesta esperada:***

  ```json
  [
      {
          "incident_id": "550e8400-e29b-41d4-a716-446655440000",
          "report_time": "2024-03-19T12:30:00Z",
          "type": "Incendio",
          "status": "Activo",
          "description": "Fuego en un edificio de oficinas"
      }
  ]
  ```
* ***Operaciones en Cassandra:***

  ```sql
  SELECT * FROM incidents_by_location WHERE location = 'Centro';
  ```

#### Actualizar el estado de un incidente

* ***Endpoint:*** `PUT /incidents/{incident_id}`
* ***Cuerpo de la petición:***

  ```json
  {
      "status": "Resuelto",
      "note": "Bomberos controlaron el fuego."
  }
  ```
* ***Operaciones en Cassandra:***

  ```sql
  UPDATE incidents_by_location
  SET status = 'Resuelto'
  WHERE location = 'Centro' AND report_time = '2024-03-19T12:30:00Z'
  AND incident_id = 550e8400-e29b-41d4-a716-446655440000;

INSERT INTO incident_history (incident_id, update_time, status, note)
VALUES (550e8400-e29b-41d4-a716-446655440000, toTimestamp(now()), 'Resuelto', 'Bomberos controlaron el fuego.');
```

#### Consultar el historial de cambios de un incidente

* ***Endpoint:*** `GET /incidents/{incident_id}/history`
* ***Respuesta esperada:***

  ```json
  [
      {
          "update_time": "2024-03-19T13:00:00Z",
          "status": "Resuelto",
          "note": "Bomberos controlaron el fuego."
      },
      {
          "update_time": "2024-03-19T12:45:00Z",
          "status": "En curso",
          "note": "Bomberos en camino."
      }
  ]
  ```
* ***Operaciones en Cassandra:***

  ```sql
  SELECT * FROM incident_history WHERE incident_id = 550e8400-e29b-41d4-a716-446655440000;
  ```

## Conclusiones

La gestión de emergencias y desastres naturales requiere soluciones tecnológicas avanzadas que permitan una respuesta rápida y eficiente. En este caso de uso, hemos explorado cómo aprovechar bases de datos modernas como MongoDB, Redis, Neo4j y Cassandra para registrar, consultar y coordinar incidentes y recursos en tiempo real. A través del diseño de APIs REST y la implementación de funcionalidades clave como indexación espacial, publicación-suscripción y cálculo de rutas óptimas, hemos optimizado la toma de decisiones en situaciones críticas. La combinación de tecnologías de bases de datos a gran escala nos ha permitido abordar de manera efectiva los desafíos de la gestión de emergencias y desastres naturales, demostrando el potencial de estas herramientas en entornos críticos y dinámicos.
