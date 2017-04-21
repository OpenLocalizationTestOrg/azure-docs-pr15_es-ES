<properties
    pageTitle="Cómo crear, administrar o eliminar una cuenta de almacenamiento en el Portal de clásico de Azure | Microsoft Azure"
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

Una cuenta de almacenamiento de Azure le proporciona acceso a los servicios de blobs de Windows Azure, cola, tabla y archivo de almacenamiento de Azure. Su cuenta de almacenamiento proporciona el espacio de nombres único para los objetos de datos de almacenamiento de Azure. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta.

Existen dos tipos de cuentas de almacenamiento:

- Una cuenta de almacenamiento estándar incluye almacenamiento de blobs, tabla, cola y archivo.
- Una cuenta de almacenamiento premium actualmente Azure máquina virtual sólo admite discos. Consulte [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md) para una introducción exhaustiva de almacenamiento Premium.

## <a name="storage-account-billing"></a>Facturación de la cuenta de almacenamiento

Facturación de uso de almacenamiento de Azure en función de su cuenta de almacenamiento. Los costos de almacenamiento se basan en cuatro factores: capacidad de almacenamiento, esquema de replicación, las transacciones de almacenamiento y salida de datos.

- Capacidad de almacenamiento hace referencia a la cantidad de la asignación de la cuenta de almacenamiento está usando para almacenar los datos. El costo de simplemente almacenar los datos se determina por la cantidad de datos que se almacena y cómo crear la réplica.
- Replicación determina cuántas copias de los datos se mantienen a la vez y en qué ubicaciones.
- Consulte transacciones para todas las operaciones lectura y escritura para el almacenamiento de Azure.
- Salida de datos hace referencia a datos que se transfieren fuera de un área de Azure. Cuando se tiene acceso a los datos de su cuenta de almacenamiento por una aplicación que no se ejecuta en la misma región, si dicha aplicación es un servicio de nube o algún otro tipo de aplicación, se cargan de salida de datos. (Para los servicios de Azure, puede seguir los pasos para agrupar los datos y los servicios en los mismos centros de datos para reducir o eliminar los gastos de salida de datos.)  

La página de [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage) proporciona información detallada de precio para la capacidad de almacenamiento, replicación y las transacciones. La página de [Detalles de precios de transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/) proporciona información detallada de precio de salida de datos.

Para obtener más información sobre destinos de capacidad y el rendimiento de la cuenta de almacenamiento, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Cuando se crea una máquina virtual Azure, una cuenta de almacenamiento se creará automáticamente en la ubicación de implementación si ya no tiene una cuenta de almacenamiento en esa ubicación. Por lo que no es necesario seguir los pasos siguientes para crear una cuenta de almacenamiento para los discos de máquina virtual. El nombre de la cuenta de almacenamiento se se basa en el nombre de la máquina virtual. Consulte la [documentación de Azure máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/) para obtener más detalles.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

1. Inicie sesión en el [Portal de clásico de Azure](https://manage.windowsazure.com).

2. En la barra de tareas en la parte inferior de la página, haga clic en **nuevo** . Elija **Los servicios de datos** | de**almacenamiento**y, a continuación, haga clic en **Crear rápido**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. En **dirección URL**, escriba un nombre para su cuenta de almacenamiento.

    > [AZURE.NOTE] Nombres de cuenta de almacenamiento deben estar entre 3 y 24 caracteres de longitud y pueden contener números y letras minúsculas solo.
    >  
    > El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El Portal de clásico de Azure indicará si ya se toma el nombre de la cuenta de almacenamiento que seleccione.

    Consulte [extremos de la cuenta de almacenamiento](#storage-account-endpoints) a continuación para obtener más información sobre cómo se utilizará el nombre de la cuenta de almacenamiento para tratar los objetos en el almacenamiento de Azure.

4. En el **Grupo ubicación/afinidad**, seleccione una ubicación para su cuenta de almacenamiento que sea más cercanos o a sus clientes. Si los datos en su cuenta de almacenamiento serán accesibles desde otro servicio de Azure, como una máquina virtual de Azure o un servicio de nube desea seleccionar un grupo de afinidad de la lista para su cuenta de almacenamiento en el mismo centro de datos con otros servicios de Azure que va a usar para mejorar el rendimiento y reducir los costos de grupo.

    Tenga en cuenta que debe seleccionar un grupo de afinidad cuando se crea la cuenta de almacenamiento. No se puede mover una cuenta existente a un grupo de afinidad. Para obtener más información sobre grupos de afinidad, vea [la ubicación del servicio con un grupo de afinidad](#service-co-location-with-an-affinity-group) .

    >[AZURE.IMPORTANT] Para determinar las ubicaciones que están disponibles para su suscripción, puede llamar a la operación de [la lista de todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Lista de proveedores de PowerShell, llame a [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). Desde. NET, use el método de la [lista](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) de la clase ProviderOperationsExtensions.
    >
    >Además, vea [Áreas de Azure](https://azure.microsoft.com/regions/#services) para obtener más información sobre qué servicios están disponibles en qué región.


5. Si tiene más de una suscripción de Azure, se muestra el campo de la **suscripción** . En la **suscripción**, escriba la suscripción de Azure que desea usar con la cuenta de almacenamiento.

6. En la **replicación**, seleccione el nivel deseado de replicación de la cuenta de almacenamiento. La opción de replicación recomendada es la replicación geo redundantes, que proporciona la duración máxima para sus datos. Para obtener más detalles sobre las opciones de replicación de almacenamiento de Azure, vea [replicación de almacenamiento de Azure](storage-redundancy.md).

6. Haga clic en **crear cuenta de almacenamiento**.

    Puede tardar unos minutos para crear su cuenta de almacenamiento. Para comprobar el estado, puede supervisar las notificaciones en la parte inferior del Portal clásico de Azure. Después de crear la cuenta de almacenamiento, la nueva cuenta de almacenamiento tiene el estado **en línea** y está lista para su uso.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Extremos de la cuenta de almacenamiento

Todos los objetos que se guardan en el almacenamiento de Azure tienen una dirección URL única. El nombre de la cuenta de almacenamiento de formularios el subdominio de dicha dirección. La combinación de nombre de dominio y subdominio, que es específico de cada servicio, forma un *punto final* de la cuenta de almacenamiento.

Por ejemplo, si su cuenta de almacenamiento se denomina *mystorageaccount*, los extremos de forma predeterminada para su cuenta de almacenamiento son:

- Servicio de objetos binarios: http://*mystorageaccount*. blob.core.windows.net

- Servicio de tabla: http://*mystorageaccount*. table.core.windows.net

- Cola de servicio: http://*mystorageaccount*. queue.core.windows.net

- Servicio de archivos: http://*mystorageaccount*. file.core.windows.net

Puede ver los extremos de su cuenta de almacenamiento en el panel de almacenamiento en el [Portal de Azure clásica](https://manage.windowsazure.com) una vez que se ha creado la cuenta.

La dirección URL para tener acceso a un objeto en una cuenta de almacenamiento se ha creado agregando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blobs de Windows podría tener este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

También puede configurar un nombre de dominio personalizado para usarlo con su cuenta de almacenamiento. Para obtener información detallada, consulte [configurar un nombre de dominio personalizado para el extremo de almacenamiento de blobs de Windows](storage-custom-domain-name.md) .

### <a name="service-co-location-with-an-affinity-group"></a>La ubicación del servicio con un grupo de afinidad

Un *grupo afinidad* es una agrupación geográfica de los servicios de Azure y máquinas virtuales con su cuenta de almacenamiento de Azure. Un grupo de afinidad puede mejorar el rendimiento de servicio mediante la ubicación de las cargas de trabajo del equipo en el mismo centro de datos o cerca de la audiencia del usuario de destino. Además, gastos de facturación no se incurre de salida cuando se tiene acceso a datos en una cuenta de almacenamiento desde otro servicio que forma parte del mismo grupo afinidad.

> [AZURE.NOTE]  Para crear un grupo de afinidad, abra el área <b>configuración</b> del [Portal de Azure clásica](https://manage.windowsazure.com), haga clic en <b>grupos de afinidad</b>y, a continuación, haga clic en <b>Agregar un grupo de afinidad</b> o el botón <b>Agregar</b> . También puede crear y administrar grupos de afinidad mediante la API de administración de servicio de Azure. Para obtener más información, vea <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">operaciones en grupos de afinidad</a> .

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Ver, copiar y volver a crear las teclas de acceso de almacenamiento

Cuando se crea una cuenta de almacenamiento, Azure genera dos teclas de acceso de almacenamiento de 512 bits, que se usan para la autenticación cuando se tiene acceso a la cuenta de almacenamiento. Proporcionando dos teclas de acceso de almacenamiento Azure le permite volver a generar las claves con ninguna interrupción en el servicio de almacenamiento o el acceso a dicho servicio.

> [AZURE.NOTE] Se recomienda evitar el uso compartido de las claves de acceso de almacenamiento con nadie. Para permitir el acceso a los recursos de almacenamiento sin entregar las claves de access, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso en su cuenta por un intervalo que defina y con los permisos que se especifican. Para obtener más información, vea [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md) .

En el [Portal de Azure clásica](https://manage.windowsazure.com), use **Administrar claves** en el panel o en la página de **almacenamiento** para ver, copiar y volver a crear las teclas de acceso de almacenamiento que se usan para tener acceso a los servicios de blobs, tabla y cola.

### <a name="copy-a-storage-access-key"></a>Copiar una tecla de acceso de almacenamiento  

Puede usar **Administrar claves** para copiar una tecla de acceso de almacenamiento para usar en una cadena de conexión. La cadena de conexión requiere el nombre de la cuenta de almacenamiento y una clave para usar en la autenticación. Para obtener información acerca de cómo configurar las cadenas de conexión para acceder a los servicios de almacenamiento de Azure, vea [Configurar cadenas de conexión de almacenamiento de Azure](storage-configure-connection-string.md).

1. En el [Portal de Azure clásica](https://manage.windowsazure.com), haga clic en **almacenamiento**y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **administrar claves**.

    **Administrar las teclas de acceso** se abre.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Para copiar una tecla de acceso de almacenamiento, seleccione el texto de la tecla. Luego contextual y haga clic en **Copiar**.

### <a name="regenerate-storage-access-keys"></a>Volver a crear las teclas de acceso de almacenamiento
Se recomienda que cambie las teclas de acceso a su cuenta de almacenamiento periódicamente para ayudar a mantener las conexiones de almacenamiento seguro. Se asignan dos teclas de acceso para que puede mantener conexiones con la cuenta de almacenamiento usando una tecla de acceso mientras regenerar la otra clave de acceso.

> [AZURE.WARNING] Volver a generar las claves de acceso puede afectar a servicios en Azure, así como sus propias aplicaciones que dependen de la cuenta de almacenamiento. Para usar la clave nueva se deben actualizar todos los clientes que utilizan la tecla de acceso para tener acceso a la cuenta de almacenamiento.

**Media services** - si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las teclas de acceso con el servicio de medios después de volver a crear las teclas.

**Aplicaciones** - si tiene aplicaciones web o servicios en la nube que utilizan la cuenta de almacenamiento, perderá las conexiones si regenerar claves, a menos que funcionar las claves. 

**Exploradores de almacenamiento** : si está utilizando las [aplicaciones del explorador de almacenamiento](storage-explorers.md), probablemente necesite actualizar la clave de almacenamiento utilizada por las aplicaciones.

Aquí es el proceso para girar las claves de acceso de almacenamiento:

1. Actualizar las cadenas de conexión en el código de la aplicación para hacer referencia a la tecla de acceso secundario de la cuenta de almacenamiento.

2. Regenerar la clave de acceso principal para su cuenta de almacenamiento. En el [Portal de Azure clásica](https://manage.windowsazure.com), desde el escritorio o la página **Configurar** , haga clic en **Administrar claves**. Haga clic en **regenerar** la clave de acceso principal y, a continuación, haga clic en **Sí** para confirmar que desea generar una nueva clave.

3. Actualizar las cadenas de conexión en el código para hacer referencia a la nueva clave de acceso principal.

4. Regenerar la clave de acceso secundario.

## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no está utilizando, use **Eliminar** en el panel o en la página **Configurar** . **Eliminar** elimina la cuenta de almacenamiento completo, incluye todos los blobs, tablas y colas de la cuenta.

> [AZURE.WARNING] No es posible restaurar una cuenta de almacenamiento eliminado o recuperar cualquier contenido que contenía antes de la eliminación. No olvide hacer copia de seguridad que desee guardar antes de eliminar la cuenta. Esto también se aplica a los recursos en la cuenta: una vez que elimine un blob, archivo o la tabla, cola, se elimina permanentemente.
>
> Si su cuenta de almacenamiento contiene los archivos de disco duro virtual para una máquina virtual Azure, debe eliminar las imágenes y los discos que utilizan los archivos de disco duro virtual antes de eliminar la cuenta de almacenamiento. En primer lugar, detenga la máquina virtual si se está ejecutando y elimínelo. Para eliminar discos, vaya a la ficha **discos** y elimine los discos allí. Para eliminar imágenes, vaya a la ficha **imágenes** y elimine las imágenes que se almacenan en la cuenta.

1. En el [Portal de Azure clásica](https://manage.windowsazure.com), haga clic en **almacenamiento**.

2. Haga clic en la entrada de la cuenta de almacenamiento excepto el nombre y, a continuación, haga clic en **Eliminar**.

     - O bien-

    Haga clic en el nombre de la cuenta de almacenamiento para abrir el panel y, a continuación, haga clic en **Eliminar**.

3. Haga clic en **Sí** para confirmar que desea eliminar la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el almacenamiento de Azure, consulte la [documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)
