<properties
    pageTitle="Administración de .NET SDK para el análisis de secuencia | Microsoft Azure"
    description="Introducción a la secuencia de análisis de administración .NET SDK. Obtenga información sobre cómo configurar y ejecutar trabajos de análisis: crear un proyecto, entradas, salidas y transformaciones."
    keywords=".NET SDK, API de análisis"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Administración de .NET SDK: Configurar y ejecutar trabajos de análisis con la API de análisis de secuencia de Azure para .NET

Obtenga información sobre cómo configurar un trabajos de análisis de ejecución con la API de análisis de secuencia para .NET mediante el SDK de .NET de administración. Configurar un proyecto, crear inicio, las transformaciones y los orígenes de entrada y salidos y detener trabajos. Para los trabajos de análisis, puede transmitir datos de almacenamiento de blobs o de un concentrador de evento.

Consulte la [documentación de referencia de administración de la API de análisis de secuencia de .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Análisis de secuencia de Azure es un servicio de totalmente administrado proporcionar procesamiento de eventos de baja latencia, altamente disponible, scalable, complejo sobre la transmisión de datos en la nube. Análisis de secuencia permite a los clientes configurar transmisión trabajos para analizar los flujos de datos y le permite conducir cerca de análisis en tiempo real.  


## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este artículo, debe tener el siguiente:

- Instalar Visual Studio 2012 o 2013.
- Descargue e instale el [SDK de .NET de Azure](https://azure.microsoft.com/downloads/).
- Crear un grupo de recursos de Azure en la suscripción. A continuación se muestra una secuencia de comandos de PowerShell de Azure. Para obtener información de Azure PowerShell, vea [instalar y configurar Azure PowerShell](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   Configurar un origen de entrada y de destino de salida para usar. Para obtener instrucciones vea [Agregar entradas](stream-analytics-add-inputs.md) para configurar una entrada de ejemplo y [Agregar salidas](stream-analytics-add-outputs.md) para configurar un resultado de ejemplo.


## <a name="set-up-a-project"></a>Configurar un proyecto

Para crear una tarea de análisis utilizar la API de análisis de secuencia de. NET, configure en primer lugar el proyecto.

1. Crear una aplicación de consola de Visual Studio C#. NET.
2. En la consola del Administrador de paquetes, ejecute los comandos siguientes para instalar los paquetes de NuGet. El primero es el SDK de .NET Management de Azure secuencia análisis. La segunda es el cliente de Azure Active Directory que se usará para la autenticación.

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Agregue la siguiente sección **appSettings** al archivo App.config:

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    Reemplazar valores para **SubscriptionId** y **ActiveDirectoryTenantId** con su suscripción de Azure e identificadores de inquilinos. Puede obtener estos valores ejecutando el siguiente cmdlet de PowerShell de Azure:

        Get-AzureAccount

5. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto:

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  Agregar un método de autenticación auxiliar:

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(
                        ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                        ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AsaClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


## <a name="create-a-stream-analytics-management-client"></a>Crear a un cliente de administración de análisis de secuencia

Un objeto **StreamAnalyticsManagementClient** le permite administrar el trabajo y los componentes de trabajo, como entrada, salida y transformación.

Agregue el código siguiente al principio del método **Main** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Valor de la variable **resourceGroupName** debe ser el mismo que el nombre del grupo de recursos creado o elegido en los pasos previos.

Para automatizar el aspecto de la presentación de credenciales de creación de trabajo, consulte [autenticar a un principal de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md).

Las secciones restantes de este artículo se suponen que este código está al principio del método **Main** .

## <a name="create-a-stream-analytics-job"></a>Crear un análisis de flujo de trabajo

El código siguiente crea una tarea de análisis de secuencia en el grupo de recursos que haya definido. Agregará una entrada y salida transformación al trabajo más adelante.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Crear un origen de entrada de análisis de secuencia

El código siguiente crea un origen de entrada de análisis de secuencia con el tipo de origen de entrada de blobs y serialización CSV. Para crear un origen de entrada de concentrador de evento, use **EventHubStreamInputDataSource** en lugar de **BlobStreamInputDataSource**. Asimismo, puede personalizar el tipo de serialización del origen de entrada.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Orígenes de entrada, ya sea desde el almacenamiento de blobs o un concentrador de evento vinculados a una tarea específica. Para usar el mismo origen de entrada para las distintas tareas, debe llamar al método de nuevo y especifique un nombre distinto.


## <a name="test-a-stream-analytics-input-source"></a>Probar un origen de entrada de análisis de secuencia

El método **TestConnection** comprueba si el trabajo de análisis de secuencia es capaz de conectar con el origen de entrada, así como otros aspectos específicos del tipo de origen de entrada. Por ejemplo, en el origen de entrada de blobs creado en un paso anterior, el método va a comprobar que el nombre de la cuenta de almacenamiento y un par de claves puede usarse para conectarse a la cuenta de almacenamiento, además de comprobar que existe el contenedor especificado.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Crear un destino de los resultados del análisis de secuencia

Crear un destino de salida es muy similar a la creación de un origen de entrada de análisis de la secuencia. Como orígenes de entrada, destinos vinculados a una tarea específica. Para utilizar el mismo destino de salida para las distintas tareas, debe llamar al método de nuevo y especifique un nombre distinto.

El código siguiente crea un destino de salida (base de datos de SQL Azure). Puede personalizar el tipo de datos de destino de salida o tipo de serialización.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Probar un destino de los resultados del análisis de secuencia

Un destino de los resultados del análisis de secuencia también tiene el método **TestConnection** para probar las conexiones.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Crear una transformación de análisis de secuencia

El código siguiente crea una transformación de análisis de secuencia con la consulta "Seleccione * de la entrada" y especifica para asignar una unidad de transmisión para el trabajo de análisis de la secuencia. Para obtener más información sobre cómo ajustar unidades streaming, vea [trabajos de análisis de secuencia de escala de Azure](stream-analytics-scale-jobs.md).


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Como entrada y salida, una transformaciones también está vinculado a la tarea de análisis de secuencia específica que se creó en.

## <a name="start-a-stream-analytics-job"></a>Iniciar un trabajo de análisis de secuencia
Después de crear una tarea de análisis de secuencia y su entrada, salidas y transformación, puede iniciar el trabajo llamando al método de **Inicio** .

El siguiente ejemplo código comienza un trabajo de análisis de secuencia con una hora de inicio de salida personalizada establecida en 12 de diciembre de 2012, 12:12:12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>Detener un análisis de flujo de trabajo
Puede detener un trabajo de análisis de secuencia ejecución llamando al método **Detener** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Eliminar un análisis de flujo de trabajo
El método **Delete** eliminará el trabajo, así como los recursos secundarias subyacentes, incluidos entrada, salidas y transformación de la tarea.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>Obtener soporte técnico
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

Haya aprender los aspectos básicos del uso de un .NET SDK para crear y ejecutar trabajos de análisis. Para obtener más información, consulte lo siguiente:

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Secuencia de azure análisis administración .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
