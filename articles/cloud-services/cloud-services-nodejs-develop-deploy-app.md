<properties
    pageTitle="Guía de inicio rápido de Node.js | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación web de Node.js simple e implementar en un servicio de nube de Azure."
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Compile e implemente una aplicación de Node.js a un servicio de nube de Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

En este tutorial se muestra cómo crear una aplicación de Node.js simple que se ejecuta en un servicio de nube de Azure. Servicios de nube son los bloques de creación de aplicaciones de nube scalable en Azure. Permiten la separación y administración independiente y escalado de componentes de front-end y back-end de la aplicación.  Servicios de nube proporcionan una máquina virtual de dedicado robusta para hospedar cada rol confiable.

Para obtener más información sobre los servicios en la nube, y cómo se comparan con los sitios Web de Azure y máquinas virtuales de Windows, vea [sitios Web de Azure, servicios en la nube y comparación de máquinas virtuales de Windows].

>[AZURE.TIP] ¿Desea para crear un sitio Web simple? Si su caso implica simplemente un simple sitio Web front-end, considere la posibilidad de [usar una aplicación web ligero]. Puede actualizar fácilmente a un servicio de nube que crece de la aplicación web y cambian los requisitos.

Siguiendo este tutorial, va a crear una aplicación web simple hospedada dentro de una función de web. Se cálculo usarlo para probar la aplicación localmente y luego implementarlo con herramientas de línea de comandos de PowerShell.

La aplicación es una simple "Hola a todos":

![Un explorador web que muestra la página web de Hola a todos][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Requisitos previos

> [AZURE.NOTE] Este tutorial usa PowerShell de Azure, que requiere Windows.

- Instalar y configurar [Azure Powershell].
- Descargue e instale el [SDK de Azure para .NET 2.7]. En la configuración de la instalación, seleccione:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Crear un proyecto de servicio de nube de Azure

Realizar las siguientes tareas para crear un nuevo proyecto de servicio de nube de Azure, junto con scaffolding de Node.js básica:

1. Ejecutar **Windows PowerShell** como administrador. desde el **Menú Inicio** o la **Pantalla de inicio**, busque **Windows PowerShell**.

2. [PowerShell conectarse] a su suscripción.

3. Escriba el siguiente cmdlet de PowerShell para crear para crear el proyecto:

        New-AzureServiceProject helloworld

    ![El resultado del comando Hola a todos AzureService de nuevo][The result of the New-AzureService helloworld command]

    El cmdlet **New-AzureServiceProject** genera una estructura básica de una aplicación de Node.js a un servicio de nube de publicación. Contiene archivos de configuración necesarios para la publicación en Azure. El cmdlet también cambia el directorio de trabajo en el directorio del servicio.

    El cmdlet crea los archivos siguientes:

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** y **ServiceDefinition.csdef**: específicos de Azure archivos necesarios para la aplicación de publicación. Para obtener más información, vea [información general sobre la creación de un servicio hospedado para Azure].

    -   **deploymentSettings.json**: almacena la configuración local que utiliza los cmdlets de implementación de Azure PowerShell.

4.  Escriba el comando siguiente para agregar una nueva función de web:

        Add-AzureNodeWebRole

    ![El resultado del comando Agregar AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    El cmdlet **Add-AzureNodeWebRole** crea una aplicación de Node.js básica. También se modifica los archivos **.csfg** y **.csdef** para agregar entradas de configuración para la nueva función.

    > [AZURE.NOTE] Si no especifica un nombre de función, se usa un nombre predeterminado. Puede proporcionar un nombre como el primer parámetro de cmdlet:`Add-AzureNodeWebRole MyRole`

La aplicación Node.js se define en el archivo **server.js**, ubicado en el directorio del rol de web (**WebRole1** de forma predeterminada). Este es el código:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Este código es, esencialmente, el mismo que el ejemplo "Hola a todos" en el sitio Web de [nodejs.org] , excepto en que utiliza el número de puerto asignado por el entorno de nube.

## <a name="deploy-the-application-to-azure"></a>Implementar la aplicación en Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Descargar la configuración de publicación de Azure

Para implementar la aplicación en Azure, primero debe descargar la configuración de publicación para su suscripción de Azure.

1.  Ejecute el cmdlet de PowerShell de Azure siguiente:

        Get-AzurePublishSettingsFile

    Usará el explorador para ir a la página de descarga de la configuración de publicación. Puede que deba iniciar sesión con una Account de Microsoft. Si es así, utilice la cuenta asociada a su suscripción de Azure.

    Guarde el perfil descargado a una ubicación de archivo que se puede acceder fácilmente a.

2.  Ejecute el siguiente cmdlet para importar el perfil de publicación que ha descargado:

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Después de importar la configuración de publicación, considere la posibilidad de eliminar el archivo descargado .publishSettings, porque contiene información que podría permitir que un usuario para acceder a su cuenta.

### <a name="publish-the-application"></a>Publicar la aplicación

Para publicar, ejecute los comandos siguientes:

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-** NombreDeServicio es el nombre de la implementación. Debe ser un nombre único, en caso contrario, se producirá un error el proceso de publicación. El comando **Get-Date** elementos de fijación de una cadena de fecha y hora que debe crear un nombre único.

- **-Ubicación** especifica el centro de datos que la aplicación se hospedarán en. Para ver una lista de los centros de datos disponibles, use el cmdlet **Get-AzureLocation** .

- **-Inicio** se abre una ventana del explorador y navega al servicio hospedado después de que finalice la implementación.

Después de la publicación se realiza correctamente, verá una respuesta similar a la siguiente:

![El resultado del comando Publicar AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> Puede tardar varios minutos para que la aplicación a implementar y no estarán disponibles cuando se publica por primera vez.

Una vez completada la implementación, una ventana del explorador se abra y navegue hasta el servicio de nube.

![Mostrar la página de world Hola; una ventana del explorador la dirección URL indica que la página se hospeda en Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Ahora, la aplicación se ejecuta en Azure.

El cmdlet **AzureServiceProject publicar** lleva a cabo los siguientes pasos:

1.  Crea un paquete para implementar. El paquete contiene todos los archivos en la carpeta de la aplicación.

2.  Crea una nueva **cuenta de almacenamiento** si no existe. La cuenta de almacenamiento de Azure se usa para almacenar el paquete de aplicación durante la implementación. Puede eliminar la cuenta de almacenamiento cuando haya terminado la implementación.

3.  Crea un nuevo **servicio de nube** si aún no existe. Un **servicio de nube** es el contenedor en el que se hospeda la aplicación cuando se implementa en Azure. Para obtener más información, vea [información general sobre la creación de un servicio hospedado para Azure].

4.  El paquete de implementación se publica en Azure.


## <a name="stopping-and-deleting-your-application"></a>Detener y eliminar la aplicación

Después de implementar la aplicación, desea deshabilitarlo para evitar costes adicionales. Facturas Azure web instancias de la función de tiempo de servidor consumido por hora. Hora del servidor se consume una vez que se implementa la aplicación, incluso si las instancias no se están ejecutando y están en el estado de detención.

1.  En la ventana de Windows PowerShell, detenga la implementación de servicio que creó en la sección anterior con el siguiente cmdlet:

        Stop-AzureService

    Detener el servicio puede tardar varios minutos. Cuando se detiene el servicio, recibirá un mensaje que indica que se ha detenido.

    ![El estado del comando Detener AzureService][The status of the Stop-AzureService command]

2.  Para eliminar el servicio, llame el siguiente cmdlet:

        Remove-AzureService

    Cuando se le solicite, escriba **Y** para eliminar el servicio.

    Eliminar el servicio puede tardar varios minutos. Después de que el servicio se ha eliminado, recibirá un mensaje que indica que el servicio se ha eliminado.

    ![El estado del comando Quitar AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] El servicio de no eliminar la cuenta de almacenamiento que se creó cuando el servicio se publicó inicialmente y continuará facturará almacenamiento utilizado. Si usa el almacenamiento nada, desee eliminarlo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Node.js].

<!-- URL List -->

[Comparación de sitios Web, servicios de nube y máquinas virtuales de Windows Azure]: ../app-service-web/choose-web-site-cloud-service-vm.md
[con una aplicación web ligero]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Azure Powershell]: ../powershell-install-configure.md
[Azure SDK para .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Conectar PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Información general sobre la creación de un servicio hospedado de Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centro para desarrolladores de Node.js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
