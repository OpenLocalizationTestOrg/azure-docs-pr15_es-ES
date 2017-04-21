<properties 
    pageTitle="Información general sobre plantillas de Widevine licencia | Microsoft Azure" 
    description="Este tema ofrece una descripción general de una plantilla de licencia de Widevine que se usa para configurar Widevine licencias." 
    authors="juliako" 
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
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Información general sobre plantillas de Widevine licencia

##<a name="overview"></a>Información general

Azure Media Services ahora le permite configurar y solicitar Widevine licencias. Cuando el Reproductor de usuario final intenta reproducir el contenido de Widevine protegido, se envía una solicitud al servicio de entrega de licencia para obtener una licencia. Si el servicio de licencias aprueba la solicitud, emite la licencia que se envía al cliente y puede utilizarse para descifrar y reproducir el contenido especificado.

Solicitud de licencia de Widevine tiene el formato de un mensaje JSON.  

Tenga en cuenta que puede elegir crear un mensaje vacío sin valores sólo "{}" y se creará una plantilla de licencia con todos los valores predeterminados.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>Mensaje JSON

Nombre | Valor | Descripción
---|---|---
carga |Cadena base64 |La solicitud de licencia enviada por un cliente. 
content_id | Cadena base64|Identificador utilizado para el derivan KeyId(s) y contenido de claves para cada content_key_specs.track_type.
proveedor |cadena |Se usa para buscar claves de contenido y directivas. Obligatorio.
nombreDeDirectiva | cadena |Nombre de una directiva previamente registrada. Opcional
allowed_track_types | enumeración  | SD_ONLY o SD_HD. Controles de qué contenido teclas deben incluirse en una licencia
content_key_specs | matriz de JSON estructuras, vea **Contenido especificaciones clave** | Un proporciona más control sobre qué contenido teclas de dirección para volver. Para obtener información detallada, vea contenido especificaciones clave debajo de.  Sólo se puede especificar allowed_track_types y content_key_specs. 
use_policy_overrides_exclusively | valor booleano. True o false | Usar atributos de directiva especificados por policy_overrides y omitir todas las directivas previamente almacenada.
policy_overrides | JSON estructura, consulte a continuación la **Directiva reemplaza** | Configuración de la directiva de esta licencia.  En el caso de que este activo tiene una directiva predefinida, se usará estos valores especificados. 
session_init | JSON estructura, consulte **Inicialización de sesión** | Datos opcionales pasan a la licencia.
parse_only | valor booleano. True o false | Se analiza la solicitud de licencia, pero no se emite ninguna licencia. Sin embargo, los valores de la solicitud de licencia se devuelven en la respuesta del formulario.  

##<a name="content-key-specs"></a>Especificaciones de la clave de contenido 

Si existe una directiva existente, no hay para especificar cualquiera de los valores de la especificación de clave de contenido.  La directiva existente asociada a este contenido se utilizará para determinar la protección de salida como HDCP y CGMS.  Si una directiva existente no está registrada con el servidor de licencias de Widevine, el proveedor de contenido puede insertar los valores que aparecen en la solicitud de licencia.   


Debe especificar cada content_key_specs para todas las pistas, independientemente de la opción de use_policy_overrides_exclusively. 


Nombre | Valor | Descripción
---|---|---
content_key_specs. track_type | cadena | Un nombre de tipo de control. Si se especifica content_key_specs en la solicitud de licencia, asegúrese de especificar que todos los tipos de seguimiento explícitamente. Si no lo hace provocará error reproducción últimos 10 segundos. 
content_key_specs  <br/> security_level | UInt32 | Define los requisitos de la potencia de cliente para la reproducción. <br/> 1 - se requiere crypto de caja blanca basada en software. <br/> 2 - se requiere crypto software y un descodificador confuso. <br/> 3 - las operaciones de cifrado y material de clave deben realizarse dentro de un entorno de ejecución de confianza de copia de seguridad de hardware. <br/> 4 - el cifrado y descodificar de contenido deben realizarse dentro de un entorno de ejecución de confianza de copia de seguridad de hardware.  <br/> 5 - el cifrado, descodificación y todo manipulación de los archivos multimedia (comprimido y descomprimido) deben corregirse dentro de un entorno de ejecución de confianza de copia de seguridad de hardware.  
content_key_specs <br/> required_output_protection.HDC | cadena - uno de: HDCP_NONE, HDCP_V1, HDCP_V2 | Indica si se requieren HDCP
content_key_specs <br/>clave | Base 64 <br/>cadena codificada|Contenido clave para este control. Si se especifica, se requiere la track_type o key_id.  Esta opción permite que el proveedor de contenido insertar la clave de contenido para este control en lugar de permitir que el servidor de licencias de Widevine generar o buscar una clave.
content_key_specs.key_ID| Cadena binario, codificado base 64 16 bytes | Identificador único para la clave. 


##<a name="policy-overrides"></a>Reemplaza de directiva 

Nombre | Valor | Descripción
---|---|---
policy_overrides. can_play | valor booleano. True o false | Indica que se permite que la reproducción del contenido. El valor predeterminado es false.
policy_overrides. can_persist | valor booleano. True o false |Indica que se puede guardar la licencia en almacenamiento volátiles para uso sin conexión. El valor predeterminado es false.
policy_overrides. can_renew | booleana true o false |Indica que se permite la renovación de esta licencia. Si es true, la duración de la licencia puede ampliarse por latidos. El valor predeterminado es false. 
policy_overrides. license_duration_seconds | Int64 | Indica que la ventana de tiempo para esta licencia específica. Un valor de 0 indica que no hay ningún límite a la duración. El valor predeterminado es 0. 
policy_overrides. rental_duration_seconds | Int64 | Indica que la ventana de tiempo mientras se permite la reproducción. Un valor de 0 indica que no hay ningún límite a la duración. El valor predeterminado es 0. 
policy_overrides. playback_duration_seconds | Int64 | La ventana de visualización de tiempo una vez que comience la reproducción dentro de la duración de la licencia. Un valor de 0 indica que no hay ningún límite a la duración. El valor predeterminado es 0. 
policy_overrides. renewal_server_url |cadena | Todas las solicitudes de latidos (renovación) de esta licencia se dirigirá a la dirección URL especificada. Este campo solo se usa si can_renew es true.
policy_overrides. renewal_delay_seconds |Int64 |¿Cuántos segundos después license_start_time, antes de intenta en primer lugar la renovación. Este campo solo se usa si can_renew es true. El valor predeterminado es 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Especifica el retraso en segundos entre las solicitudes de renovación de licencia subsiguientes, en caso de error. Este campo solo se usa si can_renew es true. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | La ventana de tiempo, en el que se permite la reproducción para continuar mientras renovación está intento todavía incorrecta debido a problemas de back-end con el servidor de licencias. Un valor de 0 indica que no hay ningún límite a la duración. Este campo solo se usa si can_renew es true.
policy_overrides. renew_with_usage | booleana true o false |Indica que se enviará la licencia para la renovación cuando se inicia el uso. Este campo solo se usa si can_renew es true. 

##<a name="session-initialization"></a>Inicialización de sesión

Nombre | Valor | Descripción
---|---|---
provider_session_token | Cadena base64 |Este token de sesión se pasa en la licencia y se existe en posterior renovación.  El símbolo de sesión no se conservan más allá de sesiones. 
provider_client_token | Cadena base64 | Símbolo de cliente para enviar en la respuesta de licencia.  Si la solicitud de licencia contiene un símbolo de cliente, este valor se omite. El token de cliente se conservará más allá de sesiones de licencia.
override_provider_client_token | valor booleano. True o false |Si false y la solicitud de licencia contiene un símbolo de cliente, utilice el símbolo de la solicitud incluso si se ha especificado un símbolo de cliente en la estructura.  Si es true, utilice siempre el token especificado en la estructura.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Configurar las licencias de Widevine con tipos de .NET

Media Services proporciona las API de .NET que permiten configurar las licencias de Widevine. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Clases definidas en el SDK de Media Services .NET

Los siguientes son las definiciones de estos tipos.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo usar las API de .NET para configurar una licencia de Widevine simple.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Vea también

[Uso del cifrado comunes PlayReady o Widevine dinámicos](media-services-protect-with-drm.md)
