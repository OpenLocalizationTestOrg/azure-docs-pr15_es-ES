<properties
    pageTitle="Crear una aplicación web de Azure Marketplace | Microsoft Azure"
    description="Aprenda a crear una nueva aplicación web de WordPress de Azure Marketplace mediante el Portal de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Crear una aplicación web de Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace hace que esté disponible una amplia gama de aplicaciones web populares desarrollado por Microsoft, compañías de terceros e iniciativas de software de código abierto. Por ejemplo, WordPress, Umbraco CMS, Drupal, etcetera. Estas aplicaciones web se basan en una amplia gama de marcos populares, como [PHP] en este ejemplo WordPress, [. NET], [Node.js], [Java]y [Python], entre otros. Para crear una aplicación web de Azure Marketplace el software solo que necesita es el explorador que use para el [Portal de Azure].

En este tutorial aprenderá cómo:

* Buscar y crear la aplicación web de servicio de aplicación de Azure basado en una plantilla de Azure Marketplace.
* Configuración del servicio de aplicación de Azure para la nueva aplicación web.
* Iniciar y administrar la aplicación web.

Para el propósito de este tutorial, se implementa un sitio de blog WordPress de Azure Marketplace. Cuando haya completado los pasos de este tutorial, tendrá su propio sitio WordPress hacia arriba y en la nube.

![Panel de ejemplo WordPress wep aplicación][WordPressDashboard1]

El sitio de WordPress implementará en este tutorial usa MySQL para la base de datos. Si desea usar en su lugar la base de datos de SQL para la base de datos, vea [Nami del proyecto], que también está disponible a través de Azure Marketplace.

> [AZURE.NOTE]
> Para completar este tutorial, necesita una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [activar las ventajas de suscriptor de Visual Studio] [ activate] o [registrarse para una prueba gratuita][free trial].
>
> Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse a una cuenta de Azure, vaya a [Probar el servicio de aplicación]. Desde allí puede crear una aplicación web de corta duración starter inmediatamente en la aplicación de servicio: no se requiere ninguna tarjeta de crédito y no hay ningún compromisos.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Buscar y crear una aplicación Web de servicio de aplicación de Azure

1. Inicie sesión en el [Portal de Azure].

1. Haga clic en **nuevo**.
    
    ![Crear un nuevo recurso de Azure][MarketplaceStart]
    
1. Buscar **WordPress**y, a continuación, haga clic en **WordPress**. (Si desea usar la base de datos de SQL en lugar de MySQL, buscar **Nami de proyecto**).

    ![Buscar WordPress en el catálogo de soluciones][MarketplaceSearch]
    
1. Después de leer la descripción de la aplicación de WordPress, haga clic en **crear**.

    ![Crear aplicaciones web de WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configuración del servicio de aplicación de Azure para la nueva aplicación Web

1. Después de haber creado una nueva aplicación web, el módulo de configuración de WordPress se mostrará, que se usará para completar los pasos siguientes:

    ![Establecer la configuración de aplicación web de WordPress][ConfigStart]

1. Escriba un nombre para la aplicación web en el cuadro de la **aplicación Web** .

    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será *{nombre}*. azurewebsites.net. Si el nombre que escribe no único, aparece un signo de exclamación rojo en el cuadro de texto.

    ![Configurar el nombre de aplicación web de WordPress][ConfigAppName]

1. Si tiene más de una suscripción, elija el que desee usar. 

    ![Configurar la suscripción para la aplicación web][ConfigSubscription]

1. Seleccione un **Grupo de recursos** o cree uno nuevo.

    Para obtener más información acerca de los grupos de recursos, vea [información general del Administrador de recursos de Azure][ResourceGroups].

    ![Configurar el grupo de recursos para la aplicación web][ConfigResourceGroup]

1. Seleccione una **Ubicación o plan de aplicación de servicio** o cree uno nuevo.

    Para obtener más información acerca de los planes de servicio de aplicaciones, vea [información general de los planes de servicio de la aplicación de Azure][AzureAppServicePlans]. 

    ![Configurar el plan de servicio para la aplicación web][ConfigServicePlan]

1. Haga clic en **base de datos**y, a continuación, en el módulo de la **Nueva base de datos MySQL** proporcionarán los valores necesarios para configurar la base de datos MySQL.

    una. Escriba un nombre nuevo o dejar el nombre predeterminado.

    b. Deje el **Tipo de base de datos** establecido en **compartido**.

    c. Elija la misma ubicación que el que eligió en la aplicación web.

    d. Elija un nivel de precios. **Mercurio** , que es gratuita con conexiones mínimas y espacio en disco, es adecuado para este tutorial.

    e. En el módulo de la **Nueva base de datos MySQL** , acepte las condiciones legales y, a continuación, haga clic en **Aceptar**. 

    ![Establecer la configuración de la base de datos de la aplicación web][ConfigDatabase]

1. En el módulo **WordPress** , acepte las condiciones legales y, a continuación, haga clic en **crear**. 

    ![Finalizar la configuración de la aplicación web y haga clic en Aceptar][ConfigFinished]

    Servicio de aplicaciones de Azure crea la aplicación web, normalmente en menos de un minuto. Puede ver el progreso haciendo clic en el icono de campana en la parte superior de la página del portal.

    ![Indicador de progreso][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar y administrar la aplicación web de WordPress
    
1. Cuando haya finalizado la creación de la aplicación web, desplácese en el Portal de Azure al grupo de recursos en la que se creó la aplicación y puede ver la aplicación web y la base de datos.

    El recurso adicional con el icono de bombilla es [Aplicación perspectivas][ApplicationInsights], que proporciona servicios de supervisión para la aplicación web.

1. En el módulo de **grupo de recursos** , haga clic en la línea de la aplicación web.

    ![Seleccione la aplicación web de WordPress][WordPressSelect]

1. En el módulo de aplicación Web, haga clic en **Examinar**.

    ![Vaya a la aplicación web de WordPress][WordPressBrowse]

1. Si se le pide que seleccione el idioma para el blog de WordPress, seleccione el idioma deseado y, a continuación, haga clic en **continuar**.

    ![Configurar el idioma para la aplicación web de WordPress][WordPressLanguage]

1. En la página de **bienvenida** WordPress, escriba la información de configuración requerida por WordPress y, a continuación, haga clic en **Instalar WordPress**.

    ![Configurar las opciones de la aplicación web de WordPress][WordPressConfigure]

1. Inicie sesión con las credenciales que creó en la página **principal** .  

1. Abrirá la página de panel de sitio y mostrar la información que haya proporcionado.    

    ![Ver el panel de WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha visto cómo crear e implementar una aplicación web de ejemplo de Azure Marketplace.

Para obtener más información sobre cómo trabajar con la aplicación de servicio Web Apps, vea los vínculos en el lado izquierdo de la página (para windows explorador amplia) o en la parte superior de la página (para windows explorador estrecho).

Para obtener más información sobre cómo desarrollar aplicaciones web de WordPress en Azure, vea [Desarrollar WordPress en los servicios de aplicación de Azure en][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Pruebe la aplicación de servicio]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portal de Azure]: https://portal.azure.com/
[Proyecto Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
