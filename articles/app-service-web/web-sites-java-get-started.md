<properties
    pageTitle="Crear una aplicación web de Java en la aplicación de servicio de Azure | Microsoft Azure"
    description="En este tutorial se muestra cómo implementar una aplicación web de Java al servicio de la aplicación de Azure."
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Crear una aplicación web de Java en la aplicación de servicio de Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial muestra cómo crear una [aplicación web en la aplicación de servicio de Azure] de Java a través del [Portal de Azure]. El Portal de Azure es una interfaz web que puede usar para administrar los recursos de Azure.

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [activar las ventajas de suscriptor de Visual Studio] o [suscribirse a una prueba gratuita].
>
> Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse a una cuenta de Azure, vaya a [Probar el servicio de aplicación]. Allí, puede crear inmediatamente una aplicación web de corta duración starter en servicio de la aplicación; no es necesaria, tarjeta de crédito y sin compromisos.

## <a name="java-application-options"></a>Opciones de la aplicación de Java

Hay varias maneras que puede configurar una aplicación de Java en una aplicación de servicio de aplicación web. 

1. Crear una aplicación y, a continuación, configuración de **aplicación**.

    Aplicación de servicio proporciona varias versiones de Tomcat y embarcadero, con la configuración predeterminada. Si la aplicación que va a hospedar funcionarán con una de las versiones integradas, este método de configuración de un contenedor de web es el más fácil y es ideal cuando lo que necesita hacer es cargar un archivo de guerra en un contenedor de web. Para este método, cree una aplicación en el Portal de Azure y, a continuación, vaya al módulo de **configuración de la aplicación** de la aplicación elegir la versión de Java junto con el contenedor de web Java deseada. Cuando use este método Java y el contenedor de web se ejecutan desde archivos de programa. Otros métodos de poner el contenedor web y potencialmente JVM en el espacio en disco. Cuando utiliza este modelo, no tiene acceso para editar archivos de este elemento del sistema de archivos. Esto significa que no puede hacer cosas como configurar el archivo *server.xml* o colocar los archivos de la biblioteca en la *carpeta/lib* . Para obtener más información, consulte la [crear y configurar una aplicación web de Java](#appsettings) sección más adelante en este tutorial.
    
2. Usar una plantilla de Azure Marketplace.

    Azure Marketplace incluye plantillas que crean y configuración aplicaciones web de Java con contenedores de web Tomcat o embarcadero automáticamente. Los contenedores de web crean las plantillas son configurables. Para obtener más información, vea la sección [usar una plantilla de Java de Azure Marketplace](#marketplace) de este tutorial.
  
3. Crear una aplicación y, a continuación, copie manualmente y editar archivos de configuración 

    Desea hospedar una aplicación personalizada de Java que no se implementa en cualquiera de los contenedores de web proporcionados por la aplicación de servicio. Por ejemplo:
    
    * La aplicación de Java requiere una versión de Tomcat o embarcadero que directamente no es compatible con la aplicación de servicio o de la galería.
    * La aplicación de Java toma las solicitudes HTTP y no se implementa como una guerra en un contenedor de web ya existente.
    * Desea configurar el contenedor de web desde cero usted mismo. 
    * Desea usar una versión de Java que no es compatible con la aplicación de servicio y desea cargar usted mismo.

    Para estos casos, puede crear una aplicación con el Portal de Azure y, a continuación, proporcionar los archivos de tiempo de ejecución adecuados manualmente. En este caso, los archivos se van a contar frente a las cuotas de espacio de almacenamiento para su plan de servicios de aplicación. Para obtener más información, vea [cargar una aplicación web de Java personalizada a Azure].

## <a name="portal"></a>Crear y configurar una aplicación web de Java

Esta sección muestra cómo crear una aplicación web y configúrelo para Java con el módulo de **configuración de la aplicación** del portal.

1. Inicie sesión en el [Portal de Azure].

2. Haga clic en **Nuevo > Web + Mobile > Web App**.

    ![Nueva aplicación Web][newwebapp]

4. Escriba un nombre para la aplicación web en el cuadro de la **aplicación Web** .

    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}. azurewebsites.net. Si el nombre que escribe no único, aparece un signo de exclamación rojo en el cuadro de texto.

5. Seleccione un **Grupo de recursos** o cree uno nuevo.

    Para obtener más información acerca de los grupos de recursos, consulte [con el Portal de Azure para administrar los recursos de Azure].

6. Seleccione una **Ubicación o plan de aplicación de servicio** o cree uno nuevo.

    Para obtener más información acerca de los planes de servicio de aplicaciones, vea [información general de los planes de servicio de aplicaciones de Azure].

7. Haga clic en **crear**.

    ![Crear aplicaciones Web][newwebapp2]
 
8. Cuando se ha creado la aplicación web, haga clic en **aplicaciones Web > {su aplicación web}**.
 
    ![Seleccione la aplicación Web][selectwebapp]

9. En el módulo de la **aplicación Web** , haga clic en **configuración**.

10. Haga clic en **configuración de la aplicación**.

11. Elija la **versión de Java**que desee. 

12. Elija la **versión secundaria de Java**que desee. Si selecciona **más recientes**, la aplicación utilizará la versión secundaria más reciente que está disponible en la aplicación de servicio para esa versión principal de Java. El elemento **más reciente** es único en aplicaciones de Java creadas a partir de la **configuración de la aplicación**. Si crea su aplicación Java de la galería, debe administrar sus propios contenedor y JVM cambios. 

12. Elija el **contenedor de Web**que desee. Si selecciona un nombre de contenedor que comienza con **más recientes**, se conservará la aplicación en la versión más reciente de ese web contenedor principales que está disponible en la aplicación de servicio. 

    ![Versiones de contenedor de Web][versions]

13. Haga clic en **Guardar**.

    Dentro de unos momentos, la aplicación web estará basada en Java y está configurado para utilizar el contenedor de web que haya seleccionado.

14. Haga clic en **aplicaciones Web > {la nueva aplicación web}**.

15. Haga clic en la **dirección URL** para ir al nuevo sitio.

    La página web confirma que se ha creado una aplicación web basada en Java.

## <a name="marketplace"></a>Usar una plantilla de Java de Azure Marketplace

Esta sección muestra cómo usar el catálogo de soluciones de Azure para crear una aplicación web de Java. El mismo flujo general también puede utilizarse para crear un móvil basado en Java o una aplicación API. 

1. Inicie sesión en el [Portal de Azure]

2. Haga clic en **Nuevo > Marketplace**.

    ![Catálogo de soluciones de nuevo][newmarketplace]

3. Haga clic en **Web + Mobile**.

    Es posible que tenga que desplazarse hacia la izquierda para ver el módulo de **catálogo de soluciones** donde puede seleccionar **Web + Mobile**.

4. En el cuadro de texto Buscar, escriba el nombre de un servidor de aplicaciones Java, como **Apache Tomcat** o **embarcadero**y, a continuación, presione ENTRAR.

5. En los resultados de búsqueda, haga clic en el servidor de aplicaciones Java.

    ![Web móvil embarcadero][webmobilejetty]

6. En el módulo **Apache Tomcat** o **embarcadero** primera, haga clic en **crear**.

    ![Módulo de Portal recogido][jettyblade]

7. En el módulo **Apache Tomcat** o **embarcadero** siguiente, escriba un nombre para la aplicación web en el cuadro de la **aplicación Web** .

    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}. azurewebsites.net. Si el nombre que escribe no único, aparece un signo de exclamación rojo en el cuadro de texto.

8. Seleccione un **Grupo de recursos** o cree uno nuevo.

    Para obtener más información acerca de los grupos de recursos, consulte [con el Portal de Azure para administrar los recursos de Azure].

9. Seleccione una **Ubicación o plan de aplicación de servicio** o cree uno nuevo.

    Para obtener más información acerca de los planes de servicio de aplicaciones, vea [información general de los planes de servicio de aplicaciones de Azure].

10. Haga clic en **crear**.

    ![Crear Portal recogido][jettyportalcreate2]

    En poco tiempo, suelen ser menores que un minuto Azure termine de crear la nueva aplicación web.

11. Haga clic en **aplicaciones Web > {la nueva aplicación web}**.

12. Haga clic en la **dirección URL** para ir al nuevo sitio.

    ![Dirección URL recogido][jettyurl]

    Tomcat se distribuye con un conjunto predeterminado de las páginas; Si ha elegido Tomcat, verá una página similar al siguiente ejemplo.

    ![Aplicación Web con Apache Tomcat][tomcat]

    Si ha elegido embarcadero, verá una página similar al siguiente ejemplo. Embarcadero no tiene un conjunto de página predeterminado, de modo que se vuelve a utilizar el mismo JSP que se usa para un sitio de Java vacío aquí.

    ![Aplicación Web mediante embarcadero][jetty]

Ahora que ha creado la aplicación web con un contenedor de la aplicación, vea la sección de [pasos siguientes](#next-steps) para obtener información sobre cómo cargar la aplicación a la aplicación web.

## <a name="next-steps"></a>Pasos siguientes

En este momento, tiene un servidor de aplicaciones de Java ejecuta en la aplicación web de la aplicación de servicio de Azure. Para implementar su propio código en la aplicación web, vea [Agregar una aplicación o página Web para la aplicación web de Java].

Para obtener más información sobre cómo desarrollar aplicaciones de Java en Azure, consulte el [Centro para desarrolladores de Java].

<!-- URL List -->

[Agregar una aplicación o página Web a la aplicación web de Java]: ./web-sites-java-add-app.md
[Información general de planes de servicio de aplicación de Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portal de Azure]: https://portal.azure.com/
[activar las ventajas de suscriptor de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[suscribirse a una prueba gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Pruebe la aplicación de servicio]: http://go.microsoft.com/fwlink/?LinkId=523751
[aplicación Web de servicio de la aplicación de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro para desarrolladores de Java]: /develop/java/
[Con el Portal de Azure para administrar los recursos de Azure]: ../azure-portal/resource-group-portal.md
[Cargar una aplicación web de Java personalizada en Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
