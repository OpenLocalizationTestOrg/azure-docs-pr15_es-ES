<properties
    pageTitle="Usar PowerShell para crear alertas para los servicios de Azure | Microsoft Azure"
    description="Usar PowerShell para crear alertas de Azure, que pueden desencadenar automatización o notificaciones cuando se cumplen las condiciones especificadas."
    authors="rboucher"
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Usar PowerShell para crear alertas para los servicios de Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Información general

Este artículo le muestra cómo configurar las alertas de Azure con PowerShell.  

Puede recibir una alerta basada en supervisión métricas para o eventos en los servicios de Azure.

- **Valores de métrica** : desencadenadores de alerta cuando el valor de una métrica especificado cruza un umbral que asignar en cualquier dirección. Es decir, active ambas cuando la condición se cumple en primer lugar y, a continuación, posteriormente cuando condición que ya no se cumple.    
- **Eventos de registro de actividad** : puede desencadenar una alerta en *todos* los eventos o, solo cuando se produce un número determinado de eventos.

Puede configurar una alerta para cuando Active hacer lo siguiente:

- enviar notificaciones de correo electrónico al administrador del servicio y coadministradores
- enviar correo electrónico a mensajes de correo electrónico adicionales que especifique.
- llamar a un webhook
- iniciar la ejecución de un runbook Azure (solo desde el portal de Azure)

Puede configurar y obtener información sobre cómo usar reglas de alertas

- [Portal de Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [línea de comandos de interfaz](insights-alerts-command-line-interface.md)
- [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Para obtener información adicional, siempre puede escribir ```get-help``` y, a continuación, el comando de PowerShell que desea obtener ayuda.

## <a name="create-alert-rules-in-powershell"></a>Crear reglas de alerta en PowerShell

1. Inicie sesión en Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Obtener una lista de las suscripciones tiene disponible. Compruebe que está trabajando con la suscripción a la derecha. Si no es así, puede configurarlo a la derecha con el resultado de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  Para mostrar las reglas existentes en un grupo de recursos, utilice el siguiente comando:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Para crear una regla, debe tener varios datos importantes en primer lugar. 
    - El **Identificador de recursos** para el recurso que desea establecer una alerta para
    - Las **definiciones de métrica** disponible para ese recurso

    Una forma de obtener el identificador del recurso es usar el portal de Azure. Suponiendo que ya se ha creado el recurso, selecciónela en el portal. A continuación, en el módulo siguiente, seleccione *Propiedades* en la sección *configuración* . El identificador del recurso es un campo en el módulo siguiente. Otra forma utilizar el [Explorador de recursos de Azure](https://resources.azure.com/).

    Es un identificador de recursos de ejemplo para una aplicación web

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Puede usar `Get-AzureRmMetricDefinition` para ver la lista de todas las definiciones de métrica para un recurso específico.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    En el ejemplo siguiente se genera una tabla con el nombre de métrica y la unidad de esa métrica.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    Una lista completa de opciones disponibles para obtener AzureRmMetricDefinition está disponible si ejecuta Get-MetricDefinitions.


5. En el ejemplo siguiente se establece una alerta en un recurso de sitio web. Desencadenadores de alerta cada vez que recibe constantemente el tráfico de 5 minutos y cuando no reciba tráfico durante 5 minutos.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Para crear webhook o enviar correo electrónico cuando se activa una alerta, cree primero el correo electrónico o el webhooks. A continuación, inmediatamente cree la regla posteriormente con-etiqueta acciones y como se muestra en el ejemplo siguiente. No se puede asociar webhook o correos electrónicos con ya ha creado reglas a través de PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. Para crear una alerta que desencadene una condición específica en el registro de actividad, use los comandos de la forma siguiente

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName corresponde a un tipo de evento en el registro de actividad. Por ejemplo, *Microsoft.Compute/virtualMachines/delete* y *microsoft.insights/diagnosticSettings/write*.

    Puede usar el comando PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) para obtener una lista de posibles operationNames. Como alternativa, puede usar el portal de Azure para el registro de actividad de la consulta y encontrar específicos más allá de las operaciones que desea crear una alerta para. Las operaciones que se muestra en la vista de gráfico de registro de los nombres descriptivos. Buscar en el JSON para la entrada y saque el valor OperationName.   

8. Compruebe que se han creado correctamente las alertas consultando las reglas individuales.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Eliminar las alertas. Estos comandos eliminar las reglas que creó anteriormente en este artículo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Obtenga una visión general de supervisión Azure](monitoring-overview.md) incluidos los tipos de información puede recopilar y supervisar.
* Más información sobre la [configuración webhooks en alertas](insights-webhooks-alerts.md).
* Más información sobre [Runbooks de automatización de Azure](..\automation\automation-starting-a-runbook.md).
* Obtenga una [Visión general de recopilación de registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recopilar métricas detalladas de alta frecuencia en el servicio.
* Obtenga una [Visión general de la colección de métricas](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
