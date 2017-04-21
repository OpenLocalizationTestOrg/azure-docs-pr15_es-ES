<properties
    pageTitle="Crear y cargar un VHD Linux en Azure"
    description="Aprenda a crear y cargar un Azure disco duro virtual (VHD) que contiene un sistema operativo Linux."
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
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Información de apoyo no distribuciones #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Importante**: Azure la plataforma SLA se aplica a máquinas virtuales que ejecutan el sistema operativo Linux solo cuando se usa una de las [distribuciones de apoyo](virtual-machines-linux-endorsed-distros.md) . Todas las distribuciones de Linux que se proporcionan en la Galería de imágenes de Azure son distribuciones visadas con la configuración necesaria.

- [Linux en Azure - apoyo distribuciones](virtual-machines-linux-endorsed-distros.md)
- [Soporte técnico para las imágenes de Linux en Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas las distribuciones que se ejecuta en Azure tendrán que cumplen a un número de requisitos previos para que se puedan ejecutar correctamente en la plataforma.  En este artículo no está completo, como cada distribución es diferente; y es posible que incluso si cumple los criterios siguientes aún tendrán que significativamente ajustar su sistema Linux para asegurarse de que se ejecute correctamente en la plataforma.

Es por este motivo que se recomienda que comience con uno de nuestros [Linux en Azure apoyo distribuciones](virtual-machines-linux-endorsed-distros.md) cuando sea posible. Los artículos siguientes le guiará para preparar las distribuciones de Linux visadas diversos que son compatibles en Azure:

- **[Distribuciones de centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Linux Debian](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Rojos sombrero Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

El resto de este artículo se centrará en las directrices generales para ejecutar la distribución de Linux en Azure.


## <a name="general-linux-installation-notes"></a>Notas de instalación de Linux general ##

- El formato VHDX no es compatible con Azure, sólo **fijo de disco duro virtual**.  Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el cmdlet de disco duro virtual de convertir. Si está utilizando VirtualBox Esto significa seleccionar **tamaño fijo** en lugar de predeterminado que se asigna dinámicamente al crear el disco.

- Al instalar el sistema Linux es *recomienda* utilizar particiones estándar en lugar de LVM (a menudo predeterminado para las instalaciones de muchos). Esto evitará LVM nombre entra en conflicto con VM duplicadas, especialmente si un disco OS necesite asociarán a otra VM idénticos para solucionar este problema. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) puede usarse en discos de datos.

- Compatibilidad de núcleo para montaje de sistemas de archivo UDF es necesario. En el primer inicio en Azure se pasa la configuración de aprovisionamiento VM Linux a través de medios con formato UDF que está conectados a los invitados. El agente de Azure Linux debe poder montaje el sistema de archivos UDF para leer su configuración y aprovisionar la máquina virtual.

- Las versiones de kernel Linux debajo 2.6.37 no admiten NUMA en Hyper-V con tamaños VM más grandes. Este problema afecta principalmente anteriores distribuciones utilizando la precede kernel rojos sombrero 2.6.32 y se ha corregido en RHEL 6.6 (núcleo-2.6.32-504). Sistemas que ejecutan kernels personalizados anteriores a 2.6.37 o basado en RHEL kernels anteriores de 2.6.32-504 debe establecer el parámetro de inicio `numa=off` en la línea de comandos en grub.conf núcleo. Para obtener más información, vea rojos sombrero [436883 KB](https://access.redhat.com/solutions/436883).

- Configure una partición de intercambio en el disco de sistema operativo. El agente de Linux puede estar configurado para crear un archivo de intercambio en el disco temporal del recurso.  Encontrará más información sobre esto en los pasos siguientes.

- Todos los archivos VHD deben tener tamaños de múltiplos de 1 MB.


### <a name="installing-linux-without-hyper-v"></a>Instalar Linux sin Hyper-V ###

En algunos casos, Linux instaladores pueden no incluye los controladores para Hyper-V en el disco de RAM inicial (initrd o initramfs) a menos que detecta que se está ejecutando un entorno de Hyper-V.  Cuando se usa un sistema de virtualización diferente (es decir, Virtualbox, KVM, etc.) para preparar su imagen de Linux, tendrá que volver a crear la initrd para asegurarse de que al menos el `hv_vmbus` y `hv_storvsc` módulos de núcleo están disponibles en el disco de RAM inicial.  Esto es un problema conocido al menos en los sistemas en función de la distribución de sombrero rojo en sentido ascendente.

El mecanismo para regenerar la imagen initrd o initramfs puede variar según la distribución. Consulte la documentación de la distribución o soporte técnico para el procedimiento correcto.  Aquí es un ejemplo de cómo volver a generar la initrd utilizando la `mkinitrd` utilidad:

Primero, haga una copia la imagen initrd existente:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

A continuación, volver a crear la initrd con la `hv_vmbus` y `hv_storvsc` módulos de núcleo:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Cambiar el tamaño de VHD ###

Imágenes de disco duro virtual de Azure deben tener un tamaño virtual alineado a 1MB.  Normalmente, VHD creados con Hyper-V ya debe alineará correctamente.  Si el disco duro virtual no está alineado correctamente puede recibir un mensaje de error similar al siguiente cuando intenta crear una *imagen* desde el disco duro virtual:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para solucionar este problema puede cambiar el tamaño de la máquina virtual mediante la consola del Administrador de Hyper-V o el cmdlet de Powershell de [Disco duro virtual de tamaño](http://technet.microsoft.com/library/hh848535.aspx) .  Si no se están ejecutando en un entorno de Windows se recomienda usar img qemu convertir (si es necesario) y cambiar el tamaño del disco duro virtual.

> [AZURE.NOTE] Hay un error conocido en versiones de img qemu > = 2.2.1 que da como resultado un disco duro virtual con formato incorrecto. El problema se ha corregido en QEMU 2.6. Se recomienda usar qemu-img 2.2.0 o inferior, o actualizar a 2.6 o superior. Referencia: https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Cambiar el tamaño del disco duro virtual directamente con herramientas como `qemu-img` o `vbox-manage` puede traducirse en un disco duro virtual no se pueda iniciar.  Así que es aconsejable convertir primero el disco duro virtual a una imagen de disco sin formato.  Si ya se ha creado la imagen de VM como imagen de disco sin formato (el valor predeterminado para algunas hipervisores como KVM), puede omitir este paso:

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calcular el tamaño necesario de la imagen de disco para asegurarse de que el tamaño virtual está alineado con 1MB.  La siguiente secuencia de comandos de shell de fiesta puede ayudarle con esto.  La secuencia de comandos usa "`qemu-img info`" para determinar el tamaño de la imagen de disco virtual y, a continuación, calcula el tamaño a la siguiente 1MB:

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Cambiar el tamaño del disco sin formato utilizando $rounded_size según esté definido en la secuencia de comandos anterior:

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. Ahora, convierta el disco sin formato a un disco duro virtual de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Requisitos de Kernel Linux ##

Los controladores de servicios de integración de Linux (vertical) para Hyper-V y Azure se ha contribuido directamente a la precede kernel de Linux. Muchas distribuciones que incluyen una versión reciente de kernel Linux (es decir, 3.x) se ya dispongo de estos controladores disponibles, o en caso contrario, proporcionar utilizados versiones de estos controladores con su kernel.  Estos controladores se actualizan constantemente en el núcleo precede con nuevas características y, por lo que cuando sea posible, se recomienda ejecutar un [respaldo de distribución](virtual-machines-linux-endorsed-distros.md) que se incluyen estas correcciones y actualizaciones.

Si está ejecutando una variante de Red sombrero Enterprise Linux versiones **6.0 6.3**, deberá instalar a los controladores más recientes de vertical para Hyper-V. Los controladores pueden encontrarse [en esta ubicación](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partir de RHEL **6.4 +** (y derivados) ya están incluidos con el núcleo de los controladores de vertical y así ningún paquete de instalación adicionales es necesarias para ejecutar los sistemas de Azure.

Si se requiere un núcleo personalizado, se recomienda usar una versión más reciente de kernel (es decir, **3,8 +**). Para las distribuciones o proveedores que mantienen su propio núcleo, será algún esfuerzo necesarios atrás periódicamente los controladores vertical del núcleo precede a su núcleo personalizado.  Incluso si ya está ejecutando una versión de kernel relativamente recientes, se recomienda realizar un seguimiento de todas las revisiones en los controladores de vertical y de atrás precedentes aquellos según sea necesario. La ubicación de los archivos de origen del controlador vertical está disponible en el archivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) en el árbol de origen de kernel Linux:

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Como mínimo, la ausencia de las siguientes revisiones se sabe que pueden provocar errores en Azure y así que estos deben incluirse en el núcleo. Esta lista no es exhaustiva o completa de todas las distribuciones:

- [ata_piix: aplazar discos a los controladores de Hyper-V de forma predeterminada](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: paquetes en tránsito en la ruta de acceso de restablecimiento de la cuenta](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: evitar el uso de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: deshabilitar escribir mismo para RAID y controladores de adaptador host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: corrección de referencia de puntero NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: errores de búfer de anillo pueden producir inmovilizar i/OS](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs: protección contra doble ejecución de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>El agente de Linux Azure ##

El [Agente de Azure Linux](virtual-machines-linux-agent-user-guide.md) (waagent) es necesario para aprovisionar correctamente una máquina virtual de Linux en Azure. Puede obtener la última versión, problemas de archivo o envíe solicitudes de extracción en la [repo GitHub agente de Linux](https://github.com/Azure/WALinuxAgent).

- El agente de Linux se publica en la licencia de Apache 2.0. Muchas distribuciones ya proporcionan RPM o deuda paquetes para el agente, y por tanto en algunos casos esto puede instalarse y actualizado con poco esfuerzo.

- El agente de Linux Azure requiere Python v2.6 +.

- El agente también requiere el módulo de python pyasn1. La mayoría de las distribuciones proporcionan esta como un paquete independiente que puede instalarse.

- En algunos casos es posible que no sea compatible con NetworkManager el agente de Azure Linux. Muchos de los paquetes RPM/deuda proporcionados por distribuciones configurar NetworkManager como un conflicto para el paquete de waagent y, por tanto, desinstalarán NetworkManager al instalar el paquete de agente Linux.


## <a name="general-linux-system-requirements"></a>Requisitos del sistema de Linux general ##

- Modifique la línea de inicio del núcleo en GRUB o GRUB2 para incluir los siguientes parámetros. Esto también se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración:

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    Esto también se asegurará de todos los mensajes de consola se envían al primer puerto serie, que puede ayudar a Azure compatibilidad con problemas de depuración.

    Además, se recomienda *Quitar* los parámetros siguientes si existen:

        rhgb quiet crashkernel=auto

    Inicio gráficos y silencioso no son útiles en un entorno de nube necesitamos todos los registros que se envíen al puerto serie.

    La `crashkernel` opción puede ser izquierda configurado si lo desea, pero tenga en cuenta que este parámetro reducir la cantidad de memoria disponible en la máquina virtual de 128MB o más, que pueden ser problemáticas en los tamaños VM más pequeños.

- Instalar al agente de Linux Azure

    El agente de Linux Azure es necesario para el aprovisionamiento de una imagen de Linux en Azure.  Muchas distribuciones de proporcionan al agente como un paquete RPM o deuda (el paquete normalmente se denomina 'WALinuxAgent' o 'walinuxagent').  El agente también se puede instalar manualmente siguiendo los pasos de la [Guía de agente de Linux](virtual-machines-linux-agent-user-guide.md).

- Asegúrese de que el servidor SSH está instalado y configurado para iniciarse durante el inicio.  Normalmente, esto es el valor predeterminado.

- No crear espacio de intercambio en el disco de sistema operativo

    El agente de Linux Azure puede configurar automáticamente el espacio de intercambio con el disco de recursos local que está conectado a la máquina virtual después de aprovisionamiento en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* y puede ser vaciado cuando la máquina virtual quedará con aprovisionamiento anulada. Después de instalar el agente de Azure Linux (vea el paso anterior), modifique los parámetros siguientes en /etc/waagent.conf en consecuencia:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Como paso final, ejecute los siguientes comandos para activar la máquina virtual:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] En Virtualbox es posible que vea el siguiente error después de ejecutar ' waagent-forzar - deprovision': `[Errno 5] Input/output error`. Este mensaje de error no es importante y se puede omitir.

- A continuación, debe apagar la máquina virtual y cargar el disco duro virtual en Azure.
