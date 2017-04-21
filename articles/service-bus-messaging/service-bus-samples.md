<properties 
    pageTitle="Información general de ejemplos de Bus de servicios de mensajería | Microsoft Azure"
    description="Clasifica y describe Bus de servicios de mensajería muestras con vínculos a cada uno."
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

# <a name="service-bus-messaging-samples"></a>Ejemplos de mensajería de Bus de servicio

Los ejemplos de mensajería de Bus de servicio muestran las características clave de [Bus de servicios de mensajería](https://azure.microsoft.com/services/service-bus/) (servicio de nube) y [Bus de servicio de Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). En este artículo se clasifica y describe los ejemplos disponibles, con vínculos a cada uno.

>[AZURE.NOTE] Ejemplos de Bus de servicio no se instalan con el SDK. Para obtener los ejemplos, visite la [página de ejemplos de Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Además, hay un conjunto actualizado de Bus de servicios de mensajería ejemplos [aquí](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (al redactar este documento, no se describen en este artículo).  

Para obtener ejemplos de retransmisión, vea [ejemplos de retransmisión de Bus de servicio](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Bus de servicios de mensajería

Los siguientes ejemplos muestran cómo escribir aplicaciones que utilicen mensajería Bus de servicio.

Observe que los ejemplos de mensajería requieren una cadena de conexión para tener acceso a su espacio de nombres de Bus de servicio.

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

### <a name="getting-started-samples"></a>Obtener ejemplos de introducción

Estos ejemplos describen funcionalidad de mensajería básica.

|Ejemplo de nombre|Descripción|Versión mínima SDK|Disponibilidad|
|---|---|---|---|
|[Introducción: Mensajería con colas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Se muestra cómo usar Bus de servicio de Microsoft Azure para enviar y recibir mensajes de una cola.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Introducción: Mensajería con temas](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Se muestra cómo usar Bus de servicio de Microsoft Azure para enviar y recibir mensajes de un tema con varias suscripciones.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|

### <a name="exploring-features"></a>Explorar características

Los siguientes ejemplos muestran diversas características de Bus de servicio.

|Ejemplo de nombre|Descripción|Versión mínima SDK|Disponibilidad|
|---|---|---|---|
|[Proveedores de Token de HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Muestra las distintas maneras de autenticación de un cliente de HTTP/resto con Bus de servicio.|2.1|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Cliente de HTTP de Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Muestra cómo enviar y recibir mensajes de Bus de servicio a través de HTTP/resto.|2.3|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Autorreenvío de Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Se muestra cómo reenviar automáticamente mensajes de una cola, suscripción o la cola a otra cola o tema. También se muestra cómo enviar un mensaje a una cola o un tema a través de una cola de transferencia.|2.3|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con mensajería: Ejemplo de sesión de canal de WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Se muestra cómo usar Bus de servicio de Microsoft Azure mediante canales de Windows Communication Foundation (WCF). El ejemplo muestra el uso de canales WCF para enviar y recibir mensajes a través de una cola de Bus de servicio. El ejemplo muestra sesión y comunicación de sesión no sobre el Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Negociada mensajería: transacciones](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Muestra cómo usar el Bus de servicios de Microsoft Azure funciones dentro de un ámbito de transacción de mensajería para asegurarse de lotes de operaciones de mensajería confirmados atómica.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con la mensajería: Las operaciones de administración con REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Muestra cómo realizar operaciones de administración de Bus de servicio con el resto.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Proveedor de recursos API REST](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Muestra cómo usar las nuevas API de REST de RDFE de Bus de servicio para administrar los espacios de nombres y entidades mensajería.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con mensajería: Ejemplo de sesión de servicio WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Se muestra cómo usar Bus de servicio de Microsoft Azure utiliza el modelo de servicio WCF. El ejemplo muestra el uso del modelo de servicio WCF para llevar a cabo basado en la sesión de comunicación a través de una cola de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Mensajería con: Respuesta de solicitud](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Se muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de solicitud y respuesta. El ejemplo muestra los clientes simples y servidores comunicarse a través de una cola de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con mensajería: cola de mensajes no enviados](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Se muestra cómo usar Bus de servicio de Microsoft Azure y la funcionalidad de mensajería "cola de mensajes no enviados". El ejemplo muestra un simple remitente y el receptor comunicarse a través de una cola de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con la mensajería: Aplazado mensajes](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Muestra cómo usar la característica de aplazamiento de mensaje de Bus de servicio de Microsoft Azure. El ejemplo muestra un simple remitente y el receptor comunicarse a través de una cola de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Mensajería con: Mensajes de la sesión](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Se muestra cómo usar la funcionalidad de sesión de mensajería y Bus de servicio de Microsoft Azure. El ejemplo muestra simples remitentes y receptores comunicarse a través de una cola de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Mensajería con: Tema de respuesta de solicitud](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Muestra cómo implementar el patrón de convocatoria y respuesta con las suscripciones y temas de Bus de servicio de Microsoft Azure. El ejemplo muestra los clientes simples y servidores comunicarse a través de un tema de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Mensajería con: Cola de respuesta de solicitud](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Se muestra cómo usar Bus de servicio de Microsoft Azure y la funcionalidad de solicitud y respuesta. El ejemplo muestra los clientes simples y servidores comunicarse a través de dos colas de Bus de servicio.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con mensajería: Detección de duplicados](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Muestra cómo utilizar la detección de mensajes duplicados de Bus de servicio de Microsoft Azure con colas. Crea dos colas, una con detección de duplicados habilitada y otras otra sin detección de duplicados.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con la mensajería: Mensajería asincrónica](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Se muestra cómo usar Bus de servicio de Microsoft Azure para enviar y recibir mensajes de una cola de forma asincrónica. La cola proporciona comunicación asincrónica desacoplada entre un remitente y cualquier número de receptores (aquí, un receptor único).|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con la mensajería: Filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Se muestra cómo usar Bus de servicio de Microsoft Azure de publicación o suscripción filtros avanzados. Crea un tema y 3 suscripciones con las definiciones de filtro diferentes, envía mensajes al tema y recibe todos los mensajes de suscripciones.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Con mensajería: Búsqueda de mensajes](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Muestra cómo usar la característica de búsqueda de mensajes de Bus de servicio de Microsoft Azure. Se muestra cómo usar la característica de búsqueda de mensajes tras la recepción.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|

## <a name="service-bus-reference-tools"></a>Herramientas de referencia de Bus de servicio

Los siguientes ejemplos muestran diversos otras características del servicio.

|Ejemplo de nombre|Descripción|Versión mínima SDK|Disponibilidad|
|---|---|---|---|
|[Explorador de Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|El Explorador de Bus de servicio permite a los usuarios conectarse a un espacio de nombres de servicio de Bus de servicio y administrar mensajería entidades de forma fácil. La herramienta proporciona características avanzadas como la funcionalidad de importación o exportación y la capacidad para probar entidades de mensajería y servicios de retransmisión.|1,8|Bus de servicio de Microsoft Azure; Bus de servicio de Windows Server|
|[Autorización: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Este ejemplo muestra cómo crear y administrar las identidades de servicio de Microsoft Azure Active Directory Control de acceso (también conocido como servicio de Control de acceso o ACS) para su uso con Bus de servicio.|N/A.|Bus de servicio de Microsoft Azure|

## <a name="next-steps"></a>Pasos siguientes

Vea los temas siguientes para información general conceptual de Bus de servicio.

- [Introducción a mensajería Bus de servicio](service-bus-messaging-overview.md)
- [Arquitectura de Bus de servicio](service-bus-architecture.md)
- [Conceptos básicos de Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
