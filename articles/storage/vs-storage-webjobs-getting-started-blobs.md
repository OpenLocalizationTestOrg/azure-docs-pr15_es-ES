<properties
    pageTitle="Empezar a trabajar con blob almacenamiento y Visual Studio conectan servicios (WebJob proyectos) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento de blobs de un proyecto WebJob después de conectar con un almacenamiento de Azure mediante Visual Studio conectada servicios."
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introducción a blobs de Windows Azure almacenamiento y Visual Studio conectan servicios (WebJob proyectos)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Este artículo proporciona ejemplos de código de C# que muestran cómo desencadenar un proceso cuando se crea o actualiza un blobs de Windows Azure. Los ejemplos de código usar la versión de [SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) 1.x. Al agregar una cuenta de almacenamiento a un proyecto WebJob mediante el cuadro de diálogo **Agregar servicios conectados** de Visual Studio, está instalado el paquete de Azure almacenamiento NuGet adecuado, se agregan las referencias de .NET correspondientes al proyecto y cadenas de conexión para la cuenta de almacenamiento se actualizan en el archivo App.config.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Cómo activar una función cuando se crea o actualiza un blob

Esta sección muestra cómo usar el atributo **BlobTrigger** .

 **Nota:** El SDK WebJobs analiza los archivos de registro para ver para blobs nuevas o modificadas. Este proceso es lenta inherente; una función podría no volverse hasta varios minutos o más después de crea el blob.  Si su aplicación necesita procesar BLOB inmediatamente, es el método recomendado crear un mensaje de cola al crear el blob y utilizar el atributo [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) en lugar del atributo **BlobTrigger** en la función que procesa el blob.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos de que se pueden enlazar con BLOB

Puede usar el atributo **BlobTrigger** en los siguientes tipos:

* **cadena**
* **TextReader**
* **Secuencia**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Otros tipos de deserializar [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Si desea trabajar directamente con la cuenta de almacenamiento de Azure, también puede agregar un parámetro **CloudStorageAccount** a la firma del método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Obtener el contenido de blobs de texto mediante un enlace a cadena

Si se espera BLOB de texto, **BlobTrigger** se puede aplicar a un parámetro de **cadena** . En el ejemplo de código siguiente enlaza un blob de texto a un parámetro de **cadena** denominado **logMessage**. La función usa dicho parámetro para escribir el contenido del objeto binario en el panel de WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Obtener serie blob contenido usando ICloudBlobStreamBinder

El ejemplo siguiente utiliza una clase que implementa **ICloudBlobStreamBinder** para habilitar el atributo **BlobTrigger** enlazar un blob al tipo de **WebImage** .

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

El código de enlace **WebImage** está disponible en una clase **WebImageBinder** que se deriva de **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Cómo manejar dudoso BLOB

Cuando se produce un error en una función **BlobTrigger** , el SDK de llamadas de nuevo, en caso de que el error se produjo por un error temporal. Si el error se produce por el contenido del objeto binario, se produce un error en la función cada vez intenta procesar el blob. De forma predeterminada, el SDK llama a una función hasta 5 veces para un blob determinado. Si se produce un error en la quinta try, el SDK agrega un mensaje a una cola denominada *webjobs-blobtrigger-toxicología*.

El número máximo de reintentos es configurable. La misma configuración [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) se usa para control de mensajes de cola dudoso y tratamiento de blobs dudoso.

El mensaje de cola para blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *{nombre WebJob}*. Funciones. *{Nombre de la función}*, por ejemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* Nombre del contenedor
* BlobName
* ETag (un identificador de versión de blobs de Windows, por ejemplo: "0x8D1DC6E70A277EF")

En el ejemplo siguiente, la función **CopyBlob** tiene código que provoca un error cada vez que se llama. Una vez el SDK llama para el número máximo de reintentos, se crea un mensaje en la cola blob dudoso y dicho mensaje se procesa mediante la función **LogPoisonBlob** .

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

El SDK deserializa automáticamente el mensaje JSON. Esto es la clase **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de sondeo BLOB

El SDK WebJobs analiza todos los contenedores especificados por atributos **BlobTrigger** al iniciar la aplicación. En una cuenta de almacenamiento grandes este examen puede tardar algún tiempo, por lo que puede ser un poco antes de que se encuentra BLOB nueva y las funciones de **BlobTrigger** se ejecutan.

Para detectar nueva o modificada BLOB después de iniciar la aplicación, el SDK lee periódicamente los registros de almacenamiento de blobs de Windows. Los registros de blob se almacenan en búfer y solo se escriben física cada 10 minutos o por lo tanto, por lo que puede ser retraso significativo después de un blob se crea o actualiza antes el correspondiente **BlobTrigger** función ejecuta.

Hay una excepción para blobs que cree mediante el atributo **Blob** . Cuando el SDK WebJobs crea un nuevo blob, pasa el nuevo blob inmediatamente a las funciones de **BlobTrigger** coincidentes. Por lo tanto, si tiene una cadena de blobs entradas y los resultados, el SDK puede procesar ellos eficazmente. Pero si desea que ejecuta el blob funciones para blobs que se hayan creado o actualizado por otros medios de procesamiento de latencia baja, se recomienda utilizar **QueueTrigger** en lugar de **BlobTrigger**.

### <a name="blob-receipts"></a>Confirmaciones de blobs

El SDK WebJobs se asegura de que ninguna función **BlobTrigger** se llama varias veces para el mismo blob nuevo o actualizado. Para ello, mantener *confirmaciones de blobs* para determinar si se ha procesado una versión blob determinado.

Confirmaciones de BLOB se almacenan en un contenedor denominado *hosts de webjobs de azure* en la cuenta de almacenamiento de Azure especificada por la cadena de conexión AzureWebJobsStorage. Una confirmación de blobs tiene la información siguiente:

* La función que se denominaba para el blob ("*{nombre WebJob}*. Funciones. *{Nombre de la función}*", por ejemplo:"WebJob1.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre de blob
* ETag (un identificador de versión de blobs de Windows, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar volver a procesar de un blob, puede eliminar manualmente la confirmación de blobs para blob desde el contenedor de *hosts de webjobs de azure* .

## <a name="related-topics-covered-by-the-queues-article"></a>Temas relacionados cubiertos por el artículo colas

Para obtener información sobre cómo controlar el procesamiento de blobs activado por un mensaje de cola o para WebJobs SDK escenarios no es específicos de manchas procesamiento, consulte [cómo usar el almacenamiento de Azure cola con el SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Temas relacionados tratados en este artículo incluyen las siguientes:

* Funciones de asincrónica
* Instancias múltiples
* Se está saliendo
* Usar atributos de WebJobs SDK en el cuerpo de una función
* Establecer las cadenas de conexión de SDK en código.
* Establecer los valores para SDK WebJobs parámetros de constructor en el código
* Configurar **MaxDequeueCount** para el control de blobs dudoso.
* Desencadenar una función manualmente
* Escribir registros

## <a name="next-steps"></a>Pasos siguientes

Este artículo proporciona ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con BLOB Azure. Para obtener más información sobre cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [los recursos de documentación WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).
