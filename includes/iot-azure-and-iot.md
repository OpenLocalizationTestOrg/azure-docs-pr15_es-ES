
# <a name="azure-and-internet-of-things"></a>Azure e Internet de las cosas

Bienvenido a Microsoft Azure e Internet de las cosas (IoT). Este artículo presenta una arquitectura de solución IoT que describe las características comunes de una solución de IoT que puede implementar utilizando los servicios de Azure. Soluciones de IoT requieren proteger la comunicación bidireccional entre dispositivos, posiblemente la numeración en los millones y un back-end de solución. Por ejemplo, un back-end de solución podría utilizar análisis predictivo, automatizada para descubrir información de la secuencia de eventos de dispositivo a la nube.

Concentrador de Azure IoT es un componente básico clave cuando implemente la arquitectura de la solución IoT utilizando los servicios de Azure. IoT Suite proporciona implementaciones completas end-to-end, de esta arquitectura para escenarios específicos de IoT. Por ejemplo: 

- La solución de *monitoreo remoto* permite supervisar el estado de los dispositivos, como las máquinas expendedoras. 
- La solución de *un mantenimiento predictivo* le ayuda a anticiparse a las necesidades de mantenimiento de dispositivos como bombas en estaciones de bombeo remotas y evitar el downtime no planificado.

## <a name="iot-solution-architecture"></a>Arquitectura de la solución de IoT

El siguiente diagrama muestra una arquitectura de solución típica IoT. El diagrama no incluye los nombres de los servicios de Azure específicos, pero describe los elementos clave en la arquitectura de una solución de IoT genérico. En esta arquitectura, dispositivos IoT recogen datos que se envían a una puerta de enlace de nube. La puerta de enlace de la nube pone los datos disponibles para el procesamiento de otros servicios de fondo desde que se entregan los datos a otras aplicaciones de línea de negocio o a los operadores humanos a través de un escritorio digital u otro dispositivo de presentación.

![Arquitectura de la solución de IoT][img-solution-architecture]

> [AZURE.NOTE] Para obtener información detallada de la arquitectura de IoT, consulte la [Arquitectura de referencia de Microsoft Azure IoT][lnk-refarch].

### <a name="device-connectivity"></a>Conectividad de dispositivos

En esta arquitectura de solución IoT, dispositivos envían telemetría, como lecturas del sensor desde una estación de bombeo, a un extremo de la nube para el almacenamiento y procesamiento. En un escenario de un mantenimiento predictivo, el back-end puede utilizar la secuencia de datos de sensor para determinar cuándo una bomba específica requiere mantenimiento. Los dispositivos también pueden recibir y responder a comandos de dispositivo de nube leyendo mensajes desde un extremo de la nube. Por ejemplo, en el caso de un mantenimiento predictivo el back-end de solución puede enviar comandos a otras bombas en la estación de bombeo para comenzar el redireccionamiento de flujos antes de mantenimiento vence al inicio para asegurarse de que el técnico de mantenimiento puede empezar cuando llega.

Uno de los mayores desafíos que enfrentan los proyectos IoT es cómo fiable y segura, conectar dispositivos al back-end solución. IoT dispositivos tienen diferentes características en comparación con otros clientes como exploradores y aplicaciones móviles. Dispositivos de IoT:

- Suelen ser sistemas incrustados con ningún operador humano.
- Se puede implementar en ubicaciones remotas, donde el acceso físico es caro.
- Sólo pueden ser accesibles a través del fondo de la solución. Hay otra forma de interactuar con el dispositivo.
- Puede haber limitado energía y recursos de procesamiento.
- Puede tener conectividad de red intermitentes, lenta o cara.
- Necesite utilizar protocolos de aplicaciones propietarias, personalizada o específicas de la industria.
- Pueden crearse mediante un amplio conjunto de plataformas de hardware y software más populares.

Además de los requisitos anteriores, cualquier solución IoT debe ofrecer también una escala, seguridad y confiabilidad. El conjunto resultante de los requerimientos de conectividad es difícil y lento implementar mediante tecnologías tradicionales como contenedores web y mensajería a brokers. Concentrador de Azure IoT y el SDK de dispositivos IoT facilitan implementar soluciones que satisfagan estos requisitos.

Un dispositivo puede comunicarse directamente con un extremo de la puerta de enlace de nube o si el dispositivo no puede utilizar cualquiera de los protocolos de comunicaciones que admite la puerta de enlace de nube, se puede conectar a través de una puerta de enlace intermedia. Por ejemplo, la [puerta de enlace de protocolo de Azure IoT] [ lnk-protocol-gateway] pueden realizar la traducción de protocolo si los dispositivos no pueden utilizar cualquiera de los protocolos que admite IoT concentrador.

### <a name="data-processing-and-analytics"></a>Procesamiento de datos y análisis

En la nube, una IoT volver la solución final es donde se produce la mayor parte del procesamiento de datos, como el filtrado y agregación de telemetría y enviarlo a otros servicios. Finalizar la solución IoT atrás:

- Recibe telemetría a escala de los dispositivos y determina cómo procesar y almacenar los datos. 
- Puede permitirle enviar comandos desde la nube a un dispositivo específico.
- Proporciona funciones de registro del dispositivo que permiten a los dispositivos de provisión y controlar qué dispositivos pueden conectarse a la infraestructura.
- Permite realizar un seguimiento del estado de los dispositivos y supervisar sus actividades.

En el caso de un mantenimiento predictivo, el back-end de solución almacena los datos de telemetría históricos. El back-end puede utilizar estos datos para identificar patrones que indican mantenimiento vence en una bomba específica.

Soluciones de IoT pueden incluir bucles de realimentación automática. Por ejemplo, puede identificar un módulo de análisis en el back-end de telemetría que la temperatura de un dispositivo en particular está por encima de los niveles normales de funcionamiento. La solución puede enviar un comando al dispositivo, indicándole que tomar medidas correctivas.

### <a name="presentation-and-business-connectivity"></a>Conectividad de presentación y del negocio

La capa de presentación y negocio conectividad permite a los usuarios finales interactuar con la solución IoT y los dispositivos. Permite a los usuarios ver y analizar los datos recopilados desde sus dispositivos. Estas vistas pueden tomar la forma de paneles o informes de BI que pueden mostrar los datos históricos o cerca de los datos en tiempo real. Por ejemplo, un operador puede comprobar el estado de la estación de bombeo concreto y ver cualquier alertas generadas por el sistema. Esta capa también permite la integración de la IoT solución back-end con las aplicaciones existentes de línea de negocio que vinculan a los procesos empresariales o flujos de trabajo. Por ejemplo, la solución de un mantenimiento predictivo puede integrarse con un sistema de programación que los libros de un ingeniero para visitar una estación de bombeo cuando la solución identifica una bomba necesitan mantenimiento.

![Panel de solución IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
