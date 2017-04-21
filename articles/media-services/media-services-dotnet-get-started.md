<properties
    pageTitle="Introducción a la entrega de contenido a petición mediante .NET | Azure"
    description="Este tutorial le guiará por los pasos de la implementación de una aplicación de entrega de contenido a petición con Azure Media Services utilizando .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introducción a la entrega de contenido a petición mediante el SDK de .NET

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Información general 

Este tutorial le guiará por los pasos de la implementación de una aplicación de la entrega de contenido de vídeo a la carta (VoD) con Azure Media Services (AMS) SDK para .NET.


El tutorial presenta el flujo de trabajo básico de Media Services y los objetos de programación más comunes y las tareas necesarias para el desarrollo de Media Services. Al final del curso, podrá transmitir o cada vez descargar un archivo de media de muestra que cargó, codificado y descargado.

## <a name="what-youll-learn"></a>Lo que aprenderá

El tutorial muestra cómo realizar las siguientes tareas:

1.  Crear una cuenta de Media Services (con el portal de Azure).
2.  Configurar extremo streaming (con el portal de Azure).
3.  Crear y configurar un proyecto de Visual Studio.
5.  Conectarse a la cuenta de Media Services.
6.  Crear un nuevo activo y cargar un archivo de vídeo.
7.  Codificar el archivo de origen en un conjunto de archivos de velocidad adaptación MP4.
8.  Publicar el activo y obtener direcciones URL para descargar streaming y progresivo.
9.  Probar reproducir contenido.

## <a name="prerequisites"></a>Requisitos previos

Los siguientes son necesarios para completar el tutorial.

- Para completar este tutorial, necesita una cuenta de Azure. 
    
    Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que se pueden usar para probar los servicios de Azure pagados. Incluso después de que se usan los créditos hacia arriba, puede mantener la cuenta y usar las características, como la característica de aplicaciones Web de servicio de la aplicación de Azure y servicios gratuitos de Azure.
- Sistemas operativos: Windows 8 o posterior, Windows 2008 R2, Windows 7.
- .NET framework 4.0 o posterior
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versiones posteriores.


##<a name="download-sample"></a>Descargar ejemplo

Obtener y ejecutar un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Crear una cuenta de Azure Media Services con el portal de Azure

Los pasos de esta sección muestran cómo crear una cuenta de AMS.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ nuevo** > **multimedia + CDN** > **Media Services**.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. En **Crear cuenta de servicios de medios** escriba valores requeridos.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. En **Nombre de la cuenta**, escriba el nombre de la nueva cuenta de AMS. Un nombre de cuenta de Media Services es todos los números en minúsculas o letras sin espacios y es de 3 a 24 caracteres de longitud.
    2. En la suscripción, seleccione entre las diferentes suscripciones Azure que tienen acceso a.
    
    2. En el **Grupo de recursos**, seleccione el recurso nuevo o existente.  Un grupo de recursos es una colección de recursos que comparten directivas, permisos y ciclo de vida. Más información [aquí](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. En **ubicación**, seleccione la región geográfica se usa para almacenar los registros de medios y metadatos para su cuenta de Media Services. Esta área se usa para procesar y transmita los elementos multimedia. Solo las regiones Media Services disponibles aparecen en el cuadro de lista desplegable. 
    
    3. En la **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento para proporcionar el almacenamiento de blobs del contenido multimedia desde su cuenta de Media Services. Puede seleccionar una cuenta existente de almacenamiento en la misma región geográfica como su cuenta de Media Services, o puede crear una cuenta de almacenamiento. Se crea una nueva cuenta de almacenamiento en la misma región. Las reglas de nombres de cuenta de almacenamiento son los mismos que las cuentas de Media Services.

        Obtenga más información sobre el almacenamiento [aquí](storage-introduction.md).

    4. Seleccione **Anclar al panel** para ver el progreso de la implementación de la cuenta.
    
7. Haga clic en **crear** en la parte inferior del formulario.

    Una vez que la cuenta se ha creado correctamente, el estado cambia a **está ejecutando**. 

    ![Configuración de servicios de medios](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para administrar su cuenta AMS (por ejemplo, cargar vídeos, codificar activos, supervisar el progreso de tarea) Utilice la ventana de **configuración** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar extremos streaming con el portal de Azure

Cuando se trabaja con uno de los escenarios más comunes ofrece vídeo a través de la velocidad de adaptación transmisión a los clientes de Azure Media Services. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

Media Services proporciona embalaje dinámico, que le permite ofrecer la velocidad de adaptación contenido MP4 codificado en formatos admitidos por el Media Services (MPEG guión, HLS, Smooth Streaming, HDS) just-in-time, sin tener que almacenar versiones preconfiguradas de cada uno de estos formatos de transmisión de transferencia.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo mezzanine (origen) en un conjunto de archivos de velocidad adaptación MP4 (se muestran los pasos de codificación más adelante en este tutorial).  
- Crear al menos una unidad de transmisión por secuencias para la *transmisión de extremo* desde el que tiene previsto entrega el contenido. Los pasos que muestran cómo cambiar el número de unidades streaming.

Con embalaje dinámico, solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services crea y sirve la respuesta adecuada en función de las solicitudes de un cliente.

Para crear y cambiar el número de unidades reservadas de transmisión, haga lo siguiente:


1. En la ventana **configuración** , haga clic en **los extremos de transmisión por secuencias**. 

2. Haga clic en el valor predeterminado transmisión extremo. 

    Aparecerá el cuadro de diálogo **Detalles de extremo de transmisión predeterminado** .

3. Para especificar el número de unidades streaming, deslice el control deslizante de **unidades de flujo de datos** .

    ![Unidades de transmisión](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Haga clic en el botón **Guardar** para guardar los cambios.

    >[AZURE.NOTE]La asignación de las nuevas unidades puede tardar hasta 20 minutos en completarse.

##<a name="create-and-configure-a-visual-studio-project"></a>Crear y configurar un proyecto de Visual Studio

1. Crear una nueva aplicación de consola de C# en Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **nombre**, la **ubicación**y el **nombre de la solución**y, a continuación, haga clic en **Aceptar**.

2. Use el paquete de NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **.NET SDK extensiones de Azure Media Services**.  Las extensiones de Media Services .NET SDK es un conjunto de métodos de extensión y funciones auxiliares que simplifica el código y que sea más fácil desarrollar con Media Services. Instalar el paquete, también instala **.NET SDK de Media Services** y agrega todas las dependencias necesarias.

3. Agregar una referencia a ensamblado System.Configuration. Este ensamblado contiene la clase **System.Configuration.ConfigurationManager** que se usa para tener acceso a archivos de configuración, por ejemplo, App.config.

4. Abra el archivo App.config (agregue el archivo a un proyecto si no se agregó de forma predeterminada) y agregue una sección *appSettings* al archivo. Establecer los valores de la clave de nombre y la cuenta de cuenta de Azure Media Services, como se muestra en el ejemplo siguiente. Para obtener el nombre de cuenta y la información de la clave, vaya al [portal de Azure](https://portal.azure.com/) y seleccione la cuenta de AMS. A continuación, seleccione **configuración** > **teclas**. Las ventanas de las claves de administrar muestra el nombre de cuenta y se muestra la clave principal y secundaria.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Sobrescribir la existente instrucciones **using** al principio del archivo Program.cs con el código siguiente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Crear una nueva carpeta en el directorio de proyectos y copiar un archivo. mp4 o .wmv que desea codificar y transmitir o descargar cada vez. En este ejemplo, se utiliza la ruta de acceso "C:\VideoFiles".

##<a name="connect-to-the-media-services-account"></a>Conectarse a la cuenta de servicios de medios

Cuando se utiliza Media Services con. NET, debe usar la clase **CloudMediaContext** para la mayoría de servicios de las tareas de programación: conectarse a Media Services cuenta; crear, actualizar, acceso y eliminación de los siguientes objetos: activos, archivos de activos, trabajos, las directivas de acceso, localizadores, etcetera.

Sobrescribir la clase de programa predeterminada con el siguiente código. El código muestra cómo leer los valores de conexión del archivo App.config y cómo crear el objeto **CloudMediaContext** para conectarse a servicios de medios. Para obtener más información sobre cómo conectarse a servicios de medios, vea [Conectar con Media Services con el SDK de servicios de medios para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La función **Main** llama a métodos que se definirá más adelante en esta sección.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Crear un nuevo activo y cargar un archivo de vídeo

En servicios de medios, cargar (o recopilar) sus archivos digitales en un activo. Entidad de **activos** puede contener vídeo, audio, imágenes, colecciones de miniaturas, texto pistas y subtítulos archivos (y los metadatos acerca de estos archivos.)  Una vez que se cargan los archivos, el contenido se almacena de forma segura en la nube para el procesamiento de más y transmisión. Los archivos en el activo se denominan **Archivos de activos**.

El método **UploadFile** descrito a continuación llamadas **CreateFromFile** (definido en las extensiones de .NET SDK). **CreateFromFile** crea un nuevo activo en el que se carga el archivo de origen especificado.

El método de **CreateFromFile** toma **AssetCreationOptions** que le permite especificar una de las siguientes opciones de creación de activos:

- **Ninguno** : No cifrado se utiliza. Este es el valor predeterminado. Tenga en cuenta que, cuando se usa esta opción, el contenido no está protegido en tránsito o en el resto de almacenamiento.
Si va a ofrecer un MP4 mediante descarga progresiva, use esta opción.
- **StorageEncrypted** - Use esta opción para cifrar el contenido de borrar localmente mediante el cifrado de-256 bits estándar de cifrado avanzado (AES), que, a continuación, se cargarán en donde se encuentra almacenado el almacenamiento de Azure cifrada al resto. Activos protegidos con cifrado de almacenamiento automáticamente sin cifrar y colocados en un sistema de archivos cifrados antes de codificación y, opcionalmente, cifrados de nuevo antes de cargarlo como un nuevo activo de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos de medios de entrada de alta calidad con cifrado almacenados en disco.
- **CommonEncryptionProtected** - Use esta opción si va a cargar contenido que ya se ha cifrado y protegido con cifrado comunes o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con PlayReady DRM).
- **EnvelopeEncryptionProtected** : Use esta opción si va a cargar HLS cifrada con AES. Tenga en cuenta que deben ha codificado y cifrado administrador transformar los archivos.

El método **CreateFromFile** también le permite especificar una devolución de llamada para informar del progreso de carga del archivo.

En el ejemplo siguiente, se especifique **ninguna** de las opciones de activos.

Agregue el método siguiente a la clase Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar el archivo de origen en un conjunto de archivos MP4 de adaptación de velocidad de bits

Después de recopila activos en Media Services, puede ser multimedia codificado, transmuxed, marca de agua etc., antes de que se entrega a los clientes. Estas actividades se programan y ejecutar en varias instancias de la función de fondo para asegurarse de alto rendimiento y disponibilidad. Estas actividades se denominan trabajos y cada trabajo se compone de atómicas tareas que realizar el trabajo real en el archivo activo.

Como se mencionó anteriormente, cuando se trabaja con Azure Media Services, uno de los escenarios más comunes ofrece adaptación velocidad transmisión a sus clientes. Media Services dinámicamente puede empaquetar un conjunto de archivos de velocidad adaptación MP4 en uno de los siguientes formatos: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar o transformar su mezzanine (origen) archivos en un conjunto de archivos de velocidad adaptación MP4 o adaptación de velocidad Smooth Streaming.  
- Obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido.

El código siguiente muestra cómo enviar un trabajo de codificación. El trabajo contiene una tarea que especifica el archivo mezzanine a transformar en un conjunto de velocidad adaptación MP4s con **Media Encoder estándar**. El código envía el trabajo y que espera hasta que se complete.

Una vez finalizado el trabajo, sería posible transmita los activos o cada vez descargar archivos MP4 creados como resultado de transcodificación.
Tenga en cuenta que no es necesario tener mayor que 0 unidades streaming para descargar archivos MP4 de cada vez.

Agregue el método siguiente a la clase Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar el activo y obtener direcciones URL para descargar streaming y progresivo

Para transmitir o descargar un activo, debe "publicación" mediante la creación de un localizador. Localizadores proporcionan acceso a archivos contenidos en el activo. Media Services es compatible con dos tipos de localizadores: OnDemandOrigin localizadores, utilizadas para transmitir contenido multimedia (por ejemplo, MPEG guión, HLS o Smooth Streaming) y localizadores de firma de Access (SA), utilizan para descargar archivos multimedia.

Después de crear los localizadores, se pueden generar las direcciones URL que se utilizan para transmitir o descargar archivos.


Una dirección URL streaming Smooth Streaming tiene el siguiente formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Una dirección URL streaming HLS tiene el siguiente formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Una dirección URL streaming MPEG guión tiene el siguiente formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Una dirección URL de SA utilizado para descargar archivos tiene el siguiente formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Extensiones de Media Services .NET SDK proporcionan métodos auxiliares adecuado que devuelven direcciones URL con formato para el activo publicado.

El código siguiente usa .NET SDK extensiones para crear localizadores y obtener transmisión y direcciones URL de descarga progresivo. El código también muestra cómo descargar archivos en una carpeta local.

Agregue el método siguiente a la clase Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Pruebe reproduciendo el contenido  

Después de ejecutar el programa que se definen en la sección anterior, las direcciones URL similares al siguiente se mostrará en la ventana de la consola.

Direcciones URL streaming adaptación:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG GUIÓN

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Direcciones URL de descarga progresivo (audio y vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para transmitir vídeo, utilice [El Reproductor de Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para probar la descarga progresiva, pegue una dirección URL en un explorador (por ejemplo, Internet Explorer, Chrome o Safari).


##<a name="next-steps-media-services-learning-paths"></a>Pasos siguientes: Media Services rutas de aprendizaje

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>¿Busca algo más?

Si este tema no contiene lo estaba esperando, falta algo o en otra forma no satisface sus necesidades, por favor, nos proporcione sus comentarios mediante el subproceso Disqus a continuación.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
