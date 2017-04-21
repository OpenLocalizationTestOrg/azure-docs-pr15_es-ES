<properties
    pageTitle="Crear y cargar un disco duro virtual de Oracle Linux | Microsoft Azure"
    description="Aprenda a crear y cargar un Azure disco duro virtual (VHD) que contiene un sistema operativo de Oracle Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar una máquina virtual de Oracle Linux de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Requisitos previos ##

En este artículo se supone que ya ha instalado un sistema operativo de Oracle Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd, por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [instalar el rol de Hyper-V y configurar una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


### <a name="oracle-linux-installation-notes"></a>Notas de instalación de Linux de Oracle

- Vea también [Notas generales de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias sobre cómo preparar Linux para Azure.

- Núcleo de Oracle rojos sombrero compatible y sus UEK3 (Kernel de Enterprise Unbreakable) son compatibles en Hyper-V y Azure. Para obtener los mejores resultados, no olvide actualizar a la última kernel durante la preparación de su disco duro virtual de Oracle Linux.

- UEK2 de Oracle no es compatible en Hyper-V y Azure como no incluye los controladores necesarios.

- El formato VHDX no es compatible con Azure, sólo **fijo de disco duro virtual**.  Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el cmdlet de disco duro virtual de convertir.

- Al instalar el sistema Linux se recomienda que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos si lo prefiere.

- No se admite NUMA tamaños de superiores VM debido a un error en las versiones de kernel Linux debajo de 2.6.37. Este problema afecta principalmente distribuciones utilizando la precede núcleo rojos sombrero 2.6.32. Instalación manual del agente de Azure Linux (waagent) deshabilitará NUMA automáticamente en la configuración de GRUB para el kernel de Linux. Encontrará más información sobre esto en los pasos siguientes.

- Configure una partición de intercambio en el disco de sistema operativo. El agente de Linux puede estar configurado para crear un archivo de intercambio en el disco temporal del recurso.  Encontrará más información sobre esto en los pasos siguientes.

- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.

- Asegúrese de que el `Addons` repositorio está habilitado. Editar el archivo `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) y cambie la línea `enabled=0` a `enabled=1` en **[ol6_addons]** o **[ol7_addons]** en este archivo.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

Debe completar los pasos de configuración específicos en el sistema operativo para la máquina virtual para que se ejecute en Azure.

1. En el panel central del Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

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

        # chkconfig network on

8. Instalar python pyasn1, ejecute el siguiente comando:

        # sudo yum install python-pyasn1

9.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra "/ boot/grub/menu.lst" en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Esto también se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Esto deshabilitará NUMA debido a un error en núcleo compatible de rojo sombrero de Oracle.

    Además, se recomienda *Quitar* los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.

    La `crashkernel` opción puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reducir la cantidad de memoria disponible en la máquina virtual de 128MB o más, que pueden ser problemáticas en los tamaños VM más pequeños.


10. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

11. Instalar al agente de Linux de Azure, ejecute el siguiente comando. La versión más reciente es 2.0.15.

        # sudo yum install WALinuxAgent

    Observe que instalar el paquete de WALinuxAgent se quitará el NetworkManager y paquetes NetworkManager gnome si no las quitó ya tal como se describe en el paso 2.

12. No crear espacio de intercambio en el disco de sistema operativo.

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Cambios en Oracle Linux 7**

Preparar una máquina virtual de Oracle Linux 7 Azure es muy similar a Oracle Linux 6, pero hay varias diferencias importantes que tener en cuenta:

 - El núcleo compatible sombrero rojo y UEK3 de Oracle son compatibles en Azure.  Se recomienda el núcleo UEK3.
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

5.  Modificar udev reglas para evitar generar reglas estáticas para las interfaces Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Asegúrese de que el servicio de red se iniciará durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on

7. Instalar el paquete de python pyasn1, ejecute el siguiente comando:

        # sudo yum install python-pyasn1

8.  Ejecute el comando siguiente para borrar los metadatos yum actual e instale las actualizaciones:

        # sudo yum clean all
        # sudo yum -y update

9.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para hacer esto abrir "/ predeterminado/etcetera/grub" en un editor de texto y editar los `GRUB_CMDLINE_LINUX` parámetro, por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Esto también se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. También desactiva la nuevas convenciones de nomenclatura OEL 7 para NIC. Además, se recomienda *Quitar* los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.

    La `crashkernel` opción puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reducir la cantidad de memoria disponible en la máquina virtual de 128MB o más, que pueden ser problemáticas en los tamaños VM más pequeños.


10. Una vez que haya terminado la edición "/ predeterminado/etcetera/grub" por encima, ejecute el comando siguiente para volver a generar la configuración de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

12. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. No crear espacio de intercambio en el disco de sistema operativo.

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Haga clic en **acción -> apagar hacia abajo** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.


## <a name="next-steps"></a>Pasos siguientes
Ahora ya está listo para usar su .vhd Oracle Linux para crear nuevas máquinas virtuales en Azure. Si es la primera vez que estás cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 en [crear y cargar un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
