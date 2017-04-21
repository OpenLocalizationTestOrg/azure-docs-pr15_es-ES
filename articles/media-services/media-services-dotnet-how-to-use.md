<properties 
    pageTitle="Cómo configurar el equipo para el desarrollo de servicios de medios con .NET" 
    description="Obtenga información sobre los requisitos previos para Media Services con el SDK de servicios de medios para .NET. También Obtenga información sobre cómo crear una aplicación de Visual Studio." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Desarrollo de Media Services con .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

En este tema se explica cómo desarrollar aplicaciones de Media Services utilizando .NET.

La biblioteca de **.NET SDK de Azure Media Services** le permite programar Media Services utilizando .NET. Para que sea aún más fáciles de desarrollar con. NET, se proporciona la biblioteca de **.NET SDK extensiones de Azure Media Services** . Esta biblioteca contiene un conjunto de métodos de extensión y funciones auxiliares que simplifican el código de .NET. Las dos bibliotecas están disponibles a través de **NuGet** y **GitHub**.


##<a name="prerequisites"></a>Requisitos previos

-   Una cuenta de Media Services en una suscripción de Azure nueva o existente. Vea el tema [cómo crear una cuenta de servicios de medios](media-services-portal-create-account.md).
-   Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
-   .NET framework 4.5.
-    2015 de Visual Studio, 2013 de Visual Studio, Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Crear y configurar un proyecto de Visual Studio

Esta sección muestra cómo crear un proyecto de Visual Studio y configurarla para el desarrollo de Media Services.  En este caso, el proyecto es una aplicación de consola de Windows en C#, pero los mismos pasos de configuración que se muestra aquí se aplican a otros tipos de proyectos que puede crear para las aplicaciones de Media Services (por ejemplo, una aplicación de Windows Forms o una aplicación Web de ASP.NET).

Esta sección muestra cómo usar **NuGet** para agregar .NET SDK de Media Services y otras bibliotecas de dependientes.

Como alternativa, puede obtener los bits de Media Services .NET SDK más recientes de GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) y [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), generar la solución y agregue las referencias para el proyecto de cliente. Tenga en cuenta que todas las dependencias es necesarias obtengan descargadas y extraídas automáticamente.

1. Crear una nueva aplicación de consola de C# en Visual Studio 2010 SP1 o versiones posteriores de VS. Escriba el **nombre**, la **ubicación**y el **nombre de la solución**y, a continuación, haga clic en Aceptar.

2. Generar la solución.

2. Use **NuGet** para instalar y agregar **.NET SDK extensiones de Azure Media Services**. Instalar el paquete, también instala **.NET SDK de Media Services** y agrega todas las dependencias necesarias.

    Asegurarse de que tiene la versión más reciente de NuGet instalado. Para obtener más información e instrucciones de instalación, consulte [NuGet](http://nuget.codeplex.com/).

2. En el Explorador de soluciones, haga clic en el nombre del proyecto y elija Administrar NuGet paquetes...

    Aparece el cuadro de diálogo Administrar paquetes de NuGet.

3. En la galería en línea, busque las extensiones de MediaServices de Azure, elija .NET SDK extensiones de Azure Media Services y, a continuación, haga clic en el botón instalar.

    El proyecto se modifica y se agregan referencias a las extensiones de Media Services .NET SDK, .NET SDK de Media Services y otros ensamblados dependientes.

4. Para promover un entorno de desarrollo más limpio, considere la posibilidad de habilitar NuGet paquete restaurar. Para obtener más información, vea [Restaurar de paquete NuGet "](http://docs.nuget.org/consume/package-restore).

3. Agregar una referencia a ensamblado **System.Configuration** . Este ensamblado contiene la System.Configuration. Clase **ConfigurationManager** que se utiliza para acceder a los archivos de configuración (por ejemplo, App.config).

    Para agregar referencias mediante el cuadro de diálogo Administrar referencias, haga clic en el nombre del proyecto en el Explorador de soluciones. A continuación, seleccione Agregar y referencias.

    Aparece el cuadro de diálogo Administrar referencias.

4. En ensamblados de .NET framework, buscar y seleccionar el ensamblado System.Configuration y haga clic en Aceptar.
5. Abra el archivo App.config (agregue el archivo a un proyecto si no se agregó de forma predeterminada) y agregue una sección *appSettings* al archivo.     
Establecer los valores de la clave de nombre y la cuenta de cuenta de Azure Media Services, como se muestra en el ejemplo siguiente.

    Para buscar el nombre y la clave de valores, vaya al portal de Azure y seleccione la cuenta. Aparece la ventana de configuración de la derecha. En la ventana Configuración, seleccione teclas. Al hacer clic en el icono junto a cada cuadro de texto, copia el valor al Portapapeles del sistema.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Sobrescribir la existente instrucciones **using** al principio del archivo Program.cs con el código siguiente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

En este momento, está listo para empezar a desarrollar una aplicación de servicios de medios.    


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
