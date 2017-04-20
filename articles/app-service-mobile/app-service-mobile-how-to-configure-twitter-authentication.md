<properties
    pageTitle="Cómo configurar la autenticación de Twitter para la aplicación de servicios de aplicación"
    description="Obtenga información sobre cómo configurar la autenticación de Twitter para la aplicación de servicios de aplicación."
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

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar el servicio de aplicación de Azure para usar Twitter como un proveedor de servicios de autenticación.

Para completar el procedimiento descrito en este tema, debe tener una cuenta de Twitter que tiene un número de teléfono y dirección de correo electrónico superiores. Para crear una nueva cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrar la aplicación con Twitter


1. Inicie sesión en el [portal de Azure]y vaya a la aplicación. Copie la **dirección URL**. Que usa para configurar la aplicación de Twitter.

2. Vaya al sitio Web de [Desarrolladores de Twitter] , inicie sesión con sus credenciales de cuenta de Twitter y haga clic en **Crear nueva aplicación**.

3. Escriba el **nombre** y una **Descripción** para la nueva aplicación. Pegue el de su aplicación **dirección URL** para el valor de **sitio Web** . A continuación, para la **Dirección URL de devolución de llamada**, pegue la **Dirección URL de devolución de llamada** que copió anteriormente. Esta es la puerta de enlace de aplicación Mobile anexada con la ruta de acceso, _/.auth/login/twitter/callback_. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Asegúrese de que está usando el esquema HTTPS.

3.  En la parte inferior de la página, lea y acepte los términos. A continuación, haga clic en **crear una aplicación de Twitter**. Esto registra la aplicación se muestra los detalles de la aplicación.

4. Haga clic en la pestaña **configuración** , active **Permitir que se utiliza para iniciar sesión con Twitter esta aplicación**y después haga clic en **Configuración de actualización**.

5. Seleccione la ficha de **claves y Tokens de acceso** . Tome nota de los valores de **Consumidor clave (API)** y **secreto comercial (API secreta)**.

    > [AZURE.NOTE] El secreto de consumidor es una credencial de seguridad importantes. No compartir este secreto con cualquier persona o distribuir con la aplicación.


## <a name="secrets"> </a>Twitter agregar información a la aplicación

13. En el [portal de Azure], vaya a la aplicación. Haga clic en **configuración**y luego **autenticación / autorización**.

14. Si la autenticación / autorización característica no está habilitada, activar el conmutador **en**.

15. Haga clic en **Twitter**. Pegue el identificador de aplicación y el secreto de aplicación valores que obtuvo previamente. A continuación, haga clic en **Aceptar**.

    ![][1]

    Aplicación de servicio proporciona autenticación de forma predeterminada, pero no restringe el acceso autorizado a la API y el contenido del sitio. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio para que solo los usuarios autenticados por Twitter, establezca **acción que se ejecuta cuando no se autentica la solicitud** en **Twitter**. Esto requiere que todas las solicitudes de autenticar y todas las solicitudes no autenticadas se redirigen a Twitter para la autenticación.

17. Haga clic en **Guardar**.

Ya está listo para usar Twitter para la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
