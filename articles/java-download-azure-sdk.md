<properties 
    pageTitle="Descargar el SDK de Java Azure" 
    description="Aprenda a descargar el SDK de Azure para Java, con código de ejemplo para los proyectos de experto y pasos básicos de instalación para el Kit de herramientas de Azure para Eclipse." 
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

# <a name="download-the-azure-sdk-for-java"></a>Descargar el SDK de Java Azure

Este artículo contiene instrucciones para descargar e instalar las bibliotecas de Azure para Java.

**Nota:** Las bibliotecas de Azure para Java se distribuye bajo [licencia de Apache, versión 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Bibliotecas de Azure para Java - descarga Manual

Para instalar manualmente las bibliotecas de Azure para Java, haga clic en <http://go.microsoft.com/fwlink/?LinkId=690320> para descargar un archivo ZIP que contiene todas las bibliotecas y todas las dependencias.

Una vez haya descargado el archivo zip en su equipo, extraer el contenido y utilice una de las siguientes opciones para agregar los archivos JAR a su proyecto:

* Importar los archivos JAR en el proyecto de Java en Eclipse.

* Configurar la **Ruta de acceso de compilación** para el proyecto de Java en Eclipse para incluir la ruta de acceso a los archivos JAR.

Para obtener información detallada sobre la configuración de compilación rutas en Eclipse, vea el artículo de la [Ruta de acceso de compilación de Java] en el sitio Web de Eclipse.

**Nota:** Consulte la license.txt y archivo ThirdPartyNotices.txt dentro del ZIP de licencia y otra información.

## <a name="azure-libraries-for-java---building-with-maven"></a>Bibliotecas de Azure para Java: creación de experto

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Paso 1: configurar el proyecto para usar Maven para generar

Crear proyectos de experto en Eclipse que usa las bibliotecas de Azure Java, siguiendo las instrucciones de la [Introducción a las bibliotecas de administración de Azure para Java] [ maven-getting-started] artículo. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Paso 2: Configure su experto con las dependencias necesarias

Una vez que el proyecto se configuró para usar Maven para generar, puede agregar la las dependencias necesarias al archivo pom.xml con sintaxis como el siguiente ejemplo. Tenga en cuenta que no es necesario agregar todas las dependencias que aparece en el ejemplo siguiente; solo debe agregar las dependencias específicas que requiere el proyecto.

> [AZURE.NOTE] Dentro de cada `<version>` elemento en el siguiente ejemplo, reemplace los marcadores de posición de "n.n.n" en este ejemplo con números de versión válida, que pueden obtenerse del [Repositorio de bibliotecas de Azure de experto].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalar el Kit de herramientas de Azure Eclipse

Esta sección contiene instrucciones básicas para instalar el Kit de herramientas de Azure para Eclipse; Para obtener instrucciones detalladas, vea [instalar el Kit de herramientas de Azure para Eclipse].

### <a name="prerequisites"></a>Requisitos previos

1. Sistemas de operativo Windows mencionados en el artículo [¿Qué es nuevo en el Kit de herramientas de Azure para Eclipse] .
1. Sistemas de operativo Macintosh o Linux mencionados en el artículo [¿Qué es nuevo en el Kit de herramientas de Azure para Eclipse] .
1. Eclipse IDE para los desarrolladores de Java EE, Indigo o posterior. Esto puede descargarse desde <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Pasos básicos de instalación

1. En Eclipse, en el menú **Ayuda** , seleccione **Instalar nuevo Software**.
1. Escriba la ubicación de sitio <http://dl.microsoft.com/eclipse> y presione **ENTRAR**.
1. Seleccione los elementos que desea instalar y haga clic en **Finalizar**.

El Kit de herramientas de Azure para Eclipse usa la última versión del SDK de Azure. Esto puede descargarse mediante el instalador de plataforma Web (WebPI) en <http://go.microsoft.com/fwlink/?LinkID=252838>. Sin embargo, si no tiene instalada, al crear su primer proyecto de implementación de Azure, el Kit de herramientas de Azure para Eclipse instalará automáticamente la versión adecuada del SDK de Azure.

## <a name="see-also"></a>Vea también

[Kit de herramientas de Azure para Eclipse]

[Instalar el Kit de herramientas de Azure Eclipse] 

[Crear una aplicación del mundo Hola para Azure en Eclipse]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Repositorio de Azure bibliotecas de experto]: http://go.microsoft.com/fwlink/?LinkID=286274
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Ruta de acceso de compilación de Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Novedades en el Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333
