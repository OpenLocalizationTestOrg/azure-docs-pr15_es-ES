<properties
    pageTitle="Ejemplos de inicio rápido de Azure PowerShell Monitor. | Microsoft Azure"
    description="Usar PowerShell para tener acceso a características de Azure Monitor como Autoescala, alertas, webhooks y buscar registros de actividad."
    authors="kamathashwin"
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
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Ejemplos de inicio rápido de Azure Monitor PowerShell

Este artículo se demuestra que muestra los comandos de PowerShell para ayudarle a tener acceso a características de Azure Monitor. Monitor de Azure le permite Autoescala Cloud Services, máquinas virtuales y aplicaciones Web y para enviar alertas o llamar según los valores de los datos de telemetría configurado con las direcciones URL de web.

>[AZURE.NOTE] Monitor de Azure es el nuevo nombre de lo que se denomina "Azure perspectivas" hasta 25 de septiembre de 2016. Sin embargo, los espacios de nombres y, por lo tanto, los comandos siguientes contengan "información".

## <a name="set-up-powershell"></a>Configurar PowerShell
Si todavía no lo ha hecho, configure PowerShell para que se ejecute en el equipo. Para obtener más información, vea [cómo instalar y configurar PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Ejemplos de este artículo

Los ejemplos en el artículo ilustran cómo puede usar cmdlets de Azure Monitor. También puede revisar la lista completa de los cmdlets de PowerShell de Monitor de Azure en [Cmdlets de Azure Monitor (perspectivas)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Iniciar sesión y usar suscripciones

En primer lugar, inicie sesión en su suscripción de Azure.

```PowerShell
Login-AzureRmAccount
```

Esto requiere que inicie sesión en. Una vez hecho, su cuenta, se muestran TenantId e Id. de suscripción predeterminado. Todos los cmdlets de Azure funcionan en el contexto de la suscripción de forma predeterminada. Para ver la lista de suscripciones que tiene acceso, use el siguiente comando.

```PowerShell
Get-AzureRmSubscription
```

Para cambiar el contexto de su trabajo a una suscripción diferente, use el siguiente comando.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Recuperar los registros de auditoría para una suscripción
Usar el `Get-AzureRmLog` cmdlet.  A continuación se muestran algunos ejemplos.

Obtener las entradas del registro de esta fecha y hora para presentar:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obtener las entradas del registro entre un intervalo de fecha y hora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtener las entradas del registro de un grupo de recursos específico:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obtener las entradas del registro de un proveedor de recursos específico entre un intervalo de fecha y hora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtener todas las entradas de registro con una persona que llama específico:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

El comando siguiente recupera la última 1000 eventos desde el registro de auditoría:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`es compatible con muchos otros parámetros. Consulte la `Get-AzureRmLog` referencia para obtener más información.

>[AZURE.NOTE] `Get-AzureRmLog`solo proporciona 15 días de historial. Usar el parámetro **- MaxEvents** le permite los últimos eventos N, más allá de los 15 días de la consulta. Eventos de acceso más de 15 días, use la API de REST o SDK (ejemplo C# mediante el SDK). Si no incluye la **hora de inicio**, el valor predeterminado es **hora de finalización** menos una hora. Si no incluye la **hora de finalización**, el valor predeterminado es hora actual. Todas las horas son en UTC.

## <a name="retrieve-alerts-history"></a>Recuperar el historial de alertas
Para ver todos los eventos de alerta, puede consultar los registros del Administrador de recursos de Azure con los siguientes ejemplos.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver el historial de una regla de alerta específica, puede usar el `Get-AzureRmAlertHistory` cmdlet, pasando el identificador de recursos de la regla de alerta.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

La `Get-AzureRmAlertHistory` cmdlet es compatible con distintos parámetros. Obtener más información, vea [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Obtener información sobre las reglas de alertas
Todos los comandos siguientes actuarán en un grupo de recursos denominado "montest".

Ver todas las propiedades de la regla de alerta:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperar todas las alertas de un grupo de recursos:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperar todas las reglas de alertas para un recurso de destino. Por ejemplo, todas las reglas de alerta se establece en una máquina virtual.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`es compatible con otros parámetros. Para obtener más información, vea [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-alert-rules"></a>Crear reglas de alertas
Puede usar el `Add-AlertRule` cmdlet para crear, actualizar o desactivar una regla de alerta.

Puede crear propiedades de correo electrónico y webhook mediante `New-AzureRmAlertRuleEmail` y `New-AzureRmAlertRuleWebhook`, respectivamente. En el cmdlet de regla de alerta, asignar estas como acciones a la propiedad de **acciones** de la regla de alerta.

La siguiente sección contiene un ejemplo que muestra cómo crear una regla de alerta con distintos parámetros.

### <a name="alert-rule-on-a-metric"></a>Regla de alertas en una métrica
La siguiente tabla describe los parámetros y valores utilizados para crear una alerta mediante una métrica.


|parámetro|valor|
|---|---|
|Nombre|  simpletestdiskwrite|
|Ubicación de esta regla de alerta|   Estados Unidos oriental|
|ResourceGroup| montest|
|TargetResourceId|  /subscriptions/s1/resourceGroups/montest/Providers/Microsoft.COMPUTE/virtualMachines/testconfig|
|MetricName de la alerta que se crea|   \Disk \PhysicalDisk (_Total) / seg. Consulte la `Get-MetricDefinitions` cmdlet debajo sobre cómo recuperar los nombres de métrica exactos|
|operador|  Mayor que|
|Valor de umbral (recuento/seg en para esta métrica)|    1|
|WindowSize (formato HH)|  05:00:00|
|agregador (estadístico de la métrica, que utiliza el recuento de promedio, en este caso)|  Promedio|
|mensajes de correo electrónico personalizados (cadena)|'foo@example.com','bar@example.com'|
|enviar correo electrónico a los propietarios y colaboradores a los lectores|    -SendToServiceOwners|

Crear una acción de correo electrónico

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Crear una acción Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Crear la regla de alerta en la métrica de % de CPU en una máquina virtual clásica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar la regla de alerta

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

El cmdlet de alerta de agregar, también actualiza la regla si ya existe una regla de alerta para las propiedades. Para desactivar una regla de alerta, incluya el parámetro **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Alerta de evento de registro de auditoría

>[AZURE.NOTE] Esta característica aún está en vista previa.

En este escenario, podrá enviar correo electrónico cuando un sitio Web se inicia correctamente en mi suscripción en el grupo de recursos *abhingrgtest123*.

Configurar una regla de correo electrónico

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurar una regla de webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Crear la regla en el evento

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recuperar la regla de alerta

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

La `Add-AlertRule` cmdlet permite otros parámetros. Obtener más información, vea [Agregar AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obtener una lista de métricas disponibles para las alertas
Puede usar el `Get-AzureRmMetricDefinition` cmdlet para ver la lista de todas las medidas para un recurso específico.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

En el ejemplo siguiente se genera una tabla con el nombre de métrica y la unidad para el mismo.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Una lista completa de opciones disponibles para `Get-AzureRmMetricDefinition` está disponible en [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Crear y administrar la configuración de Autoescala
Un recurso, como una aplicación Web, VM, servicio de nube o Configurar escala de VM puede tener solo una configuración de Autoescala para él.
Sin embargo, cada opción Autoescala puede tener varios perfiles. Por ejemplo, uno para un perfil de escala basado en el rendimiento y una segunda para una programación según el perfil. Cada perfil puede tener varias reglas configuradas en él. Para obtener más información sobre cómo ajustar automáticamente, vea [cómo Autoescala una aplicación](../cloud-services/cloud-services-how-to-scale.md).

Estos son los pasos que se utilizará:

1. Cree reglas.
2. Crear la asignación de las reglas que ha creado previamente a los perfiles de perfiles.
3. Opcional: Crear las notificaciones de Autoescala configurando propiedades webhook y correo electrónico.
4. Crear una configuración de Autoescala con un nombre en el recurso de destino mediante la asignación de los perfiles y las notificaciones que creó en los pasos anteriores.

Los siguientes ejemplos muestran cómo crear una configuración de Autoescala de una escala de máquina virtual para un sistema operativo de Windows según empleando la métrica de uso de CPU.

Primero, cree una regla de escalado, con un aumento de recuento de la instancia.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

A continuación, cree una regla de escala de, con una disminución del recuento de instancia.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

A continuación, cree un perfil para las reglas.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crear una propiedad de webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crear la propiedad de notificación para la configuración de Autoescala, incluido el correo electrónico y la webhook que creó anteriormente.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por último, cree la configuración de ajustar automáticamente al agregar el perfil que creó anteriormente.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obtener más información acerca de cómo administrar la configuración de Autoescala, vea [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Autoescala historial
En el ejemplo siguiente se muestra cómo puede ver Autoescala reciente y eventos de alerta. Use la búsqueda de registros de auditoría para ver el historial de Autoescala.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Puede usar el `Get-AzureRmAutoScaleHistory` cmdlet para recuperar Autoescala historial.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obtener más información, vea [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Ver detalles de una configuración de Autoescala
Puede usar el `Get-Autoscalesetting` cmdlet para recuperar más información acerca de la opción Ajustar automáticamente.

En el ejemplo siguiente se muestra detalles acerca de todas las configuraciones de Autoescala en el grupo de recursos 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

En el ejemplo siguiente se muestra información detallada sobre todas las configuraciones de Autoescala en el grupo de recursos 'myrg1' y específicamente la configuración de Autoescala denominada 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Quitar una opción Autoescala
Puede usar el `Remove-Autoscalesetting` cmdlet para eliminar una configuración de Autoescala.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Administrar perfiles de registro para los registros de auditoría

Puede crear un *perfil de registro* y exportar datos de los registros de auditoría a una cuenta de almacenamiento y configurar la retención de datos para el mismo. Si lo desea, también puede transmitir los datos a su centro de evento. Tenga en cuenta que esta característica está actualmente en la vista previa y solo puede crear un perfil de registro por suscripción. Puede usar los siguientes cmdlets con su suscripción actual para crear y administrar perfiles de registro. También puede elegir una determinada suscripción. Aunque PowerShell valores predeterminados para la suscripción actual, siempre puede cambiar dicho usando `Set-AzureRmContext`. Puede configurar los registros de auditoría para enrutar los datos a cualquier cuenta de almacenamiento o evento concentrador dentro de la suscripción. Los datos se escriben como archivos blob en formato JSON.

### <a name="get-a-log-profile"></a>Obtener un perfil de registro
Para capturar los perfiles de registro existente, use la `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Agregar un perfil de registro sin retención de datos

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Quitar un perfil de registro

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Agregar un perfil de registro con retención de datos

Puede especificar la propiedad **- RetentionInDays** con el número de días, como un entero positivo, donde se conservan los datos.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Agregar el perfil de registro con EventHub y retención
Además de enrutar los datos a la cuenta de almacenamiento, también puede transmitirlo a un concentrador de evento. Tenga en cuenta que en esta versión de vista previa y el almacenamiento de la configuración de la cuenta es obligatorio pero la configuración de concentrador de evento es opcional.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar los registros de diagnóstico
Muchos servicios de Azure proporcionan registros adicionales y telemetría, incluidos los grupos de seguridad de red de Azure, equilibradores de carga de Software, depósito clave, servicios de búsqueda de Azure y lógica de aplicaciones y se pueden configurar para guardar los datos en su cuenta de almacenamiento de Azure. Esta operación sólo puede realizarse en un nivel de recursos y la cuenta de almacenamiento debe aparecer en la misma región como el recurso de destino donde se configura la configuración de diagnósticos.

### <a name="get-diagnostic-setting"></a>Obtener la configuración de diagnóstico

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Deshabilitar la configuración de diagnóstico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilita diagnóstico sin retención

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilita diagnóstico con retención

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilita diagnóstico con retención de una categoría de registro específico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
