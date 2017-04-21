<properties
    pageTitle="Crear y cargar un disco duro virtual de SUSE Linux en Azure"
    description="Aprenda a crear y cargar un Azure disco duro virtual (VHD) que contiene un sistema operativo SUSE Linux."
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar una máquina virtual SLES o openSUSE de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Requisitos previos ##

En este artículo se supone que ya ha instalado un SUSE o openSUSE sistema operativo Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd, por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [instalar el rol de Hyper-V y configurar una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE notas de instalación

- Vea también [Notas generales de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias sobre cómo preparar Linux para Azure.

- El formato VHDX no es compatible con Azure, sólo **fijo de disco duro virtual**.  Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el cmdlet de disco duro virtual de convertir.

- Al instalar el sistema Linux se recomienda que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos si lo prefiere.

- Configure una partición de intercambio en el disco de sistema operativo. El agente de Linux puede estar configurado para crear un archivo de intercambio en el disco temporal del recurso.  Encontrará más información sobre esto en los pasos siguientes.

- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.


## <a name="use-suse-studio"></a>Utilice SUSE Studio
[SUSE Studio](http://www.susestudio.com) fácilmente puede crear y administrar las imágenes SLES y openSUSE de Azure y Hyper-V. Este es el enfoque recomendado para personalizar sus propias imágenes SLES y openSUSE.

Como alternativa a la creación de su propio disco duro virtual, SUSE también publica imágenes BYOS (Traer su propia suscripción) para SLES en [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparar el Service Pack 4 SUSE Linux Enterprise Server 11 ##

1. En el panel central del Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

3. Registrar el sistema de SUSE Linux Enterprise para permitir que las actualizaciones de descargar e instalar paquetes.

4. Actualizar el sistema con las últimas revisiones:

        # sudo zypper update

5. Instalar al agente de Linux de Azure del repositorio de SLES:

        # sudo zypper install WALinuxAgent

6. Compruebe si waagent está establecido en "on" en chkconfig y, si no, habilite para iniciar automáticamente:
               
        # sudo chkconfig waagent on

7. Compruebe si el servicio de waagent se ejecuta y, si no es así, puede iniciarlo: 

        # sudo service waagent start
                
8. Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra "/ boot/grub/menu.lst" en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Esto se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración.

9. Confirme que /boot/grub/menu.lst y/etc/fstab referencia el disco mediante su UUID (por uuid) en vez del identificador de disco (por id). 

    Obtener disco UUID
    
        # ls /dev/disk/by-uuid/

    Si /dev/disk/by-id es usado, actualizar /boot/grub/menu.lst y/etcetera/fstab con el valor de nombre propio por uuid

    Antes de cambiar
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Después de cambiar
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modificar udev reglas para evitar generar reglas estáticas para las interfaces Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. Es recomendado para editar el archivo "/ etcetera/sysconfig/red/dhcp" y cambiar la `DHCLIENT_SET_HOSTNAME` parámetro a la siguiente:

        DHCLIENT_SET_HOSTNAME="no"

12. En "/ etcetera/sudoers", comente o quite las siguientes líneas si existen:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

14. No crear espacio de intercambio en el disco de sistema operativo.

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.


----------

## <a name="prepare-opensuse-131"></a>Preparar openSUSE 13.1 + ##

1. En el panel central del Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

3. En el shell, ejecute el comando '`zypper lr`'. Si el comando devuelve resultados similares a los siguientes, los repositorios configurados según lo esperado, no es necesario realizar ajustes (tenga en cuenta que los números de versión pueden variar):

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Si el comando no devuelve "repositorios definidos...", a continuación, utilice los siguientes comandos para agregar estos repos:

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    A continuación, puede comprobar los repositorios se han agregado al ejecutar el comando '`zypper lr`' nuevamente. En caso de que uno de los repositorios de actualización pertinente no está habilitada, habilitar con el siguiente comando:

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Actualizar el núcleo a la última versión disponible:

        # sudo zypper up kernel-default

    O bien para actualizar el sistema con todas las revisiones recientes:

        # sudo zypper update

5.  Instalar al agente de Linux Azure.

        # sudo zypper install WALinuxAgent

6.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra "/ boot/grub/menu.lst" en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Esto se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Además, quite los siguientes parámetros de la línea de inicio del núcleo si existen:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  Es recomendado para editar el archivo "/ etcetera/sysconfig/red/dhcp" y cambiar la `DHCLIENT_SET_HOSTNAME` parámetro a la siguiente:

        DHCLIENT_SET_HOSTNAME="no"

8.  **Importante:** En "/ etcetera/sudoers", comente o quite las siguientes líneas si existen:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

10. No crear espacio de intercambio en el disco de sistema operativo.

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Asegúrese de que se ejecuta el agente de Linux de Azure en el inicio:

        # sudo systemctl enable waagent.service

13. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.

## <a name="next-steps"></a>Pasos siguientes
Ahora ya está listo para usar el disco duro virtual de SUSE Linux para crear nuevas máquinas virtuales en Azure. Si es la primera vez que estás cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 en [crear y cargar un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
