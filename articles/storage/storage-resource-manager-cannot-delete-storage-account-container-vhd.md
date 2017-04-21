<properties
    pageTitle="Solucionar problemas al eliminar cuentas de almacenamiento de Azure, contenedores o VHD en una implementación de administrador de recursos | Microsoft Azure"
    description="Solucionar problemas al eliminar cuentas de almacenamiento de Azure, contenedores o VHD en una implementación de administrador de recursos"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="genli"/>

# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Solucionar problemas al eliminar cuentas de almacenamiento de Azure, contenedores o VHD en una implementación de administrador de recursos

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Puede recibir errores al intentar eliminar una cuenta de almacenamiento de Azure, un contenedor o un disco duro virtual (disco duro virtual) en el [portal de Azure](https://portal.azure.com). Este artículo proporcionan instrucciones de solución de problemas para ayudar a resolver el problema en una implementación de administrador de recursos de Azure.

Si en este artículo no resuelve el problema de Azure, visite los foros de Azure en [MSDN y el desbordamiento de pila](https://azure.microsoft.com/support/forums/). Puede publicar su problema en estos foros o para @AzureSupport en Twitter. Además, puede abrir una solicitud de soporte técnico de Azure seleccionando **obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Síntomas

### <a name="scenario-1"></a>Escenario 1

Cuando intenta eliminar un disco duro virtual en una cuenta de almacenamiento en una implementación de administrador de recursos, recibe el siguiente mensaje de error:

**Error al eliminar blob 'vhds/BlobName.vhd'. Error: No hay una concesión en el objeto binario y se ha especificado ningún identificador de concesión en la convocatoria.**

Este problema puede ocurrir porque una máquina virtual (VM) tiene un permiso en el disco duro virtual que está intentando eliminar.

### <a name="scenario-2"></a>Escenario 2

Cuando intenta eliminar un contenedor de una cuenta de almacenamiento en una implementación de administrador de recursos, recibe el siguiente mensaje de error:

**Error al eliminar el contenedor de almacenamiento 'VHD'. Error: No hay una concesión en el contenedor y se ha especificado ningún identificador de concesión en la convocatoria.**

Este problema puede ocurrir porque el contenedor tiene un disco duro virtual que está bloqueado en el estado de la concesión.

### <a name="scenario-3"></a>Escenario 3

Cuando intenta eliminar una cuenta de almacenamiento en una implementación de administrador de recursos, recibe el siguiente mensaje de error:

**Error al eliminar la cuenta de almacenamiento 'StorageAccountName'. Error: No se puede eliminar la cuenta de almacenamiento debido a sus artefactos está en uso.**

Este problema puede ocurrir porque la cuenta de almacenamiento contiene un disco duro virtual que está en el estado de la concesión.

## <a name="solution"></a>Solución

Para resolver estos problemas, debe identificar el disco duro virtual que causa el error y la máquina virtual asociada. A continuación, suelte el disco duro virtual de la máquina virtual (para discos de datos) o eliminar la máquina virtual que está usando el disco duro virtual (para discos de sistema operativo). Esto quita la concesión desde el disco duro virtual y permite que se pueda eliminar.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Paso 1: Identificar el problema de disco duro virtual y la máquina virtual de asociados


1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. En el menú de **concentrador** , seleccione **todos los recursos**. Vaya a la cuenta de almacenamiento que desea eliminar y, a continuación, seleccione **BLOB** > **VHD**.

    ![Captura de pantalla del portal, con la cuenta de almacenamiento y el contenedor de "VHD" resaltado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

3. Compruebe las propiedades de cada disco duro virtual en el contenedor. Busque el disco duro virtual que está en el estado de **arrendado** . A continuación, determinar qué VM está utilizando el disco duro virtual. Normalmente, puede determinar qué VM contiene el disco duro virtual comprobando el nombre del disco duro virtual:

    - Discos de sistemas operativos generalmente seguir esta convención de nomenclatura: VMNameYYYYMMDDHHMMSS.vhd
    - Discos de datos, en general, sigan esta convención de nomenclatura: VMName-AAAAMMDD-HHMMSS.vhd

    ![Captura de pantalla de la información del contenedor en el portal, con el nombre de la máquina virtual, el estado de "Bloqueado" y el estado de la concesión de "Arrendado" resaltado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Paso 2: Quitar la concesión en el disco duro virtual

Para eliminar la máquina virtual que está usando el disco duro virtual (para discos de sistemas operativos):

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).
2.  En el menú de **concentrador** , seleccione **máquinas virtuales de Windows**.
3.  Seleccione la máquina virtual que contiene una concesión en el disco duro virtual.
4.  Asegúrese de que no hay nada conocidas usa la máquina virtual, y ya no necesita la máquina virtual.
5.  En la parte superior de la hoja de **Detalles de la máquina virtual** , seleccione **Eliminar**y, a continuación, haga clic en **Sí** para confirmar.
6.  Se debe eliminar la máquina virtual, pero debe conservarse el disco duro virtual. Sin embargo, el disco duro virtual ya no debería tener una concesión en él. Puede tardar unos minutos para la concesión liberar. Para comprobar que se libera la concesión, vaya a **todos los recursos** > **Nombre de la cuenta de almacenamiento** > **BLOB** > **VHD**. En el panel **Propiedades de blobs** , el valor de **Estado de la concesión** debe ser **Unlocked**.

Para separar el disco duro virtual de la máquina virtual que está utilizando (para discos de datos):

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).
2.  En el menú de **concentrador** , seleccione **máquinas virtuales de Windows**.
3.  Seleccione la máquina virtual que contiene una concesión en el disco duro virtual.
4.  Seleccione los **discos** en el módulo de **Detalles de la máquina virtual** .
5.  Seleccione el disco de datos que contiene una concesión en el disco duro virtual. Puede determinar qué disco duro virtual está conectado en el disco activando la dirección URL del disco duro virtual.
6.  Determinar con certeza que conocidas nada está usando el disco de datos.
7.  Haga clic en **separar** en el módulo de **Detalles del disco** .
8.  Ahora se debería desasociar el disco de la máquina virtual, y el disco duro virtual ya no debería tener una concesión en él. Puede tardar unos minutos para la concesión liberar. Para comprobar que se ha enviado la concesión, vaya a **todos los recursos** > **Nombre de la cuenta de almacenamiento** > **BLOB** > **VHD**. En el panel **Propiedades de blobs** , el valor de **Estado de la concesión** debe ser **Unlocked**.

## <a name="what-is-a-lease"></a>¿Qué es una concesión?

Una concesión es un bloqueo que puede usarse para controlar el acceso a un objeto binario (por ejemplo, un disco duro virtual). Cuando se le concede un blob, solo los propietarios de la concesión pueden acceder a los blobs. Una concesión es importante por los siguientes motivos:

-   Evita daños en los datos si varios propietarios intentan escribir en la misma parte del blob al mismo tiempo.
-   Impide que el blob eliminarse si algo es conocidas utilizando (por ejemplo, una máquina virtual).
-   Impide que la cuenta de almacenamiento eliminarse si algo es conocidas utilizando (por ejemplo, una máquina virtual).



## <a name="next-steps"></a>Pasos siguientes

- [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
- [Cómo dividir la concesión bloqueada de almacenamiento de blobs de Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
