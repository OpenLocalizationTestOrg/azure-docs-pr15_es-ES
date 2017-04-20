<properties
    pageTitle="Novedades en el Kit de herramientas de Azure para IntelliJ | Microsoft Azure"
    description="Obtenga información acerca de las características más recientes en el Kit de herramientas de Azure para IntelliJ."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Novedades en el Kit de herramientas de Azure para IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Kit de herramientas de Azure lanzamientos IntelliJ

Este artículo contiene información sobre las varias versiones y las actualizaciones más recientes para el Kit de herramientas de Azure para IntelliJ.

> [AZURE.NOTE] También es un Kit de herramientas de Azure para IDE Eclipse. Para obtener más información, consulte el [Kit de herramientas de Azure para Eclipse].

### <a name="august-26-2016"></a>26 de agosto de 2016

El Kit de herramientas de Azure para IntelliJ - versión de 2016 agosto incluye las siguientes mejoras:

* **Distribuciones de JDK personalizado**. El Kit de herramientas de Azure para IntelliJ admite especificar e implementar una versión JDK arbitraria al contenedor de la aplicación Web de Azure:
  - Además de JDKs proporcionados por Azure, también puede elegir entre una gran variedad de versiones de OpenJDK Zulú ofrecerse en Azure con los sistemas de Azul.
  - También puede especificar su propia distribución JDK si carga como un archivo ZIP a su cuenta de almacenamiento.
* **Mejoras en la vista del explorador de Azure**:
  - Soporte técnico para la administración de la máquina Virtual con el nuevo modelo de administrador de recursos de Azure: lista, crear y eliminar máquinas virtuales basados en el Administrador de recursos sin salir del IDE.
  - Soporte técnico para la administración de cuenta de almacenamiento blob mediante el Administrador de recursos de Azure, que complementa la funcionalidad de administración de cuentas de almacenamiento "clásico" existente.
* **Controlador de JDBC de Microsoft 6.0 para SQL Server**. Esta actualización incluye el controlador JDBC más reciente de Microsoft SQL Server (6.0), que es ahora incluyen como una biblioteca que puede agregar fácilmente a los proyectos de Java, reemplazar, con ello, la versión más antigua.

### <a name="june-29-2016"></a>29 de junio de 2016

El Kit de herramientas de Azure para IntelliJ - de 2016 junio versión incluye las siguientes mejoras:

* **Requisito de Java 8**. Ahora, el Kit de herramientas de Azure para IntelliJ requiere Java 8, aunque este requisito es solo para el Kit de herramientas: las aplicaciones pueden seguir usando todas las versiones de Java que son compatibles con Azure.
* **Compatibilidad con la última JDKs Java**. Las últimas versiones de la JDKs Java ahora son compatibles con el Kit de herramientas de Azure para IntelliJ.
* **Compatibilidad con Azure SDK v2.9.1**. La última versión del SDK de Azure ahora es el requisito previo mínimo para el Kit de herramientas de Azure para IntelliJ.
* **Ejemplos de integrado**. Ahora, el Kit de herramientas de Azure para IntelliJ características varias aplicaciones de ejemplo para ayudar a los desarrolladores a empezar.
* **Integración de herramientas de HDInsight**. Herramientas de HDInsight de Azure ahora se incluye con el Kit de herramientas de Azure para IntelliJ. Para obtener más información, vea el [Complemento de herramientas de HDInsight para IntelliJ].
* **Depuración de aplicaciones Web de Java remota**. El Kit de herramientas de Azure para IntelliJ ahora admite depuración remota de aplicaciones web de Java en la aplicación de servicio de Azure.

### <a name="april-12-2016"></a>12 de abril de 2016

El Kit de herramientas de Azure para IntelliJ - versión de 2016 de abril incluye las siguientes mejoras:

* **Compatibilidad con Azure SDK v2.9.0**. La última versión del SDK de Azure ahora es el requisito previo mínimo para el Kit de herramientas de Azure para IntelliJ.
* **Uso vario, mejoras de rendimiento y la capacidad de respuesta relacionadas con el soporte técnico de la aplicación Web de Azure**. Un número de optimización del rendimiento en cómo el Kit de herramientas se comunica con Azure resultado en una interfaz de usuario más rápida.
* **Capacidad para eliminar un contenedor de la aplicación Web existente en Azure desde dentro de IntelliJ**. El Kit de herramientas de Azure para IntelliJ permite eliminar un contenedor de Azure Web existente sin salir de IntelliJ.

## <a name="see-also"></a>Vea también ##

Para obtener más información sobre los Toolkits de Azure para Java IDE, consulte los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - [Instalar el Kit de herramientas de Azure Eclipse]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]
  - [Novedades en el Kit de herramientas de Azure para Eclipse]
- [Kit de herramientas de Azure para IntelliJ]
  - [Instalar el Kit de herramientas de Azure IntelliJ]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]
  - *Novedades en el Kit de herramientas de Azure para IntelliJ (en este artículo)*

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalar el Kit de herramientas de Azure Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalar el Kit de herramientas de Azure IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novedades en el Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Complemento de herramientas de HDInsight para IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
