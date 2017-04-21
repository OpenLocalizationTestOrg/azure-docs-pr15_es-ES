<properties
    pageTitle="Agregar un disco a Linux VM | Microsoft Azure"
    description="Más información sobre cómo agregar un disco persistente a su VM Linux"
    keywords="máquina virtual Linux, agregar recursos disco"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Agregar un disco a una VM Linux

Este artículo le muestra cómo adjuntar un disco persistente a su máquina virtual para que puede conservar los datos - incluso si su máquina virtual se reaprovisionarla debido a mantenimiento o su tamaño. Para agregar un disco, debe [CLI Azure](../xplat-cli-install.md) configurado en modo de administrador de recursos (`azure config mode arm`).  

## <a name="quick-commands"></a>Comandos rápidos

En los siguientes ejemplos de comando, reemplace los valores entre &lt; y &gt; con los valores de su entorno.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Adjuntar un disco

Asociar un disco nuevo es rápido. Tipo de `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para crear y vincular un nuevo disco GB para su máquina virtual. Si no identifica explícitamente una cuenta de almacenamiento, cualquier disco que crea se coloca en la misma cuenta de almacenamiento donde reside el disco de sistema operativo.  Debe tener un aspecto similar al siguiente:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Salida

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectarse a la VM Linux montaje del nuevo disco

> [AZURE.NOTE] En este tema se conecta a una máquina virtual con nombres de usuario y contraseñas. Para usar pares de claves públicos y privados para comunicarse con su máquina virtual, vea [cómo utilizar SSH con Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md). Puede modificar la conectividad **SSH** de VM creada con la `azure vm quick-create` comando utilizando la `azure vm reset-access` comando para restablecer el acceso **SSH** completamente, agregar o quitar usuarios o agregar archivos de claves públicas para proteger el acceso.

Tenga a SSH en su VM Azure a partición, dar formato y montaje el disco nuevo para que su VM Linux puede usarla. Si no está familiarizado con conexión con **ssh**, el comando adopta la forma `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`y el siguiente aspecto:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Salida

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

Ahora que está conectado a su máquina virtual, ya está listo para adjuntar un disco.  En primer lugar, busque el disco con `dmesg | grep SCSI` (el método que utilice para detectar el disco nuevo puede variar). En este caso, es algo parecido a:

```bash
dmesg | grep SCSI
```

Salida

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

y en el caso de este tema, el `sdc` disco es la que queremos. Ahora las particiones en el disco con `sudo fdisk /dev/sdc` --suponiendo que en su caso, el disco estaba `sdc`, que sea un disco principal en la partición 1 y acepte los demás valores predeterminados.

```bash
sudo fdisk /dev/sdc
```

Salida

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Crear la partición escribiendo `p` en el símbolo del sistema:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Y escribir un sistema de archivos en la partición mediante el comando **mkfs** , que especifica el tipo de sistema de archivos y el nombre del dispositivo. En este tema, usaremos `ext4` y `/dev/sdc1` desde arriba:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Salida

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Ahora, cree un directorio para montaje el sistema de archivo mediante `mkdir`:

```bash
sudo mkdir /datadrive
```

Y montaje el directorio utilizando `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

El disco de datos ahora está listo para usar como `/datadrive`.

```bash
ls
```

Salida

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar el equipo debe agregarse al archivo/etcetera/fstab. Además, se recomienda que el UUID (identificador único universal de) se usa en/etcetera/fstab para hacer referencia a la unidad en lugar de con el nombre del dispositivo (como por ejemplo, `/dev/sdc1`). Si el sistema operativo detecta un error de disco durante el inicio, usando el UUID evita la incorrecto disco está en una ubicación concreta. Restante discos de datos podría asignarse los mismos identificadores de dispositivo. Para buscar el UUID de la nueva unidad, use la utilidad **blkid** :

```bash
sudo -i blkid
```

El resultado tiene un aspecto similar al siguiente:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] Incorrectamente editando el **archivo/etc/fstab** podría provocar un reinicio del sistema. Si no está seguro, consulte la documentación de la distribución para obtener información sobre cómo editar correctamente este archivo. También se recomienda que se crea una copia de seguridad del archivo/etc/fstab antes de modificar.

A continuación, abra el **archivo/etc/fstab** en un editor de texto:

```bash
sudo vi /etc/fstab
```

En este ejemplo, se utiliza el valor UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**. Agregue la siguiente línea al final del **archivo/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] Quitar más adelante un disco de datos sin modificar fstab podría provocar la máquina virtual no pueda iniciarse. La mayoría de las distribuciones proporcionan la `nofail` o `nobootwait` fstab opciones. Estas opciones permiten un sistema de arranque incluso si se produce un error en el disco montaje durante el inicio. Consulte la documentación de la distribución para obtener más información acerca de estos parámetros.

>[AZURE.NOTE] La opción de **nofail** garantiza que se inicia la máquina virtual incluso si el sistema de archivos está dañado o el disco no existe en el inicio. Sin esta opción, que puede encontrarse comportamiento como se describe en [No SSH VM Linux debido a errores FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Soporte de recortar/asignación para Linux en Azure
Algunos kernels Linux admiten las operaciones de RECORTE o asignación para descartar bloques sin usar en el disco. Esto es útil principalmente en almacenamiento estándar para informar a Azure que elimina páginas ya no son válido y se puede descartar. Esto puede ahorrar costos si crea archivos grandes y, a continuación, elimínelas.

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

- Recuerde que el nuevo disco no está disponible para la máquina virtual si reinicia a menos que escriba dicha información al archivo [fstab](http://en.wikipedia.org/wiki/Fstab) .
- Para asegurarse de que la VM Linux está configurada correctamente, revise las recomendaciones de [optimizar el rendimiento de la máquina Linux](virtual-machines-linux-optimization.md) .
- Expandir la capacidad de almacenamiento agregando discos adicionales y [configurar RAID](virtual-machines-linux-configure-raid.md) rendimiento adicionales.
