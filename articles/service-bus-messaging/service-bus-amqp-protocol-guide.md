<properties 
    pageTitle="AMQP 1.0 en Guía de protocolo de Bus de servicio de Azure y Hubs evento | Microsoft Azure" 
    description="Guía de protocolo a expresiones y descripción de AMQP 1.0 en Bus de servicio de Azure y Hubs de evento" 
    services="service-bus,event-hubs" 
    documentationCenter=".net" 
    authors="clemensv" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="07/01/2016"
    ms.author="clemensv;jotaub;hillaryc;sethm"/>

# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 en Guía de protocolo de Bus de servicio de Azure y Hubs de evento

Advanced mensaje cola protocolo 1.0 es un protocolo de tramas y transferencia normalizado para asincrónica, seguridad y confiabilidad transferir mensajes entre dos partes. Es el principal protocolo de mensajería de Bus de servicio de Azure y Azure evento Hubs. Ambos servicios también admiten HTTPS. El protocolo SBMP propietario que también es posible que desaparece en favor AMQP.

AMQP 1.0 es el resultado de colaboración de la industria que reunió proveedores de software intermedio, como Microsoft y sombrero rojo, con muchos usuarios software intermedio mensajería como JP Morgan Chase que representa el sector de servicios financieros. El foro de normalización técnica para las especificaciones de extensión y protocolo AMQP es OASIS y ha logrado aprobación formal como un estándar internacional como ISO/IEC 19494.

## <a name="goals"></a>Objetivos

En este artículo brevemente resume los conceptos básicos de 1.0 AMQP mensajería especificación junto con un conjunto pequeño de borradores de extensión que actualmente está finalizando en el Comité técnico OASIS AMQP y explica cómo Bus de servicio de Azure implementa y se basa en estas especificaciones.

El objetivo es que cualquier programador mediante cualquier pila de cliente AMQP 1.0 existente en cualquier plataforma puedan interactuar con Bus de servicio de Azure a través de AMQP 1.0.

Pilas de propósito general AMQP 1.0 comunes, como Apache protones o AMQP.NET interacción, ya implementan todos los gestos de núcleo AMQP 1.0. Los movimientos básicos a veces se ajustan con un nivel superior API; Apache protones incluso ofrece dos, la API de Messenger imprescindible y las API de Reactor reactiva.

En la siguiente descripción, consideraremos que la administración de conexiones, sesiones y vínculos AMQP y la administración de control de flujo y transferencias de marco se administran por la pila respectiva (por ejemplo, Apache protones-C) y no requieren mucho si cualquier atención específica de los desarrolladores de aplicaciones. Forma abstracta consideraremos la existencia de unos fundamentos de API como la capacidad de conectarse y crear objetos de abstracción de algún tipo de *remitente* y el *destinatario* , lo que tiene alguna forma de `send()` y `receive()` operaciones, respectivamente.

Cuando va a hablar sobre las capacidades avanzadas de Bus de servicio de Azure, como mensaje de exploración o administración de sesiones, los que se explica en términos AMQP, sino también como una implementación de pseudo superpuesta sobre esta abstracción API supuesta.

## <a name="what-is-amqp"></a>¿Qué es AMQP?

AMQP es un protocolo de tramas y transferencia. Tramas significa que proporciona estructura para las secuencias de datos binarios que fluyen en cualquier dirección de una conexión de red. La estructura proporciona delimitación de bloques distintos de datos: marcos: intercambiar entre las partes conectadas. Las capacidades de transferencia Asegúrese de que ambas partes de la comunicación pueden establecer una visión compartida sobre cuándo se transferirán marcos y cuando transferencias deberán considerarse completadas.

A diferencia de anteriormente expirada borradores producidos por el grupo de trabajo de AMQP que están en uso por unos agentes de mensaje, protocolo de AMQP 1.0 final y normalizado de grupo de trabajo no fijar la presencia de un agente de mensaje o cualquier topología concreta entidades dentro de un agente de mensaje.

El protocolo puede utilizarse para la comunicación de punto a punto simétrica, para la interacción con los agentes de mensaje que admiten colas y entidades de publicación o suscripción, como sucede en Bus de servicio de Azure. También se pueden utilizar para la interacción con la infraestructura de mensajería cuando los patrones de interacción son distintos de colas normales, como ocurre con Azure evento Hubs. Un concentrador de evento actúa como una cola cuando se envían eventos, pero actúa más como un servicio de almacenamiento en serie cuando se leen los eventos. algo es similar a una unidad de cinta. El cliente elige un desplazamiento en el flujo de datos disponibles y, a continuación, se sirve todos los eventos de ese desplazamiento a la última fecha disponible.

El protocolo AMQP 1.0 está diseñado para ser extensible, lo que permite la especificaciones mejorar sus capacidades. Las especificaciones de tres extensión que trataremos en este documento se muestra cómo hacerlo. Para la comunicación a través de la infraestructura de WebSockets/HTTPS existente en configuración de puertos TCP AMQP nativos puede resultar difícil, una especificación de enlace define cómo capa AMQP sobre WebSockets. Para interactuar con la infraestructura de mensajería de forma de convocatoria y respuesta para fines de administración o para proporcionar funcionalidades avanzadas, la especificación de administración de AMQP define a los fundamentos de la interacción básica necesarios. Para la integración del modelo de autorización federados, la especificación de seguridad basada en notificaciones AMQP define cómo asociar y renovar tokens autorización asociados con vínculos.

## <a name="basic-amqp-scenarios"></a>Escenarios AMQP básicos

Esta sección explica el uso básico de AMQP 1.0 con Bus de servicio de Azure, que incluye la creación de conexiones, sesiones y vínculos y transferir mensajes a y desde entidades de Bus de servicios como colas, temas y suscripciones.

El origen más relevantes para obtener información sobre cómo funciona AMQP es la especificación AMQP 1.0, pero se ha escrito la especificación exactamente con guía de implementación y no para enseñar el protocolo. En esta sección se centra en Introducción tanto terminología según sea necesario para describir cómo Bus de servicio usa AMQP 1.0. Para una introducción más completa a AMQP, así como una explicación más amplia de AMQP 1.0, puede revisar [este curso de vídeo][].

### <a name="connections-and-sessions"></a>Sesiones y conexiones

![][1]

AMQP llama a los programas comunicar *contenedores*; los contienen *nodos*, que son las entidades de comunicación dentro de los contenedores. Una cola puede ser un nodo secundario. AMQP permite multiplexado, por lo que puede usarse una sola conexión para muchas rutas de comunicación entre nodos; Por ejemplo, un cliente de aplicación simultáneamente puede recibir de una cola y enviar a otra cola con la misma conexión de red.

La conexión de red, por tanto, se ancla en el contenedor. Se inicia el contenedor de la función de cliente realizar una conexión de socket TCP saliente a un contenedor de la función de receptor que escucha y acepta las conexiones entrantes de TCP. El protocolo de enlace de conexión incluye la versión del protocolo, declarar o negociar el uso de la seguridad a nivel de transporte (TLS/SSL) y un protocolo de enlace de autenticación o autorización en el ámbito de la conexión que se basa en SASL.

Bus de servicios de Azure requiere el uso de TLS en todo momento. Es compatible con conexiones en el puerto TCP 5671, según el cual la conexión TCP en primer lugar se superpone con TLS antes de entrar en el enlace del protocolo AMQP y también es compatible con conexiones en el puerto TCP 5672 por el servidor inmediatamente le ofrece una actualización obligatoria de conexión TLS con el modelo previsto AMQP. El enlace de AMQP WebSockets crea un túnel sobre el puerto TCP 443 que, a continuación, es equivalente a AMQP 5671 conexiones.

Después de configurar la conexión y TLS, Bus de servicio ofrece dos opciones de mecanismo SASL:

-   Sin formato SASL se usa generalmente para pasar las credenciales de usuario y contraseña en un servidor. Bus de servicio no tiene cuentas, pero denominado [reglas de seguridad de acceso compartido](service-bus-shared-access-signature-authentication.md), que otorgar derechos y están asociadas a una clave. El nombre de una regla se usa como el nombre de usuario y la clave (como texto codificado de base 64) se utiliza como la contraseña. Los derechos asociados con la regla elegida rigen las operaciones que se permiten en la conexión.

-   SASL anónimo se utiliza para omitir la autorización de SASL cuando desee utilizar el modelo de seguridad basada en notificaciones (CBS) que se describe más adelante. Con esta opción, puede establecerse a una conexión de cliente anónimamente durante un tiempo durante el que el cliente sólo puede interactuar con el extremo CBS y el protocolo de enlace CBS debe completar.

Después de establece la conexión de transporte, los contenedores declaran el tamaño máximo de trama están dispuestos controlar y después de que espera unilateralmente deberá desconectar el si no hay ninguna actividad en la conexión.

También declare cuántos canales simultáneos son compatibles. Un canal es una ruta de transferencia unidireccional, saliente, virtual encima de la conexión. Una sesión de toma de un canal de cada uno de los contenedores interconectados para formar una trayectoria de la comunicación bidireccional.

Sesiones tienen un modelo de control de flujo basado en la ventana; Cuando se crea una sesión, cada parte declara cuántos marcos está dispuesto a Aceptar en la ventana de recepción. Como las tramas de exchange partes, relleno de marcos transferido ventana y transferencias detener cuando se completa la ventana y hasta que se restablecer o expandida usa el *flujo de performative* (*performative* es el término AMQP para intercambiar entre las dos partes de gestos de nivel de protocolo).

Este modelo basado en la ventana equivale aproximadamente el concepto de control de flujo basado en la ventana, pero en el nivel de sesión dentro del socket TCP. Concepto de protocolo de lo que permite varias sesiones simultáneas existe para que el tráfico de alta prioridad podría prisa antiguas limitado el tráfico normal, como en una calle express de autopista.

Bus de servicios de Azure utiliza actualmente exactamente una sesión para cada conexión. El tamaño máximo de marco de Bus de servicio es 262.144 bytes (256K bytes) para el estándar de Bus de servicio y Hubs de evento. Es 1.048.576 (1 MB) de servicio Bus Premium. Bus de servicio imponer las ventanas de limitación de nivel de sesión determinadas, pero reinicia la ventana con regularidad como parte del control de flujo de nivel de vínculo (consulte [la sección siguiente](#links)).

Conexiones, canales y sesiones son efímeras. Si la conexión subyacente contrae, conexiones, se deben restablecer túnel TLS, contexto de autorización de SASL y sesiones.

### <a name="links"></a>Vínculos

![][2]

AMQP transfiere mensajes a través de vínculos. Un vínculo es una ruta de comunicación creada en una sesión que permite transferir mensajes en un sentido; la negociación de estado de transferencia es sobre el vínculo y bidireccional entre las partes conectadas.

Cualquier contenedor pueden crear vínculos en cualquier momento y a través de una sesión existente, lo que hace AMQP diferente de muchos otros protocolos, incluidos HTTP y MQTT, donde la apertura de transferencias y la ruta de transferencia es un privilegio exclusivo de la parte de la creación de la conexión.

El contenedor iniciar vínculo pregunta al contenedor opuesto para aceptar un vínculo y elige una función de remitente o destinatario. Por lo tanto, ya sea contenedor puede iniciar creación unidireccional o rutas de acceso de la comunicación bidireccional con el último modelan como pares de vínculos.

Vínculos son denominados y asociados con nodos. Como se indicó al principio, nodos son las entidades de comunicación dentro de un contenedor.

En Bus de servicio de Azure, un nodo es directamente equivalente a una cola, un tema, una suscripción o una subcarpetas de la cola de una cola o de suscripción. El nombre de nodo utilizado en AMQP, por tanto, es el nombre relativo de la entidad dentro del espacio de nombres de Bus de servicio. Si una cola se denomina **myqueue**, es el nombre de nodo AMQP. Una suscripción de tema sigue la convención de la API HTTP por la que se ordenan en una colección de recursos "suscripciones" y por tanto, una suscripción **sub** o un tema **mytopic** tiene el nombre de nodo AMQP **mytopic, suscripciones/sub**.

El cliente que se conecta también es necesario para usar un nombre de nodo local para crear vínculos; Bus de servicio no es normativas sobre los nombres de nodo y no los interpretará. Pilas de cliente AMQP 1.0 generalmente utilizan un esquema para garantizar que los nombres de nodo efímeros son únicos en el ámbito del cliente.

### <a name="transfers"></a>Transferencias

![][3]

Una vez que se ha establecido un vínculo, pueden transferir mensajes sobre dicho vínculo. En AMQP, se ejecuta una transferencia con un gesto de protocolo explícitas (la *transferencia* performative) que mueve un mensaje del remitente al receptor sobre un vínculo. Una transferencia es completada al que se "liquidar", lo que significa que ambas partes han establecido una visión compartida del resultado de esa transferencia.

En el caso más sencillo, puede elegir el remitente enviar mensajes "previamente liquidados", lo que significa que el cliente no está interesado en el resultado y el receptor no proporcionará los comentarios sobre el resultado de la operación. Este modo es compatible con Bus de servicio de Azure en el nivel de protocolo AMQP, pero no se exponen en cualquiera de la API de cliente.

El caso normal es que los mensajes se envían sin liquidar y, a continuación, indica el receptor aceptación o rechazo mediante la *disposición* performative. Rechazo se produce cuando el receptor no puede aceptar el mensaje por cualquier motivo, y el mensaje de rechazo contiene información sobre el motivo, que es una estructura de error definida por AMQP. Si se rechazarán mensajes debido a errores internos dentro de Azure Bus de servicio, el servicio devuelve información adicional dentro de esa estructura que puede usarse para proporcionar sugerencias de diagnósticos al personal de soporte técnico si archiva las solicitudes de soporte técnico. Puede aprender más detalles acerca de los errores más adelante.

Una forma especial de rechazo es el estado *publicado* , que indica que el destinatario tiene ninguna técnica objeciones la transferencia, pero también ningún interés en liquidar a la transferencia. Que caso existe, por ejemplo, cuando un mensaje se ha entregado a un cliente de Bus de servicio y, a continuación, el cliente elige el mensaje "abandonar" porque no se puede realizar el trabajo es el resultado de procesamiento del mensaje mientras la entrega del mensaje no es defectuoso. Una variación de dicho estado es el estado *modificado* , que permite realizar cambios en el mensaje como su publicación. Bus de servicio no utiliza ese estado en este momento.

La especificación de AMQP 1.0 define un más disposición estado *recibido* que específicamente ayuda a administrar recuperación de vínculo. Recuperación de vínculo permite reconstituir el estado de un vínculo y pendiente entregas encima de una nueva conexión y la sesión, cuando se pierde la conexión anterior y la sesión.

Azure Bus de servicio no admite la recuperación de vínculo; Si el cliente pierde la conexión al servicio Bus con un mensaje sin liquidar transferir pendiente, se pierde la transferencia de mensajes y el cliente debe volver a conectar, restablezca el vínculo y vuelva a intentar a la transferencia.

Por lo tanto, Bus de servicio de Azure y Hubs evento admiten "al menos una vez" transferencias donde puede estar seguro el remitente del mensaje que haya almacenado y aceptado, pero no admite "exactamente una vez" transferencias en el nivel AMQP, donde el sistema intentará recuperar el vínculo y continuar negociar el estado de entrega para evitar la duplicación de la transferencia del mensaje.

Para compensar envía posible duplicado Bus de servicio de Azure es compatible con la detección de duplicados como una característica opcional colas y temas. Registros de detección de duplicados el mensaje identificadores de todos los mensajes entrantes durante un tiempo definidas por el usuario y colocar automáticamente todos los mensajes enviados con los mismos identificadores de mensaje durante ese mismo período.

### <a name="flow-control"></a>Control de flujo

![][4]

Además del modelo de control de flujo de nivel de sesión se indicó, cada vínculo tiene su propio modelo de flujo de control. Control de flujo de nivel de sesión protege el contenedor de tener que controlar demasiados marcos en una vez que el control de flujo de nivel de vínculo coloca la aplicación responsable de la cantidad de mensajes que desea controlar desde un vínculo y cuando.

En un vínculo de transferencias sólo pueden ocurrir cuando el remitente tiene suficiente "crédito vincular". Vínculo crédito es un contador establecido por el receptor con el *flujo* performative, cuyo ámbito es un vínculo. Cuando y mientras el remitente está asignado vínculo crédito, intentará utilizar ese crédito por la entrega de mensajes. Cada disminuye de entrega del mensaje el vínculo pendiente de crédito en uno. Cuando se utiliza el crédito vínculo hacia arriba, dejar las entregas.

Cuando está Bus de servicio de la función de receptor al instante proporcionará remitente con crédito vínculo suficientes para que se pueden enviar mensajes inmediatamente. Como se utiliza el vínculo crédito, Bus de servicio ocasionalmente enviará un *flujo* performative al remitente para actualizar el saldo de vínculo.

En la función de remitente, Bus de servicio rápidamente enviará mensajes usar cualquier crédito vínculo pendientes.

Una llamada "recibir" en el nivel de la API se convierte en un *flujo de* performative enviado a Bus de servicio del cliente y Bus de servicio va a utilizar este crédito tomando el primer mensaje desbloqueado, disponible en la cola, bloquearlo y transferirla. Si no hay ningún mensaje disponible para su entrega, cualquier crédito pendientes por cualquier vínculo establece con que entidad determinada permanecerán registrado en orden de llegada y los mensajes se bloqueado y se transfieren a medida que estén disponibles para usar cualquier crédito pendientes.

El bloqueo en un mensaje se libera cuando la transferencia se liquida en uno de los estados terminales *aceptado*, *rechazado*o *publicado*. El mensaje se quita de Bus de servicio cuando el estado de terminal es *aceptado*. Permanece en Bus de servicio y se entregarán al destinatario siguiente cuando llegue a la transferencia de cualquiera de los otros Estados. Bus de servicio automáticamente mover los mensajes en la cola de la entidad cuando llega el recuento de entrega máximo permitido para la entidad debido a repetidos rechazos o versiones.

Aunque las API de Bus de servicio oficial no exponen directamente dicha opción hoy, un cliente de protocolo de nivel inferior AMQP puede utilizar el modelo de crédito vínculo para activar la interacción "estilo de extracción" de emitir una unidad de crédito para cada solicitud de recepción en un modelo de "estilo de inserción" al emitir un gran número de créditos de vínculo y, a continuación, recibir mensajes cuando estén disponibles sin otras interacciones. Inserción es compatible a través de la configuración de la propiedad [MessagingFactory.PrefetchCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.prefetchcount.aspx) o [MessageReceiver.PrefetchCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.prefetchcount.aspx) . Cuando se les distinto de cero, el cliente AMQP usa como el crédito de vínculo.

En este contexto es importante comprender que el reloj de la fecha de vencimiento el bloqueo en el mensaje dentro de la entidad comienza cuando el mensaje se toma de la entidad y no cuando el mensaje se que se coloca en la conexión. Siempre que el cliente indica que está preparado para recibir mensajes mediante el uso del vínculo crédito, por lo tanto, se espera conocidas se extraen mensajes a través de la red y estar preparado para solucionarlos. En caso contrario, el bloqueo de mensaje puede que haya caducado antes de que el mensaje se ha entregado incluso. El uso de control de flujo de vínculo crédito debe reflejar directamente la preparación inmediata tratar disponibles mensajes enviados al destinatario.

En resumen, las secciones siguientes proporcionan una introducción esquemática del flujo de performative durante las interacciones de API diferentes. Cada sección describe una operación lógica diferente. Algunas de esas interacciones pueden ser "lento", lo que significa que solo se puede realizar una vez necesarios. Creación de un remitente del mensaje no puede causar una interacción red hasta que se envíe ni se solicita el primer mensaje.

Las flechas muestran la dirección del flujo de performative.

#### <a name="create-message-receiver"></a>Crear el receptor de mensaje

| Cliente                                                                                                                                                | Bus de servicio                                                                                                                                   |
|---------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------   |
| --> Adjuntar ()<br/>nombre = {nombre de vínculo}<br/>controlar = {controlador numérico,}<br/>función =**receptor**,<br/>origen = {nombre de la entidad,}<br/>destino = {identificador de vínculo de cliente}<br/>)         | Cliente se conecta a entidad como receptor                                                                                                         |
| Respuestas de Bus de servicio adjuntando el final del vínculo                                                                                                     | <--adjuntar ()<br/>nombre = {nombre de vínculo}<br/>controlar = {controlador numérico,}<br/>función =**remitente**,<br/>origen = {nombre de la entidad,}<br/>destino = {identificador de vínculo de cliente}<br/>)       |

#### <a name="create-message-sender"></a>Crear el remitente del mensaje

| Cliente                                                                                                            | Bus de servicio                                                                                                           |
|------------------------------------------------------------------------------------------------------------------ |--------------------------------------------------------------------------------------------------------------------   |
| --> Adjuntar ()<br/>nombre = {nombre de vínculo}<br/>controlar = {controlador numérico,}<br/>función =**remitente**,<br/>origen = {identificador de vínculo de cliente,}<br/>destino = {nombre de la entidad}<br/>)   | Ninguna acción                                                                                                                     |
| Ninguna acción                                                                                                                 | <--adjuntar ()<br/>nombre = {nombre de vínculo}<br/>controlar = {controlador numérico,}<br/>función =**receptor**,<br/>origen = {identificador de vínculo de cliente,}<br/>destino = {nombre de la entidad}<br/>)     |

#### <a name="create-message-sender-error"></a>Crear el remitente del mensaje (Error)

| Cliente                                                                                                            | Bus de servicio                                                           |
|------------------------------------------------------------------------------------------------------------------ |---------------------------------------------------------------------  |
| --> Adjuntar ()<br/>nombre = {nombre de vínculo}<br/>controlar = {controlador numérico,}<br/>función =**remitente**,<br/>origen = {identificador de vínculo de cliente,}<br/>destino = {nombre de la entidad}<br/>)   | Ninguna acción                                                                     |
| Ninguna acción                                                                                                                 | <--adjuntar ()<br/>nombre = {nombre de vínculo}<br/>controlar = {controlador numérico,}<br/>función =**receptor**,<br/>origen = null,<br/>destino = null<br/>)<br/><br/><--desasociar ()<br/>controlar = {controlador numérico,}<br/>cerrado =**Verdadero**,<br/>error = {información de error de}<br/>)  |

#### <a name="close-message-receiversender"></a>Cerrar el mensaje receptor/remitente

| Cliente                                            | Bus de servicio                                       |
|-------------------------------------------------  |-------------------------------------------------  |
| --> Desasociar ()<br/>controlar = {controlador numérico,}<br/>cerrado =**true**<br/>)    | Ninguna acción                                                 |
| Ninguna acción                                                 | <--desasociar ()<br/>controlar = {controlador numérico,}<br/>cerrado =**true**<br/>)    |

#### <a name="send-success"></a>Enviar (correcto)

| Cliente                                                                                                                        | Bus de servicio                                                                                           |
|------------------------------------------------------------------------------------------------------------------------------ |------------------------------------------------------------------------------------------------------ |
| --> (transferencia<br/>id de entrega = {controlador numérico,}<br/>etiqueta de entrega = {identificador binario}<br/>liquidar =**false**,, más =**false**,<br/>estado =**null**,<br/>reanudar =**false**<br/>)   | Ninguna acción                                                                                                     |
| Ninguna acción                                                                                                                             | <--(disposición<br/>función = receptor,<br/>en primer lugar = {id de entrega}<br/>última = {id de entrega}<br/>liquidar =**Verdadero**,<br/>estado =**aceptado**<br/>)   |

#### <a name="send-error"></a>Enviar (Error)

| Cliente                                                                                                                        | Bus de servicio                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------ |-----------------------------------------------------------------------------------------------------------------------------  |
| --> (transferencia<br/>id de entrega = {controlador numérico,}<br/>etiqueta de entrega = {identificador binario}<br/>liquidar =**false**,, más =**false**,<br/>estado =**null**,<br/>reanudar =**false**<br/>)   | Ninguna acción                                                                                                                             |
| Ninguna acción                                                                                                                             | <--(disposición<br/>función = receptor,<br/>en primer lugar = {id de entrega}<br/>última = {id de entrega}<br/>liquidar =**Verdadero**,<br/>estado = (**rechazado**<br/>error = {información de error de}<br/>)<br/>)     |

#### <a name="receive"></a>Recibir

| Cliente                                                                                                | Bus de servicio                                                                                                                   |
|------------------------------------------------------------------------------------------------------ |------------------------------------------------------------------------------------------------------------------------------ |
| --> (de flujo<br/>vínculo crédito = 1<br/>)                                                                                 | Ninguna acción                                                                                                                             |
| Ninguna acción                                                                                                     | < transferir ()<br/>id de entrega = {controlador numérico,}<br/>etiqueta de entrega = {identificador binario}<br/>liquidar =**false**,<br/>más =**false**,<br/>estado =**null**,<br/>reanudar =**false**<br/>)     |
| --> (disposición<br/>función =**receptor**,<br/>en primer lugar = {id de entrega}<br/>última = {id de entrega}<br/>liquidar =**Verdadero**,<br/>estado =**aceptado**<br/>)   | Ninguna acción                                                                                                                             |

#### <a name="multi-message-receive"></a>Recepción de mensajes de múltiples

| Cliente                                                                                                    | Bus de servicio                                                                                                                       |
|--------------------------------------------------------------------------------------------------------   |--------------------------------------------------------------------------------------------------------------------------------   |
| --> (de flujo<br/>vínculo crédito = 3<br/>)                                                                                 | Ninguna acción                                                                                                                                 |
| Ninguna acción                                                                                                         | < transferir ()<br/>id de entrega = {controlador numérico,}<br/>etiqueta de entrega = {identificador binario}<br/>liquidar =**false**,<br/>más =**false**,<br/>estado =**null**,<br/>reanudar =**false**<br/>)     |
| Ninguna acción                                                                                                         | < transferir ()<br/>id de entrega = {controlador numérico + 1},<br/>etiqueta de entrega = {identificador binario}<br/>liquidar =**false**,<br/>más =**false**,<br/>estado =**null**,<br/>reanudar =**false**<br/>)   |
| Ninguna acción                                                                                                         | < transferir ()<br/>id de entrega = {controlador numérico + 2},<br/>etiqueta de entrega = {identificador binario}<br/>liquidar =**false**,<br/>más =**false**,<br/>estado =**null**,<br/>reanudar =**false**<br/>)   |
| --> (disposición<br/>función = receptor,<br/>en primer lugar = {id de entrega}<br/>última = {id de entrega + 2},<br/>liquidar =**Verdadero**,<br/>estado =**aceptado**<br/>)     | Ninguna acción                                                                                                                                 |

### <a name="messages"></a>Mensajes

Las siguientes secciones explican las propiedades de las secciones de mensaje AMQP estándares utilizadas por Bus de servicio y cómo se asignan a las API de Bus de servicio oficial.

#### <a name="header"></a>encabezado

| Nombre de campo        | Uso                             | Nombre de la API          |
|----------------   |-------------------------------    |---------------    |
| resistentes           | -                                 | -                 |
| prioridad          | -                                 | -                 |
| TTL               | Tiempo de vida para este mensaje     | [Período de vida](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)     |
| primer comprador    | -                                 | -                 |
| recuento de entrega    | -                                 | [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx)   |

#### <a name="properties"></a>propiedades

| Nombre de campo            | Uso                                                                                                                             | Nombre de la API                                      |
|---------------------- |---------------------------------------------------------------------------------------------------------------------------------  |--------------------------------------------   |
| Id. de mensaje            | Identificador de forma libre, definido por la aplicación para este mensaje. Se utiliza para la detección duplicada.                                         | [Id. de mensaje](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)                                   |
| identificador de usuario               | Identificador de usuario definido por la aplicación, no interpretado Bus de servicio.                                                              | No es accesible a través de la API de Bus de servicio.   |
| Para                    | Identificador de destino definido por la aplicación, no interpretado Bus de servicio.                                                       | [Para](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.to.aspx)                                             |
| Asunto               | Identificador de finalidad de mensaje definido por la aplicación, no interpretado Bus de servicio.                                                   | [Etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)                                       |
| responder a              | Indicador de ruta de respuesta definido por la aplicación no interpretado Bus de servicio.                                                         | [Origen](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.replyto.aspx)                                       |
| Id. de correlación        | Identificador de correlación definido por la aplicación, no interpretado Bus de servicio.                                                       | [CorrelationId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.correlationid.aspx)                               |
| tipo de contenido          | Indicador de tipo de contenido definido por la aplicación en el cuerpo, no interpretado Bus de servicio.                                          | [ContentType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx)                                   |
| codificación de contenido      | Definido por la aplicación codificación de contenido de indicador para el cuerpo, no interpretado Bus de servicio.                                      | No es accesible a través de la API de Bus de servicio.   |
| fecha de caducidad absoluta  | Declara en qué absoluto instantánea caducará el mensaje. Omite como entrada (se observa ttl encabezado), relevantes de salida.   | [ExpiresAtUtc](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.expiresatutc.aspx)                                 |
| hora de creación         | Declara momento en que se creó el mensaje. No utiliza Bus de servicio                                                           | No es accesible a través de la API de Bus de servicio.   |
| Id. de grupo              | Identificador definido por la aplicación de un conjunto de mensajes relacionados. Se usa para sesiones de Bus de servicio.                                      | [Id. de sesión](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)                                   |
| secuencia de grupo        | Contador de identificar el número de secuencia relativa del mensaje dentro de una sesión. Omitido por Bus de servicio.                         | No es accesible a través de la API de Bus de servicio.   |
| responder a id de grupo     | -                                                                                                                                 | [ReplyToSessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.replytosessionid.aspx)                             |

## <a name="advanced-service-bus-capabilities"></a>Capacidades avanzadas de Bus de servicio

Esta sección describen las capacidades avanzadas de Bus de servicio de Azure que se basan en las extensiones de borrador AMQP actualmente que se desarrolla en el Comité técnico OASIS para AMQP. Bus de servicios de Azure implementa el último estado de los borradores y adoptará cambios introducidos como los borradores alcance estado estándar.

> [AZURE.NOTE] Bus de servicio de mensajería operaciones avanzadas son compatibles con pensar en un modelo de solicitud y respuesta. Los detalles de estas operaciones se describen en el documento [AMQP 1.0 en Bus de servicio: operaciones basado en solicitud/respuesta](https://msdn.microsoft.com/library/azure/mt727956.aspx).

### <a name="amqp-management"></a>Administración de AMQP

La especificación de administración de AMQP es la primera de las extensiones de borrador que trataremos aquí. Esta especificación define un conjunto de los gestos del protocolo superpuesta el protocolo AMQP que permiten interacciones de administración con la infraestructura de mensajería sobre AMQP. La especificación define operaciones genéricas como *crear*, *leer*, *Actualizar*y *Eliminar* para administrar entidades dentro de una infraestructura de mensajería y un conjunto de operaciones de consulta.

Todos los gestos requieren una interacción de solicitud/respuesta entre el cliente y la infraestructura de mensajería y, por tanto, la especificación define cómo modelar ese modelo de interacción en la parte superior AMQP: el cliente se conecta a la infraestructura de mensajería, inicia una sesión y, a continuación, se crea un par de vínculos. En un vínculo, el cliente actúa como remitente y en el otro actúa como receptor, creando así un par de los vínculos que pueden actuar como un canal bidireccional.

| Operación lógica            | Cliente                      | Bus de servicio                 |
|------------------------------|-----------------------------|-----------------------------|
| Crear solicitud de ruta de acceso de respuesta | --> Adjuntar ()<br/>nombre = {*nombre del vínculo*,}<br/>controlar = {*controlador numérico*},<br/>función =**remitente**,<br/>origen =**null**,<br/>destino = "administración myentity / $"<br/>)                            |Ninguna acción                             |
|Crear solicitud de ruta de acceso de respuesta                              |Ninguna acción                             | \<--Adjuntar ()<br/>nombre = {*nombre del vínculo*,}<br/>controlar = {*controlador numérico*},<br/>función =**receptor**,<br/>origen = null,<br/>destino = "myentity"<br/>)                            |
|Crear solicitud de ruta de acceso de respuesta                              | --> Adjuntar ()<br/>nombre = {*nombre del vínculo*,}<br/>controlar = {*controlador numérico*},<br/>función =**receptor**,<br/>origen = "administración myentity / $",<br/>destino = "id de myclient$"<br/>)                            |                             |Ninguna acción
|Crear solicitud de ruta de acceso de respuesta                              |Ninguna acción                             | \<--Adjuntar ()<br/>nombre = {*nombre del vínculo*,}<br/>controlar = {*controlador numérico*},<br/>función =**remitente**,<br/>origen = "myentity"<br/>destino = "id de myclient$"<br/>)                            |

Tiene que los dos vínculos en su lugar, la implementación de la solicitud y respuesta es sencilla: una solicitud es un mensaje enviado a una entidad dentro de la infraestructura de mensajería que comprende este patrón. En dicho mensaje de solicitud, se establece el campo *respuesta* en la sección *Propiedades* del identificador de *destino* del vínculo en el que se van a entregar la respuesta. La entidad de control se procesar la solicitud y, a continuación, ofrecer la respuesta sobre el vínculo cuyo identificador de *destino* coincide con el identificador indicado *responder a* .

El modelo evidentes requiere que el contenedor de cliente y el identificador generado por el cliente para el destino de respuesta son únicos en todos los clientes y, por motivos de seguridad, también difíciles de predecir.

El intercambio de mensajes utilizado para el protocolo de administración y para todos los demás protocolos que usan el mismo patrón ocurre en el nivel de la aplicación; no definen nuevos movimientos de nivel de protocolo AMQP. Que es intencionado para aplicaciones pueden aprovechar las ventajas inmediato de estas extensiones con compatible con pilas AMQP 1.0.

Bus de servicios de Azure implementar actualmente cualquiera de las características principales de la especificación de administración, pero el modelo de solicitud/respuesta definido por la especificación de administración es fundamental para la característica de seguridad basada en notificaciones y para casi todas las capacidades avanzadas que trataremos en las secciones siguientes.

### <a name="claims-based-authorization"></a>Autorización basada en notificaciones

El borrador de la especificación de autorización en función de reclamaciones AMQP (CBS) se basa en el modelo de solicitud/respuesta de la especificación de administración y describe un modelo generalizado para saber cómo utilizar tokens de seguridad federada con AMQP.

El modelo de seguridad predeterminados de AMQP analizado en la introducción se basa en SASL y se integra con el protocolo de enlace de conexión AMQP. Uso de SASL tiene las ventajas que ofrece un modelo extensible para el que se han definido un conjunto de mecanismos de que cualquier protocolo que anteriormente se basa en SASL puede beneficiarse. Entre esos mecanismos son "PLAIN" para la transferencia de nombres de usuario y contraseñas, "Externas" enlazar al TLS seguridad, "Anónimo" para expresar la ausencia de autenticación y autorización explícita y una amplia variedad de mecanismos adicionales que permiten pasar autenticación o credenciales de autorización o símbolos.

Integración de SASL de AMQP tiene dos inconvenientes:

-   Todas las credenciales y tokens se aplican a la conexión. Una infraestructura de mensajería puede proporcionar control de acceso diferenciado de forma por entidad. Por ejemplo, lo que permite la portador de un símbolo enviar respuestas pero no cola B. Con el contexto de autorización anclado en la conexión, no es posible usar una sola conexión y aún utilice tokens de acceso diferente para respuestas y cola B.

-   Normalmente tokens de acceso solo son válidos por un tiempo limitado. Esto obliga a los usuarios para adquirir periódicamente tokens y proporciona una oportunidad para el emisor de tokens para rechazar emitir un token fresco si han cambiado los permisos de acceso del usuario. Conexiones de AMQP pueden durar muy largos períodos de tiempo. El modelo SASL solo proporciona una oportunidad para establecer un token durante la conexión, lo que significa que la infraestructura de mensajería tiene que desconectar el cliente cuando expira el token o necesita para aceptar el riesgo de que permite una comunicación continua con un cliente que tiene derechos de acceso han sido revocados provisional.

La especificación de AMQP CBS implementada por Bus de servicio de Azure, permite una solución elegante de ambas cuestiones: permite que un cliente asociar tokens de acceso a cada nodo y actualizar esos símbolos antes de que expiren, sin interrumpir el flujo de mensajes.

CBS define un nodo de administración virtual, denominado *$cbs*, que proporciona la infraestructura de mensajería. El nodo Administración acepta tokens en nombre de otros nodos en la infraestructura de mensajería.

Gesto de protocolo es un cambio de convocatoria y respuesta definida por la especificación de administración. Esto significa que el cliente establece un par de vínculos con el nodo *$cbs* y, a continuación, pasa una solicitud en el vínculo de salida y, a continuación, espera la respuesta en el vínculo de entrada.

El mensaje de solicitud tiene las siguientes propiedades de la aplicación:

| Clave        | Opcional | Tipo de valor | Contenido de valor                             |
|------------|----------|------------|--------------------------------------------|
| operación  | No       | cadena     | **Superponer token**                                |
| tipo       | No       | cadena     | El tipo de token que se van a.            |
| nombre       | No       | cadena     | Al que se aplica el token "público". |
| caducidad | Sí      | marca de tiempo  | El tiempo de expiración del token.              |

La propiedad *name* identifica la entidad con la que el token será asociado. En Bus de servicio es la ruta de acceso a la cola o suscripción de tema. La propiedad *tipo* identifica el tipo de token:

| Tipo de token                      | Descripción de token      | Tipo de cuerpo           | Notas                                                    |
|---------------------------------|------------------------|---------------------|----------------------------------------------------------|
| amqp:jwt                        | JSON Web Token (JWT)   | Valor de AMQP (cadena) | No está disponible.  |
| amqp:SWT                        | Simple Web Token (SWT) | Valor de AMQP (cadena) | Solo es compatible con tokens SWT emitidos por AAD/ACS          |
| ServiceBus.Windows.NET:sastoken | Símbolo de SA de Bus de servicio  | Valor de AMQP (cadena) | -                                                        |

Tokens otorgar derechos. Bus de servicio sabe tres derechos fundamentales: "Enviar" permite enviar "Escuchar" permite recibir, y "Administrar" manipular entidades. Tokens SWT emitidos por AAD/ACS explícitamente incluyen esos derechos como notificaciones. Tokens SA de Bus de servicio, consulte reglas configuradas en el espacio de nombres o entidad y las reglas se configuran con derechos. El token de inicio de sesión con la clave asociada a esa regla lo que hace que el token express los derechos correspondientes. El símbolo de una entidad mediante *superponer token* permitirá el cliente conectado para interactuar con la entidad por los derechos de tokens. Un vínculo donde el cliente asume el rol de *remitente* requiere el "Enviar" derecho, asumir la función de *receptor* requiere el derecho "Escuchar".

El mensaje de respuesta tiene los siguientes valores de *Propiedades de la aplicación*

| Clave                | Opcional | Tipo de valor | Contenido de valor                    |
|--------------------|----------|------------|-----------------------------------|
| código de estado        | No       | int        | Código de respuesta HTTP **[RFC2616]**. |
| Descripción de estado | Sí      | cadena     | Descripción del estado.        |

El cliente puede llamar a *token superponer* varias veces y para cualquier entidad en la infraestructura de mensajería. Los símbolos ámbito es cliente actual y anclados en la conexión actual, lo que significa que el servidor quitará cualquier tokens se conservan cuando la conexión se interrumpe.

La implementación actual de Bus de servicio solo permite CBS junto con el método SASL "Anónimo". Una conexión SSL/TLS siempre debe existir antes de la negociación SASL.

El mecanismo anónimo, por tanto, debe ser compatible con el cliente de AMQP 1.0 elegido. Acceso anónimo significa que el protocolo de enlace de la conexión inicial, incluida la creación de la sesión inicial ocurre sin Bus de servicio saber que está creando la conexión.

Una vez establecida la conexión y la sesión, adjuntar los vínculos a la *$cbs* nodo y enviar la solicitud de *token superponer* son los únicos permitido operaciones. Se debe establecer un token válido correctamente con una solicitud de *token superponer* para algún nodo de entidad en 20 segundos después de que se ha establecido la conexión, en caso contrario, la conexión se interrumpe unilateralmente bus de servicio.

El cliente es responsable de posteriormente para realizar un seguimiento de expiración de token. Cuando expira un token, Bus de servicio disminuirá rápidamente todos los vínculos en la conexión a la entidad respectiva. Para evitar esto, el cliente puede reemplazar el token para el nodo con uno nuevo en cualquier momento mediante el nodo de administración de virtual *$cbs* con el mismo gesto de *token superponer* y sin interfiera con el tráfico de carga que fluye en vínculos diferentes.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre AMQP, consulte los siguientes vínculos:

- [Información general de AMQP de Bus de servicio]
- [Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas]
- [AMQP de Bus de servicio de Windows Server]

[Este curso de vídeo]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp/amqp1.png
[2]: ./media/service-bus-amqp/amqp2.png
[3]: ./media/service-bus-amqp/amqp3.png
[4]: ./media/service-bus-amqp/amqp4.png

[Información general de AMQP de Bus de servicio]: service-bus-amqp-overview.md
[Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP de Bus de servicio de Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx