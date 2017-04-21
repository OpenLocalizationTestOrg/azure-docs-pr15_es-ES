<properties
    pageTitle="Tutorial de la API de REST de supervisión de Azure | Microsoft Azure"
    description="Cómo autenticar solicitudes y usar la API de REST de supervisión de Azure."
    authors="mcollier, rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Tutorial de la API de REST de supervisión de Azure
En este artículo se muestra cómo realizar la autenticación de modo que el código puede utilizar la [Referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

La API de Monitor de Azure permite mediante programación recuperar las definiciones de métrica predeterminada disponible (el tipo de métrica como tiempo de CPU, las solicitudes, etc.), detalle y valores de métrica. Una vez recuperados, los datos se pueden guardar en un almacén de datos independiente como base de datos de SQL Azure, DocumentDB o lago de datos de Azure. Desde allí, se puede realizar un análisis adicional según sea necesario.

Además de trabajar con varios puntos de datos métrica, tal como se muestra en este artículo, la API Monitor hace posible la lista de reglas de alertas, ver registros de actividad y mucho más. Para obtener una lista completa de las operaciones disponibles, vea la [Referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autenticación de solicitudes de Azure Monitor

El primer paso es autenticar la solicitud.

Todas las tareas que se ejecuta en la API de Monitor de Azure usar el modelo de autenticación del Administrador de recursos de Azure. Por lo tanto, se deben autenticar todas las solicitudes con Azure Active Directory (AD Azure). Un enfoque para autenticar la aplicación cliente es crear un anuncio Azure principal del servicio y recuperar el símbolo de autenticación (JWT). El siguiente script de ejemplo muestra cómo crear un servicio de Azure AD principal a través de PowerShell. Para un análisis más detallada, consulte la documentación sobre el [uso de PowerShell de Azure para crear un servicio principal para tener acceso a los recursos](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). También es posible [crear un principio del servicio a través del portal de Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar la API de Monitor de Azure, la aplicación cliente debe usar al capital servicio creado previamente para autenticar. El ejemplo siguiente script de PowerShell muestra un enfoque, con [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) para empezar la autenticación JWT token. El token JWT se pasa como parte de un parámetro de autorización de HTTP en solicitudes a la API de REST de Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Una vez completado el paso de la configuración de autenticación, las consultas se pueden ejecutar con la API de REST de Azure Monitor. Hay dos consultas útiles:

1. Las definiciones de métrica para un recurso de la lista

2. Recuperar los valores de métrica


## <a name="retrieve-metric-definitions"></a>Recuperar las definiciones de métricas
>[AZURE.NOTE] Para recuperar definiciones métricas con la API de REST de Azure Monitor, use "2016-03-01" como la versión de la API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Para una aplicación de lógica de Azure, las definiciones de métrica tendría un aspecto similares a la siguiente captura de pantalla:

![ALT ""JSON vista de respuesta de definición métrica."](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Para obtener más información, consulte la documentación de [la métricas definiciones de un recurso en la API de REST de Azure Monitor de la lista](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperar valores de métrica
Una vez se conocen las definiciones de métricas disponibles, es posible recuperar los valores de métrica relacionados. Usar nombre 'value' de la métrica (no ' localizedValue') para las solicitudes filtradas (por ejemplo, recuperar los puntos de datos métrica 'CpuTime' y 'Solicita'). Si no se especifica ningún filtro, se devuelve la métrica predeterminada.

>[AZURE.NOTE] Para recuperar los valores de métrica con la API de REST de Azure Monitor, use "2016-06-01" como la versión de la API.

**Método**: GET

**Solicitar URI**: https://management.azure.com/subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/_{nombres de proveedor de recursos}_/_{tipo de recurso}_/_{nombre del recurso}_/providers/microsoft.insights/metrics?$filter=_{filtro}_& versión api =_{apiVersion}_

Por ejemplo, para recuperar los puntos de datos métrica de RunsSucceeded para el intervalo de tiempo determinado y para un grano de tiempo de 1 hora, la solicitud sería como sigue:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

El resultado tendría un aspecto similar al ejemplo siguiente captura de pantalla:

![ALT "Respuesta JSON que muestra el valor de métrica de tiempo medio de respuesta"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Para recuperar varios puntos de datos o agregación, agregue los nombres de definición de métrica y tipos de agregación para el filtro, tal como se muestra en el ejemplo siguiente:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Usar ARMClient
Una alternativa al uso de PowerShell (tal como se muestra arriba), es usar [ARMClient](https://github.com/projectkudu/ARMClient) en el equipo de Windows. ARMClient controla la autenticación de Azure AD (y token JWT resultante) automáticamente. Uso de ARMClient para recuperar datos métricas de esquema de los siguientes pasos:

1. Instale [Chocolatey](https://chocolatey.org/) y [ARMClient](https://github.com/projectkudu/ARMClient).

2. En una ventana de terminal, escriba _armclient.exe inicio de sesión_. Esto le pide que inicie sesión en Azure.

3. Tipo de _armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Tipo de _armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![ALT "Usar ARMClient para trabajar con la API de REST de supervisión Azure"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Recuperar el identificador de recursos
Con la API de REST puede ayudarle a entender las definiciones de métricas disponibles, detalle y valores relacionados. Esta información es útil cuando se usa la [Biblioteca de administración de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

El código anterior, el identificador de recursos para usar es la ruta completa al recurso Azure deseado. Por ejemplo, para consultar en una aplicación Web de Azure, el identificador del recurso sería:

*/subscriptions/{Subscription-ID}/resourceGroups/{Resource-Group-Name}/Providers/Microsoft.Web/Sites/{Site-Name}/*

La siguiente lista contiene algunos ejemplos de formatos de identificador de recursos para diversos recursos Azure:

* **Concentrador IoT** - /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.Devices/IotHubs/_{nombre de concentrador iot}_

* **Elástico grupo SQL** - /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.Sql/servers/_{grupo db}_/elasticpools/_{nombre sql del grupo}_

* **Base de datos de SQL (v12)** - /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.Sql/servers/_{nombre del servidor}_/databases/_{nombre de la base de datos}_

* **Bus de servicio** : /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.ServiceBus/_{nombres}_/_{nombre de servicebus}_

* **Conjuntos de escala de VM** - /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{nombre de vm}_

* **Máquinas virtuales** - /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.Compute/virtualMachines/_{nombre de vm}_

* **Evento Hubs** - /subscriptions/_{Id. de suscripción}_/resourceGroups/_{nombre de grupo de recursos}_/providers/Microsoft.EventHub/namespaces/_{eventhub nombres}_


Existen enfoques alternativos para recuperar el identificador del recurso, incluidos con el Explorador de recursos de Azure, ver el recurso deseado en el portal de Azure y a través de PowerShell o la CLI de Azure.

### <a name="azure-resource-explorer"></a>Explorador de recursos de Azure
Para encontrar el identificador de recursos de un recurso que desee, un enfoque útil es usar la herramienta de [Explorador de recursos de Azure](https://resources.azure.com) . Vaya al recurso que desee y, a continuación, mire el identificador que se muestra, como se muestra en la siguiente captura de pantalla:

![ALT "Explorador de Azure recursos"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal de Azure
También puede obtenerse el identificador de recursos desde el portal de Azure. Para ello, vaya al recurso que desee y, a continuación, seleccione Propiedades. El identificador del recurso se muestra en la hoja de propiedades, como se muestra en la siguiente captura de pantalla:

![ALT "identificador de recurso muestran en la hoja de propiedades en el portal de Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
El identificador de recursos se puede recuperar mediante cmdlets de PowerShell de Azure también. Por ejemplo, para obtener el identificador de recursos para una aplicación Web de Azure, ejecute el cmdlet Get-AzureRmWebApp, como se muestra en la siguiente captura de pantalla:

![ALT "Id. de recurso que se obtienen mediante PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>CLI de Azure
Para recuperar el identificador de recursos con CLI Azure, ejecute el comando 'Web App azure mostrar', que especifica la '--json' opción, como se muestra en la siguiente captura de pantalla:

![ALT "Id. de recurso que se obtienen mediante PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Recuperar datos de registro de actividad
Además de trabajar con definiciones de métrica y valores relacionados, también es posible recuperar adicionales perspectivas interesantes relacionados con recursos de Azure. Por ejemplo, es posible consultar los datos de [registro de actividad](https://msdn.microsoft.com/library/azure/dn931934.aspx) . En el ejemplo siguiente se muestra el uso de la API de REST de Azure Monitor para consultar los datos de registro de actividad dentro de un intervalo de fechas específico para una suscripción de Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Pasos siguientes
* Revise la [información general sobre supervisión](monitoring-overview.md).
* Ver las [mediciones compatibles con el Monitor de Azure](monitoring-supported-metrics.md).
* Revise la [Referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Revise la [biblioteca de administración de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
