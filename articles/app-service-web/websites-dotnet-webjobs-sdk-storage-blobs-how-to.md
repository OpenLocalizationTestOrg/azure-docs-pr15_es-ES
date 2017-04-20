<properties 
    pageTitle="Cómo usar el almacenamiento de blobs de Windows Azure con el SDK WebJobs" 
    description="Obtenga información sobre cómo usar el almacenamiento de blobs de Windows Azure con el SDK de WebJobs. Desencadenar un proceso cuando aparezca un blob nuevo en un contenedor y controlar 'dudoso BLOB'." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Cómo usar el almacenamiento de blobs de Windows Azure con el SDK WebJobs

## <a name="overview"></a>Información general

Esta guía proporciona ejemplos de código de C# que muestran cómo desencadenar un proceso cuando se crea o actualiza un blobs de Windows Azure. Los ejemplos de código utilizan [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versión 1.x.

Para obtener ejemplos de código que muestran cómo crear BLOB, vea [cómo usar el almacenamiento de Azure cola con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
La guía se supone que sabe [cómo crear un proyecto WebJob en Visual Studio con las cadenas de conexión que apunten a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md) o a [varias cuentas de almacenamiento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Cómo activar una función cuando se crea o actualiza un blob

Esta sección muestra cómo usar la `BlobTrigger` atributo. 

> [AZURE.NOTE] El SDK WebJobs analiza los archivos de registro para ver para blobs nuevas o modificadas. Este proceso no está en tiempo real; una función podría no volverse hasta varios minutos o más después de crea el blob. Además, base de [almacenamiento registros se crean en "esfuerzos"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; No hay ninguna garantía de todos los eventos que se va a capturar. En algunas condiciones, es posible que perdido registros. Si las limitaciones de velocidad y confiabilidad de desencadenadores de blob no son aceptables para su aplicación, es el método recomendado crear un mensaje de cola al crear el blob y utilizar el atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) en lugar de la `BlobTrigger` atributo en la función que procesa el blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Marcador de posición único nombre blob con extensión  

En el ejemplo de código siguiente copia BLOB de texto que aparecen en el contenedor de *entrada* al contenedor de *salida* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

El constructor de atributos tiene un parámetro de cadena que especifica el nombre del contenedor y un marcador de posición para el nombre de blobs de Windows. En este ejemplo, si se crea un blob llamado *Blob1.txt* en el contenedor de *entrada* , la función crea un blob llamado *Blob1.txt* en el contenedor de *salida* . 

Puede especificar un patrón de nombre con el marcador de posición del nombre de blobs, como se muestra en el ejemplo siguiente:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Este código copia solo BLOB que tienen nombres que empiecen por "original-". Por ejemplo, *Blob1.txt original* en el contenedor de *entrada* se copia en *Copia Blob1.txt* en el contenedor de *salida* .

Si necesita especificar un patrón de nombre para los nombres de blobs de Windows que tiene llaves en el nombre, duplicar las llaves. Por ejemplo, si desea buscar BLOB en el contenedor de *imágenes* que tengan nombres similar a esta:

        {20140101}-soundfile.mp3

Use esta opción para el diseño:

        images/{{20140101}}-{name}

En el ejemplo, el valor de *nombre* de marcador de posición sería *soundfile.mp3*. 

### <a name="separate-blob-name-and-extension-placeholders"></a>Marcadores de posición de nombre y la extensión de blob independiente

El ejemplo siguiente cambia la extensión de archivo como copia BLOB que aparecen en el contenedor de *entrada* al contenedor de *salida* . El código registra la extensión del objeto binario de *entrada* y establece la extensión de blobs de *salida* a *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a id="types"></a>Tipos de que se pueden enlazar con BLOB

Puede usar el `BlobTrigger` atributo en los siguientes tipos:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Otros tipos de deserializar [ICloudBlobStreamBinder](#icbsb) 

Si desea trabajar directamente con la cuenta de almacenamiento de Azure, también puede agregar un `CloudStorageAccount` parámetro de la firma del método.

Para obtener ejemplos, vea el [código de enlace de blobs de Windows en el repositorio de sdk de webjobs de azure en GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Obtener el contenido de blobs de texto mediante un enlace a cadena

Si se espera BLOB de texto, `BlobTrigger` se puede aplicar a un `string` parámetro. El ejemplo siguiente enlaza un blob de texto a un `string` parámetro denominado `logMessage`. La función usa dicho parámetro para escribir el contenido del objeto binario en el panel de WebJobs SDK. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Obtener serie blob contenido usando ICloudBlobStreamBinder

El ejemplo siguiente utiliza una clase que implementa `ICloudBlobStreamBinder` para habilitar la `BlobTrigger` atributo enlazar un blob a la `WebImage` tipo.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK", 
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

La `WebImage` código de enlace está disponible en un `WebImageBinder` clase que se deriva de `ICloudBlobStreamBinder`.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input, 
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Obtener la ruta de acceso de blobs para la activación blob

Para obtener el nombre del contenedor y el nombre de blobs de blobs de Windows que ha activado la función, incluya un `blobTrigger` parámetro en la firma de la función de cadena.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>Cómo manejar dudoso BLOB

Cuando un `BlobTrigger` función correctamente, el SDK llama de nuevo, en caso de que el error se produjo por un error temporal. Si el error se produce por el contenido del objeto binario, se produce un error en la función cada vez intenta procesar el blob. De forma predeterminada, el SDK llama a una función hasta 5 veces para un blob determinado. Si se produce un error en la quinta try, el SDK agrega un mensaje a una cola denominada *webjobs-blobtrigger-toxicología*.

El número máximo de reintentos es configurable. La misma configuración [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) se usa para control de mensajes de cola dudoso y tratamiento de blobs dudoso. 

El mensaje de cola para blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *{nombre WebJob}*. Funciones. *{Nombre de la función}*, por ejemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* Nombre del contenedor
* BlobName
* ETag (un identificador de versión de blobs de Windows, por ejemplo: "0x8D1DC6E70A277EF")

En el ejemplo siguiente, la `CopyBlob` función tiene código que hace que un error cada vez que se llama. Después de que el SDK lo llama para el número máximo de reintentos, se crea un mensaje en la cola blob dudoso y ese mensaje es procesado por la `LogPoisonBlob` función. 

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }
        
        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

El SDK deserializa automáticamente el mensaje JSON. Esto es el `PoisonBlobMessage` clase: 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>Algoritmo de sondeo BLOB

El SDK WebJobs analiza todos los contenedores especificados por `BlobTrigger` atributos al iniciar la aplicación. En una cuenta de almacenamiento grandes este análisis pueden tardar algún tiempo, por lo que puede ser un poco antes de que se encuentra nuevas BLOB y `BlobTrigger` funciones se ejecutan.

Para detectar nueva o modificada BLOB después de iniciar la aplicación, el SDK lee periódicamente los registros de almacenamiento de blobs de Windows. Los registros de blob se almacenan en búfer y solo se escriben física cada 10 minutos o por lo tanto, así que puede haber retraso significativo después de un blob se crea o actualiza antes el correspondiente `BlobTrigger` se ejecuta la función. 

Hay una excepción para blobs que se crean mediante la `Blob` atributo. Cuando el SDK WebJobs crea un nuevo blob, pasa el nuevo blob inmediatamente a cualquier coincidentes `BlobTrigger` funciones. Por lo tanto, si tiene una cadena de blobs entradas y los resultados, el SDK puede procesar ellos eficazmente. Pero si desea ejecutar el blob funciones de procesamiento de BLOB que se hayan creado o actualizado por otros medios de latencia baja, te recomendamos que uses `QueueTrigger` en lugar de `BlobTrigger`.

### <a id="receipts"></a>Confirmaciones de blobs

El SDK WebJobs se asegura de que no `BlobTrigger` función se llama varias veces para el mismo blob nuevo o actualizado. Para ello, mantener *confirmaciones de blobs* para determinar si se ha procesado una versión blob determinado.

Confirmaciones de BLOB se almacenan en un contenedor denominado *hosts de webjobs de azure* en la cuenta de almacenamiento de Azure especificada por la cadena de conexión AzureWebJobsStorage. Una confirmación de blobs tiene la información siguiente:

* La función que se denominaba para el blob ("*{nombre WebJob}*. Funciones. *{Nombre de la función}*", por ejemplo:"WebJob1.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre de blob
* ETag (un identificador de versión de blobs de Windows, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar volver a procesar de un blob, puede eliminar manualmente la confirmación de blobs para blob desde el contenedor de *hosts de webjobs de azure* .

## <a id="queues"></a>Temas relacionados cubiertos por el artículo colas

Para obtener información sobre cómo controlar el procesamiento de blobs activado por un mensaje de cola o para WebJobs SDK escenarios no es específicos de manchas procesamiento, consulte [cómo usar el almacenamiento de Azure cola con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Temas relacionados tratados en este artículo incluyen las siguientes:

* Funciones de asincrónica
* Instancias múltiples
* Se está saliendo
* Usar atributos de WebJobs SDK en el cuerpo de una función
* Establecer las cadenas de conexión de SDK en código.
* Establecer los valores para SDK WebJobs parámetros de constructor en el código
* Configurar `MaxDequeueCount` para el control de blobs dudoso.
* Desencadenar una función manualmente
* Escribir registros

## <a id="nextsteps"></a>Pasos siguientes

Esta guía proporciona ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con BLOB Azure. Para obtener más información sobre cómo usar WebJobs de Azure y el SDK WebJobs, vea [Azure WebJobs recomienda recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
