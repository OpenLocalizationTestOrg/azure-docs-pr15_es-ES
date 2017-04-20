<properties
    pageTitle="Sincronizar el contenido de una carpeta de nube al servicio de la aplicación de Azure"
    description="Obtenga información sobre cómo implementar la aplicación a la aplicación de servicio de Azure a través de la sincronización de contenido de una carpeta de la nube."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizar el contenido de una carpeta de nube al servicio de la aplicación de Azure

En este tutorial se muestra cómo implementar [Aplicación de servicio de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) sincronizar el contenido de los servicios de almacenamiento de nube populares como Dropbox y OneDrive. 

## <a name="overview"></a>Información general sobre implementación de sincronización de contenido

La implementación de la sincronización de contenido a petición funciona con el [motor de implementación de Kudu](https://github.com/projectkudu/kudu/wiki) integrado con el servicio de aplicación. En el [Portal de Azure](https://portal.azure.com), puede designar una carpeta en el almacenamiento de la nube, trabajar con el código de la aplicación y el contenido de la carpeta y sincronizar con la aplicación de servicio con el clic de botón. Sincronizar contenido utiliza el proceso de Kudu de compilación e implementación. 
    
## <a name="contentsync"></a>Cómo habilitar la implementación de la sincronización de contenido
Para habilitar la sincronización de contenido desde el [Portal de Azure](https://portal.azure.com), siga estos pasos:

1. Módulo de la aplicación en el Portal de Azure, haga clic en **configuración** > **Origen de la implementación**. Haga clic en **Elegir origen**, seleccione **OneDrive** o **Dropbox** como origen de para su implementación. 

    ![Sincronizar contenido](./media/app-service-deploy-content-sync/deployment_source.png)

    >[AZURE.NOTE] Debido a las diferencias subyacentes en las API, **OneDrive para la empresa** no se admite en este momento. 

2. Completar el flujo de trabajo de autorización para habilitar la aplicación de servicio tener acceso a una ruta designada predefinida específica para OneDrive o Dropbox todo el contenido del servicio de la aplicación se almacenarán.  
    Después de la autorización del servicio de aplicación de plataforma hará que la opción para crear una carpeta de contenido en la ruta de acceso de contenido designado o elegir una carpeta de contenido existente en esta ruta de acceso de contenido designado. Las rutas de acceso de contenido designados en sus cuentas de almacenamiento de nube utilizadas para la sincronización de servicio de aplicación son las siguientes:  
    * **OneDrive**:`Apps\Azure Web Apps` 
    * **Lista desplegable**:`Dropbox\Apps\Azure`

3. Tras la sincronización inicial de contenido se puede iniciar la sincronización de contenido a petición desde el portal de Azure. Historial de implementación está disponible con el módulo de **implementaciones** .

    ![Historial de implementación](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Más información para la implementación de la lista desplegable está disponible en [implementar desde Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). 


