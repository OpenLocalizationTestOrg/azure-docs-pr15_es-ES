<properties
    pageTitle="Cómo configurar la autenticación de Facebook para la aplicación de servicios de aplicación"
    description="Obtenga información sobre cómo configurar la autenticación de Facebook para la aplicación de servicios de aplicación."
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

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar el servicio de aplicación de Azure para usar Facebook como un proveedor de servicios de autenticación.

Para completar el procedimiento descrito en este tema, debe tener una cuenta de Facebook que tiene una dirección de correo electrónico comprobado y un número de teléfono móvil. Para crear una nueva cuenta de Facebook, vaya a [facebook.com].

## <a name="register"> </a>Registrar la aplicación con Facebook

1. Inicie sesión en el [portal de Azure]y vaya a la aplicación. Copie la **dirección URL**. Que usa para configurar la aplicación de Facebook.

2. En otra ventana del explorador, vaya al sitio Web de [Desarrolladores de Facebook] e iniciar sesión con sus credenciales de cuenta de Facebook.

3. (Opcional) Si usted no ha registrado, haga clic en **aplicaciones** > **registrar como desarrollador**, a continuación, acepte la directiva y siga los pasos de registro.

4. Haga clic en **Mis aplicaciones** > **Agregar una nueva aplicación** > **sitio Web** > **Omitir y crear ID de aplicación**. 

5. En **Nombre para mostrar**, escriba un nombre único para la aplicación, su **Correo electrónico de contacto**, elija una **categoría** para la aplicación, a continuación, haga clic en **Crear ID de aplicación** y completar la comprobación de seguridad. Se abrirá el panel del programador para la nueva aplicación de Facebook.

6. "Inicio de sesión de Facebook", haga clic en **Empezar**. Agregar de la aplicación **Redirección URI** **válido OAuth**redirigir URI y luego haga clic en **Guardar cambios**. 

    > [AZURE.NOTE] El redireccionamiento URI es la dirección URL de la aplicación anexada con la ruta de acceso, _/.auth/login/facebook/callback_. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Asegúrese de que está usando el esquema HTTPS.

6. En el panel de navegación izquierdo, haga clic en **configuración**. En el campo **Secreto de aplicación** , haga clic en **Mostrar**, escriba su contraseña si solicitado, a continuación, tome nota de los valores del **Identificador de aplicación** y **Secreto de aplicación**. Utilice estos más adelante para configurar la aplicación en Azure.

    > [AZURE.IMPORTANT] El secreto de aplicación es una credencial de seguridad importantes. No compartir este secreto con cualquier persona o distribuir una aplicación de cliente.

7. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar a otras cuentas de Facebook, haga clic en **Revisar de la aplicación** y permiten **hacer < - aplicación-nombre > público** permitir el acceso de público general utilizando la autenticación de Facebook.

## <a name="secrets"> </a>Información de Facebook de agregar a la aplicación

1. En el [portal de Azure], vaya a la aplicación. Haga clic en **configuración de** > **autenticación / autorización**y asegúrese de que la **Aplicación de servicio de autenticación** está **activada**.

2. Haga clic en **Facebook**, pegue los valores de ID de aplicación y el secreto de la aplicación que ha obtenido previamente, opcionalmente, habilite los ámbitos necesarios para la aplicación y haga clic en **Aceptar**.

    ![][0]

    Aplicación de servicio proporciona autenticación de forma predeterminada, pero no restringe el acceso autorizado a la API y el contenido del sitio. Debe autorizar a los usuarios en el código de la aplicación.

3. (Opcional) Para restringir el acceso al sitio para que solo los usuarios autenticados mediante Facebook, establecer **acción para cuando no se autentica la solicitud** a **Facebook**. Esto requiere que todas las solicitudes de autenticar y todas las solicitudes no autenticadas se redirigen a Facebook para la autenticación.

4. Cuando haya terminado de configurar la autenticación, haga clic en **Guardar**.

Ya está listo para usar Facebook para la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de Azure]: https://portal.azure.com/
