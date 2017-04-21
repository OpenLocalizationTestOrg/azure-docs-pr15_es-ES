<properties
    pageTitle="Instalar la interfaz de línea de comandos de Azure | Microsoft Azure"
    description="Instalar la interfaz de línea de comandos de Azure (CLI) para Mac, Linux y Windows empezar a usar los servicios de Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Instalar la CLI de Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI de Azure](xplat-cli-install.md)

Instalar rápidamente la interfaz de línea de comandos de Azure (Azure CLI) para usar un conjunto de código abierto basado en el shell de comandos para crear y administrar recursos en Microsoft Azure. Tiene varias opciones para instalar estas herramientas entre plataformas en su equipo: 

* **paquete de npm** - ejecutar npm (el Administrador de paquete para JavaScript) para instalar el paquete de Azure CLI más reciente en su distribución Linux o sistema operativo. Requiere node.js y npm en su equipo.
* **Instalador** - descarga un instalador para facilitar la instalación en Mac o Windows.
* **Contenedor de docker** : empezar a usar la última CLI en un contenedor de Docker listos para ejecutarse. Requiere host Docker en el equipo.
    
Para obtener más opciones y fondo, vea el repositorio de proyecto en [GitHub](https://github.com/azure/azure-xplat-cli). 

Una vez instalada, [conectarlo a su suscripción de Azure](xplat-cli-connect.md) y ejecute los comandos de **azure** desde la interfaz de línea de comandos (fiesta, Terminal, símbolo y así sucesivamente) para trabajar con los recursos de Azure CLI Azure.



## <a name="option-1-install-an-npm-package"></a>Opción 1: Instalar un paquete de npm

Para instalar la CLI desde un paquete npm, asegúrese de que haya descargado e instalado las [últimas Node.js y npm](https://nodejs.org/en/download/package-manager/). A continuación, ejecute **npm instalar** para instalar el paquete cli de azure: 

    npm install -g azure-cli

En las distribuciones de Linux, debe usar **sudo** para poder ejecutar correctamente el comando __npm__ , como sigue:

    sudo npm install -g azure-cli

> [AZURE.NOTE]Si necesita instalar o actualizar Node.js y npm en su distribución Linux o sistema operativo, le recomendamos que instale la versión más reciente de Node.js LTADOS (4.x). Si usa una versión anterior, puede obtener errores de instalación. 

Si lo prefiere, descargue el [archivo tar] de Linux más reciente[ linux-installer] para el paquete npm localmente. A continuación, instalar el paquete de npm descargados como sigue (en las distribuciones de Linux que debe usar **sudo**):

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Opción 2: Usar un instalador

Si usa un equipo Mac o Windows, los siguientes archivos de instalación CLI están disponibles para su descarga:

* [Programa de instalación de Mac OS X][mac-installer]

* [MSI de Windows][windows-installer] 

>[AZURE.TIP]En Windows, también puede descargar el [Instalador de plataforma Web](https://go.microsoft.com/?linkid=9828653) para instalar la CLI. Este instalador le da la opción de instalar el SDK de Azure adicionales y herramientas de línea de comandos después de instalar CLI. 


## <a name="option-3-use-a-docker-container"></a>Opción 3: Usar un contenedor de Docker

Si ha configurado su equipo como un host de [Docker](https://docs.docker.com/engine/understanding-docker/) , puede ejecutar la última CLI de Azure en un contenedor Docker. (En las distribuciones de Linux que debe usar **sudo**), ejecute el siguiente comando:

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Ejecute los comandos CLI de Azure
Después de instalar CLI Azure, ejecute el comando de **azure** desde la interfaz de usuario de línea de comandos (fiesta, Terminal, símbolo y así sucesivamente). Por ejemplo, para ejecutar el comando de ayuda, escriba lo siguiente:

```
azure help
```
> [AZURE.NOTE]En algunas distribuciones de Linux, puede recibir un error similar al `/usr/bin/env: ‘node’: No such file or directory`. Este error proviene de instalaciones recientes de Node.js se instala en /usr/bin/nodejs. Para corregir esto, cree un vínculo simbólico a /usr/bin/node al ejecutar este comando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver la versión de CLI Azure instaló, escriba lo siguiente:

```
azure --version
```

¡Ahora ya está listo! Para obtener acceso a todos los comandos CLI para trabajar con sus propios recursos, [conectarse a su suscripción de Azure desde la CLI de Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Al empezar a usar CLI de Azure, verá un mensaje que le pregunta si desea permitir que Microsoft recopile información de uso. Participación es voluntaria. Si decide participar, puede detener en cualquier momento ejecutando `azure telemetry --disable`. Para habilitar la participación en cualquier momento, ejecute `azure telemetry --enable`.


## <a name="update-the-cli"></a>Actualizar la CLI

Microsoft publica con frecuencia versiones actualizadas de Azure CLI. Vuelva a instalar la CLI mediante el programa de instalación para su sistema operativo o ejecutar el contenedor Docker más reciente. O bien, si tiene las últimas Node.js y npm instalado, actualice escribiendo los siguientes (en las distribuciones de Linux que debe usar **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Habilitar la finalización de pestaña

Finalización de la pestaña de comandos CLI es compatible para Mac y Linux.

Para habilitar la en zsh, ejecute:

```
echo '. <(azure --completion)' >> .zshrc
```

Para habilitar la en fiesta, ejecute:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Pasos siguientes 

* [Conectar desde la CLI a su suscripción de Azure](xplat-cli-connect.md) para crear y administrar recursos de Azure.

* Para obtener más información acerca de la CLI de Azure, descargar el código fuente, informar de problemas o contribuir al proyecto, visite el [repositorio de GitHub para CLI Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene preguntas sobre el uso de la CLI de Azure o Azure, visite los [Foros de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Si lo desea, también puede probar el basado en Python [Azure CLI 2.0 Preview](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
