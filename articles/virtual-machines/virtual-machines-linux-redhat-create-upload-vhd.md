<properties
    pageTitle="Crear y cargar un rojo sombrero Enterprise Linux disco duro virtual para su uso en Azure"
    description="Aprenda a crear y cargar un Azure disco duro virtual (VHD) que contiene un sistema operativo rojos sombrero Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar una máquina virtual basada en rojo sombrero de Azure

En este artículo, aprenderá a preparar una máquina virtual de Red sombrero Enterprise Linux (RHEL) para su uso en Azure. Versiones de RHEL que se tratan en este artículo son 6,7, 7.1 y 7.2. Para preparar los hipervisores en este artículo se tratan son Hyper-V, en función del núcleo Máquina Virtual (KVM) y VMware. Para obtener más información sobre los requisitos de elegibilidad para participar en el programa de acceso a la nube de sombrero de rojo, vea el [sitio Web de acceso a la nube de sombrero rojo](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) y [Ejecuta RHEL en Azure](https://access.redhat.com/articles/1989673).

[Preparar una máquina virtual de RHEL 6,7 desde el Administrador de Hyper-V](#rhel67hyperv)

[Preparar una máquina virtual de RHEL 7.1 y 7.2 desde el Administrador de Hyper-V](#rhel7xhyperv)

[Preparar una máquina virtual de RHEL 6,7 de KVM](#rhel67kvm)

[Preparar una máquina virtual de RHEL 7.1 y 7.2 de KVM](#rhel7xkvm)

[Preparar una máquina virtual de RHEL 6,7 de VMware](#rhel67vmware)

[Preparar una máquina virtual de RHEL 7.1 y 7.2 de VMware](#rhel7xvmware)

[Preparar una máquina virtual de RHEL 7.1 y 7.2 desde un archivo](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar una máquina virtual basada en rojo sombrero desde el Administrador de Hyper-V
### <a name="prerequisites"></a>Requisitos previos
Esta sección se supone que ya ha instalado una imagen RHEL (desde un archivo ISO que obtuvo desde el sitio Web de sombrero de color rojo) en un disco duro virtual (VHD). Para obtener más detalles sobre cómo usar el Administrador de Hyper-V para instalar una imagen de sistema operativo, vea [instalar el rol de Hyper-V y configurar una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalación de RHEL**

- Vea también [Notas generales de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias sobre cómo preparar Linux para Azure.

- El formato más reciente de VHDX no es compatible con Azure. Puede convertir el disco en el formato de disco duro virtual mediante el Administrador de Hyper-V o el cmdlet de PowerShell de **disco duro virtual de convertir** .

- VHD deben crearse como "fijo"--dinámicos VHD no son compatibles.

- Cuando va a instalar el sistema Linux, le recomendamos que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema. LVM o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos si lo prefiere.

- Configure una partición de intercambio en el disco de sistema operativo. Puede configurar el agente de Linux para crear un archivo de intercambio en el disco temporal del recurso. Más información sobre esto está disponible en los siguientes pasos.

- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.

- Al usar **img qemu** para convertir imágenes de disco al formato de disco duro virtual, tenga en cuenta que hay un error conocido en versiones de img qemu 2.2.1 o posterior. Este error se produce en un disco duro virtual con formato incorrecto. El problema está pensado para corregirse en un próximo lanzamiento de img qemu. Por ahora, le recomendamos que use qemu-img versión 2.2.0 o una versión anterior.

### <a id="rhel67hyperv"> </a>Preparar una máquina virtual de RHEL 6,7 desde el Administrador de Hyper-V###


1.  En el Administrador de Hyper-V, seleccione la máquina virtual.

2.  Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3.  Desinstalar NetworkManager ejecutando el siguiente comando:

        # sudo rpm -e --nodeps NetworkManager

    Tenga en cuenta que si ya no está instalado el paquete, este comando se producirá un error con un mensaje de error. Se espera.

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

6.  Mover reglas de udev para evitar generar reglas estáticas de la interfaz Ethernet (o quitar). Estas reglas causar problemas cuando clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Asegúrese de que se iniciará el servicio de red durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on

8.  Registrar su suscripción de sombrero de color rojo para habilitar la instalación de paquetes del repositorio de RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  El paquete de WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras sombrero rojo. Habilitar el repositorio de adicionales, ejecute el siguiente comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra `/boot/grub/menu.lst` en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Esto también garantiza que todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Esto deshabilitará NUMA debido a un error en la versión de kernel que se usa en RHEL 6.

    Además de la acción anterior, se recomienda que quite los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.

    La opción crashkernel puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reduzca la cantidad de memoria disponible en la máquina virtual de 128 MB o más. Esto podría ser problemático en pequeños VM.

11. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio. Normalmente, esto es el valor predeterminado. Modificar /etc/ssh/sshd_config para incluir la línea siguiente:

        ClientAliveInterval 180

12. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Observe que instalar el paquete de WALinuxAgent se quitará el NetworkManager y paquetes NetworkManager gnome si no las quitó ya tal como se describe en el paso 2.

13. No crear espacio de intercambio en el disco de sistema operativo.
El agente de Linux Azure puede configurar automáticamente el espacio de intercambio mediante el disco de recursos local que está conectado a la máquina virtual después de la máquina virtual se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Anular la suscripción (si es necesario), ejecute el siguiente comando:

        # sudo subscription-manager unregister

15. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Haga clic en **acción > Cerrar** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.
 

### <a id="rhel7xhyperv"> </a>Preparar una máquina virtual de RHEL 7.1 y 7.2 desde el Administrador de Hyper-V###

1.  En el Administrador de Hyper-V, seleccione la máquina virtual.

2.  Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

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

5.  Asegúrese de que se iniciará el servicio de red durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on

6.  Registrar su suscripción de sombrero de color rojo para habilitar la instalación de paquetes del repositorio de RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y editar el parámetro **GRUB_CMDLINE_LINUX** . Por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Esto también garantiza que todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Además de la acción anterior, se recomienda que quite los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.
    La opción crashkernel puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reduzca la cantidad de memoria disponible en la máquina virtual de 128 MB o más. Esto podría ser problemático en pequeños VM.

8.  Cuando termine de edición `/etc/default/grub`, ejecute el comando siguiente para volver a crear la configuración de grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio. Normalmente, esto es el valor predeterminado. Modificar `/etc/ssh/sshd_config` para incluir la línea siguiente:

        ClientAliveInterval 180

10. El paquete de WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras sombrero rojo. Habilitar el repositorio de adicionales, ejecute el siguiente comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. No crear espacio de intercambio en el disco de sistema operativo. El agente de Linux Azure puede configurar automáticamente el espacio de intercambio mediante el disco de recursos local que está conectado a la máquina virtual después de la máquina virtual se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los siguientes parámetros en `/etc/waagent.conf` correctamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Si desea anular la suscripción, ejecute el siguiente comando:

        # sudo subscription-manager unregister

14. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Haga clic en **acción > Cerrar** en el Administrador de Hyper-V. VHD Linux está preparado para cargarse en Azure.
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar una máquina virtual basada en rojo sombrero de KVM

### <a id="rhel67kvm"> </a>Preparar una máquina virtual de RHEL 6,7 de KVM###


1.  Descargue la imagen KVM de RHEL 6,7 de sitio Web de sombrero de color rojo.

2.  Establecer una contraseña de raíz.

    Generar una contraseña cifrada y copiar el resultado del comando:

        # openssl passwd -1 changeme

    Establecer una contraseña de raíz con guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Cambiar el segundo campo del usuario raíz de "!" a la contraseña cifrada.

3.  Crear una máquina virtual KVM desde la imagen qcow2, establezca el tipo de disco en **qcow2**y establezca el modelo de dispositivo de interfaz de red virtual como **virtio**. A continuación, inicie la máquina virtual e inicie sesión como raíz.

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

6.  Mover regla udev para evitar generar reglas estáticas de la interfaz Ethernet (o quitar). Estas reglas causar problemas cuando clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Asegúrese de que se iniciará el servicio de red durante el inicio, ejecute el siguiente comando:

        # chkconfig network on

8.  Registrar su suscripción de sombrero de color rojo para habilitar la instalación de paquetes del repositorio de RHEL ejecutando el siguiente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra `/boot/grub/menu.lst` en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Esto también garantiza que todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Esto deshabilitará NUMA debido a un error en la versión de kernel que se usa en RHEL 6.

    Además de la acción anterior, se recomienda que quite los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.
    La opción crashkernel puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reduzca la cantidad de memoria disponible en la máquina virtual de 128 MB o más. Esto podría ser problemático en pequeños VM.

10. Agregar módulos de Hyper-V en initramfs:  

    Editar `/etc/dracut.conf` y agregar contenido: add_drivers += "hv_vmbus hv_netvsc hv_storvsc"

    Volver a crear initramfs: # dracut – f - v

11. Desinstalar nube al inicio:

        # yum remove cloud-init

12. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse al inicio:

        # chkconfig sshd on

    Modificar /etc/ssh/sshd_config para incluir las siguientes líneas:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        # service sshd restart

13. El paquete de WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras sombrero rojo. Habilitar el repositorio de adicionales, ejecute el siguiente comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # yum install WALinuxAgent
        # chkconfig waagent on

15. El agente de Linux Azure puede configurar automáticamente el espacio de intercambio mediante el disco de recursos local que está conectado a la máquina virtual después de la máquina virtual se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en **/etc/waagent.conf** en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Anular la suscripción (si es necesario), ejecute el siguiente comando:

        # subscription-manager unregister

17. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Apagar la máquina virtual en KVM.

19. Convertir la imagen de qcow2 al formato de disco duro virtual.
    Convertir primero la imagen en formato:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. Redondear en caso contrario, el tamaño para alinear con 1 MB: # MB = $((1024*1024)) tamaño # = $(img qemu información -f bruto--salida json "rhel-6.7.raw" | \ gawk ' coincide con ($0, o "tamaño virtual": ([0-9] +), /, val) {imprimir val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Convertir el disco sin formato en un disco duro virtual de tamaño fijo:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Preparar una máquina virtual de RHEL 7.1 y 7.2 de KVM###


1.  Descargue la imagen KVM de RHEL 7.1 (o 7.2) desde el sitio Web de sombrero de color rojo. Usaremos RHEL 7.1 como el ejemplo a continuación.

2.  Establecer una contraseña de raíz.

    Generar una contraseña cifrada y copiar el resultado del comando:

        # openssl passwd -1 changeme

    Establecer una contraseña de raíz con guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Cambiar el segundo campo del usuario de raíz de "!" a la contraseña cifrada.

3.  Crear una máquina virtual KVM desde la imagen qcow2, establezca el tipo de disco en **qcow2**y establezca el modelo de dispositivo de interfaz de red virtual como **virtio**. A continuación, inicie la máquina virtual e inicie sesión como raíz.

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

6.  Asegúrese de que se iniciará el servicio de red durante el inicio, ejecute el siguiente comando:

        # chkconfig network on

7.  Registrar su suscripción de sombrero de color rojo para habilitar la instalación de paquetes del repositorio de RHEL ejecutando el siguiente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y editar el parámetro **GRUB_CMDLINE_LINUX** . Por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Esto también garantiza que todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Además de la acción anterior, se recomienda que quite los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.
    La opción crashkernel puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reduzca la cantidad de memoria disponible en la máquina virtual de 128 MB o más. Esto podría ser problemático en pequeños VM.

9.  Cuando termine de edición `/etc/default/grub`, ejecute el comando siguiente para volver a crear la configuración de grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Agregar módulos de Hyper-V en initramfs:

    Editar `/etc/dracut.conf` y agregar contenido:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruir initramfs:

        # dracut –f -v

11. Desinstalar nube al inicio:

        # yum remove cloud-init

12. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse al inicio:

        # systemctl enable sshd

    Modificar /etc/ssh/sshd_config para incluir las siguientes líneas:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        systemctl restart sshd

13. El paquete de WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras sombrero rojo. Habilitar el repositorio de adicionales, ejecute el siguiente comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # yum install WALinuxAgent

    Habilitar el servicio de waagent:

        # systemctl enable waagent.service

15. No crear espacio de intercambio en el disco de sistema operativo. El agente de Linux Azure puede configurar automáticamente el espacio de intercambio mediante el disco de recursos local que está conectado a la máquina virtual después de la máquina virtual se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los siguientes parámetros en `/etc/waagent.conf` correctamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Anular la suscripción (si es necesario), ejecute el siguiente comando:

        # subscription-manager unregister

17. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Apagar la máquina virtual en KVM.

19. Convertir la imagen de qcow2 al formato de disco duro virtual.

    Convertir primero la imagen en formato:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. En caso contrario, se redondea hacia arriba el tamaño para alinear con 1 MB:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Convertir el disco sin formato en un disco duro virtual de tamaño fijo:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar una máquina virtual basada en rojo sombrero de VMware
### <a name="prerequisites"></a>Requisitos previos
Esta sección se supone que ya ha instalado una máquina virtual RHEL en VMware. Para obtener más información sobre cómo instalar un sistema operativo en VMware, consulte la [Guía de instalación de sistema operativo de invitado de VMware](http://partnerweb.vmware.com/GOSIG/home.html).

- Cuando instala el sistema operativo Linux, le recomendamos que use particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco de sistema operativo nunca debe estar asociado a otra VM para solucionar este problema. LVM o RAID puede usarse en discos de datos si lo prefiere.

- Configure una partición de intercambio en el disco de sistema operativo. Puede configurar el agente de Linux para crear un archivo de intercambio en el disco temporal del recurso. Puede encontrar más información sobre esto en los siguientes pasos.

- Cuando se crea el disco duro virtual, seleccione **disco virtual de almacén como un solo archivo**.



### <a id="rhel67vmware"> </a>Preparar una máquina virtual de RHEL 6,7 de VMware###

1.  Desinstalar NetworkManager ejecutando el siguiente comando:

         # sudo rpm -e --nodeps NetworkManager

    Tenga en cuenta que si ya no está instalado el paquete, este comando se producirá un error con un mensaje de error. Se espera.

2.  Crear un archivo con el nombre de **red** en/etc./sysconfig/directorio que contiene el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  Cree un archivo llamado **eth0 ifcfg** en la /etc/sysconfig/network-scripts / directorio que contiene el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  Mover regla udev para evitar generar reglas estáticas de la interfaz Ethernet (o quitar). Estas reglas causar problemas cuando clonar una máquina virtual de Microsoft Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  Asegúrese de que se iniciará el servicio de red durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on

6.  Registrar su suscripción de sombrero de color rojo para habilitar la instalación de paquetes del repositorio de RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  El paquete de WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras sombrero rojo. Habilitar el repositorio de adicionales, ejecute el siguiente comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra "/ boot/grub/menu.lst" en un editor de texto y asegúrese de que el núcleo predeterminado incluye los parámetros siguientes:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Esto también garantiza que todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Esto deshabilitará NUMA debido a un error en la versión de kernel que se usa en RHEL 6.
    Además de la acción anterior, se recomienda que quite los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.
    La opción crashkernel puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reduzca la cantidad de memoria disponible en la máquina virtual de 128 MB o más. Esto podría ser problemático en pequeños VM.

9.  Agregar módulos de Hyper-V en initramfs:

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio. Normalmente, esto es el valor predeterminado. Modificar `/etc/ssh/sshd_config` para incluir la línea siguiente:

        ClientAliveInterval 180

11. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. No crear espacio de intercambio en el disco de sistema operativo:

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio mediante el disco de recursos local que está conectado a la máquina virtual después de la máquina virtual se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los siguientes parámetros en `/etc/waagent.conf` correctamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Anular la suscripción (si es necesario), ejecute el siguiente comando:

        # sudo subscription-manager unregister

14. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Apagar la máquina virtual y a continuación, convierta el archivo VMDK a un archivo .vhd.

    Convertir primero la imagen en formato:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. En caso contrario, se redondea hacia arriba el tamaño para alinear con 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Convertir el disco sin formato en un disco duro virtual de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Preparar una máquina virtual de RHEL 7.1 y 7.2 de VMware###

1.  Crear un archivo con el nombre de **red** en/etc./sysconfig/directorio que contiene el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  Cree un archivo llamado **eth0 ifcfg** en la /etc/sysconfig/network-scripts / directorio que contiene el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  Asegúrese de que se iniciará el servicio de red durante el inicio, ejecute el siguiente comando:

        # sudo chkconfig network on

4.  Registrar su suscripción de sombrero de color rojo para habilitar la instalación de paquetes del repositorio de RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  Modifique la línea de inicio del núcleo en la configuración de grub para incluir parámetros adicionales del núcleo para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y editar el parámetro **GRUB_CMDLINE_LINUX** . Por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Esto también garantiza que todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración. Además de la acción anterior, se recomienda que quite los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.
    La opción crashkernel puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reduzca la cantidad de memoria disponible en la máquina virtual de 128 MB o más. Esto podría ser problemático en pequeños VM.

6.  Cuando termine de edición `/etc/default/grub`, ejecute el comando siguiente para volver a crear la configuración de grub:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Agregar módulos de Hyper-V en initramfs:

    Editar `/etc/dracut.conf`, agregar contenido:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruir initramfs:

        # dracut –f -v

8.  Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio. Normalmente, esto es el valor predeterminado. Modificar `/etc/ssh/sshd_config` para incluir la línea siguiente:

        ClientAliveInterval 180

9.  El paquete de WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras sombrero rojo. Habilitar el repositorio de adicionales, ejecute el siguiente comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Instalar al agente de Linux de Azure, ejecute el siguiente comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. No crear espacio de intercambio en el disco de sistema operativo. El agente de Linux Azure puede configurar automáticamente el espacio de intercambio mediante el disco de recursos local que está conectado a la máquina virtual después de la máquina virtual se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los siguientes parámetros en `/etc/waagent.conf` correctamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Si desea anular la suscripción, ejecute el siguiente comando:

        # sudo subscription-manager unregister

13. Ejecute los comandos siguientes para activar la máquina virtual y prepararlos para aprovisionamiento en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Apagar la máquina virtual y a continuación, convierta el archivo VMDK al formato de disco duro virtual.

    Convertir primero la imagen en formato:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. En caso contrario, se redondea hacia arriba el tamaño para alinear con 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Convertir el disco sin formato en un disco duro virtual de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar una máquina virtual basada en rojo sombrero de ISO usando un archivo de forma automática


### <a id="rhel7xkickstart"> </a>Preparar una máquina virtual de RHEL 7.1 y 7.2 desde un archivo###


1.  Crear un archivo con el siguiente contenido y guarde el archivo. Para obtener más información sobre la instalación de comenzar, consulte la [Guía de instalación de comenzar](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  Coloque el archivo en un lugar accesible desde el sistema de la instalación.

3.  En el Administrador de Hyper-V, cree una nueva máquina virtual. En la página **Conectar disco duro Virtual** , seleccione **asociar un disco duro virtual más adelante**y completar al Asistente de máquina Virtual de nuevo.

4.  Abra la configuración de la máquina virtual:

    una.  Adjuntar un disco duro virtual nuevo a la máquina virtual. Asegúrese de seleccionar **Formato de disco duro virtual** y **Tamaño fijo**.

    b.  Adjuntar la instalación ISO a la unidad de DVD.

    c.  Configure el BIOS para iniciar desde el CD.

5.  Inicie la máquina virtual. Cuando aparezca la Guía de instalación, presione **ficha** para configurar las opciones de inicio.

6.  ENTRAR `inst.ks=<the location of the kickstart file>` al final de las opciones de inicio y a continuación, presione **ENTRAR**.

7.  Espere a que termine la instalación. Cuando haya terminado, la máquina virtual se cerrará automáticamente. VHD Linux está preparado para cargarse en Azure.

## <a name="known-issues"></a>Problemas conocidos
Hay problemas conocidos cuando se usa RHEL 7.1 en Hyper-V y Azure.

### <a name="disk-io-freeze"></a>Inmovilizar i/OS de disco

Este problema puede ocurrir durante las actividades de i/OS del disco de almacenamiento frecuentes con RHEL 7.1 en Hyper-V y Azure.   

Tasa de reproducción:

Este problema es intermitente. Sin embargo, se produce más frecuentemente durante frecuentes operaciones de i/OS de disco en Hyper-V y Azure.   


[AZURE.NOTE] Este problema conocido ya se ha tratado por sombrero rojo. Para instalar las revisiones asociadas, ejecute el siguiente comando:

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>El controlador de Hyper-V no se pudo incluir en el disco de RAM inicial cuando se usa un hipervisor-Hyper-V

En algunos casos, Linux instaladores podrían no incluir los controladores Hyper-v en el disco de RAM inicial (initrd o initramfs) a menos que detecta que se está ejecutando en un entorno de Hyper-V.

Cuando usa un sistema de virtualización diferente (es decir, Virtualbox, Xen, etc.) para preparar su imagen de Linux, tendrá que volver a crear initrd para asegurarse de que al menos los módulos de kernel de hv_vmbus y hv_storvsc están disponibles en el disco de RAM inicial. Esto es un problema conocido al menos en los sistemas en función de la distribución de sombrero rojo en sentido ascendente.

Para resolver este problema, debe agregar módulos de Hyper-V en initramfs y vuelva a crearlo:

Editar `/etc/dracut.conf` y agregar contenido:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Reconstruir initramfs:

        # dracut –f -v

Para obtener más detalles, consulte la información acerca de cómo [reconstruir initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Pasos siguientes
Ahora ya está listo para usar el disco duro virtual de Red sombrero Enterprise Linux para crear nuevas máquinas virtuales en Azure. Si es la primera vez que estás cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 en [crear y cargar un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Para obtener más detalles sobre la hipervisores que están certificados para ejecutar rojos sombrero Enterprise Linux, consulte [el sitio Web de sombrero de color rojo](https://access.redhat.com/certified-hypervisors).
