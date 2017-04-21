<properties
   pageTitle="Configurar el entorno de desarrollo | Microsoft Azure"
   description="Instalar las herramientas, SDK y tiempo de ejecución y crear un clúster de desarrollo local. Después de completar esta configuración, estará listo para generar aplicaciones."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Preparar el entorno de desarrollo

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Para crear y ejecutar [aplicaciones de Azure servicio tela] [ 1] en su equipo de desarrollo, instale el tiempo de ejecución, SDK y herramientas. También debe habilitar la ejecución de las secuencias de comandos de Windows PowerShell incluido en el SDK.

## <a name="prerequisites"></a>Requisitos previos
### <a name="supported-operating-system-versions"></a>Versiones de sistema operativo compatible
Se admiten las siguientes versiones de sistema operativo para el desarrollo:

- Windows 7
- Windows 8, Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Sólo Windows 7 incluye Windows PowerShell 2.0 de forma predeterminada. Cmdlets de PowerShell de tela de servicio requiere PowerShell 3.0 o posterior. Puede [Descargar Windows PowerShell 5.0] [ powershell5-download] desde Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Instalar las herramientas, SDK y tiempo de ejecución

El instalador de plataforma Web ofrece dos configuraciones para el desarrollo de tela de servicio:

- [Instalar el tiempo de ejecución de servicio tela, SDK y herramientas de Visual Studio de 2015 (requiere la actualización de Visual Studio 2015 2 o posterior)][full-bundle-vs2015]
- [Instalar el servicio tela runtime y SDK sólo (sin Visual Studio tools)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Habilitar la ejecución de scripts de PowerShell

Servicio tela usa secuencias de comandos de Windows PowerShell para crear un clúster de desarrollo local y para implementar aplicaciones desde Visual Studio. De forma predeterminada, Windows impide que se ejecuten estas secuencias de comandos. Para habilitarlos, debe modificar la directiva de ejecución de PowerShell. Abra el PowerShell como administrador y escriba el siguiente comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha terminado de configurar su entorno de desarrollo, comenzar a crear y ejecutar aplicaciones.

- [Crear su primera aplicación de servicio tela en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Aprenda a implementar y administrar las aplicaciones en el clúster local](service-fabric-get-started-with-a-local-cluster.md)
- [Obtenga más información sobre los modelos de programación: servicios confiables y actores confiable](service-fabric-choose-framework.md)
- [Consulte los ejemplos de código de servicio tela en GitHub](https://aka.ms/servicefabricsamples)
- [Visualizar el clúster mediante el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md)
- [Siga la ruta de aprendizaje de tela de servicio para obtener una introducción amplia a la plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página de campaña de tela de servicio"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "RC VS"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Vínculo de VS WebPI de 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Vínculo de WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Vínculo de SDK WebPI principales"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
