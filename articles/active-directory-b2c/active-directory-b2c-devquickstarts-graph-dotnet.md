<properties
    pageTitle="Azure Active Directory B2C: Usar la API de gráfico | Microsoft Azure"
    description="Cómo llamar a la API de gráfico para un inquilino B2C usando una identidad de aplicación para automatizar el proceso."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>B2C de Azure AD: Utilizar la API de gráfico

Inquilinos de Azure Active Directory (AD Azure) B2C suelen ser muy grandes. Esto significa que muchas tareas comunes de administración de inquilinos deben llevarse a cabo mediante programación. Un ejemplo principal es la administración de usuarios. Debe migrar un almacén de usuario existente a un inquilino B2C. Desea hospedar el registro de usuario en su propia página y crear cuentas de usuario en Azure AD en segundo plano. Estos tipos de tareas requieren la capacidad para crear, leer, actualizar y eliminar cuentas de usuario. Puede realizar estas tareas mediante la API de Azure AD Graph.

Para los inquilinos B2C, existen dos modos principales de comunicarse con la API de gráfico.

- Para tareas interactivas, ejecutar una vez, debe actuar como una cuenta de administrador en el inquilino B2C al realizar las tareas. Este modo requiere un administrador que inicie sesión con credenciales ese administrador para poder realizar llamadas a la API de gráfico.
- Para tareas automatizadas, continuas, debe usar algún tipo de cuenta de servicio que proporcione los privilegios necesarios para realizar tareas de administración. En Azure AD, puede hacerlo por registrar una aplicación y la autenticación de Azure AD. Esto se hace mediante un **Identificador de la aplicación** que usa las [credenciales de cliente de OAuth 2.0 concesión](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). En este caso, la aplicación actúa como Sí, no como un usuario, llame a la API de gráfico.

En este artículo, trataremos cómo llevar a cabo el caso de uso automatizado. Para mostrar, vamos a crear un .NET 4.5 `B2CGraphClient` que realiza el usuario crear, leer, actualizar y eliminar operaciones (CRUD). El cliente tendrá una interfaz de línea de comandos de Windows (CLI) que permite invocar varios métodos. Sin embargo, el código se escribe en se comportan de forma interactiva y automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtener a un inquilino de Azure AD B2C

Para poder crear aplicaciones o usuarios o interactúa con Azure AD, necesitará un inquilino de Azure AD B2C y una cuenta de administrador global en el inquilino. Si no tiene un inquilino, [Introducción a Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Registrar una aplicación de servicio en el inquilino

Una vez un inquilino B2C, debe crear una aplicación de servicio mediante cmdlets de PowerShell de Azure AD.
En primer lugar, descargue e instale el [Asistente Microsoft Online Services inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152). A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits de Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Para usar la API de gráfico con el inquilino de B2C, debe registrar una aplicación dedicada con PowerShell. Siga las instrucciones de este artículo para hacerlo. No puede volver a usar las aplicaciones B2C ya existente que registra en el portal de Azure.

Después de instalar el módulo de PowerShell, abra PowerShell y conectarse a su inquilino B2C. Después de ejecutar `Get-Credential`, se le pedirá un nombre de usuario y contraseña, escriba el nombre de usuario y la contraseña de su cuenta de administrador de inquilinos B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de crear la aplicación, debe generar un nuevo **secreto de cliente**.  La aplicación utilizará el secreto de cliente para autenticar a Azure AD y adquirir tokens de acceso. Puede generar un secreto válido de PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

El comando final debe imprimir su nuevo secreto de cliente. Cópiela en un lugar seguro. Necesitará más adelante. Ahora puede crear la aplicación proporcionando el nuevo secreto de cliente como credencial para la aplicación:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Si crea correctamente la aplicación, debe imprimir las propiedades de la aplicación como los anteriores. Necesitará ambos `ObjectId` y `AppPrincipalId`, así que también a copiar esos valores.

Después de crear una aplicación de su inquilino B2C, debe asignar los permisos que necesita para realizar operaciones de CRUD de usuario. Asignar las tres funciones de aplicación: los lectores de directorio (para leer los usuarios), los creadores de directorio (para crear y actualizar los usuarios) y un administrador de la cuenta de usuario (para eliminar usuarios). Estas funciones tienen identificadores conocidos, por lo que puede reemplazar el `-RoleMemberObjectId` parámetro con `ObjectId` desde arriba y ejecute los comandos siguientes. Para ver la lista de todas las funciones de directorio, intente ejecutar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Ahora tiene una aplicación que tiene permiso para crear, leer, actualizar y eliminar los usuarios de su inquilino B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Descargar, configurar y generar el código de ejemplo

En primer lugar, descargue el código de ejemplo y ejecutarlo. A continuación, se tardarán más detalles sobre ella.  Puede [descargar el código de ejemplo como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). También puede clonar en un directorio de su elección:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abrir la `B2CGraphClient\B2CGraphClient.sln` solución de Visual Studio en Visual Studio. En la `B2CGraphClient` del proyecto, abra el archivo `App.config`. Reemplazar la configuración de la tres aplicación con sus propios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A continuación, haga clic en el `B2CGraphClient` solución y volver a generar los datos de ejemplo. Si son correctas, ahora debería tener un `B2C.exe` archivo ejecutable se encuentra en `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Generar operaciones de CRUD de usuario con la API de gráfico

Para utilizar el B2CGraphClient, abra una `cmd` de comandos de Windows solicitar y cambie el directorio a la `Debug` directorio. Vuelva a ejecutar la `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Esta opción mostrará una breve descripción de cada comando. Cada vez que se invoca uno de estos comandos `B2CGraphClient` realiza una solicitud a la API de Azure AD Graph.

### <a name="get-an-access-token"></a>Obtener un token de acceso

Cualquier solicitud de la API de Graph requiere un token de acceso para la autenticación. `B2CGraphClient`usa el código abierto Active Directory autenticación biblioteca (ADAL) para ayudar a adquirir tokens de acceso. ADAL facilita la adquisición de token mediante una API simple y encargarse de algunos detalles importantes, como el almacenamiento en caché de tokens de acceso. No debe usar ADAL para obtener tokens, aunque. También puede obtener tokens diseñando solicitudes HTTP.

> [AZURE.NOTE]
    Este ejemplo de código utiliza v2 ADAL para comunicarse con la API de gráfico.  Debe utilizar ADAL v2 o v3 para obtener el token de acceso que se puede utilizar con la API de Azure AD Graph.

Cuando `B2CGraphClient` se ejecuta, crea una instancia de la `B2CGraphClient` clase. El constructor para esta clase configura un scaffolding ADAL autenticación:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Usaremos el `B2C Get-User` comando como ejemplo. Cuando `B2C Get-User` se invoca sin ningún entradas adicionales, las llamadas CLI el `B2CGraphClient.GetAllUsers(...)` método. Este método llama a `B2CGraphClient.SendGraphGetRequest(...)`, que envía una solicitud HTTP GET a la API de gráfico. Antes de `B2CGraphClient.SendGraphGetRequest(...)` envía GET solicitar, primero obtiene un acceso token usando ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Puede obtener un acceso token para la API de Graph llamando el ADAL `AuthenticationContext.AcquireToken(...)` método. ADAL devuelve un `access_token` que representa la identidad de la aplicación.

### <a name="read-users"></a>Leer los usuarios

Si desea obtener una lista de usuarios u obtener un usuario concreto de la API de gráfico, puede enviar un HTTP `GET` solicitar a los `/users` punto final. Una solicitud de todos los usuarios en un inquilino es similar a esta:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver esta solicitud, ejecute:

 ```
 > B2C Get-User
 ```

Hay dos cosas importantes que tenga en cuenta:

- El token de acceso adquirido a través de ADAL se agrega a la `Authorization` encabezado utilizando la `Bearer` combinación.
- Para B2C inquilinos, debe usar el parámetro de consulta `api-version=1.6`.

Ambos de estos detalles se controlan en la `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Crear cuentas de usuario de consumidor

Al crear cuentas de usuario de su inquilino B2C, puede enviar un HTTP `POST` solicitar a los `/users` punto final:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La mayoría de estas propiedades en esta solicitud es necesarios para crear los usuarios de consumidores. Para obtener más información, haga clic en [aquí](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Tenga en cuenta que el `//` comentarios han incluido para ilustración. No incluirlos en una solicitud de real.

Para ver la solicitud, ejecute uno de los siguientes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

La `Create-User` comando toma un archivo de .json como un parámetro de entrada. Esta página contiene una representación JSON de un objeto de usuario. Hay dos archivos de .json de ejemplo en el código de ejemplo: `usertemplate-email.json` y `usertemplate-username.json`. Puede modificar estos archivos para adaptarla a sus necesidades. Además de los campos obligatorios anteriores, se incluyen varios campos opcionales que puede usar en estos archivos. Obtener más información sobre los campos opcionales puede encontrarse en la [referencia de entidad de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Puede ver cómo se crea la solicitud de publicación en `B2CGraphClient.SendGraphPostRequest(...)`.

- Se asocia un token de acceso a la `Authorization` encabezado de la solicitud.
- Establece `api-version=1.6`.
- Incluye el objeto de usuario JSON en el cuerpo de la solicitud.

> [AZURE.NOTE]
Si las cuentas que desea migrar desde una tienda existente del usuario tiene intensidad de contraseña inferior a la [rotura de contraseña segura aplicada Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), puede deshabilitar el requisito de contraseña segura utilizando la `DisableStrongPassword` valor en el `passwordPolicies` propiedad. Por ejemplo, puede modificar la solicitud del usuario crear indicada anteriormente como sigue: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Actualizar cuentas de usuario de consumidor

Cuando actualiza los objetos de usuario, el proceso es similar a la que se utiliza para crear objetos de usuario. Pero este proceso utiliza el HTTP `PATCH` método:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Intente actualizar un usuario mediante la actualización de los archivos JSON con los nuevos datos. A continuación, puede usar `B2CGraphClient` para ejecutar uno de estos comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspeccionar la `B2CGraphClient.SendGraphPatchRequest(...)` método para obtener más información sobre cómo enviar esta solicitud.

### <a name="search-users"></a>Usuarios de búsqueda

Para buscar usuarios de su inquilino B2C de un par de maneras. Una, con el usuario objeto identificador o ambas, con identificador de inicio de sesión del usuario (por ejemplo, el `signInNames` propiedad).

Ejecute uno de los comandos siguientes para buscar un usuario específico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Estos son algunos ejemplos:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminar usuarios

El proceso para eliminar un usuario es sencillo. Usar HTTP `DELETE` método y construcción la dirección URL con el Id. de objeto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver un ejemplo, escriba este comando y ver la solicitud de eliminación se imprime en la consola:

```
> B2C Delete-User <object-id-of-user>
```

Inspeccionar la `B2CGraphClient.SendGraphDeleteRequest(...)` método para obtener más información sobre cómo enviar esta solicitud.

Puede realizar muchas otras acciones con la API de Azure AD Graph además de administración de usuarios. La [referencia de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) proporciona detalles sobre cada acción, junto con las solicitudes de ejemplo.

## <a name="use-custom-attributes"></a>Usar atributos personalizados

La mayoría de aplicaciones de consumidor necesiten para almacenar algún tipo de información del perfil de usuario personalizada. Una forma puede hacer esto es definir un atributo personalizado en el inquilino B2C. A continuación, puede tratar ese atributo del mismo modo que cualquier otra propiedad que trata en un objeto de usuario. Puede actualizar el atributo, elimine el atributo, la consulta mediante el atributo, enviar el atributo como una notificación de tokens de inicio de sesión y mucho más.

Para definir un atributo personalizado en el inquilino B2C, vea la [referencia de atributo personalizado B2C](active-directory-b2c-reference-custom-attr.md).

Puede ver los atributos personalizados definidos en el inquilino B2C usando `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

El resultado de estas funciones revela los detalles de cada atributo personalizado, como por ejemplo:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Puede usar el nombre completo, como por ejemplo `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como una propiedad de los objetos de usuario.  Actualizar el archivo de .json con la nueva propiedad y un valor de la propiedad y, a continuación, ejecute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Usando `B2CGraphClient`, tiene una aplicación de servicio que puede administrar los usuarios del inquilino B2C mediante programación. `B2CGraphClient`utiliza su propia identidad de la aplicación para autenticar a la API de Azure AD Graph. También se adquiere tokens usando un secreto de cliente. Como incorporar esta funcionalidad en la aplicación, recuerde que algunos puntos clave para las aplicaciones B2C:

- Debe conceder los permisos adecuados en el inquilino de la aplicación.
- Por ahora, debe usar v2 ADAL para obtener el token de acceso. (También puede enviar mensajes de protocolo directamente, sin usar una biblioteca.)
- Cuando llame a la API de gráfico, use `api-version=1.6`.
- Al crear y actualizar usuarios consumidores, algunas propiedades son obligatorios, como se describió anteriormente.

Si tiene alguna pregunta o solicitudes de acciones que desea realizar con la API de gráfico en su inquilino B2C, deja un comentario en este artículo o un problema de archivo en el repositorio de ejemplo de código de GitHub.
