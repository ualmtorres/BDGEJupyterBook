# Actividades complementarias

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

Este documento contiene una serie de actividades complementarias que se pueden realizar para profundizar en los contenidos del curso. Cada actividad plantea un escenario de uso y una serie de requisitos y funcionalidades que se deben implementar utilizando distintas tecnologías de bases de datos a gran escala. Las actividades están diseñadas para que se puedan aplicar los conocimientos adquiridos en el curso a situaciones reales y complejas, y para que se puedan desarrollar habilidades prácticas en el diseño e implementación de sistemas de bases de datos a gran escala.

**Objetivos**

* Aplicar los conocimientos adquiridos en el curso a situaciones reales y complejas.
* Desarrollar habilidades prácticas en el diseño e implementación de sistemas de bases de datos a gran escala.
* Profundizar en el uso de tecnologías de bases de datos a gran escala, como MongoDB, Redis, Neo4j y Cassandra.

## Gestión de una red de transporte y movilidad urbana

En esta actividad se plantea desarrollar una plataforma que gestione información sobre redes de transporte urbano, estaciones de bicicletas compartidas, rutas de autobuses y tráfico en tiempo real. Los usuarios pueden consultar información sobre estaciones cercanas, optimizar rutas y recibir notificaciones sobre eventos de tráfico o transporte público. Entre las principales funcionalidades de la plataforma se encuentran:

* Gestión de estaciones y ubicaciones manejando información espacial. El objetivo es almacenar y consultar ubicaciones de estaciones de bicicletas, paradas de autobuses y puntos de interés relevantes (como parkings o estaciones de carga de vehículos eléctricos). Este componente se desarrollará con MongoDB y se utilizará su funcionalidad de indexación espacial para almacenar y consultar información espacial.
* Gestión de tráfico en tiempo real y disponibilidad de vehículos. El objetivo es ,mantener información en tiempo real sobre el tráfico en distintas zonas y la disponibilidad de bicicletas en cada estación. Este componente se desarrollará con Redis y se utilizará su funcionalidad de listas y hashes para almacenar y consultar información en tiempo real. Asimismo, se puede usar el mecanismo de publicación/suscripción de Redis para notificar a los usuarios sobre disponibilidad de vehículos.
* Optimización de rutas y recomendaciones de transporte público. El objetivo es gestionar la red de transporte como un grafo, donde los nodos representen estaciones y los enlaces, las conexiones entre ellas con tiempos de viaje estimados. Este componente se desarrollará con Neo4j, se modelará la red de transporte como un grafo y se utilizarán algoritmos de búsqueda de caminos más cortos para recomendar la mejor ruta entre dos ubicaciones.
* Historial de viajes y patrones de uso. El objetivo es almacenar datos históricos sobre viajes realizados, patrones de uso y estadísticas de movilidad. Este componente se desarrollará con Cassandra y se utilizará un esquema diseñado para consultas rápidas sobre datos históricos, como obtener el historial de via jes de un usuario o analizar la demanda de vehículos en distintos momentos del día.

[Descripción detallada de la actividad](gestionRedTransporteMovilidadUrbana)

## Gestión de emergencias y desastres naturales

En esta actividad se plantea desarrollar una plataforma que gestione información sobre emergencias y desastres naturales, como incendios, inundaciones, terremotos o accidentes. Los usuarios pueden consultar información sobre eventos en tiempo real, recibir notificaciones sobre alertas y evacuaciones y acceder a recursos de ayuda y asistencia. Entre las principales funcionalidades de la plataforma se encuentran:

* Gestión de incidentes y recursos. El objetivo es almacenar información sobre incidentes, como su localización, gravedad y estado, y sobre los recursos disponibles para atenderlos, como equipos de emergencia, vehículos y suministros. Este componente se desarrollará con MongoDB y se utilizará su funcionalidad de documentos embebidos para almacenar información sobre incidentes y recursos relacionados, así como la funcionalidad de índices geoespaciales para consultar incidentes cercanos a una ubicación.
* Notificaciones y alertas en tiempo real. El objetivo es conocer en tiempo real la disponibilidad de recursos y la evolución de los incidentes, y notificar a los usuarios sobre alertas y evacuaciones. Este componente se desarrollará con Redis y se utilizará su funcionalidad de listas y colas para almacenar y consultar información en tiempo real. Asimismo, se puede usar el mecanismo de publicación/suscripción de Redis para notificar a los usuarios sobre alertas y evacuaciones.
* Coordinación de unidades de emergencia y rutas óptimas. El objetivo es coordinar la actuación de las unidades de emergencia y optimizar las rutas de desplazamiento, teniendo en cuenta la localización de los incidentes y la disponibilidad de recursos. Este componente se desarrollará con Neo4j, se modelará la red de emergencias como un grafo y se utilizarán algoritmos de búsqueda de caminos más cortos para coordinar las unidades de emergencia y mejorar la eficiencia en la asignación de recursos.
* Registro histórico de intervenciones. El objetivo es almacenar datos históricos sobre intervenciones realizadas, tiempos de respuesta y recursos utilizados. Este componente se desarrollará con Cassandra y se utilizará un esquema diseñado para consultas rápidas sobre datos históricos, como obtener el historial de intervenciones en un incidente o analizar la eficiencia en la asignación de recursos en distintos tipos de incidentes.

[Descripción detallada de la actividad](gestionEmergenciasDesastresNaturales)
