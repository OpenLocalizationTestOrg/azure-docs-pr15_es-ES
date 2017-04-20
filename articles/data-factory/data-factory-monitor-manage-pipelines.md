<properties 
    pageTitle="Supervisar y administrar el generador de datos de Azure canalizaciones" 
    description="Obtenga información sobre cómo usar el Portal de Azure y Azure PowerShell para supervisar y administrar fábricas de datos de Azure y canalizaciones que haya creado." 
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
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="spelluru"/>


# <a name="monitor-and-manage-azure-data-factory-pipelines"></a>Supervisar y administrar el generador de datos de Azure canalizaciones
> [AZURE.SELECTOR]
- [Usar el portal de Azure y Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Mediante la supervisión y aplicación de administración](data-factory-monitor-manage-app.md)

El servicio de generador de datos proporciona una vista completa y confiable de los servicios de movimiento de almacenamiento, procesamiento y datos. El servicio proporciona un control Panel le ayuda a que puede usar para realizar las siguientes tareas: 

- Evaluar rápidamente el estado de la canalización de datos de llevar a cabo.
- Identificar problemas y tomar medidas correctivas si es necesario. 
- Linaje de datos de control. 
- Realizar un seguimiento de las relaciones entre los datos a través de cualquiera de los orígenes.
- Contabilidad histórica completa de la vista de ejecución del trabajo, mantenimiento del sistema y dependencias.

En este artículo se describe cómo supervisar, administrar y depurar su canalizaciones. También se proporciona información sobre cómo crear alertas y recibir una notificación cuando se produzcan errores.

## <a name="understand-pipelines-and-activity-states"></a>Comprender los Estados de actividad y canalizaciones
Con el portal de Azure, puede:

- Ver el generador de datos como un diagrama
- Visualizar las actividades de una canalización
- Ver los conjuntos de datos de entrada y salida
- y mucho más. 

Esta sección proporciona también cómo un sector transiciones de un estado a otro estado.   

### <a name="navigate-to-your-data-factory"></a>Desplácese hasta el generador de datos
1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).
2.  En el menú de la izquierda, haga clic en **generadores de datos** . Si no se ve, haga clic en **más servicios >** y haga clic en **fábricas de datos** en **inteligencia + análisis de** categoría. 

    ![Examinar todo -> generadores de datos](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

    Debería ver todos los generadores de datos en el módulo de **generadores de datos** . 
4. En el módulo de generadores de datos, seleccione el generador de datos que le interesa.

    ![Seleccione el generador de datos](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)  
5.  y verá la página de inicio (módulo**Generador de datos** ) para el generador de datos.

    ![Módulo de generador de datos](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vista de diagrama de la fábrica de datos
La vista de diagrama de un generador de datos ofrece un lugar único para supervisar y administrar el generador de datos y sus activos.

Para ver la vista de diagrama de la fábrica de datos, haga clic en **diagrama** en la página de inicio del generador de datos.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Puede acercar, alejar, zoom para ajustar, zoom al 100%, bloquear el diseño del diagrama y colocar automáticamente canalizaciones y tablas. También puede ver la información de linaje de datos (mostrar elementos ascendentes y descendentes de los elementos seleccionados).
 

### <a name="activities-inside-a-pipeline"></a>Actividades dentro de una canalización 
1. Haga clic en la canalización y haga clic en **abrir canalización** para ver todas las actividades de la canalización junto con conjuntos de datos de entrada y salida para las actividades. Esta característica es útil cuando la canalización está formado por más de una actividad y desea conocer el linaje operativo de una sola canalización.

    ![Menú abrir canalización](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)  
2. En el ejemplo siguiente, verá dos actividades de la canalización con sus entradas y salidas. La actividad titulada **JoinData** del tipo de actividad de subárbol HDInsight y **EgressDataAzure** del tipo de actividad de copia se encuentran en esta canalización de ejemplo. 
    
    ![Actividades dentro de una canalización](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Puede navegar a la página de inicio del generador de datos haciendo clic en el vínculo del generador de datos en la ruta de exploración en la esquina superior izquierda.

    ![Volver al generador de datos](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-state-of-each-activity-inside-a-pipeline"></a>Estado de la vista de cada actividad dentro de una canalización
Puede ver el estado actual de una actividad al ver el estado de cualquiera de los conjuntos de datos creados mediante la actividad. 

Por ejemplo: en el ejemplo siguiente, la **BlobPartitionHiveActivity** se ejecutó correctamente y genera un conjunto de datos denominado **PartitionedProductsUsageTable**, que se encuentra en estado **Listo** .

![Estado de canalización](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Haga doble clic en el **PartitionedProductsUsageTable** en la vista de diagrama, se muestran todos los sectores creados mediante una actividad distintas ejecuciones dentro de una canalización. Puede ver que la **BlobPartitionHiveActivity** se ejecutó correctamente cada mes para los últimos ocho meses y genere los sectores en estado **Listo** .

Los sectores del conjunto de datos en el generador de datos pueden tener uno de los estados siguientes:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descripción</th>
</tr>
<tr>
    <td rowspan="8">En espera</td><td>ScheduleTime</td><td>No ha llegado la hora de la segmentación de datos ejecutar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Las dependencias precedentes no están listos.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Recursos de cálculo no están disponibles.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas las instancias de actividad están ocupadas ejecutando otros sectores.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Actividad está en pausa y no puede ejecutar los sectores hasta que se reanude.</td>
</tr>
<tr>
<td>Vuelva a intentar</td><td>Se vuelve a intentar la ejecución de la actividad.</td>
</tr>
<tr>
<td>Validación</td><td>Aún no ha iniciado la validación.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Esperando la validación de volver a intentar.</td>
</tr>
<tr>
<tr
<td rowspan="2">En curso</td><td>Validar</td><td>Validación en curso.</td>
</tr>
<td></td>
<td>La segmentación de datos se está procesando.</td>
</tr>
<tr>
<td rowspan="4">No se pudo</td><td>Tiempo de espera agotado</td><td>Ejecución tardó más tiempo del está permitido por la actividad.</td>
</tr>
<tr>
<td>Cancelar</td><td>Cancelar la acción del usuario.</td>
</tr>
<tr>
<td>Validación</td><td>Error de validación.</td>
</tr>
<tr>
<td></td><td>Error al generar o validar la segmentación de datos.</td>
</tr>
<td>Listo</td><td></td><td>El sector está listo para su uso.</td>
</tr>
<tr>
<td>Omitido</td><td></td><td>La segmentación de datos no se procesa.</td>
</tr>
<tr>
<td>Ninguno</td><td></td><td>Un sector que usa existen con un estado diferente, pero se ha restablecido.</td>
</tr>
</table>



Puede ver los detalles sobre un sector haciendo clic en una entrada de división en el módulo de **Sectores de actualizado recientemente** .

![Detalles de segmentación de datos](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Si el sector se ha ejecutado varias veces, verá varias filas en la lista **se ejecuta la actividad** . Puede ver detalles sobre una actividad ejecutar haciendo clic en la entrada de ejecución en la lista **que se ejecuta la actividad** . La lista muestra todos los archivos de registro junto con un mensaje de error si hay alguna. Esta característica es muy útil para ver y depurar registros sin salir de la fábrica de datos.

![Detalles de ejecución de actividad](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Si la segmentación de datos no está en el estado **Listo** , puede ver los sectores precedentes que no están listos e impiden la segmentación de datos actual de la ejecución de la lista de **sectores precedentes que son no está listos** . Esta característica es útil cuando su sector está en estado de **espera** y desea conocer las dependencias precedentes en el que está esperando la segmentación de datos.

![Ascendente sectores no está listos](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado del conjunto de datos
Una vez que implementa un generador de datos y las canalizaciones tienen un período de active válido, el conjunto de datos sectores transición de un estado a otro. Actualmente, el estado del sector sigue el siguiente diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

El flujo de transición de estado de conjunto de datos en el generador de datos: espera -> en curso o en curso (Validating) -> Listo o error

Inician los sectores en un estado **de espera** para condiciones previas cumplirse antes de ejecutar. A continuación, inicia la ejecución de la actividad y la segmentación de datos se incluye en el estado **En curso** . La ejecución de la actividad puede correcta o un error. La segmentación de datos está marcado como **Listo**' o **error** basándose en el resultado de la ejecución. 

Puede restablecer la segmentación de datos para volver desde estado **Listo** o **error** al estado de **espera** . Puede marcar también el estado de sector para **Saltar**, que impide la ejecución de la actividad y no procesar la segmentación de datos.


## <a name="manage-pipelines"></a>Administrar canalizaciones
Puede administrar sus canalizaciones con PowerShell de Azure. Por ejemplo, puede pausar y reanudar canalizaciones al ejecutar los cmdlets de PowerShell de Azure. 

### <a name="pause-and-resume-pipelines"></a>Pausar y reanudar canalizaciones
Puede pausar o suspender tuberías mediante el cmdlet de Powershell **Suspender AzureRmDataFactoryPipeline** . Este cmdlet es útil cuando no desea ejecutar las canalizaciones hasta que se solucione un problema.

Por ejemplo: en la siguiente pantalla, se ha identificado un problema con la **PartitionProductsUsagePipeline** en el generador de datos de **productrecgamalbox1dev** y desea suspender la canalización.

![Canalización de suspensión](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Para suspender una canalización, ejecute el siguiente comando PowerShell:

    Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Por ejemplo:

    Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Una vez que se ha corregido el problema con la **PartitionProductsUsagePipeline**, se puede reanudar la canalización suspendida, ejecute el siguiente comando PowerShell:

    Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Por ejemplo:

    Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## <a name="debug-pipelines"></a>Depurar canalizaciones
Generador de datos de Azure ofrece capacidades enriquecidas a través del portal de Azure y Azure PowerShell para depurar y solucionar problemas de canalizaciones.

### <a name="find-errors-in-a-pipeline"></a>Buscar errores en una canalización
Si se produce un error en la ejecución de la actividad de una canalización, el conjunto de datos creado mediante la canalización está en un estado de error debido a error. Puede depurar y solucionar problemas en el generador de datos de Azure con los siguientes mecanismos.

#### <a name="use-azure-portal-to-debug-an-error"></a>Usar el portal de Azure depurar un error:

3.  En el módulo de la **tabla** , haga clic en el sector de problema con el **estado de** **error**.

    ![Módulo de tabla con el sector de problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.  En el módulo de **Sectores de datos** , haga clic en la actividad que no se pudo ejecutar.
    
    ![Dataslice con un error](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.  En el módulo de **Detalles de ejecución de la actividad** , puede descargar los archivos asociados con el procesamiento de HDInsight. Haga clic en descargar para estado/stderr descargar el archivo de registro de errores que contiene información detallada sobre el error.

    ![Ejecutar el módulo de detalles con error de actividad](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)  

#### <a name="use-the-powershell-to-debug-an-error"></a>Usar la PowerShell depurar un error
1.  Iniciar **PowerShell Azure**.
3.  Ejecutar comando **Get-AzureRmDataFactorySlice** para ver los sectores y sus Estados. Verá un sector con el estado: **error**.       

            Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    
    Por ejemplo:
        
            Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

    Reemplazar **StartDateTime** con el valor de StartDateTime especificado para el conjunto de AzureRmDataFactoryPipelineActivePeriod.
4. Ahora, ejecute el cmdlet **Get-AzureRmDataFactoryRun** para obtener detalles sobre la actividad ejecutar para la segmentación de datos.

        Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
        <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    
    Por ejemplo:

        Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

    El valor de StartDateTime es la hora de inicio para el sector de error o problema que anotó en el paso anterior. La fecha y hora debe estar entre comillas dobles.
5.  Debe ver el resultado con detalles sobre el error (similar al siguiente):

            Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
            ResourceGroupName       : ADF
            DataFactoryName         : LogProcessingFactory3
            TableName               : EnrichedGameEventsTable
            ProcessingStartTime     : 10/10/2014 3:04:52 AM
            ProcessingEndTime       : 10/10/2014 3:06:49 AM
            PercentComplete         : 0
            DataSliceStart          : 5/5/2014 12:00:00 AM
            DataSliceEnd            : 5/6/2014 12:00:00 AM
            Status                  : FailedExecution
            Timestamp               : 10/10/2014 3:04:52 AM
            RetryAttempt            : 0
            Properties              : {}
            ErrorMessage            : Pig script failed with exit code '5'. See wasb://     adfjobs@spestore.blob.core.windows.net/PigQuery
                                            Jobs/841b77c9-d56c-48d1-99a3-
                        8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                        more details.
            ActivityName            : PigEnrichLogs
            PipelineName            : EnrichGameLogsPipeline
            Type                    :
    
    
6.  Puede ejecutar **AzureRmDataFactoryLog guardar** cmdlet con Id ver en el resultado y descargar los archivos de registro con el **-DownloadLogsoption** para el cmdlet.

            Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"


## <a name="rerun-failures-in-a-pipeline"></a>Vuelva a ejecutar errores en una canalización

### <a name="using-azure-portal"></a>Usar el portal de Azure

Una vez solucionar problemas y errores en una canalización de depuración, puede volver a ejecutar errores navegar a la segmentación de datos de error y haciendo clic en el botón **Ejecutar** en la barra de comandos.

![Vuelva a ejecutar un sector ha fallado](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

En caso de que la segmentación de datos ha fallado validación debido a un error de directiva (para ex: datos no está disponibles), puede corregir el error y vuelva a validar haciendo clic en el botón **Validar** en la barra de comandos.
![Corregir los errores y validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="using-azure-powershell"></a>Uso de PowerShell de Azure

Puede volver a ejecutar errores mediante el cmdlet Set-AzureRmDataFactorySliceStatus. Vea el tema de [AzureRmDataFactorySliceStatus conjunto](https://msdn.microsoft.com/library/mt603522.aspx) de sintaxis y otros detalles sobre el cmdlet. 

**Ejemplo:** En el ejemplo siguiente se establece el estado de todos los sectores de la tabla 'DAWikiAggregatedData' en espera en el generador de datos de Azure 'WikiADF'.

La UpdateType se establece en UpstreamInPipeline, lo que significa que Estados de cada segmento de la tabla y todas las tablas (ascendentes) dependientes están establecidos "Espera". Otro valor posible para este parámetro es "Individual".

    Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## <a name="create-alerts"></a>Crear alertas
Eventos de usuario de Azure registros cuando un recurso de Azure (por ejemplo, un generador de datos) se creó, se actualiza o se elimina. Puede crear alertas sobre estos eventos. Generador de datos le permite capturar varias métricas y crear avisos de las estadísticas. Se recomienda usar eventos para métricas y supervisión en tiempo real con fines históricos. 

### <a name="alerts-on-events"></a>Alertas de eventos
Eventos de Azure proporcionan útiles recomendaciones sobre lo que sucede en los recursos de Azure. Eventos de usuario de Azure registros cuando un recurso de Azure (por ejemplo, un generador de datos) se creó, se actualiza o se elimina. Al usar el generador de datos de Azure, los eventos se generan cuando:

- Generador de datos de Azure es creado, actualizar o eliminar.
- Procesamiento de datos (denominado mientras se ejecuta) se ha iniciado o completado.
- Se crea un clúster de HDInsight a petición y se quita.

Puede crear alertas en estos eventos de usuario y configurarlos para enviar notificaciones de correo electrónico al administrador y coadministradores de la suscripción. Además, puede especificar las direcciones de correo electrónico de los usuarios que necesiten recibir notificaciones por correo electrónico cuando se cumplen las condiciones. Esta característica es útil cuando desea recibir una notificación cuando se produzcan errores y no desea supervisar continuamente el generador de datos.

> [AZURE.NOTE] Actualmente, el portal no mostrar alertas sobre los eventos. Usar la [supervisión y aplicación de administración](data-factory-monitor-manage-app.md) para ver todas las alertas.

#### <a name="specifying-an-alert-definition"></a>Especificar una definición de alerta:
Para especificar una definición de alerta, cree un archivo JSON que describe las operaciones que desea que se le avise en. En el ejemplo siguiente, la alerta envía una notificación de correo electrónico para la operación de RunFinished. Para que sea específico, se envía una notificación de correo electrónico cuando se complete una ejecución en el generador de datos y ha fallado la ejecución (estado = FailedExecution).

    {
        "contentVersion": "1.0.0.0",
         "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "parameters": {},
        "resources": 
        [
            {
                "name": "ADFAlertsSlice",
                "type": "microsoft.insights/alertrules",
                "apiVersion": "2014-04-01",
                "location": "East US",
                "properties": 
                {
                    "name": "ADFAlertsSlice",
                    "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                    "isEnabled": true,
                    "condition": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                        "dataSource": 
                        {
                            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                            "operationName": "RunFinished",
                            "status": "Failed",
                            "subStatus": "FailedExecution"   
                        }
                    },
                    "action": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                        "customEmails": [ "<your alias>@contoso.com" ]
                    }
                }
            }
        ]
    }

La definición de JSON, se puede quitar **subestado** si no desea recibir una alerta sobre un error específico.

En este ejemplo se establece la alerta para todos los generadores de datos de la suscripción. Si desea que la alerta de estar configurado para un generador de datos determinado, puede especificar datos fábrica **resourceUri** en el **origen de datos**:

    "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

La tabla siguiente proporciona la lista de acciones disponibles y Estados (y subcarpetas estados).

Nombre de la operación | Estado | Estado de Sub
-------------- | ------ | ----------
RunStarted | Introducción | Iniciar
RunFinished | No se pudo / se realizó correctamente | FailedResourceAllocation<br/><br/>Se realizó correctamente<br/><br/>FailedExecution<br/><br/>Tiempo de espera agotado<br/><br/>< cancelado<br/><br/>FailedValidation<br/><br/>Abandonados
OnDemandClusterCreateStarted | Introducción
OnDemandClusterCreateSuccessful | Se realizó correctamente
OnDemandClusterDeleted | Se realizó correctamente

Para obtener más información acerca de los elementos JSON utilizados en el ejemplo, vea [Crear regla de alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) . 

#### <a name="deploying-the-alert"></a>Implementación de la alerta 
Para implementar la alerta, use el cmdlet de PowerShell de Azure: **Nuevo AzureRmResourceGroupDeployment**, tal como se muestra en el ejemplo siguiente:

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

Una vez que la implementación del grupo de recursos se ha completado correctamente, verá los siguientes mensajes:

    VERBOSE: 7:00:48 PM - Template is valid.
    WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
    Please update scripts to remove this parameter.
    VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
    VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
    
    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :

> [AZURE.NOTE] Puede usar la API de REST de [Crear la regla de alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para crear una regla de alerta. La carga JSON es similar al ejemplo JSON.  

#### <a name="retrieving-the-list-of-azure-resource-group-deployments"></a>Recuperar la lista de implementaciones de grupo de recursos de Azure
Para recuperar la lista de implementaciones de grupo de recursos de Azure implementadas, use el cmdlet: **Get-AzureRmResourceGroupDeployment**, tal como se muestra en el ejemplo siguiente:

    Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
    
    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :


#### <a name="troubleshooting-user-events"></a>Solución de problemas de eventos de usuario


1. Puede ver todos los eventos generados tras hacer clic en el mosaico **métricas y operaciones** .

    ![Mosaico métricas y operaciones](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)

2. Haga clic en el mosaico de **eventos** para ver los eventos. 

    ![Mosaico de eventos](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. En el módulo de **eventos** , puede ver los detalles sobre los eventos, filtrar eventos y así sucesivamente. 

    ![Módulo de eventos](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Haga clic en una **operación** de la lista de las operaciones que se produce un error.
    
    ![Seleccione una operación](./media/data-factory-monitor-manage-pipelines/select-operation.png) 
5. Haga clic en un evento de **error** para ver los detalles sobre el error.

    ![Error de evento](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)
  

Consulte el artículo de [Azure conocimientos Cmdlets](https://msdn.microsoft.com/library/mt282452.aspx) para los cmdlets de PowerShell que puede usar para agregar get o eliminar alertas. Estos son algunos ejemplos de usar el cmdlet **Get-AlertRule** : 


    PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
        
            Properties :
            Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
            Condition   :
            DataSource :
            EventName             :
            Category              :
            Level                 :
            OperationName         : RunFinished
            ResourceGroupName     :
            ResourceProviderName  :
            ResourceId            :
            Status                : Failed
            SubStatus             : FailedExecution
            Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
            Condition   :
            Description : One or more of the data slices for the Azure Data Factory has failed processing.
            Status      : Enabled
            Name:       : ADFAlertsSlice
            Tags       :
            $type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
            Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
            Location   : West US
            Name       : ADFAlertsSlice
    
    PS C:\> Get-AlertRule -res $resourceGroup

            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
            Location   : West US
            Name       : FailedExecutionRunsWest0
    
            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
            Location   : West US
            Name       : FailedExecutionRunsWest3

    PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
    
            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
            Location   : West US
            Name       : FailedExecutionRunsWest0

Ejecute los siguientes comandos de obtener ayuda para ver los detalles y ejemplos para el cmdlet Get-AlertRule. 

    get-help Get-AlertRule -detailed 
    get-help Get-AlertRule -examples


- Si ve los eventos de generación de alertas en el módulo de portal pero no recibirá notificaciones de correo electrónico, compruebe si la dirección de correo electrónico especificada está establecido para recibir correos electrónicos de remitentes externos. Los correos electrónicos de alerta pueden ha bloqueado por la configuración de correo electrónico.

### <a name="alerts-on-metrics"></a>Alertas métrica
Generador de datos le permite capturar varias métricas y crear avisos de las estadísticas. Puede supervisar y crear alertas en los siguientes indicadores para los sectores en el generador de datos.
 
- Error ejecución
- Se ejecuta con éxito

Estas métricas son útiles y permiten obtener una visión general de ejecuciones general erróneas y correctas en el generador de datos. Métricas se emiten cada vez que hay una ejecución del sector. Encima de la hora, estas métricas se suman y se inserta a su cuenta de almacenamiento. Por lo tanto, para habilitar métricas, configurar una cuenta de almacenamiento.


#### <a name="enabling-metrics"></a>Habilitar métricas:
Para habilitar las mediciones, haga clic en los siguientes puntos de módulos del generador de datos:

**Supervisión** -> **métrica** -> **configuración de diagnóstico** -> **diagnóstico**

![Vínculo de diagnóstico](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

En el módulo de **diagnóstico** , haga clic **en** , seleccione la cuenta de almacenamiento y guardar.

![Módulo de diagnóstico](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Una vez guardado, puede tardar hasta una hora para la métrica esté visible en el módulo de supervisión, como agregación métricas se realiza cada hora.


### <a name="setting-up-alert-on-metrics"></a>Configurar alerta para métricas:

Haga clic en módulo **métricas del generador de datos** : 

![Mosaico de métricas del generador de datos](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

En el módulo **métrica** , haga clic en **+ Agregar aviso** en la barra de herramientas. 
![Placa métrica del generador de datos: Agregar alerta](./media/data-factory-monitor-manage-pipelines/add-alert.png)

En la página **Agregar una regla de alerta** , realice los pasos siguientes y haga clic en **Aceptar**.
 
- Escriba un nombre para la alerta (ejemplo: error de alerta).
- Escriba una descripción para la alerta (ejemplo: enviar un correo electrónico cuando se produce un error).
- Seleccione una métrica (error se ejecuta y se inicia correctamente).
- Especifique una condición y un valor de umbral.   
- Especifique el período. 
- Especifique si debe enviarse un correo electrónico a los propietarios, colaboradores y los lectores.
- y mucho más. 

![Placa métrica del generador de datos: Agregar alerta](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Una vez agregada la regla de alerta correctamente, se cierra el módulo y verá la nueva alerta en la página **métrica** . 

![Placa métrica del generador de datos: Agregar alerta](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

También debe ver el número de alertas en el mosaico de **alertas** . Haga clic en el mosaico de **alertas** .

![Módulo de métrica de fábrica de datos - reglas de alertas](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

En el módulo de **alertas** , verá las alertas existentes. Para agregar una alerta, haga clic en **Agregar aviso** en la barra de herramientas.

![Módulo de reglas de alertas](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notificaciones de alerta:
Una vez que la regla de alerta coincide con la condición, recibirá una alerta por correo electrónico activada. Una vez que se resuelve el problema y la condición de alerta no coinciden con más, recibirá una alerta por correo electrónico resuelta.

Este comportamiento es diferente a los eventos donde se envía una notificación sobre cada error para la regla de alerta califica.

### <a name="deploying-alerts-using-powershell"></a>Implementar alertas con PowerShell
Puede implementar las alertas de métricas de la misma manera como lo hace para los eventos. 

**Definición de la alerta:**

    {
        "contentVersion" : "1.0.0.0",
        "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "parameters" : {},
        "resources" : [
        {
                "name" : "FailedRunsGreaterThan5",
                "type" : "microsoft.insights/alertrules",
                "apiVersion" : "2014-04-01",
                "location" : "East US",
                "properties" : {
                    "name" : "FailedRunsGreaterThan5",
                    "description" : "Failed Runs greater than 5",
                    "isEnabled" : true,
                    "condition" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                        "dataSource" : {
                            "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                            "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                            "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
    >/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                            "metricName" : "FailedRuns"
                        },
                        "threshold" : 5.0,
                        "windowSize" : "PT3H",
                        "timeAggregation" : "Total"
                    },
                    "action" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                        "customEmails" : ["abhinav.gpt@live.com"]
                    }
                }
            }
        ]
    }
 
Reemplazar subscriptionId, resourceGroupName y dataFactoryName en los datos de ejemplo con los valores apropiados.

*metricName* a partir de ahora es compatible con dos valores:
- FailedRuns
- SuccessfulRuns

**Implementación de la alerta:**

Para implementar la alerta, use el cmdlet de PowerShell de Azure: **Nuevo AzureRmResourceGroupDeployment**, tal como se muestra en el ejemplo siguiente:

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

Debe ver el siguiente mensaje después de implementación correcta:

    VERBOSE: 12:52:47 PM - Template is valid.
    VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
    VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
    
    
    DeploymentName    : FailedRunsGreaterThan5
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 7/27/2015 7:52:56 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           


También puede usar el cmdlet **Add-AlertRule** para implementar una regla de alerta. Vea el tema de [Agregar AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) para obtener más detalles.  

## <a name="move-data-factory-to-a-different-resource-group-or-subscription"></a>Mover el generador de datos a otro grupo de recursos o suscripción
Puede mover un generador de datos a otro grupo de recursos o una suscripción diferente mediante el botón de barra de comandos de **mover** en la página de inicio de la fábrica de datos. 

![Mover el generador de datos](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

También puede mover todos los recursos relacionados (por ejemplo, alertas asociadas con el generador de datos) junto con el generador de datos.

![Cuadro de diálogo Mover recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
