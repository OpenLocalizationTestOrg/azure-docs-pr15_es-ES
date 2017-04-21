<properties
    pageTitle="Usar instantáneas incrementales para la copia de seguridad y recuperación de máquinas virtuales de Windows Azure | Microsoft Azure"
    description="Crear una solución personalizada para la copia de seguridad y recuperación de los discos de Azure máquina virtual mediante instantáneas incrementales."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Copia de seguridad discos de Azure máquina virtual con instantáneas incrementales

## <a name="overview"></a>Información general

Almacenamiento de Azure proporciona la capacidad de tomar instantáneas de BLOB. Instantáneas capturan el estado de blob en ese momento. En este artículo, describiremos un escenario de cómo puede mantener copias de seguridad de discos de máquinas virtuales mediante instantáneas. Puede usar esta metodología cuando elige no usar el servicio de recuperación y copia de seguridad de Azure y desea crear una estrategia de copia de seguridad personalizada para los discos de máquina virtual.

Discos de Azure máquinas virtuales se almacenan como blobs de página en el almacenamiento de Azure. Dado que estamos hablando de estrategia de copia de seguridad para discos de máquinas virtuales de este artículo, se hará referencia a instantáneas en el contexto de blobs de página. Para obtener más información acerca de las instantáneas, consulte [crear una instantánea de un Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>¿Qué es una instantánea?

Una instantánea de blob es una versión de solo lectura de un objeto binario que se genera en un punto en el tiempo. Una vez que se ha creado una instantánea, se puede se leer, copiado, o eliminado, pero no se ha modificado. Instantáneas proporcionan una manera de realizar una copia de un blob tal y como aparece en un momento determinado. Hasta el resto versión 2015-04-05 tenía la capacidad de copiar instantáneas completas. Con el resto de versión 2015-07-08 y versiones posteriores, también puede copiar las instantáneas incrementales.

## <a name="full-snapshot-copy"></a>Copia instantánea completa

Instantáneas se pueden copiar a otra cuenta de almacenamiento como un blob conservar copias de seguridad de la base blob. También puede copiar una instantánea sobre sus blobs base, que es similar a restaurar el blob con una versión anterior. Cuando una instantánea se copia desde una cuenta de almacenamiento a otro, ocupará el mismo espacio de blobs de página base. Por lo tanto, instantáneas todos copias de una cuenta de almacenamiento a otro serán lentas y también consume gran cantidad de espacio en la cuenta de almacenamiento de destino.

>[AZURE.NOTE] Si copia la base blob a otro destino, no se copian las instantáneas del blob con él. Del mismo modo, si sobrescribir un blob base con una copia, instantáneas asociadas con el objeto blob base no se ven afectadas y permanecen intactas en nombre de la base de blobs de Windows.

### <a name="back-up-disks-using-snapshots"></a>Copia de seguridad discos mediante instantáneas

Como una estrategia de copia de seguridad para los discos de máquina virtual, puede tomar instantáneas periódicas de blobs de disco o una página y copiarlos a otra cuenta de almacenamiento con herramientas como operación [Copiar Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o [AzCopy](storage-use-azcopy.md). Puede copiar una instantánea en un blob de página de destino con un nombre diferente. El blob de página de destino resultante es un blob de página puede escribir y no una instantánea. Más adelante en este artículo describiremos los pasos para realizar copias de seguridad de discos de la máquina virtual mediante instantáneas.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos mediante instantáneas

Cuando es el momento de restaurar el disco a una versión anterior de estable capturada en una de las instantáneas de copia de seguridad, puede copiar una instantánea sobre el blob de página base. Tras promover la instantánea a la página base blob el permanece instantánea, pero su origen se sobrescribe con una copia que puede leer y escribir. Más adelante en este artículo describiremos los pasos para restaurar una versión anterior del disco de la instantánea.

### <a name="implementing-full-snapshot-copy"></a>Implementación de copia instantánea completa

Puede implementar una copia instantánea completa haciendo lo siguiente

-   En primer lugar, tomar una instantánea de la base blob mediante la operación de [Blobs de instantánea](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   A continuación, copie la instantánea a una cuenta de almacenamiento de destino utilizando [Copiar Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Repita este proceso para conservar copias de seguridad de la base blob.

## <a name="incremental-snapshot-copy"></a>Copia instantánea incremental

La nueva característica de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API proporciona una manera mucho mejor para realizar copias de seguridad de las instantáneas de los discos o blobs de página. La API devuelve la lista de cambios entre el blob base y las instantáneas. Esto reduce la cantidad de espacio de almacenamiento en la cuenta de copia de seguridad. La API admite blobs de página en el almacenamiento de Premium como almacenamiento estándar. Uso de esta API que puede crear soluciones de copia de seguridad más rápidas y eficaces para máquinas virtuales de Azure. Estará disponible con la versión del resto de 2015-07-08 o superior.

Copia instantánea incremental permite copiar desde una cuenta de almacenamiento a otro de la diferencia entre,

-   Blob base y su instantánea o
-   Dos instantáneas de la base blob

Siempre que se cumplan las condiciones siguientes,

- El blob se creó en el 1 de enero de 2016 o posterior.
- El blob no se sobrescribe con [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) o [Copia Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) entre dos instantáneas.


**Nota**: esta característica está disponible para Premium y estándar Blobs de página de Azure.

Cuando tenga una estrategia de copia de seguridad personalizada que usa instantáneas, copiar las instantáneas de una cuenta de almacenamiento a otro puede ser muy lento y consume una gran cantidad de espacio de almacenamiento. En lugar de copiar la instantánea completa a una cuenta de almacenamiento de copia de seguridad, puede escribir la diferencia entre instantáneas consecutivas a un blob de página de copia de seguridad. De este modo, se reduce significativamente tiempo para copiar y espacio para almacenar las copias de seguridad.

### <a name="implementing-incremental-snapshot-copy"></a>Implementación de copia de instantánea Incremental

Puede implementar instantáneas incrementales haciendo lo siguiente

-   Tomar una instantánea de la base blob con [Blob instantánea](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copiar la instantánea en la cuenta de almacenamiento de copia de seguridad de destino con [Blob de copia](https://msdn.microsoft.com/library/azure/dd894037.aspx). Se trata de blobs de página de copia de seguridad. Tomar una instantánea de este blob de página de copia de seguridad y almacenar en la cuenta de copia de seguridad.
-   Tomar otra instantánea de la base blob con Blob instantánea.
-   Obtener la diferencia entre la primeros y segunda instantáneas de base blob mediante [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Use el nuevo parámetro **prevsnapshot** para especificar el valor de fecha y hora de la instantánea que desea obtener la diferencia con. Cuando este parámetro está presente, la respuesta del resto incluirá sólo las páginas que se han cambiado entre instantánea de destino y anterior incluidas páginas borrar.
-   Use [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para aplicar estos cambios a blobs de página de copia de seguridad.
-   Por último, tomar una instantánea de blobs de copia de seguridad de página y guardarlo en la cuenta de almacenamiento de copia de seguridad.

En la siguiente sección, describiremos con más detalle cómo puede mantener copias de seguridad de discos mediante copia instantánea Incremental

## <a name="scenario"></a>Escenario

En esta sección describiremos un escenario que implica una estrategia de copia de seguridad personalizada mediante instantáneas de discos de máquina virtual.

Considere la posibilidad de una VM de Azure serie DS con un disco de P30 de almacenamiento premium conectado. El disco P30 denominado *mypremiumdisk* se almacena en una cuenta de almacenamiento premium denominada *mypremiumaccount*. Una cuenta de almacenamiento estándar denominada *mybackupstdaccount* se usará para almacenar la copia de seguridad de *mypremiumdisk*. Vamos a tener una instantánea de *mypremiumdisk* cada 12 horas.

Para obtener información sobre cómo crear discos y cuenta de almacenamiento, consulte [acerca de Azure cuentas de almacenamiento](storage-create-storage-account.md).

Para obtener información sobre la copia de seguridad de máquinas virtuales de Azure, consulte [Planear Azure VM copias de seguridad](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Pasos para mantener las copias de seguridad de un disco mediante instantáneas incrementales

Los pasos descritos a continuación tome instantáneas de *mypremiumdisk* y mantener las copias de seguridad en *mybackupstdaccount*. La copia de seguridad será un blob de página estándar denominado *mybackupstdpageblob*. El blob de copia de seguridad página reflejará siempre el mismo estado que la última instantánea de *mypremiumdisk*.

1.  Primero, cree el blob de página de copia de seguridad para el disco de almacenamiento premium. Para ello, tomar una instantánea de *mypremiumdisk* llamado *mypremiumdisk_ss1*.
2.  Copiar esta instantánea en mybackupstdaccount como un blob de página denominado *mybackupstdpageblob*.
3.  Tomar una instantánea de *mybackupstdpageblob* llamado *mybackupstdpageblob_ss1*, con [Blob instantánea](https://msdn.microsoft.com/library/azure/ee691971.aspx) y guardarlo en *mybackupstdaccount*.
4.  Durante la ventana de copia de seguridad, crear otra instantánea de *mypremiumdisk*, diga *mypremiumdisk_ss2*y almacenar en *mypremiumaccount*.
5.  Obtener los cambios incrementales entre las dos instantáneas, *mypremiumdisk_ss2* y *mypremiumdisk_ss1*, con [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) en *mypremiumdisk_ss2* del parámetro **prevsnapshot** establecido en la marca de tiempo de *mypremiumdisk_ss1*. Escribir estos cambios incrementales de blobs de página de copia de seguridad *mybackupstdpageblob* en *mybackupstdaccount*. Si se han eliminado rangos en los cambios incrementales, debe borrar de blobs de página de copia de seguridad. Utilice [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para escribir los cambios incrementales en el blob de la página de copia de seguridad.
6.  Tomar una instantánea de la página de copia de seguridad blob *mybackupstdpageblob*, denominado *mybackupstdpageblob_ss2*. Eliminar la instantánea anterior *mypremiumdisk_ss1* de cuenta de almacenamiento premium.
7.  Repita los pasos del 4 al 6 todas las ventanas de copia de seguridad. De esta forma, puede mantener copias de seguridad de *mypremiumdisk* en una cuenta de almacenamiento estándar.

![Haga una copia de seguridad de disco mediante instantáneas incrementales](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Pasos para restaurar un disco de instantáneas

Los pasos descritos a continuación restaurarán disco premium, *mypremiumdisk* a una instantánea anterior de la cuenta de almacenamiento de copia de seguridad *mybackupstdaccount*.

1.  Identificar el punto en el tiempo que desea restaurar el disco de premium. Supongamos que es de instantánea *mybackupstdpageblob_ss2*, que se almacena en la cuenta de almacenamiento de copia de seguridad *mybackupstdaccount*.
2.  En mybackupstdaccount, promover la instantánea *mybackupstdpageblob_ss2* como el nuevo de blobs de página copia de seguridad de base *mybackupstdpageblobrestored*.
3.  Tomar una instantánea de este blob de página de copia de seguridad restaurados, denominado *mybackupstdpageblobrestored_ss1*.
4.  Copiar de blobs de página restaurada *mybackupstdpageblobrestored* de *mybackupstdaccount* a *mypremiumaccount* como el disco de premium nueva *mypremiumdiskrestored*.
5.  Tomar una instantánea de *mypremiumdiskrestored*, denominado *mypremiumdiskrestored_ss1* para realizar copias de seguridad incrementales futuras.
6.  Punto DS serie VM a la restaurada *mypremiumdiskrestored* en disco y separar el antiguo *mypremiumdisk* de la máquina virtual.
7.  Comenzar el proceso de copia de seguridad que se describe en la sección anterior para el disco restaurada *mypremiumdiskrestored*, usando la *mybackupstdpageblobrestored* como blobs de página de copia de seguridad.

![Restaurar el disco de instantáneas](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo crear instantáneas de un blob y planear la infraestructura de copia de seguridad de máquina virtual con los siguientes vínculos.

- [Crear una instantánea de un Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planear la infraestructura de copia de seguridad de VM](../backup/backup-azure-vms-introduction.md)
