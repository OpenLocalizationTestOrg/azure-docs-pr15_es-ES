<properties
    pageTitle="Solucionar problemas de la eliminación de cuentas de almacenamiento de Azure, contenedores o VHD en una implementación clásica | Microsoft Azure"
    description="Solucionar problemas de la eliminación de cuentas de almacenamiento de Azure, contenedores o VHD en una implementación clásica"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Solucionar problemas de la eliminación de cuentas de almacenamiento de Azure, contenedores o VHD en una implementación clásica

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Puede recibir errores al intentar eliminar la cuenta de almacenamiento de Azure, el contenedor o el disco duro virtual en el [portal de Azure](https://portal.azure.com/) o en el [portal de clásico de Azure](https://manage.windowsazure.com/). Los problemas que pueden estar causados por las circunstancias siguientes:

-   Al eliminar una máquina virtual, el disco y el disco duro virtual no se eliminan automáticamente. Que podría ser la causa de error en la eliminación de la cuenta de almacenamiento. No eliminamos el disco de modo que puede usar el disco para montaje VM otra.

-   Sigue siendo una concesión en un disco o blob que está asociada con el disco.

Si el problema de Azure no se trata en este artículo, visite los foros de Azure en [MSDN y el desbordamiento de pila](https://azure.microsoft.com/support/forums/). Puede publicar el problema en estos foros o para @AzureSupport en Twitter. Además, puede abrir una solicitud de soporte técnico de Azure seleccionando **obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Síntomas

En la sección siguiente se enumera los errores comunes que puede recibir cuando intenta eliminar las cuentas de almacenamiento de Azure, contenedores o VHD.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Escenario 1: No se puede eliminar una cuenta de almacenamiento

Cuando vaya a la cuenta de almacenamiento en el [portal de Azure](https://portal.azure.com/) o [portal clásica Azure](https://manage.windowsazure.com/) y seleccione **Eliminar**, es posible que vea el siguiente mensaje de error:

*Cuenta de almacenamiento StorageAccountName contiene imágenes de máquina virtual. Asegúrese de que se quitan estas imágenes de máquina virtual antes de eliminar esta cuenta de almacenamiento.*

También es posible que vea este error:

**En Azure el portal**:

*Error al eliminar la cuenta de almacenamiento < vm almacenamiento-nombre de cuenta >. No se puede eliminar la cuenta de almacenamiento < vm almacenamiento-nombre de cuenta >: ' cuenta de almacenamiento < vm almacenamiento-nombre de cuenta > tiene algunas imágenes activa o discos. Asegúrese de haber quitado estas imágenes o discos antes de eliminar esta cuenta de almacenamiento.'.*

**En el Azure clásica portal**:

*Cuenta de almacenamiento < vm almacenamiento-nombre de cuenta > tiene algunas imágenes activa o discos, por ejemplo, xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Asegúrese de que estas imágenes o discos se quitan antes de eliminar esta cuenta de almacenamiento.*

O

**En Azure el portal**:

Cuenta de almacenamiento de *< vm almacenamiento-nombre de cuenta > tiene 1 contenedores que tienen una imagen activa o artefactos de disco. Asegúrese de que se quitan los artefactos del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento*.

**En el Azure clásica portal**:

Cuenta de almacenamiento de error al enviar *< vm almacenamiento-nombre de cuenta > tiene 1 contenedores que tienen una imagen activa o artefactos de disco. Asegúrese de que se quitan los artefactos del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento. Cuando intenta eliminar una cuenta de almacenamiento y hay discos sigue activos asociados, verá un mensaje que indica que hay discos activos que deben eliminarse*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Escenario 2: No se puede eliminar un contenedor

Al intentar eliminar el contenedor de almacenamiento, es posible que vea el siguiente error:

No se pudo eliminar el contenedor de almacenamiento * <container name>. Error: ' actualmente no hay una concesión en el contenedor y se ha especificado ningún identificador de concesión en la solicitud*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Escenario 3: No se puede eliminar un disco duro virtual

Después de eliminar una máquina virtual y, a continuación, intente eliminar el BLOB para los VHD asociados, podría aparecer el siguiente mensaje:

*Error al eliminar blob ' ruta de acceso o XXXXXX-XXXXXX-os-1447379084699.vhd'. Error: ' actualmente no hay una concesión en el objeto binario y se ha especificado ningún identificador de concesión en la convocatoria.*

## <a name="solution"></a>Solución
Para resolver los problemas más comunes, pruebe el método siguiente:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Paso 1: Eliminar los discos de sistema operativo que impiden la eliminación de la cuenta de almacenamiento, un contenedor o un disco duro virtual

1. Cambiar el [portal clásica Azure](https://manage.windowsazure.com/).
2. Seleccione **máquina VIRTUAL** > **discos**.

    ![Imagen de discos en máquinas virtuales de Azure portal clásico.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Busque los discos que están asociados a la cuenta de almacenamiento, un contenedor o un disco duro virtual que desea eliminar. Al comprobar la ubicación del disco, encontrará la cuenta de almacenamiento asociado, el contenedor o el disco duro virtual.

    ![Imagen que muestra la información de ubicación de los discos en Azure portal clásica](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Confirme que no hay VM aparece en el campo **adjunto a** los discos y, a continuación, elimine los discos.

    > [AZURE.NOTE] Si un disco está conectado a una máquina virtual, no podrá eliminarlo. Discos se separan asincrónica desde una máquina virtual eliminada. Puede tardar unos minutos después de elimina la máquina virtual para que este campo eliminar.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Paso 2: Elimine las imágenes de máquina virtual que impiden la eliminación de la cuenta de almacenamiento o el contenedor

1. Cambiar el [portal clásica Azure](https://manage.windowsazure.com/).
2. Seleccione **máquina VIRTUAL** > de**imágenes**y, a continuación, elimine las imágenes que están asociadas a la cuenta de almacenamiento, un contenedor o un disco duro virtual.

    Después de eso, intente eliminar la cuenta de almacenamiento, un contenedor o un disco duro virtual de nuevo.

> [AZURE.WARNING] No olvide hacer copia de seguridad que desee guardar antes de eliminar la cuenta. No es posible restaurar una cuenta de almacenamiento eliminado o recuperar cualquier contenido que contenía antes de la eliminación. Esto también se aplica a los recursos en la cuenta: una vez que elimine el disco duro virtual, blobs, tablas, cola o archivo, se elimina permanentemente. Asegúrese de que el recurso no está en uso.

## <a name="about-the-stopped-deallocated-status"></a>Sobre el estado de su interrupción (asignación)

Máquinas virtuales que se crearon en el modelo de implementación clásica y que se han conservado tendrán el estado **detenido (desasignado)** en el [portal de Azure](https://portal.azure.com/) o [Azure portal clásico](https://manage.windowsazure.com/).

**Azure portal clásica**:

![Detener estado (Deallocated) para máquinas virtuales en el portal de Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Portal de azure**:

![Ha dejado de estado (manera) para máquinas virtuales en Azure portal clásico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Un estado "Detenido (desasignado)" libera los recursos del equipo, como la CPU, la memoria y la red. Sin embargo, los discos, aún se conservan para que puede volver a crear rápidamente la máquina virtual si es necesario. Estos discos se crean en la parte superior VHD, que se copia en el almacenamiento de Azure. La cuenta de almacenamiento tiene estos VHD y los discos tener arrendamientos en esas VHD.

## <a name="next-steps"></a>Pasos siguientes

- [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
- [Cómo dividir la concesión bloqueada de almacenamiento de blobs de Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
