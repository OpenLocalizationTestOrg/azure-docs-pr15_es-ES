<properties
   pageTitle="Servicio tela e implementar contenedores en Linux | Microsoft Azure"
   description="Estructura de servicio y el uso de contenedores Docker implementar aplicaciones microservice. En este artículo se describe las capacidades que proporciona tela de servicio para los contenedores y cómo implementar una imagen de contenedor Docker en un clúster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Implementar un contenedor Docker en tela de servicio

> [AZURE.SELECTOR]
- [Implementar el contenedor de Windows](service-fabric-deploy-container.md)
- [Implementar Docker contenedor](service-fabric-deploy-container-linux.md)

En este artículo le guiará a través de la creación de servicios de contenido en los contenedores Docker en Linux.

Servicio tela tiene varias funciones de contenedor que pueden ayudarle con la creación de aplicaciones que se componen de microservices que son contenedores. Se trata de servicios de contenido.

Incluyen las capacidades;

- La activación y la implementación de la imagen de contenedor
- Gobernanza de recursos
- Autenticación de repositorio
- Puerto de contenedor para la asignación de puerto de host
- Comunicación y detección de contenedor a
- Capacidad de configurar y establecer variables de entorno


## <a name="packaging-a-docker-container-with-yeoman"></a>Empaquetar un contenedor de docker con yeoman
Al empaquetar un contenedor en Linux, puede elegir uno para usar una plantilla de yeoman o [crear de forma manual el paquete de aplicación](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

Una aplicación de servicio tela puede contener uno o varios contenedores, cada una con una función específica en la entrega de la funcionalidad de la aplicación. El SDK de tela de servicio para Linux incluye un generador de [Yeoman](http://yeoman.io/) que hace que sea fácil de crear la aplicación y agregar una imagen de contenedor. Vamos a usar a Yeoman para crear una nueva aplicación con un solo contenedor Docker denominado *SimpleContainerApp*. Puede agregar que más servicios más tarde si edita la generada manifiestos archivos.

## <a name="create-the-application"></a>Creación de la aplicación

1. En un terminal, escriba **yo azuresfguest**.

2. En el marco de trabajo elija **contenedor** .

3. Nombre de la aplicación, por ejemplo, SimpleContainerApp

4. Proporcionar la dirección URL de la imagen de contenedor desde un repo DockerHub. Se abrirá el formulario [repo] / [nombre de la imagen]

![Generador de servicio tela Yeoman de contenedores][sf-yeoman]

## <a name="deploy-the-application"></a>Implementar la aplicación

Una vez creado la aplicación, puede implementar al clúster local mediante la CLI de Azure.

1. Conectar con el clúster de tela de servicio local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilice la secuencia de comandos en la plantilla de instalación para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```

3. Abra un explorador y vaya al servicio tela explorador en http://localhost:19080/explorador (reemplazar host local con la dirección IP privada de la máquina virtual si usa a Vagrant en Mac OS X).

4. Expanda el nodo aplicaciones y observe que ahora hay una entrada para el tipo de aplicación y otro para la primera instancia de ese tipo.

5. Utilice la secuencia de comandos de desinstalación proporcionado en la plantilla para eliminar la instancia de aplicación y anular el registro del tipo de aplicación.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre la estructura de servicio y contenedores](service-fabric-containers-overview.md)
- [Interacción con clústeres de servicio tela mediante la CLI de Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

