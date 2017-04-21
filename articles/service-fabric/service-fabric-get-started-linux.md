<properties
   pageTitle="Configurar el entorno de desarrollo en Linux | Microsoft Azure"
   description="Instale el SDK y el tiempo de ejecución y crear un clúster de desarrollo local en Linux. Después de completar esta configuración, estará listo para generar aplicaciones."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Preparar el entorno de desarrollo en Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Para implementar y ejecutar [aplicaciones de Azure servicio tela](service-fabric-application-model.md) en su equipo de desarrollo de Linux, instale el SDK comunes y el tiempo de ejecución. También puede instalar opcional SDK para Java y .NET principales.

## <a name="prerequisites"></a>Requisitos previos
### <a name="supported-operating-system-versions"></a>Versiones de sistema operativo compatible
Se admiten las siguientes versiones de sistema operativo para el desarrollo:

- Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Actualizar sus propias fuentes apt

Para instalar el SDK y el paquete de runtime asociado a través de apt get, primero debe actualizar sus propias fuentes apt.

1. Abra un terminal.
2. Agregar la repo tela de servicio a la lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Agregar la nueva clave GPG a sus claves apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Actualizar las listas de paquete en función de los repositorios recién agregados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Instalar y configurar el SDK

Una vez que se actualicen sus propias fuentes, puede instalar el SDK.

1. Instale el paquete de servicio tela SDK. Le pedirá que confirme la instalación y aceptar un contrato de licencia.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Ejecute la secuencia de comandos de instalación SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurar la CLI entre plataformas Azure

[Azure CLI entre plataformas] [ azure-xplat-cli-github] incluye comandos para interactuar con las entidades de tela de servicio, incluidos los clústeres y las aplicaciones. Se basa en Node.js por lo que deberá [asegurarse de que ha instalado el nodo] [ install-node] antes de continuar con las instrucciones a continuación.

1. Clonar la repo github a su equipo de desarrollo.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Cambiar a la repo duplicado e instale dependencias de CLI con el Administrador de paquetes de nodo (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Crear un vínculo simbólico desde la carpeta bin/azure de la repo duplicado a /usr/bin/azure para que se agrega a la ruta de acceso y comandos están disponibles desde cualquier directorio.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Por último, habilitar comandos de tela de servicio de Autocompletar.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Configurar un clúster local

Si ha instalado todo correctamente, debería poder iniciar un clúster local.

1. Ejecute la secuencia de comandos de instalación de clúster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra un navegador web y vaya a http://localhost:19080/explorador. Si ha iniciado el clúster, debería ver el panel Explorador de tela de servicio.

    ![Explorador de tela de servicio en Linux][sfx-linux]

En este momento, es posible implementar los paquetes de aplicaciones de servicio tela predefinidos o nuevos basados en contenedores de invitado o ejecutables de invitado. Para crear nuevos servicios mediante el Java o .NET Core SDK, siga los siguientes pasos de configuración opcionales.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Instale el complemento de Java SDK y Eclipse neón (opcional)

El SDK de Java proporciona las bibliotecas y plantillas necesarias para crear los servicios de servicio tela con Java.

1. Instalar el paquete del SDK de Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Ejecute la secuencia de comandos de instalación SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Puede instalar el complemento Eclipse para tela de servicio desde el IDE de neón Eclipse.

1. En Eclipse, asegúrese de que tiene Buildship versión 1.0.17 o posterior instalado. Puede comprobar las versiones de los componentes instalados eligiendo **Ayuda > detalles de la instalación**. Puede actualizar Buildship siguiendo las instrucciones [aquí][buildship-update].

2. Para instalar el complemento del servicio tela, elija **Ayuda > instalar nuevo Software...**

3. En el cuadro de texto "Trabajar con", escriba: http://dl.windowsazure.com/eclipse/servicefabric

4. Haga clic en Agregar.

    ![Complemento de Eclipse][sf-eclipse-plugin]

5. Elija el complemento de la estructura de servicio y haga clic en siguiente.

6. Continúe con la instalación y acepte el contrato de licencia de usuario final.

## <a name="install-the-net-core-sdk-optional"></a>Instalar .NET Core SDK (opcional)

.NET Core SDK proporciona las bibliotecas y plantillas necesarias para crear servicios de servicio tela con núcleo de .NET entre plataformas.

1. Instale el paquete de .NET Core SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Ejecute la secuencia de comandos de instalación SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Crear su primera aplicación de Java en Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Preparar el entorno de desarrollo de OSX](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
