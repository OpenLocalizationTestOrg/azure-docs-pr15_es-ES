<properties
    pageTitle="Crear y cargar un VHD Linux basado en CentOS en Azure"
    description="Aprenda a crear y cargar un Azure disco duro virtual (VHD) que contiene un sistema operativo basado en CentOS Linux."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar una máquina virtual basada en CentOS de Azure

- [Preparar una máquina virtual de 6.x de CentOS de Azure](#centos-6x)
- [Preparar una máquina virtual de CentOS 7.0 + de Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Requisitos previos ##

En este artículo se supone que ya ha instalado un CentOS (o derivado similar) sistema operativo de Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd, por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [instalar el rol de Hyper-V y configurar una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


**Notas de instalación de centOS**

- Vea también [Notas generales de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias sobre cómo preparar Linux para Azure.

- El formato VHDX no es compatible con Azure, sólo **fijo de disco duro virtual**.  Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el cmdlet de disco duro virtual de convertir.

- Al instalar el sistema Linux se recomienda que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema.  [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos si lo prefiere.

- No se admite NUMA tamaños de superiores VM debido a un error en las versiones de kernel Linux debajo de 2.6.37. Este problema afecta principalmente distribuciones utilizando la precede núcleo rojos sombrero 2.6.32. Instalación manual del agente de Azure Linux (waagent) deshabilitará NUMA automáticamente en la configuración de GRUB para el kernel de Linux. Encontrará más información sobre esto en los pasos siguientes.

- Configure una partición de intercambio en el disco de sistema operativo. El agente de Linux puede estar configurado para crear un archivo de intercambio en el disco temporal del recurso.  Encontrará más información sobre esto en los pasos siguientes.

- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.


## <a name="centos-6x"></a>CentOS 6.x ##

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3. Desinstalar NetworkManager ejecutando el siguiente comando:

        # sudo rpm -e --nodeps NetworkManager

    **Nota:** Si ya no está instalado el paquete, este comando se producirá un mensaje de error. Se espera.

4.  Crear un archivo con el nombre de **red** en la `/etc/sysconfig/` directorio que contiene el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Cree un archivo llamado **eth0 ifcfg** en la `/etc/sysconfig/network-scripts/` directorio que contiene el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificar udev reglas para evitar generar reglas estáticas para las interfaces Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Asegúrese de que el servicio de red se iniciará durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on


8. **CentOS 6.3 solo**: instale los controladores para los servicios de integración de Linux (vertical).

    **Importante: El paso es solo válido para CentOS 6.3 y versiones anteriores.**  En CentOS 6.4 + los servicios de integración de Linux están *ya disponibles en el núcleo estándar*.

    - Siga las instrucciones de instalación en la [página de descarga de vertical](https://www.microsoft.com/en-us/download/details.aspx?id=46842) e instalar el RPM en la imagen.  


9. Instalar el paquete de python pyasn1, ejecute el siguiente comando:

        # sudo yum install python-pyasn1

10. Si desea usar los reflejos OpenLogic que se hospedan en los centros de datos de Azure, a continuación, reemplace el archivo /etc/yum.repos.d/CentOS-Base.repo con los siguientes repositorios.  También agregará el repositorio de **[openlogic]** que incluye paquetes para el agente de Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Nota:** El resto de esta guía se presupone que está utilizando al menos el repo [openlogic], que se utilizará para instalar al agente de Azure Linux a continuación.


11. Agregue la siguiente línea a /etc/yum.conf:

        http_caching=packages

    Y **en CentOS 6.3 solo** agregue la línea siguiente:

        exclude=kernel*

12. Deshabilitar el módulo yum "fastestmirror" editando el archivo "/ etc/yum/pluginconf.d/fastestmirror.conf" y en `[main]`, escriba lo siguiente:

        set enabled=0

13. Ejecute el comando siguiente para borrar los metadatos yum actual:

        # yum clean all

14. **En CentOS 6.3 solo**, actualizar el núcleo mediante el siguiente comando:

        # sudo yum --disableexcludes=all install kernel

15. Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra "/ boot/grub/menu.lst" en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Esto también se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Esto deshabilitará NUMA debido a un error en la versión de kernel utilizada por CentOS 6.

    Además, se recomienda *Quitar* los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.

    La `crashkernel` opción puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reducir la cantidad de memoria disponible en la máquina virtual de 128MB o más, que pueden ser problemáticas en los tamaños VM más pequeños.


16. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

17. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent

    Observe que instalar el paquete de WALinuxAgent se quitará el NetworkManager y paquetes NetworkManager gnome si no las quitó ya tal como se describe en el paso 2.

18. No crear espacio de intercambio en el disco de sistema operativo.

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Cambios en CentOS 7 (y derivados similares)**

Preparar una máquina virtual de CentOS 7 Azure es muy similar al 6 de CentOS, pero hay varias diferencias importantes que tener en cuenta:

 - El paquete NetworkManager ya no está en conflicto con el agente de Azure Linux. Este paquete está instalado de forma predeterminada y se recomienda que no se elimina.
 - GRUB2 ahora se usa como el cargador de arranque de forma predeterminada, por lo que ha cambiado el procedimiento para modificar los parámetros de kernel (consulte a continuación).
 - XFS ahora es el sistema de archivos de forma predeterminada. El sistema de archivos ext4 aún puede utilizarse si lo desea.


**Pasos de configuración**

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3.  Crear un archivo con el nombre de **red** en la `/etc/sysconfig/` directorio que contiene el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Cree un archivo llamado **eth0 ifcfg** en la `/etc/sysconfig/network-scripts/` directorio que contiene el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificar udev reglas para evitar generar reglas estáticas para las interfaces Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Asegúrese de que el servicio de red se iniciará durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on

7. Instalar el paquete de python pyasn1, ejecute el siguiente comando:

        # sudo yum install python-pyasn1

8. Si desea usar los reflejos OpenLogic que se hospedan en los centros de datos de Azure, a continuación, reemplace el archivo /etc/yum.repos.d/CentOS-Base.repo con los siguientes repositorios.  También agregará el repositorio de **[openlogic]** que incluye paquetes para el agente de Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Nota:** El resto de esta guía se presupone que está utilizando al menos el repo [openlogic], que se utilizará para instalar al agente de Azure Linux a continuación.

9.  Ejecute el comando siguiente para borrar los metadatos yum actual e instale las actualizaciones:

        # sudo yum clean all
        # sudo yum -y update

10. Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abrir "/ predeterminado/etcetera/grub" en un editor de texto y editar los `GRUB_CMDLINE_LINUX` parámetro, por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Esto también se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. También desactiva la nuevas convenciones de nomenclatura de CentOS 7 para NIC. Además, se recomienda *Quitar* los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.

    La `crashkernel` opción puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reducir la cantidad de memoria disponible en la máquina virtual de 128MB o más, que pueden ser problemáticas en los tamaños VM más pequeños.

11. Una vez que haya terminado la edición "/ predeterminado/etcetera/grub" por encima, ejecute el comando siguiente para volver a generar la configuración de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

13. **Solo si la creación de la imagen de VMWare, VirtualBox o KVM:** Agregar módulos de Hyper-V en initramfs:

    Editar `/etc/dracut.conf`, agregar contenido:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruir la initramfs:

        # dracut –f -v

14. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. No crear espacio de intercambio en el disco de sistema operativo.

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.

## <a name="next-steps"></a>Pasos siguientes
Ahora ya está listo para usar el disco duro virtual de CentOS Linux para crear nuevas máquinas virtuales en Azure. Si es la primera vez que estás cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 en [crear y cargar un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
