<properties 
    pageTitle="Información general de ejemplos de retransmisión de Bus de servicio | Microsoft Azure"
    description="Clasifica y describe ejemplos de retransmisión de Bus de servicio con vínculos a cada uno."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-samples"></a>Ejemplos de retransmisión de Bus de servicio

Los ejemplos de retransmisión de Bus de servicio muestran las características clave de [retransmisión de Bus de servicio](https://azure.microsoft.com/services/service-bus/). En este artículo se clasifica y describe los ejemplos disponibles, con vínculos a cada uno.

>[AZURE.NOTE] Ejemplos de Bus de servicio no se instalan con el SDK. Para obtener los ejemplos, visite la [página de ejemplos de Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Además, hay un conjunto actualizado de ejemplos de retransmisión de Bus de servicio [aquí](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (al redactar este documento, no se describen en este artículo).  

Para obtener ejemplos de mensajería, vea [ejemplos de Bus de servicios de mensajería](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Retransmisión de Bus de servicio

Los siguientes ejemplos muestran cómo escribir aplicaciones que utilizan el servicio de retransmisión de Bus de servicio.

Tenga en cuenta que los ejemplos de retransmisión requieren una cadena de conexión para tener acceso a su espacio de nombres de Bus de servicio.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Para obtener una cadena de conexión de Bus de servicio de Azure

1. Inicie sesión en el [portal de Azure](http://portal.azure.com).

1. En la columna izquierda, haga clic en **Bus de servicio**.

1. Haga clic en el nombre del espacio de nombres en la lista.

3. En el módulo de espacio de nombres, haga clic en **directivas de acceso compartido**.

4. En el módulo de **directivas de acceso compartido** , haga clic en **RootManageSharedAccessKey**.

6. Copie la cadena de conexión en el Portapapeles.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Para obtener una cadena de conexión de servicio de Bus de Windows Server

1. Ejecute el siguiente cmdlet de PowerShell:

    ```
    get-sbClientConfiguration
    ```

2. Pegue la cadena de conexión en el archivo App.config de la muestra.

## <a name="service-bus-relay"></a>Retransmisión de Bus de servicio

Ejemplos que muestran la retransmisión de Bus de servicio.

### <a name="getting-started"></a>Introducción

|Ejemplo de nombre|Descripción|Versión mínima SDK|Disponibilidad|
|---|---|---|---|
|[Retransmisión mensajería: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Muestra cómo ejecutar un cliente de Bus de servicio y el servicio en Azure. En este ejemplo se configura mediante programación Bus de servicio. Información de seguridad y el entorno sola se almacena en los archivos de configuración.|1,8|Bus de servicio de Microsoft Azure|
|[Autenticación de mensajería retransmitida: Secreto compartido](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Se muestra cómo usar un nombre del emisor y el secreto de emisor para autenticar con Bus de servicio.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmisión autenticación mensajería: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Muestra cómo exponer un servicio HTTP que requieren autenticación de usuario del cliente.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmisión enlaces mensajería: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Muestra cómo utilizar el enlace **WebHttpRelayBinding** para devolver datos binarios mediante el modelo de programación de Web.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitidos enlaces mensajería: NetTcp retransmitido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Se muestra cómo usar el enlace **NetTcpRelayBinding** .|1,8|Bus de servicio de Microsoft Azure|

### <a name="exploring-features"></a>Explorar características

Ejemplos que muestran diversas características de retransmisión de Bus de servicio.

|Ejemplo de nombre|Descripción|Versión mínima SDK|Disponibilidad|
|---|---|---|---|
|[Autenticación de mensajería retransmitida: WebToken Simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Se muestra cómo usar una credencial de token web simple para autenticar con Bus de servicio. El ejemplo es similar al ejemplo eco, con algunos cambios. Más concretamente, en este ejemplo se agrega un comportamiento en las aplicaciones de ChannelFactory (cliente) y ServiceHost (servicio).|1,8|Bus de servicio de Microsoft Azure|
|[Mensajería retransmitido: Equilibrio de carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Muestra cómo usar Bus de servicio de Microsoft Azure para enrutar los mensajes a varios receptores. Muestra varias instancias de un servicio sencillo comunicarse con un cliente mediante el enlace de **NetTcpRelayBinding**|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitidos enlaces mensajería: Eventos de red](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Muestra cómo utilizar el enlace de **NetEventRelayBinding** bus de servicio de Microsoft Azure.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitido enlaces mensajería: WS2007Http sesión](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Muestra cómo utilizar el enlace de **WS2007HttpRelayBinding** con las sesiones confiables habilitadas. También se muestra cómo especificar credenciales de Bus de servicio en el archivo de configuración en lugar de mediante programación.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitido enlaces mensajería: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Se muestra cómo usar el enlace de **WS2007HttpRelayBinding** con la seguridad de los mensajes para proteger los mensajes de llevar a cabo mientras sigue que requieren los clientes autenticar con Bus de servicio.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitido mensajería: Intercambio de metadatos](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Muestra cómo exponer un extremo de metadatos que usa el enlace de retransmisión. **MetadataExchange** es compatible con los siguientes enlaces de retransmisión: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**y **WS2007HttpRelayBinding**.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitidos enlaces mensajería: Directa de NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Muestra cómo configurar el enlace **NetTcpRelayBinding** para el modo de conexión **híbrido** que primero establece una conexión retransmitida y, si es posible, se cambia automáticamente a una conexión directa entre un cliente y un servicio de soporte técnico.|1,8|Bus de servicio de Microsoft Azure|
|[Enlaces mensajería retransmitidos: Nombre de usuario NetTcp MsgSec](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Muestra cómo usar el enlace de **NetTcpRelayBinding** con la seguridad de los mensajes.|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitido enlaces mensajería: Neto unidireccional](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Muestra cómo exponer y consumir un extremo de servicio mediante el enlace **NetOnewayRelayBinding** .|1,8|Bus de servicio de Microsoft Azure|
|[Retransmitido enlaces mensajería: WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Muestra cómo utilizar el enlace **WS2007HttpRelayBinding** . Muestra un servicio simple que usa no hay opciones de seguridad y no requiere que los clientes para autenticar.|1,8|Bus de servicio de Microsoft Azure|

## <a name="next-steps"></a>Pasos siguientes

Vea los temas siguientes para información general conceptual de Bus de servicio.

- [Información general de retransmisión de Bus de servicio](service-bus-relay-overview.md)
- [Arquitectura de Bus de servicio](../service-bus-messaging/service-bus-architecture.md)
- [Conceptos básicos de Bus de servicio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)