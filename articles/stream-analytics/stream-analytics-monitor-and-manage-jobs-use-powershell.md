<properties 
    pageTitle="Supervisar y administrar trabajos de análisis de secuencia con PowerShell | Microsoft Azure" 
    description="Obtenga información sobre cómo usar PowerShell de Azure y los cmdlets para supervisar y administrar trabajos de análisis de secuencia." 
    keywords="powershell Azure, cmdlets de powershell de azure, comandos de powershell, secuencias de comandos de powershell" 
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


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Supervisar y administrar trabajos de análisis de secuencia con los cmdlets de PowerShell de Azure

Obtenga información sobre cómo supervisar y administrar los recursos de análisis de secuencia con los cmdlets de PowerShell de Azure y secuencias de comandos de powershell que se ejecutan tareas básicas de análisis de secuencia.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Requisitos previos para ejecutar los cmdlets de PowerShell de Azure para análisis de secuencia

 - Crear un grupo de recursos de Azure en la suscripción. A continuación se muestra una secuencia de comandos de PowerShell de Azure. Para obtener información de Azure PowerShell, vea [instalar y configurar Azure PowerShell](../powershell-install-configure.md);  

PowerShell Azure 0.9.8:  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Trabajos de análisis de secuencia creados mediante programación no tienen supervisión habilitado de forma predeterminada.  Puede habilitar la supervisión en el Portal de Azure navegar a la página del Monitor del trabajo y haciendo clic en el botón habilitar manualmente o mediante programación hacerlo siguiendo los pasos que se encuentra en [Los análisis de secuencia de Azure - Monitor secuencia de análisis de trabajos de programación](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlets de PowerShell Azure para análisis de secuencia
Los siguientes cmdlets de PowerShell de Azure puede usarse para supervisar y administrar trabajos de análisis de secuencia de Azure. Tenga en cuenta que Azure PowerShell diferentes versiones. 
**En los ejemplos que muestran el primer comando es para Azure PowerShell 0.9.8, el segundo comando es para Azure PowerShell 1.0.** Los comandos de PowerShell 1.0 de Azure siempre tendrá "AzureRM" en el comando.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Obtiene información sobre el trabajo sobre un trabajo específico dentro de un grupo de recursos o enumera todos los trabajos de análisis de secuencia definidos en la suscripción de Azure o grupo de recursos especificado.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Este comando PowerShell devuelve información acerca de los trabajos de análisis de secuencia de la suscripción de Azure.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Este comando PowerShell devuelve información acerca de los trabajos de análisis de secuencia en el grupo de recursos StreamAnalytics predeterminado Central EE.

**Ejemplo 3**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Este comando PowerShell devuelve información acerca del trabajo de análisis de secuencia StreamingJob en el grupo de recursos StreamAnalytics predeterminado Central EE.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Enumera todas las entradas que se definen en una tarea de análisis de secuencia especificada u obtiene información sobre una entrada específica.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando PowerShell devuelve información acerca de todas las entradas que se definen en el trabajo StreamingJob.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Este comando PowerShell devuelve información acerca de la entrada denominada EntryStream definido en el trabajo StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Enumera todas las salidas que se definen en una tarea de análisis de secuencia especificada u obtiene información sobre un resultados específicos.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando PowerShell devuelve información acerca de los resultados que se definen en el trabajo StreamingJob.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Este comando PowerShell devuelve información acerca de los resultados de la llamada de salida definido en el trabajo StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtiene información sobre la cuota de transmisión de unidades en una región determinada.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Este comando PowerShell devuelve información sobre la cuota y el uso de unidades streaming en la región Central de EE..

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtiene información sobre una transformación específica definida en una tarea de análisis de la secuencia.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Este comando PowerShell devuelve información acerca de la transformación denominada StreamingJob en el trabajo StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Nuevo AzureStreamAnalyticsInput | Nueva AzureRMStreamAnalyticsInput
Crea una nueva entrada de una tarea de análisis de la secuencia o actualiza una entrada existente especificada.
  
Puede especificar el nombre de la entrada en el archivo .json o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe ser el mismo que del archivo.

Si especifica una entrada que ya existe y no se especifica forzar parámetro –, el cmdlet le preguntará si desea reemplazar la entrada existente o no.

Si especifica – forzar el parámetro y especifique el nombre de entrada de una existente, se reemplazará la entrada sin confirmación.

Para obtener información detallada sobre la estructura del archivo JSON y contenido, consulte la [Entrada de crear (análisis de secuencia de Azure)] [ msdn-rest-api-create-stream-analytics-input] sección de la [biblioteca de referencia de API REST de administración de secuencia análisis][stream.analytics.rest.api.reference].

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Este comando de PowerShell, crea una nueva entrada del archivo Input.json. Si ya se ha definido una entrada existente con el nombre especificado en el archivo de definición de entrada, el cmdlet le preguntará si desea reemplazarlo o no.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Este comando de PowerShell, crea una nueva entrada en el trabajo de EntryStream. Si ya se ha definido una entrada existente con este nombre, el cmdlet le preguntará si desea reemplazarlo o no.

**Ejemplo 3**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Este comando PowerShell reemplaza la definición del origen de entrada existente llamado EntryStream con la definición del archivo.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Nuevo AzureStreamAnalyticsJob | Nueva AzureRMStreamAnalyticsJob
Crea una nueva tarea de análisis de secuencia de Microsoft Azure o actualiza la definición de una tarea existente especificada.

Puede especificar el nombre de la tarea en el archivo .json o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe ser el mismo que del archivo.

Si especifica un nombre de trabajo que ya existe y no se especifica forzar parámetro –, el cmdlet le preguntará si desea reemplazar el trabajo existente o no.

Si especifica – forzar el parámetro y especifique un nombre de trabajo existente, se reemplazará la definición de trabajo sin confirmación.

Para obtener información detallada sobre la estructura del archivo JSON y contenido, consulte [Crear un trabajo de análisis de secuencia] [ msdn-rest-api-create-stream-analytics-job] sección de la [biblioteca de referencia de API REST de administración de secuencia análisis][stream.analytics.rest.api.reference].

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Este comando de PowerShell, crea un nuevo trabajo de la definición del JobDefinition.json. Si ya se ha definido un trabajo con el nombre especificado en el archivo de definición de trabajo existente, el cmdlet le preguntará si desea reemplazarlo o no.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Este comando PowerShell reemplaza la definición de trabajo para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Nuevo AzureStreamAnalyticsOutput | Nueva AzureRMStreamAnalyticsOutput
Crea una nueva salida dentro de una tarea de análisis de la secuencia o actualiza un resultado existente.  

El nombre de la salida se puede especificar en el archivo .json o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe ser el mismo que del archivo.

Si especifica un resultado que ya existe y no se especifica forzar parámetro –, el cmdlet le preguntará si desea reemplazar la salida existente o no.

Si especifica – forzar el parámetro y especifique el nombre de salida de una existente, el resultado se reemplazará sin confirmación.

Para obtener información detallada sobre la estructura del archivo JSON y contenido, consulte la [Salida crear (análisis de secuencia de Azure)] [ msdn-rest-api-create-stream-analytics-output] sección de la [biblioteca de referencia de API REST de administración de secuencia análisis][stream.analytics.rest.api.reference].

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Este comando PowerShell crea una nueva llamada "salida" en el trabajo StreamingJob de salida. Si ya se ha definido un resultado existente con este nombre, el cmdlet le preguntará si desea reemplazarlo o no.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Este comando PowerShell reemplaza la definición de "salida" en el trabajo StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Nuevo AzureStreamAnalyticsTransformation | Nueva AzureRMStreamAnalyticsTransformation
Crea una nueva transformación dentro de una tarea de análisis de la secuencia o actualiza la transformación existente.
  
Puede especificar el nombre de la transformación en el archivo .json o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe ser el mismo que del archivo.

Si especifica una transformación que ya existe y no se especifica forzar parámetro –, el cmdlet le preguntará si desea reemplazar la transformación existente o no.

Si especifica – forzar el parámetro y especifique un nombre de transformación existente, se reemplazará la transformación sin confirmación.

Para obtener información detallada sobre la estructura del archivo JSON y contenido, consulte la [Transformación crear (análisis de secuencia de Azure)] [ msdn-rest-api-create-stream-analytics-transformation] sección de la [biblioteca de referencia de API REST de administración de secuencia análisis][stream.analytics.rest.api.reference].

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Este comando PowerShell crea una nueva transformación denominada StreamingJobTransform en el trabajo StreamingJob. Si ya está definida una transformación existente con este nombre, el cmdlet le preguntará si desea reemplazarlo o no.

**Ejemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Este comando PowerShell reemplaza la definición de StreamingJobTransform en el trabajo StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Quitar AzureStreamAnalyticsInput | Quitar AzureRMStreamAnalyticsInput
Forma asincrónica elimina una entrada específica de una tarea de análisis de secuencia de Microsoft Azure.  
Si especifica forzar parámetro –, la entrada se eliminarán sin confirmación.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Este comando de PowerShell, quita la entrada EventStream en el trabajo StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Quitar AzureStreamAnalyticsJob | Quitar AzureRMStreamAnalyticsJob
Forma asincrónica elimina una tarea de análisis de secuencia específica en Microsoft Azure.  
Si especifica forzar parámetro –, se eliminará el trabajo sin confirmación.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando PowerShell quita el trabajo StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Quitar AzureStreamAnalyticsOutput | Quitar AzureRMStreamAnalyticsOutput
Forma asincrónica elimina un resultado específico de una tarea de análisis de secuencia de Microsoft Azure.  
Si especifica – parámetro forzar, el resultado se eliminarán sin confirmación.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

El resultado de salida en el trabajo StreamingJob este quita el comando PowerShell.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Inicio AzureStreamAnalyticsJob | Inicio AzureRMStreamAnalyticsJob
Asincrónica implementa e inicia una tarea de análisis de secuencia de Microsoft Azure.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Este comando PowerShell inicia el trabajo StreamingJob con una hora de inicio de salida personalizada establecida en 12 de diciembre de 2012, 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Detener AzureStreamAnalyticsJob | Detener AzureRMStreamAnalyticsJob
Forma asincrónica se detiene un trabajo de análisis de secuencia de ejecución en Microsoft Azure y anula la asignación de recursos que hubiera que se usaban. La definición de trabajo y metadatos estará disponibles dentro de su suscripción a través del portal de Azure y la administración de las API, por ejemplo, que se pueden editar y reiniciar el trabajo. No se cobrará para un trabajo en el estado de detención.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando PowerShell detiene el trabajo StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Prueba AzureStreamAnalyticsInput | AzureRMStreamAnalyticsInput de prueba
Comprueba la capacidad de análisis de secuencia para conectarse a una entrada especificada.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Este comando PowerShell comprueba el estado de conexión de la entrada EntryStream en StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Prueba AzureStreamAnalyticsOutput | AzureRMStreamAnalyticsOutput de prueba
Comprueba la capacidad de análisis de secuencia para conectarse a una salida especificada.

**Ejemplo 1**

PowerShell Azure 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Este pruebas de comandos de PowerShell el estado de conexión de los resultados de salida en StreamingJob.  

## <a name="get-support"></a>Obtener soporte técnico
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
