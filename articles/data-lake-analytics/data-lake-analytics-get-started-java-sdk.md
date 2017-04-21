<properties
   pageTitle="Usar el análisis de datos lago Java SDK para desarrollar aplicaciones | Azure"
   description="Utilizar datos lago análisis Java SDK de Azure para desarrollar aplicaciones"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-java-sdk"></a>Tutorial: Introducción a análisis de lago de datos de Azure con el SDK de Java

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Obtenga información sobre cómo usar el SDK de Java Azure análisis de lago de datos para crear una cuenta de Azure datos lago y realizar operaciones básicas, tales como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta y trabajar con trabajos. Para obtener más información acerca de lago de datos, vea [Análisis de lago de datos de Azure](data-lake-analytics-overview.md).

En este tutorial, desarrollará una aplicación de consola de Java que contiene ejemplos de las tareas administrativas comunes, así como crear datos de prueba y enviar un trabajo.  Para pasar por el mismo tutorial utilizar otras herramientas compatibles, haga clic en las pestañas en la parte superior de esta sección.

## <a name="prerequisites"></a>Requisitos previos

* Kit de desarrollo de Java (JDK) 8 (usando Java versión 1,8).
* IntelliJ u otro entorno de desarrollo de Java adecuado. Esto es opcional pero se recomienda. Las siguientes instrucciones use IntelliJ.
* **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
* **Habilitar la suscripción de Azure** de vista previa de análisis de datos lago público. Vea [las instrucciones](data-lake-analytics-get-started-portal.md#signup).
* Crear una aplicación de Azure Active Directory (AAD) y recuperar su **Identificador de cliente**, el **Identificador de inquilino**y la **clave**. Para obtener más información acerca de las aplicaciones de AAD e instrucciones sobre cómo obtener un identificador de cliente, vea [Crear Active Directory aplicación y servicio principal con portal](../resource-group-create-service-principal-portal.md). El URI de respuesta y la clave también estará disponible desde el portal de una vez que tenga la aplicación creada y clave generada.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?

El fragmento de código siguiente proporciona código para **no interactivo** autenticación, donde la aplicación proporciona sus propias credenciales.

Debe dar a su aplicación de permiso para crear recursos en Azure este tutorial para que funcione. Es **muy recomendable** solo conceder permisos de colaborador de esta aplicación a un grupo de recursos nuevo, sin usar y vacío en su suscripción de Azure los fines de este tutorial.

## <a name="create-a-java-application"></a>Crear una aplicación de Java

1. Abra IntelliJ y cree un nuevo proyecto de Java con la plantilla de **Aplicación de la línea de comandos** .

2. Haga clic en el proyecto en el lado izquierdo de la pantalla y haga clic en **Agregar compatibilidad del marco**. Elija **experto** y haga clic en **Aceptar**.

3. Abra el archivo recién creado **"pom.xml"** y agregue el siguiente fragmento de texto entre el ** \</Version >** etiqueta y la ** \</proyecto >** etiqueta:

    Nota: Este paso es temporal hasta que esté disponible en experto en el SDK de análisis de lago de datos de Azure. En este artículo se actualizará cuando esté disponible en experto en el SDK. Todas las futuras actualizaciones de este SDK estará disponible a través de experto.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-20160513.000802-24</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-runtime</artifactId>
                <version>1.0.0-20160513.000812-28</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.rest</groupId>
                <artifactId>client-runtime</artifactId>
                <version>1.0.0-20160513.000825-29</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-analytics</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Vaya a **archivo**, **configuración**, a continuación, **compilar**, **ejecución**, **implementación**. Seleccione **Herramientas de creación**, **experto**, **Importar**. A continuación, active **experto en importar proyectos automáticamente**.

5. Abra **Main.java** y reemplace el bloque de código existente con el código siguiente. Además, proporcionar los valores de parámetros resaltadas en el fragmento de código, como **localFolderPath**, **_adlaAccountName**, **_adlsAccountName**, **_resourceGroupName** y reemplace los marcadores de posición para **Identificador de cliente**, **Secreto de cliente**, **Identificador de INQUILINO**e **Id. de suscripción**.

    Este código pasa por el proceso de crear cuentas de almacén de lago de datos y análisis de datos lago, crear archivos en el almacén, una tarea, obteniendo el estado del trabajo, descarga de salida de trabajo y finalmente eliminar la cuenta.

    >[AZURE.NOTE] Actualmente no hay un problema conocido con el servicio de lago de datos de Azure.  Si la aplicación de ejemplo se interrumpe o encuentra un error, debe eliminar manualmente las cuentas de almacén de lago de datos y análisis de datos lago que crea la secuencia de comandos.  Si no está familiarizado con el Portal, la Guía de [Administrar Azure datos lago el análisis con el Portal de Azure](data-lake-analytics-manage-use-portal.md) le ayudarán a trabajar.


        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.analytics.*;
        import com.microsoft.azure.management.datalake.analytics.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.util.ArrayList;
        import java.util.UUID;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _adlaAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store and Analytics accounts
                WaitForNewline("Authenticated.", "Creating NEW accounts.");
                CreateAccounts();
                WaitForNewline("Accounts created.", "Displaying accounts.");
        
                // List Data Lake Store and Analytics accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
                List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
                for (DataLakeAnalyticsAccount acct : adlaListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Accounts displayed.", "Creating files.");
        
                // Create a file in Data Lake Store: input1.csv
                // TODO: these change order in the next patch
                byte[] bytesContents = "123,abc".getBytes();
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);
        
                WaitForNewline("File created.", "Submitting a job.");
        
                // Submit a job to Data Lake Analytics
                UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();", "testJob");
                WaitForNewline("Job submitted.", "Getting job status.");
        
                // Wait for job completion and output job status
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                System.out.println("Waiting for job completion.");
                WaitForJob(jobId);
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                WaitForNewline("Job completed.", "Downloading job output.");
        
                // Download job output from Data Lake Store
                DownloadFile("/output1.csv", localFolderPath + "output1.csv");
                WaitForNewline("Job output downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/output1.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
                _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
                _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
                _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
                _adlsClient.setSubscriptionId(_subId);
                _adlaClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
        
                System.out.println(reason + "\r\nPress ENTER to continue...");
                try{System.in.read();}
                catch(Exception e){}
        
                if (!nextAction.isEmpty())
                {
                    System.out.println(nextAction);
                }
            }
        
            // Create Data Lake Store and Analytics accounts
            public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
                // Create ADLS account
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
        
                // Create ADLA account
                DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
                adlsInfo.setName(_adlsAccountName);
        
                DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
                adlsInfo.setProperties(adlsInfoProperties);
        
                List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
                adlsInfoList.add(adlsInfo);
        
                DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
                adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
                adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);
        
                DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
                adlaParameters.setLocation(_location);
                adlaParameters.setName(_adlaAccountName);
                adlaParameters.setProperties(adlaProperties);
        
                    /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */
        
                _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
            }
        
            //todo: this changes in the next version of the API
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Submit a U-SQL job by providing script contents.
            // Returns the job ID
            public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
                UUID jobId = java.util.UUID.randomUUID();
                USqlJobProperties properties = new USqlJobProperties();
                properties.setScript(script);
                JobInformation parameters = new JobInformation();
                parameters.setName(jobName);
                parameters.setJobId(jobId);
                parameters.setType(JobType.USQL);
                parameters.setProperties(properties);
        
                JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();
        
                return jobId;
            }
        
            // Wait for job completion
            public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                while (jobInfo.getState() != JobState.ENDED)
                {
                    jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
                }
                return jobInfo.getResult();
            }
        
            // Get job status
            public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                return jobInfo.getState().toValue();
            }
        }

6. Siga las indicaciones para ejecutar y completar la solicitud.


## <a name="see-also"></a>Vea también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestaña en la parte superior de la página.
- Para ver una consulta más compleja, vea [registros de sitio Web de analizar mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones SQL U, vea [secuencias de comandos SQL de U desarrollar mediante datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener SQL U, consulte [referencia del lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348)y [empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, vea [Administrar Azure datos lago el análisis con el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener una visión general de análisis de lago de datos, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).
