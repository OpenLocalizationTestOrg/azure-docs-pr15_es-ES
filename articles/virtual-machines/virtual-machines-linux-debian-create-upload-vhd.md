<properties
    pageTitle="Preparar el disco duro virtual de Linux Debian | Microsoft Azure"
    description="Aprenda a crear Debian 7 y 8 archivos de disco duro virtual para su implementación en Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar un disco duro virtual Debian de Azure

## <a name="prerequisites"></a>Requisitos previos
Esta sección se supone que ya ha instalado un sistema operativo Linux Debian desde un archivo ISO descargado desde el [sitio Web Debian](https://www.debian.org/distrib/) en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; Hyper-V es sólo un ejemplo. Para obtener instrucciones mediante Hyper-V, vea [instalar el rol de Hyper-V y configurar una máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="installation-notes"></a>Notas de instalación

- Vea también [Notas generales de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias sobre cómo preparar Linux para Azure.
- El formato más reciente de VHDX no es compatible con Azure. Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el cmdlet de **disco duro virtual de convertir** .
- Al instalar el sistema Linux se recomienda que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos si lo prefiere.
- Configure una partición de intercambio en el disco de sistema operativo. El agente de Azure Linux se puede configurar para crear un archivo de intercambio en el disco temporal del recurso. Encontrará más información sobre esto en los pasos siguientes.
- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.


## <a name="use-azure-manage-to-create-debian-vhds"></a>Usar Administrar Azure para crear VHD Debian

Existen herramientas disponibles para generar Debian VHD para Azure, como la [azure-administrar](https://github.com/credativ/azure-manage) secuencias de comandos de [credativ](http://www.credativ.com/). Este es el enfoque recomendado en comparación con la creación de una imagen desde cero. Por ejemplo, para crear un VHD 8 Debian ejecute los comandos siguientes para descargar azure administrar (y dependencias) y ejecutar la secuencia de comandos de azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar un disco duro virtual Debian de forma manual

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3. Comentario de la línea para **CD-ROM de deuda** en `/etc/apt/source.list` si configura la máquina virtual con un archivo ISO.

4. Editar la `/etc/default/grub` y modifique el parámetro **GRUB_CMDLINE_LINUX** como sigue para incluir parámetros adicionales del núcleo para Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Reconstruir la grub y ejecutar:

        # sudo update-grub

6. Agregar repositorios de Azure de Debian a /etc/apt/sources.list para Debian 7 o 8:

    **7.x Debian "Wheezy"**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Instalar al agente de Linux Azure:

        # sudo apt-get update
        # sudo apt-get install waagent

8. Para Debian 7, se requiere para ejecutar el núcleo de 3,16 desde el repositorio de wheezy backports. Primero, cree un archivo llamado /etc/apt/preferences.d/linux.pref con el siguiente contenido:

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    A continuación, ejecute "sudo apt get instalar linux-imagen-amd64" para instalar el nuevo kernel.

8. Activar la máquina virtual y prepararlos para aprovisionamiento en Azure y ejecutar:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Haga clic en **acción** -> apagar hacia abajo en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.


## <a name="next-steps"></a>Pasos siguientes

Ahora ya está listo para usar Debian virtual disco duro para crear nuevas máquinas virtuales en Azure. Si es la primera vez que estás cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 en [crear y cargar un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
