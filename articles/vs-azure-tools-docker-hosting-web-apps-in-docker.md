<properties
   pageTitle="Implementar un contenedor de ASP.NET núcleo Linux Docker a un host remoto de Docker | Microsoft Azure"
   description="Aprenda a usar Visual Studio Tools para Docker para implementar una aplicación web de ASP.NET principales en un contenedor de Docker que se ejecuta en una máquina virtual Linux de Host de Azure Docker"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Implementar un contenedor de ASP.NET a un host de Docker remoto

## <a name="overview"></a>Información general
Docker es un motor de contenedor ligero, similar en algunas formas de una máquina virtual, que puede usar para alojar aplicaciones y servicios.
Este tutorial le guiará a través de uso de la extensión de [Visual Studio Tools de 2015 para Docker](http://aka.ms/DockerToolsForVS) para implementar una aplicación de ASP.NET Core a un host de Docker en Azure con PowerShell.

## <a name="prerequisites"></a>Requisitos previos
La siguiente es necesaria para completar este tutorial:

- Crear una máquina virtual de Azure Docker Host como se describe en [cómo usar docker máquina con Azure](./virtual-machines/virtual-machines-linux-docker-machine.md)
- Instalar la [Actualización de 2015 de Visual Studio 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK básico de Microsoft ASP.NET 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
- Instalar [Herramientas de 2015 de Visual Studio para Docker - vista previa](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. crear una aplicación web de ASP.NET principales
Los siguientes pasos le guiará a través de crear una aplicación de ASP.NET Core básica que se utilizará en este tutorial.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. agregar soporte Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. utilizar la secuencia de comandos de PowerShell DockerTask.ps1 

1.  Abra un símbolo del sistema de PowerShell en el directorio raíz de su proyecto. 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Validar el control remoto de host se está ejecutando. Debería ver estado = ejecución 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Si está usando la versión Beta de Docker, el host no se mencionan aquí.

1.  Generar el uso de la aplicación-parámetro de compilación

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] Si está usando la versión Beta de Docker, omita el equipo argumento -
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Ejecute la aplicación utilizando el - parámetro de ejecución

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] Si está usando la versión Beta de Docker, omita el equipo argumento -
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Una vez finalizada la docker, debería ver resultados similares a los siguientes:

    ![Ver la aplicación][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
