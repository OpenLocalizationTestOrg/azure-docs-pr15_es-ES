<properties
    pageTitle="Vuelva a intentar la lógica en el SDK de servicios de medios para .NET | Microsoft Azure"
    description="El tema proporciona una descripción general de la lógica de reintento en el SDK de servicios de medios para .NET."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Vuelva a intentar la lógica en el SDK de servicios de medios para .NET

Cuando se trabaja con servicios de Microsoft Azure, pueden producirse errores transitorias. Si se produce un error transitorio, en la mayoría de los casos, después de varios intentos unos operación correcta. El SDK de servicios de medios para .NET implementa la lógica de reintento para tratar errores transitorias asociados con las excepciones y errores que son causados por solicitudes web, ejecutar consultas, guardar los cambios y las operaciones de almacenamiento.  De forma predeterminada, el SDK de servicios de medios para .NET ejecuta cuatro reintentos antes de volver a iniciar la excepción a la aplicación. El código de la aplicación debe controlar esta excepción correctamente.  
  
 La siguiente es una breve guía de directivas de solicitud de Web, almacenamiento, consulta y SaveChanges.  
  
-   La directiva de almacenamiento se usa para las operaciones de almacenamiento de blobs (carga o descarga de archivos de activos).  
  
-   La directiva de solicitud de Web se usa para las solicitudes de web genérico (por ejemplo, para obtener un símbolo de autenticación y la resolución del extremo de clúster de los usuarios).  
  
-   La directiva de consulta se usa para consultar entidades resto (por ejemplo, mediaContext.Assets.Where(...)).  
  
-   La directiva de SaveChanges se usa para hacer nada que cambia los datos en el servicio (por ejemplo, la creación de una actualización de una entidad, llamar a una función de servicio para una operación de entidad).  
  
 Este tema enumeran los tipos de excepción y códigos de error que se administran por el SDK de servicios de medios para .NET lógica de reintento.  
  
## <a name="exception-types"></a>Tipos de excepción  

La siguiente tabla describe las excepciones que el SDK de servicios de medios para .NET controla o no controla para algunas operaciones que pueden causar errores transitorias.  
  
Excepción|Solicitud de Web|Almacenamiento de información|Consulta|SaveChanges
----|------|----|---|---
Excepción<br/>Para obtener más información, consulte la sección [códigos de estado de excepción](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Sí|Sí|Sí|Sí  
DataServiceClientException<br/> Para obtener más información, consulte [códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|No|Sí|Sí|Sí
DataServiceQueryException<br/> Para obtener más información, consulte [códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|No|Sí|Sí|Sí  
DataServiceRequestException<br/> Para obtener más información, consulte [códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|No|Sí|Sí|Sí  
DataServiceTransportException|No|No|Sí|Sí
TimeoutException|Sí|Sí|Sí|No
SocketException|Sí|Sí|Sí|Sí  
StorageException|No|Sí|No|No 
IOException|No|Sí|No|No
  
###  <a name="WebExceptionStatus"></a>Códigos de estado de excepción  

La siguiente tabla muestra los códigos de error de excepción se implementa la lógica de reintento. La enumeración [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define los códigos de estado.  
  
Estado|Solicitud de Web|Almacenamiento de información|Consulta|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|Sí|Sí|Sí|Sí
NameResolutionFailure|Sí|Sí|Sí|Sí  
ProxyNameResolutionFailure|Sí|Sí|Sí|Sí  
SendFailure|Sí|Sí|Sí|Sí
PipelineFailure|Sí|Sí|Sí|No  
ConnectionClosed|Sí|Sí|Sí|No  
KeepAliveFailure|Sí|Sí|Sí|No  
UnknownError|Sí|Sí|Sí|No 
ReceiveFailure|Sí|Sí|Sí|No  
RequestCanceled|Sí|Sí|Sí|No  
Tiempo de espera|Sí|Sí|Sí|No
Error de protocolo <br/>El control de código de estado HTTP controla los intentos de error de protocolo. Para obtener más información, consulte [códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Sí|Sí|Sí|Sí|  
  
###  <a name="HTTPStatusCode"></a>Códigos de estado de error HTTP  

Cuando las operaciones de consulta y SaveChanges inician DataServiceClientException, DataServiceQueryException o DataServiceQueryException, se devuelve el código de estado de error HTTP en la propiedad StatusCode.  La siguiente tabla muestra los códigos de error se implementa la lógica de reintento.  
  
 
Estado|Solicitud de Web|Almacenamiento de información|Consulta|SaveChanges 
---|----|----|----|----
401|No|Sí|No|No
403|No|Sí<br/>Control de reintentos de espera más larga.|No|No  
408|Sí|Sí|Sí|Sí
429|Sí|Sí|Sí|Sí  
500|Sí|Sí|Sí|No  
502|Sí|Sí|Sí|No  
503|Sí|Sí|Sí|Sí  
504|Sí|Sí|Sí|No  
  
Si desea realizar un vistazo a la implementación real del SDK de servicios de medios para .NET lógica de reintento, vea [azure sdk de media services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
