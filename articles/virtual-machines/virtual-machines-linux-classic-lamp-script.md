<properties
    pageTitle="Usar la extensión CustomScript en una máquina virtual de Linux | Microsoft Azure"
    description="Aprenda a usar la extensión CustomScript para implementar aplicaciones en Linux máquinas virtuales de Windows en Azure creado con el modelo de implementación clásico."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Implementar una aplicación de luz con la CustomScript extensión de Azure para Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


La extensión de Microsoft Azure CustomScript para Linux ofrece una manera para personalizar sus máquinas virtuales (VM) mediante la ejecución de código arbitrario escrito en cualquier lenguaje de secuencias de comandos compatible con la máquina virtual (por ejemplo, Python y fiesta). Esto proporciona una manera muy flexible para automatizar la implementación de aplicaciones en varios equipos.

Puede implementar la CustomScript extensión con el portal clásico Azure, Windows PowerShell o la interfaz de línea de comandos de Azure (Azure CLI).

En este artículo que usaremos CLI Azure para implementar una aplicación de luz simple para una VM Ubuntu creados con el modelo de implementación clásico.

## <a name="prerequisites"></a>Requisitos previos

En este ejemplo, cree primero dos máquinas virtuales de Azure con Ubuntu 14.04 o posterior. Las máquinas virtuales se denominan *script vm* y *vm de luz*. Usar nombres únicos al crear las máquinas virtuales. Uno se usa para ejecutar los comandos de CLI y una se utiliza para implementar la aplicación de luz.

También necesita una cuenta de almacenamiento de Azure y una clave para obtener acceso a él (que puede ir desde el portal de clásico Azure).

Si necesita ayuda para crear máquinas virtuales de Linux en Azure consulte [crear Máquina Virtual ejecuta Linux](virtual-machines-linux-classic-createportal.md).

Los comandos de instalación, suponen Ubuntu, pero puede adaptar la instalación para cualquier distro Linux compatible.

La máquina virtual de script vm debe tener CLI Azure instalado, con una conexión de trabajo a Azure. Para obtener ayuda sobre este consulte [instalar y configurar la interfaz de línea de comandos de Azure](../xplat-cli-install.md).

## <a name="upload-a-script"></a>Cargar una secuencia de comandos

Usaremos la CustomScript extensión para ejecutar una secuencia de comandos en una máquina virtual remota para instalar la pila de luz y crear una página PHP. Para obtener acceso a la secuencia de comandos desde cualquier lugar se cargará como una blobs de Windows Azure.

### <a name="script-overview"></a>Información general de secuencia de comandos

El ejemplo de script instala una pila de luz a Ubuntu (incluida la configuración de una instalación silenciosa de MySQL), escribe un archivo PHP simple y Apache inicia.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Cargar script

Guarde el script como un archivo de texto, por ejemplo *install_lamp.sh*y luego cárguelo en el almacenamiento de Azure. Puede hacerlo fácilmente con CLI de Azure. En el ejemplo siguiente se carga el archivo en un contenedor de almacenamiento denominado "secuencias de comandos". Si no existe el contenedor debe crearla primero.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Crear un archivo JSON que describe cómo descargar la secuencia de comandos de almacenamiento de Azure. Guardar como *public_config.json* (reemplace "mystorage" por el nombre de su cuenta de almacenamiento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Implementar la extensión

Ahora puede usar el comando siguiente para implementar la CustomScript extensión Linux VM remoto mediante la CLI de Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

El comando anterior se descarga y ejecuta la secuencia de comandos de *install_lamp.sh* en la máquina virtual denominada *vm lámpara*.

Dado que la aplicación incluye un servidor web, no olvide abrir un puerto de escucha HTTP en la máquina virtual remoto con el comando siguiente.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Supervisión y solución de problemas

Puede comprobar cómo se ejecuta el script personalizado consultando el archivo de registro en la máquina virtual remoto. SSH *lámpara vm* y cola el archivo de registro con el comando siguiente.

    cd /var/log/azure/customscript
    tail -f handler.log

Después de ejecutar la CustomScript extensión, puede ir a la página PHP que creó para obtener más información. La página PHP en el ejemplo de este artículo es *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Recursos adicionales

Puede usar los mismos pasos básicos para implementar aplicaciones más complejas. En este ejemplo se guardó la secuencia de comandos de instalación como un blob público en el almacenamiento de Azure. Una opción más segura sería almacenar la secuencia de comandos de instalación como un blob seguro con una [Firma de acceso seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SA).

Recursos adicionales para Azure CLI, Linux y la CustomScript extensión se muestran a continuación.

[Automatizar tareas de personalización de máquina virtual Linux con extensión CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensiones de Azure Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Informática en Azure de código abierto y Linux](virtual-machines-linux-opensource-links.md)
