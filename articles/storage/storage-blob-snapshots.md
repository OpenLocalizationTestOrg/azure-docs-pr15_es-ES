<properties
    pageTitle="Crear una instantánea de sólo lectura de un blob | Microsoft Azure"
    description="Obtenga información sobre cómo crear una instantánea de un blob para copiar los datos de blob en un momento determinado. Comprender cómo se facturación instantáneas y cómo usarlos para minimizar los gastos de capacidad."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="create-a-blob-snapshot"></a>Crear una instantánea de blobs

## <a name="overview"></a>Información general

Una instantánea es una versión de solo lectura de un objeto binario que se toma en un punto en el tiempo. Las instantáneas son útiles para realizar copias de seguridad BLOB. Después de crear una instantánea, puede leer, copiar o eliminar, pero no puede modificarlo.

Una instantánea de un blob es idéntica a su base blob, excepto que el blob URI tiene un valor de **DateTime** que se anexa a los blobs URI para indicar el momento en que se realizó la instantánea. Por ejemplo, si una página blob URI es `http://storagesample.core.blob.windows.net/mydrives/myvhd`, la instantánea URI es similar a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Todas las instantáneas compartan URI de blobs base. La única distinción entre el blob base y la instantánea es el valor de **DateTime** anexado.

Un blob puede tener cualquier número de instantáneas. Instantáneas se conservan hasta que se eliminen explícitamente. Una instantánea no puede sobrevivir a su base blob. Puede enumerar las instantáneas asociadas con el objeto blob base para realizar un seguimiento de sus instantáneas actuales.

Cuando se crea una instantánea de un blob, propiedades del sistema del blob se copian a la instantánea con los mismos valores. Metadatos de blobs base también se copian en la instantánea, a menos que especifique metadatos independiente para la instantánea cuando lo cree.

Los permisos asociados con el objeto blob base no afectará a la instantánea. No se puede adquirir un contrato en una instantánea.

## <a name="create-a-snapshot"></a>Crear una instantánea

En el ejemplo siguiente se muestra cómo crear una instantánea en .NET. Este ejemplo especifica metadatos independiente para la instantánea cuando se crea.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Copiar instantáneas

Operaciones de copia con BLOB e instantáneas siguen estas reglas:

- Puede copiar una instantánea sobre sus blobs base. Al promover una instantánea hasta la posición de la base blob, puede restaurar una versión anterior de un blob. El permanece instantánea, pero la base blob se sobrescribe con una copia de escritura de la instantánea.

- Puede copiar una instantánea en un blob de destino con un nombre diferente. El blob de destino resultante es un blob grabable y no una instantánea.

- Cuando se copia un blob de origen, cualquier instantáneas de blobs de origen no se copian al destino. Cuando se sobrescribe un blob de destino con una copia, instantáneas asociadas con el blob de destino original permanecen intactas.

- Cuando se crea una instantánea de un blob bloque, la lista de bloques confirmada del blob también se copia en la instantánea. No se copian los bloques sin confirmar.

## <a name="specify-an-access-condition"></a>Especificar una condición de access

Puede especificar una condición de access para que se crea la instantánea solo si se cumple una condición. Para especificar una condición de access, use la propiedad **AccessCondition** . Si no se cumple la condición especificada, no se crea la instantánea y el servicio de blobs devuelve el código de estado HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Eliminar instantáneas

No puede eliminar un blob con instantáneas a menos que también se eliminan las instantáneas. Puede eliminar una instantánea de forma individual o especificar que se eliminen todas las instantáneas cuando se elimina el blob de origen. Si intenta eliminar un blob que aún tiene instantáneas, se produce un error.

En el ejemplo siguiente se muestra cómo eliminar un blob y sus instantáneas en. NET, donde `blockBlob` es una variable de tipo **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Instantáneas con el almacenamiento de Azure Premium

Uso de instantáneas con seguimiento de almacenamiento Premium estas reglas:

- El número máximo de instantáneas por blobs de página en una cuenta de almacenamiento premium es 100. Si se supera este límite, la operación de instantánea Blob devuelve el código de error 409 (**SnapshotCountExceeded**).

- Puede tomar una instantánea de un blob de página en una cuenta de almacenamiento premium una vez cada 10 minutos. Si se excede la tasa, la operación de instantánea Blob devuelve el código de error 409 (**SnaphotOperationRateExceeded**).

- No puede llamar a obtener Blob para leer una instantánea de un blob de página en una cuenta de almacenamiento premium. Llamar a obtener Blob en una instantánea en una cuenta de almacenamiento premium devuelve el código de error 400 (**InvalidOperation**). Sin embargo, puede llamar a obtener propiedades de blobs y obtener metadatos de blobs frente a una instantánea en una cuenta de almacenamiento premium.

- Para leer una instantánea, puede usar la operación de blobs de copiar para copiar una instantánea en otro blobs de página en la cuenta. El blob de destino para la operación de copia no puede tener cualquier instantáneas existentes. Si el blob de destino tiene instantáneas, la operación de copia Blob devuelve el código de error 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Devolver el URI absoluto a una instantánea

Este ejemplo de código C# crea una instantánea y escribe el URI absoluto para la ubicación principal.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Comprender la acumulación de instantáneas de cargos

Crear una instantánea, que es una copia de sólo lectura de un blob, puede dar lugar a gastos adicionales de almacenamiento a su cuenta. Al diseñar la aplicación, es importante tener en cuenta cómo pueden acumular estos cargos por lo que puede minimizar costos innecesarios.

### <a name="important-billing-considerations"></a>Consideraciones de facturación

La siguiente lista incluye puntos clave para tener en cuenta al crear una instantánea.

- Cuenta de almacenamiento incurre cargos para bloques únicos o páginas, independientemente de si están en el objeto binario o en la instantánea. Su cuenta no incurre en gastos adicionales para instantáneas asociadas con un blob hasta que actualice el blob en la que se basan. Después de actualizar el blob base, diverge desde sus instantáneas. En este caso, se cargan para las páginas en cada blob o una instantánea o bloques únicos.

- Cuando se reemplaza un bloque dentro de un blob bloque, ese bloque se cargará posteriormente como un único bloque. Esto es verdadero, incluso si el bloqueo tiene el mismo identificador de bloque y los mismos datos que en la instantánea. Después de que se confirma el bloque, difiere de su equivalente en cualquier instantánea y se cargará para sus datos. Lo mismo sucede para una página en un blob de página que se actualiza con datos idénticos.

- Reemplazar un blob bloque llamando al método **UploadFile**, **UploadText**, **UploadStream**o **UploadByteArray** reemplazan todos los bloques en el blob. Si tiene una instantánea asociada con blob, todos los bloques de la base blob e instantánea ahora difieren y se cargará todos los bloques en ambos BLOB. Esto es verdadero, incluso si los datos de la base blob y la instantánea permanecen idénticos.

- El servicio de blobs de Windows Azure no tiene un medio para determinar si dos bloques contienen datos idénticos. Cada bloque que se cargan y confirmada se trata como único, incluso si tiene los mismos datos y el mismo identificador de bloque. Dado que acumulan cargos de bloques únicos, es importante tener en cuenta actualizar de blob que contiene los resultados de una instantánea en bloques únicos adicionales y cargos adicionales.

> [AZURE.NOTE] Procedimientos recomendados indican que administrar instantáneas cuidadosamente para evitar cargos adicionales. Se recomienda administrar instantáneas de la siguiente manera:

> - Eliminar y volver a crear instantáneas asociadas a un blob siempre que actualice el blob, incluso si está actualizando con datos idénticos, a menos que el diseño de la aplicación es necesario mantener instantáneas. Al eliminar y volver a crear instantáneas de blobs de Windows, puede asegurarse de que el objeto binario e instantáneas no difieren.

> - Si está manteniendo instantáneas para un blob, evitar la llamada a **UploadFile**, **UploadText**, **UploadStream**o **UploadByteArray** para actualizar el blob. Estos métodos reemplazan todos los bloques en el blob, para que su base blob e instantáneas difieren considerablemente. En su lugar, actualice el menor número posible de bloques mediante los métodos **PutBlock** y **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Instantánea escenarios de facturación


Los siguientes escenarios demuestran acumulación de gastos para un blob de bloque y sus instantáneas.

En el escenario 1, el blob base no se actualizó después de que se realizó la instantánea, por lo que se incurre cargos solo para bloques únicos 1, 2 y 3.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

En el escenario 2, se ha actualizado el blob base, pero la instantánea no tiene. Bloque 3 se actualizó y aunque contiene los mismos datos y el mismo ID, no es el mismo bloque 3 en la instantánea. Como resultado, la cuenta se cargará de bloques de cuatro.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

En el escenario 3, se ha actualizado el blob base, pero la instantánea no tiene. Bloque 3 se reemplazó con bloque 4 del BLOB base, pero la instantánea todavía refleja bloque 3. Como resultado, la cuenta se cargará de bloques de cuatro.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

En el escenario 4, el blob base se ha actualizado completamente y no contiene ninguno de sus bloques originales. Por tanto, se cargará la cuenta para todos los bloques únicos ocho. Este escenario puede ocurrir si está utilizando un método de actualización como **UploadFile**, **UploadText**, **UploadFromStream**o **UploadByteArray**, porque estos métodos reemplazan todo el contenido de un blob.

![Recursos de almacenamiento de Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de uso de almacenamiento de blobs, vea [Ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Puede descargar una aplicación de ejemplo y ejecutarla o examinar el código en GitHub. 
