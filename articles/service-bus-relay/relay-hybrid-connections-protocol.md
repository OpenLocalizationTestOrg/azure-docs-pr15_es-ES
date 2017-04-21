<properties 
    pageTitle="Protocolo de conexiones de retransmisión Azure híbrido | Microsoft Azure"
    description="Guía de protocolo de retransmisión híbrido conexiones zure."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de conexiones de retransmisión Azure híbrido

Retransmisión de Azure es uno de los pilares capacidad clave de la plataforma de Bus de servicio de Azure. Nueva capacidad de "Híbrido conexiones" de retransmisión es una evolución segura, abrir protocolo basada en HTTP y WebSockets.

Reemplaza la primera, igualmente denominado característica "BizTalk Services" que se creó en una base de protocolo propietario. La integración de híbrido conexiones a servicios de aplicación de Azure continuarán funcionando como-es.

"Híbrido conexiones" permiten establecer la comunicación de secuencia binario bidireccional entre dos aplicaciones en red, según la cual pueden residen una o ambas de las partes detrás de NAT o los Firewalls.

Este documento describe las interacciones del cliente con la retransmisión de conexiones híbrido para conectar clientes en las funciones de detector y remitente y cómo escucha acepta nuevas conexiones.

## <a name="interaction-model"></a>Modelo de interacción

La retransmisión de conexiones híbrido conecta dos partes con un punto de encuentro en la nube de Azure que las partes pueden detectar y conectarse a desde la perspectiva de su propia de la red. Ese punto de reunión se denomina "Híbrido de la conexión" en este y otro documentación en las API y también en el Portal de Azure. El extremo de servicio de conexiones de híbrido se denomina "servicio" para el resto de este documento.

El modelo de interacción se basa en la nomenclatura establecida por otras API de red:

Hay una escucha que primero indica la preparación para controlar las conexiones entrantes y posteriormente acepta como llegan. En el otro lado, hay un cliente de conexión que se conectará la escucha, esperando conexión aceptado para establecer una ruta de acceso de la comunicación bidireccional. "Conectar", "Escuchar", "Aceptar" es los mismos términos que encontrará en la mayoría de socket API.

Cualquier modelo de comunicación retransmitido tiene cualquiera de las partes realizar conexiones salientes hacia un extremo de servicio, que realiza a la "escucha" también un "cliente" en uso coloquiales y también puede provocar otras sobrecargas terminología; la terminología precisa, por tanto, usamos para conexiones híbrido es la siguiente:

Los programas en ambos lados de la conexión se denominan a "cliente", ya que son los clientes al servicio. El cliente que espera y acepta conexiones "escucha" o dice que la función de"escucha". El cliente que inicia una nueva conexión hacia una escucha a través del servicio se denomina "remitente" o "función de remitente".

## <a name="listener-interactions"></a>Interacciones escucha

La escucha tiene cuatro interacciones con el servicio. todos los detalles de alambre se describen más adelante en este documento en la sección referencia.

### <a name="listen"></a>Escuchar

Para indicar que está preparado para el servicio que es una escucha listo para aceptar conexiones, crea una conexión saliente web. El protocolo de enlace de conexión lleva el nombre de una conexión de híbrido configurado en el espacio de nombres de retransmisión y un token de seguridad que dé "Escuchar" derecho en el nombre. Cuando se acepte la socket web por el servicio, el registro y el socket web establecido se mantiene activo como el canal de control de"" para habilitar todas las interacciones subsiguientes. El servicio permite a hasta 25 escucha simultánea en una conexión híbrido. Si hay escucha 2 o más activo, las conexiones entrantes se equilibrada entre ellas en orden aleatorio; no se garantiza la distribución razonable.

### <a name="accept"></a>Aceptar

Cada vez que abre una nueva conexión en el servicio de un remitente, el servicio elija y notificar a uno de los agentes de escucha activas en la conexión híbrido. La notificación se envía a la escucha sobre el canal de control abierto como mensaje JSON que contiene la dirección URL del extremo del socket Web que el oyente debe conectarse para aceptar la conexión.

La dirección URL puede y debe usarse directamente por el oyente sin ningún trabajo adicional; la información codificada solo es válida durante un breve período de tiempo, básicamente para siempre que el remitente está dispuesto a esperar para la conexión esté establecido-to-end, pero hasta un máximo de 30 segundos. La dirección URL sólo puede utilizarse para el intento de una conexión correcta. En cuanto se establece la conexión de socket Web con la dirección URL de la reunión, todas las actividades más en este socket Web se transmite desde y al remitente, sin intervención ni interpretación por el servicio.

### <a name="renew"></a>Renovar 

El token de seguridad que debe usarse para registrar la escucha y mantener el canal de control puede caducar mientras la escucha esté activa. La expiración token no afectará a las conexiones en curso, pero hará que el canal de control quitar el servicio o la justo después de la instantánea de expiración. Gesto de "renovación" es un mensaje JSON que puede enviar la escucha para reemplazar el token asociado con el canal de control, para que se puede mantener el canal de control para períodos ampliados.

### <a name="ping"></a>Ping 

Si el canal de control permanece inactivo durante mucho tiempo, intermediarios en la forma, como la carga equilibradores o NAT pueden quitar la conexión de TCP. Gesto de "ping" evita mediante el envío de una cantidad pequeña de datos en el canal que le recuerda todos los usuarios de la ruta de red que la conexión está pensada para ser activo y también sirve como una prueba liveness el oyente. Si falla, debe tener en cuenta el canal de control inutilizable y la escucha debe volver a conectarse.

## <a name="sender-interaction"></a>Interacción del remitente

El remitente sólo tiene una única interacción con el servicio, que se conecta.

### <a name="connect"></a>Conectar

Gesto de "conectar" abre un socket Web en el servicio, que proporciona el nombre de la conexión híbrido y (opcional, pero necesario de forma predeterminada) conceder permisos "Enviar" de la cadena de consulta de token de seguridad. A continuación, el servicio interactuar con el agente de escucha de la manera en que se describió anteriormente y que el oyente cree una conexión de cita que se van a unir con este socket Web. Después de que se ha aceptado el socket Web, todas las interacciones en el socket Web será, por tanto, con una escucha conectada.

## <a name="interaction-summary"></a>Resumen de interacción

El resultado de este modelo de interacción es que el cliente remitente extrae el protocolo de enlace con un socket Web "limpiar" que se ha conectado a una escucha y que no es necesario más preámbulos o preparación. Esto permite prácticamente en cualquier implementación de cliente de socket Web existente fácilmente aprovechar el servicio de conexiones de híbrido simplemente proporcionando una dirección de URL correctamente diseñada en su nivel de cliente de Web socket.

La conexión de encuentro Socket Web que el oyente obtiene a través de la interacción Aceptar también es limpiar y se puede entregar a cualquier implementación de servidor de socket Web existente con algunas mínima abstracción adicional que distingue entre operaciones de "Aceptar" en escucha de red local de su entorno y las conexiones de híbrido remoto "Aceptar".

## <a name="protocol-reference"></a>Referencia de protocolo

Esta sección describen los detalles de las interacciones de protocolo descritos anteriormente.

Todas las conexiones de socket Web se realizan en el puerto 443 como una actualización de la versión 1.1 de HTTPS, que normalmente se extrae por algunos marco de socket Web o API. La descripción aquí se mantiene implementación neutral, sin sugiriendo un marco concreto.

## <a name="listener-protocol"></a>Protocolo de escucha

El protocolo de escucha consta de los gestos de conexión dos y tres operaciones de mensaje.

### <a name="listener-control-channel-connection"></a>Conexión de canal de escucha control

Se abre el canal de control con la creación de una conexión de socket Web para:

*wss: / / {dirección de espacio de nombres} /* *$servicebus* */* *hybridconnection /**{path}? acción de hc sb =... & id de hc sb =... & token de hc sb =... "*

La *dirección de espacio de nombres* es el nombre de dominio completo del espacio de nombres de retransmisión de Azure que hospeda la conexión híbrido, normalmente del formulario {*minombre}. servicebus.windows.net.*

Las opciones de parámetro de cadena de consulta son los siguientes

| Parámetro        | ¿Obligatorio? | Descripción                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| acción de hc SB | Sí       | El rol de escucha el parámetro debe ser **acción de hc sb = escuchar**                                                                                                                                |
| {path}       | Sí       | La ruta de acceso de URL codificada espacio de nombres de la conexión de híbrido preconfigurado para registrar esta escucha en. Esta expresión se anexa a los * **$servicebus**/**hybridconnection /*** parte de la ruta de acceso. |
| símbolo de hc SB  | Sí\*     | La escucha debe proporcionar un válido, URL codificada servicio Bus compartido acceso Token para la conexión de híbrido que dé derecho **escuchar** o espacio de nombres.                                           |
| Id. de hc de SB     | No        | Este ID opcional proporcionado por el cliente permite llevar a cabo el seguimiento de diagnóstico.                                                                                                                             |

Si se produce un error en la conexión de Web debido a la ruta de acceso de conexión híbrida no está registrado, o un símbolo que faltan o no válido o algún otro error, se proporcionará la información de error utiliza el modelo de comentarios de estado de HTTP 1.1 normal. La descripción del estado contendrá un seguimiento de identificador de error que se puede comunicar a soporte técnico de Azure:

| Código | Error          | Descripción                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | No se encontró      | La **ruta de acceso** de conexión híbrido es válida o la dirección URL base es incorrecto |
| 401  | No autorizado   | El token de seguridad es que faltan o con formato incorrecto o no válidos                  |
| 403  | Prohibido      | El token de seguridad no es válido para esta ruta de acceso para realizar esta acción          |
| 500  | Error interno | Hubo un problema en el servicio                                    |

Si el servicio cierra deliberadamente la conexión Web después de que se ha establecido inicialmente hacia arriba, la razón para hacerlo para que se comunicará con un código de error adecuado de protocolo socket de Web junto con un mensaje de error descriptivo que también incluye un id. El servicio no se cerró el canal de control sin que se produzca una condición de error. Cualquier cierre limpio es cliente controlada.

| Estado de WS | Descripción                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | La ruta de conexión híbrido se ha eliminado o desactivado.                           |
| 1008      | Ha caducado el token de seguridad y, por tanto, se infringe la directiva de autorización. |
| 1011      | Hubo un problema dentro del servicio.                                           |

### <a name="accept-handshake"></a>Aceptar mutuo 

La notificación de aceptación se envía por el servicio a la escucha sobre el canal de control establecido anteriormente como mensaje JSON en un marco de texto de socket de Web. No hay ninguna respuesta a este mensaje.

El mensaje contiene un objeto JSON denominado "Aceptar", que define las siguientes propiedades en este momento:

-   **dirección** : la cadena de URL que se usará para establecer el Socket Web al servicio para aceptar una conexión entrante.

-   **id** : el identificador único para esta conexión. Si el identificador se ha proporcionado por el cliente de remitente, es el valor de remitente proporcionado, en caso contrario, es un valor generado por el sistema.

-   **connectHeaders** : todos los encabezados HTTP proporcionadas al extremo de retransmisión por el remitente, que también incluye el protocolo de WebSocket Sec y los encabezados de extensiones de WebSocket de segundo.

| Aceptar mensaje                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

La dirección URL del mensaje JSON se usa la escucha para establecer el Socket Web para aceptar o rechazar el socket del remitente.

#### <a name="accepting-the-socket"></a>Aceptar el socket

Para aceptar, la escucha establece una conexión de WebSocket a la dirección proporcionada.

Cuenta que para el período de vista previa, la dirección URI puede usar una dirección IP vacíos y el certificado TLS proporcionado por el servidor se producirá un error de validación en esa dirección. Esto se corregirse durante la vista previa.

Si el mensaje "Aceptar" lleva un encabezado "WebSocket Sec de protocolo", se espera que el oyente sólo acepta la WebSocket si es compatible con ese protocolo y que establece el encabezado se establezca el WebSocket.

Lo mismo se aplica al encabezado "Extensiones de WebSocket seg". Si el marco de trabajo es compatible con una extensión, debe establecer el encabezado de la respuesta del lado *servidor* del protocolo de enlace "Extensiones de WebSocket seg" necesario para la extensión.

La dirección URL que debe usarse como-es para establecer el socket Aceptar, pero contiene los parámetros siguientes:

| Parámetro        | ¿Obligatorio? | Descripción                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| acción de hc SB | Sí       | Para aceptar un socket el parámetro debe ser **acción de hc sb = Aceptar**                                                                                                                                                                                                                          |
| {path}       | Sí       | La ruta de acceso de URL codificada espacio de nombres de la conexión de híbrido preconfigurado para registrar esta escucha en. Esta expresión se anexa a los * **$servicebus**/**hybridconnection /*** parte de la ruta de acceso.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| identificador de hc SB | No        | Consulte Descripción de **id** anteriores.                                                                                                                                                                                                                                                              |

Si hay un error, el servicio puede responder como sigue:

| Código | Error          | Descripción                         |
|------|----------------|-------------------------------------|
| 403  | Prohibido      | La dirección URL no es válida.               |
| 500  | Error interno | Hubo un problema en el servicio |

Después de establecer la conexión, el servidor se cerrará el socket Web cuando el socket de Web remitente apaga, o con el estado siguiente

| Estado de WS | Descripción                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | El cliente del remitente cierra la conexión                                        |
| 1001      | La ruta de conexión híbrido se ha eliminado o desactivado.                           |
| 1008      | Ha caducado el token de seguridad y, por tanto, se infringe la directiva de autorización. |
| 1011      | Hubo un problema dentro del servicio.                                           |

#### <a name="rejecting-the-socket"></a>Rechazar el socket

Rechazar el socket después de inspeccionar el mensaje "Aceptar", requiere un enlace similar para que el código de estado y la descripción de estado comunicar el motivo del rechazo pueden flujo al remitente.

La opción de diseño de protocolo es usar un enlace de WebSocket (que está diseñado para que termine en un estado de error definido) para que las implementaciones de cliente de escucha puede continuar con el se basan en un cliente de WebSocket y no es necesario emplear un adicional, el cliente HTTP.

Para rechazar el socket, el cliente toma la dirección URI desde el mensaje "Aceptar" y agrega dos parámetros de cadena de consulta:

| Parámetro             | ¿Obligatorio? | Descripción                             |
|-------------------|-----------|-----------------------------------------|
| statusCode        | Sí       | Código numérico de estado HTTP                |
| statusDescription | Sí       | Motivo legible humano del rechazo |

La URI resultante se utiliza para establecer una conexión de WebSocket; una vez más, recuerde que el certificado TLS puede no coincide con la dirección durante la vista previa, por lo que deba estar deshabilitados validación.

Cuando se complete correctamente, este protocolo de enlace se producirá deliberadamente un código de error HTTP 410, ya que no se ha establecido ningún WebSocket. Si se produce un error, estas son las opciones:

| Código | Error          | Descripción                         |
|------|----------------|-------------------------------------|
| 403  | Prohibido      | La dirección URL no es válida.               |
| 500  | Error interno | Hubo un problema en el servicio |

### <a name="listener-token-renewal"></a>Renovación de token de escucha

Cuando el token de la escucha está a punto de caducar, puede reemplazarlo enviando un mensaje de marco de texto al servicio a través del canal de control establecidos. El mensaje contiene un objeto JSON denominado "renewToken", que define la propiedad siguiente en este momento:

-   **token** válido, URL codificada servicio Bus compartido acceso Token para el espacio de nombres o conexión híbrido que dé derecho **escuchar** .

| renewToken mensaje                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Si falla la validación de token, acceso denegado y servicio de nube cerrará la websocket del canal de control con un error, en caso contrario, no hay ninguna respuesta.

| Estado de WS | Descripción                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | Ha caducado el token de seguridad y, por tanto, se infringe la directiva de autorización. |

<a name="sender-protocol"></a>Protocolo del remitente
---------------

El protocolo de remitente es idéntico al cómo se establece una escucha. El objetivo es la máxima transparencia para el socket de Web-to-end. La dirección para conectarse a es el mismo que el oyente, pero difiere de la "acción" y el token necesita un permiso diferente:

*wss: / / {dirección de espacio de nombres} /* *$servicebus* */* *hybridconnection /**{path}? acción de hc sb =... & id de hc sb =... \[& token de hc sbc =... \]*

La *dirección de espacio de nombres* es el nombre de dominio completo del espacio de nombres de retransmisión de Azure que hospeda la conexión híbrido, normalmente del formulario {*minombre}. servicebus.windows.net.*

La solicitud puede contener arbitrarios encabezados HTTP adicionales, incluidos los definidos por la aplicación. Todos los encabezados suministrados fluyan para la escucha y se pueden encontrar en el objeto "connectHeader" del mensaje de control "Aceptar".

Las opciones de parámetro de cadena de consulta son los siguientes

| Parámetro        | ¿Obligatorio? | Descripción                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| acción de hc SB | Sí       | El rol de escucha el parámetro debe ser **acción = conectar**                                                                                                                                                    |
| {path}       | Sí       | La ruta de acceso de URL codificada espacio de nombres de la conexión de híbrido preconfigurado para registrar esta escucha en.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| símbolo de hc SB | Yes\*     | La escucha debe proporcionar un válido, URL codificada servicio Bus compartido acceso Token para la conexión de híbrido que dé derecho **Enviar** o espacio de nombres.                                                            | | identificador de hc SB | No        | Un identificador opcional que permite llevar a cabo el seguimiento de diagnóstico y estará disponible para la escucha durante el enlace de aceptación.                                                                                       |

Si se produce un error en la conexión de Web debido a la ruta de acceso de conexión híbrida no está registrado, o un símbolo que faltan o no válido o algún otro error, se proporcionará la información de error utiliza el modelo de comentarios de estado de HTTP 1.1 normal. La descripción del estado contendrá un seguimiento de identificador de error que se puede comunicar a soporte técnico de Azure:

| Código | Error          | Descripción                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | No se encontró      | La **ruta de acceso** de conexión híbrido es válida o la dirección URL base es incorrecto |
| 401  | No autorizado   | El token de seguridad es que faltan o con formato incorrecto o no válidos                  |
| 403  | Prohibido      | El token de seguridad no es válido para esta ruta de acceso para realizar esta acción          |
| 500  | Error interno | Hubo un problema en el servicio                                    |

Si el servicio cierra deliberadamente la conexión Web después de que se ha establecido inicialmente hacia arriba, la razón para hacerlo para que se comunicará con un código de error adecuado de protocolo socket de Web junto con un mensaje de error descriptivo que también incluye un id.

| Estado de WS | Descripción                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | La escucha de cerrar el socket.                                                 |
| 1001      | La ruta de conexión híbrido se ha eliminado o desactivado.                           |
| 1008      | Ha caducado el token de seguridad y, por tanto, se infringe la directiva de autorización. |
| 1011      | Hubo un problema dentro del servicio.                                           |

## <a name="next-steps"></a>Pasos siguientes:

- [Retransmisión de preguntas más frecuentes](relay-faq.md)
- [Crear un espacio de nombres](relay-create-namespace-portal.md)
- [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a nodo](relay-hybrid-connections-node-get-started.md)