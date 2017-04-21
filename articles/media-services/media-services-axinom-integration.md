<properties 
    pageTitle="Usar Axinom para ofrecer Widevine licencias a Azure Media Services | Microsoft Azure" 
    description="Este artículo describe cómo puede usar Azure Media Services (AMS) para ofrecer una secuencia que se cifra dinámicamente AMS con PlayReady y Widevine DRMs. La licencia de PlayReady proviene de servidor de licencias PlayReady de servicios de medios y se ha entregado Widevine licencia Axinom servidor de licencias." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Usar Axinom para ofrecer Widevine licencias a Azure Media Services  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Información general

Azure Media Services (AMS) agregó Google Widevine dinámico protección (consulte [blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obtener información detallada). Además, Azure Media Player (AMP) también se agrega soporte Widevine (consulte [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) para obtener información detallada). Esto es un logro principal de transmisión de contenido de guión protegido por CENC con múltiples-native-DRM (PlayReady y Widevine) en los exploradores modernos disponer de MSE y EME.

A partir de la Media Services .NET SDK versión 3.5.2, Media Services le permite configurar la plantilla de la licencia de Widevine y obtener Widevine licencias. También puede usar los siguientes asociados de AMS que le ayudarán a ofrecer Widevine licencias: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

En este artículo se describe cómo integrar y probar el servidor de licencias de Widevine administrado por Axinom. Más concretamente, abarca:  

- Configuración de cifrado comunes dinámico con multi-DRM (PlayReady y Widevine) con la URL de adquisición de licencia correspondiente;
- Generar un token JWT para cumplir con los requisitos del servidor de licencias;
- Desarrollo de aplicaciones de Azure Media Player que controla la adquisición de licencias con la autenticación de token JWT;

El sistema completo y el flujo de contenido en el siguiente diagrama puede describirse mejor ID de clave, clave, valor de inicialización clave, token JTW y sus notificaciones.

![GUIÓN y CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Protección del contenido

Para configurar protección dinámica y la directiva de entrega clave, vea el blog de Mingfei: [cómo configurar Widevine embalaje con Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Puede configurar protección CENC dinámica con múltiples DRM para guión transmisión de estos procedimientos:

1. Protección de PlayReady para MS Edge y IE11, que podría tener restricciones de autorización de token. La directiva restringido token adjuntará un token emitido por una Token servicio seguro (STS), como Azure Active Directory;
1. Protección de Widevine para Chrome, puede requiere autenticación token con token emitido por otro STS. 

Consulte [Generación de Token JWT](media-services-axinom-integration.md#jwt-token-generation) sección ¿por qué Azure Active Directory no se puede utilizar como un STS Widevine servidor de Axinom de licencias.

###<a name="considerations"></a>Consideraciones

1. Debe utilizar el Axinom especificado inicialización clave (8888000000000000000000000000000000000000) y su generado o seleccionado tecla ID para generar la clave de contenido para configurar el servicio de entrega clave. Servidor de licencias de Axinom emite todas las licencias que contiene las claves de contenido según la misma inicialización clave es válida de producción y pruebas.
1. La dirección URL de adquisición de licencia Widevine para realizar pruebas: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Se permite HTTP y HTTS.

##<a name="azure-media-player-preparation"></a>Preparación del Reproductor multimedia de Azure

AMP v1.4.0 es compatible con la reproducción de contenido de AMS dinámicamente empaquetado con PlayReady y Widevine DRM.
Si el servidor de licencias de Widevine no requiere autenticación token, no hay nada adicional que debe realizar para probar el contenido de un guión protegido por Widevine. Por ejemplo, el equipo de AMP ofrece un sencillo [ejemplo](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), donde puede ver su funcionamiento en borde y IE11 con PlayReady y Chrome con Widevine.
El servidor de licencias de Widevine proporcionado por Axinom requiere autenticación de token JWT. El token JWT debe enviarse con la solicitud de licencia a través de un encabezado HTTP "X-AxDRM de mensaje". Para ello, debe agregar el siguiente código de javascript en la página web de hospedaje AMP antes de establecer el origen:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

El resto del código de AMP es API AMP estándar como en un documento AMP [aquí](http://amp.azure.net/libs/amp/latest/docs/).

Tenga en cuenta que javascript anterior para el encabezado de autorización personalizado configuración sigue un enfoque a corto plazo antes del funcionario se publicó el enfoque a largo plazo en panel de administración.

##<a name="jwt-token-generation"></a>Generación de Token JWT

Axinom Widevine el servidor de licencias para realizar pruebas requiere autenticación de token JWT. Además, una de las notificaciones del token JWT es de un tipo de objeto complejo en lugar de datos simples.

Desgraciadamente, Azure AD sólo puede emitir tokens JWT con tipos simples. Del mismo modo, API de .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler y JwtPayload) solo permite la entrada de tipo de objeto complejo como notificaciones. Sin embargo, las notificaciones se serializan aún como cadena. Por lo tanto, no podemos utilizar cualquiera de los dos para generar el token JWT para Widevine solicitud de licencia.


De John Sheehan [paquete JWT Nuget](https://www.nuget.org/packages/JWT) satisface las necesidades para que se va a usar este paquete Nuget.

A continuación, encontrará el código para generar token JWT con las notificaciones necesarias según sea necesario Axinom Widevine servidor de licencias para realizar pruebas:

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Servidor de licencias de Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Consideraciones

1.  Aunque requiere servicio de entrega de licencia de PlayReady AMS "portador =" anterior a un símbolo de autenticación, el servidor de licencias de Axinom Widevine no utilizarlo.
2.  La clave de comunicación Axinom sirve como clave de firma. Tenga en cuenta que la clave es una cadena hexadecimal, pero debe tratará como una serie de bytes no es una cadena de codificación. Para ello, el método ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Recuperar el Id. de clave

Es podrán que haya observado que en el código para generar un JWT identificador de token, clave se requiere. Dado que las necesidades de token JWT está preparada antes Reproductor de carga AMP clave ID es necesario recuperar para generar el token JWT.

Por supuesto, hay varias maneras de obtener mantenga de clave de Id. Por ejemplo, uno puede almacenar Id. de clave junto con los metadatos de contenido en una base de datos. O bien puede recuperar Id. de archivo MPD guión (descripción de la presentación de medios) clave. El código siguiente es para el último.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Resumen

Con la adición más reciente de compatibilidad de Widevine de protección de contenido de Azure Media Services y el Reproductor multimedia de Azure, estamos capaz de implementar la transmisión de guión + múltiples-native-DRM (PlayReady + Widevine) con ambos servicio de licencias de PlayReady en AMS y Widevine servidor de licencias de Axinom de los siguientes exploradores moderna:

- Chrome
- Borde de Microsoft en Windows 10
- Internet Explorer 11 en Windows 8.1 y Windows 10
- Firefox (escritorio) y Safari en Mac (no iOS) también son compatibles con Silverlight y la misma dirección URL con el Reproductor multimedia de Azure

Los siguientes parámetros son necesarios en el servidor de licencias de Axinom Widevine aprovechar solución mínima. Excepto clave ID, el resto de los parámetros proporcionados por Axinom en función de su configuración de servidor Widevine.


Parámetro|Cómo se utiliza
---|---
Id. de clave de comunicación|Se debe incluir como valor de la reclamación "com_key_id" en token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) sección).
Clave de comunicación|Deben usarse como la clave de firma de token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) sección).
Valor de inicialización clave|Deben usarse para generar la clave de contenido con el contenido de una determinada Id. de clave (consulte [esta](media-services-axinom-integration.md#content-protection) sección).
Dirección URL de adquisición de licencia Widevine|Debe usarse en configuración de directiva de entrega de activos para guión transmisión (consulte [esta](media-services-axinom-integration.md#content-protection) sección).
Id. de clave de contenido|Se debe incluir como parte del valor de notificación de mensaje de derechos de token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) sección). 


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Confirmaciones 

Nos gustaría conscientes de las siguientes personas quién ha contribuido hacia la creación de este documento: Kristjan Jõgi de Axinom, Mingfei Yan y Amit Rajput.
