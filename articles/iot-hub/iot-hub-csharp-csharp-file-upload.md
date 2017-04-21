<properties
    pageTitle="Cargar archivos desde dispositivos con concentrador IoT | Microsoft Azure"
    description="Siga este curso para aprender a cargar archivos desde dispositivos con Azure IoT concentrador C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Tutorial: Cómo cargar archivos desde dispositivos a la nube con concentrador IoT

## <a name="introduction"></a>Introducción

Azure concentrador IoT es un servicio totalmente gestionado que permite confiable y comunicaciones seguras de bidireccional entre millones de IoT dispositivos y una aplicación back-end. Tutoriales anterior ([Introducción a IoT concentrador] y [enviar mensajes de dispositivo de nube con concentrador IoT]) ilustran la básica dispositivo en nube y funcionalidad de mensajería de dispositivo de nube de concentrador IoT y el tutorial de [mensajes del proceso de dispositivo en nube] describe una manera confiable almacenar mensajes de nube de dispositivo en el almacenamiento de blobs de Windows Azure. Sin embargo, en algunos casos no puede asignar fácilmente los datos de que sus dispositivos envían en los mensajes de nube de dispositivo relativamente pequeños que acepta IoT concentrador. Por ejemplo, archivos grandes que contienen imágenes, vídeos, datos de vibración muestreadas de alta frecuencia o que contengan algún tipo de datos procesados previamente. Estos archivos suelen lote procesado en la nube con herramientas como [Generador de datos de Azure] o la pila [Hadoop] . Cuando las cargas de archivos desde un dispositivo preferidas para enviar eventos, aún puede usar la funcionalidad de seguridad y confiabilidad de concentrador IoT.

En este tutorial se basa en el código en el tutorial de [enviar mensajes de dispositivo de nube con IoT concentrador] para mostrar cómo usar las capacidades de carga de archivo de concentrador IoT. Muestra cómo proporcionar de forma segura un dispositivo con una Azure blob URI para cargar un archivo y cómo usar las notificaciones de carga de archivo IoT concentrador para desencadenar el procesamiento del archivo en el fondo de la aplicación.

Al final de este tutorial ejecutar dos aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación que creó en el tutorial de [enviar mensajes de dispositivo de nube con IoT concentrador] , que se carga un archivo al almacenamiento usando un URI SAS proporcionada por el concentrador IoT.
* **ReadFileUploadNotification**, que recibe las notificaciones de carga de archivo desde su centro IoT.

> [AZURE.NOTE] Concentrador IoT es compatible con muchas plataformas de dispositivos e idiomas (incluidos C, Java y Javascript) a través del dispositivo de Azure IoT SDK. Consulte el [Centro para desarrolladores de Azure IoT] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo para el código que se muestra en este tutorial y generalmente Azure IoT concentrador.

Para poder completar este tutorial, necesita lo siguiente:

+ Microsoft Visual Studio 2015,

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Asociar una cuenta de almacenamiento de Azure a IoT concentrador

Porque el dispositivo simulado carga un archivo a un blob, debe tener una cuenta de [Almacenamiento de Azure] asociada a IoT concentrador. Cuando se asocia una cuenta de almacenamiento de Azure a un concentrador IoT, el concentrador puede generar URI SAS que puede usar un dispositivo de forma segura cargar un archivo a un contenedor de blob. El servicio de concentrador IoT y el SDK de dispositivos coordinar el proceso que genera el URI SAS y hace que esté disponible para un dispositivo que se usará para cargar un archivo.

Siga las instrucciones de [cargas de archivos de configurar con el portal de Azure] [ lnk-configure-upload] para asociar una cuenta de Azure almacenamiento a su centro IoT.

## <a name="upload-a-file-from-a-simulated-device"></a>Cargar un archivo desde un dispositivo simulado

En esta sección, modifique la aplicación de dispositivo simulado que creó en [enviar mensajes de dispositivo de nube con IoT concentrador] para recibir mensajes de dispositivo de nube de concentrador IoT.

1. En Visual Studio, haga clic en el proyecto **SimulatedDevice** , haga clic en **Agregar**y, a continuación, haga clic en **Elemento existente**. Vaya a un archivo de imagen e incluir en el proyecto. En este tutorial se da por hecho que la imagen se denomina `image.jpg`.

2. Haga clic en la imagen y, a continuación, haga clic en **Propiedades**. Asegúrese de que **Copiar en el directorio de salida** se establece en **Copiar siempre**.

    ![][1]

3. En el archivo **Program.cs** , agregue las siguientes instrucciones en la parte superior del archivo:

        using System.IO;

4. Agregue el método siguiente a la clase **Program** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    La `UploadToBlobAsync` método toma del origen de nombre y la secuencia de archivo del archivo de cargarse y controla la carga al almacenamiento. La aplicación de consola muestra el tiempo que se tarda en cargar el archivo.

5. Agregue el método siguiente en el método **Main** , justo antes de la `Console.ReadLine()` línea:

        SendToBlobAsync();

> [AZURE.NOTE] Para simplificar, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, un crecimiento exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores].

## <a name="receive-a-file-upload-notification"></a>Recibir una notificación de carga de archivo

En esta sección, escriba una aplicación de consola de Windows que recibe mensajes de notificación de carga de archivo de concentrador IoT.

1. En la solución actual de Visual Studio, cree un nuevo proyecto de Visual C# Windows con la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **ReadFileUploadNotification**.

    ![Nuevo proyecto de Visual Studio][2]

2. En el Explorador de soluciones, haga clic en el proyecto **ReadFileUploadNotification** y, a continuación, haga clic en **Administrar paquetes de NuGet**.

    Muestra la ventana Administrar paquetes de NuGet.

2. Buscar `Microsoft.Azure.Devices`, haga clic en **instalar**y acepte los términos de uso. 

    Descargas, instalaciones y agrega una referencia a la [Azure IoT - paquete NuGet SDK de servicio] en el proyecto **ReadFileUploadNotification** .

3. En el archivo **Program.cs** , agregue las siguientes instrucciones en la parte superior del archivo:

        using Microsoft.Azure.Devices;

4. Agregue los campos siguientes a la clase **Program** . Sustituir el valor de marcador de posición con la cadena de conexión de hub IoT de [Introducción a IoT concentrador]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Agregue el método siguiente a la clase **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Tenga en cuenta que el modelo de recepción es la misma utilizado para recibir mensajes de dispositivo de nube desde la aplicación del dispositivo.

6. Por último, agregue las siguientes líneas al método **Main** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1. En Visual Studio, haga clic en la solución y seleccione **proyectos de inicio de establecer**. Seleccione **varios proyectos de inicio**y luego seleccione la acción **Iniciar** para **ReadFileUploadNotification** y **SimulatedDevice**.

2. Presione **F5**. Ambas aplicaciones deberían empezar. Ahora debe aparecer la carga completada en una aplicación de consola y el mensaje de notificación de carga recibida por otra aplicación de consola. Puede usar el [portal de Azure] o el Explorador de servidores de Visual Studio para comprobar la presencia del archivo cargado en la cuenta de almacenamiento de Azure.

  ![][50]


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo aprovechar las capacidades de carga de archivo de IoT concentrador para simplificar la carga de archivos desde dispositivos. Puede seguir explorar las características de concentrador IoT y escenarios con los siguientes artículos:

- [Crear un concentrador IoT mediante programación][lnk-create-hub]
- [Introducción a C SDK][lnk-c-sdk]
- [Concentrador IoT SDK][lnk-sdks]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portal de Azure]: https://portal.azure.com/

[Generador de datos de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Enviar mensajes de la nube para dispositivos con IoT concentrador]: iot-hub-csharp-csharp-c2d.md
[Process mensajes de dispositivo a la nube]: iot-hub-csharp-csharp-process-d2c.md
[Introducción a IoT concentrador]: iot-hub-csharp-csharp-getstarted.md
[Centro para desarrolladores de Azure IoT]: http://www.azure.com/develop/iot

[Control de errores transitorias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT - paquete NuGet SDK de servicio]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


