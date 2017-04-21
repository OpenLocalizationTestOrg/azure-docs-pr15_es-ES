<properties
   pageTitle="Generación de línea de comandos para Azure | Microsoft Azure"
   description="Generación de línea de comandos de Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="command-line-build-for-azure"></a>Generación de línea de comandos de Azure

## <a name="overview"></a>Información general

Puede crear un paquete de implementación de Azure ejecutando MSBuild en el símbolo. Puede configurar y definir versiones de depuración, ensayo y producción, además de automatizar parte del proceso de generación.


## <a name="microsoft-build-engine-msbuild"></a>Motor de compilación de Microsoft (MSBuild)

Utilizando el motor de generación de Microsoft (MSBuild), puede crear productos en Diseñar entornos de laboratorio donde no está instalado Visual Studio. MSBuild utiliza un formato XML para archivos de proyecto de extensible y totalmente admitido por Microsoft. En este formato de archivo, puede describir elementos que deben ser integrado para una o varias plataformas y configuraciones.

También puede ejecutar MSBuild en un símbolo del sistema y este tema describe este enfoque. Al establecer propiedades en un símbolo del sistema, puede crear configuraciones específicas de un proyecto. Asimismo, también puede definir los objetivos que creará el comando MSBuild. Para obtener más información acerca de los parámetros de línea de comandos y MSBuild, vea [Referencia de la línea de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Instalación

Como se describe en el siguiente procedimiento, debe instalar software y herramientas en el servidor de compilación antes de crear un paquete de Azure mediante MSBuild:

1. Instalar .NET Framework 4 o versiones posteriores, que incluye MSBuild.

1. Instale las [Herramientas de creación de Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (busque MicrosoftAzureAuthoringTools x64.msi o MicrosoftAzureAuthoringTools x86.msi.

1. Instalar las [Bibliotecas de Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (busque MicrosoftAzureLibsForNet x64.msi o MicrosoftAzureLibs x86.msi.

1. Copie el archivo Microsoft.WebApplication.targets desde una instalación de Visual Studio en otro equipo.

    El archivo se encuentra en el directorio C:\Program Files (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 para Visual Studio 2012) y debe copiarla en el mismo directorio en el servidor de compilación.

1. Instale las [Herramientas de Azure para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Busque WindowsAzureTools.vs120.exe generar proyectos de Visual Studio de 2013.

## <a name="msbuild-parameters"></a>Parámetros de MSBuild

Es la manera más sencilla de crear un paquete ejecutar MSBuild con la `/t:Publish` opción. De forma predeterminada, este comando crea un directorio con respecto a la carpeta raíz del proyecto, por ejemplo, ProjectDir\bin\Configuration\app.publish\. al crear un proyecto de Azure, generar dos archivos, el archivo de paquete y el archivo adjunto de configuración:

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

De forma predeterminada, cada proyecto Azure incluye un archivo de configuración del servicio para local (depuración) crea y otro para versiones de nube (provisional o producción), pero puede agregar o quitar los archivos de configuración del servicio según sea necesario. Si genera un paquete de Visual Studio, se le pedirá que archivo de configuración del servicio para incluir junto con el paquete. Al crear un paquete mediante MSBuild, el archivo de configuración del servicio local se incluye de forma predeterminada. Para incluir un archivo de configuración del servicio diferente, establezca la `TargetProfile` propiedad del comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Si desea usar un directorio alternativo para el paquete almacenado y archivos de configuración, establezca la ruta de acceso mediante el `/p:PublishDir=Directory\` opción, incluido el separador de barra diagonal inversa al final.

## <a name="deployment"></a>Implementación

Una vez creado el paquete, puede implementarlo en Azure. Para obtener un tutorial que muestra que el proceso, consulte el sitio Web de Azure. Para obtener información sobre cómo automatizar este proceso, consulte [La entrega continua para los servicios de nube en Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
