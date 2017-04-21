<properties
    pageTitle="Utilizar importar o exportar para transferir datos al almacenamiento de blobs | Microsoft Azure"
    description="Aprenda a crear la importación y exportación de trabajos en el portal de Azure clásico para transferir datos de almacenamiento de blobs."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Usar el servicio de importación o exportación de Microsoft Azure transferir datos al almacenamiento de blobs

## <a name="overview"></a>Información general

Servicio de importación o exportación de Azure le permite transferir con seguridad grandes cantidades de datos con el almacenamiento de blobs de Windows Azure enviando unidades de disco duro a un centro de datos de Azure. También puede usar este servicio para transferir datos desde el almacenamiento de blobs de Windows Azure a unidades de disco duro y enviar a su sitio local. Este servicio es adecuado en situaciones donde desea transferir varias TB de datos o de Azure, pero no es factible debido a los costos de red alto o de ancho de banda limitado cargar o descargar a través de la red.

El servicio requiere que las unidades de disco duro debe ser BitLocker cifrada para la seguridad de los datos. El servicio admite cuentas de almacenamiento clásica presentes en todas las regiones de Azure público. Debe enviar unidades de disco duro a una de las ubicaciones compatibles especificadas más adelante en este artículo.

En este artículo, se más información sobre el servicio de importación o exportación de Azure y cómo enviar unidades para copiar los datos desde el almacenamiento de blobs de Windows Azure.

> [AZURE.IMPORTANT] Puede crear y administrar la importación y exportación de trabajos de almacenamiento clásica con el portal de clásico o el [servicio de importación o exportación API de REST](http://go.microsoft.com/fwlink/?LinkID=329099). Cuentas de almacenamiento de administrador de recursos no se admiten en este momento.

## <a name="when-should-i-use-the-azure-importexport-service"></a>¿Cuándo debo usar el servicio de importación o exportación de Azure?

Puede utilizar el servicio de importación o exportación de Azure al cargar o descargar los datos en la red es demasiado lento u obtener el ancho de banda de red adicional es costo prohibitivo.

Puede utilizar este servicio en escenarios como:

- Migración de datos en la nube: mover rápidamente grandes cantidades de datos en Azure y rentable.
- Distribución de contenido: rápidamente enviar datos a los sitios de cliente.
- Copia de seguridad: Realizar copias de seguridad de los datos locales para almacenar en el almacenamiento de blobs de Windows Azure.
- Recuperación de datos: recuperar gran cantidad de datos almacenados en el almacenamiento de blobs y que se entregue a su ubicación local.

## <a name="pre-requisites"></a>Requisitos previos

En esta sección, se muestran los requisitos previos necesarios para usar este servicio. Revise ellos detenidamente antes de enviar las unidades.

### <a name="storage-account"></a>Cuenta de almacenamiento

Debe tener una suscripción existente de Azure y una o varias cuentas de almacenamiento **clásico** para utilizar el servicio de importación o exportación. Cada trabajo puede utilizarse para transferir datos a o desde una cuenta de almacenamiento clásico. En otras palabras, un trabajo de importación/exportación no puede abarcar varias cuentas de almacenamiento. Para obtener información sobre cómo crear una nueva cuenta de almacenamiento, consulte [cómo crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Tipos de BLOB

Puede usar el servicio de importación o exportación de Azure para copiar datos a **bloque** BLOB o blobs de **página** . Por el contrario, solo puede exportar **bloque** BLOB, blobs de **página** o BLOB de **datos anexados** de almacenamiento de Azure con este servicio.

### <a name="job"></a>Trabajo

Para comenzar el proceso de importación o exportación de almacenamiento de blobs, primero crear una tarea. Una tarea puede ser un trabajo de importación o un trabajo de exportación:

- Crear un trabajo de importación al que desea transferir datos tiene local a BLOB en su cuenta de almacenamiento de Azure.
- Crear un trabajo de exportación al que desea transferir datos almacenados como BLOB en su cuenta de almacenamiento para las unidades de disco duro que se le envíe.

Cuando se crea una tarea, notificar el servicio de importación o exportación que se envío una o más unidades de disco duro a un centro de datos de Azure.

- Para un trabajo de importación, se envío unidades de disco duro que contiene los datos.
- Para un trabajo de exportación se envío unidades de disco duro vacías.
- Puede enviar hasta 10 unidades de disco duro por trabajo.

Puede crear una importación o exportación de trabajo mediante el [portal de clásico](https://manage.windowsazure.com/) o la [API de REST de importación o exportación de almacenamiento de Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

### <a name="client-tool"></a>Herramienta de cliente

Es el primer paso para crear una tarea de **importación** preparar la unidad de disco que se envían para la importación. Para preparar la unidad de disco, debe conectarse a un servidor local y ejecutar la herramienta de cliente de importación o exportación de Azure en el servidor local. Esta herramienta de cliente permite copiar los datos a la unidad, cifrado de los datos en la unidad con BitLocker y generar los archivos de diario de unidad.

Los archivos de diario contienen información básica sobre el trabajo y en la unidad como número de serie de unidad y nombre de la cuenta de almacenamiento. Este archivo de diario no se almacena en la unidad. Se utiliza durante la creación de trabajo de importación. Paso a paso los detalles sobre la creación de trabajo se proporcionan más adelante en este artículo.

La herramienta de cliente solo es compatible con el sistema operativo de Windows de 64 bits. Consulte la sección de [sistema operativo](#operating-system) para versiones específicas de sistema operativo compatible.

Descargue la última versión de la [herramienta de cliente de Azure importar o exportar](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Para obtener más detalles sobre el uso de la herramienta de importación o exportación de Azure, vea la [Referencia de herramienta de importación o exportación de Azure](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Unidades de disco duro

3,5 solo se admiten unidades de disco duro interno SATA II/III para su uso con el servicio de importación o exportación. Puede utilizar unidades de disco duro hasta 10TB.
Para trabajos de importación, se procesará solo el primer volumen de datos en la unidad. El volumen de datos debe tener el formato NTFS.
Al copiar datos en el disco duro, puede adjuntarlo directamente mediante un conector SATA o puede adjuntar externamente mediante un adaptador de SATA II/III USB externo. Le recomendamos que utilice uno de los siguientes adaptadores de SATA II/III USB externos:

- Anker 68UPSATAA - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Si tiene un convertidor que no está enumerado anteriormente, puede intentar ejecutar la herramienta de importación o exportación Azure utilizando el convertidor para preparar la unidad y ver si funciona antes de comprar un convertidor compatible.

> [AZURE.IMPORTANT] Unidades de disco duro externas que vienen con un adaptador USB integrado no son compatibles con este servicio. Además, no se puede utilizar el disco dentro de las mayúsculas y minúsculas de un disco duro externo; No enviar unidades de disco duro externos.

### <a name="encryption"></a>Cifrado

Los datos de la unidad se deben cifrados con cifrado de unidad BitLocker. Esto protege los datos mientras está en tránsito.

Para los trabajos de importación, hay dos formas para realizar el cifrado. La primera forma es utilizar la / cifrar el parámetro al ejecutar la herramienta de cliente durante la preparación de unidad. La segunda forma es habilitar cifrado BitLocker manualmente en la unidad y especificar la clave de cifrado en la línea de comandos de la herramienta de cliente durante la preparación de unidad.

Para los trabajos de exportación, después de copiarán los datos en las unidades, el servicio cifrar la unidad con BitLocker antes de enviar a usted. La clave de cifrado se proporcionará a usted a través del portal de clásico.  

### <a name="operating-system"></a>Sistema operativo

Puede usar uno de los siguientes sistemas operativos de 64 bits para preparar el disco duro mediante la herramienta de importación o exportación de Azure antes de enviar la unidad de Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Enterprise de Windows 8, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Todos estos sistemas operativos admiten cifrado de unidad BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup> Si está utilizando un equipo de Windows 10 para preparar el disco duro, descargue la última versión de la herramienta de importación o exportación de Azure.

### <a name="locations"></a>Ubicaciones

El servicio de importación o exportación de Azure admite copiar datos a y desde todas las cuentas de almacenamiento de Azure público. Puede enviar en unidades de disco duro a una de las siguientes ubicaciones. Si su cuenta de almacenamiento en una ubicación de Azure pública que no se especifica aquí, se proporcionará una ubicación de envío alternativo al crear el trabajo con el portal de clásico o la API de REST de importar o exportar.

Admite ubicaciones de envío:

- Estados Unidos oriental

- Estados Unidos occidental

- Estados Unidos oriental 2

- Central de EE.

- Norte Central de EE.

- Sur Central de EE.

- Europa del Norte

- Europa occidental

- Asia oriental

- Sudeste asiático

- Australia Oriental

- Australia sureste

- Japón oeste

- Japón oriental

- India central


### <a name="shipping"></a>Envío

**Unidades de envío al centro de datos:**

Al crear un trabajo de importación o exportación, se le proporcionará una dirección de envío de una de las ubicaciones compatibles para enviar las unidades. La dirección de envío proporcionada dependen de la ubicación de la cuenta de almacenamiento, pero no puede ser la misma que la ubicación de la cuenta de almacenamiento.

Puede usar los operadores como FedEx, DHL, puede o US Postal Service para enviar las unidades de la dirección de envío.

**Unidades de envío desde el centro de datos:**

Al crear un trabajo de importación o exportación, debe proporcionar una dirección de remite para Microsoft para usar cuando las unidades de envío después de que su trabajo esté completa. Asegúrese de que proporcione una dirección de remite válida para evitar retrasos en el proceso.

También debe proporcionar un transportista FedEx o DHL válido para ser usada por Microsoft para envío de las unidades de nuevo el número de cuenta. Un número de cuenta de FedEx se requiere para las unidades de envío de las ubicaciones de Estados Unidos y Europa. Un número de cuenta DHL se requiere para las unidades de envío de Asia y Australia ubicaciones. Puede crear una cuenta de transportista de [DHL](http://www.dhl.com/) (Asia y Australia) o [FedEx](http://www.fedex.com/us/oadr/) (para Estados Unidos y Europa) si no tiene uno. Si ya tiene un número de cuenta de la compañía, compruebe que es válida.

En los paquetes de envío, debe seguir los términos en [Términos de servicio de Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Tenga en cuenta que necesiten el medio físico que envían a cruz bordes internacionales. Usted es responsable de importar o exportar conforme a las leyes aplicables de sus datos y medios físicos. Antes de enviarlo para el medio físico, póngase en contacto con su asesores para comprobar que los elementos multimedia y datos legal pueden enviarse al centro de datos identificados. Esto le ayudará a garantizar que llegue Microsoft de forma oportuna. Por ejemplo, cualquier paquete que intersecará bordes internacionales necesita una factura comercial acompañar con el paquete (excepto si paso bordes dentro de la Unión Europea). Puede imprimir una copia de la factura comercial desde el sitio Web de transportista relleno. Ejemplo de comercial factura [Factura comercial de FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf)o que estén [factura comercial DHL] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf). Asegúrese de que Microsoft no se indica como exportador.

## <a name="how-does-the-azure-importexport-service-work"></a>¿Cómo funciona el servicio de importación o exportación de Azure?

Puede transferir datos entre su sitio local y el almacenamiento de blobs de Windows Azure con el servicio de importación o exportación de Azure creando trabajos y unidades de disco duro a un centro de datos de Azure de envío. Enviar cada unidad de disco duro está asociado con un solo trabajo. Cada trabajo está asociado a una cuenta de almacenamiento única. Revise la [sección Requisitos previos](#pre-requisites) detenidamente para obtener información sobre las características específicas de este servicio compatible como blobs tipos, tipos de disco, ubicaciones y envío.

En esta sección, describiremos un alto nivel de los pasos necesarios para importación y exportación trabajos. Más adelante en la [sección de inicio rápido](#quick-start), proporcionamos instrucciones paso a paso para crear una importación y exportación de trabajo.

### <a name="inside-an-import-job"></a>Dentro de un trabajo de importación

En un nivel alto, un trabajo de importación implica los siguientes pasos:

- Determinar los datos que desea importar y a continuación, el número de unidades que se necesita.
- Identificar el BLOB de destino para los datos en el almacenamiento de blobs.
- Use la herramienta de importación o exportación de Azure para copiar los datos a una o más unidades de disco duro y los cifra con BitLocker.  
- Crear un trabajo de importación en su cuenta de almacenamiento clásico de destino mediante el portal de clásico o la API de REST de importar o exportar. Si usa el portal clásica, cargue los archivos de diario de unidad.
- Proporcione la dirección de remite y el número de cuenta de transportista que se usará para envío las unidades.
- Enviar las unidades de disco duro a la dirección de envío proporcionados durante la creación del trabajo.
- Actualice la entrega número en los detalles del trabajo de importación de seguimiento y enviar el trabajo de importación.
- Unidades se recibió y se procesan en el centro de datos de Azure.
- Unidades se envían con su cuenta de transportista a la dirección de remite en el trabajo de importación.

    ![Figura 1:Import flujo de trabajo](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>Dentro de un trabajo de exportación

En un nivel alto, un trabajo de exportación implica los siguientes pasos:

- Determinar la exportación de datos y el número de unidades que se necesita.
- Identificar el BLOB de origen o rutas de acceso de contenedor de los datos en el almacenamiento de blobs.
- Crear un trabajo de exportación en su cuenta de almacenamiento de origen mediante el portal de clásico o la API de REST de importar o exportar.
- Especificar el BLOB de origen o rutas de acceso de contenedor de los datos en el trabajo de exportación.
- Proporcione la dirección de remite y el número de cuenta de transportista para que se usará para envío las unidades.
- Enviar las unidades de disco duro a la dirección de envío proporcionados durante la creación del trabajo.
- Actualice la entrega número en los detalles del trabajo de exportación de seguimiento y enviar el trabajo de exportación.
- Las unidades se recibió y se procesan en el centro de datos de Azure.
- Las unidades están encriptadas con BitLocker; las teclas están disponibles a través del portal de clásico.  
- Las unidades se envían con su cuenta de transportista a la dirección de remite en el trabajo de importación.

    ![Figura 2:Export flujo de trabajo](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Ver el estado del trabajo

Puede realizar un seguimiento del estado de la importación o exportación trabajos desde el portal de clásico. Vaya a su cuenta de almacenamiento en el portal de clásica y haga clic en la pestaña de **Importar o exportar** . Aparecerá una lista de los trabajos en la página. Puede filtrar la lista en el estado del trabajo, en nombre del trabajo, tipo de trabajo o número de seguimiento.

Verá uno de los siguientes estados de trabajo dependiendo de dónde se encuentra la unidad de disco en el proceso.

| Estado del trabajo   | Descripción                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Crear     | Se ha creado su trabajo, pero no ha proporcionado la información de envío.                                                                                                                                                                                                                                                                                                |
| Envío     | Se ha creado su trabajo y ha proporcionado la información de envío. **Nota**: cuando la unidad se ha entregado al centro de datos de Azure, el estado aún puede mostrar "Envío" algún tiempo. Una vez que inicia el servicio para copiar los datos, el estado cambiará a "Transferir". Si desea ver el estado de más específica de la unidad de disco, puede usar la API de REST de importar o exportar. |
| Transferir | Los datos se transfiere desde el disco duro (para un trabajo de importación) o en el disco duro (para un trabajo de exportación).                                                                                                                                                                                                                                                                 |
| Embalaje    | Finalizar la transferencia de los datos y el disco duro se está preparando para envío a usted.                                                                                                                                                                                                                                                                             |
| Completar     | El disco duro se ha enviado a usted.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Tiempo de trabajo de proceso

La cantidad de tiempo que se tarda en proceso un trabajo de importación o exportación varía dependiendo de factores diferentes, como la hora de envío, tipo, tipo y tamaño de los datos copiados y el tamaño de los discos proporcionados de trabajo. El servicio de importación o exportación no tiene un SLA. Puede usar la API de REST para realizar un seguimiento del progreso de trabajo más de cerca. Hay un parámetro porcentaje completado de la operación de trabajos de la lista que da una indicación de progreso de la copia. Póngase en contacto con nosotros si necesita una estimación para completar un trabajo críticas de importación o exportación de tiempo.

### <a name="pricing"></a>Precios

**Unidad tarifa de manipulación**

Hay una cuota de manipulación de unidad de cada unidad procesada como parte de la importación o exportación de trabajo. Ver los detalles de los [Precios de importación o exportación de Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Los gastos de envío**

Cuando se envían unidades de Azure, pagar los gastos de envío para el transportista. Cuando Microsoft devuelve las unidades a usted, los gastos de envío se cargarán a la cuenta de transportista que proporciona en el momento de la creación de empleo.

**Costes de transacción**

No hay ningún costos de transacción al importar datos en el almacenamiento de blobs. Los cargos de salida estándar son aplicables cuando se exportan los datos de almacenamiento de blobs de Windows. Para obtener más detalles sobre los costes de transacción, vea [precios de transferencia de datos.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Inicio rápido

En esta sección, proporcionamos instrucciones paso a paso para crear una importación y un trabajo de exportación. Asegúrese de que cumple todos los [requisitos previos](#pre-requisites) antes de continuar.

## <a name="how-to-create-an-import-job"></a>¿Cómo crear un trabajo de importación?

Crear un trabajo de importación para copiar datos a su cuenta de Azure almacenamiento de unidades de disco duro por una o más unidades que contienen datos en el centro de datos especificada de envío. El trabajo de importación concede detalles sobre los datos que se va a copiar, destino cuenta de almacenamiento y la información de envío en el servicio de importación o exportación de Azure unidades de disco duro. Crear un trabajo de importación es un proceso de tres pasos. En primer lugar, preparar las unidades mediante la herramienta de cliente de Azure importar o exportar. En segundo término, enviar un trabajo de importación con el portal de clásico. Tercera enviar las unidades de la dirección de envío proporcionados durante la creación de empleo y actualizar la información de envío en los detalles de su trabajo.   

> [AZURE.IMPORTANT] Puede enviar un único trabajo por cuenta de almacenamiento. Puede importar cada unidad que se envían a una cuenta de almacenamiento. Por ejemplo, supongamos que desea importar datos en dos cuentas de almacenamiento. Debe utilizar unidades de disco duro independientes para cada cuenta de almacenamiento y crear tareas independientes para cada cuenta de almacenamiento.

### <a name="prepare-your-drives"></a>Preparar las unidades

El primer paso al importar datos mediante el servicio de importación o exportación de Azure es preparar las unidades mediante la herramienta de cliente de Azure importar o exportar. Siga los pasos siguientes para preparar las unidades.

1.  Identifique los datos que desea importar. Esto puede deberse a directorios y archivos independientes en el servidor local o en un recurso compartido de red.  

2.  Determinar el número de unidades que debe según el tamaño total de los datos. Obtener el número de unidades de disco duro de 3,5 SATA II/III necesarios.

3.  Identificar la cuenta de almacenamiento de destino, contenedor, directorios virtuales y BLOB.

4.  Determinar los directorios y archivos independientes que se copiarán en cada unidad de disco duro.

5.  Use la [Herramienta de importación o exportación de Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para copiar los datos en una o más unidades de disco duro.

    - La herramienta de importación o exportación de Azure crea sesiones de copiar para copiar los datos de origen en las unidades de disco duro. En una sesión única copia, la herramienta puede copiar un único directorio junto con sus subdirectorios o un solo archivo.

    - Puede que tenga varias copia sesiones si los datos de origen abarcan muchos directorios.

    - Cada unidad de disco duro que prepare necesitarán sesión al menos una copia.

6.  Puede especificar la / cifrar el parámetro para habilitar el cifrado de Bitlocker en la unidad de disco duro. Como alternativa, puede habilitar el cifrado de Bitlocker manualmente en la unidad de disco duro y proporcionar la tecla mientras se ejecuta la herramienta.

7.  La herramienta de importación o exportación de Azure genera un archivo de diario de unidad de cada unidad de medida está preparado. El archivo de diario de unidad se almacena en el equipo local, no en la misma unidad. Se cargue el archivo de diario cuando se crea el trabajo de importación. Un archivo de diario de unidad incluye el identificador de unidad y la clave de BitLocker, así como otra información acerca de la unidad.
**Importante**: cada unidad de disco duro prepare dará como resultado un archivo de diario. Cuando se crea el trabajo de importación con el portal de clásico, debe cargar todos los archivos de diario de las unidades que forman parte de ese trabajo de importación. Unidades sin diario no se puede procesar archivos.

8.  No modifique los datos en las unidades de disco duro o el archivo de diario después de completar la preparación del disco.

A continuación se muestran los comandos y ejemplos para preparar la unidad de disco duro con la herramienta de cliente de Azure importar o exportar.

Azure importar o exportar cliente PrepImport comando herramienta para la primera sesión de copiar copiar un directorio:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Ejemplo:**

En el ejemplo siguiente se va a copiar todos los archivos y subdirectorios de H:\Video a la unidad de disco duro en X:. Los datos se importarán a la cuenta de almacenamiento de destino especificada por la clave de cuenta de almacenamiento y en el contenedor de almacenamiento denominado vídeo. Si no está presente el contenedor de almacenamiento, se creará. Este comando también formato y cifrar la unidad de disco duro de destino.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure importar o exportar cliente PrepImport comando herramienta para sesiones subsiguientes copiar copiar un directorio:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Para sesiones de copia posteriores a la misma unidad de disco duro, especifique el mismo nombre de archivo de diario y proporcionar un nuevo ID; no es necesario que proporcionar de nuevo, la unidad de clave y de destino de la cuenta de almacenamiento ni para dar formato o cifrar la unidad. En este ejemplo se copiarán la carpeta H:\Photo y sus subdirectorios a la misma unidad de destino, en el contenedor de almacenamiento denominado fotografía.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

La herramienta de cliente de importación o exportación de Azure comando PrepImport para la primera sesión de copiar copiar un archivo:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

La herramienta de cliente de Azure importar o exportar PrepImport comando para sesiones subsiguientes copiar copiar un archivo:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Recuerde**: de forma predeterminada, los datos se importarán como Blobs de bloque. Puede usar el parámetro /BlobType para importar los datos como un BLOB de página. Por ejemplo, si va a importar archivos de disco duro virtual que se instalarse como discos en una máquina virtual de Azure, debe importarlos como Blobs de página. Si no está seguro de qué blob tipo para usar, puede especificar /blobType:auto y le ayudará a determinar el tipo correcto. En este caso, todos los archivos de disco duro virtual y vhdx se importarán como Blobs de página y el resto se importarán como Blobs de bloque.

Ver más detalles sobre cómo usar la herramienta de cliente de importación o exportación de Azure en [Preparar las unidades de disco duro para importar](https://msdn.microsoft.com/library/dn529089.aspx).

Además, consulte el [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](https://msdn.microsoft.com/library/dn529097.aspx) para obtener más instrucciones paso a paso.  

### <a name="create-the-import-job"></a>Crear el trabajo de importación

1.  Una vez que ha preparado la unidad de disco, vaya a su cuenta de almacenamiento en el [portal de clásica](https://manage.windowsazure.com) y ver el panel. **Eche un vistazo rápido**, haga clic en **crear un trabajo de importación**. Revise los pasos y Active la casilla de verificación para indicar que ha preparado la unidad de disco y que tiene el archivo de diario de unidad disponible.

2.  En el paso 1, proporcionar información de contacto de la persona responsable de trabajo de importación y una dirección de remite válida. Si desea guardar los datos del registro detallado para el trabajo de importación, active la opción para **Guardar el registro detallado en el contenedor de blob 'waimportexport'**.

3.  En el paso 2, cargue los archivos de diario de unidad obtenida durante el paso de preparación de unidad. Debe cargar un archivo de cada unidad que ha preparado.

    ![Crear el trabajo de importación - paso 3](./media/storage-import-export-service/import-job-03.png)

4.  En el paso 3, escriba un nombre descriptivo para el trabajo de importación. Observe que el nombre que escribe puede contener sólo letras minúsculas, números, guiones y caracteres de subrayado, debe comenzar con una letra y no puede contener espacios. Use el nombre que elija para realizar un seguimiento de los trabajos mientras se encuentran en curso y una vez que se han completado.

    A continuación, seleccione la región del centro de datos de la lista. La región del centro de datos indicará el centro de datos y la dirección a la que debe enviar el paquete. Consulte las preguntas más frecuentes a continuación para obtener más información.

5.  En el paso 4, seleccione el transportista retorno de la lista y escriba su número de cuenta de transportista. Microsoft usará esta cuenta para enviar las unidades una vez finalizado el trabajo de importación.

    Si tiene el número de seguimiento, seleccione el operador de entrega de la lista y escriba el número de seguimiento.

    Si no tiene un número de seguimiento ha, elija **proporcionaré mi información de envío de trabajo de importación una vez que he enviado mi paquete**, a continuación, complete el proceso de importación.

6. Para introducir el número de seguimiento después de que ha enviado el paquete, volver a la página **Importar o exportar** para su cuenta de almacenamiento en el portal de clásico, seleccione su trabajo en la lista y elija **Información de envío**. Desplazarse por el asistente y escriba su número de seguimiento en el paso 2.

    Si el número de seguimiento no se actualiza antes de crear el trabajo, la tarea va a expirar.

    Si la tarea está en el estado de creación, envío o transferir, también puede actualizar el número de cuenta de transportista en el paso 2 del asistente. Una vez que la tarea está en el estado de embalaje, no puede actualizar su número de cuenta de transportista para ese trabajo.

7. Puede realizar un seguimiento del progreso de trabajo en el panel de portal. Consulte ¿Qué significa cada estado de trabajo en la sección anterior al [Ver el estado del trabajo](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>¿Cómo crear un trabajo de exportación?

Crear un trabajo de exportación para que le notifique el servicio de importación o exportación que se publicaremos una o más unidades vacías al centro de datos para que datos puedan ser exportados desde su cuenta de almacenamiento a las unidades y las unidades enviados.

### <a name="prepare-your-drives"></a>Preparar las unidades

Comprobaciones previas siguientes se recomiendan para preparar las unidades de un trabajo de exportación:

1. Compruebe el número de discos necesario mediante el comando de PreviewExport de la herramienta de Azure importar o exportar. Para obtener más información, consulte [Vista previa de unidad uso para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Le ayuda a obtener una vista previa de uso de la unidad para el BLOB que ha seleccionado, en función del tamaño de las unidades que se va a usar.

2. Compruebe que pueden leer y escribir en el disco duro que se envían para el trabajo de exportación.

### <a name="create-the-export-job"></a>Crear el trabajo de exportación

1.  Para crear un trabajo de exportación, vaya a su cuenta de almacenamiento en el [portal de clásica](https://manage.windowsazure.com)y ver el panel. **Eche un vistazo rápido**, haga clic en **crear un trabajo de exportación** y continúe con el asistente.

2.  En el paso 2, proporcionar información de contacto de la persona responsable de esta tarea de exportación. Si desea guardar los datos del registro detallado para el trabajo de exportación, active la opción para **Guardar el registro detallado en el contenedor de blob 'waimportexport'**.

3.  En el paso 3, especifique qué datos blob que desea exportar desde su cuenta de almacenamiento a la unidad en blanco o en las unidades. Puede elegir exportar todos los datos blob en la cuenta de almacenamiento, o puede especificar que blobs o conjuntos de blobs para exportar.

    Para especificar un blob para exportar, utilice el selector **Es igual a** y especifique la ruta de acceso relativa para el blob, comenzando por el nombre del contenedor. Use *$root* para especificar el contenedor raíz.

    Para especificar todos los blobs comenzando con un prefijo, use el selector **Empieza por** y especifique el prefijo, comenzando con una barra diagonal '/'. El prefijo puede ser el prefijo del nombre del contenedor, el nombre del contenedor completa o el nombre de contenedor completo seguido por el prefijo del nombre del blob.

    La siguiente tabla muestra ejemplos de rutas de blobs válida:

    Selector|Ruta de acceso de blobs|Descripción
    ---|---|---
    Empieza por|/|Exporta todos los blobs en la cuenta de almacenamiento
    Empieza por|/$root /|Exporta todos los blobs en el contenedor raíz
    Empieza por|/Book|Exporta todos los blobs en cualquier contenedor que comienza con el prefijo **libro**
    Empieza por|/Music/|Exporta todos los blobs en contenedor **música**
    Empieza por|amor/música /|Exporta todos los blobs en contenedor **música** que comienzan con el prefijo **amor**
    Es igual a|$root/logo.bmp|Exportaciones blob **logo.bmp** en el contenedor raíz
    Es igual a|videos/Story.mp4|Exportaciones blob **story.mp4** en contenedor **vídeos**

    Debe proporcionar las rutas de acceso de blobs de formatos válidos para evitar errores durante el proceso, como se muestra en esta captura de pantalla.

    ![Crear tarea de exportación: paso 3](./media/storage-import-export-service/export-job-03.png)


4.  En el paso 4, escriba un nombre descriptivo para el trabajo de exportación. El nombre que escribe puede contener sólo letras minúsculas, números, guiones y caracteres de subrayado, debe comenzar con una letra y no puede contener espacios.

    Indica el centro de datos al que debe enviar el paquete de la región del centro de datos. Consulte las preguntas más frecuentes a continuación para obtener más información.

5.  En el paso 5, seleccione el transportista retorno de la lista y escriba su número de cuenta de transportista. Microsoft usará esta cuenta para enviar su unidades una vez finalizado el trabajo de exportación.

    Si tiene el número de seguimiento, seleccione el operador de entrega de la lista y escriba el número de seguimiento.

    Si no tiene un número de seguimiento ha, elija **proporcionaré mi información de envío para este trabajo una vez que he enviado mi paquete de exportación**, a continuación, complete el proceso de exportación.

6. Para introducir el número de seguimiento después de que ha enviado el paquete, volver a la página **Importar o exportar** para su cuenta de almacenamiento en el portal de clásico, seleccione su trabajo en la lista y elija **Información de envío**. Desplazarse por el asistente y escriba su número de seguimiento en el paso 2.

    Si el número de seguimiento no se actualiza antes de crear el trabajo, la tarea va a expirar.

    Si la tarea está en el estado de creación, envío o transferir, también puede actualizar el número de cuenta de transportista en el paso 2 del asistente. Una vez que la tarea está en el estado de embalaje, no puede actualizar su número de cuenta de transportista para ese trabajo.

    > [AZURE.NOTE] Si el blob exportarse está en uso en el momento de copiar en el disco duro, servicio de importación o exportación de Azure tomar una instantánea del blob y copie la instantánea.

7.  Para realizar un seguimiento de su progreso de tarea en el panel en el portal de clásico. Consulte ¿Qué significa cada estado de trabajo en la sección anterior en "ver el estado de tarea".

8.  Después de recibir las unidades con los datos exportados, puede ver y copiar las claves de BitLocker generadas por el servicio de la unidad de disco. Vaya a su cuenta de almacenamiento en el portal de clásica y haga clic en la pestaña de importar o exportar. Seleccione el trabajo de exportación de la lista y haga clic en el botón teclas de vista. Las claves de BitLocker aparecen como se muestra a continuación:

    ![Ver las claves de BitLocker para el trabajo de exportación](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Vaya a través de la sección Preguntas más frecuentes sobre como se tratan las preguntas más comunes que los clientes surgir al usar este servicio.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes ##


**¿Cuánto tiempo tardará para copiar los datos después de mis unidades alcance el centro de datos?**

El tiempo para copiar varía según distintos factores como el tipo de trabajo, tipo y tamaño de los datos copiados, tamaño de los discos proporcionan y existentes de carga de trabajo. Puede variar de un par de días a un par de semanas, dependiendo de estos factores. Por lo tanto, es difícil proporcionar una estimación general. El servicio intenta optimizar su trabajo copiando varias unidades en paralelo cuando sea posible. Si tiene un trabajo críticas de importación o exportación de tiempo, póngase en contacto con nosotros para una estimación.

**¿Cuándo debo usar el servicio de importación o exportación de Azure?**
Uno debe tener en cuenta usando Azure importar exportar si carga o descarga de red tarda aproximadamente estimación más de 7 días. Puede calcular cuánto se tardará utilizando cualquier Calculadora en línea o [Descargar](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) lo que se encuentra en nuestra Azure importar exportar REST API muestra en el repositorio de ejemplos de Azure en [Calculadora de velocidad de transferencia de datos](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). No es un cálculo exacto, pero solo una indicación aproximada.

**¿Puedo usar el servicio de importación o exportación de Azure con una cuenta de administrador de recursos de almacenamiento?**

No, no puede copiar datos a o desde una cuenta de almacenamiento de administrador de recursos directamente mediante el servicio de importación o exportación de Azure. El servicio sólo admite cuentas de almacenamiento clásico. Soporte de cuenta de almacenamiento de administrador de recursos se próximamente. Como alternativa, puede importar datos a una cuenta de almacenamiento clásica y migrar a su cuenta de almacenamiento de administrador de recursos mediante [AzCopy](storage-use-azcopy.md) o CopyBlob.

**¿Puedo copiar archivos de Azure con el servicio de importación o exportación de Azure?**

No, el servicio de importación o exportación de Azure solo admite bloque BLOB y Blobs de página. Todos los demás tipos de almacenamiento, incluidos los archivos de Azure, tablas y colas no son compatibles.

**¿Está disponible para suscripciones de CSP el servicio de importación o exportación de Azure?**

No, el servicio de importación o exportación de Azure no admite suscripciones CSP. Se agregará la compatibilidad en el futuro.

**¿Omitir el paso de preparación de unidad para un trabajo de importación o puedo preparar una unidad sin copiar?**

Debe estar preparada cualquier unidad en la que desea enviar para importar datos con la herramienta de cliente de Azure importar o exportar. Debe usar la herramienta de cliente para copiar datos a la unidad.

**¿Es necesario realizar la preparación de disco al crear un trabajo de exportación?**

Se recomiendan no, pero algunas comprobaciones previas. Compruebe el número de discos necesario mediante el comando de PreviewExport de la herramienta de Azure importar o exportar. Para obtener más información, consulte [Vista previa de unidad uso para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Le ayuda a obtener una vista previa de uso de la unidad para el BLOB que ha seleccionado, en función del tamaño de las unidades que se va a usar. Compruebe también que puede leer y escribir en el disco duro que se enviarán para el trabajo de exportación.

**¿Qué sucede si envío accidentalmente un disco duro que no se ajusten a los requisitos compatibles?**

El centro de datos de Azure devolverá la unidad que no se ajusta a los requisitos compatibles para usted. Si solo algunas de las unidades en el paquete cumplen los requisitos de soporte técnico, procesará las unidades y las unidades que no cumplan los requisitos se devolverán a usted.

**¿Puedo cancelar mi trabajo?**

Puede cancelar un trabajo cuando su estado es crear o de envío.

**¿Cuánto tiempo se puede ver el estado de las tareas completadas en portal clásica?**

Puede ver el estado de las tareas completadas de 90 días. Tareas completadas se eliminarán después de 90 días.

**Si quiero a importar o exportar más de 10 unidades, ¿qué debo hacer?**

Una importación o exportación trabajo hacer referencia a solo 10 unidades en un solo trabajo del servicio de importación o exportación. Si desea enviar más de 10 unidades, puede crear varios trabajos. Unidades que están asociadas a la misma tarea deben enviarse juntos en el mismo paquete.

**¿Puedo usar un adaptador USB SATA que no está en la lista recomendada?**

Si tiene un convertidor que no está enumerado anteriormente, puede intentar ejecutar la herramienta de importación o exportación Azure utilizando el convertidor para preparar la unidad y ver si funciona antes de comprar un convertidor compatible.

**¿El servicio formatear las unidades antes de volver a ellos?**

No. Todas las unidades están encriptadas con BitLocker.

**¿Puedo adquirir unidades para las tareas de importación o exportación de Microsoft?**

No. Necesitará enviar sus propia unidades para ambos importar y exportar trabajos.

**Una vez finalizado el trabajo de importación, ¿qué se Mis datos aspecto en la cuenta de almacenamiento? ¿Se conservará la jerarquía del directorio?**

Al preparar una unidad de disco duro para un trabajo de importación, el destino especificado por el /dstdir: parámetro. Este es el contenedor de destino en la cuenta de almacenamiento a la que se copian los datos desde el disco duro. Dentro de este contenedor de destino, se crean directorios virtuales para las carpetas de la unidad de disco dura y BLOB se crea para archivos.

**¿Si la unidad tiene archivos que ya existen en mi cuenta de almacenamiento, el servicio sobrescribirá BLOB existente en mi cuenta de almacenamiento?**

Al preparar la unidad, puede especificar si se deben sobrescribir los archivos de destino o ignorada mediante el parámetro denominado /Disposition: < cambiar nombre | sobrescribir no | sobrescribir >. De forma predeterminada, el servicio se cambie el nombre de los nuevos archivos en lugar de sobrescribir BLOB existente.

**¿Es compatible con los sistemas operativos de 32 bits la herramienta de cliente de importación o exportación de Azure?**
No. La herramienta de cliente solo es compatible con los sistemas operativos de Windows de 64 bits. Consulte la sección de sistemas operativos de los [requisitos previos](#pre-requisites) para obtener una lista completa de versiones de sistemas operativos compatibles.

**¿Debo incluir nada que no sea la unidad de disco duro en Mi paquete?**

Envía solo las unidades de disco duras. No incluya elementos como cables de alimentación o cables USB.

**¿Tengo que enviar mis unidades con FedEx o DHL?**

Puede enviar unidades para el centro de datos con cualquier operador conocido como FedEx, DHL, puede o US Postal Service. Sin embargo, para enviar las unidades a usted desde el centro de datos, debe proporcionar un número de cuenta de FedEx en Estados Unidos y la Unión Europea, o un número de cuenta DHL en las regiones de Asia y Australia.

**¿Hay algunas restricciones con mi unidad internacionalmente de envío?**

Tenga en cuenta que necesiten el medio físico que envían a cruz bordes internacionales. Usted es responsable de importar o exportar conforme a las leyes aplicables de sus datos y medios físicos. Antes de enviarlo para el medio físico, póngase en contacto con sus asesores para comprobar que los elementos multimedia y datos legal pueden enviarse al centro de datos identificados. Esto le ayudará a garantizar que llegue Microsoft de forma oportuna.

**Al crear una tarea, la dirección de envío es una ubicación diferente de la ubicación de mi cuenta de almacenamiento. ¿Qué debo hacer?**

Algunas ubicaciones de almacenamiento de la cuenta se asignan a las ubicaciones de envío alternativo. Previamente ubicaciones disponibles de envío pueden también temporalmente asignarse a ubicaciones alternativas. Comprobar siempre la dirección de envío proporcionada durante la creación de trabajo antes de enviar las unidades.

**¿Por qué mi estado de trabajo en la clásica portal diga "envío" cuando el sitio Web de transportista muestra mi paquete se entrega?**

El estado en el portal de clásica cambia de envío transferencia cuando la unidad de procesamiento se inicia. Si la unidad de disco alcanzó la instalación, pero no ha iniciado el procesamiento, el estado del trabajo mostrará como envío.

**Cuando la unidad de envío, el transportista le pide el nombre de contacto de centro de datos y el número de teléfono. ¿Qué debo proporcionar?**

El número de teléfono se proporciona durante la creación del trabajo. Si necesita un nombre de contacto, póngase en contacto con nosotros en waimportexport@microsoft.com y le ofrecemos con esa información.

**¿Puedo usar el servicio de importación o exportación de Azure para copiar buzones PST y datos de SharePoint en O365?**

Consulte [los archivos PST de importación o los datos de SharePoint a Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**¿Puedo usar el servicio de importación o exportación de Azure para copiar las copias de seguridad sin conexión en el servicio de copia de seguridad de Azure?**

Consulte [flujo de trabajo de copia de seguridad sin conexión de copia de seguridad de Azure](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Vea también:

- [Configurar la herramienta de cliente de importación o exportación de Azure](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transferir datos con la utilidad de línea de comandos de AzCopy](storage-use-azcopy.md)

- [Ejemplo de la API de REST de Azure importar exportar](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
