<properties 
    pageTitle="Conectarse a Media Services cuenta con la API de REST | Microsoft Azure" 
    description="Este tema muestra cómo conectarse a usar API de REST de Media Services." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Conectarse a Media Services cuenta con la API de REST de servicios de medios

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [REST](media-services-rest-connect-programmatically.md)

En este tema se describe cómo obtener una conexión a Microsoft Azure Media Services mediante programación cuando se programa con la API de REST de servicios de medios.

Dos cosas son necesarias al acceder a Microsoft Azure Media Services: un token de acceso que proporciona servicios de Control de acceso (ACS) de Azure y URI de Media Services propiamente dicho. Puede usar cualquier medio que desee al crear estas solicitudes como especificar los valores de encabezado correcto y pase el token de acceso correctamente al llamar a Media Services.

Los pasos siguientes describen el flujo de trabajo más comunes cuando se usa la API de REST de servicios de medios para conectarse a Media Services:

1. Obtener un token de acceso 
2. Conectarse a los servicios de medios URI 

    >[AZURE.NOTE] Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar llamadas posteriores a la URI nuevo.
También puede recibir una respuesta HTTP/1.1 200 que contiene la descripción de metadatos de la API de ODATA.

3. Registrar las llamadas de API posteriores a la nueva dirección URL. 

    Por ejemplo, si después de probar a conectarse, obtuvo lo siguiente:

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Debe publicar las llamadas de API posteriores a https://wamsbayclus001rest-hs.cloudapp.net/api/.

##<a name="access-control-address"></a>Dirección de control de acceso

Dirección de control de acceso de Media Services es https://wamsprodglobal001acs.accesscontrol.windows.net, excepto la región del Norte de China, donde está https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##<a name="getting-an-access-token"></a>Obtener un token de acceso

Para obtener acceso a servicios de medios directamente a través de la API de REST, recuperar un token de acceso de ACS y usar durante cada solicitud HTTP que realice en el servicio. Este token es similar a otros tokens proporcionados por ACS basada en solicitudes de acceso proporcionados en el encabezado de una solicitud HTTP y mediante el protocolo de OAuth v2. No es necesario cualquier otros requisitos previos antes de conectarse directamente a Media Services.

En el ejemplo siguiente se muestra el encabezado de solicitud HTTP y el cuerpo utilizado para recuperar un símbolo.

**Encabezado**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Cuerpo**:

Debe demostrar los valores client_id y client_secret en el cuerpo de esta solicitud; client_id y client_secret corresponden a los valores de nombre de la cuenta y AccountKey, respectivamente. Estos valores se proporcionan por Media Services al configurar su cuenta. 

Tenga en cuenta que el AccountKey para su cuenta de Media Services deben URL codificada (consulte la [Codificación de porcentaje](http://tools.ietf.org/html/rfc3986#section-2.1) cuando se usa como el valor de client_secret en su solicitud de token de acceso.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Por ejemplo: 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


En el ejemplo siguiente se muestra la respuesta HTTP que contiene el acceso token en el cuerpo de la respuesta.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Se recomienda a los valores de "access_token" y "expires_in" a un externo de almacenamiento en caché. Los datos de tokens podrían recuperará al almacenamiento y volver a utilizar en las llamadas de la API de REST de servicios de medios. Esto es especialmente útil para escenarios donde el token puede compartirse con seguridad entre varios procesos o equipos.

Asegúrese de que supervisar el valor de "expires_in" del token de acceso y actualizar las llamadas API de REST con tokens nuevas según sea necesario.

###<a name="connecting-to-the-media-services-uri"></a>Conectarse a los servicios de medios URI

La raíz URI de Media Services es https://media.windows.net/. Inicialmente, debe conectar este URI y si recibe una redirección 301 en respuesta, debe realizar llamadas subsiguientes a la URI nuevo. Además, no use cualquier lógica automática de redirección y seguir en las convocatorias. No se reenviarán al nuevo URI verbos HTTP y organismos de la solicitud.

Observe que la raíz URI para cargar y descargar archivos de activos es https://yourstorageaccount.blob.core.windows.net/ donde el nombre de la cuenta de almacenamiento es el mismo que se utiliza durante la instalación de la cuenta de Media Services.

En el ejemplo siguiente se muestra la solicitud HTTP en la raíz de Media Services URI (https://media.windows.net/). La solicitud obtiene una redirección 301 en respuesta. La siguiente solicitud está usando el nuevo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Solicitud HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**Respuesta HTTP**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Solicitud HTTP** (usando el nuevo URI):
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**Respuesta HTTP**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] Una vez creado el nuevo URI, que es el URI que debe usarse para comunicarse con los servicios de medios. 


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
