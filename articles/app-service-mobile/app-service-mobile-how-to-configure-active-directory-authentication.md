<properties
    pageTitle="Cómo configurar la autenticación de Azure Active Directory para la aplicación de servicios de aplicación"
    description="Obtenga información sobre cómo configurar la autenticación de Azure Active Directory para la aplicación de servicios de aplicación."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
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

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar los servicios de aplicación de Azure para usar Azure Active Directory como un proveedor de servicios de autenticación.

## <a name="express"> </a>Configurar Azure Active Directory usando la configuración de express

13. En el [portal de Azure], vaya a la aplicación. Haga clic en **configuración**y, a continuación, **Autenticación/autorización**.

14. Si la autenticación / autorización característica no está habilitada, activar el conmutador **en**.

15. Haga clic en **Azure Active Directory**y, a continuación, haga clic en **Express** en **Modo de administración**.

16. Haga clic en **Aceptar** para registrar la aplicación en Azure Active Directory. Se creará un nuevo registro. Si desea elegir un registro existente en su lugar, haga clic en **Seleccionar una aplicación existente** y, a continuación, busque el nombre de un registro creado previamente dentro de su inquilino.
Haga clic en el registro para seleccionarlo y haga clic en **Aceptar**. A continuación, haga clic en **Aceptar** en el módulo de configuración de Azure Active Directory.

    ![][0]

    Aplicación de servicio proporciona autenticación de forma predeterminada, pero no restringe el acceso autorizado a la API y el contenido del sitio. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio para que solo los usuarios autenticados Azure Active Directory, establecer **acción para cuando no se autentica la solicitud de** iniciar sesión **con Azure Active Directory**. Esto requiere que todas las solicitudes de autenticar y todas las solicitudes no autenticadas se redirigen a Azure Active Directory para la autenticación.

17. Haga clic en **Guardar**.

Ya está listo para usar Azure Active Directory para la autenticación en la aplicación.

## <a name="advanced"> </a>(Método alternativo) configure manualmente Azure Active Directory con la configuración avanzada
También puede elegir proporcionar configuración manual. Esta es la mejor solución si es diferente del inquilino con la que iniciar sesión en Azure el inquilino AAD que desea usar. Para completar la configuración, primero debe crear un registro de Azure Active Directory y, a continuación, debe proporcionar algunos de los detalles de registro para la aplicación de servicio.

### <a name="register"> </a>Registrar la aplicación con Azure Active Directory

1. Inicie sesión en el [portal de Azure]y vaya a la aplicación. Copie la **dirección URL**. Que usa para configurar la aplicación de Azure Active Directory.

3. Inicie sesión en el [portal de clásica Azure] y vaya a **Active Directory**.

    ![][2]

4. Seleccione el directorio y, a continuación, seleccione la pestaña de **aplicaciones** en la parte superior. En la parte inferior para crear un nuevo registro de aplicación, haga clic en **Agregar** .

5. Haga clic en **Agregar una aplicación se desarrolla mi organización**.

6. En el Asistente para agregar aplicaciones, escriba un **nombre** para la aplicación y haga clic en el tipo de **Aplicación o Web API de Web** . A continuación, haga clic en para continuar.

7. En el cuadro **Dirección URL de inicio de sesión** , pegue la dirección URL de la aplicación que copió anteriormente. Escriba esa misma dirección URL en el cuadro **URI de ID de aplicación** . A continuación, haga clic en para continuar.

8. Una vez agregada la aplicación, haga clic en la ficha **Configurar** . Editar la **Dirección URL de respuesta** en **el inicio de sesión único** para ser la dirección URL de la aplicación anexada con la ruta de acceso, _/.auth/login/aad/callback_. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Asegúrese de que está usando el esquema HTTPS.

    ![][3]

9. Haga clic en **Guardar**. A continuación, copie el **Identificador de cliente** para la aplicación. Va a configurar la aplicación para utilizarla más adelante.

10. En la barra de comandos de la parte inferior, haga clic en **Los extremos de la vista**y, a continuación, copie la dirección URL del **Documento de metadatos de federación** y descargar ese documento o desplazarse hasta él en un explorador.

11. En el elemento de **EntityDescriptor** raíz, debe ser un atributo **entityID** del formulario `https://sts.windows.net/` seguido de un GUID específico en el inquilino (denominado "inquilino Id."). Copiar este valor: servirá como la **Dirección URL del emisor**. Va a configurar la aplicación para utilizarla más adelante.

### <a name="secrets"> </a>Información de agregar Azure Active Directory a la aplicación

13. En el [portal de Azure], vaya a la aplicación. Haga clic en **configuración**y, a continuación, **Autenticación/autorización**.

14. Si no está habilitada la característica de autenticación o autorización, gire el interruptor en **activado**.

15. Haga clic en **Azure Active Directory**y, a continuación, haga clic en **Avanzadas** , en **Modo de administración**. Pegue el valor del identificador de cliente y la dirección URL de emisor que obtuvo previamente. A continuación, haga clic en **Aceptar**.

    ![][1]

    Aplicación de servicio proporciona autenticación de forma predeterminada, pero no restringe el acceso autorizado a la API y el contenido del sitio. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio para que solo los usuarios autenticados Azure Active Directory, establecer **acción para cuando no se autentica la solicitud de** iniciar sesión **con Azure Active Directory**. Esto requiere que todas las solicitudes de autenticar y todas las solicitudes no autenticadas se redirigen a Azure Active Directory para la autenticación.

17. Haga clic en **Guardar**.

Ya está listo para usar Azure Active Directory para la autenticación en la aplicación.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar una aplicación de cliente nativo

Azure Active Directory también le permite registrar a clientes nativos, lo que proporciona mayor control sobre la asignación de permisos. Es necesario si desea realizar inicios de sesión con una biblioteca, como la **Biblioteca de autenticación de Active Directory**.

1. Vaya a **Active Directory** en el [portal de clásico de Azure].

2. Seleccione el directorio y, a continuación, seleccione la pestaña de **aplicaciones** en la parte superior. En la parte inferior para crear un nuevo registro de aplicación, haga clic en **Agregar** .

3. Haga clic en **Agregar una aplicación se desarrolla mi organización**.

4. En el Asistente para agregar aplicaciones, escriba un **nombre** para la aplicación y haga clic en el tipo de **Aplicación nativa de cliente** . A continuación, haga clic en para continuar.

5. En el cuadro **Redirigir URI** , escriba extremo de _/.auth/login/done_ de su sitio, mediante la combinación de HTTPS. Este valor debe ser similar a _https://contoso.azurewebsites.net/.auth/login/done_. Si crea una aplicación de Windows, use en su lugar el [paquete SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.

6. Una vez agregada la aplicación nativa, haga clic en la ficha **Configurar** . Busque el **Identificador de cliente** y tome nota de este valor.

7. Desplazarse en la página hacia abajo hasta la sección **permisos a otras aplicaciones** y haga clic en **Agregar aplicación**.

8. Busque la aplicación web que registrado anteriormente y haga clic en el icono de más. A continuación, haga clic en la casilla para cerrar el cuadro de diálogo. Si la aplicación web no se puede encontrar, desplácese hasta su registro y agregar una nueva dirección de respuesta URL (por ejemplo, la versión HTTP de la dirección URL actual), haga clic en Guardar y, a continuación, repita estos pasos, la aplicación debería aparecer en la lista.

9. En la nueva entrada que acaba de agregar, abra la lista desplegable de **Permisos de delegado** y seleccione **acceso (aplicación)**. A continuación, haga clic en **Guardar**.

Se ha configurado una aplicación de cliente nativo que puede tener acceso a la aplicación de servicio de aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Portal de clásico de Azure]: https://manage.windowsazure.com/
[alternative method]:#advanced
