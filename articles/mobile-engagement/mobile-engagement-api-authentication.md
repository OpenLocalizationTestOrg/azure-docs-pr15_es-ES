<properties 
    pageTitle="Autenticar con la API REST de contratación móvil"
    description="Describe cómo autenticar con las API de REST de compromiso de Azure Mobile" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticar con la API REST de contratación móvil

## <a name="overview"></a>Información general

Este documento describe cómo obtener un Oauth AAD válida token para autenticar con la API de REST de compromiso Mobile. 

Se supone que tiene una suscripción válida a Azure y ha creado una aplicación móvil compromiso mediante uno de los [Tutoriales de desarrollador](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticación

Microsoft Azure Active Directory según OAuth token se utiliza para la autenticación. 

En el orden de la solicitud de autenticación una API, un encabezado de autorización debe agregarse a cada solicitud que tiene el siguiente formato:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Tokens de Azure Active Directory caducan en 1 hora.

Hay varias maneras de obtener un token. Dado que generalmente se llaman a las API de un servicio de nube, que desea usar una clave de API. Una clave de API en la terminología de Azure se denomina una contraseña de la entidad de servicio. El siguiente procedimiento describe una forma de configurar manualmente.

### <a name="one-time-setup-using-script"></a>Configuración única (usando secuencias de comandos)

Debe seguir el conjunto de instrucciones para realizar la instalación mediante una secuencia de comandos de PowerShell que toma el tiempo mínimo de instalación, pero utiliza los valores predeterminados más permitidos. Si lo desea, puede seguir las instrucciones de la [configuración manual](mobile-engagement-api-authentication-manual.md) para hacerlo directamente desde el portal de Azure y realizar una configuración más preciso. 

1. Obtener la última versión de Azure PowerShell desde [aquí](http://aka.ms/webpi-azps). Para obtener más información sobre las instrucciones de descarga, puede ver este [vínculo](../powershell-install-configure.md).  

2. Una vez instalada Azure PowerShell, use los comandos siguientes para asegurarse de que tiene instalado el **módulo Azure** :

    una. Asegúrese de que el módulo de PowerShell de Azure está disponible en la lista de módulos disponibles. 
    
        Get-Module –ListAvailable 

    ![Módulos de Azure disponibles][1]
        
    b. Si no encuentra el módulo de PowerShell de Azure en la lista anterior debe ejecutar lo siguiente:
        
        Import-Module Azure 
        
3. Inicio de sesión para el Administrador de recursos de Azure de PowerShell, ejecute el siguiente comando y proporcionar su nombre de usuario y contraseña de su cuenta de Azure: 
        
        Login-AzureRmAccount

4. Si tiene varias suscripciones, a continuación, debe ejecutar los siguientes:

    una. Obtener una lista de todas las suscripciones y copie la SubscriptionId de la suscripción que desea usar. Asegúrese de que esta suscripción es la misma que la que tiene la aplicación de contratación Mobile que va a interactuar con el uso de la API. 

        Get-AzureRmSubscription

    b. Ejecute el siguiente comando proporcionar la SubscriptionId configurar la suscripción para usarse.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Copiar el texto de la secuencia de comandos de [AzureRmServicePrincipalOwner.ps1 de nuevo](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) en el equipo local y guárdelo como un cmdlet de PowerShell (por ejemplo, `APIAuth.ps1`) y ejecutarla `.\APIAuth.ps1`. 
    
6. La secuencia de comandos le pedirá que proporcione una entrada de **principalName**. Proporcione un nombre adecuado que desea usar para crear la aplicación de Active Directory (por ejemplo, APIAuth). 

7. Una vez completada la secuencia de comandos, aparecerán los siguientes cuatro valores que necesitará para autenticar mediante programación con AD así que asegúrese de que copiarlos. 
        
    **TenantId**, **SubscriptionId**, **ApplicationId**y **secreto**.

    Usará TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` y secreto como `{CLIENT_SECRET}`.

    > [AZURE.NOTE] La directiva de seguridad predeterminada puede bloquear ejecuten scripts de PowerShell. Si es así, configurar temporalmente la directiva de ejecución para permitir la ejecución de scripts mediante el siguiente comando:

        > Set-ExecutionPolicy RemoteSigned

8. Le mostramos cómo el conjunto de cmdlets de PS tendrá un aspecto similar. 

    ![][3]

9. En el portal de administración de Azure, compruebe que se ha creado una nueva aplicación de AD con el nombre proporcionado a la secuencia de comandos denominado **principalName** en **que aplicaciones mostrar mi compañía posee**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Pasos para obtener un token válido

1. Llamar a la API con los siguientes parámetros y asegúrese de reemplazar el INQUILINO\_ID, cliente\_ID y cliente\_secreto:

    - **URL de solicitud** como *https://login.microsoftonline.com/ {INQUILINO\_ID} / oauth2 o token*
    - **Encabezado de tipo de contenido de HTTP** como *application/x-www-form-urlencoded*
    - En el **Cuerpo de la solicitud HTTP** como *conceder\_tipo = cliente\_credenciales & client_id = {cliente\_ID} & client_secret = {cliente\_secreto} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    A continuación se muestra una solicitud de ejemplo:

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Esto es una respuesta de ejemplo:

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    En este ejemplo se incluye la codificación URL de los parámetros de entrada, `resource` valor es realmente `https://management.core.windows.net/`. Tenga cuidado también URL codificar `{CLIENT_SECRET}` como puede contener caracteres especiales.

    > [AZURE.NOTE] Para realizar pruebas, puede usar una herramienta de cliente de HTTP como [Fiddler](http://www.telerik.com/fiddler) o [extensión de Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. Ahora en cada llamada API, incluya el encabezado de la solicitud de autorización:

        Authorization: Bearer {ACCESS_TOKEN}

    Si recibe un código de 401 estado devuelto, compruebe el cuerpo de la respuesta, lo puede indicarle que haya caducado el token. En este caso, recibe un nuevo símbolo.

##<a name="using-the-apis"></a>Uso de la API

Ahora que ya tiene un token válido, ya está listo para realizar llamadas de API.

1. En cada solicitud API, debe pasar un token válido, vigente que obtuvo en la sección anterior.

2. Debe conectar algunos parámetros en la convocatoria URI que identifica la aplicación. URI de la solicitud es similar a la siguiente

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    Para obtener los parámetros, haga clic en el nombre de la aplicación y haga clic en paneles y se verá una página como la siguiente con todos los parámetros de 3.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** el nombre de su grupo de recursos se va a **MobileEngagement** a menos que cree uno nuevo. 

    ![Parámetros de URI de API de compromiso de móvil][2]

>[AZURE.NOTE] <br/>
>1. Omitir la dirección de raíz de la API mientras se tratase para las API anterior.<br/>
>2. Si ha creado la aplicación con Azure clásica portal debe usar el nombre de recurso de aplicación que es distinto del nombre de la aplicación. Si ha creado la aplicación en el Portal de Azure debe usar el nombre de la aplicación propiamente dicho (no hay ninguna distinción entre el nombre de recurso de aplicación y aplicación para aplicaciones creadas en el nuevo portal).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



