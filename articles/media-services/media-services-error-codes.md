<properties
    pageTitle="Códigos de error de Azure Media Services | Microsoft Azure"
    description="El tema ofrece una descripción general de los códigos de error de Azure Media Services."
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

# <a name="azure-media-services-error-codes"></a>Códigos de error de Azure Media Services

Cuando se usa servicios de Microsoft Azure Media, puede recibir los códigos de error HTTP del servicio según problemas como símbolos de autenticación que se va a expirar a las acciones que no son compatibles con servicios de medios. A continuación se muestra una lista de **códigos de error HTTP** que se pueden devolver Media Services y las posibles causas para ellos.  
  
## <a name="400-bad-request"></a>400 Solicitud incorrecta

La solicitud contiene información no válida y rechazada debido a uno de los motivos siguientes:

- Se especifica una versión no compatible de API. Para la versión más reciente, consulte [configuración de desarrollo de API de REST de servicios de multimedia](media-services-rest-how-to-use.md).
- No se especifica la versión de la API de Media Services. Para obtener información sobre cómo especificar la versión de la API, vea [Conectar con Media Services con la API de REST de servicios de medios](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Si está utilizando el .NET o el SDK de Java para conectarse a servicios de medios, se especifica la versión de la API automáticamente cada vez que intente y realice alguna acción contra Media Services.
- Se ha especificado una propiedad sin definir. Es el nombre de propiedad en el mensaje de error. Pueden especificar sólo las propiedades que son miembros de una entidad dada. Para obtener una lista de entidades y sus propiedades, vea [Referencia de la API de REST de Azure Media Services](http://msdn.microsoft.com/library/azure/hh973617.aspx) .
- Se ha especificado un valor de propiedad no válido. Es el nombre de propiedad en el mensaje de error. Consulte el vínculo anterior para tipos de propiedad válidos y sus valores.
- Falta un valor de propiedad y si es necesario.
- Parte de la dirección URL especificada contiene un valor incorrecto.
- Se intentó actualizar una propiedad WriteOnce.
- Se ha intentado crear un trabajo que tiene un activo de entrada con un AssetFile principal que no se ha especificado o no se pudo determinar.
- Se intentó actualizar un localizador SA. Localizadores SA solo pueden crearse o eliminados. Transmisión localizadores se puede actualizar. Para obtener más información, vea [localizadores](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- Se ha enviado una operación no admitida o la consulta. 

## <a name="401-unauthorized"></a>401 no autorizado

No se pudo autenticar la solicitud (antes de que se pueden autorizar) debido a uno de los motivos siguientes:

- Falta el encabezado de autenticación.
- Valor de encabezado de autenticación incorrecta.
    - El token ha expirado. Si usa las API de REST directamente, vea [Conectar con Media Services con la API de REST de servicios de medios](media-services-rest-connect_programmatically.md) para aprender a generar un nuevo token de autenticación. Si está utilizando la .NET o el SDK de Java, cree un objeto CloudMediaContext o MediaContract para generar el token. Para obtener más información acerca de cómo hacerlo, vea [Conectar con Media Services con el SDK de servicios de medios para .NET](media-services-dotnet-connect-programmatically.md).
    - El token contiene una firma no válida.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 Prohibido

No se permite la solicitud debido a una de las razones siguientes:

- La cuenta de Media Services no se encuentra o se ha eliminado.
- La cuenta de Media Services está deshabilitada y el tipo de solicitud no HTTP GET. Operaciones de servicio devolverá la respuesta 403.
- El símbolo de autenticación no contiene información de credenciales del usuario: nombre de la cuenta o SubscriptionId. Puede encontrar esta información en la extensión de la interfaz de usuario de servicios de medios para su cuenta de Media Services en el Portal de administración de Azure.
- No pueden tener acceso a los recursos.
    - Se ha intentado usar un MediaProcessor que no está disponible para su cuenta de Media Services.
    - Se intentó actualizar una plantilla de trabajo definida por el Media Services.
    - Se ha intentado sobrescribir localizador algunos otros servicios de Media de la cuenta.
    - Se ha intentado sobrescribir ContentKey algunos otros servicios de Media de la cuenta.

- No se pudo crear el recurso debido a una cuota de servicio que se alcanzó para la cuenta de Media Services. Para obtener más información sobre las cuotas de servicio, consulte [cuotas y limitaciones](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 no encontrado

No se admite la solicitud de recursos debido a uno de los motivos siguientes:

- Se intentó actualizar una entidad que no existe.
- Se ha intentado eliminar una entidad que no existe.
- Se ha intentado crear una entidad vinculada a una entidad que no existe.
- Se ha intentado obtener una entidad que no existe.
- Se ha intentado especificar una cuenta de almacenamiento que no está asociada con la cuenta de Media Services.  

## <a name="409-conflict"></a>409 conflicto

No se permite la solicitud debido a una de las razones siguientes:

- Más de un AssetFile tiene el nombre especificado dentro del activo.
- Se ha intentado crear un segundo AssetFile principal dentro del activo.
- Se ha intentado crear un ContentKey con el Id ya en uso.
- Se ha intentado crear un localizador con el Id ya en uso.
- Más de un IngestManifestFile con el nombre especificado dentro de la IngestManifest.
- Se ha intentado vincular un segundo cifrado de almacenamiento ContentKey al cifrado de almacenamiento activo.
- Se ha intentado vincular el mismo ContentKey al activo.
- Se ha intentado crear un localizador de un activo cuyo contenedor de almacenamiento falta o ya no está asociado con el activo.
- Se ha intentado crear un localizador de un activo que ya tiene 5 localizadores en uso. (El almacenamiento de azure exige el límite de cinco directivas de acceso compartido en un contenedor de almacenamiento).
- Vincular cuenta de almacenamiento de un activo a un IngestManifestAsset no es idéntica a la cuenta de almacenamiento utilizada por el elemento primario IngestManifest.  

## <a name="500-internal-server-error"></a>Error de servidor interno 500

Durante el procesamiento de la solicitud, Media Services encuentra un error que impide el procesamiento de continuar. Esto puede deberse a uno de los motivos siguientes:

- No se puede crear un activo o trabajo, información de cuota de servicio de la cuenta de Media Services no está disponible temporalmente.
- No se puede crear un contenedor de almacenamiento de blobs activo o IngestManifest, información de cuenta de almacenamiento de la cuenta no está disponible temporalmente.
- Otros errores inesperados. 

## <a name="503-service-unavailable"></a>503 Servicio no disponible

El servidor está actualmente no puede recibir solicitudes. Este error puede deberse a solicitudes excesivo al servicio. Media Services mecanismo de limitación restringe el uso de recursos para las aplicaciones que solicitar excesivo al servicio.

>[AZURE.NOTE]Compruebe el mensaje de error y la cadena de código de error para obtener información más detallada sobre el motivo por el que tiene el error 503. Este error siempre significa el límite.

Descripciones de estado posibles son:

- "El servidor está ocupado. Ejecuciones anteriores de este tipo de solicitud tardaron más de {0} segundos."
- "El servidor está ocupado. Más de {0} solicitudes por segundo pueden regular."
- "El servidor está ocupado. Más de solicitudes de {0} dentro de {1} segundos pueden regular."

Para controlar este error, se recomienda utilizar lógica de reintento de interrupción exponencial. Eso significa usar cada vez más tiempo espera entre reintentos para las respuestas de errores consecutivos.  Para obtener más información, vea el [Bloque de aplicación de control de errores transitorias](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Si está utilizando [SDK de Azure Media Services para .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), la lógica de reintento para el error 503 se ha implementado el SDK.  
  
## <a name="see-also"></a>Vea también  

[Códigos de Error de administración de servicios de multimedia](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
