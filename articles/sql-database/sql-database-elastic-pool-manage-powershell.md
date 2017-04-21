<properties 
    pageTitle="Administrar un grupo de base de datos elásticas (PowerShell) | Microsoft Azure" 
    description="Obtenga información sobre cómo usar PowerShell para administrar un grupo de elásticos de la base de datos."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Supervisar y administrar un grupo de base de datos elástico con PowerShell 

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Administrar un [grupo elásticos de la base de datos](sql-database-elastic-pool.md) de uso de cmdlets de PowerShell. 

Códigos de error comunes, vea [códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos](sql-database-develop-error-messages.md).

Valores para los grupos se pueden encontrar en [límites de almacenamiento y eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Requisitos previos

* Azure PowerShell 1.0 o superior. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
* Grupos de base de datos elástico solo están disponibles con servidores V12 de base de datos de SQL. Si tiene un servidor V11 de base de datos de SQL, [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-portal.md) en un solo paso.


## <a name="move-a-database-into-an-elastic-pool"></a>Mover una base de datos a un grupo de elástico

Puede mover una base de datos dentro o fuera de un grupo con el [Conjunto AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Cambiar la configuración de rendimiento de un grupo

Cuando se reduce el rendimiento, puede cambiar la configuración del grupo de servidores para acomodar el crecimiento. Usar el cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Establezca el parámetro - Dtu a la eDTUs por grupo. Consulte los [límites de almacenamiento y eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) para los valores posibles.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Obtener el estado de las operaciones del fondo

Crear un grupo puede tardar tiempo. Para realizar un seguimiento del estado de las operaciones del fondo incluidas la creación y actualizaciones, use el cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) .

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Obtener el estado de mover una base de datos elástico dentro y fuera de un grupo

Mover una base de datos puede tardar tiempo. Realizar un seguimiento de un estado de mover mediante el cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) .

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Obtener datos de uso de recursos para un grupo

Métricas que se pueden recuperar como un porcentaje del límite de grupo de recursos:   


| Nombre de métrica | Descripción |
| :-- | :-- |
| CPU\_por ciento | Utilización de cálculo promedio en porcentaje del límite de la agrupación. |
| físico\_datos\_leer\_por ciento | Uso de i/OS promedio en porcentaje basado en el límite del grupo. |
| registro de\_escribir\_por ciento | Promedio escribir utilización de los recursos en porcentaje del límite de la agrupación. | 
| DTU\_consumo\_por ciento | Utilización de eDTU promedio en porcentaje del límite de eDTU para el grupo | 
| almacenamiento\_por ciento | Utilización de almacenamiento de información promedio en porcentaje del límite de almacenamiento de la agrupación. |  
| los trabajadores\_por ciento | Máximos simultáneos trabajadores (solicitudes) en porcentaje basado en el límite del grupo. |  
| sesiones\_por ciento | Número máximo de sesiones simultáneo en porcentaje basado en el límite del grupo. | 
| eDTU_limit | Máximo del grupo elástico DTU configuración actual de este grupo elástica durante este intervalo. |
| almacenamiento\_límite | Límite de almacenamiento máximo del grupo elástico actual configuración para este grupo elástico en megabytes durante este intervalo. |
| eDTU\_utilizado | EDTUs promedio utilizada por el grupo en este intervalo. |
| almacenamiento\_utilizado | Almacenamiento medio que utiliza el grupo en este intervalo de bytes |

**Períodos de detalle/retención métricas:**

* Se devolverán los datos de detalle de cinco minutos.  
* Retención de datos es de 35 días.  

Este cmdlet y API limita el número de filas que se pueden recuperar en una llamada a 1000 filas (aproximadamente 3 días de datos de detalle de cinco minutos). Pero este comando se puede llamar varias veces con intervalos de tiempo de inicio o finalización diferente para recuperar los datos más 

Para recuperar las métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Obtener datos de uso de recursos para una base de datos elástica

Estas API son los mismos que las API (V12) actuales utilizadas para supervisar el uso de recursos de una base de datos independiente, excepto la diferencia semántica siguiente. 

Para esta API métricas recuperados se expresan como un porcentaje de la por eDTUs max (o capital equivalente para la métrica subyacente como CPU, IO etcetera) para ese grupo. Por ejemplo, 50% de utilización de cualquiera de estas métricas indica que el recurso específico consumo al 50% de la por límite de capital de base de datos para ese recurso en el grupo principal. 

Para recuperar las métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Agregar una alerta para un recurso de grupo

Puede agregar reglas de alertas a recursos para enviar notificaciones de correo electrónico o las cadenas de alerta a los [extremos de la dirección URL](https://msdn.microsoft.com/library/mt718036.aspx) cuando llega a un umbral de utilización que haya configurado el recurso. Usar el cmdlet Add-AzureRmMetricAlertRule. 

> [AZURE.IMPORTANT]Utilización de los recursos supervisión para grupos de elástico tiene un retraso de al menos 20 minutos. Configurar alertas de menos de 30 minutos para los grupos de elásticos no es compatible actualmente. Configuración de todas las alertas para grupos elástico con un período (parámetro denominado "-tamañoDeVentana" en la API de PowerShell) de menos de 30 minutos posible no se desencadene. Asegúrese de que las alertas que definir para grupos de elástico use un punto (WindowSize) de 30 minutos o más.

En este ejemplo se agrega una alerta para obtener una notificación cuando se consumo de eDTU del grupo supera determinado umbral.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Agregar avisos a todas las bases de datos en un grupo

Puede agregar reglas de alertas para todas las bases de datos en un grupo de elástico para enviar notificaciones de correo electrónico o las cadenas de alerta a los [extremos de la dirección URL](https://msdn.microsoft.com/library/mt718036.aspx) cuando un recurso alcanza un umbral de utilización de configurar la alerta. 

> [AZURE.IMPORTANT] Utilización de los recursos supervisión para grupos de elástico tiene un retraso de al menos 20 minutos. Configurar alertas de menos de 30 minutos para los grupos de elásticos no es compatible actualmente. Configuración de todas las alertas para grupos elástico con un período (parámetro denominado "-tamañoDeVentana" en la API de PowerShell) de menos de 30 minutos posible no se desencadene. Asegúrese de que las alertas que definir para grupos de elástico use un punto (WindowSize) de 30 minutos o más.

En este ejemplo se agrega una alerta para cada una de las bases de datos en un grupo para obtener una notificación cuando se consumo de esa base de datos DTU supera determinado umbral.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Recopilar y supervisar los datos de uso de recursos entre varios grupos de una suscripción

Cuando tiene un gran número de bases de datos en una suscripción, es difícil de supervisar cada grupo elástico por separado. En su lugar, los cmdlets de PowerShell de base de datos SQL y consultas SQL T pueden combinarse para recopilar datos de uso de recursos de varios grupos y sus bases de datos para la supervisión y el análisis de uso de recursos. Un [ejemplo de implementación](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) de como un conjunto de scripts de powershell que encontrará en el repositorio de muestras GitHub SQL Server junto con la documentación sobre lo que hace y cómo usarla.

Para utilizar esta implementación de ejemplo siga estos pasos que se indican a continuación.


1. Descargar [secuencias de comandos y la documentación](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifique las secuencias de comandos para su entorno. Especifique uno o más servidores en el que se hospedan elásticos grupos.
3. Especificar una base de datos de telemetría ¿dónde están las métricas recopiladas almacenarse. 
4. Personalizar la secuencia de comandos para especificar la duración de la ejecución de las secuencias de comandos.

En un nivel alto, las secuencias de comandos haga lo siguiente:

*   Enumera todos los servidores de una suscripción de Azure determinada (o una lista especificada de servidores).
*   Se ejecuta un trabajo en segundo plano para cada servidor. El trabajo se ejecuta en un bucle a intervalos regulares y recopila los datos de telemetría para todos los grupos en el servidor. A continuación, se carga los datos recopilados en la base de datos de telemetría especificado.
*   Enumera una lista de bases de datos de cada grupo para recopilar los datos de uso de recursos de base de datos. A continuación, se carga los datos recopilados en la base de datos de telemetría.

Para supervisar el mantenimiento de grupos elásticas y las bases de datos se pueden analizar las métricas recopiladas en la base de datos de telemetría. La secuencia de comandos también instala una función predefinida de valor de tabla (TVF) en la base de datos de telemetría para ayudar a agregar las medidas de una ventana de tiempo especificado. Por ejemplo, los resultados de la función TVF pueden usarse para mostrar "top N elásticos grupos con la utilización de eDTU máximo en una ventana de tiempo determinado." Si lo desea, use analíticas herramientas como Excel o Power BI para consultar y analizar los datos recopilados.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Ejemplo: recuperar métricas de consumo de recursos para un grupo y sus bases de datos

Este ejemplo recupera los indicadores de consumo para un grupo determinado de elástico y todas sus bases de datos. Los datos recopilados es con formato y escritos en un archivo con formato de archivo .csv. El archivo se puede examinar con Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Latencia de las operaciones del fondo elásticas

- Cambiar el eDTUs min por base de datos o max eDTUs por base de datos normalmente completa en 5 minutos o menos.
- Cambiar el eDTUs por grupo depende de la cantidad total de espacio utilizado por todas las bases de datos en el grupo. Calcular el promedio de cambios 90 minutos o menos por 100 GB. Por ejemplo, si utiliza el espacio total todas las bases de datos en el grupo es 200 GB, entonces la latencia esperada para cambiar la eDTU de grupo por grupo es 3 horas o menos.

## <a name="migrate-from-v11-to-v12-servers"></a>Migrar desde V11 a V12 servidores

Cmdlets de PowerShell están disponibles para iniciar, detener o supervisar una actualización a V12 de base de datos de SQL Azure de V11 o cualquier otra versión de pre-V12.

- [Actualización a V12 de base de datos de SQL con PowerShell](sql-database-upgrade-server-powershell.md)

Documentación de referencia acerca de estos cmdlets de PowerShell, vea:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Inicio AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Detener AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


El cmdlet Stop significa Cancelar, no haga una pausa. No hay ninguna manera de reanudar una actualización, que no sea de nuevo desde el principio. El cmdlet Stop limpia y libera todos los recursos adecuados.

## <a name="next-steps"></a>Pasos siguientes

- [Crear elásticos trabajos](sql-database-elastic-jobs-overview.md) Trabajos elásticos le permiten ejecutar secuencias de comandos SQL T en cualquier número de bases de datos en el grupo.
- Consulte [escala fuera con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md): use herramientas de base de datos elástica para escalado, mover los datos, la consulta o crear transacciones.
