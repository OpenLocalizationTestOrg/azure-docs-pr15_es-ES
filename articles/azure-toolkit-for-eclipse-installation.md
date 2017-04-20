<properties
    pageTitle="Instalar el Kit de herramientas de Azure Eclipse | Microsoft Azure"
    description="Obtenga información sobre cómo instalar el Kit de herramientas de Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalar el Kit de herramientas de Azure Eclipse

El Kit de herramientas de Azure para Eclipse proporciona plantillas y funcionalidad que le permiten crear fácilmente, desarrollar, probar e implementar aplicaciones de Azure mediante el entorno de desarrollo Eclipse. El Kit de herramientas de Azure para Eclipse es un proyecto de código abierto, cuyo código fuente está disponible en la licencia MIT desde el sitio del proyecto en GitHub en la siguiente URL:

<https://github.com/Microsoft/Azure-Tools-for-Java>

Los pasos siguientes muestran cómo instalar el Kit de herramientas de Azure para Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Para instalar el Kit de herramientas de Azure para Eclipse

1. Iniciar Eclipse.

1. Cuando se abra Eclipse, haga clic en el menú **Ayuda** y, a continuación, haga clic en **Instalar nuevo Software**, tal como se muestra en la siguiente ilustración.

    ![Instalar el Kit de herramientas de Azure Eclipse][01]

1. En el cuadro de diálogo de **Software disponible** , dentro del cuadro de texto de **trabajo con** , escriba **http://dl.microsoft.com/eclipse** seguido de la tecla **ENTRAR** .

1. En el panel de **nombre** , consulte el **Kit de herramientas de Azure para Eclipse**y desactive **contacto todos actualización sitios durante la instalación para encontrar el software necesario**. La pantalla debería ser similar al siguiente:

    ![Instalar el Kit de herramientas de Azure Eclipse][02]

1. Si se expande el **Kit de herramientas de Azure para Eclipse**, verá los siguientes elementos:

    * **Complemento de perspectivas de aplicación para Java**: este componente le permite usar los servicios de registro y análisis de telemetría de Azure para las aplicaciones y las instancias de servidor.
    * **Filtro de servicios de Control de acceso de Azure**: este componente proporciona compatibilidad con autenticación de usuarios de la aplicación con Azure ACS, lo que permite escenarios de inicio de sesión único y externalización lógica de identidad de la aplicación.
    * **Complemento de Azure comunes**: este componente proporciona la funcionalidad común necesaria por otros componentes del Kit de herramientas.
    * **Explorador de Azure para Eclipse**: este componente proporciona la funcionalidad común necesaria por otros componentes del Kit de herramientas.
    * **Complemento de Azure para Eclipse con Java**: este componente proporciona soporte para desarrollar proyectos que le ayudan a crear, probar e implementar aplicaciones de Java para la nube de Microsoft Azure en Eclipse y a través de la línea de comandos.
    * **Complemento de aplicaciones Web de Azure con Java**: este componente proporciona soporte técnico para implementar aplicaciones web de Java a los contenedores de Microsoft Azure Web App.
    * **4.2 de controlador JDBC de Microsoft para SQL Server**: este componente proporciona JDBC API para SQL Server y la base de datos de SQL de Microsoft Azure para Java plataforma Enterprise Edition 8.
    * **Paquete de bibliotecas de cliente de Qpid Apache de JMS**: este componente proporciona el componente de cliente JMS del proyecto Apache Qpid para habilitar la aplicación para usar la mensajería AMQP en Microsoft Azure.
    * **Paquete de Microsoft Azure bibliotecas para Java**: este componente proporciona las API para obtener acceso a servicios de Microsoft Azure, como el almacenamiento de bus de servicio, ejecución del servicio, etcetera.

1. Haga clic en **siguiente**. (Si experimenta retrasos inusuales al instalar el Kit de herramientas, asegúrese de que **todos los sitios durante de actualización de contacto instalar para encontrar el software necesario** no está seleccionada.)

1. En el cuadro de diálogo **Detalles de la instalación** , haga clic en **siguiente**.

    ![Revise los detalles de la instalación][03]

1. En el cuadro de diálogo **Revise licencias** , revise los términos de la licencia. Si acepta los términos del contrato de licencia, haga clic en **acepto los términos de la licencia** y, a continuación, haga clic en **Finalizar**. (El resto de los pasos se supone que acepte los términos de la licencia. Si no acepta los términos de la licencia, salir del proceso de instalación.)

    ![Licencias de revisión][04]

    Eclipse se descargue e instale los paquetes necesarios.

    ![Progreso de la instalación][05]

1. Si se le pide que reinicie Eclipse para completar la instalación, haga clic en **Sí**.

    ![Reinicie el símbolo del sistema][06]

## <a name="see-also"></a>Vea también

Para obtener más información sobre los Toolkits de Azure para Java IDE, consulte los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - *Instalar el Kit de herramientas de Azure Eclipse (en este artículo)*
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]
  - [Novedades en el Kit de herramientas de Azure para Eclipse]
- [Kit de herramientas de Azure para IntelliJ]
  - [Instalar el Kit de herramientas de Azure IntelliJ]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]
  - [Novedades en el Kit de herramientas de Azure para IntelliJ]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalar el Kit de herramientas de Azure IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novedades en el Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades en el Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

