<properties
    pageTitle="Cómo configurar la autenticación de Account de Microsoft para la aplicación de servicios de aplicación"
    description="Obtenga información sobre cómo configurar la autenticación de Account de Microsoft para la aplicación de servicios de aplicación."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Microsoft Account

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar el servicio de aplicación de Azure para usar Account Microsoft como proveedor de servicios de autenticación. 

## <a name="register-microsoft-account"> </a>Registrar la aplicación con la cuenta de Microsoft

1. Inicie sesión en el [portal de Azure]y vaya a la aplicación. Copie la **dirección URL**, que más adelante se usa para configurar la aplicación con Account de Microsoft.

2. Vaya a la página [Mis aplicaciones] de Microsoft Account Developer Center e inicie sesión con su cuenta de Microsoft, si es necesario.

3. Haga clic en **Agregar una aplicación**, a continuación, escriba un nombre de aplicación y haga clic en **crear una aplicación**.

4. Anote el **Identificador de la aplicación**, ya que lo necesitará más adelante. 

5. En "Plataformas", haga clic en **Agregar plataforma** y seleccione "Web".

6. En "Redirigir URI" proporcionar el extremo de la aplicación y después haga clic en **Guardar**. 
 
    >[AZURE.NOTE]El redireccionamiento URI es la dirección URL de la aplicación anexada con la ruta de acceso, _/.auth/login/microsoftaccount/callback_. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Asegúrese de que está usando el esquema HTTPS.

7. "Información confidencial de aplicación", haga clic en **Generar nueva contraseña**. Anote el valor que aparece. Una vez que el usuario sale de la página, no se mostrará nuevamente.


    > [AZURE.IMPORTANT] La contraseña es una credencial de seguridad importantes. No compartir la contraseña con cualquier persona o distribuir una aplicación de cliente.

## <a name="secrets"> </a>Información de agregar la cuenta de Microsoft a la aplicación de servicio de aplicaciones

1. En el [portal de Azure], vaya a la aplicación, haga clic en **configuración**de volver > **autenticación / autorización**.

2. Si la autenticación / autorización característica no está habilitada, cambie **de**.

3. Haga clic en la **Cuenta de Microsoft**. Pegue los valores de ID de aplicación y la contraseña que ha obtenido previamente y, opcionalmente, habilite los ámbitos de que la aplicación requiere. A continuación, haga clic en **Aceptar**.

    ![][1]

    Aplicación de servicio proporciona autenticación de forma predeterminada, pero no restringe el acceso autorizado a la API y el contenido del sitio. Debe autorizar a los usuarios en el código de la aplicación.

4. (Opcional) Para restringir el acceso al sitio para que solo los usuarios autenticados por cuenta de Microsoft, establecer **acción para cuando no se autentica la solicitud** a la **Cuenta de Microsoft**. Esto requiere que todas las solicitudes de autenticar y todas las solicitudes no autenticadas se redirigen a la cuenta de Microsoft para la autenticación.

5. Haga clic en **Guardar**.

Ya está listo para usar Account de Microsoft para la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal de Azure]: https://portal.azure.com/
