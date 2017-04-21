<properties 
    pageTitle="Tutorial: Crear una canalización con la actividad de copia con la API de .NET | Microsoft Azure" 
    description="En este tutorial, cree una canalización del generador de datos de Azure con una actividad de copia con la API de .NET." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Crear una canalización con la actividad de copia con la API de .NET
> [AZURE.SELECTOR]
- [Información general y los requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Asistente para copiar](data-factory-copy-data-wizard-tutorial.md)
- [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure plantilla de administrador de recursos](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API DE REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API DE .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


En este tutorial se muestra cómo crear y supervisar un generador de datos de Azure con la API de .NET. La canalización en el generador de datos utiliza una actividad de copiar para copiar los datos desde el almacenamiento de blobs de Windows Azure a base de datos de SQL Azure.

La actividad de copia realiza el movimiento de datos en el generador de datos de Azure. La actividad funciona con un servicio disponible globalmente que puede copiar datos entre varias almacena datos de forma segura, confiable y scalable. Consulte el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más información acerca de la actividad de copia.   

> [AZURE.NOTE] 
> Este artículo no trata sobre todos los la API de .NET del generador de datos. Vea [Referencia de la API de la fábrica de datos .NET](https://msdn.microsoft.com/library/mt415893.aspx) para obtener más información sobre el generador de datos .NET SDK. 

## <a name="prerequisites"></a>Requisitos previos
- Vaya a través de [información general de Tutorial y requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener información general sobre el tutorial y complete los pasos de **requisito previo** . 
- Visual Studio 2012 o 2013 o 2015
- Descargar e instalar el [SDK de .NET de Azure](http://azure.microsoft.com/downloads/)
- PowerShell Azure. Siga las instrucciones que aparecen en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) instalar Azure PowerShell en el equipo. Usar PowerShell Azure para crear una aplicación de Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Crear una aplicación en Azure Active Directory
Crear una aplicación de Azure Active Directory, cree una entidad de seguridad de servicio de la aplicación y asignar al rol de **Colaborador del generador de datos** .  

1. Iniciar **PowerShell**. 
1. Ejecute el comando siguiente y escriba el nombre de usuario y contraseña que utiliza para iniciar sesión en el portal de Azure.
    
        Login-AzureRmAccount   
2. Ejecute el comando siguiente para ver todas las suscripciones para esta cuenta.

        Get-AzureRmSubscription 
3. Ejecute el comando siguiente para seleccionar la suscripción que desea trabajar con. Reemplazar ** &lt;NameOfAzureSubscription** &gt; con el nombre de su suscripción de Azure. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Anote **SubscriptionId** y **TenantId** desde el resultado de este comando. 
4. Crear un grupo de recursos de Azure denominado **ADFTutorialResourceGroup** ejecutando el siguiente comando en el PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Si ya existe el grupo de recursos, especifique si desea actualizar (Y) o mantener como (N). 

    Si usa otro grupo de recursos, debe usar el nombre de su grupo de recursos en lugar de ADFTutorialResourceGroup en este tutorial.
5. Crear una aplicación de Azure Active Directory. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Si recibe el siguiente error, especifique una dirección URL diferente y vuelva a ejecutar el comando. 

        Another object with the same value for property identifierUris already exists.

6. Crear el anuncio principal del servicio. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Agregar a servicio principal para el rol de **Colaborador del generador de datos** . 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Obtener el identificador de aplicación.

        $azureAdApplication

    Anote el identificador de aplicación (**applicationID** de los resultados).

Deberá seguir cuatro valores de estos pasos: 

- Id. de inquilinos
- Id. de suscripción
- Identificador de la aplicación 
- Contraseña (especificado en el primer comando)   

## <a name="walkthrough"></a>Tutorial
1. Con Visual Studio 2012/2013/2015, cree una aplicación de consola de C#. NET.
    1. Inicie **Visual Studio** 2012/2013/2015.
    2. Haga clic en **archivo**, elija **nuevo**y haga clic en **proyecto**.
    3. Expanda **plantillas**y a continuación, seleccione **Visual C#**. En este tutorial, utilice C#, pero puede usar cualquier lenguaje. NET.
    4. Seleccione la **Aplicación de consola** de la lista de tipos de proyecto a la derecha.
    5. Escriba **DataFactoryAPITestApp** para el nombre.
    6. Haga clic en la ubicación **C:\ADFGetStarted** .
    7. Haga clic en **Aceptar** para crear el proyecto.
2. Haga clic en **Herramientas**, elija **Administrador de paquetes de Nuget**y haga clic en la **Consola del Administrador de paquetes**.
3.  En la **Consola del Administrador de paquete**, realice los pasos siguientes: 
    1.  Ejecute el comando siguiente para instalar el paquete de fábrica de datos:`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Ejecute el comando siguiente para instalar el paquete de Azure Active Directory (utilizar API de Active Directory en el código):`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Agregue la siguiente sección **appSetttings** al archivo **App.config** . Esta configuración se utiliza el método auxiliar: **GetAuthorizationHeader**. 

    Reemplazar valores para ** &lt;ID de aplicación&gt;**, ** &lt;contraseña&gt;**, ** &lt;identificador de suscripción&gt;**, y ** &lt;ID de inquilinos&gt; ** con sus propios valores. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Agregue el código siguiente que crea una instancia de clase de **DataPipelineManagementClient** para el método **Main** . Use este objeto para crear un generador de datos, un servicio vinculado, los conjuntos de datos de entrada y salida y una canalización. También se pueden utilizar este objeto para supervisar sectores de un conjunto de datos en tiempo de ejecución.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Reemplace el valor de **resourceGroupName** con el nombre de su grupo de recursos de Azure. 
    > 
    > Actualizar el nombre del generador de datos (**dataFactoryName**) sea único. Nombre de la fábrica de datos debe ser único global. Vea el tema de la [Fábrica de datos - las reglas de nombres](data-factory-naming-rules.md) para las reglas de nomenclatura para artefactos del generador de datos. 

7. Agregue el código siguiente que crea un **Generador de datos** para el método **Main** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Agregue el código siguiente que crea un **almacenamiento de Azure vinculado servicio** al método **Main** . 

    > [AZURE.IMPORTANT] Reemplazar **storageaccountname** y **accountkey** con nombre y la clave de su cuenta de almacenamiento de Azure. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Agregue el código siguiente que crea un **servicio de vinculado de SQL Azure** al método **Main** .
 
    > [AZURE.IMPORTANT] Reemplace **nombre_de_servidor**, **databasename**, **nombre de usuario**y **contraseña** con nombres de su servidor, la base de datos, el usuario y la contraseña de SQL Azure.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Agregue el código siguiente que crea **los conjuntos de datos de entrada y salida** para el método **Main** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Agregue el código siguiente que **crea y activa una canalización** al método **Main** . Esta canalización tiene **CopyActivity** que toma **BlobSource** como origen y **BlobSink** como un receptor.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Agregue el código siguiente al método **principal** para conocer el estado de un sector de datos del conjunto de datos de salida. Hay solo sector esperada en este ejemplo.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Agregue el código siguiente para obtener detalles de un segmento de datos de ejecución al método **Main** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Agregue el siguiente método auxiliar utilizado por el método **principales** a la clase **Program** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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


15. En el Explorador de soluciones, expanda el proyecto (**DataFactoryAPITestApp**), haga clic en **referencias**y haga clic en **Agregar referencia**. Seleccione la casilla de verificación de ensamblado "**System.Configuration**" y haga clic en **Aceptar**. 
16. Crear la aplicación de consola. Haga clic en **Generar** en el menú y haga clic en **Generar solución**. 
16. Confirme que hay al menos un archivo en el contenedor **adftutorial** el almacenamiento de blobs de Windows Azure. Si no es así, crear archivo de **Emp.txt** en el Bloc de notas con el siguiente contenido y cárguelo en el contenedor adftutorial.

        John, Doe
        Jane, Doe
     
17. Ejecutar el ejemplo haciendo clic en **Depurar** -> **Iniciar depuración** en el menú. Cuando vea los **Detalles de un segmento de datos de ejecución de introducción**, espere unos minutos y presione **ENTRAR**. 
18. Utilice el portal de Azure para comprobar que el generador de datos **APITutorialFactory** se crea con los siguientes artefactos: 
    - Vinculado de servicio: **LinkedService_AzureStorage** 
    - Conjunto de datos: **DatasetBlobSource** y **DatasetBlobDestination**.
    - Canalización: **PipelineBlobSample** 
18. Comprobar que los registros del dos empleado se crean en la tabla "**emp**" en la base de datos de SQL Azure especificada.

## <a name="next-steps"></a>Pasos siguientes

- Lea el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) , que proporciona información detallada sobre la actividad de copia que usó en el tutorial.
- Vea [Referencia de la API de la fábrica de datos .NET](https://msdn.microsoft.com/library/mt415893.aspx) para obtener más información sobre el generador de datos .NET SDK. Este artículo no trata sobre todos los la API de .NET del generador de datos. 

 
