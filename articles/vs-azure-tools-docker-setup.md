<properties
   pageTitle="Configurar un Host de Docker con VirtualBox | Microsoft Azure"
   description="Instrucciones paso a paso para configurar una instancia de Docker predeterminado con Machine Docker y VirtualBox"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Configurar un Host de Docker con VirtualBox

## <a name="overview"></a>Información general
En este artículo le guiará en el proceso de configuración de una instancia de Docker predeterminada con Machine Docker y VirtualBox. Si está usando la [versión beta de Docker para Windows](http://beta.docker.com/), esta configuración no es necesaria.

## <a name="prerequisites"></a>Requisitos previos
Las siguientes herramientas deben instalarse.

- [Cuadro de herramientas docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configuración del cliente de Docker con Windows PowerShell

Para configurar a un cliente de Docker, simplemente abrir Windows PowerShell y realice los pasos siguientes:

1. Crear una instancia de host de docker predeterminado.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Compruebe que la instancia predeterminada está configurado y en ejecución. (Verá una instancia denominada 'predeterminado' ejecutando.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![resultados de la máquina docker ls][0]
 
1. Establecer predeterminado como el host actual y configurar su shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Mostrar los contenedores Docker activos. La lista debe estar vacía.

    ```PowerShell
    docker ps
    ```

    ![resultado de docker ps][1]
 
> [AZURE.NOTE]Cada vez que reinicie el equipo de desarrollo, deberá reiniciar el host docker local.
> Para ello, ejecute el siguiente comando en un símbolo del sistema: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
