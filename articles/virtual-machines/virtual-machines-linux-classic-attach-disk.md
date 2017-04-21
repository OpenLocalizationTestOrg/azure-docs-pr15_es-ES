<properties
    pageTitle="Adjuntar un disco a una VM Linux | Microsoft Azure"
    description="Obtenga información sobre cómo adjuntar un disco de datos a una máquina virtual Azure con Linux e inicialización para que esté listo para su uso."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Cómo adjuntar un disco de datos a una máquina Virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Vea cómo [adjuntar un disco de datos mediante el modelo de implementación de administrador de recursos](virtual-machines-linux-add-disk.md).

Puede adjuntar discos vacíos y discos que contienen datos a sus máquinas virtuales de Azure. Ambos tipos de discos son archivos .vhd que residen en una cuenta de almacenamiento de Azure. Como con la adición de cualquier disco en un equipo Linux, después de conectar el disco necesita inicializar y darle formato para que esté listo para su uso. Este artículo se describen adjuntando discos vacíos y discos ya que contienen datos a sus máquinas virtuales, así como cómo, a continuación, inicializar y dar formato a un disco nuevo.

> [AZURE.NOTE]Es recomendable usar uno o más discos independientes para almacenar los datos de la máquina virtual. Cuando se crea una máquina virtual Azure, tiene un sistema operativo y un disco temporal. **No utilice el disco temporal para almacenar datos persistentes.** Como se indica el nombre, proporciona almacenamiento temporal solo. No ofrece redundancia o copia de seguridad porque no se encuentran en el almacenamiento de Azure.
> El disco temporal es normalmente administrado por el agente de Linux de Azure y montaje automáticamente a **/mnt/resource** (o **/mnt/mnt** en Ubuntu imágenes). Por otro lado, un disco de datos podría denominarse el núcleo Linux algo parecido a `/dev/sdc`, y es necesario crear particiones, dar formato y montaje de este recurso. Consulte la [Guía del usuario de Azure Linux agente] [ Agent] para obtener más detalles.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inicializar un nuevo disco de datos en Linux

1. SSH a su máquina virtual. Para obtener más información, vea [cómo iniciar sesión en una máquina virtual ejecuta Linux][Logon].

2. Siguiente que debe buscar el identificador del dispositivo para iniciar el disco de datos. Hay dos formas de hacerlo:

    un) Grep para dispositivos SCSI en los registros, como en el siguiente comando:

            $sudo grep SCSI /var/log/messages

    Para distribuciones de Ubuntu recientes, tendrá que usar `sudo grep SCSI /var/log/syslog` porque el inicio de sesión `/var/log/messages` pueden estar deshabilitados de forma predeterminada.

    Puede encontrar el identificador del último disco de datos que se ha agregado en los mensajes que se muestran.

    ![Obtener los mensajes de disco](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OR

    b) usar la `lsscsi` comando para averiguar el id. `lsscsi`se puede instalar, ya sea por `yum install lsscsi` (rojo sombrero según las distribuciones) o `apt-get install lsscsi` (Debian según las distribuciones). Puede encontrar el disco que está buscando por su **número de unidad lógica**o _lun_ . Por ejemplo, el _lun_ para los discos ha adjuntado se puede ver fácilmente en `azure vm disk list <virtual-machine>` como:

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Comparar estos datos con el resultado de `lsscsi` para la misma máquina virtual de ejemplo:

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    El último número de la tupla en cada fila es el _lun_. Consulte `man lsscsi` para obtener más información.

3. Cuando se le solicite, escriba el comando siguiente para crear el dispositivo:

        $sudo fdisk /dev/sdc


4. Cuando se le pida, escriba **n** para crear una nueva partición.


    ![Crear el dispositivo](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Cuando se le pida, escriba **p** para que la partición la partición principal. Escriba **1** para que sea la primera partición y, a continuación, escriba ENTRAR para aceptar el valor predeterminado de cilindro. En algunos sistemas, puede mostrar los valores predeterminados de la primera y la últimos sectores, en lugar de cilindro. Puede elegir aceptar estos valores predeterminados.


    ![Crear partición](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Escriba **p** para ver los detalles sobre el disco que se que se divide.


    ![Información de disco de lista](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Escriba **w** para escribir los valores para el disco.


    ![Escribir los cambios de disco](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. Ahora puede crear el sistema de archivos en la nueva partición. Anexar el número de partición al identificador del dispositivo (en el ejemplo siguiente `/dev/sdc1`). En el ejemplo siguiente se crea una partición de ext4 en /dev/sdc1:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Crear el sistema de archivos](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] Sistemas de SuSE Linux Enterprise 11 solo admiten acceso de solo lectura para sistemas de archivos ext4. Para estos sistemas, se recomienda dar formato de nuevo sistema de archivos ext3 en lugar de ext4.


9. Cree un directorio montaje nuevo sistema de archivos, como sigue:

        # sudo mkdir /datadrive


10. Por último puede montaje la unidad, como sigue:

        # sudo mount /dev/sdc1 /datadrive

    El disco de datos ahora está listo para usar como **/datadrive**.
    
    ![Crear el directorio y montaje del disco](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Agregar la nueva unidad a/etc/fstab:

    Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar el equipo debe agregarse al archivo/etcetera/fstab. Además, se recomienda que el UUID (identificador único universal de) se usa en/etc/fstab para hacer referencia a la unidad en lugar de simplemente el nombre del dispositivo (es decir, /dev/sdc1). Usar el UUID evita la incorrecto disco está en una ubicación dada si el sistema operativo detecta un error de disco durante el inicio y los restantes discos de datos, a continuación, se asigna los identificadores de dispositivos. Para buscar el UUID de la nueva unidad, puede usar la utilidad de **blkid** :

        # sudo -i blkid

    El resultado tiene un aspecto similar al siguiente:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] Incorrectamente editando el **archivo/etc/fstab** podría provocar un reinicio del sistema. Si no está seguro, consulte la documentación de la distribución para obtener información sobre cómo editar correctamente este archivo. También se recomienda que se crea una copia de seguridad del archivo/etc/fstab antes de modificar.

    A continuación, abra el **archivo/etc/fstab** en un editor de texto:

        # sudo vi /etc/fstab

    En este ejemplo, se utiliza el valor UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**. Agregue la siguiente línea al final del **archivo/etc/fstab** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    O bien, en sistemas basados en SuSE Linux tendrá que usar un formato ligeramente diferente:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] La `nofail` opción garantiza que se inicia la máquina virtual incluso si el sistema de archivos está dañado o el disco no existe en el inicio. Sin esta opción, puede que encuentre comportamiento como se describe en [No SSH VM Linux debido a errores FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Ahora puede comprobar que el sistema de archivos es montaje correctamente desmontar y, a continuación, montar el sistema de archivos, es decir usando el ejemplo de punto de montaje `/datadrive` creado en los pasos anteriores:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Si el `mount` comando genera un error, compruebe el archivo de fstab/etcetera/sintaxis correcta. Si se crean las unidades de datos adicionales o particiones, introducirlos en/etcetera/fstab también por separado.

    Convertir la unidad modificables con este comando:

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Quitar posteriormente un disco de datos sin modificar fstab podría provocar la máquina virtual no pueda iniciarse. Si se trata de una repetición comunes, la mayoría de las distribuciones proporcionan la `nofail` o `nobootwait` hora de inicio de opciones de fstab que permiten iniciar incluso si se produce un error en el disco montaje en un sistema. Consulte la documentación de la distribución para obtener más información acerca de estos parámetros.

### <a name="trimunmap-support-for-linux-in-azure"></a>Soporte de recortar/asignación para Linux en Azure
Algunos kernels Linux admiten las operaciones de RECORTE o asignación para descartar bloques sin usar en el disco. Estas operaciones son útiles principalmente almacenamiento estándar para informar a Azure que elimina páginas ya no son válido y se puede descartar. Descartar páginas puede guardar costo si crea archivos grandes y, a continuación, elimínelas.

Hay dos formas de habilitar el RECORTE de soporte técnico en su VM Linux. Como siempre, consulte la distribución para el enfoque recomendado:

- Usar la `discard` opción de montaje `/etc/fstab`, por ejemplo:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Como alternativa, puede ejecutar la `fstrim` comando manualmente desde la línea de comandos o agregar a su crontab para ejecutar regularmente:

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Solución de problemas
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## <a name="next-steps"></a>Pasos siguientes
Puede obtener más información sobre el uso de la VM Linux en los siguientes artículos:

- [Cómo iniciar sesión en una máquina virtual con Linux][Logon]

- [Cómo desvincular un disco de un equipo virtual Linux](virtual-machines-linux-classic-detach-disk.md)

- [Usar CLI Azure con el modelo de implementación de clásico](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
