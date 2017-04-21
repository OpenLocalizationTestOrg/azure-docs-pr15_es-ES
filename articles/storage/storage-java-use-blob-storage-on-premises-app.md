<properties
    pageTitle="Aplicación de almacenamiento de blobs (Java) local | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación de consola que carga una imagen a Azure y, a continuación, se muestra la imagen en el explorador. Ejemplos de código de Java."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>Aplicación de almacenamiento de blobs local

## <a name="overview"></a>Información general

En el ejemplo siguiente se muestra cómo puede usar el almacenamiento de Azure para almacenar imágenes en Azure. El código de este artículo es para una aplicación de consola que carga una imagen a Azure y, a continuación, crea un archivo HTML que muestra la imagen en el explorador.

## <a name="prerequisites"></a>Requisitos previos

- Se instala un Kit de programador Java (JDK), versión 1,6 o posterior.
- Está instalado el SDK de Azure.
- El TARRO para las bibliotecas de Azure para Java y cualquier JARs dependencia aplicable, se instalan y están en la ruta de compilación usada por el compilador Java. Para obtener información sobre la instalación de las bibliotecas de Azure para Java, vea [descargar el SDK de Azure para Java](java-download-azure-sdk.md).
- Se ha configurado una cuenta de almacenamiento de Azure. El código de este artículo, se usará el nombre de la cuenta y la clave de cuenta para la cuenta de almacenamiento. Para obtener información sobre la creación de una cuenta de almacenamiento y [las teclas de acceso de almacenamiento de vista y copiar](storage-create-storage-account.md#view-and-copy-storage-access-keys) para obtener información sobre cómo recuperar la clave de cuenta, consulte [cómo crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) .

- Ha creado un archivo de imagen local denominado almacenado en la ruta de acceso c:\\myimages\\image1.jpg. También puede modificar el constructor   **FileInputStream** en el ejemplo para usar un nombre de ruta de acceso y el archivo de imagen diferente.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Usar el almacenamiento de blobs de Windows Azure para cargar un archivo

A continuación se presenta un procedimiento paso a paso. Si le gustaría continúe, todo el código se presenta más adelante en este artículo.

Para comenzar el código, incluyendo importaciones para las clases de almacenamiento de Azure principales, las clases de cliente de blobs de Windows Azure, las clases IO Java y la clase **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Declarar una clase denominada **StorageSample**e incluir el corchete de apertura, **{**.

    public class StorageSample {

Dentro de la clase **StorageSample** , declare una variable de cadena que contiene el protocolo de extremo de forma predeterminada, el nombre de la cuenta de almacenamiento y la clave de acceso de almacenamiento, tal como se especifica en la cuenta de almacenamiento de Azure. Reemplace los valores de marcador de posición **su\_cuenta\_nombre** y **su\_cuenta\_clave** con su propia clave de cuenta nombre y la cuenta, respectivamente.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Agregar en la declaración de **principal**, incluya un bloque de **probar** e incluir el corchete de apertura es necesario **{**.

    public static void main(String[] args)
    {
        try
        {

Declarar variables del tipo siguiente (las descripciones son de cómo se usan en este ejemplo):

-   **CloudStorageAccount**: utilizar iniciar el objeto con el nombre de la cuenta de almacenamiento de Azure y la clave de cuenta y para crear el objeto de cliente de blobs.
-   **CloudBlobClient**: usado para tener acceso al servicio de blobs de Windows.
-   **CloudBlobContainer**: se usan para crear un contenedor de blob, los BLOB en el contenedor de la lista y eliminar el contenedor.
-   **CloudBlockBlob**: usaste para cargar un archivo de imagen local al contenedor.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Asignar un valor a la variable de **cuenta** .

    account = CloudStorageAccount.parse(storageConnectionString);

Asignar un valor a la variable **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Asignar un valor a la variable de **contenedor** . Se obtendrá una referencia a un contenedor denominado **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Crear el contenedor. Este método creará el contenedor si aún no existe (y devuelve **true**). Si existe el contenedor, devuelve **false**. Una alternativa a **createIfNotExists** es el método de **crear** (que se devolverá un error si ya existe el contenedor).

    container.createIfNotExists();

Configurar el acceso anónimo para el contenedor.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtener una referencia a blobs de bloque, que representan el blob de almacenamiento de Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilice el constructor de **archivo** para obtener una referencia al archivo creado localmente que va a cargar. Asegúrese de que ha creado este archivo antes de ejecutar el código.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Cargue el archivo local mediante una llamada al método **CloudBlockBlob.upload** . El primer parámetro para el método **CloudBlockBlob.upload** es un objeto **FileInputStream** que representa el archivo local que se cargan al almacenamiento de Azure. El segundo parámetro es el tamaño, en bytes, del archivo.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Llamar a una función auxiliar denominada **MakeHTMLPage**para convertir una página HTML básica que contiene un ** &lt;imagen&gt; ** elemento con el origen establecido en blob que se encuentra ahora en su cuenta de almacenamiento de Azure. El código de **MakeHTMLPage** se explicará más adelante en este artículo.

    MakeHTMLPage(container);

Imprimir un mensaje de estado y obtener información acerca de la página HTML creada.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Cerrar el bloque **try** insertando un corchete de cierre: **}**

Controlar las excepciones siguientes:

-   **FileNotFoundException**: puede producir los constructores **FileInputStream** o **clase FileOutputStream** .
-   **StorageException**: se puede iniciar la biblioteca de almacenamiento de Azure cliente.
-   **URISyntaxException**: puede ser iniciada por el método **ListBlobItem.getUri** .
-   **Excepciones**: excepciones genérico.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Cerrar **principal** insertando un corchete de cierre: **}**

Crear un método denominado **MakeHTMLPage** para crear una página HTML básica. Este método tiene un parámetro de tipo **CloudBlobContainer**, que se utilizará para recorrer la lista de BLOB cargado. Este método producirá excepciones de tipo **FileNotFoundException**, que se pueden producir por la **clase FileOutputStream** constructor y **URISyntaxException**, que puede ser iniciada por el método de **ListBlobItem.getUri** . Incluir el corchete de apertura, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Crear un archivo local denominado **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Escribir en el archivo local, agregar en la ** &lt;html&gt;**, ** &lt;encabezado&gt;**, y ** &lt;cuerpo&gt; ** elementos.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Recorrer la lista de BLOB cargado. Para cada blob, en la página HTML crear un ** &lt;img&gt; ** elemento que tiene el atributo **src** enviado al URI del blob ya que existe en su cuenta de almacenamiento de Azure.
Aunque agregado sólo una imagen en este ejemplo, si ha agregado más, este código podría recorrer en todos ellos.

Para simplificar, se supone que cada blob cargado es una imagen. Si ha actualizado BLOB que no sean imágenes o blobs de página en lugar de blobs de bloque, modifique el código según sea necesario.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Cerrar la ** &lt;cuerpo&gt; ** elemento y la ** &lt;html&gt; ** elemento.

    stream.println("</body>");
    stream.println("</html>");

Cierre el archivo local.

    stream.close();

Cerrar **MakeHTMLPage** insertando un corchete de cierre: **}**

Cerrar **StorageSample** insertando un corchete de cierre: **}**

El siguiente es el código completo de este ejemplo. Recuerde que debe modificar los valores de marcador de posición **su\_cuenta\_nombre** y **su\_cuenta\_clave** para usar su nombre de cuenta y la clave de cuenta, respectivamente.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Además de cargar el archivo de imagen local con el almacenamiento de Azure, el código de ejemplo crea un namedindex.html de archivo local, puede abrir en el explorador para ver la imagen cargada.

Dado que el código contiene el nombre de la cuenta y la clave de cuenta, asegúrese de que el código fuente es seguro.

## <a name="to-delete-a-container"></a>Para eliminar un contenedor

Como se cargan de almacenamiento, puede que prefiera eliminar el contenedor de **gettingstarted** cuando termine de experimentar con este ejemplo. Para eliminar un contenedor, use el método de **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Para llamar al método **CloudBlobContainer.delete** , el proceso de inicialización de objetos **CloudStorageAccount**, **ClodBlobClient**y **CloudBlobContainer** es el mismo que aparece para el método **createIfNotExist** . A continuación se muestra un ejemplo completo que elimina el contenedor denominado **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Para obtener información general de los otros métodos y clases de almacenamiento de blobs de Windows, vea [cómo usar el almacenamiento de blobs de Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información acerca de las tareas de almacenamiento más complejas.

- [Almacenamiento de Azure SDK de Java](https://github.com/azure/azure-storage-java)
- [Referencia SDK de cliente de almacenamiento de Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
