<properties
    pageTitle="Copiar o mover datos de almacenamiento con AzCopy | Microsoft Azure"
    description="Use la utilidad AzCopy para mover o copiar datos a o desde blobs, la tabla y el contenido del archivo. Copiar datos en Azure almacenamiento de archivos locales o copiar datos en o entre cuentas de almacenamiento. Migrar fácilmente los datos con el almacenamiento de Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Transferir datos con la herramienta de línea de comandos de AzCopy

## <a name="overview"></a>Información general

AzCopy es una herramienta de línea de comandos de Windows diseñada para copiar datos a y desde el almacenamiento de blobs de Microsoft Azure, el archivo y la tabla con los comandos simples con un rendimiento óptimo. Puede copiar datos de un objeto a otro dentro de su cuenta de almacenamiento, o entre cuentas de almacenamiento.

> [AZURE.NOTE] Esta guía se supone que ya está familiarizado con [El almacenamiento de Azure](https://azure.microsoft.com/services/storage/). Si no es así, será útil leer la documentación de [Introducción al almacenamiento de Azure](storage-introduction.md) . Lo más importante, debe [crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) para poder empezar a usar AzCopy.

## <a name="download-and-install-azcopy"></a>Descargar e instalar AzCopy

### <a name="windows"></a>Windows

Descargue la [última versión de AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac o Linux.

AzCopy no está disponible para sistemas operativos Mac o Linux. Sin embargo, CLI de Azure es una alternativa adecuada para copiar datos a y desde el almacenamiento de Azure. Leer [mediante la CLI de Azure con el almacenamiento de Azure](storage-azure-cli.md) para obtener más información.

## <a name="writing-your-first-azcopy-command"></a>Escribir el primer comando AzCopy

La sintaxis básica para los comandos de AzCopy es:

    AzCopy /Source:<source> /Dest:<destination> [Options]

Abra una ventana de comandos y navegue hasta el directorio de instalación de AzCopy en su equipo, donde la `AzCopy.exe` ejecutable se encuentra. Si lo desea, puede agregar la ubicación de instalación de AzCopy a la ruta del sistema. De forma predeterminada, se instala AzCopy a `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` o `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Los siguientes ejemplos muestran una gran variedad de escenarios para copiar datos a y desde Microsoft Azure BLOB, archivos y las tablas. Consulte la sección [AzCopy parámetros](#azcopy-parameters) para una explicación detallada de los parámetros utilizados en cada muestra.

## <a name="blob-download"></a>Blobs: descargar

### <a name="download-single-blob"></a>Descargar blob único

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Tenga en cuenta que si la carpeta `C:\myfolder` no existe, se crea y descargar AzCopy `abc.txt ` en la nueva carpeta.

### <a name="download-single-blob-from-secondary-region"></a>Descargar blob único de región secundaria

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Tenga en cuenta que debe tener acceso de lectura almacenamiento geo redundantes habilitado.

### <a name="download-all-blobs"></a>Descargar todos los blobs

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Supongamos que la siguientes BLOB residen en el contenedor especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de descarga, en el directorio `C:\myfolder` incluirá los archivos siguientes:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Si no especifica la opción `/S`, no se descargará ningún BLOB.

### <a name="download-blobs-with-specified-prefix"></a>Descargar BLOB con prefijo especificado

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Supongamos que la siguientes BLOB residen en el contenedor especificado. Todos los blobs empieza con el prefijo `a` se descargará:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de descarga de la carpeta `C:\myfolder` incluirá los archivos siguientes:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

El prefijo se aplica al directorio virtual, que la primera parte del nombre de blobs de formularios. En el ejemplo anterior, el directorio virtual no coincide con el prefijo especificado, por lo que no se ha descargado. Además, si la opción `\S` no se especifica, AzCopy se descargará cualquier BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Establecer la hora de última modificación de los archivos exportados a ser igual que el BLOB de origen

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

También puede excluir BLOB de la operación de descarga en función de su hora de última modificación. Por ejemplo, si desea excluir BLOB cuya última hora de modificación es la misma o más reciente que el archivo de destino, agregue la `/XN` opción:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

O si desea excluir BLOB cuya última hora de modificación es la misma o anteriores al archivo de destino, agregue la `/XO` opción:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>Blobs: cargar

### <a name="upload-single-file"></a>Cargar un solo archivo

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Si el contenedor de destino especificado no existe, AzCopy se cree y cargue el archivo en él.

### <a name="upload-single-file-to-virtual-directory"></a>Cargar un solo archivo al directorio virtual

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Si el directorio virtual especificado no existe, AzCopy se cargue el archivo para incluir el directorio virtual en su nombre (*por ejemplo*, `vd/abc.txt` en el ejemplo anterior).

### <a name="upload-all-files"></a>Cargar todos los archivos

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Especificar opción `/S` carga el contenido del directorio especificado en forma recursiva de almacenamiento de blobs, lo que significa que todas las subcarpetas y los archivos se cargarán también. Por ejemplo, imaginemos que se encuentran los siguientes archivos en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de carga, el contenedor incluirá los archivos siguientes:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si no especifica la opción `/S`, AzCopy ¿no se cargan de forma recursiva. Después de la operación de carga, el contenedor incluirá los archivos siguientes:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Cargar archivos que coincidan con el patrón especificado

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Supongamos los siguientes archivos se encuentran en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de carga, el contenedor incluirá los archivos siguientes:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si no especifica la opción `/S`, AzCopy sólo cargará BLOB que no se encuentran en un directorio virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especifique el tipo de contenido MIME de un blob de destino

De forma predeterminada, AzCopy establece el tipo de contenido de un blob de destino a `application/octet-stream`. A partir de la versión 3.1.0, puede especificar explícitamente el tipo de contenido mediante la opción `/SetContentType:[content-type]`. Esta sintaxis establece el tipo de contenido para todos los blobs en una operación de carga.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Si especifica `/SetContentType` sin un valor, a continuación, AzCopy se establecerá cada blob o tipo de contenido del archivo según su extensión de archivo.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>Blobs: copiar

### <a name="copy-single-blob-within-storage-account"></a>Copiar blob único dentro de la cuenta de almacenamiento

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Cuando copia un blob dentro de una cuenta de almacenamiento, se realiza una operación de [copia del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-single-blob-across-storage-accounts"></a>Copiar blob único a través de cuentas de almacenamiento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Cuando copia un blob entre cuentas de almacenamiento, se realiza una operación de [copia del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copiar blob único de secundaria región a área principal

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Tenga en cuenta que debe tener acceso de lectura almacenamiento geo redundantes habilitado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiar blob único y sus instantáneas entre cuentas de almacenamiento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Después de la operación de copia, el contenedor de destino incluirá el blob y sus instantáneas. Suponiendo que el blob en el ejemplo anterior tiene dos instantáneas, el contenedor incluirá el blob e instantáneas siguientes:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copiar sincrónicamente BLOB a través de cuentas de almacenamiento

AzCopy predeterminada copia asincrónica datos entre dos extremos de almacenamiento. Por lo tanto, la operación de copia se ejecuta en segundo plano con capacidad de reserva de ancho de banda que no tiene SLA en términos de cómo rápidamente un blob se copiarán y AzCopy Compruebe periódicamente el estado de la copia hasta que la copia se haya completado o error.

La `/SyncCopy` opción garantiza que la operación de copia obtendrá velocidad coherente. AzCopy realiza la copia sincrónica descargando el BLOB para copiar desde el origen especificado en la memoria local y, a continuación, los carga en el destino de almacenamiento de blobs de Windows.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`puede generar el costo de salida adicionales en comparación con copia asincrónica, el enfoque recomendado es usar esta opción en una máquina virtual de Azure que está en la misma región como su cuenta de almacenamiento de origen para evitar el costo de salida.

## <a name="file-download"></a>Archivo: descargar

### <a name="download-single-file"></a>Descargar un solo archivo

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si el origen especificado es un recurso compartido de archivos de Azure, debe especificar el nombre de archivo exacto (*por ejemplo,* `abc.txt`) para descargar un solo archivo, o especificar opción `/S` para descargar todos los archivos en el recurso compartido de forma recursiva. Intenta especificar un patrón de archivo y la opción `/S` juntas, se producirá un error.

### <a name="download-all-files"></a>Descargar todos los archivos

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Tenga en cuenta que no se descargan las carpetas vacías.

## <a name="file-upload"></a>Archivo: cargar

### <a name="upload-single-file"></a>Cargar un solo archivo

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Cargar todos los archivos

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Tenga en cuenta que no se cargarán todas las carpetas vacías.

### <a name="upload-files-matching-specified-pattern"></a>Cargar archivos que coincidan con el patrón especificado

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>Archivo: copiar

### <a name="copy-across-file-shares"></a>Copiar a través de recursos compartidos de archivos

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Copie en el recurso compartido de archivos de objetos binarios

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Tenga en cuenta que no se admite copiar asincrónica de almacenamiento de archivos para blobs de página.

### <a name="copy-from-blob-to-file-share"></a>Copiar de blob a recurso compartido de archivos

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Copiar archivos de forma sincrónica

Puede especificar la `/SyncCopy` opción para copiar los datos de almacenamiento de archivos al almacenamiento de archivos, de almacenamiento de archivos al almacenamiento de blobs y de almacenamiento de blobs de almacenamiento de archivos sincrónicamente, AzCopy para ello, descargar los datos de origen en memoria local y cargar de nuevo destino.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Cuando se copian desde el almacenamiento de archivos al almacenamiento de blobs de Windows, el tipo de blob predeterminado es blobs de bloque, usuario puede especificar la opción `/BlobType:page` para cambiar el tipo de blob de destino.

Tenga en cuenta que `/SyncCopy` puede generar salida adicional comparación de copia asincrónica de costo, el enfoque recomendado es usar esta opción en la máquina virtual de Azure que está en la misma región como su cuenta de almacenamiento de origen para evitar el costo de salida.

## <a name="table-export"></a>Tabla: exportar

### <a name="export-table"></a>Exportar tabla

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy escribe un archivo manifiesto a la carpeta de destino especificado. El archivo manifiesto se utiliza en el proceso de importación para localizar los archivos de datos necesarios y realizar la validación de datos. El archivo manifiesto usa la siguiente convención de nomenclatura predeterminada:

    <account name>_<table name>_<timestamp>.manifest

Usuario también puede especificar la opción `/Manifest:<manifest file name>` para establecer el nombre de archivo manifiestos.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Exportación de división en varios archivos

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utiliza un *índice de volumen* en los nombres de archivo de datos dividida para distinguir varios archivos. El índice de volumen consta de dos partes, un *índice de intervalo de claves de partición* y un *índice de archivo de división*. Los índices comienzan en cero.

El índice de intervalo de claves de partición será 0 si el usuario no especifica la opción `/PKRS`.

Por ejemplo, suponga que AzCopy genera dos archivos de datos después de que el usuario especifica la opción `/SplitSize`. Los nombres de archivo de datos resultante podrían ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenga en cuenta que el mínimo posible valor opción `/SplitSize` es 32MB. Si el destino especificado es el almacenamiento de blobs, AzCopy se divida el archivo de datos una vez que sus tamaños alcanza el límite de tamaño de blob (200GB), independientemente de si la opción `/SplitSize` se ha especificado por el usuario.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Exportar tabla a formato de archivo de datos JSON o CSV

AzCopy predeterminada exporta tablas a archivos de datos JSON. Puede especificar la opción `/PayloadFormat:JSON|CSV` exportar las tablas como JSON o CSV.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Cuando se especifica el formato CSV de carga, AzCopy también generará un archivo de esquema con la extensión de archivo `.schema.csv` para cada archivo de datos.

### <a name="export-table-entities-concurrently"></a>Exportar entidades de tabla de forma simultánea

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy se iniciará operaciones simultáneas para exportar entidades cuando el usuario especifica la opción `/PKRS`. Cada operación exporta un intervalo de clave de partición.

Tenga en cuenta que el número de operaciones simultáneas también se controla mediante la opción `/NC`. AzCopy utiliza el número de procesadores como el valor predeterminado de `/NC` al copiar entidades de tabla, incluso si `/NC` no se ha especificado. Cuando el usuario especifica la opción `/PKRS`, AzCopy usa el valor más pequeño de los dos valores, los intervalos de clave de partición frente a implícitamente o explícitamente especifican operaciones simultáneas - para determinar el número de operaciones simultáneas para empezar. Para obtener más detalles, escriba `AzCopy /?:NC` en la línea de comandos.

### <a name="export-table-to-blob"></a>Exportar tabla a blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy generará un archivo de datos JSON en el contenedor de blob siguientes convención de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

El archivo de datos JSON generado sigue el formato de carga para metadatos mínimos. Para obtener más información sobre este formato de carga, vea [Aplicar formato de carga para operaciones de servicio de la tabla](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Tenga en cuenta que al exportar tablas a BLOB, AzCopy descargar las entidades de tabla en archivos de datos temporales local y, a continuación, cargar las entidades en el blob. Estos archivos de datos temporales se colocan en la carpeta de archivos de diario con la ruta de acceso predeterminada "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", puede especificar la opción/Z: [carpeta de archivos de diario] para cambiar el diario de ubicación de la carpeta de archivos y, por tanto, cambiar la ubicación de archivos de datos temporales. Tamaño de las entidades de la tabla y el tamaño que haya especificado en la opción /SplitSize decide el tamaño de los archivos temporales de datos, aunque el archivo de datos temporal en el disco local se eliminarán al instante una vez que se ha cargado el blob, asegúrese de que tiene suficiente espacio en disco local para almacenar estos archivos de datos temporales antes de que se hayan eliminado.

## <a name="table-import"></a>Tabla: importar

### <a name="import-table"></a>Tabla de importación

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

La opción `/EntityOperation` indica cómo insertar entidades en la tabla. Los valores posibles son:

- `InsertOrSkip`: Omite una entidad existente o inserta una nueva entidad si no existe en la tabla.
- `InsertOrMerge`: Combina una entidad existente o inserta una nueva entidad si no existe en la tabla.
- `InsertOrReplace`: Reemplaza una entidad existente o inserta una nueva entidad si no existe en la tabla.

Observe que no puede especificar la opción `/PKRS` en el caso de importación. A diferencia del escenario de exportación, en el que se debe especificar opción `/PKRS` para iniciar operaciones simultáneas, AzCopy predeterminada iniciará operaciones simultáneas al importar una tabla. El número predeterminado de operaciones simultáneas iniciado es igual al número de procesadores; Sin embargo, puede especificar un número diferente de simultáneas con la opción `/NC`. Para obtener más detalles, escriba `AzCopy /?:NC` en la línea de comandos.

Tenga en cuenta que AzCopy sólo admite la importación de JSON, no CSV. AzCopy no admite la importación de tabla de JSON creados por el usuario y archivos de manifiestos. Ambos archivos deben proceder de una exportación de tabla AzCopy. Para evitar errores, no modifique el JSON exportado o archivo de manifiestos.

### <a name="import-entities-to-table-using-blobs"></a>Importar entidades a la tabla con BLOB

Supongamos un contenedor de Blob contiene lo siguiente: archivo A JSON que representa una tabla de Azure y su archivo de manifiestos adjunta.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Puede ejecutar el comando siguiente para importar entidades en una tabla usando el archivo manifiesto en ese contenedor blob:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Otras características de AzCopy

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiar datos que no existen en el destino

La `/XO` y `/XN` parámetros le permiten excluir recursos de origen anterior o posterior de copiado, respectivamente. Si solo desea copiar los recursos de origen que no existen en el destino, puede especificar ambos parámetros en el comando AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Nota: Este es incompatible cuando el origen o destino es una tabla.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Usar un archivo de respuesta para especificar los parámetros de línea de comandos

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

Puede incluir todos los parámetros de línea de comandos AzCopy en un archivo de respuesta. AzCopy procesa los parámetros en el archivo como si hubiera especificado en la línea de comandos, realizar una sustitución directa con el contenido del archivo.

Se supone un archivo de respuesta denominado `copyoperation.txt`, que contiene las siguientes líneas. Cada parámetro AzCopy se puede especificar en una sola línea

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

o en diferentes líneas:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy se producirá un error si divide el parámetro entre dos líneas, como se muestra aquí para la `/sourcekey` parámetro:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Usar varios archivos de respuesta para especificar los parámetros de línea de comandos

Se supone un archivo de respuesta denominado `source.txt` que especifica un contenedor de origen:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Un archivo de respuesta denominado `dest.txt` que especifica una carpeta de destino en el sistema de archivos:

    /Dest:C:\myfolder

Un archivo de respuesta denominado `options.txt` que especifica las opciones de AzCopy:

    /S /Y

Para llamar a AzCopy con estos archivos de respuesta, que residen en un directorio `C:\responsefiles`, use este comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy procesa este comando tal como lo haría si incluye todos los parámetros individuales en la línea de comandos:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Especificar una firma de acceso compartido (SA)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

También puede especificar una SA en el contenedor URI:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Carpeta de archivos de diario

Cada vez que se emite un comando para AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada, o bien, si existe en una carpeta que especifica mediante esta opción. Si el archivo de diario no existe en cualquier lugar, AzCopy trata la operación como nueva y genera un nuevo archivo de diario.

Si existe el archivo de diario, AzCopy comprueba si la línea de comandos que escribe coincide con la línea de comandos en el archivo de diario. Si coinciden con las líneas de comandos dos, AzCopy reanuda la operación incompleta. Si no coinciden, se le pedirá que lo bien sobrescribir el archivo de diario para iniciar una nueva operación o cancelar la operación actual.

Si desea usar la ubicación predeterminada para el archivo de diario:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Si se omite la opción `/Z`, o especificar opción `/Z` sin la ruta de acceso, como se muestra arriba, AzCopy crea el archivo de diario en la ubicación predeterminada, que es `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si ya existe el archivo de diario, AzCopy reanuda la operación basada en el archivo de diario.

Si desea especificar una ubicación personalizada para el archivo de diario:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Este ejemplo crea el archivo de diario si aún no existe. Si existe, AzCopy reanuda la operación basada en el archivo de diario.

Si desea reanudar una operación de AzCopy:

    AzCopy /Z:C:\journalfolder\

En este ejemplo se reanuda la última operación, que es posible que no han terminado.

### <a name="generate-a-log-file"></a>Generar un archivo de registro

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Si especifica la opción `/V` sin proporcionar una ruta de acceso de archivo para el registro detallado, a continuación, AzCopy crea el archivo de registro en la ubicación predeterminada, que es `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

En caso contrario, puede crear un archivo de registro en una ubicación personalizada:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Tenga en cuenta que si especifica una ruta de acceso relativa siguiendo opción `/V`, como por ejemplo `/V:test/azcopy1.log`, se crea el registro detallado en el directorio de trabajo actual dentro de una subcarpeta llamada `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificar el número de operaciones simultáneas para iniciar

Opción `/NC` especifica el número de operaciones de copia simultánea. De forma predeterminada, AzCopy inicia un determinado número de operaciones simultáneas para mejorar el rendimiento de transferencia de datos. Para operaciones de tabla, el número de operaciones simultáneas es igual que el número de procesadores que tiene. Para operaciones de archivos y Blob, el número de operaciones simultáneas es igual a 8 veces el número de procesadores que tiene. Si está ejecutando AzCopy a través de una red de ancho de banda bajo, puede especificar un número menor para/NC evitar errores causados por competencia de recursos.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Ejecutar AzCopy en emulador de almacenamiento de Azure

Puede ejecutar AzCopy en el [Emulador de Azure almacenamiento](storage-use-emulator.md) de Blobs:

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

y tablas:

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>Parámetros de AzCopy

A continuación, se describen los parámetros para AzCopy. También puede escribir uno de los siguientes comandos de la línea de comandos para obtener ayuda en el uso de AzCopy:

- Para obtener ayuda detallada de línea de comandos para AzCopy:`AzCopy /?`
- Para obtener ayuda detallada con cualquier parámetro AzCopy:`AzCopy /?:SourceKey`
- Para obtener ejemplos de línea de comandos:`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Origen: "origen"

Especifica el origen de datos desde el que desea copiar. El origen puede ser un directorio del sistema de archivos, un contenedor de blob, un directorio virtual de blobs, un recurso compartido de archivos de almacenamiento, un directorio de archivo de almacenamiento o una tabla de Azure.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="destdestination"></a>/ Dest: "destino"

Especifica el destino para copiar a. El destino puede ser un directorio del sistema de archivos, un contenedor de blob, un directorio virtual de blobs, un recurso compartido de archivos de almacenamiento, un directorio de archivo de almacenamiento o una tabla de Azure.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="patternfile-pattern"></a>/ Patrón: "patrón de archivo"

Especifica un modelo de archivo que indica qué archivos copiar. El comportamiento del parámetro /Pattern depende de la ubicación del origen de datos y a continuación, la presencia de la opción de modo recursiva. Modo recursivo se especifica mediante la opción/el s.

Si el origen especificado es un directorio del sistema de archivos, a continuación, los caracteres comodín estándar está en vigor y patrón de archivo se compara con archivos en el directorio. Si la opción que se especifica /, a continuación, AzCopy también coincide con el modelo especificado en todos los archivos de todas las subcarpetas debajo del directorio.

Si el origen especificado es un contenedor de blob o directorio virtual, no se aplica caracteres comodín. Si la opción que se especifica/s., AzCopy, a continuación, interpreta el patrón de archivo especificado como un prefijo de blobs de Windows. Si la opción que no se especifica /, a continuación, AzCopy coincide con el patrón de archivos con los nombres de blobs exacta.

Si el origen especificado es un recurso compartido de archivos de Azure, debe especificar el nombre de archivo exacto, (por ejemplo, abc.txt) para copiar un solo archivo, o especifique la opción /S para copiar todos los archivos en el recurso compartido de forma recursiva. Al intentar especificar un patrón de archivo y la opción /S juntos se producirá un error.

AzCopy utiliza la concordancia entre mayúsculas y minúsculas cuando la /Source es un contenedor de blob o directorio virtual de blobs de Windows y utiliza la concordancia entre mayúsculas y minúsculas en todos los demás casos.

El modelo de archivo predeterminado utilizado cuando no se especifica ningún patrón de archivo es *.* para una ubicación de sistema de archivos o un prefijo vacío para una ubicación de almacenamiento de Azure. No se admite la especificación de varios patrones de archivo.

**Aplicables a:** BLOB, archivos

### <a name="destkeystorage-key"></a>/ DestKey: "clave de almacenamiento"

Especifica la clave de cuenta de almacenamiento para el recurso de destino.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="destsassas-token"></a>/ DestSAS: "SA token"

Especifica una firma de acceso compartido (SA) con permisos de lectura y escritura para el destino (si procede). Alrededor de las asociaciones de seguridad con comillas dobles, ya que es posible contiene caracteres de línea de comandos especiales.

Si el recurso de destino es un contenedor de blob, uso compartido de archivos o una tabla, puede especificar esta opción seguida del símbolo de sa o puede especificar las asociaciones de seguridad como parte del contenedor de blob de destino, uso compartido de archivos o URI de la tabla, sin esta opción.

Si el origen y destino son ambos BLOB, blob de destino debe residen en la misma cuenta de almacenamiento que el blob de origen.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="sourcekeystorage-key"></a>/ SourceKey: "clave de almacenamiento"

Especifica la clave de cuenta de almacenamiento para el recurso de origen.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="sourcesassas-token"></a>/ SourceSAS: "SA token"

Especifica una firma de acceso compartido con permisos de lectura y la lista para el origen (si procede). Alrededor de las asociaciones de seguridad con comillas dobles, ya que es posible contiene caracteres de línea de comandos especiales.

Si el recurso de origen es un contenedor de blob, y se proporciona una clave ni un SA, se leerá el contenedor de blob a través de acceso anónimo.

Si el origen es un recurso compartido de archivos o una tabla, se debe proporcionar una clave o una SA.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="s"></a>/S

Especifica el modo recursivo para operaciones de copia. En el modo recursivo, AzCopy copiará todos los BLOB o archivos que coincidan con el patrón de archivo especificado, las subcarpetas incluidas.

**Aplicables a:** BLOB, archivos

### <a name="blobtypeblock--page--append"></a>/ BlobType: "bloque" | "página" | "anexar"

Especifica si el blob de destino es un blob bloque, un blob de página o un blob de datos anexados. Esta opción es aplicable solo cuando se carga un blob. En caso contrario, se genera un error. Si el destino es un blob y no se especifica esta opción, de forma predeterminada, AzCopy crea un blob de bloque.

**Aplicables a:** BLOB

### <a name="checkmd5"></a>/ CheckMD5

Calcula un hash MD5 para datos descargados y comprueba que el hash MD5 almacenados en el blob o propiedad de MD5 de contenido del archivo coincide con el hash calculado. La comprobación de MD5 está desactivada de forma predeterminada, lo que debe especificar esta opción para realizar la comprobación de MD5 al descargar los datos.

Observe que el almacenamiento de Azure no garantiza que el hash MD5 almacenado para el archivo o blob esté actualizado. Es responsabilidad del cliente para actualizar el MD5 siempre que se modifica el archivo o blob.

AzCopy siempre se establece la propiedad de MD5 de contenido de un archivo o blobs de Windows Azure después de cargar el servicio.  

**Aplicables a:** BLOB, archivos

### <a name="snapshot"></a>O instantánea

Indica si desea transferir instantáneas. Esta opción solo es válida cuando el origen es un blob.

Se cambia el nombre de las instantáneas de blobs transferido en este formato: .extension nombre blob (tiempo de instantánea)

De forma predeterminada, las instantáneas no se copian.

**Aplicables a:** BLOB

### <a name="vverbose-log-file"></a>/ V: [detallado-archivo de registro]

Mensajes de estado detallados de resultados en un archivo de registro.

De forma predeterminada, el archivo de registro detallado se denomina AzCopyVerbose.log en `%LocalAppData%\Microsoft\Azure\AzCopy`. Si especifica una ubicación de archivo existente para esta opción, se agregará el registro detallado a ese archivo.  

**Aplicables a:** Tablas de blobs, archivos,

### <a name="zjournal-file-folder"></a>/ Z: [carpeta de archivos de diario]

Especifica una carpeta de archivos de diario para reanudar una operación.

AzCopy siempre admite reanudar si se ha interrumpido una operación.

Si no se especifica esta opción, o se ha especificado sin una ruta de carpeta, AzCopy creará el archivo de diario en la ubicación predeterminada, que es % LocalAppData%\Microsoft\Azure\AzCopy.

Cada vez que se emite un comando para AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada, o bien, si existe en una carpeta que especifica mediante esta opción. Si el archivo de diario no existe en cualquier lugar, AzCopy trata la operación como nueva y genera un nuevo archivo de diario.

Si existe el archivo de diario, AzCopy comprueba si la línea de comandos que escribe coincide con la línea de comandos en el archivo de diario. Si coinciden con las líneas de comandos dos, AzCopy reanuda la operación incompleta. Si no coinciden, se le pedirá que lo bien sobrescribir el archivo de diario para iniciar una nueva operación o cancelar la operación actual.

Cuando finalice la operación, se elimina el archivo de diario.

Tenga en cuenta que no se admite reanudar una operación de un archivo de diario creado por una versión anterior de AzCopy.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="parameter-file"></a>/@:"parameter-file"

Especifica un archivo que contiene parámetros. AzCopy procesa los parámetros en el archivo como si hubiera especificado en la línea de comandos.

En un archivo de respuesta, puede especificar varios parámetros en una sola línea o especifique cada parámetro en su propia línea. Tenga en cuenta que un parámetro individual no puede abarcar varias líneas.

Archivos de respuesta pueden incluir las líneas de comentarios que comienzan con el símbolo #.

Puede especificar varios archivos de respuesta. Sin embargo, tenga en cuenta que AzCopy no admite archivos de respuesta anidados.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="y"></a>/ Y

Suprime todos los mensajes de confirmación de AzCopy.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="l"></a>/ L

Especifica una operación de anuncio no se copian datos.

AzCopy interpretará el uso de esta opción como una simulación para ejecutar la línea de comandos sin esta opción/l y contar el número de objetos se copiarán, puede especificar la opción /V al mismo tiempo para comprobar qué objetos se copiarán en el registro detallado.

El comportamiento de esta opción también depende de la ubicación del origen de datos y la presencia de la recursiva modo /S y archivo de trama opción /Pattern.

AzCopy requiere permiso de lectura y la lista de esta ubicación de origen al usar esta opción.

**Aplicables a:** BLOB, archivos

### <a name="mt"></a>/ MT

Establece la hora de última modificación del archivo descargado a ser igual que el blob de origen o del archivo.

**Aplicables a:** BLOB, archivos

### <a name="xn"></a>/XN

Excluye un recurso de código fuente más reciente. No se copiarán los recursos si la hora de última modificación del origen es el mismo o más recientes que destino.

**Aplicables a:** BLOB, archivos

### <a name="xo"></a>/XO

Excluye un recurso de origen anterior. No se copiarán los recursos si la hora de última modificación del origen es el mismo o anteriores al destino.

**Aplicables a:** BLOB, archivos

### <a name="a"></a>/A

Carga sólo los archivos que tienen el atributo modificado.

**Aplicables a:** BLOB, archivos

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Carga sólo los archivos que tienen el conjunto de atributos especificado.

Incluyen atributos disponibles:

- R = archivos de solo lectura
- A = archivos listos para archivar
- S = los archivos del sistema
- H = archivos ocultos
- C = archivos comprimidos
- N = archivos Normal
- E = los archivos de cifrado
- T = archivos temporales
- O = archivos sin conexión
- I = archivos no indizados

**Aplicables a:** BLOB, archivos

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Excluye los archivos que tienen el conjunto de atributos especificado.

Incluyen atributos disponibles:

- R = archivos de solo lectura
- A = archivos listos para archivar
- S = los archivos del sistema
- H = archivos ocultos
- C = archivos comprimidos
- N = archivos Normal
- E = los archivos de cifrado
- T = archivos temporales
- O = archivos sin conexión
- I = archivos no indizados

**Aplicables a:** BLOB, archivos

### <a name="delimiterdelimiter"></a>/ Delimitador: "delimitador"

Indica el carácter delimitador utilizado para delimitar directorios virtuales en un nombre de blobs de Windows.

De forma predeterminada, AzCopy utiliza / como carácter delimitador. Sin embargo, AzCopy admite el uso de cualquier carácter comunes (como @, # o %) como un delimitador. Si debe incluir uno de estos caracteres especiales en la línea de comandos, escriba el nombre de archivo entre comillas dobles.

Esta opción solo es aplicable para descargar BLOB.

**Aplicables a:** BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "número-de-simultánea-operaciones"

Especifica el número de operaciones simultáneas.

AzCopy de forma predeterminada inicia un determinado número de operaciones simultáneas para mejorar el rendimiento de transferencia de datos. Tenga en cuenta que gran cantidad de operaciones simultáneas en un entorno de bajo ancho de banda puede saturar la conexión de red y evitar las operaciones de haber completado. Limitar las operaciones simultáneas basadas en ancho de banda de red disponible real.

El límite superior para operaciones simultáneas es 512.

**Aplicables a:** Tablas de blobs, archivos,

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Tabla"

Especifica que el `source` recurso es un disponibles en el entorno de desarrollo local que se ejecuta en el emulador de almacenamiento de blobs de Windows.

**Aplicables a:** Blobs, tablas

### <a name="desttypeblob--table"></a>/ TipoDest: "Blob" | "Tabla"

Especifica que el `destination` recurso es un disponibles en el entorno de desarrollo local que se ejecuta en el emulador de almacenamiento de blobs de Windows.

**Aplicables a:** Blobs, tablas

### <a name="pkrskey1key2key3"></a>/ PKRS: "clave1 clave2 # key3 #..."

Divide el intervalo de clave de partición para habilitar la exportación de datos de tabla en paralelo, lo que aumenta la velocidad de la operación de exportación.

Si no se especifica esta opción, AzCopy utiliza un subproceso único para exportar entidades de tabla. Por ejemplo, si el usuario especifica /PKRS: "aa #bb" y, a continuación, AzCopy inicia tres operaciones simultáneas.

Cada operación exporta a uno de los tres intervalos de clave de partición, tal como se muestra a continuación:

  [clave de partición primero, aa)

  [bb aa)

  [bb clave de partición última]

**Aplicables a:** Tablas

### <a name="splitsizefile-size"></a>/ SplitSize: "tamaño de archivo"

Especifica el archivo exportado dividir tamaño en MB, el valor mínimo permitido es 32.

Si no se especifica esta opción, AzCopy se exportar datos de una tabla a un solo archivo.

Si se exportan los datos de la tabla a un blob y el tamaño del archivo exportado alcanza el límite de 200 GB para el tamaño de blob, AzCopy dividir el archivo exportado, incluso si no se especifica esta opción.

**Aplicables a:** Tablas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica el comportamiento de importación de datos de tabla.

- InsertOrSkip - omite una entidad existente o se inserta una nueva entidad si no existe en la tabla.

- InsertOrMerge - combina una entidad existente o se inserta una nueva entidad si no existe en la tabla.

- InsertOrReplace - se reemplaza una entidad existente o se inserta una nueva entidad si no existe en la tabla.

**Aplicables a:** Tablas

### <a name="manifestmanifest-file"></a>/ Manifiestos: "archivo de manifiesto"

Especifica el archivo manifiesto en Exportar la tabla y la operación de importación.

Esta opción es opcional durante la operación de exportación, AzCopy generará un archivo manifiesto con nombre predefinido si esta opción no se especifica.

Esta opción es necesaria durante la operación de importación para ubicar los archivos de datos.

**Aplicables a:** Tablas

### <a name="synccopy"></a>/ SyncCopy

Indica si se va a copiar sincrónicamente BLOB o archivos entre dos puntos finales de almacenamiento de Azure.

AzCopy predeterminada usa copia asincrónica de servidor. Especifique esta opción para realizar una copia sincrónica, que se descarga BLOB o archivos en la memoria local y, a continuación, se cargarán en el almacenamiento de Azure.

Puede usar esta opción al copiar archivos de almacenamiento de blobs, almacenamiento de archivos o desde el almacenamiento de blobs de almacenamiento de archivos, o viceversa.

**Aplicables a:** BLOB, archivos

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "tipo de contenido"

Especifica el tipo de contenido MIME para archivos o BLOB de destino.

AzCopy establece el tipo de contenido de un archivo o blob en aplicación/octet-stream de forma predeterminada. Puede establecer el tipo de contenido para todos los archivos o BLOB especificando explícitamente un valor para esta opción.

Si especifica esta opción sin un valor, AzCopy establecerá cada blob o tipo de contenido del archivo según su extensión de archivo.

**Aplicables a:** BLOB, archivos

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Especifica el formato del archivo de datos exportados de tabla.

Si no se especifica esta opción, de forma predeterminada, AzCopy exporta el archivo de datos de tabla en formato JSON.

**Aplicables a:** Tablas

## <a name="known-issues-and-best-practices"></a>Problemas conocidos y los procedimientos recomendados

### <a name="limit-concurrent-writes-while-copying-data"></a>Límite de escritura simultánea al copiar datos

Al copiar BLOB o archivos con AzCopy, tenga en cuenta que otra aplicación puede modificar los datos mientras se está copiando. Si es posible, asegúrese de que los datos que se va a copiar no se modifique durante la operación de copia. Por ejemplo, al copiar un disco duro virtual asociado con un equipo virtual Azure, asegúrese de que ninguna otra aplicación actualmente está escribiendo en el disco duro virtual. Una buena manera de hacerlo es mediante la concesión de los recursos para copiar. Como alternativa, puede crear una instantánea del disco duro virtual en primer lugar y, a continuación, copie la instantánea.

Si no puede impedir que otras aplicaciones de escritura que BLOB o archivos mientras se copian, a continuación, tener en cuenta que en el momento en que el trabajo finaliza, los recursos copiados pueden ya no paridad completa con los recursos de origen.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Ejecutar una instancia de AzCopy en un equipo.
AzCopy está diseñado para maximizar la utilización de los recursos de equipo para acelerar la transferencia de datos, se recomienda ejecutar sólo una instancia de AzCopy en un equipo y especifique la opción `/NC` si necesita operaciones más simultáneas. Para obtener más detalles, escriba `AzCopy /?:NC` en la línea de comandos.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Habilitar algoritmos MD5 compatibles de FIPS para AzCopy cuando se "usar FIPS algoritmos compatibles para el cifrado, firma y operaciones hash".
AzCopy predeterminada usa implementación .NET MD5 para calcular el MD5 al copiar los objetos, pero hay algunos requisitos de seguridad que necesitan AzCopy para habilitar la configuración de MD5 compatible con FIPS.

Puede crear un archivo app.config `AzCopy.exe.config` con propiedad `AzureStorageUseV1MD5` y colóquelo separada con AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

• Propiedad "AzureStorageUseV1MD5" True - el valor predeterminado, AzCopy utilizará implementación de .NET MD5.
• False: AzCopy utilizará el algoritmo de MD5 compatible con FIPS.

Tenga en cuenta que algoritmos compatibles FIPS está deshabilitado de forma predeterminada en el equipo de Windows, puede escribir secpol.msc en la ventana Ejecutar y comprobar este modificador en configuración de seguridad -> directivas locales -> seguridad Opciones -> codificación de sistema: usar compatible con algoritmos FIPS para cifrado, firma y operaciones hash.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el almacenamiento de Azure y AzCopy, consulte los siguientes recursos.

### <a name="azure-storage-documentation"></a>Documentación de Azure almacenamiento:

- [Introducción al almacenamiento de Azure](storage-introduction.md)
- [Cómo usar el almacenamiento de blobs de .NET](storage-dotnet-how-to-use-blobs.md)
- [Cómo usar el almacenamiento de archivos de .NET](storage-dotnet-how-to-use-files.md)
- [Cómo usar el almacenamiento de tablas de .NET](storage-dotnet-how-to-use-tables.md)
- [Cómo crear, administrar o eliminar una cuenta de almacenamiento](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Entradas de blog de Azure almacenamiento:
- [Introducción a vista previa biblioteca de movimiento de datos de almacenamiento de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Introducción copia sincrónico y tipo de contenido personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Anuncio de disponibilidad General de AzCopy 3.0 Además de la versión preliminar de AzCopy 4.0 con soporte técnico de tabla y archivo](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Optimizado para escenarios de copia a gran escala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: Compatibilidad almacenamiento geo redundantes de acceso de lectura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Transferir datos con el modo de volver a iniciar y el símbolo de SA](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Usar cuenta entre copiar Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Cargar o descargar archivos para Blobs de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
