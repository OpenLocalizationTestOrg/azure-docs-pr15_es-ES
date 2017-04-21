<properties
    pageTitle="Optimizar su VM Linux en Azure | Microsoft Azure"
    description="Obtenga información sobre algunas sugerencias de optimización para asegurarse de que ha configurado su Linux VM para obtener un rendimiento óptimo en Azure"
    keywords="máquina virtual de Linux, máquina virtual linux, máquina virtual de ubuntu" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Optimizar su VM Linux en Azure

Crear una máquina virtual (VM) de Linux es fácil hacer desde la línea de comandos o desde el portal. Este tutorial muestra cómo garantizar que haya configurarlo para optimizar su rendimiento en la plataforma de Microsoft Azure. Este tema usa un servidor de Ubuntu máquina virtual, pero también puede crear máquina virtual de Linux con [sus propias imágenes como plantillas](virtual-machines-linux-create-upload-generic.md).  

## <a name="prerequisites"></a>Requisitos previos

En este tema se supone que ya tiene una suscripción de Azure ([suscripción de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)), [instaló la CLI de Azure](../xplat-cli-install.md) y ya ha proporcionado una máquina virtual en su suscripción de Azure. Antes de hacer cualquier cosa con Azure - debe autenticar a su suscripción. Para hacerlo con Azure CLI, solo tiene que escribir `azure login` para iniciar el proceso interactivo. 

## <a name="azure-os-disk"></a>Disco de sistema operativo de Azure

Una vez que se crea una Linux Vm en Azure, tiene dos discos asociados con él. / dev/sda es el disco de sistema operativo, /dev/sdb es el disco temporal.  No utilice el disco de sistema operativo principal (/ desarrollo/empaquetados) cualquier cosa excepto el sistema operativo, tal como está optimizada para el tiempo de inicio rápido de máquina virtual y proporciona un buen rendimiento para las cargas de trabajo. Que desea adjuntar uno o más discos a su máquina virtual obtener persistentes y optimizado de almacenamiento para sus datos. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Agregar discos para destinos de tamaño y rendimiento 

En función del tamaño de la máquina virtual, puede adjuntar hasta 16 discos adicionales en una serie, 32 discos en una serie de D y máquina de 64 discos en una serie de G - cada hasta 1 TB de tamaño. Agregar discos adicionales según sea necesario por el espacio y los requisitos de IOps. Cada disco tiene un destino de rendimiento de 500 IOps para almacenamiento estándar y hasta 5000 IOps por disco para el almacenamiento de Premium.  Para obtener más información acerca de los discos de almacenamiento de Premium, consulte [almacenamiento Premium: almacenamiento de alto rendimiento para máquinas virtuales de Azure](../storage/storage-premium-storage.md)

Para lograr la más IOps en discos de almacenamiento de Premium donde se establecieron su configuración de caché a "Sólo lectura" o "Ninguno", debe deshabilitar "obstáculos" mientras montaje el sistema de archivos en Linux. No es necesario obstáculos porque la escritura en discos de almacenamiento de Premium copia es resistente para esta configuración de caché.

- Si usa **reiserFS**, deshabilitar obstáculos con la opción de montaje "barrera = ninguno" (para habilitar obstáculos, use "barrera = vaciado")
- Si usa **ext3/ext4**, deshabilitar obstáculos con la opción de montaje "barrera = 0" (para habilitar obstáculos, use "barrera = 1")
- Si usa **XFS**, deshabilitar obstáculos con la opción de montaje "nobarrier" (para habilitar obstáculos, use la opción "barrera")

## <a name="storage-account-considerations"></a>Consideraciones de la cuenta de almacenamiento

Cuando se crea la VM Linux en Azure, debe asegurarse de que adjuntar discos de cuentas de almacenamiento que reside en la misma región como la máquina virtual para garantizar cerca y minimizar la latencia de red.  Cada cuenta de almacenamiento estándar tiene un máximo de 20 k IOps y la capacidad de un tamaño de 500 TB.  Esto funciona a 40 discos muy utilizados, incluidos el disco de sistema operativo y crear los discos de datos. Para las cuentas de almacenamiento Premium, no hay ningún límite máximo IOps pero hay un límite de tamaño de 32 TB. 

Al tratar con alta IOps y cargas de trabajo ha elegido almacenamiento estándar para los discos, debe dividir los discos entre varias cuentas de almacenamiento para asegurarse de que no haya elegido el límite de IOps 20.000 para cuentas de almacenamiento estándar. La máquina virtual puede contener una mezcla de discos de en cuentas de almacenamiento diferente y tipos de cuenta de almacenamiento para lograr la configuración óptima. 

## <a name="your-vm-temporary-drive"></a>La unidad VM temporal

De forma predeterminada cuando se crea una máquina virtual, Azure le ofrece un sistema operativo (/ desarrollo/empaquetados) y un temporal disco (/ desarrollo/sdb).  Todos los discos adicionales que agrega mostrarse como /dev/sdc, /dev/sdd, /dev/sde y así sucesivamente. Todos los datos en el disco temporal (/ desarrollo/sdb) no está resistentes y se pueden perder si eventos específicos, como máquina virtual de cambio de tamaño, redistribución, o mantenimiento fuerza el reinicio de la máquina virtual.  El tamaño y el tipo de su disco temporal está relacionado con el tamaño de la memoria virtual seleccionado en el momento de la implementación. En el caso de cualquiera del tamaño premium VM (serie DS, G y DS_V2) la unidad temporal se copia por un SSD local para obtener un rendimiento adicional de hasta 48 k IOps. 

## <a name="linux-swap-file"></a>Archivo de intercambio de Linux

Si su máquina virtual de Azure proviene de una imagen Ubuntu o CoreOS, puede usar CustomData para enviar una configuración de la nube a la nube al inicio. Si ha [cargado una imagen personalizada de Linux](virtual-machines-linux-upload-vhd.md) que usa nube al inicio, configure también las particiones de intercambio con la nube al inicio.

En las imágenes de nube Ubuntu, debe usar nube al inicio para configurar la partición de intercambio. Vea la siguiente página en la wiki de Ubuntu para obtener más detalles: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Imágenes sin soporte de nube al inicio, imágenes VM implementadas de Azure Marketplace tienen un agente de Linux VM integrado con el sistema operativo. Este agente permite la máquina virtual interactuar con diversos servicios de Azure. Suponiendo que ha implementado una imagen estándar de Azure Marketplace, necesarias para hacer lo siguiente para configurar correctamente la configuración de archivo de intercambio de Linux:

Localizar y modificar dos entradas en el archivo **/etc/waagent.conf** . Controlan la existencia de un archivo de intercambio dedicada y el tamaño del archivo de intercambio. Los parámetros que desea para modificar están `ResourceDisk.EnableSwap=N` y`ResourceDisk.SwapSizeMB=0` 

Debe realizar los cambios a la siguiente:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size en MB para satisfacer sus necesidades} 

Una vez haya realizado los cambios, debe reiniciar el waagent o la VM Linux para reflejar los cambios.  Conocer los cambios se han implementado y se ha creado un archivo de intercambio cuando se usa el `free` comando para ver el espacio disponible. El ejemplo siguiente tiene un archivo de intercambio de 512MB creado como resultado de modificar el archivo waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de programación de i/OS para el almacenamiento de Premium

Con la 2.6.18 kernel de Linux, el valor predeterminado i/OS algoritmo de programación cambió de límite a CFQ (completamente algoritmo de cola). Patrones de i/OS acceso aleatorio, hay mínima diferencia de las diferencias de rendimiento entre CFQ y la fecha límite.  Basado en SSD discos donde el patrón de i/OS de disco es principalmente secuencial, volviendo al algoritmo NOOP o fecha límite puede lograr un mejor rendimiento de i/OS.

### <a name="view-the-current-io-scheduler"></a>Ver al programador i/OS actual

Utilice el siguiente comando:  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Verá la siguiente resultado, lo que indica al programador actual.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Cambiar el dispositivo actual (/ desarrollo/empaquetados) de i/OS algoritmo de programación

Use los comandos siguientes:  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Establecer esta opción para/dev/sda por sí sola no es útil. Debe establecerse en todos los discos de datos donde secuenciales i/OS domina el patrón de i/OS.  

Verá el siguiente resultado, que indica que se ha vuelto a ese grub.cfg correctamente y que el programador predeterminado se ha actualizado a NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para la familia de distribución Redhat, solo se necesita el siguiente comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>Utilizar RAID de Software para alcanzar mayores / operaciones

Si las cargas de trabajo requieren más IOps que puede ofrecer un disco, debe usar una configuración de RAID de software de varios discos. Como Azure ya realiza la resistencia del disco en el nivel de tela local, obtendrá el máximo nivel de rendimiento de una configuración de bandas RAID 0.  Debe aprovisionar y crear nuevos discos en el entorno de Azure y adjúntelos a su VM Linux antes de particiones, el formato y las unidades de montaje.  Encontrará más información sobre cómo configurar una configuración RAID de software en sus VM Linux en azure en el documento de **[Configuración de Software RAID en Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Pasos siguientes

Recuerde, con todas las discusiones de optimización, es necesario realizar pruebas antes y después de cada cambio para medir el impacto que tendrá el cambio.  Optimización es un proceso paso a paso que tendrá los resultados diferentes en diferentes equipos en su entorno.  Lo que funciona para una configuración no funcionen para que otras personas.

Algunos vínculos útiles a recursos adicionales: 

- [Almacenamiento de Premium: El almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual](../storage/storage-premium-storage.md)
- [Guía de usuario de Azure agente Linux](virtual-machines-linux-agent-user-guide.md)
- [Optimizar el rendimiento de MySQL en máquinas virtuales de Linux Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurar el Software RAID en Linux](virtual-machines-linux-configure-raid.md)
