<properties
    pageTitle="Cómo configurar la autenticación de Google para la aplicación de servicios de aplicación"
    description="Obtenga información sobre cómo configurar la autenticación de Google para la aplicación de servicios de aplicación."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar el servicio de aplicación de Azure para usar Google como un proveedor de servicios de autenticación.

Para completar el procedimiento descrito en este tema, debe tener una cuenta de Google que tenga una dirección de correo electrónico superiores. Para crear una nueva cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrar la aplicación de Google

1. Inicie sesión en el [portal de Azure]y vaya a la aplicación. Copie la **dirección URL**, que se utiliza más tarde para configurar la aplicación de Google.

2. Vaya al sitio Web de [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) , inicie sesión con sus credenciales de cuenta de Google, haga clic en **Crear proyecto**, proporcione un **nombre de proyecto**, haga clic en **crear**.

3. **API Social** haga clic en **Google + API** y **Habilitar**.

4. En el panel de navegación izquierdo, **credenciales** > **OAuth consentimiento pantalla**, a continuación, seleccione la **dirección de correo electrónico**, escriba un **Nombre de producto**y haga clic en **Guardar**.

5. En la ficha **credenciales** , haga clic en **crear credenciales** > **identificador de cliente de OAuth**, a continuación, seleccione **aplicación Web**.

6. Pegue la aplicación servicio **URL** que copió anteriormente en **Autorizados orígenes de JavaScript**y luego pegue el redireccionamiento URI **Autorizados URI redirigir**. La redirección URI es la dirección URL de la aplicación anexada con la ruta de acceso, _/.auth/login/google/callback_. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Asegúrese de que está usando el esquema HTTPS. A continuación, haga clic en **crear**.

7. En la siguiente pantalla, tome nota de los valores del identificador de cliente y secreto de cliente.


    > [AZURE.IMPORTANT]
    El secreto de cliente es una credencial de seguridad importantes. No compartir este secreto con cualquier persona o distribuir una aplicación de cliente.


## <a name="secrets"> </a>Google agregar información a la aplicación

8. En el [portal de Azure], vaya a la aplicación. Haga clic en **configuración**y luego **autenticación / autorización**.

9. Si la autenticación / autorización característica no está habilitada, activar el conmutador **en**.

10. Haga clic en **Google**. Pegue los valores de ID de aplicación y el secreto de la aplicación que ha obtenido previamente y, opcionalmente, habilite los ámbitos de que la aplicación requiere. A continuación, haga clic en **Aceptar**.

    ![][1]

    Aplicación de servicio proporciona autenticación de forma predeterminada, pero no restringe el acceso autorizado a la API y el contenido del sitio. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio para que solo los usuarios autenticados por Google, establecer **acción para cuando no se autentica la solicitud** a **Google**. Esto requiere que todas las solicitudes de autenticar y todas las solicitudes no autenticadas se redirigen a Google para la autenticación.

12. Haga clic en **Guardar**.

Ya está listo para usar Google para la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Azure]: https://portal.azure.com/

