<properties 
    pageTitle="Autenticar con la API de REST de contratación Mobile - configuración manual"
    description="Describe cómo configurar manualmente la autenticación para las API de REST de contratación Mobile" 
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
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticar con la API de REST de contratación Mobile - configuración manual

Esta es una documentación apéndice autenticar [con las API de REST de contratación Mobile](mobile-engagement-api-authentication.md). Asegúrese de que se lee en primer lugar para obtener el contexto. Describe una alternativa para realizar la configuración de un solo uso para configurar la autenticación para las API de REST de contratación Mobile con el Portal de Azure. 

>[AZURE.NOTE] Las siguientes instrucciones basadas en esta [Guía de Active Directory](../resource-group-create-service-principal-portal.md) y personalizadas para qué se necesita para la autenticación para las API de compromiso de móvil. Así que haga referencia a él si desea conocer los pasos siguientes en detalle. 

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

     ![Seleccione Active Directory][1]

3. Elija el **Valor predeterminado de Active Directory** en el portal de Azure. 

     ![Seleccionar directorio][2]

    >[AZURE.IMPORTANT] Este método funciona cuando se trabaja en el valor predeterminado de la cuenta de Active Directory y no funcionará si está realizando en Active Directory que ha creado en su cuenta. 

4. Para ver las aplicaciones en el directorio, haga clic en **aplicaciones**.

     ![ver aplicaciones][3]

5. Haga clic en **Agregar**. 

     ![Agregar aplicación][4]

6. Haga clic en **Agregar una aplicación se desarrolla mi organización**

     ![nueva aplicación][5]

6. Rellene el nombre de la aplicación y seleccione el tipo de aplicación **O de aplicaciones WEB API WEB** y haga clic en el botón siguiente.

     ![aplicación de nombre][6]

7. Puede proporcionar cualquier URL ficticias para **Dirección URL de inicio de sesión** y **URI de ID de aplicación**. No se utilizan para nuestro escenario y no se validarán las direcciones URL a sí mismos.  

     ![propiedades de la aplicación][7]

8. Al final de este tendrá una aplicación AAD con el nombre proporcionado anteriormente como la siguiente. Esta es su **AD\_aplicación\_nombre** y tome nota del mismo.  

     ![nombre de la aplicación][8]

9. Haga clic en el nombre de la aplicación y haga clic en **Configurar**.

     ![configurar la aplicación][9]

10. Anote el ID de cliente que se utilizará como **cliente\_ID** para la API de llamadas. 

     ![configurar la aplicación][10]

11. Desplácese hacia abajo hasta la sección de **claves** y agregue una clave con preferentemente duración de 2 años (caducidad) y haga clic en **Guardar**. 

     ![configurar la aplicación][11]


12. Inmediatamente, copie el valor que se muestra para la clave, tal como se muestra únicamente ahora y no se almacena para que no se mostrará nunca más. Si la pierde, tendrá que generar una nueva clave. Esta será la **CLIENT_SECRET** para las llamadas de API. 

     ![configurar la aplicación][12]

    >[AZURE.IMPORTANT] Esta clave caducará al final de la duración especificada para que asegúrese de renovarla cuando llegue el momento de lo contrario, la autenticación de API deje de funcionar. También puede eliminar y volver a crear esta clave si cree que se ha comprometido.
 
13. Haga clic en el botón de **Vista EXTREMOS** ahora que se abrirá el cuadro de diálogo de **Extremos de la aplicación** . 

    ![][13]

14. En el cuadro de diálogo extremos de la aplicación, copie el **Extremo de TOKEN OAUTH 2.0**. 

    ![][14]

15. Este extremo estarán en la forma siguiente, donde el GUID de la dirección URL es su **TENANT_ID** así que asegúrese de una nota de ella: 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. Ahora se continuará configurar los permisos de esta aplicación. Para ello debe abrir el [portal de Azure](https://portal.azure.com). 

17. Haga clic en **Grupos de recursos** y busque el grupo de recursos de **Compromiso de móvil** .  

    ![][15]

18. Haga clic en el grupo de recursos de **Contratación Mobile** y navegue hasta el módulo de **configuración de** aquí. 

    ![][16]

19. Haga clic en **usuarios** en el módulo de configuración y, a continuación, haga clic en **Agregar** para agregar un usuario. 

    ![][17]

20. Haga clic en **Seleccionar una función**

    ![][18]

21. Haga clic en **propietario**

    ![][19]

22. Busque el nombre de la aplicación **AD\_aplicación\_nombre** en el cuadro de búsqueda. No se ve aquí predeterminada. Una vez encontrado, selecciónelo y haga clic en **Seleccionar** en la parte inferior de la hoja. 

    ![][20]

23. En el módulo de **Access agregar** , se muestran como **1 el usuario, grupos 0**. En este módulo para confirmar el cambio, haga clic en **Aceptar** . 

    ![][21]

Se ha completado la configuración de AAD necesaria y está establecidas para llamar a la API. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



