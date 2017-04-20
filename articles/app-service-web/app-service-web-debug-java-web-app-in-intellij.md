<properties 
    pageTitle="Depurar una aplicación Web de Java en Azure en IntelliJ | Microsoft Azure" 
    description="En este tutorial se muestra cómo usar el Kit de herramientas de Azure para IntelliJ depurar una aplicación Web de Java ejecuta en Azure." 
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

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Depurar una aplicación Web de Java en Azure en IntelliJ

Este tutorial muestra cómo depurar una aplicación Web de Java ejecuta en Azure mediante el [Kit de herramientas de Azure para IntelliJ]. Para simplificar, que usa un ejemplo de página JSP (Java Server) básico para este tutorial, pero los pasos sería similares para un servlet Java al depurar en Azure.

Cuando haya completado este tutorial, la aplicación tendrá un aspecto similar a la siguiente ilustración al depurar en IntelliJ:

![][01]
 
## <a name="prerequisites"></a>Requisitos previos

* Un Java Developer Kit (JDK), v 1,8 o posterior.
* IntelliJ IDEA Ultimate Edition. Esto puede descargarse desde <https://www.jetbrains.com/idea/download/index.html>.
* Una distribución de un servidor web basado en Java o el servidor de aplicaciones, como Apache Tomcat o embarcadero.
* Una suscripción de Azure, que se puede adquirir de <https://azure.microsoft.com/en-us/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El Kit de herramientas de Azure para IntelliJ. Para obtener más información, vea [instalar el Kit de herramientas de Azure para IntelliJ].
* Un proyecto Web dinámica crear e implementar al servicio de la aplicación de Azure; Por ejemplo, vea [crear un saludo World Online para Azure en IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Depurar una aplicación Web de Java en Azure

Para completar estos pasos de esta sección, puede usar un proyecto de Web dinámica existente que ya ha implementado como una aplicación Web de Java en Azure, descargar un [Proyecto de Web dinámico de ejemplo] y siga los pasos de [crear un saludo World Online para Azure en IntelliJ] para implementar en Azure. 

1. Abra el proyecto de aplicación Web de Java que implementa en Azure en IntelliJ.

1. Haga clic en el menú **Ejecutar** y, a continuación, haga clic en **Editar configuraciones**.

    ![][02]

1. Cuando se abre el cuadro de diálogo **Ejecutar o depurar configuraciones** : 

    1. Seleccione la **aplicación Web de Azure**.
    1. Haga clic en **+** para agregar una nueva configuración.
    1. Proporcione un **nombre** para la configuración.
    1. Acepte los valores predeterminados restantes que sugieren el Kit de herramientas de Azure y, a continuación, haga clic en **Aceptar**.

        ![][03]

1. Seleccione la configuración de depuración de la aplicación Web de Azure que acaba de crear en la parte superior derecha del menú y haga clic en **Depurar**

    ![][04]

1. Cuando se le indique que **Habilitar depuración remota en la aplicación Web remota ahora?**, haga clic en **Aceptar**.

1. Cuando se le pida que **la aplicación web ya está lista para depuración remota**, haga clic en **Aceptar**.

    ![][05]

1. Seleccione la configuración de depuración de la aplicación Web de Azure que acaba de crear en la parte superior derecha del menú y, a continuación, haga clic en **Depurar**.

1. Un símbolo del sistema de Windows o shell Unix abrirá y preparar una conexión necesarios para depurar; debe esperar hasta que la conexión a su aplicación Web de Java remoto es correcta antes de continuar. Si usa Windows, tendrá un aspecto similar a la siguiente ilustración:

    ![][06]

1. Insertar un punto de interrupción en la página JSP, a continuación, abra la dirección URL para la aplicación Web de Java en un explorador:

    1. Abra **El Explorador de Azure** en IntelliJ.
    1. Vaya a **Aplicaciones Web** y la aplicación Web de Java que desee depurar.
    1. Haga clic con el botón secundario en la aplicación Web y haga clic en **Abrir en el explorador**.
    1. IntelliJ ahora entrará en modo de depuración.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

Para obtener información adicional sobre la creación de aplicaciones Web de Azure, vea [Introducción a aplicaciones Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Instalar el Kit de herramientas de Azure IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Proyecto de Web dinámica de ejemplo]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a aplicaciones Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
