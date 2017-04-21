<properties
   pageTitle="Configurar el entorno de desarrollo en Mac OS X | Microsoft Azure"
   description="Instalar las herramientas, SDK y tiempo de ejecución y crear un clúster de desarrollo local. Después de completar esta configuración, estará listo para crear aplicaciones en Mac OS X."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar el entorno de desarrollo en Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Puede crear aplicaciones de servicio tela ejecuten en clústeres Linux con Mac OS X. En este artículo se describe cómo configurar Mac para el desarrollo.

## <a name="prerequisites"></a>Requisitos previos

Tela de servicio no se ejecuta de forma nativa en OS X. Para ejecutar un clúster de servicio tela local, le proporcionamos un equipo virtual de Ubuntu preconfigurado con Vagrant y VirtualBox. Antes de empezar, debe:

- [Vagrant (v1.8.4 o posterior)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Crear la VM local

Para crear la máquina virtual local que contiene un clúster de 5 nodos tela de servicio, haga lo siguiente:

1. Clonar la repo Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Vaya a la copia local de la repo

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Opcional) Modificar la configuración predeterminada de la máquina virtual

    De forma predeterminada, la máquina virtual local está configurada como sigue:

    - 3 GB de memoria asignada
    - Red privada host configurado en la dirección IP 192.168.50.50 habilitar paso a través de tráfico desde el host de Mac

    Puede cambiar cualquiera de estas opciones o agregar otra configuración de la máquina virtual de la Vagrantfile. Consulte la [documentación de Vagrant](http://www.vagrantup.com/docs) la lista completa de opciones de configuración.

4. Crear la máquina virtual

    ```bash
    vagrant up
    ```

    Este paso descarga la imagen de VM preconfigurada, inicio se localmente y a continuación, configure una estructura local de servicio de clúster en él. Debe esperar que llevar algunos minutos. Si el programa de instalación se realiza correctamente, verá un mensaje en el resultado que indica que se está iniciando el clúster.

    ![Programa de instalación iniciando siguiendo el aprovisionamiento de máquina virtual de clúster][cluster-setup-script]

5. Para comprobar que el clúster se ha configurado correctamente desplazándose hasta el Explorador de tela de servicio en http://192.168.50.50:19080/explorador (suponiendo que mantiene el IP de una red privada de forma predeterminada).

    ![Explorador de tela de servicio se ven en el host de Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Instale el complemento de servicio tela de neón Eclipse (opcional)

Servicio tela proporciona un complemento para IDE Eclipse neón que puede simplificar el proceso de creación e implementación de servicios de Java.

1. En Eclipse, asegúrese de que tiene Buildship versión 1.0.17 o posterior instalado. Puede comprobar las versiones de los componentes instalados eligiendo **Ayuda > detalles de la instalación**. Puede actualizar Buildship siguiendo las instrucciones [aquí][buildship-update].

2. Para instalar el complemento del servicio tela, elija **Ayuda > instalar nuevo Software...**

3. En el cuadro de texto "Trabajar con", escriba: http://dl.windowsazure.com/eclipse/servicefabric.

4. Haga clic en Agregar.

    ![Complemento de neón Eclipse para tela de servicio][sf-eclipse-plugin-install]

5. Elija el complemento de la estructura de servicio y haga clic en siguiente.

6. Continúe con la instalación y acepte el contrato de licencia de usuario final.

## <a name="next-steps"></a>Pasos siguientes

- [Crear su primera aplicación de servicio tela para Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Crear un clúster de tela de servicio en el portal de Azure](service-fabric-cluster-creation-via-portal.md)
- [Crear un clúster de tela de servicio mediante el Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md)
- [Entender el modelo de aplicación de servicio tela](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
