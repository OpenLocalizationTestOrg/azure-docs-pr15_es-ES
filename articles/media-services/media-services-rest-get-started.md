<properties 
    pageTitle="Introducción a la entrega de contenido a petición mediante el resto | Microsoft Azure" 
    description="Este tutorial le guiará por los pasos de la implementación de una aplicación de entrega de contenido a petición con Azure Media Services con la API de REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introducción a la entrega de contenido a petición con REST 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Este tutorial le guiará por los pasos de la implementación de una aplicación de la entrega de contenido de vídeo a la carta (VoD) mediante las API de REST de Azure Media Services (AMS). 

El tutorial presenta el flujo de trabajo básico de Media Services y los objetos de programación más comunes y las tareas necesarias para el desarrollo de Media Services. Al final del curso, podrá transmitir o cada vez descargar un archivo de media de muestra que cargó, codificado y descargado.  

## <a name="prerequisites"></a>Requisitos previos
Los siguientes requisitos previos necesarios para iniciar el desarrollo de Media Services con las API de REST.

- Entender cómo desarrollar con la API de REST de servicios de medios. Para obtener más información, vea [media services-resto-información general](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- Una aplicación de su elección que puede enviar solicitudes HTTP y las respuestas. Este tutorial usa [Fiddler](http://www.telerik.com/download/fiddler). 

En este tutorial se muestran las siguientes tareas.

1.  Crear una cuenta de Media Services (con el portal de Azure).
2.  Configurar extremo streaming (con el portal de Azure).
1.  Conectarse a la cuenta de Media Services con la API de REST.
1.  Crear un nuevo activo y cargar un archivo de vídeo con la API de REST.
1.  Configurar unidades streaming con la API de REST.
2.  Codificar el archivo de origen en un conjunto de archivos de velocidad adaptación MP4 con la API de REST.
1.  Publicar el activo y las direcciones URL con la API de REST de descarga de obtener transmisión y progresivo. 
1.  Reproducir el contenido. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Crear una cuenta de Azure Media Services con el portal de Azure

Los pasos de esta sección muestran cómo crear una cuenta de AMS.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ nuevo** > **multimedia + CDN** > **Media Services**.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. En **Crear cuenta de servicios de medios** escriba valores requeridos.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. En **Nombre de la cuenta**, escriba el nombre de la nueva cuenta de AMS. Un nombre de cuenta de Media Services es todos los números en minúsculas o letras sin espacios y es de 3 a 24 caracteres de longitud.
    2. En la suscripción, seleccione entre las diferentes suscripciones Azure que tienen acceso a.
    
    2. En el **Grupo de recursos**, seleccione el recurso nuevo o existente.  Un grupo de recursos es una colección de recursos que comparten directivas, permisos y ciclo de vida. Más información [aquí](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. En **ubicación**, seleccione la región geográfica se usa para almacenar los registros de medios y metadatos para su cuenta de Media Services. Esta área se usa para procesar y transmita los elementos multimedia. Solo las regiones Media Services disponibles aparecen en el cuadro de lista desplegable. 
    
    3. En la **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento para proporcionar el almacenamiento de blobs del contenido multimedia desde su cuenta de Media Services. Puede seleccionar una cuenta existente de almacenamiento en la misma región geográfica como su cuenta de Media Services, o puede crear una cuenta de almacenamiento. Se crea una nueva cuenta de almacenamiento en la misma región. Las reglas de nombres de cuenta de almacenamiento son los mismos que las cuentas de Media Services.

        Obtenga más información sobre el almacenamiento [aquí](storage-introduction.md).

    4. Seleccione **Anclar al panel** para ver el progreso de la implementación de la cuenta.
    
7. Haga clic en **crear** en la parte inferior del formulario.

    Una vez que la cuenta se ha creado correctamente, el estado cambia a **está ejecutando**. 

    ![Configuración de servicios de medios](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para administrar su cuenta AMS (por ejemplo, cargar vídeos, codificar activos, supervisar el progreso de tarea) Utilice la ventana de **configuración** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar extremos streaming con el portal de Azure

Cuando se trabaja con uno de los escenarios más comunes ofrece vídeo a través de la velocidad de adaptación transmisión a los clientes de Azure Media Services. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

Media Services proporciona embalaje dinámico, que le permite ofrecer la velocidad de adaptación contenido MP4 codificado en formatos admitidos por el Media Services (MPEG guión, HLS, Smooth Streaming, HDS) just-in-time, sin tener que almacenar versiones preconfiguradas de cada uno de estos formatos de transmisión de transferencia.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo mezzanine (origen) en un conjunto de archivos de velocidad adaptación MP4 (se muestran los pasos de codificación más adelante en este tutorial).  
- Crear al menos una unidad de transmisión por secuencias para la *transmisión de extremo* desde el que tiene previsto entrega el contenido. Los pasos que muestran cómo cambiar el número de unidades streaming.

Con embalaje dinámico, solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services crea y sirve la respuesta adecuada en función de las solicitudes de un cliente.

Para crear y cambiar el número de unidades reservadas de transmisión, haga lo siguiente:


1. En la ventana **configuración** , haga clic en **los extremos de transmisión por secuencias**. 

2. Haga clic en el valor predeterminado transmisión extremo. 

    Aparecerá el cuadro de diálogo **Detalles de extremo de transmisión predeterminado** .

3. Para especificar el número de unidades streaming, deslice el control deslizante de **unidades de flujo de datos** .

    ![Unidades de transmisión](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Haga clic en el botón **Guardar** para guardar los cambios.

    >[AZURE.NOTE]La asignación de las nuevas unidades puede tardar hasta 20 minutos en completarse.

## <a id="connect"></a>Conectarse a la cuenta de Media Services con la API de REST

Dos cosas son necesarias al acceder a Azure Media Services: un token de acceso que proporciona servicios de Control de acceso (ACS) de Azure y URI de Media Services propiamente dicho. Puede usar cualquier medio que desee al crear estas solicitudes como especificar los valores de encabezado correcto y pase el token de acceso correctamente al llamar a Media Services.

Los pasos siguientes describen el flujo de trabajo más comunes cuando se usa la API de REST de servicios de medios para conectarse a Media Services:

1. Obtener un token de acceso. 
2. Conectarse a los servicios de medios URI.  

    Recuerde que después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar llamadas posteriores a la URI nuevo. También puede recibir una respuesta HTTP/1.1 200 que contiene la descripción de metadatos de la API de ODATA.
3. Registrar las llamadas de API posteriores a la nueva dirección URL. 
    
    Por ejemplo, si después de probar a conectarse, obtuvo lo siguiente:
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Debe publicar las llamadas de API posteriores a https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>Obtener un token de acceso

Para obtener acceso a servicios de medios directamente a través de la API de REST, recuperar un token de acceso de ACS y usar durante cada solicitud HTTP que realice en el servicio. No es necesario cualquier otros requisitos previos antes de conectarse directamente a Media Services.

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

Necesita resultó los valores client_id y client_secret en el cuerpo de esta solicitud; client_id y client_secret corresponden a los valores de nombre de la cuenta y AccountKey, respectivamente. Estos valores se proporcionan por Media Services al configurar su cuenta. 

El AccountKey para su cuenta de Media Services debe ser la URL codificada cuando se usa como el valor de client_secret en su solicitud de token de acceso.

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Se recomienda a los valores de "access_token" y "expires_in" a un externo de almacenamiento en caché. Los datos de tokens podrían recuperará al almacenamiento y volver a utilizar en las llamadas de la API de REST de servicios de medios. Esto es especialmente útil para escenarios donde el token puede compartirse con seguridad entre varios procesos o equipos.

Asegúrese de que supervisar el valor de "expires_in" del token de acceso y actualizar las llamadas API de REST con tokens nuevas según sea necesario.

###<a name="connecting-to-the-media-services-uri"></a>Conectarse a los servicios de medios URI

La raíz URI de Media Services es https://media.windows.net/. Inicialmente, debe conectar este URI y si recibe una redirección 301 en respuesta, debe realizar llamadas subsiguientes a la URI nuevo. Además, no use cualquier lógica automática de redirección y seguir en las convocatorias. No se reenviarán al nuevo URI verbos HTTP y organismos de la solicitud.

La raíz URI para cargar y descargar archivos de activos es https://yourstorageaccount.blob.core.windows.net/ donde el nombre de la cuenta de almacenamiento es el mismo que se utiliza durante la instalación de la cuenta de Media Services.

En el ejemplo siguiente se muestra la solicitud HTTP en la raíz de Media Services URI (https://media.windows.net/). La solicitud obtiene una redirección 301 en respuesta. La siguiente solicitud está usando el nuevo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Solicitud HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] De ahora en el nuevo URI se utiliza en este tutorial.

## <a id="upload"></a>Crear un nuevo activo y cargar un archivo de vídeo con la API de REST

En servicios de medios, cargar los archivos digitales en un activo. Entidad de **activos** puede contener vídeo, audio, imágenes, colecciones de miniaturas, texto pistas y subtítulos archivos (y los metadatos acerca de estos archivos.)  Una vez que los archivos se cargan en activo, el contenido se almacena de forma segura en la nube para el procesamiento de más y transmisión. 

Uno de los valores que debe proporcionar cuando se crea un activo es opciones de creación de activos. La propiedad de **Opciones** es un valor de enumeración que describe las opciones de cifrado que pueden crearse con un activo. Un valor válido es uno de los valores de la lista siguiente, no una combinación de valores que aparecen en esta lista:

 
- **Ninguno** = se usa**0** - sin cifrado. Al usar esta opción no está protegido su contenido en tránsito o en el resto de almacenamiento.
    Si va a ofrecer un MP4 mediante descarga progresiva, use esta opción. 
- **StorageEncrypted** = **1** - cifra el contenido de borrar localmente mediante el cifrado AES de 256 bits y, a continuación, se carga en el almacenamiento de Azure donde esté almacenado cifrado al resto. Activos protegidos con cifrado de almacenamiento automáticamente sin cifrar y colocados en un sistema de archivos cifrados antes de codificación y, opcionalmente, cifrados de nuevo antes de cargarlo como un nuevo activo de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos de medios de entrada de alta calidad con cifrado almacenados en disco.
- **CommonEncryptionProtected** = **2** : Use esta opción si va a cargar contenido que ya se ha cifrado y protegido con cifrado comunes o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con PlayReady DRM).
- **EnvelopeEncryptionProtected** = **4** : Use esta opción si va a cargar HLS cifrada con AES. Los archivos deben codificados y cifrados transformar administrador.

### <a name="create-an-asset"></a>Crear un activo

Un activo es un contenedor de varios tipos de o conjuntos de objetos en servicios de medios, incluyendo vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y archivos de subtítulos. En la API de REST, crear un activo debe enviar solicitud POST a Media Services y colocar cualquier información de propiedad sobre su activo en el cuerpo de la solicitud.

En el ejemplo siguiente se muestra cómo crear un activo.

**Solicitud HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

**Respuesta HTTP**

Si es correcto, se devuelve lo siguiente:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Crear un AssetFile

La entidad [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa un archivo de audio o vídeo que se almacena en un contenedor de blob. Un archivo de activos siempre está asociado a un activo y un recurso puede contener uno o varios AssetFiles. Se produce un error en la tarea de servicios de Media Encoder si un objeto de archivo activo no está asociado a un archivo digital en un contenedor de blob.

Después de cargar el archivo de medios digitales en un contenedor de blob, use la solicitud HTTP **Combinar** para actualizar la AssetFile con información sobre el archivo multimedia (como se muestra más adelante en el tema). 

**Solicitud HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Respuesta HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Crear la AccessPolicy con permiso de escritura. 

Antes de cargar los archivos en el almacenamiento de blobs, establezca el acceso derechos de directiva para escribir en un activo. Para ello, publique una solicitud HTTP al conjunto de entidades AccessPolicies. Definir un valor de DurationInMinutes tras la creación o recibe un mensaje de error de servidor interno 500 en respuesta. Para obtener más información sobre AccessPolicies, consulte [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

En el ejemplo siguiente se muestra cómo crear un AccessPolicy:
        
**Solicitud HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Respuesta HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Obtener la dirección URL de carga

Para recibir la dirección URL de carga real, cree un localizador SA. Localizadores definen la hora de inicio y el tipo de extremo de conexión para los clientes que deseen tener acceso a archivos de un activo. Puede crear varias entidades de localizador para AccessPolicy activos par y gestionar solicitudes de otro cliente y necesidades. Cada uno de estos localizadores usa el valor de hora de inicio más el valor de DurationInMinutes de la AccessPolicy para determinar la longitud de tiempo que se puede utilizar una dirección URL. Para obtener más información, consulte [localizador](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Una dirección URL de SA tiene el siguiente formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Aplicarán algunas consideraciones:

- No puede tener más de cinco localizadores únicos asociados a un determinado activo a la vez. Para obtener más información, consulte localizador.
- Si necesita cargar archivos inmediatamente, debe establecer el valor de hora de inicio de cinco minutos antes de la hora actual. Esto es porque puede haber reloj skew entre el equipo cliente y Media Services. Además, debe ser el valor de hora de inicio en el siguiente formato de fecha y hora: YYYY-MM-ddTHH (por ejemplo, "2014-05-23T17:53:50Z").   
- Puede haber un segundo 30-40 retrasar después de crea un localizador para cuando esté disponible para su uso. Este problema se aplica a la dirección URL de SA y localizadores de origen.

En el ejemplo siguiente se muestra cómo crear un localizador de URL SA, según se define en la propiedad de tipo en el cuerpo de la solicitud ("1" para un localizador SA) y "2" para un localizador de origen On Demand. La propiedad de **ruta de acceso** devuelta contiene la dirección URL que debe utilizar para cargar el archivo.
    
**Solicitud HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Respuesta HTTP**

Si es correcto, se devuelve la respuesta siguiente:
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Cargar un archivo en un contenedor de almacenamiento de blobs
    
Una vez que la AccessPolicy y el localizador establecer, el archivo cargado en un contenedor de almacenamiento de blobs de Windows Azure utilizando las API de REST de almacenamiento de Azure. Puede cargar en la página o bloquear BLOB. 

>[AZURE.NOTE] Debe agregar el nombre de archivo para el archivo que desea cargar en el valor de la **ruta de acceso** de localizador recibido en la sección anterior. Por ejemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para obtener más información sobre cómo trabajar con BLOB de almacenamiento de Azure, vea [API de REST de servicio de blobs de Windows](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Actualizar la AssetFile 

Ahora que ha cargado el archivo, actualizar la información de FileAsset tamaño (y otros). Por ejemplo:
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Respuesta HTTP**

Si es correcta, la siguiente se devuelve: HTTP/1.1 204 Sin contenido

## <a name="delete-the-locator-and-accesspolicy"></a>Eliminar el AccessPolicy y el localizador 

**Solicitud HTTP**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**Respuesta HTTP**

Si es correcto, se devuelve lo siguiente:

    HTTP/1.1 204 No Content 
    ...

**Solicitud HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Respuesta HTTP**

Si es correcto, se devuelve lo siguiente:

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Configurar unidades streaming con la API de REST

Cuando se trabaja con Azure Media Services adaptación velocidad transmisión uno de los escenarios más comunes ofrece a sus clientes. Con la transmisión de velocidad adaptación, puede cambiar el cliente de una secuencia de mayor o menor velocidad tal como se muestra el vídeo basados en el ancho de banda de red actual, de la CPU y otros factores. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe). 

Media Services proporciona embalaje dinámico, que le permite proporcionar su contenido de velocidad adaptación MP4 o Smooth Streaming codificado en transmisión formatos admitidos por el Media Services (MPEG guión, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de transmisión. 

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- obtener al menos una unidad de transmisión por secuencias para la **transmisión de extremo **desde el que tiene previsto ofrecer su contenido (descrito en esta sección).
- codificar o transformar su mezzanine (origen) de archivos en un conjunto de velocidad adaptación MP4 archivos o adaptación de velocidad Smooth Streaming (se muestran los pasos de codificación más adelante en este tutorial),  

Con embalaje dinámico, solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services crea y sirve la respuesta adecuada en función de las solicitudes de un cliente. 


>[AZURE.NOTE] Para obtener información acerca de detalles de los precios, consulte [Detalles de precios de servicios de medios](http://go.microsoft.com/fwlink/?LinkId=275107).

Para cambiar el número de unidades reservadas de transmisión, haga lo siguiente:
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Obtener el extremo streaming que desea actualizar

Por ejemplo, vamos a obtener el primer extremo streaming en su cuenta (puede tener hasta dos extremos streaming en estado de ejecución al mismo tiempo.)

**Solicitud HTTP**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Respuesta HTTP**
    
Si es correcto, se devuelve lo siguiente:
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>Cambiar la escala del extremo de secuencias
 
**Solicitud HTTP**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**Respuesta HTTP**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Comprobar el estado de una operación de ejecución larga

La asignación de las nuevas unidades tarda aproximadamente 20 minutos en completarse. Para comprobar el estado de la operación, use el método de **operaciones** y especificar el identificador de la operación. La operación se devolvió identificador en la respuesta a la solicitud de **escala** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Solicitud HTTP**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**Respuesta HTTP**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Codificar el archivo de origen en un conjunto de archivos MP4 de adaptación de velocidad de bits

Antes después recopila que pueden codificar activos en Media Services, media, transmuxed, marca de agua y así sucesivamente, se entrega a los clientes. Estas actividades se programan y ejecutar en varias instancias de la función de fondo para asegurarse de alto rendimiento y disponibilidad. Estas actividades se denominan trabajos y cada [trabajo](http://msdn.microsoft.com/library/azure/hh974289.aspx) se compone de tareas atómicas que haga el trabajo real en el archivo activo. 

Como se mencionó anteriormente, cuando se trabaja con Azure Media Services adaptación velocidad transmisión uno de los escenarios más comunes ofrece a sus clientes. Media Services dinámicamente puede empaquetar un conjunto de archivos de velocidad adaptación MP4 en uno de los siguientes formatos: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe). 

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- codificar o transformar su mezzanine (origen) de archivos en un conjunto de velocidad adaptación MP4 archivos o adaptación de velocidad Smooth Streaming,  
- obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto ofrecer su contenido. 

En la sección siguiente muestra cómo crear un trabajo que contiene una tarea de codificación. La tarea especifica para transformar el archivo mezzanine en un conjunto de velocidad adaptación MP4s con **Media Encoder estándar**. La sección también muestra cómo supervisar el progreso de procesamiento del trabajo. Una vez completada la tarea, podrá crear localizadores que son necesarios para obtener acceso a los recursos. 

### <a name="get-a-media-processor"></a>Obtener un procesador de medios

En servicios de medios, un procesador de media es un componente que trata de una tarea de procesamiento específico, como la codificación de conversión de formato, contenido multimedia cifrado o descifrado. Para la tarea de codificación que se muestra en este tutorial, vamos a usar el estándar de codificador multimedia.

El código siguiente solicita el identificador del codificador. 

**Solicitud HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Respuesta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Crear un trabajo

Cada tarea puede tener una o varias tareas según el tipo de procesamiento que desee realizar. A través de la API de REST, puede crear tareas y sus tareas relacionadas en una de estas dos maneras: tareas pueden ser definido en línea a través de la propiedad de navegación de tareas en entidades de trabajo o de proceso por lotes de OData. El SDK de servicios de medios usa procesamiento por lotes. Sin embargo, para la legibilidad de los ejemplos de código de este tema, las tareas son define en línea. Para obtener información sobre el proceso por lotes, consulte el [Proceso por lotes de protocolo Open Data (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

En el ejemplo siguiente se muestra cómo crear y publicar un trabajo con una que tarea se establece para codificar un vídeo en una resolución específica y la calidad. La siguiente sección de la documentación contiene la lista de todos los la [tarea preestablecidos](http://msdn.microsoft.com/library/mt269960) compatibles con el procesador Media Encoder estándar.  

**Solicitud HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Respuesta HTTP**

Si es correcto, se devuelve la respuesta siguiente:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Hay algunas cosas importantes a tener en cuenta en cualquier solicitud de tarea:

- Propiedades de TaskBody deben usar literal XML para definir el número de entrada o salida activos de la tarea. El tema de la tarea contiene la definición del esquema XML para el XML.
- En la definición de TaskBody, valor cada interna <inputAsset> y <outputAsset> se debe establecer como JobInputAsset(value) o JobOutputAsset(value).
- Una tarea puede tener varios activos de salida. Un JobOutputAsset(x) sólo puede utilizarse una vez como un resultado de una tarea en una tarea.
- Puede especificar JobInputAsset o JobOutputAsset como un entrada activo de una tarea.
- Las tareas no deben formar un ciclo.
- El parámetro de valor que se pasa a JobInputAsset o JobOutputAsset representa el valor de índice de un activo. Los activos reales se definen en las propiedades de navegación InputMediaAssets y OutputMediaAssets en la definición de la entidad de trabajo. 

>[AZURE.NOTE] Porque Media Services se basa en OData v3, se hace referencia a los activos individuales en InputMediaAssets y OutputMediaAssets colecciones de propiedades de navegación a través de un "__metadata: uri" valor de nombre de par. 

- InputMediaAssets asigna a uno o varios de los activos que haya creado en Media Services. OutputMediaAssets creados por el sistema. No hacer referencia a un activo existente.
- Nombre OutputMediaAssets utilizando el atributo assetName. Si este atributo no está presente, el nombre de la OutputMediaAsset independientemente del valor de texto interno de la <outputAsset> elemento es con un sufijo el valor de nombre del trabajo o el valor de Id. de trabajo (en el caso de que no está definida la propiedad nombre). Por ejemplo, si establece un valor para assetName para "Muestra", podría establecer la propiedad nombre de OutputMediaAsset a "Muestra". Sin embargo, si no ha definido un valor para assetName, pero se ha definido el nombre del trabajo a "NewJob", el nombre de OutputMediaAsset sería "_NewJob JobOutputAsset (valor)". 

    En el ejemplo siguiente se muestra cómo se establece el atributo assetName:
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Para habilitar encadenar de tarea:

    - Un trabajo debe tener al menos dos tareas
    - Debe haber al menos una tarea cuya entrada es el resultado de otra tarea en el trabajo.

Para obtener más información, vea [crear un trabajo de codificación con la API de REST de servicios de medios](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Supervisar el progreso de procesamiento

Puede recuperar el estado del trabajo mediante la propiedad de estado, tal como se muestra en el ejemplo siguiente. 

**Solicitud HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Respuesta HTTP**

Si es correcto, se devuelve la respuesta siguiente:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Cancelar un trabajo

Media Services le permite cancelar trabajos de ejecución a través de la función CancelJob. Esta llamada devuelve un código de error 400 si intenta cancelar un trabajo cuando se cancela su estado, Cancelar, error o terminado.

En el ejemplo siguiente se muestra cómo llamar a CancelJob.


**Solicitud HTTP**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


Si es correcto, se devuelve un código de 204 respuesta sin cuerpo del mensaje.

>[AZURE.NOTE] Debe codificar URL el id de trabajo (normalmente nb:jid:UUID: algúnValor) cuando se pasa como un parámetro para CancelJob.


### <a name="get-the-output-asset"></a>Obtener el activo de salida 

El código siguiente muestra cómo solicitar el Id. de activo de salida 


**Solicitud HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Respuesta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Publicar los activos y get transmisión y las direcciones URL de descarga progresivo con la API de REST

Para transmitir o descargar un activo, debe "publicación" mediante la creación de un localizador. Localizadores proporcionan acceso a archivos contenidos en el activo. Media Services es compatible con dos tipos de localizadores: OnDemandOrigin localizadores, utilizadas para transmitir contenido multimedia (por ejemplo, MPEG guión, HLS o Smooth Streaming) y localizadores de firma de Access (SA), utilizan para descargar archivos multimedia.

Una vez que cree los localizadores, se pueden generar las direcciones URL que se utilizan para transmitir o descargar archivos. 


Una dirección URL streaming Smooth Streaming tiene el siguiente formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Una dirección URL streaming HLS tiene el siguiente formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Una dirección URL streaming MPEG guión tiene el siguiente formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Una dirección URL de SA utilizado para descargar archivos tiene el siguiente formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Esta sección muestra cómo realizar las siguientes tareas necesarias para los activos de "publicación".  

- Crear la AccessPolicy con permiso de lectura 
- Crear una dirección URL de SA para descargar contenido 
- Crear una dirección URL de origen para transmitir contenido 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Crear la AccessPolicy con permiso de lectura

Antes de descargar o transmisión cualquier contenido multimedia, defina un AccessPolicy con permisos de lectura y crear la entidad localizador correspondiente que especifica el tipo de mecanismo de entrega que desee habilitar para sus clientes. Para obtener más información sobre las propiedades disponibles, vea [Propiedades de la entidad AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

En el ejemplo siguiente se muestra cómo especificar la AccessPolicy para permisos de lectura para un determinado activo.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Si es correcto, se devuelve un código de 201 éxito que describa la entidad AccessPolicy que ha creado. A continuación, use el identificador de AccessPolicy junto con el Id. de activo del activo que contiene el archivo que desea ofrecer (por ejemplo, un activo de salida) para crear la entidad localizador.

>[AZURE.NOTE]
Este flujo de trabajo básico es igual a cargar un archivo cuando se recopila un activo (como se trató anteriormente en este tema). Además, como cargar archivos, si usted (o sus clientes) necesitan tener acceso a los archivos inmediatamente, establezca el valor de hora de inicio en cinco minutos antes de la hora actual. Esta acción es necesaria porque puede haber reloj skew entre el cliente y servicios de medios. El valor de hora de inicio debe estar en el siguiente formato de fecha y hora: YYYY-MM-ddTHH (por ejemplo, "2014-05-23T17:53:50Z").


###<a name="creating-a-sas-url-for-downloading-content"></a>Crear una dirección URL de SA para descargar contenido 

El código siguiente muestra cómo obtener una dirección URL que puede usarse para descargar un archivo multimedia creado y cargado previamente. El AccessPolicy tiene el conjunto de permisos de lectura y la ruta de acceso de localizador hace referencia a una dirección URL de descarga de SA.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Si es correcto, se devuelve la respuesta siguiente:

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


La propiedad de **ruta de acceso** devuelta contiene la dirección URL de SA.

>[AZURE.NOTE]
Si descargar contenido de almacenamiento cifrado, debe manualmente descifrar antes de representarlo o usar la MediaProcessor descifrado de almacenamiento en una tarea de procesamiento de archivos procesados en texto sin cifrar a un OutputAsset de salida y, a continuación, descargar del activo. Para obtener más información sobre el procesamiento, vea Crear un trabajo de codificación con la API de REST de servicios de medios. Además, no se pueden actualizar SA URL localizadores después de haber creado. Por ejemplo, no puede volver a usar el mismo localizador con un valor de hora de inicio actualizado. Esto es debido a la forma en que se crean SA URL. Si desea obtener acceso a un activo para su descarga después de un localizador, debe crear una nueva con una nueva hora de inicio.

###<a name="download-files"></a>Descargar archivos

Una vez que la AccessPolicy y el localizador establecer, puede descargar archivos mediante las API de REST de almacenamiento de Azure.  

>[AZURE.NOTE] Debe agregar el nombre de archivo para el archivo que desea descargar en el valor de la **ruta de acceso** de localizador recibido en la sección anterior. Por ejemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para obtener más información sobre cómo trabajar con BLOB de almacenamiento de Azure, vea [API de REST de servicio de blobs de Windows](http://msdn.microsoft.com/library/azure/dd135733.aspx).

Como resultado de la tarea de codificación que ha realizado anteriores (codificación en conjunto de adaptación MP4), tiene varios archivos MP4 que puede descargarse cada vez. Por ejemplo:    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Crear una dirección URL de transmisión por secuencias para transmitir contenido


El código siguiente muestra cómo crear un localizador URL streaming:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Si es correcto, se devuelve la respuesta siguiente:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Para transmitir una URL de origen Smooth Streaming en un reproductor multimedia streaming, debe agregar la ruta de acceso de propiedad con el nombre de Smooth Streaming manifiestos archivo seguido de "/ manifiestos".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir HLS, anexar (formato = m3u8 aapl) después de la "/ manifiestos".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir guión MPEG, anexar (formato = csf de tiempo de mpd) después de la "/ manifiestos".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Reproducir el contenido  

Para transmitir vídeo, utilice [El Reproductor de Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para probar la descarga progresiva, pegue una dirección URL en un explorador (por ejemplo, Internet Explorer, Chrome, Safari).


##<a name="next-steps-media-services-learning-paths"></a>Pasos siguientes: Media Services rutas de aprendizaje

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>¿Busca algo más?

Si este tema no contiene lo estaba esperando, falta algo o en otra forma no satisface sus necesidades, por favor, nos proporcione sus comentarios mediante el subproceso Disqus a continuación.



