<properties
    pageTitle="Instalar el Kit de herramientas de Azure IntelliJ | Microsoft Azure"
    description="Obtenga información sobre cómo instalar el Kit de herramientas de Azure para la IDEA de IntelliJ."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="installing-the-azure-toolkit-for-intellij"></a>Instalar el Kit de herramientas de Azure IntelliJ

El Kit de herramientas de Azure para IntelliJ proporciona plantillas y la funcionalidad que permiten crear fácilmente, desarrollar, probar e implementar aplicaciones de Azure mediante el entorno de desarrollo IntelliJ IDEA. El Kit de herramientas de Azure para IntelliJ es un proyecto de código abierto, cuyo código fuente está disponible en la licencia MIT desde el sitio del proyecto en GitHub en la siguiente URL:

<https://github.com/Microsoft/Azure-Tools-for-Java>

Existen dos métodos para instalar el Kit de herramientas de Azure IntelliJ, desde el cuadro de diálogo de configuración y en el menú Configurar en la pantalla de inicio; ambos métodos de instalación se muestra en los siguientes pasos.

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Para instalar el Kit de herramientas de Azure para IntelliJ desde el cuadro de diálogo de configuración

1. Iniciar IntelliJ IDEA.

1. Cuando se abre la IDEA de IntelliJ, haga clic en **archivo**y luego haga clic en **configuración**.

    ![Abrir el cuadro de diálogo de configuración de IDEA IntelliJ][01a]

1. En el cuadro de diálogo Configuración, haga clic en **complementos**y, a continuación, haga clic en **Examinar repositorios**.

    ![Cuadro de diálogo de configuración de IntelliJ IDEA][02a]

1. En el cuadro de diálogo **Examinar repositorios** , escriba "Azure" en el cuadro de búsqueda. Resalte el **Kit de herramientas de Azure para IntelliJ**y, a continuación, haga clic en **instalar**.

    ![Busque el Kit de herramientas de Azure para IntelliJ][03]

    IDEA IntelliJ mostrará el progreso de la instalación en un cuadro de diálogo.

    ![Progreso de la instalación][04]

1. Cuando haya finalizado la instalación, haga clic en **Reiniciar IntelliJ IDEA**.

    ![Reinicie IntelliJ IDEA][05]

1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de configuración.

    ![Cierre el cuadro de diálogo de configuración de IntelliJ IDEA][06]

1. Cuando se le solicite reiniciar IntelliJ IDEA o posponer, haga clic en **reiniciar**.

    ![Reinicie IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Para instalar el Kit de herramientas de Azure para IntelliJ desde la pantalla de inicio

1. Iniciar IntelliJ IDEA.

1. Cuando aparezca la pantalla de inicio de IntelliJ IDEA, haga clic en **Configurar**, haga clic en **complementos**.

    ![Instalar los complementos de IDEA IntelliJ][01b]

1. En el cuadro de diálogo **complementos** , haga clic en **Examinar repositorios**.

    ![Examinar IntelliJ IDEA complemento repositorios][02b]

1. En el cuadro de diálogo **Examinar repositorios** , escriba "Azure" en el cuadro de búsqueda. Resalte el **Kit de herramientas de Azure para IntelliJ**y, a continuación, haga clic en **instalar**.

    ![Busque el Kit de herramientas de Azure para IntelliJ][03]

    IDEA IntelliJ mostrará el progreso de la instalación en un cuadro de diálogo.

    ![Progreso de la instalación][04]

1. Cuando haya finalizado la instalación, haga clic en **Reiniciar IntelliJ IDEA**.

    ![Reinicie IntelliJ IDEA][05]

1. Cuando se le solicite reiniciar IntelliJ IDEA o posponer, haga clic en **reiniciar**.

    ![Reinicie IntelliJ IDEA][07]

## <a name="see-also"></a>Vea también

Para obtener más información sobre los Toolkits de Azure para Java IDE, consulte los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - [Instalar el Kit de herramientas de Azure Eclipse]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]
  - [Novedades en el Kit de herramientas de Azure para Eclipse]
- [Kit de herramientas de Azure para IntelliJ]
  - *Instalar el Kit de herramientas de Azure IntelliJ (en este artículo)*
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]
  - [Novedades en el Kit de herramientas de Azure para IntelliJ]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalar el Kit de herramientas de Azure Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novedades en el Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades en el Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png
