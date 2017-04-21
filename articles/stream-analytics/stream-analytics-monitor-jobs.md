<properties
    pageTitle="Supervisar mediante programación de trabajos en análisis de secuencia | Microsoft Azure"
    description="Obtenga información sobre cómo supervisar mediante programación de trabajos de análisis de secuencia creados mediante las API de REST, Azure SDK o Powershell."
    keywords="monitor de .net, supervisar trabajo de aplicación"
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


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Crear mediante programación un monitor de análisis de flujo de trabajo
 En este artículo muestra cómo habilitar la supervisión de una tarea de análisis de la secuencia. No trabajos creados mediante las API de REST, Azure SDK o Powershell hacer el análisis de secuencia supervisión ha habilitado de forma predeterminada.  Puede habilitar manualmente esto en el Portal de Azure navegar a la página del Monitor del trabajo y haciendo clic en el botón Habilitar, o puede automatizar este proceso siguiendo los pasos de este artículo. En la pestaña "Monitor" en el Portal de Azure para el trabajo de análisis de secuencia se muestran los datos de supervisión.

![monitor de trabajo ficha trabajos](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este artículo, debe tener el siguiente:

- Visual Studio 2012 o 2013.
- Descargue e instale el [SDK de .NET de Azure](https://azure.microsoft.com/downloads/).
- Una tarea de análisis de secuencia existente que necesita supervisión habilitado.

## <a name="setup-a-project"></a>Configuración de un proyecto

1.  Crear una aplicación de consola de Visual Studio C# .net.
2.  En la consola del Administrador de paquetes, ejecute los comandos siguientes para instalar los paquetes de NuGet. El primero es el SDK de .NET Management de Azure secuencia análisis. La segunda es el SDK de Monitor de Azure que se usará para habilitar la supervisión. El último es el cliente de Azure Active Directory que se usará para la autenticación.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Agregue la siguiente sección appSettings al archivo App.config.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Reemplazar valores para *SubscriptionId* y *ActiveDirectoryTenantId* con su suscripción de Azure e identificadores de inquilinos. Puede obtener estos valores ejecutando el siguiente cmdlet de PowerShell:

    ```
    Get-AzureAccount
    ```
4.  Agregue las siguientes instrucciones using al archivo de origen (Program.cs) en el proyecto.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Agregar un método de autenticación auxiliar.

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

## <a name="create-management-clients"></a>Crear a clientes de administración
El siguiente código configurará los clientes de administración y las variables necesarias.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Habilitar la supervisión de un proyecto existente de análisis de secuencia

El siguiente código permitirá la supervisión de un proyecto de análisis de secuencia **existente** . La primera parte del código realiza una solicitud GET con el servicio de análisis de secuencia para recuperar información sobre el trabajo de análisis de secuencia concreto. Utiliza la propiedad "Id" (recuperada de la solicitud GET) como un parámetro para el método de ubicación en la segunda mitad del código que envía un SUPERPONER solicitar al servicio perspectivas para habilitar la supervisión para el trabajo de análisis de la secuencia.

> [AZURE.WARNING]
> Si previamente ha habilitado la supervisión de un trabajo de análisis de secuencia diferente a través del Portal de Azure o mediante programación a través de la debajo de código, **se recomienda que proporcione el mismo nombre de cuenta de almacenamiento que hizo cuando habilitó anteriormente supervisión.**
>
> La cuenta de almacenamiento está vinculada a la región que creó el trabajo de análisis de secuencia de, no específicamente para la tarea.
>
> Todos los análisis de flujo de trabajo (y otros recursos de Azure) en esa misma región compartan esta cuenta de almacenamiento para almacenar los datos de supervisión. Si proporciona una cuenta de almacenamiento diferente, puede provocar efectos secundarios no deseados a la supervisión de sus otras tareas de análisis de flujo y otros recursos de Azure.
>
> El nombre de la cuenta de almacenamiento utilizado para reemplazar ```“<YOUR STORAGE ACCOUNT NAME>”``` a continuación debe ser una cuenta de almacenamiento que está en la misma suscripción como el análisis de flujo de trabajo se habilita la supervisión de.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obtener soporte técnico
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
