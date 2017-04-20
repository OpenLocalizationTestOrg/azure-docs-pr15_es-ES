<properties 
    pageTitle="Compile e implemente una aplicación web de Node.js Azure con WebMatrix" 
    description="Tutorial que le enseña a usar WebMatrix para desarrollar una aplicación de Node.js e implementar a Azure aplicación de servicio Web Apps." 
    services="app-service\web" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016"
    ms.author="robmcm"/>


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Compile e implemente una aplicación web de Node.js Azure con WebMatrix

En este tutorial se muestra cómo usar WebMatrix para desarrollar una aplicación de Node.js e implementar aplicaciones de [Azure aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714) Web. WebMatrix es una herramienta de desarrollo de web gratuita de Microsoft que todo lo que necesita para el desarrollo de aplicaciones web o sitio Web incluye. WebMatrix incluye diversas características que hacen que sea fácil de usar Node.js incluidos la finalización de código, plantillas previamente creadas y compatibilidad de editor para Jade, menos y CoffeeScript. Más información sobre [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Tras completar a esta guía, tendrá una aplicación web de Node.js ejecuta en el servicio de aplicación de Azure.
 
Captura de pantalla de la aplicación completada está por debajo de:

![Sitio Web de nodo de Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="sign-into-azure"></a>Inicie sesión en Azure

Siga estos pasos para crear una aplicación web en la aplicación de servicio de Azure.

1. Iniciar WebMatrix
2. Si esta es la primera vez ha utilizado WebMatrix, se le pedirá que inicie sesión en Azure.  En caso contrario, puede haga clic en el botón **Inicio de sesión** y elija **Agregar cuenta**.  Seleccione esta opción para **iniciar sesión** con su Account de Microsoft.

    ![Agregar cuenta][addaccount]

3. Si se dispone de una cuenta de Azure, puede iniciar sesión con su Account de Microsoft:

    ![Inicie sesión en Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Crear un sitio con una plantilla integrada para Azure

1. En la pantalla de inicio, haga clic en el botón **nuevo** y elija la **Galería de plantillas** para crear un nuevo sitio de la Galería de plantillas:

    ![Nuevo sitio de galería de plantillas][sitefromtemplate]

2. En el cuadro de diálogo de **sitio a partir de plantilla** , seleccione **nodo** y, a continuación, seleccione **Sitio Express**. Por último, haga clic en **siguiente**. Si no tiene los requisitos previos para la plantilla de **Sitio Express** , se le pedirá que van a instalar.

    ![Seleccione la plantilla express][webmatrix-templates]

3. Si ha iniciado sesión en Azure, ahora tiene la opción de crear una aplicación de servicio de aplicación web de su sitio local.  Elija un nombre único y seleccione el centro de datos donde desea que la aplicación web de servicio de la aplicación que se cree: 

    ![Crear sitios en Azure][nodesitefromtemplateazure]
    
4. Cuando WebMatrix termine de crear el sitio local y la creación de la aplicación de servicio de aplicación web, se muestra el IDE WebMatrix.

    ![WebMatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Publicar su aplicación en Azure

1. En WebMatrix, haga clic en **Publicar** en la cinta de **Inicio** para mostrar el cuadro de diálogo **Vista previa de publicación** para el sitio.

    ![vista previa de publicación][webmatrix-node-publishpreview]

2. Haga clic en **continuar**. Una vez completada la publicación, la dirección URL de la aplicación de servicio de la aplicación web se muestra en la parte inferior del IDE WebMatrix

    ![publicar completado][webmatrix-publish-complete]

3. Haga clic en el vínculo para abrir la aplicación de servicio de aplicación web en el explorador.

    ![Aplicación web de Express][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Modificar y publicar la aplicación.

Puede modificar fácilmente y volver a publicar la aplicación. A continuación, le será un simple cambie al encabezado en el archivo **index.jade** y a continuación, volver a publicar la aplicación.

1. En WebMatrix, seleccione **archivos**y, a continuación, expanda la carpeta **vistas** . Abra el archivo **index.jade** haciendo doble clic en él.

    ![index.jade de visualización de WebMatrix][webmatrix-modify-index]

2. Cambiar la línea del párrafo a la siguiente:

        p Welcome to #{title} with WebMatrix on Azure!

3. Guardar los cambios y, a continuación, haga clic en el icono de la publicación. Por último, haga clic en **continuar** en el cuadro de diálogo **Vista previa de publicación** y espere a que la actualización que se publiquen.

    ![vista previa de publicación][webmatrix-republish]

4. Cuando haya finalizado la publicación, utilice el vínculo que se devuelve cuando el proceso de publicación es completado para ver la aplicación web de servicio de la aplicación actualizada.

    ![Aplicación web de nodo de Azure][webmatrix-node-completed]

##<a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de las versiones de Node.js que se proporcionan con Azure y cómo especificar la versión para usarlo con la aplicación, vea [especificar una versión de Node.js en una aplicación de Azure](../nodejs-specify-node-version-azure-apps.md).

Si tiene problemas con la aplicación después de que se ha implementado en Azure, consulte [cómo depurar una aplicación web de Node.js en Azure aplicación de servicio](web-sites-nodejs-debug.md) para obtener información sobre diagnosticar el problema.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 