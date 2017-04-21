<properties
    pageTitle="Introducción a trabajos elásticos de la base de datos"
    description="cómo usar trabajos elásticos de la base de datos"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Introducción a los trabajos de base de datos elásticas

Elásticos trabajos de base de datos (preview) para la base de datos de SQL Azure permite confiabilidad ejecutar secuencias de comandos de SQL de T que abarcan varias bases de datos y volver a intentar automáticamente y garantías de finalización final. Para obtener más información acerca de la característica de trabajo elástico base de datos, consulte la [página de información general de la característica](sql-database-elastic-jobs-overview.md).

En este tema se extiende el ejemplo que se encuentra en [Introducción a las herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md). Cuando finalice, podrá: Obtenga información sobre cómo crear y administrar trabajos que administración un grupo de bases de datos relacionados. No es necesario utilizar las herramientas de escala elástica para aprovechar las ventajas de los trabajos elásticos.

## <a name="prerequisites"></a>Requisitos previos

Descargar y ejecutar la [Introducción de ejemplo de herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Crear un fragmentar administrador del mapa con la aplicación de ejemplo

Aquí creará un mapa fragmentar manager junto con varios shards, seguido de inserción de datos en el shards. Si ya tiene shards configurado con datos sharded en ellas, puede omitir los siguientes pasos y mover a la siguiente sección.

1. Crear y ejecutar la aplicación de ejemplo **Introducción con herramientas de base de datos elástico** . Siga los pasos hasta el paso 7 en la sección [descargar y ejecuta la aplicación de ejemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Al final del paso 7, verá el símbolo del sistema siguiente:

    ![símbolo del sistema][1]

2.  En la ventana de comandos, escriba "1" y presione **ENTRAR**. Esto crea la fragmentar Administrador de mapa y agrega dos shards en el servidor. A continuación, escriba "3" y presione **ENTRAR**; Repita esta acción cuatro veces. Esto inserta filas de datos de ejemplo en su shards.

3.  El [Portal de Azure](https://portal.azure.com) debe mostrar tres nuevas bases de datos en su servidor v12:

    ![Confirmación Visual Studio][2]

    En este momento, se creará una colección de base de datos personalizada que refleja las bases de datos en el mapa de partes. Esto nos permitirá crear y ejecutar un trabajo que agregar una nueva tabla a través de shards.

Aquí se normalmente crearía un destino de mapa fragmentar, mediante el cmdlet **AzureSqlJobTarget de nuevo** . La base de datos de administrador de mapa fragmentar debe estar establecida como destino de la base de datos y, a continuación, se especifica el mapa de partes específicas como destino. En su lugar, vamos a enumerar las bases de datos en el servidor y agregar las bases de datos a la nueva colección personalizada con la excepción de base de datos principal.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Crea una colección personalizada y agrega todas las bases de datos en el servidor al destino colección personalizada con la excepción de patrón.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Crear un Script de SQL T para ejecución en bases de datos

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Crear el trabajo para ejecutar una secuencia de comandos en el grupo personalizado de bases de datos

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Ejecutar el trabajo 

El siguiente script de PowerShell puede utilizarse para ejecutar una tarea existente:

Actualizar la variable siguiente para reflejar el nombre del trabajo que desee que se han ejecutado:

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Recuperar el estado de la ejecución de una tarea única

Use el cmdlet **Get-AzureSqlJobExecution** mismo con el parámetro **IncludeChildren** para ver el estado de ejecuciones de trabajo secundario, es decir, el estado específico para cada ejecución del trabajo en cada base de datos de destino de la tarea.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>Ver el estado de múltiples ejecuciones del trabajo

El cmdlet **Get-AzureSqlJobExecution** tiene varios parámetros opcionales que se pueden usar para mostrar varias ejecuciones del trabajo, filtradas mediante los parámetros proporcionados. A continuación muestra algunas de las maneras posibles para usar Get-AzureSqlJobExecution:

Recuperar todas las ejecuciones de trabajo activo de nivel superior:

    Get-AzureSqlJobExecution

Recuperar todas las ejecuciones de trabajos de nivel superior, incluidos ejecuciones de trabajos inactivos:

    Get-AzureSqlJobExecution -IncludeInactive

Recuperar ejecuciones de trabajos de todos los secundarios de un identificador de ejecución de trabajo proporcionado, incluidos ejecuciones de trabajos inactivos:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recuperar todas las ejecuciones del trabajo creadas con una programación / combinación, incluyendo trabajos inactivos del trabajo:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Para recuperar todos los trabajos de identificación de un mapa de fragmentar especificado, incluidas las tareas inactivas:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Para recuperar todos los trabajos de identificación de una colección personalizada especificada, incluidas las tareas inactivas:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recuperar la lista de ejecuciones de tareas de trabajo en ejecución de una tarea específica:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Recuperar detalles de ejecución de tareas de trabajo:

El siguiente script de PowerShell puede usarse para ver los detalles de una ejecución de la tarea de trabajo, que es especialmente útil al depurar errores de ejecución.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Recuperar errores dentro de ejecuciones de la tarea de trabajo

El objeto JobTaskExecution incluye una propiedad para el ciclo de vida de la tarea, junto con una propiedad de mensaje. Si la ejecución de tareas de trabajo error, establecerá la propiedad del ciclo de vida a *error* y se establecerá la propiedad de mensaje para el mensaje de excepción resultante y su pila. Si una tarea no se realizó correctamente, es importante ver los detalles de las tareas de trabajo que no se realizó correctamente para un trabajo determinado.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>Esperando completar una ejecución de trabajo

Puede utilizarse el siguiente script de PowerShell que esperar completar una tarea de trabajo:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Crear una directiva de ejecución personalizado

Elásticos trabajos de base de datos admite la creación de directivas de ejecución personalizados que se pueden aplicar al iniciar trabajos.
  
Directivas de ejecución actualmente permiten para definir:

* Nombre: El identificador de la directiva de ejecución.
* Tiempo de espera de trabajo: Tiempo Total antes de que un trabajo se cancelarán elástico trabajos de base de datos.
* Intervalo de reintento inicial: Intervalo esperar antes del primer reintento.
* Intervalo de reintentos máximo: Extremo de intervalos de reintento que se utiliza.
* Coeficiente de espera de intervalo de reintento: Coeficiente que se usa para calcular el siguiente intervalo entre reintentos.  Se usa la siguiente fórmula: (intervalo de reintentos inicial) * Math.pow ((coeficiente de espera de intervalo) (número de intentos de) - 2). 
* Número máximo de intentos: El número máximo de intentos intenta llevar a cabo una tarea.

La directiva de ejecución de forma predeterminada usa los siguientes valores:

* Nombre: Directiva de ejecución predeterminado
* Tiempo de espera de trabajo: 1 semana
* Intervalo de reintento inicial: 100 milisegundos
* Intervalo de reintentos máximo: 30 minutos
* Vuelva a intentar coeficiente de intervalo: 2
* Número máximo de intentos: 2.147.483.647

Crear la directiva de ejecución que desee:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Actualizar una directiva de ejecución personalizado

Actualizar la directiva de ejecución que desee actualizar:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Cancelar un trabajo

Elástico trabajos de base de datos es compatible con las solicitudes de cancelación de trabajos.  Si elástico trabajos de base de datos detecta una solicitud de cancelación de un trabajo que se está ejecutando actualmente, intentará detener el trabajo.

Hay dos formas diferentes que elástico trabajos de base de datos puede realizar una cancelación:

1. Cancelar está ejecutando tareas: si se detecta una cancelación mientras se está ejecutando actualmente una tarea, se intentará una cancelación dentro de los aspectos actualmente en ejecución de la tarea.  Por ejemplo: si hay una consulta larga están llevando a cabo cuando se intenta realizar una cancelación, habrá un intento de cancelar la consulta.
2. Cancelación de tareas de reintentos: Si el subproceso de control detecta una cancelación antes de que se inicia una tarea de ejecución, el subproceso de control evitará iniciar la tarea y declarar la solicitud como cancelada.

Si se solicita una cancelación de trabajo para una tarea principal, se aceptará la solicitud de cancelación para el trabajo primario y para todas sus tareas secundarias.
 
Para enviar una solicitud de cancelación, use el cmdlet **Stop AzureSqlJobExecution** y establezca el parámetro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Eliminar un trabajo por nombre y el historial de trabajos

Elásticos trabajos de base de datos admite la eliminación asincrónica de trabajos. Se puede marcar una tarea para su eliminación y el sistema eliminará el trabajo y todo su historial de trabajo después de han completado todas las ejecuciones del trabajo para el trabajo. El sistema no cancelará automáticamente ejecuciones de trabajo activo.  

En su lugar, se debe invocar AzureSqlJobExecution detener para cancelar ejecuciones de trabajo activo.

Para desencadenar la eliminación de trabajo, use el cmdlet **Quitar AzureSqlJob** y establezca el parámetro de **nombreDeTrabajo** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Crear un destino de la base de datos personalizada
Objetivos de la base de datos personalizada pueden definirse en trabajos elástico base de datos que se pueden usar para la ejecución directamente o para su inclusión dentro de un grupo personalizado de la base de datos. Dado que **grupos elásticos de la base de datos** no son directamente compatibles con mediante APIs de PowerShell, simplemente cree un destino de la base de datos personalizada y destino de la colección de base de datos personalizada que abarca todas las bases de datos en el grupo.

Establecer las siguientes variables para reflejar la información de la base de datos que desee:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Crear un destino de la colección de base de datos personalizada
Puede definir un destino de la colección de base de datos personalizada para habilitar la ejecución en varios destinos definidos de la base de datos. Después de crea un grupo de la base de datos, bases de datos pueden estar asociados al destino de la colección personalizada.

Establecer las siguientes variables para reflejar la configuración de destino de la colección personalizada que desee:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Agregar bases de datos a un destino de la colección de base de datos personalizada

Objetivos de la base de datos se pueden asociadas con destinos de colección de base de datos personalizada para crear un grupo de bases de datos. Cuando se crea una tarea que está destinado a un destino de la colección de base de datos personalizada, se expandirá para las bases de datos asociados al grupo en el momento de ejecución de destino.

Agregar la base de datos a una colección específica personalizada:

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Revise las bases de datos dentro de un destino de la colección de base de datos personalizada

Usar el cmdlet **Get-AzureSqlJobTarget** para recuperar las bases de datos secundarios dentro de un destino de la colección de base de datos personalizada. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Crear una tarea para ejecutar un script a través de un destino de la colección de base de datos personalizada

Usar el cmdlet **AzureSqlJob de nuevo** para crear un trabajo en un grupo de bases de datos definido por un destino de la colección de base de datos personalizada. Elásticos trabajos de base de datos se expanda el trabajo en varios trabajos de secundarios cada correspondiente a una base de datos asociada con el objetivo de la colección de base de datos personalizada y asegurarse de que la secuencia de comandos se ejecuta en cada base de datos. De nuevo, es importante que las secuencias de comandos sean idempotentes ser y son resistentes a reintentos.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Recopilación de datos en bases de datos

**Los trabajos de elásticos de la base de datos** admite la ejecución de una consulta a través de un grupo de bases de datos y envía los resultados de la tabla de una base de datos especificada. Puede consultar la tabla después del hecho para ver los resultados de la consulta de cada base de datos. Esto proporciona un mecanismo asincrónico para ejecutar una consulta a través de muchas bases de datos. Casos de error como una de las bases de datos no está disponible temporalmente se administran automáticamente a través de reintentos.

La tabla de destino especificada se crearán automáticamente si todavía existe, que coinciden con el esquema del conjunto de resultados devuelto. Si la ejecución de una secuencia de comandos devuelve varios conjuntos de resultados, trabajos de base de datos elástico solo enviar la primera de ellas a la tabla de destino proporcionado.

El siguiente script de PowerShell puede utilizarse para ejecutar una secuencia de comandos de recopilación de sus resultados en una tabla especificada. Esta secuencia de comandos supone que se ha creado una secuencia de comandos T SQL que genera un único conjunto de resultados y se ha creado un destino de la colección de base de datos personalizada.

Establecer las siguientes acciones para reflejar el script deseado, credenciales y destino de ejecución:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Crear e iniciar un trabajo para escenarios de la colección de datos
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Crear una programación de la ejecución del trabajo con un desencadenador de trabajo

El siguiente script de PowerShell puede utilizarse para crear una programación recurrente. Esta secuencia de comandos usa un intervalo de un minuto, pero nuevo AzureSqlJobSchedule también admite parámetros - DayInterval, - HourInterval, - MonthInterval y - WeekInterval. Se pueden crear las programaciones de ejecutarán solo una vez pasando por - tendrá que repetir.

Crear una nueva programación:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Crear un desencadenador de trabajo para que una tarea que se ejecute en una programación de tiempo

Puede definirse un desencadenador de trabajo para que una tarea que se ejecute según una programación de hora. El siguiente script de PowerShell puede utilizarse para crear un desencadenador de trabajo.

Establecer las siguientes variables para que coincidan con el trabajo que desee y la programación:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Quitar una asociación programada para detener la tarea de ejecución de programación

Para interrumpir la ejecución del trabajo recurrente a través de un desencadenador de trabajo, se puede quitar el desencadenador de trabajo. Quitar un desencadenador de trabajo para detener una tarea se ejecute según una programación mediante el cmdlet **Quitar AzureSqlJobTrigger** .

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importar los resultados de la consulta de base de datos elástico a Excel

 Puede importar los resultados de una consulta a un archivo de Excel.

1. Inicie Excel 2013.
2.  Vaya a la cinta de opciones de **datos** .
3.  Haga clic en **Desde otros orígenes** y haga clic en **Desde SQL Server**.

    ![Importación de Excel desde otros orígenes][5]
4.  En el **Asistente para la conexión de datos** , escriba la credenciales de inicio de sesión y nombre de servidor. A continuación, haga clic en **siguiente**.
5.  En el cuadro de diálogo **Seleccionar la base de datos que contiene los datos que desea**, seleccione la base de datos de **ElasticDBQuery** .
6.  Seleccione la tabla de **clientes** en la vista de lista y haga clic en **siguiente**. A continuación, haga clic en **Finalizar**.
7.  En el formulario de **Importar datos** , en **Seleccione cómo desea ver los datos en el libro**, seleccione **tabla** y haga clic en **Aceptar**.

Todas las filas de la tabla **compradores** , almacenados en otros shards rellenan la hoja de Excel.

## <a name="next-steps"></a>Pasos siguientes
Ahora puede usar las funciones de datos de Excel. Usar la cadena de conexión con el nombre del servidor, nombre de la base de datos y las credenciales para conectarse las herramientas de integración de BI y los datos a la base de datos de consulta elástico. Asegúrese de que se admite SQL Server como origen de datos de la herramienta. Hacer referencia a la base de datos de consulta elásticas y tablas externas igual que cualquier otra base de datos de SQL Server y tablas de SQL Server para conectarse a con la herramienta.

### <a name="cost"></a>Costo
No hay ningún cargo adicional para usar la característica de consulta de base de datos elástico. Sin embargo, en este momento, esta característica está disponible únicamente en las bases de datos premium como un punto final, pero la shards pueden ser de cualquier nivel de servicio.

Para obtener información sobre precios, consulte [Los detalles de precios de base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
