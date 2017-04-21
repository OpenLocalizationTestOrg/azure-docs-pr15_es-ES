<properties 
    pageTitle="Configurar LVM en una máquina virtual ejecuta Linux | Microsoft Azure" 
    description="Obtenga información sobre cómo configurar LVM en Linux en Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar LVM en una máquina virtual de Linux en Azure

Este documento describe cómo configurar el Administrador de volumen lógico (LVM) en la máquina virtual Azure. Aunque es posible configurar LVM en cualquier disco conectado a la máquina virtual, de forma predeterminada mayoría imágenes de nube no tendrán LVM configurado en el disco de sistema operativo. Esto es para evitar problemas con los grupos de volumen duplicados si el sistema operativo disco es nunca conectado a otra máquina virtual de la misma distribución y tipo, es decir, durante un escenario de recuperación. Por lo tanto, se recomienda que utilicen LVM en los discos de datos.


## <a name="linear-vs-striped-logical-volumes"></a>Lineal frente seccionados volúmenes lógicos

LVM puede usarse para combinar un número de discos físicos en un mismo volumen de almacenamiento. De forma predeterminada LVM suele crear volúmenes lógicos lineales, lo que significa que el almacenamiento físico es concatenado. En este caso las operaciones de lectura y escritura normalmente solo se enviará a un único disco. Por el contrario, podemos crear también volúmenes lógicos seccionados donde se distribuye de lectura y escritura en varios discos contenidos en el grupo de volumen (es decir, similar a RAID0). Por motivos de rendimiento, es probable que desee bandas su volúmenes lógicos para que la lectura y escritura utiliza los discos de datos adjuntos.

Este documento describe cómo combinar varios discos de datos en un grupo de volumen único y, a continuación, cree un volumen lógico con bandas. Algo en general, los pasos siguientes para trabajar con la mayoría de las distribuciones. En la mayoría de los casos las utilidades y flujos de trabajo para administrar LVM en Azure no son básicamente diferentes a otros entornos. Como de costumbre, consulte también con su proveedor de Linux para la documentación y los procedimientos recomendados para usar LVM con la distribución en particular.


## <a name="attaching-data-disks"></a>Conectar discos de datos
Uno desea empezar con dos o más discos de datos vacía al usar LVM. En función de sus necesidades IO, puede adjuntar discos que se almacenan en el almacenamiento de información estándar, con hasta 500 IO/ps por disco o nuestro almacenamiento Premium con hasta 5.000 IO/ps por disco. En este artículo no entra en detalles sobre cómo aprovisionar y adjuntar discos de datos a una máquina virtual Linux. Consulte la Microsoft Azure artículo [adjuntar un disco](virtual-machines-linux-add-disk.md) para obtener instrucciones detalladas sobre cómo adjuntar un disco de datos vacía a una máquina virtual de Linux en Azure.

## <a name="install-the-lvm-utilities"></a>Instale las herramientas LVM

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS & Linux de Oracle**

        # sudo yum install lvm2

- **SLES 12 y openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    En SLES11 también debe editar /etc/sysconfig/lvm y establecer `LVM_ACTIVATED_ON_DISCOVERED` "Habilitar":

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Configurar LVM
En esta guía se asume que ha adjuntado tres discos de datos que se va a denominar `/dev/sdc`, `/dev/sdd` y `/dev/sde`. Tenga en cuenta que estas no siempre sean los mismos nombres de ruta de acceso en la máquina virtual. Puede ejecutar '`sudo fdisk -l`' o comando similar a la lista de los discos disponibles.

1. Preparar la volúmenes físicos:

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Crear un grupo de volumen. En este ejemplo se llama al volumen grupo "datos-vg01":

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Crear los volúmenes lógicos. El comando a continuación se creará un único volumen lógico denominado "datos-lv01" para el grupo de volumen completo que abarque, pero tenga en cuenta que también es posible crear múltiples volúmenes lógicos en el grupo de volumen.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Formatear el volumen lógico

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Con el uso de SLES11 "-t ext3" en lugar de ext4. SLES11 solo es compatible con acceso de solo lectura a File Systems ext4.


## <a name="add-the-new-file-system-to-etcfstab"></a>Agregar el nuevo sistema de archivos a/etc/fstab

**Precaución:** Incorrectamente editando el archivo/etc/fstab podría provocar un reinicio del sistema. Si no está seguro, consulte la documentación de la distribución para obtener información sobre cómo editar correctamente este archivo. También se recomienda que se crea una copia de seguridad del archivo/etc/fstab antes de modificar.

1. Crear el punto de montaje deseado para el nuevo sistema de archivos, por ejemplo:

        # sudo mkdir /data


2. Busque la ruta de acceso de volumen lógico

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Abra/etc/fstab en un editor de texto y agregue una entrada para el nuevo sistema de archivos, por ejemplo:

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    A continuación, guarde y cierre/etc/fstab.


4. Comprobar que la entrada de fstab/etcetera/es correcta:

        # sudo mount -a

    Si este comando da como resultado un mensaje de error, compruebe la sintaxis en el archivo/etcetera/fstab.

    Vuelva a ejecutar la `mount` comando para asegurarse de que el sistema de archivos es montaje:

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Opcional) Parámetros de inicio a prueba de errores en/etc/fstab

    Muchas distribuciones de incluyan la `nobootwait` o `nofail` montaje parámetros que se pueden agregar al archivo/etcetera/fstab. Estos parámetros permiten de errores cuando un sistema de archivos determinado de montaje y permitir que el sistema Linux continuar iniciar incluso si no puede montaje correctamente el sistema de archivos RAID. Consulte la documentación de la distribución para obtener más información acerca de estos parámetros.

    Ejemplo (Ubuntu):

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
