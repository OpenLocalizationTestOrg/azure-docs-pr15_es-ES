<properties 
    pageTitle="Cómo autorizar cuentas de programador mediante OAuth 2.0 en administración de la API de Azure" 
    description="Obtenga información sobre cómo autorizar a los usuarios con OAuth 2.0 en administración de la API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Cómo autorizar cuentas de programador mediante OAuth 2.0 en administración de la API de Azure

Muchas API admiten [OAuth 2.0](http://oauth.net/2/) para proteger la API y asegurarse de que solo los usuarios válidos tienen acceso, y sólo pueden acceder a los recursos a los que está autorizados. Para usar la consola de desarrollador interactiva de la administración de API de Azure con esas API, el servicio le permite configurar la instancia de servicio para trabajar con su OAuth 2.0 API habilitado.

## <a name="prerequisites"> </a>Los requisitos previos

Esta guía le muestra cómo configurar la instancia de servicio de administración de la API para usar OAuth 2.0 autorización para cuentas de desarrollador, pero no muestra cómo configurar un proveedor de servicios de OAuth 2.0. La configuración de cada proveedor OAuth 2.0 es diferente, aunque los pasos son similares y los fragmentos necesarios de información usada en la configuración de la instancia del servicio de administración de la API de OAuth 2.0 son los mismos. Este tema muestra ejemplos de uso de Azure Active Directory como un proveedor de servicios de OAuth 2.0.

>[AZURE.NOTE] Para obtener más información sobre cómo configurar 2.0 OAuth con Azure Active Directory, vea el ejemplo de [DotNet de GraphAPI de Web App][] .

## <a name="step1"> </a>Configurar un servidor de autorización de OAuth 2.0 en administración de la API

Para empezar, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher.

![Portal de Publisher][api-management-management-console]

>[AZURE.NOTE] Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

Haga clic en **seguridad** en el menú de **Administración de la API** de la izquierda, haga clic en **OAuth 2.0**y, a continuación, haga clic en **Agregar servidor de autorización**.

![OAuth 2.0][api-management-oauth2]

Después de hacer clic en **Agregar servidor de autorización**, se muestra el nuevo formulario de servidor de autorización.

![Nuevo servidor][api-management-oauth2-server-1]

En los campos **nombre** y **Descripción** , escriba un nombre y una descripción opcional. 

>[AZURE.NOTE] Estos campos se usan para identificar el servidor de autorización de OAuth 2.0 dentro de la instancia actual del servicio de administración de la API y sus valores no provienen desde el servidor de OAuth 2.0.

Escriba la **dirección URL de la página de registro de cliente**. Esta página es donde los usuarios pueden crear y administrar sus cuentas y varía según el proveedor de OAuth 2.0 utilizado. La **dirección URL de la página de registro de cliente** se redirige a la página que los usuarios pueden usar para crear y configurar sus propias cuentas para los proveedores de OAuth 2.0 que admiten la administración de usuarios de cuentas. Algunas organizaciones no configura o use esta función incluso si lo admite el proveedor de OAuth 2.0. Si su proveedor de OAuth 2.0 no tiene la administración de usuarios de cuentas configurados, escriba una URL de marcador de posición aquí como la dirección URL de su empresa o una dirección URL como `https://placeholder.contoso.com`.

La siguiente sección del formulario contiene la configuración de **código de autorización conceder tipos**, **dirección URL del extremo de autorización**y **método de solicitud de autorización** .

![Nuevo servidor][api-management-oauth2-server-2]

Especifique el **código de autorización conceder tipos** marcando los tipos deseados. **Código de autorización** se especifica de forma predeterminada.

Escriba la **dirección URL del extremo de autorización**. Para Azure Active Directory, esta dirección URL será similar a la siguiente dirección URL, donde `<client_id>` se sustituye por el id de cliente que identifica la aplicación en el servidor de OAuth 2.0.

    https://login.windows.net/<client_id>/oauth2/authorize

El **método de solicitud de autorización** especifica cómo se envía la solicitud de autorización en el servidor de OAuth 2.0. **Obtener** está activada de forma predeterminada.

La siguiente sección es donde se especifican la **dirección URL del extremo de Token**, **métodos de autenticación de cliente**, **token de acceso de método de envío**y **ámbito predeterminado** .

![Nuevo servidor][api-management-oauth2-server-3]

Para un servidor de Azure Active Directory OAuth 2.0, la **dirección URL del extremo de Token** tendrá el siguiente formato, donde `<APPID>` tiene el formato de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

La configuración predeterminada de **los métodos de autenticación de cliente** es **básica**y **token de acceso de método de envío** es **el encabezado de autorización**. Estos valores se configuran en esta sección del formulario, junto con el **ámbito predeterminado**.

La sección de **credenciales de cliente** contiene el **Identificador de cliente** y el **secreto de cliente**, que se obtienen durante el proceso de creación y configuración de su servidor de OAuth 2.0. Una vez que se especifican el **Identificador de cliente** y el **secreto de cliente** , se genera la **redirect_uri** para el **código de autorización** . Este URI se utiliza para configurar la dirección URL de respuesta en la configuración del servidor OAuth 2.0.

![Nuevo servidor][api-management-oauth2-server-4]

Si se establece el **código de autorización conceder tipos** a **contraseña de propietario de recursos**, la sección de **credenciales de contraseña de propietario de recursos** se utiliza para especificar las credenciales; en caso contrario, puede dejar en blanco.

![Nuevo servidor][api-management-oauth2-server-5]

Una vez completado el formulario, haga clic en **Guardar** para guardar la configuración del servidor de autorización de API administración OAuth 2.0. Una vez que se guarda la configuración del servidor, puede configurar las API para usar esta configuración, como se muestra en la siguiente sección.

## <a name="step2"> </a>Configurar una API para usar la autorización del usuario OAuth 2.0

Haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda, haga clic en el nombre de la API deseada, haga clic en **seguridad**y, a continuación, active la casilla de **OAuth 2.0**.

![Autorización del usuario][api-management-user-authorization]

Seleccione el **servidor de autorización** de deseado desde la lista desplegable y haga clic en **Guardar**.

![Autorización del usuario][api-management-user-authorization-save]

## <a name="step3"> </a>Probar la autorización del usuario OAuth 2.0 en el Portal de programadores

Una vez que haya configurado el servidor de autorización de OAuth 2.0 y configurado su API para usar ese servidor, puede probar, vaya al Portal de programadores y llamar a una API.  En el menú superior derecha, haga clic en **portal de programadores** .

![Portal de programadores][api-management-developer-portal-menu]

Haga clic en **las API de** en el menú superior y seleccione **API de eco**.

![API de eco][api-management-apis-echo-api]

>[AZURE.NOTE] Si tiene solo una API configurada o visible a su cuenta, haga clic en las API de lleva directamente a las operaciones para la API.

Seleccione la operación de **Obtener recursos** , haga clic en **Abrir la consola**y, a continuación, seleccione el **código de autorización** de la lista desplegable.

![Abra la consola][api-management-open-console]

Cuando se selecciona el **código de autorización** , se muestra una ventana emergente con el formulario de inicio de sesión del proveedor OAuth 2.0. En este ejemplo se proporciona el formulario de inicio de sesión de Azure Active Directory.

>[AZURE.NOTE] Si tiene elementos emergentes deshabilitadas se le pedirá para habilitarlos mediante el explorador. Después de habilitarlas, seleccionar **código de autorización** nuevo y el formulario de inicio de sesión se mostrarán.

![Inicia sesión][api-management-oauth2-signin]

Una vez que haya iniciado sesión, los **encabezados de la solicitud** se rellena con un `Authorization : Bearer` encabezado que autoriza la solicitud.

![Token de encabezado de la solicitud][api-management-request-header-token]

En este momento puede configurar los valores que desee para el resto de parámetros y enviar la solicitud. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de la administración de la API y OAuth 2.0, vea el siguiente vídeo y que acompaña a [artículo](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introducción a la administración de la API de Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[DotNet de GraphAPI de Web App]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

