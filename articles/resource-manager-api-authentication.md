<properties 
   pageTitle="Autenticación de Active Directory y el Administrador de recursos | Microsoft Azure"
   description="Guía del desarrollador de autenticación con la API del Administrador de recursos de Azure y Active Directory para integrar una aplicación con otras suscripciones de Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Cómo usar Azure Active Directory y el Administrador de recursos para administrar los recursos de cliente

## <a name="introduction"></a>Introducción

Si es un desarrollador de software que necesita para crear una aplicación que administra los recursos de Azure del cliente, este tema muestra cómo autenticar con la API del Administrador de recursos de Azure y obtener acceso a los recursos de otras suscripciones. 

La aplicación puede tener acceso a la API del Administrador de recursos de par de maneras:

1. **Usuario + acceso de la aplicación**: para las aplicaciones que tienen acceso a los recursos en nombre de un usuario que ha iniciado sesión. Este método funciona para aplicaciones, como aplicaciones web y herramientas de línea de comandos, que tratan con solo "administración interactivo" de los recursos de Azure.
1. **Acceso de solo aplicación**: para las aplicaciones que se ejecutan servicios daemon y las tareas programadas. Identidad de la aplicación se le ha concedido acceso directo a los recursos. Este método funciona para las aplicaciones que necesitan acceso a largo plazo"sin conexión" en Azure.

Este tema proporciona instrucciones paso a paso para crear una aplicación que emplea ambos métodos autorización. Muestra cómo realizar cada paso con la API de REST o C#. La aplicación de ASP.NET MVC completa está disponible en [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Todo el código de este tema se ejecuta como una aplicación web que puede probar en [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>¿Qué significa la aplicación web

La aplicación web:

1. Signos de un usuario de Azure.
2. Le pide al usuario para conceder el acceso de la aplicación web para el Administrador de recursos.
3. Obtiene el usuario + token de acceso de la aplicación para obtener acceso a Administrador de recursos.
4. Utiliza el token (desde el paso 3) llame al administrador de recursos y asignar a principal del servicio de la aplicación a un rol en la suscripción, lo cual proporciona el acceso a largo plazo de aplicación a la suscripción.
5. Obtiene acceso de solo aplicación token.
6. Utiliza token (desde el paso 5) para administrar los recursos en la suscripción a través del Administrador de recursos.

Aquí es el flujo de llevar a cabo de la aplicación web.

![Flujo de autenticación del Administrador de recursos](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como un usuario, proporciona el identificador de suscripción para la suscripción que desea usar:

![proporciona la identificación de suscripción](./media/resource-manager-api-authentication/sample-ux-1.png)

Seleccione la cuenta que desea usar para iniciar sesión.

![Seleccione la cuenta](./media/resource-manager-api-authentication/sample-ux-2.png)

Proporcionar sus credenciales.

![proporcionar credenciales](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceder acceso de aplicación a las suscripciones de Azure:
 
![Conceder acceso](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Administrar suscripciones conectados:

![Conectar la suscripción](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Registrar la aplicación

Antes de comenzar a codificar, registrar la aplicación web con Azure Active Directory (AD). El registro de aplicación, crea una identidad central para su aplicación en Azure AD. Contiene información básica sobre la aplicación como identificador de cliente de OAuth, direcciones URL de la respuesta y las credenciales de la aplicación que se utiliza para autenticar y obtener acceso a las API de administrador de recursos de Azure. El registro de aplicación también registra los diversos permisos delegados que necesita la aplicación para obtener acceso a Microsoft APIs en nombre del usuario. 

Dado que la aplicación tiene acceso a otra suscripción, debe configurar como una aplicación de varios inquilino. Para pasar la validación, proporcione un dominio de Active Directory. Para ver los dominios de Active Directory, inicie sesión en el [portal de clásico](https://manage.windowsazure.com). Seleccione Active Directory y, a continuación, seleccione **dominios**.

En el ejemplo siguiente se muestra cómo registrar la aplicación con PowerShell de Azure. Debe tener la última versión (agosto de 2016) de PowerShell de Azure para este comando funcione. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
Para iniciar sesión la aplicación de AD, necesita el identificador de la aplicación y la contraseña. Para ver el identificador de aplicación que devuelve el comando anterior, use:

    $app.ApplicationId

En el ejemplo siguiente se muestra cómo registrar la aplicación mediante CLI de Azure. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Los resultados incluyen el ID, que necesita para autenticar como la aplicación.

### <a name="optional-configuration---certificate-credential"></a>Configuración opcional: credenciales de certificado

Azure AD también es compatible con las credenciales de certificado para aplicaciones: crear un certificado autofirmado, mantener la clave privada y agregar la clave pública para el registro de aplicación de Azure AD. Para la autenticación, la aplicación envía una carga pequeña a Azure AD ha iniciado sesión con su clave privada y Azure AD valida la firma mediante la clave pública que registrado.

Para obtener información sobre cómo crear una aplicación de AD con un certificado, vea [Usar PowerShell de Azure para crear un servicio principal para tener acceso a recursos](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) o [Uso Azure CLI para crear un servicio principal para tener acceso a los recursos](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>Obtener el identificador de inquilinos de Id. de suscripción

Para solicitar un símbolo que puede utilizarse para llamar a Administrador de recursos, la aplicación debe conocer el identificador del inquilino del inquilino Azure AD que hospeda la suscripción de Azure. Más probable es que los usuarios saben sus identificadores de suscripción, pero es posible que no sepan a su inquilino identificadores de Active Directory. Para obtener el identificador del usuario inquilino, pídale al usuario para el identificador de la suscripción. Proporcionar esa id de suscripción al enviar una solicitud de acerca de la suscripción:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

La solicitud se produce un error porque el usuario no se ha iniciado todavía, pero puede recuperar el identificador de inquilinos de la respuesta. En esa excepción recuperar el identificador del inquilino desde el valor de encabezado de respuesta para **Autenticar WWW**. Consulte esta implementación en el método [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obtener usuario + token de acceso de la aplicación

La aplicación redirige al usuario a Azure AD con un OAuth 2.0 autorizar solicitar - autenticar las credenciales del usuario y obtener un código de autorización. La aplicación, utiliza el código de autorización para obtener un acceso token para el Administrador de recursos. El método [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) crea la solicitud de autorización.

Este tema muestra las solicitudes de la API de REST para autenticar al usuario. También puede usar bibliotecas auxiliares para realizar la autenticación en el código. Para obtener más información sobre estas bibliotecas, vea [Bibliotecas de autenticación de Azure Active Directory](./active-directory/active-directory-authentication-libraries.md). Para obtener instrucciones sobre la integración de la administración de identidades en una aplicación, consulte la [Guía del desarrollador de Azure Active Directory](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Solicitud de AUTH (OAuth 2.0)

Emitir una abrir identificador conectar/OAuth2.0 autorizar solicitud al extremo autorizar de Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Los parámetros de cadena de consulta que están disponibles para esta solicitud se describen en el tema de [un código de autorización de la solicitud](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

En el ejemplo siguiente se muestra cómo solicitar autorización de OAuth2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica el usuario y, si es necesario, pregunta al usuario para conceder permiso a la aplicación. Devuelve el código de autorización a la dirección URL de respuesta de la aplicación. O bien según la response_mode solicitado, Azure AD envía los datos de cadena de consulta o como entrada de datos.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Solicitud de AUTH (ID abrir conectar)

Si no solo desea tener acceso al administrador de recursos de Azure en nombre del usuario, pero también permiten al usuario iniciar sesión en la aplicación con su cuenta de Azure AD, emitir una abrir identificador conectar autorizar solicitud. Con abrir identificador Connect, la aplicación también recibe una id_token de Azure AD que puede usar la aplicación para el usuario de inicio de sesión.

Los parámetros de cadena de consulta que están disponibles para esta solicitud se describen en el tema de [enviar la solicitud de inicio de sesión](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Es una solicitud de abrir conectar de Id. de ejemplo:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica el usuario y, si es necesario, pregunta al usuario para conceder permiso a la aplicación. Devuelve el código de autorización a la dirección URL de respuesta de la aplicación. O bien según la response_mode solicitado, Azure AD envía los datos de cadena de consulta o como entrada de datos.

Es un ejemplo de respuesta conectar de identificador abierto:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Solicitud de token (OAuth2.0 código conceder flujo)

Ahora que la aplicación ha recibido el código de autorización de Azure AD, es hora de obtener el acceso token para el Administrador de recursos de Azure.  Registrar un OAuth2.0 código conceder Token solicitar al extremo Azure AD Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Los parámetros de cadena de consulta que están disponibles para esta solicitud se describen en el tema [usar el código de autorización](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

En el ejemplo siguiente se muestra una solicitud de token de concesión de código con credenciales de contraseña:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Cuando se trabaja con las credenciales de certificado, cree un JSON Web Token (JWT) y el inicio de sesión (RSA SHA256) con la clave privada de credenciales de certificado de la aplicación. Los tipos de notificación para el token se muestran en [notificaciones del token JWT](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Para referencia, vea el [código de Active Directory Auth biblioteca (. NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para firmar cliente aserción JWT tokens.

Vea las [especificaciones conectar de identificador abierto](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obtener más información sobre la autenticación de cliente. 

En el ejemplo siguiente se muestra una solicitud de token de concesión de código con credenciales de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Conceder a una respuesta de ejemplo para el código de token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Controlar la respuesta de token de concesión de código

Una respuesta correcta token contiene (usuario + aplicación) token de acceso para el Administrador de recursos de Azure. La aplicación usa este token de acceso para tener acceso al administrador de recursos en nombre del usuario. La duración del token de acceso emitido por Azure AD es una hora. No es probable que la aplicación web necesita renovar (usuario + aplicación) token de acceso. Si es necesario renovar el token de acceso, use el token de actualización que recibe la aplicación en la respuesta de token. Publique una solicitud OAuth2.0 Token al extremo Azure AD Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Se describen los parámetros para usar con la solicitud de actualización en [actualizar el token de acceso](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

En el ejemplo siguiente se muestra cómo usar la actualización token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Aunque tokens de actualización pueden usarse para obtener nuevos tokens de acceso para el Administrador de recursos de Azure, no son adecuadas para el acceso sin conexión por la aplicación. La duración de tokens actualización está limitada y actualización tokens se enlazan al usuario. Si el usuario deja la organización, pierde el acceso de la aplicación utilizando el token de actualización. Este enfoque no es adecuado para las aplicaciones que se usan los equipos a administrar sus recursos de Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Comprobar si el usuario puede asignar acceso a la suscripción

La aplicación ahora tiene un token de acceso de administrador de recursos de Azure en nombre del usuario. El siguiente paso es conectar la aplicación a la suscripción. Después de conectar, la aplicación puede administrar las suscripciones incluso cuando el usuario no está presente (acceso sin conexión a largo plazo). 

Para que cada suscripción para conectarse, llame a los [permisos de administrador de recursos de lista](https://msdn.microsoft.com/library/azure/dn906889.aspx) API para determinar si el usuario tiene derechos de administración de acceso para la suscripción.

El método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) de la aplicación de ejemplo de ASP.NET MVC implementa esta llamada.

En el ejemplo siguiente se muestra cómo solicitar permisos de un usuario en una suscripción. 83cfe939-2402-4581-b761-4f59b0a041e4 es el identificador de la suscripción.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Es un ejemplo de la respuesta para obtener los permisos de usuario de suscripción:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Los permisos API devuelve varios permisos. Cada permiso consta de acciones permitidas (acciones) y no permitidas acciones (notactions). Si está presente en la lista de acciones permitidas de cualquier permiso una acción y no están presentes en la lista de notactions de permiso, el usuario puede llevar a cabo esta acción. **Microsoft.Authorization/RoleAssignments/Write** es la acción que otorga acceso derechos de administración. La aplicación debe analizar el resultado de permisos para buscar una coincidencia de regex en esta cadena de acción en las acciones y notactions de cada permiso.

## <a name="get-app-only-access-token"></a>Obtener acceso de solo aplicación token

Ahora, sabrá si el usuario puede asignar acceso a la suscripción de Azure. Son los siguientes pasos:

1. Asignar el rol RBAC correspondiente a la identidad de la aplicación de la suscripción.
2. Validar la asignación de acceso consultando los permisos de la aplicación de la suscripción o accediendo usando solo aplicación token de administrador de recursos.
1. Grabar la conexión en la estructura de datos de aplicaciones "conectada suscripciones" - conservar el identificador de la suscripción.

Echemos un vistazo más de cerca en el primer paso. Para asignar el rol RBAC correspondiente a la identidad de la aplicación, debe determinar:

- El identificador de objeto de identidad de la aplicación en Azure Active Directory del usuario
- El identificador de la función RBAC que requiere la aplicación de la suscripción

Cuando la aplicación autentica a un usuario de un anuncio de Azure, crea un objeto de servicio principal para la aplicación en ese Azure AD. Azure permite funciones RBAC asignarse a principales de servicio para conceder acceso directo a las aplicaciones correspondientes en Azure recursos. Esta acción es exactamente lo desea. Consulta de la API de Azure AD gráfico para determinar el identificador de la entidad de seguridad de servicio de la aplicación en el usuario que ha iniciado sesión está Azure AD.

Solo tiene un token de acceso para el Administrador de recursos de Azure: necesita un nuevo token de acceso para llamar a la API de Azure AD Graph. Todas las aplicaciones de Azure AD tiene permiso para consultar su propio objeto principal de servicio, por lo que un token de acceso de solo aplicación es suficiente.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obtener acceso de solo aplicación token API de Azure AD Graph

Para autenticar la aplicación y obtener un símbolo de la API de Azure AD Graph, emitir una solicitud de token de flujo de cliente credencial conceder OAuth2.0 al extremo de token de Azure AD (**https://login.microsoftonline.com/ {directory_domain_name} / OAuth2/Token**).

El método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) de la aplicación de ejemplo de ASP.net MVC Obtiene un acceso de solo aplicación token API de gráfico con el Active Directory Authentication Library para .NET.

Los parámetros de cadena de consulta que están disponibles para esta solicitud se describen en el tema de la [solicitud de un Token de acceso](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Una solicitud de ejemplo para las credenciales de cliente conceder token: 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Una respuesta de ejemplo para la credencial del cliente conceder token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtener el Id. de objeto de capital de servicio de aplicación de Azure AD del usuario

Ahora, use el token de acceso de solo aplicación para consultar las [identidades de servicio de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API para determinar el identificador de objeto de servicio de la aplicación principal en el directorio.

El método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) de la aplicación de ejemplo de ASP.net MVC implementa esta llamada.

En el ejemplo siguiente se muestra cómo solicitar a principal del servicio de la aplicación. a0448380-c346-4f9f-b897-c18733de9394 es el identificador de cliente de la aplicación.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

En el ejemplo siguiente se muestra una respuesta a la solicitud de servicio de la aplicación principal 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obtener el identificador de la función RBAC de Azure

Para asignar el rol RBAC correspondiente a su servicio principal, debe determinar el identificador de la función RBAC de Azure.

La función RBAC adecuada para la aplicación:

- Si su aplicación sólo supervisa la suscripción, sin realizar cambios, requiere permisos de lector solo en la suscripción. Asignar la función **lector** .
- Si la aplicación administra Azure la suscripción, crear, modificar o eliminar entidades, requiere uno de los permisos de colaborador.
  - Para administrar un determinado tipo de recurso, asignar los roles de colaborador específicos del recurso (Máquina Virtual colaborador, colaborador de red Virtual, colaborador de la cuenta de almacenamiento, etcetera)
  - Para administrar cualquier tipo de recurso, asignar el rol de **Colaborador** .

La asignación de roles para la aplicación está visible para los usuarios, así que seleccione la privilegios mínimos necesarios.

Llamar a la [API de definición de rol de administrador de recursos](https://msdn.microsoft.com/library/azure/dn906879.aspx) para todos los roles de Azure RBAC y búsqueda de la lista, luego, la iteración en el resultado para buscar la definición de la función deseada por nombre.

El método [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) de la aplicación de ejemplo de ASP.net MVC implementa esta llamada.

En el ejemplo de solicitud siguiente se muestra cómo obtener el identificador de la función RBAC de Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb es el identificador de la suscripción.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La respuesta está en el siguiente formato: 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

No es necesario llamar a esa API de forma periódica. Una vez que haya determinado el GUID conocido de la definición de rol, puede crear el identificador de la definición de rol como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Estos son los GUID conocidos de funciones integradas utilizados con frecuencia:

| Función | GUID |
| ----- | ------ |
| Lector | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Colaborador | b24988ac-6180-42a0-ab88-20f7382dd24c
| Colaborador de máquina virtual | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Una red virtual colaborador | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Colaborador de la cuenta de almacenamiento | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Sitio Web colaborador | de139f84-1756-47ae-9be6-808fbbe84772
| Colaborador de Plan de Web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Colaborador SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Colaborador de DB de SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Asignar roles RBAC a aplicación

Todo lo que necesita para asignar el rol RBAC correspondiente a su servicio principal con el [Administrador de recursos crear asignación de roles](https://msdn.microsoft.com/library/azure/dn906887.aspx) API tener.

El método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) de la aplicación de ejemplo de ASP.net MVC implementa esta llamada.

Una solicitud de ejemplo para asignar rol RBAC a la aplicación: 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

En la convocatoria, se usan los siguientes valores:

| GUID | Descripción |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | el identificador de la suscripción
| c3097b31-7309-4c59-b4e3-770f8406bad2 | el identificador de objeto de la entidad de seguridad de servicio de la aplicación
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | el identificador de la función Lector
| 4f87261d-2816-465D-8311-70a27558df4c | un nuevo guid creado para la nueva asignación de roles

La respuesta está en el siguiente formato: 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obtener acceso de solo aplicación token para el Administrador de recursos de Azure

Tiene deseado validar esa aplicación tener acceso a la suscripción, realizar una tarea de prueba en la suscripción con un símbolo de aplicación.

Para obtener un acceso de solo aplicación token, siga las instrucciones de sección [acceda solo aplicación token de Azure AD Graph API](#app-azure-ad-graph), con un valor diferente para el parámetro de recursos: 

    https://management.core.windows.net/

El método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de la aplicación de ejemplo de ASP.NET MVC Obtiene un acceso de solo aplicación token para el Administrador de recursos de Azure con el Active Directory Authentication Library para .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obtener los permisos de la aplicación de suscripción

Para comprobar que la aplicación tenga el acceso deseado en una suscripción de Azure, también puede llamar a la API de [Permisos de administrador de recursos](https://msdn.microsoft.com/library/azure/dn906889.aspx) . Este enfoque es similar a cómo se determina si el usuario tiene derechos de administración de acceso para la suscripción. Sin embargo, esta vez, llame a la API de permisos con el token de acceso de solo aplicación que ha recibido en el paso anterior.

El método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de la aplicación de ejemplo de ASP.NET MVC implementa esta llamada.

## <a name="manage-connected-subscriptions"></a>Administrar suscripciones conectadas

Cuando se asigna el rol RBAC correspondiente al servicio de la aplicación principal de la suscripción, la aplicación puede mantener supervisión y administrar mediante tokens de acceso de solo aplicación para el Administrador de recursos de Azure.

Si el propietario de la suscripción Quita la asignación de roles de la aplicación con el portal clásico o las herramientas de línea de comandos, la aplicación ya no está teniendo acceso a la suscripción. En ese caso, debe notificar al usuario que la conexión con la suscripción se ha interrumpido desde fuera de la aplicación y les proporcionan una opción para la conexión "reparar". "Reparación" ¿simplemente volver a crear la asignación de roles que se eliminó sin conexión.

Como ha habilitado el usuario conectarse suscripciones a la aplicación, debe permitir al usuario desconectar suscripciones demasiado. Desde un punto de vista de la administración de acceso, desconectar significa eliminar la asignación de roles tiene principal del servicio de la aplicación de la suscripción. Si lo desea, puede quitado demasiado cualquier estado en la aplicación de la suscripción. Solo los usuarios con permiso de administración de acceso de la suscripción son capaces de desconectar la suscripción.

El [método RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) de la aplicación de ejemplo de ASP.net MVC implementa esta llamada.

Eso es todo: los usuarios pueden ahora fácilmente conectarse y administrar sus suscripciones Azure con la aplicación.

