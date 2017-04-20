> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Este artículo proporciona una explicación detallada del [código de ejemplo de Hola a todos] [ lnk-helloworld-sample] para ilustrar los componentes fundamentales de la [Puerta de enlace de SDK de Azure IoT] [ lnk-gateway-sdk] arquitectura. El ejemplo utiliza el SDK de puerta de enlace de concentrador IoT para construir una puerta de enlace simple que registra un mensaje de "hello world" en un archivo cada cinco segundos.

Este tutorial trata sobre:

- **Conceptos**: información general y conceptual de los componentes que forman ninguna puerta de enlace se crea con el SDK de puerta de enlace de IoT.  
- **Arquitectura del ejemplo Hola a todos**: describe cómo se aplican los conceptos del ejemplo Hola a todos y cómo funcionan conjuntamente los componentes.
- **Cómo generar el ejemplo**: los pasos necesarios para generar el ejemplo.
- **Cómo ejecutar el ejemplo**: los pasos necesarios para ejecutar el ejemplo. 
- **La salida típica**: un ejemplo de la salida se obtendrá al ejecutar el ejemplo.
- **Fragmentos de código**: una colección de fragmentos de código para mostrar cómo el ejemplo Hello World implementa los componentes clave de la puerta de enlace.

## <a name="azure-iot-gateway-sdk-concepts"></a>Conceptos de Azure SDK de puerta de enlace de IoT

Antes de examinar el código de ejemplo o crear su propia puerta de enlace de campo con el SDK de puerta de enlace de IoT, debe comprender los conceptos clave que respaldan la arquitectura de SDK.

### <a name="modules"></a>Módulos

Crear una puerta de enlace con el SDK de puerta de enlace de Azure IoT mediante la creación y montaje de *módulos*. Módulos utilizan *mensajes* para intercambiar datos entre sí. Un módulo recibe un mensaje, realiza alguna acción en él, opcionalmente transforma en un mensaje nuevo y, a continuación, lo publica para otros módulos procesar. Algunos módulos pueden producir sólo nuevos mensajes y nunca se procesan los mensajes entrantes. Una cadena de módulos crea una canalización de procesamiento de datos con cada módulo realiza una transformación sobre los datos en un punto de esa canalización.

![Una cadena de módulos generados con el SDK de Azure IoT puerta de enlace de la puerta de enlace][1]
 
El SDK contiene lo siguiente:

- Módulos previamente escritos que realizan funciones comunes de puerta de enlace.
- Las interfaces que un desarrollador puede utilizar para escribir módulos personalizados.
- La infraestructura necesaria para implementar y ejecutar un conjunto de módulos.

El SDK proporciona una capa de abstracción que permite crear puertas de enlace para que se ejecute en una variedad de sistemas operativos y plataformas.

![Capa de abstracción de Azure SDK de puerta de enlace de IoT][2]

### <a name="messages"></a>Mensajes

Aunque pensar en módulos de pasar mensajes entre sí es una forma cómoda para conceptualizar cómo funciona una puerta de enlace, no refleja exactamente lo que sucede. Módulos utilizan un broker para comunicarse entre sí, publicar mensajes en el agente (bus, pubsub o cualquier otro patrón de mensajería) y dejar que el broker enrutar el mensaje a los módulos conectados a él.

Un módulos, se utiliza la función **Broker_Publish** para publicar un mensaje en el agente. El agente entrega los mensajes a un módulo al invocar una función de devolución de llamada. Un mensaje consiste en un conjunto de propiedades de clave/valor y contenido que se pasa como un bloque de memoria.

![La función del Broker en el SDK de puerta de enlace de Azure IoT][3]

### <a name="message-routing-and-filtering"></a>Enrutamiento de mensajes y el filtrado

Hay dos maneras de dirigir mensajes a los módulos correctos. Un conjunto de vínculos puede pasarse al intermediario para que el intermediario sepa el origen y el receptor para cada módulo, o el módulo puede filtrar en las propiedades del mensaje. Un módulo sólo debe actuar en un mensaje si el mensaje va dirigido a él. Los vínculos y el filtrado de mensajes es lo que efectivamente crea una canalización de mensajes.

## <a name="hello-world-sample-architecture"></a>Arquitectura de ejemplo de Hola mundo

El ejemplo Hello World ilustra los conceptos descritos en la sección anterior. El ejemplo Hello World implementa una puerta de enlace que tiene un proceso que consta de dos módulos:

-   Crea un mensaje cada cinco segundos el módulo *Hola a todos* y lo pasa al módulo de registrador.
-   El módulo *registrador* escribe los mensajes que recibe en un archivo.

![Arquitectura del ejemplo Hello World generado con el SDK de puerta de enlace de Azure IoT][4]

Como se describe en la sección anterior, el módulo Hello World no pasar mensajes directamente al módulo registrador cada cinco segundos. En su lugar, publica un mensaje en el agente cada cinco segundos.

El módulo registrador recibe el mensaje de broker y actúa sobre ella, escribir el contenido del mensaje en un archivo.

El módulo registrador consume sólo mensajes de los agentes, nunca publica nuevos mensajes al intermediario.

![Cómo el broker enruta mensajes entre los módulos en el SDK de puerta de enlace de Azure IoT][5]

La siguiente figura muestra la arquitectura del ejemplo Hola a todos y las rutas de acceso relativas a los archivos de origen que implementan las distintas partes de la muestra en el [repositorio]de[lnk-gateway-sdk]. Explorar el código por su cuenta o utilizar los fragmentos de código a continuación como guía.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk