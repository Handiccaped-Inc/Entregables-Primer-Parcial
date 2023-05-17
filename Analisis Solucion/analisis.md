# Analisis de la solucion

En el proyecto Open Market, se utilizó una arquitectura cliente-servidor monolitico en capas y para gestionar varias instancias de un servidor se usó un balanceador de carga. Además, se implementaron patrones de diseño como comando y observador en el lado del cliente. A continuación, se presenta un análisis para cada uno de los atributos de calidad que ademas tiene en cuenta los de escalabilidad, rendimiento y modificabilidad, ademas se menciona para cada uno al menos una preocupacián que se resuelve (concern), una táctica que se incluye y un patrón de arquitectura que se aplicó.


# Atributos de calidad

## Escalabilidad
### Preocupacion
Es necesario que el sistema sea capaz de manejar un crecimiento en el número de clientes y sus demandas.
### Tactica
**Introducir la concurrencia (Administrar recursos)** : Si las solicitudes se pueden procesar en paralelo, el tiempo bloqueado se puede reducir. La simultaneidad se puede introducir procesando diferentes flujos de eventos en diferentes subprocesos o creando subprocesos adicionales para procesar diferentes conjuntos de actividades. (Una vez que se ha introducido la simultaneidad, puede elegir políticas de planificacion(Scheduling) para lograr los objetivos que considere deseables utilizando la táctica de recursos de planificador).
### Patron
El patron de arquitectura que se aplico fue el de cliente servidor, este permite una mayor escalabilidad y modularidad en comparación con otros enfoques arquitectónicos. y mientras el cliente y el servidor continúen comunicándose a través de interfaces bien definidas, pueden evolucionar de forma independiente,
para manejar la concurrencia los servidores a menudo usan técnicas como el uso de hilos o procesos separados para manejar solicitudes simultáneas. Cada solicitud se puede asignar a un hilo o un proceso independiente para "procesar varias solicitudes al mismo tiempo".

## Rendimiento

### Preocupacion
Es necesario que el sistema sea capaz de distribuir la carga de las solicitudes en varias instancias de un servidor
### Tactica
**Mantener multiples copias de computacion (Administrar recursos)**: Esta táctica reduce la congestión que ocurriría si todas las solicitudes de servicio se asignaran a una sola instancia. Los servicios replicados en una arquitectura de microservicios o los servidores web replicados en un conjunto de servidores son ejemplos de réplicas de computación. Aqui es donde entra en juego el balanceador de carga, un balanceador de carga es un software que asigna nuevas tareas a uno de los servidores replicados disponibles; los criterios para la asignación varían, pero pueden ser tan simples como un esquema de round-robin o asignar la siguiente solicitud al servidor menos ocupado.
### Patron
El patron de arquitectura que se aplico fue el patron llamado balanceador de carga, este patrón implica la introducción de un componente adicional de la arquitectura de la aplicación que se ubica entre el cliente y el servidor de la aplicación.

El balanceador de carga actúa como intermediario, recibe todas las solicitudes de los clientes y luego determina a qué servidor de aplicaciones reenviar cada solicitud. Existen varios algoritmos y estrategias de equilibrio de carga que se pueden utilizar para tomar estas decisiones. Algunos de los métodos más comunes incluyen:

- **Round Robin**: las solicitudes se distribuyen secuencialmente entre los servidores de forma circular. Cada solicitud se envía al siguiente servidor disponible en la lista.

- **Balanceo ponderado**: a cada servidor se le asigna un peso en función de su capacidad o los recursos disponibles. Las solicitudes se envían al servidor en proporción a su peso.

- **Carga mínima**: el balanceador de carga analiza la carga actual en cada servidor y dirige la solicitud al servidor con la carga mínima actual.

- **Hashing**: una función hash se utiliza para asignar una solicitud a un servidor en función de algún atributo de la solicitud, como la dirección IP del cliente. Esto asegura que las solicitudes del mismo cliente siempre se envíen al mismo servidor.

Un equilibrador de carga también puede realizar otras funciones, como monitorear el estado de los servidores y eliminar de la rotación los servidores fallidos o sobrecargados. Además, algunos balanceadores de carga pueden implementar técnicas avanzadas como el almacenamiento en caché de respuestas para reducir la carga en los servidores de aplicaciones.

## Modificabilidad
### Preocupacion
Es necesario que el sistema sea modificable y que el costo de los cambios en terminos de tiempo y dinero sea reducido
### Tactica
**Dividir modulos(Incrementar cohesion)** : Si el módulo que está siendo modificado incluye responsabilidades que no son cohesivas, es probable que los costos de modificación sean altos. Refactorizar el módulo en varios módulos más cohesivos debería reducir el costo promedio de cambios futuros. Dividir un módulo no debe consistir simplemente en colocar la mitad de las líneas de código en cada submódulo; en su lugar, debería resultar sensata y apropiadamente en una serie de submódulos que sean cohesivos por sí mismos.

### Patron

Los patrones de arquitectura que se usaron fueron cliente-servidor monolitico en capas, El patrón cliente-servidor consiste en un servidor que proporciona servicios simultáneamente a múltiples clientes distribuidos. El ejemplo más común es un servidor web que proporciona información a múltiples usuarios simultáneos de un sitio web,el patrón de capas divide el sistema de tal manera que los módulos puedan desarrollarse y evolucionar por separado con poca interacción entre las partes, lo cual respalda la portabilidad, la modificabilidad y la reutilización. Para lograr esta separación de preocupaciones, el patrón de capas divide el software en unidades llamadas capas. Cada capa es un grupo de módulos que ofrece un conjunto cohesivo de servicios. La relación permitida para utilizar entre las capas está sujeta a una restricción clave: las relaciones deben ser unidireccionales, todo esto sumado hace que la modificabilidad sea mucho mas facil de realizar, ademas de que usar una patron de arquitectura como el cliente servidor trae otras ventajas como la escalabilidad que se mencionó anteriormente.

## Usabilidad

### Preocupación
Es necesario que el sistema y su aquitectura del lado del cliente sea interactiva
### Tactica
 **Mantener el modelo del sistema(Soportar la iniciativa del sistema)**: El sistema mantiene un modelo explícito de sí mismo. Esto se utiliza para determinar el comportamiento esperado del sistema de manera que se pueda proporcionar retroalimentación apropiada al usuario. Una manifestación común de un modelo de sistema es una barra de progreso que predice el tiempo necesario para completar la actividad actual.

### Patrón
El patron de arquitectura usado fue el MVC (Modelo-Vista-Controlador), este esta Enfocado en separar el modelo (la lógica "de negocios" subyacente del sistema), de su implementación en una o más vistas de interfaz de usuario. En el modelo MVC original, el modelo envia actualizaciones a una vista, que el usuario ve e interactua con. Las interacciones del usuario, como pulsaciones de teclas, clics de botón, movimientos del mouse, etc., se transmiten al controlador, que las interpreta como operaciones en el modelo y luego envia esas operaciones al modelo, que cambiaba su estado en respuesta. El camino inverso también es parte del patrón MVC original. Es decir, el modelo puede cambiar y el controlador enviar actualizaciones a la vista. El envío de actualizaciones depende de si el MVC se encuentra en un solo proceso o se distribuye en varios procesos (y potencialmente a través de la red). Si el MVC está en un solo proceso, entonces las actualizaciones se envían utilizando el patrón observador. Si el MVC se distribuye en varios procesos, a menudo se utiliza el patrón publicador-suscriptor para enviar actualizaciones, el uso de este patron hace que la interaccion con el sistema sea interactiva
