<properties
    pageTitle="Cómo crear, administrar o eliminar una cuenta de almacenamiento en el Portal de Azure | Microsoft Azure"
    description="Crear una nueva cuenta de almacenamiento, administrar las claves de acceso de cuenta o eliminar una cuenta de almacenamiento en el Portal de Azure. Obtenga información acerca de las cuentas de almacenamiento estándar y premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>Acerca de las cuentas de almacenamiento de Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Información general

Una cuenta de almacenamiento de Azure proporciona un único espacio de nombres para almacenar y tener acceso a los objetos de datos de almacenamiento de Azure. Todos los objetos de una cuenta de almacenamiento se cargarán juntos como un grupo. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Facturación de la cuenta de almacenamiento

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Cuando se crea una máquina virtual Azure, una cuenta de almacenamiento se creará automáticamente en la ubicación de implementación si ya no tiene una cuenta de almacenamiento en esa ubicación. Por lo que no es necesario seguir los pasos siguientes para crear una cuenta de almacenamiento para los discos de máquina virtual. El nombre de la cuenta de almacenamiento se se basa en el nombre de la máquina virtual. Consulte la [documentación de Azure máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/) para obtener más detalles.

## <a name="storage-account-endpoints"></a>Extremos de la cuenta de almacenamiento

Todos los objetos que se guardan en el almacenamiento de Azure tienen una dirección URL única. El nombre de la cuenta de almacenamiento de formularios el subdominio de dicha dirección. La combinación de nombre de dominio y subdominio, que es específico de cada servicio, forma un *punto final* de la cuenta de almacenamiento.

Por ejemplo, si su cuenta de almacenamiento se denomina *mystorageaccount*, los extremos de forma predeterminada para su cuenta de almacenamiento son:

- Servicio de objetos binarios: http://*mystorageaccount*. blob.core.windows.net

- Servicio de tabla: http://*mystorageaccount*. table.core.windows.net

- Cola de servicio: http://*mystorageaccount*. queue.core.windows.net

- Servicio de archivos: http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Una cuenta de almacenamiento de blobs sólo expone el extremo de servicio de blobs de Windows.

La dirección URL para tener acceso a un objeto en una cuenta de almacenamiento se ha creado agregando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blobs de Windows podría tener este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

También puede configurar un nombre de dominio personalizado para usarlo con su cuenta de almacenamiento. Para cuentas de almacenamiento clásica, consulte [configurar un nombre para el extremo de almacenamiento de blobs de dominio personalizado](storage-custom-domain-name.md) para obtener información detallada. Para las cuentas de almacenamiento de administrador de recursos, esta capacidad no se ha agregado el [portal de Azure](https://portal.azure.com) todavía, pero puede configurarlo con PowerShell. Para obtener más información, vea el cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. En el menú de concentrador, seleccione **nuevo** -> **datos + almacenamiento** -> **cuenta de almacenamiento**.

3. Escriba un nombre para su cuenta de almacenamiento. Vea los [extremos de la cuenta de almacenamiento](#storage-account-endpoints) para obtener más información sobre cómo se utilizará el nombre de la cuenta de almacenamiento para tratar los objetos en el almacenamiento de Azure.

    > [AZURE.NOTE] Nombres de cuenta de almacenamiento deben estar entre 3 y 24 caracteres de longitud y pueden contener números y letras minúsculas solo.
    >  
    > El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El portal de Azure indica si el nombre de cuenta de almacenamiento seleccionado ya está en uso.

4. Especificar el modelo de implementación que se utilizarán: **Administrador de recursos** o **clásica**. **Administrador de recursos** es el modelo de implementación recomendado. Para obtener más información, vea [Descripción de administrador de recursos e implementación clásica](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Cuentas de almacenamiento de blobs solo pueden crearse con el modelo de implementación de administrador de recursos.

5. Seleccione el tipo de cuenta de almacenamiento: **almacenamiento de blobs**de **Propósito General** o. **Propósito general** es el valor predeterminado.

    Si se ha seleccionado el **Propósito General** , a continuación, especifique el nivel de rendimiento: **estándar** o **Premium**. El valor predeterminado es **estándar**. Para obtener más detalles sobre las cuentas de almacenamiento estándar y premium, vea [Introducción a Microsoft Azure almacenamiento](storage-introduction.md) y [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md).

    Si se ha seleccionado el **Almacenamiento de blobs** , a continuación, especifique el nivel de acceso: **activa** o **interesante**. El valor predeterminado es **activa**. Consulte [el almacenamiento de blobs de Windows Azure: enfriar y caliente niveles](storage-blob-storage-tiers.md) para obtener más detalles.

6. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **GRS**, **RA GRS**o **ZRS**. El valor predeterminado es **RA GRS**. Para obtener más detalles sobre las opciones de replicación de almacenamiento de Azure, vea [replicación de almacenamiento de Azure](storage-redundancy.md).

7. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

8. Especificar un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para obtener más información sobre los grupos de recursos, vea [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

9. Seleccione la ubicación geográfica para su cuenta de almacenamiento. Para obtener más información sobre qué servicios están disponibles en qué región, vea [Regiones de Azure](https://azure.microsoft.com/regions/#services) .

10. Haga clic en **crear** para crear la cuenta de almacenamiento.

## <a name="manage-your-storage-account"></a>Administrar su cuenta de almacenamiento

### <a name="change-your-account-configuration"></a>Cambiar la configuración de cuenta

Después de crear la cuenta de almacenamiento, puede modificar su configuración, como cambiar la opción de replicación de la cuenta o cambiar el nivel de acceso de una cuenta de almacenamiento de blobs de Windows. En el [portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento, haga clic en **toda la configuración** y, a continuación, haga clic en **configuración** para ver o cambiar la configuración de cuenta.

> [AZURE.NOTE] Según el nivel de rendimiento que eligió al crear la cuenta de almacenamiento, es podrán que algunas opciones de replicación no esté disponibles.

Cambiar la opción de replicación cambiará a los precios. Para obtener más detalles, consulte la página [precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) .

Para las cuentas de almacenamiento de blobs, cambiar el nivel de acceso puede implicar cargos para que el cambio, además de cambiar los precios. Vea las [cuentas de almacenamiento de blobs - precios y facturación](storage-blob-storage-tiers.md#pricing-and-billing) para obtener más detalles.

### <a name="manage-your-storage-access-keys"></a>Administrar las claves de acceso de almacenamiento

Cuando se crea una cuenta de almacenamiento, Azure genera dos teclas de acceso de almacenamiento de 512 bits, que se usan para la autenticación cuando se tiene acceso a la cuenta de almacenamiento. Proporcionando dos teclas de acceso de almacenamiento Azure le permite volver a generar las claves con ninguna interrupción en el servicio de almacenamiento o el acceso a dicho servicio.

> [AZURE.NOTE] Se recomienda evitar el uso compartido de las claves de acceso de almacenamiento con nadie. Para permitir el acceso a los recursos de almacenamiento sin entregar las claves de access, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso en su cuenta por un intervalo que defina y con los permisos que se especifican. Para obtener más información, vea [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Ver y copiar las teclas de acceso de almacenamiento

En el [portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento, haga clic en **toda la configuración** y, a continuación, haga clic en **las teclas de acceso** para ver, copiar y volver a crear las claves de acceso de la cuenta. El módulo de **Teclas de acceso** también incluye cadenas de conexión preconfigurado mediante las claves principales y secundarias que puede copiar para usar en las aplicaciones.

#### <a name="regenerate-storage-access-keys"></a>Volver a crear las teclas de acceso de almacenamiento

Se recomienda que cambie las teclas de acceso a su cuenta de almacenamiento periódicamente para ayudar a mantener las conexiones de almacenamiento seguro. Se asignan dos teclas de acceso para que puede mantener conexiones con la cuenta de almacenamiento usando una tecla de acceso mientras regenerar la otra clave de acceso.

> [AZURE.WARNING] Volver a generar las claves de acceso puede afectar a servicios en Azure, así como sus propias aplicaciones que dependen de la cuenta de almacenamiento. Para usar la clave nueva se deben actualizar todos los clientes que utilizan la tecla de acceso para tener acceso a la cuenta de almacenamiento.

**Media services** - si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las teclas de acceso con el servicio de medios después de volver a crear las teclas.

**Aplicaciones** - si tiene aplicaciones web o servicios en la nube que utilizan la cuenta de almacenamiento, perderá las conexiones si regenerar claves, a menos que funcionar las claves.

**Exploradores de almacenamiento** : si está utilizando las [aplicaciones del explorador de almacenamiento](storage-explorers.md), probablemente necesite actualizar la clave de almacenamiento utilizada por las aplicaciones.

Aquí es el proceso para girar las claves de acceso de almacenamiento:

1. Actualizar las cadenas de conexión en el código de la aplicación para hacer referencia a la tecla de acceso secundario de la cuenta de almacenamiento.

2. Regenerar la clave de acceso principal para su cuenta de almacenamiento. En el módulo de **Teclas de acceso** , haga clic en **Regenerar clave1**y, a continuación, haga clic en **Sí** para confirmar que desea generar una nueva clave.

3. Actualizar las cadenas de conexión en el código para hacer referencia a la nueva clave de acceso principal.

4. Regenerar la clave de acceso secundario de la misma manera.

## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no está utilizando, vaya a la cuenta de almacenamiento en el [portal de Azure](https://portal.azure.com)y haga clic en **Eliminar**. Eliminar una cuenta de almacenamiento, elimina toda la cuenta, incluidos todos los datos de la cuenta.

> [AZURE.WARNING] No es posible restaurar una cuenta de almacenamiento eliminado o recuperar cualquier contenido que contenía antes de la eliminación. No olvide hacer copia de seguridad que desee guardar antes de eliminar la cuenta. Esto también se aplica a los recursos en la cuenta: una vez que elimine un blob, archivo o la tabla, cola, se elimina permanentemente.

Para eliminar una cuenta de almacenamiento que esté asociada a una máquina virtual Azure, primero debe asegurarse de que se hayan eliminado los discos de máquina virtual. Si no se elimina los discos de máquina virtual en primer lugar, a continuación, al intentar eliminar la cuenta de almacenamiento, verá un mensaje de error similar al:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si la cuenta de almacenamiento utiliza el modelo de implementación de clásico, puede quitar el disco de máquina virtual siguiendo estos pasos en el [portal de Azure](https://manage.windowsazure.com):

1. Desplácese hasta el [portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la pestaña máquinas virtuales de Windows.
3. Haga clic en la ficha discos.
4. Seleccione el disco de datos y luego haga clic en Eliminar disco.
5. Para eliminar las imágenes de disco, vaya a la ficha imágenes y elimine las imágenes que se almacenan en la cuenta.

Para obtener más información, consulte la [documentación de la máquina Virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Pasos siguientes

- [Almacenamiento de blobs de Windows Azure: Niveles de buenas y activa](storage-blob-storage-tiers.md)
- [Replicación de almacenamiento de Azure](storage-redundancy.md)
- [Configurar cadenas de conexión de almacenamiento de Azure](storage-configure-connection-string.md)
- [Transferir datos con la herramienta de AzCopy de línea de comandos](storage-use-azcopy.md)
- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).
