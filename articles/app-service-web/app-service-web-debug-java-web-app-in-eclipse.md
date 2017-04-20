<properties 
    pageTitle="Depurar una aplicación Web de Java en Azure en Eclipse | Microsoft Azure" 
    description="En este tutorial se muestra cómo usar el Kit de herramientas de Azure para Eclipse depurar una aplicación Web de Java ejecuta en Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Depurar una aplicación Web de Java en Azure en Eclipse

Este tutorial muestra cómo depurar una aplicación Web de Java ejecuta en Azure mediante el [Kit de herramientas de Azure para Eclipse]. Para simplificar, que usa un ejemplo de página JSP (Java Server) básico para este tutorial, pero los pasos sería similares para un servlet Java al depurar en Azure.

Cuando haya completado este tutorial, la aplicación tendrá un aspecto similar a la siguiente ilustración al depurar en Eclipse:

![][01]
 
## <a name="prerequisites"></a>Requisitos previos

* Un Java Developer Kit (JDK), v 1,8 o posterior.
* Eclipse IDE para los desarrolladores de Java EE, Indigo o posterior. Esto puede descargarse desde <http://www.eclipse.org/downloads/>.
* Una distribución de un servidor web basado en Java o el servidor de aplicaciones, como Apache Tomcat o embarcadero.
* Una suscripción de Azure, que se puede adquirir de <https://azure.microsoft.com/en-us/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El Kit de herramientas de Azure para Eclipse. Para obtener más información, vea [instalar el Kit de herramientas de Azure para Eclipse].
* Un proyecto Web dinámica crear e implementar al servicio de la aplicación de Azure; Por ejemplo, vea [crear un saludo World Online para Azure en Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Depurar una aplicación Web de Java en Azure

Para completar estos pasos de esta sección, puede usar un proyecto de Web dinámica existente que ya ha implementado como una aplicación Web de Java en Azure, descargar un [Proyecto de Web dinámico de ejemplo] y siga los pasos de [crear un saludo World Online para Azure en Eclipse] para implementar en Azure. 

1. Abra Eclipse.

1. Configurar los tiempos de espera de depuración remota:

    1. Haga clic en el menú de **Windows** en Eclipse y, a continuación, haga clic en **Preferencias**.
    1. Expanda el nodo de **Java** y luego seleccione **Depurar**.
    1. Configurar las opciones de **tiempo de espera de depurador (ms)** tanto el **Inicio de tiempo de espera (ms)** a `120000`.

        ![][02]

    1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Preferencias** .

1. En la vista de explorador de proyectos de Eclipse, haga clic derecho en el proyecto Web dinámicas que se ha implementado en Azure. Cuando aparezca el menú contextual, seleccione **Depurar como**y, a continuación, haga clic en **Azure Web App**.

    ![][03]

1. Si se trata de depuración de su proyecto Web dinámica por primera vez, se abrirá el cuadro de diálogo **Configuraciones de depuración** ; puede aceptar los valores predeterminados que se especifican mediante el Kit de herramientas en la ficha **Conectar** . En la ficha **origen** , haga clic en **Agregar**y, a continuación, **proyecto Java**, seleccione **Dinámica Project Web**y, a continuación, haga clic en **Aceptar**. Una vez haya completado estos pasos, haga clic en **Depurar**.

    ![][04]

1. Cuando se le indique que **Habilitar depuración remota en la aplicación Web remota ahora?**, haga clic en **Aceptar**.

1. Cuando se le pida que **la aplicación web ya está lista para depuración remota**, haga clic en **Aceptar**.

    ![][05]

1. Cuando se vuelve a aparecer en el cuadro de diálogo **Configuraciones depurar** , haga clic en **Depurar**.

1. Un símbolo del sistema de Windows o shell Unix abrirá y preparar una conexión necesarios para depurar; debe esperar hasta que la conexión a su aplicación Web de Java remoto es correcta antes de continuar. Si usa Windows, tendrá un aspecto similar a la siguiente ilustración.

    ![][06]

1. Insertar un punto de interrupción en la página JSP, a continuación, abra la dirección URL para la aplicación Web de Java en un explorador:

    1. Abra **El Explorador de Azure** en Eclipse.
    1. Vaya a **Aplicaciones Web** y la aplicación Web de Java que desee depurar.
    1. Haga clic con el botón secundario en la aplicación Web y haga clic en **Abrir en el explorador**.
    1. Eclipse ahora entrará en modo de depuración.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

Para obtener información adicional sobre la creación de aplicaciones Web de Azure, vea [Introducción a aplicaciones Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Instalar el Kit de herramientas de Azure Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Proyecto de Web dinámica de ejemplo]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a aplicaciones Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png
