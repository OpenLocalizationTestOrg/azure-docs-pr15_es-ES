<properties
    pageTitle="Crear y cargar un disco duro virtual de Ubuntu Linux en Azure"
    description="Aprenda a crear y cargar un Azure disco duro virtual (VHD) que contiene un sistema operativo de Ubuntu Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar una máquina virtual de Ubuntu de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Imágenes de nube Ubuntu oficiales
Ubuntu ahora publica VHD oficial de Azure para su descarga en [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Si necesita crear su propia imagen de Ubuntu especializada para Azure, en lugar de utilizar el siguiente procedimiento manual se recomienda comenzar con estos conocidos trabajar VHD y personalizarlo como sea necesario. Las últimas versiones de imagen siempre se encuentra en las siguientes ubicaciones:

 - Ubuntu 12.04/precisos: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/fiable equipo: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que ya ha instalado un sistema operativo de Ubuntu Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd, por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [instalar el rol de Hyper-V y configurar una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalación de Ubuntu**

- Vea también [Notas generales de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias sobre cómo preparar Linux para Azure.
- El formato VHDX no es compatible con Azure, sólo **fijo de disco duro virtual**.  Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el cmdlet de disco duro virtual de convertir.
- Al instalar el sistema Linux se recomienda que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos si lo prefiere.
- Configure una partición de intercambio en el disco de sistema operativo. El agente de Linux puede estar configurado para crear un archivo de intercambio en el disco temporal del recurso.  Encontrará más información sobre esto en los pasos siguientes.
- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.


## <a name="manual-steps"></a>Pasos manuales

> [AZURE.NOTE] Antes de crear su propia imagen de Ubuntu personalizada para Azure, considere usar las imágenes de [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) en su lugar.


1. En el panel central del Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

3.  Reemplace los repositorios actuales en la imagen para usar repos de Azure de Ubuntu. Los pasos varían ligeramente dependiendo de la versión de Ubuntu.

    Antes de modificar /etc/apt/sources.list, se recomienda realizar una copia de seguridad:

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. Las imágenes de Ubuntu Azure ahora están siguiendo el núcleo *habilitación de hardware* (HWE). Actualizar el sistema operativo del núcleo más reciente, ejecute los comandos siguientes:

    Ubuntu 12.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Modifique la línea de inicio del núcleo para Grub incluir parámetros adicionales del núcleo para Azure. Para hacer esto abrir "/ predeterminado/etcetera/grub" en un editor de texto, busque la variable denominada `GRUB_CMDLINE_LINUX_DEFAULT` (o agréguelo si es necesario) y editarlo para incluir los parámetros siguientes:

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Guardar y cerrar este archivo y, a continuación, ejecute '`sudo update-grub`'. Esto se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure con problemas de depuración de soporte técnico.

6.  Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

7.  Instalar al agente de Linux Azure:

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Tenga en cuenta que instalar el `walinuxagent` paquete se quitará el `NetworkManager` y `NetworkManager-gnome` paquetes, si están instalados.

8.  Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.


## <a name="next-steps"></a>Pasos siguientes
Ahora ya está listo para usar el disco duro virtual de Ubuntu Linux para crear nuevas máquinas virtuales en Azure. Si es la primera vez que estás cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 en [crear y cargar un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Referencias ##

Núcleo de Ubuntu hardware habilitación (HWE):

- [http://blog.utlemming.org/2015/01/Ubuntu-1404-Azure-images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-cloud-images-Now-Using-hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
