<properties 
    pageTitle="Crear y administrar trabajos de base de datos elástico con PowerShell" 
    description="Usa PowerShell para administrar grupos de la base de datos de SQL Azure" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Crear y administrar un trabajos de elásticos de la base de datos de base de datos SQL con PowerShell (vista preliminar)

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



Las APIs PowerShell para **trabajos elásticos de la base de datos** (en la vista previa) le permiten definir un grupo de bases de datos con la que se ejecutarán secuencias de comandos. Este artículo le muestra cómo crear y administrar **trabajos de elásticos de la base de datos** de uso de cmdlets de PowerShell. Vea [información general de trabajos elástico](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Para una prueba gratuita, vea [prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Un conjunto de bases de datos creadas con las herramientas de base de datos elástico. Consulte [Introducción a las herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md).
* PowerShell Azure. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
* **Base de datos elástico trabajos** Paquete de PowerShell: consulte [instalar elástico base de datos de trabajos](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Seleccione la suscripción de Azure

Para seleccionar la suscripción que necesita su suscripción Id (**-SubscriptionId**) o suscripción nombre (**-SubscriptionName**). Si tiene varias suscripciones, puede ejecutar el cmdlet **Get-AzureRmSubscription** y copie la información de la suscripción que desee en el conjunto de resultados. Una vez que la información de la suscripción, ejecute el siguiente commandlet para establecer esta suscripción como el valor predeterminado, es decir, el destino para crear y administrar tareas:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

Se recomienda el [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) para que uso desarrollar y ejecutar secuencias de comandos de PowerShell en los trabajos de base de datos elástico.

## <a name="elastic-database-jobs-objects"></a>Elásticos objetos de trabajos de base de datos

La siguiente tabla muestra todos los tipos de objeto de **base de datos elástico trabajos** junto con sus correspondientes APIs PowerShell y descripción.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descripción</th>
    <th>API de PowerShell relacionadas</th>
  </tr>
  <tr>
    <td>Credenciales</td>
    <td>Nombre de usuario y contraseña para usar al conectarse a bases de datos de ejecución de secuencias de comandos o una aplicación de DACPACs. <p>La contraseña se cifra antes de enviar a y almacenar en la base de datos de elástico trabajos de base de datos.  La contraseña se descifra servicio elástico trabajos de base de datos a través de la credencial creado y cargados desde la secuencia de comandos de instalación.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Nueva AzureSqlJobCredential</p><p>Establecer AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Secuencia de comandos</td>
    <td>Secuencia de comandos de Transact-SQL que se usará para ejecución en bases de datos.  La secuencia de comandos debe estar creado para ser idempotente, ya que el servicio volverá a intentar la ejecución de la secuencia de comandos en errores.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Nueva AzureSqlJobContent</p>
    <p>Establecer AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplicación de nivel de datos </a> paquete que se aplique a través de bases de datos.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Nueva AzureSqlJobContent</p>
    <p>Establecer AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Destino de la base de datos</td>
    <td>Base de datos y nombre de servidor que hace referencia a una base de datos de SQL Azure.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nueva AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Destino del mapa de partes</td>
    <td>Combinación de una base de datos de destino y una credencial que se utilizará para determinar la información almacenada en un mapa de fragmentar elástico base de datos.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nueva AzureSqlJobTarget</p>
    <p>Establecer AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de la colección personalizada</td>
    <td>Grupo definido de bases de datos colectivamente utilice para ejecución.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nueva AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Colección personalizada secundarios destino</td>
    <td>Destino de la base de datos que se hace referencia en una colección personalizada.</td>
    <td>
    <p>AzureSqlJobChildTarget agregar</p>
    <p>Quitar AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Trabajo</td>
    <td>
    <p>Definición de parámetros para una tarea que se pueden usar para activar la ejecución o para completar una programación.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Nueva AzureSqlJob</p>
    <p>Establecer AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Ejecución de la tarea</td>
    <td>
    <p>Contenedor de tareas necesarias para ejecutar una secuencia de comandos o aplicar un DACPAC a un destino con las credenciales para conexiones de base de datos con errores se administra de acuerdo con una directiva de ejecución.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Inicio AzureSqlJobExecution</p>
    <p>Detener AzureSqlJobExecution</p>
    <p>Espera AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Ejecución de la tarea de trabajo</td>
    <td>
    <p>Unidad de trabajo para completar una tarea.</p>
    <p>Si una tarea de trabajo no está disponible para correctamente ejecutar, el mensaje de excepción resultante se registrarán y se creará una nueva tarea de trabajo coincidentes y ejecuta de acuerdo con la directiva de ejecución especificado.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Inicio AzureSqlJobExecution</p>
    <p>Detener AzureSqlJobExecution</p>
    <p>Espera AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Directiva de ejecución de trabajo</td>
    <td>
    <p>Controles del trabajo intervalos entre reintentos y límites de intentos de tiempos de espera de ejecución.</p>
    <p>Elásticos trabajos de base de datos incluye una directiva de ejecución de trabajo predeterminada que se producen esencialmente infinitos reintentos de errores de la tarea de trabajo con interrupción exponencial de intervalos entre cada reintento.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Nueva AzureSqlJobExecutionPolicy</p>
    <p>Establecer AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Programación</td>
    <td>
    <p>Tiempo según la especificación de ejecución tenga lugar en un intervalo recurrente o en una sola vez.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Nueva AzureSqlJobSchedule</p>
    <p>Establecer AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Desencadenadores de trabajo</td>
    <td>
    <p>Una asignación entre un trabajo y una programación para desencadenar la ejecución del trabajo según la programación.</p>
    </td>
    <td>
    <p>Nueva AzureSqlJobTrigger</p>
    <p>Quitar AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Tipos de grupo de trabajos de base de datos elástico compatibles
El trabajo ejecutará secuencias de comandos de Transact-SQL (T-SQL) o aplicación de DACPACs a través de un grupo de bases de datos. Cuando se envía un trabajo que se ejecute en un grupo de bases de datos, la tarea "expande" la en trabajos secundario donde cada una realiza la ejecución solicitado frente a una base de datos en el grupo. 
 
Existen dos tipos de grupos que puede crear: 

* Grupo [Mapa fragmentar](sql-database-elastic-scale-shard-map-management.md) : cuando se envía un trabajo a un mapa de partes de destino, la consulta el mapa fragmentar para determinar su conjunto actual de shards y, a continuación, crea secundarios trabajos para cada fragmentar en el mapa de partes.
* Grupo personalizado de colección: un conjunto de bases de datos personalizadas. Cuando una tarea está destinado a una colección personalizada, crea a secundarios trabajos para cada base de datos actualmente en la colección personalizada.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Para establecer la base de datos elástico trabajos de conexión

Una conexión debe establecerse a la *base de datos de control* de trabajos antes de utilizar la API de trabajos. Si ejecuta este cmdlet, activa una ventana de credencial para solicitar el nombre de usuario y contraseña creada al instalar trabajos de base de datos elástica se muestra. Todos los ejemplos incluidos en este tema se suponen que ya se ha realizado este primer paso.

Abrir una conexión a los trabajos de base de datos elástico:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenciales cifradas dentro de los trabajos de base de datos elásticas

Las credenciales de la base de datos se pueden insertar en la *base de datos de control* de trabajos con su contraseña cifrada. Es necesario almacenar credenciales para habilitar trabajos que se pueden ejecutar en un momento posterior, (con las programaciones de trabajo).
 
Cifrado funciona a través de un certificado creado como parte de la secuencia de comandos de instalación. La secuencia de comandos de instalación crea y carga el certificado en el servicio de nube de Azure para descifrado de las contraseñas cifrados almacenados. Más adelante, el servicio de nube de Azure almacena la clave pública de la *base de datos de control* de trabajos que permite la interfaz API de PowerShell o Portal clásico de Azure para cifrar una contraseña proporcionada sin necesidad de tener el certificado instalado localmente.
 
Las contraseñas de credenciales son cifrada y segura de los usuarios con acceso de solo lectura a los objetos de base de datos elástico trabajos. Pero es posible que un usuario malintencionado con acceso de lectura y escritura a objetos elástico trabajos de base de datos extraer una contraseña. Las credenciales están diseñadas para reutilizar en ejecuciones de trabajos. Las credenciales se pasan a bases de datos de destino al establecer conexiones. Actualmente no hay ninguna restricción en las bases de datos de destino que usa para cada credencial, usuario malintencionado agregue un destino de la base de datos para una base de datos en el control del usuario malintencionado. El usuario volvió puede iniciar un trabajo de esta base de datos para obtener la contraseña de la credencial de destino.

Prácticas recomendadas de seguridad para trabajos de base de datos elástico incluyen:

* Limitar el uso de la API a las personas de confianza.
* Las credenciales deben tener los privilegios mínimos necesarios para realizar la tarea de trabajo.  En este artículo de MSDN de SQL Server [autorización y permisos](https://msdn.microsoft.com/library/bb669084.aspx) , se puede ver más información.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Para crear una credencial cifrada para la ejecución del trabajo a través de bases de datos

Para crear una nueva credencial cifrada, el [**cmdlet Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) solicita un nombre de usuario y contraseña que se puede pasar al [**cmdlet AzureSqlJobCredential de nuevo**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Actualizar credenciales

Cuando cambien las contraseñas, use el [**cmdlet Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) y establezca el parámetro **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Para definir un destino de mapa de base de datos elástico fragmentar

Para ejecutar un trabajo en todas las bases de datos de un conjunto de partes (creado con la [biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md)), utilice un mapa de fragmentar como el destino de la base de datos. En este ejemplo se requiere una aplicación de sharded creada con la biblioteca de cliente de base de datos elástico. Consulte [Introducción a ejemplo de herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md).

La base de datos de administrador de mapa fragmentar debe estar establecida como destino de la base de datos y, a continuación, se debe especificar el mapa de partes específicas como un destino.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Crear un Script de SQL T para ejecución en bases de datos

Al crear secuencias de comandos de SQL de T de ejecución, es muy recomendado para crear de forma que sean [idempotentes](https://en.wikipedia.org/wiki/Idempotence) y resistentes frente a errores. Elásticos trabajos de base de datos volverá a intentar la ejecución de una secuencia de comandos siempre ejecución encuentra un error, independientemente de la clasificación del error.

Use el [**cmdlet AzureSqlJobContent de nuevo**](https://msdn.microsoft.com/library/mt346085.aspx) para crear y guardar una secuencia de comandos de ejecución y configurar los parámetros **ContentName -** y **- CommandText** .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Crear un script nuevo desde un archivo
Si se define la secuencia de comandos SQL T dentro de un archivo, use esta opción para importar la secuencia de comandos:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Actualizar una secuencia de comandos SQL T para ejecución en bases de datos  

Esta secuencia de comandos de PowerShell actualiza el texto del comando SQL T para un script existente.

Establecer las siguientes variables para reflejar la definición de secuencia de comandos que quiera establecer:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Actualizar la definición de una secuencia de comandos

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Para crear una tarea para ejecutar un script a través de un mapa de partes

Esta secuencia de comandos de PowerShell inicia una tarea de ejecución de una secuencia de comandos a través de cada fragmentar en un mapa de fragmentar elástico escala.

Establecer las siguientes variables para reflejar el script deseado y destino:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Para ejecutar un trabajo 

Esta secuencia de comandos de PowerShell ejecuta una tarea existente:

Actualizar la variable siguiente para reflejar el nombre del trabajo que desee que se han ejecutado:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Para recuperar el estado de la ejecución de una tarea única

Usar el [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) y establezca el parámetro **JobExecutionId** para ver el estado de ejecución del trabajo.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Use el cmdlet **Get-AzureSqlJobExecution** mismo con el parámetro **IncludeChildren** para ver el estado de ejecuciones de trabajo secundario, es decir, el estado específico para cada ejecución del trabajo en cada base de datos de destino de la tarea.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Ver el estado de múltiples ejecuciones del trabajo

El [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) tiene varios parámetros opcionales que se pueden usar para mostrar varias ejecuciones del trabajo, filtradas mediante los parámetros proporcionados. A continuación muestra algunas de las maneras posibles para usar Get-AzureSqlJobExecution:

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Para recuperar errores dentro de ejecuciones de la tarea de trabajo

El **objeto JobTaskExecution** incluye una propiedad para el ciclo de vida de la tarea, junto con una propiedad de mensaje. Si la ejecución de tareas de trabajo error, establecerá la propiedad del ciclo de vida a *error* y se establecerá la propiedad de mensaje para el mensaje de excepción resultante y su pila. Si una tarea no se realizó correctamente, es importante ver los detalles de las tareas de trabajo que no se realizó correctamente para un trabajo determinado.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Esperar completar una ejecución de trabajo

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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Cancelar que se están ejecutando tareas: si se detecta una cancelación mientras se está ejecutando actualmente una tarea, se intentará una cancelación dentro de los aspectos actualmente en ejecución de la tarea.  Por ejemplo: si hay una consulta larga están llevando a cabo cuando se intenta realizar una cancelación, habrá un intento de cancelar la consulta.
2. Cancelar reintentos de tarea: si el subproceso de control detecta una cancelación antes de que se inicia una tarea de ejecución, el subproceso de control evitará iniciar la tarea y declarar la solicitud como cancelada.

Si se solicita una cancelación de trabajo para una tarea principal, se aceptará la solicitud de cancelación para el trabajo primario y para todas sus tareas secundarias.
 
Para enviar una solicitud de cancelación, use el [**cmdlet Stop AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) y establezca el parámetro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Para eliminar un trabajo y el historial de trabajos de forma asincrónica

Elásticos trabajos de base de datos admite la eliminación asincrónica de trabajos. Se puede marcar una tarea para su eliminación y el sistema eliminará el trabajo y todo su historial de trabajo después de han completado todas las ejecuciones del trabajo para el trabajo. El sistema no cancelará automáticamente ejecuciones de trabajo activo.  

Invocar [**AzureSqlJobExecution detener**](https://msdn.microsoft.com/library/mt346053.aspx) para cancelar ejecuciones de trabajo activo.

Para desencadenar la eliminación de trabajo, use el [**cmdlet quitar AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) y establezca el parámetro de **nombreDeTrabajo** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>Para crear un destino de la base de datos personalizada

Puede definir los objetivos de la base de datos personalizada de ejecución directa o para su inclusión dentro de un grupo personalizado de la base de datos. Por ejemplo, porque **grupos elásticos de la base de datos** ya no son directamente compatibles con PowerShell APIs, puede crear un destino de la base de datos personalizada y destino de la colección de base de datos personalizada que abarca todas las bases de datos en el grupo.

Establecer las siguientes variables para reflejar la información de la base de datos que desee:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Para crear un destino de la colección de base de datos personalizada

Usar el cmdlet [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para definir un destino de la colección de base de datos personalizada para habilitar la ejecución en varios destinos definidos de la base de datos. Después de crear un grupo de la base de datos, bases de datos se pueden asociadas con el objetivo de la colección personalizada.

Establecer las siguientes variables para reflejar la configuración de destino de la colección personalizada que desee:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Agregar bases de datos a un destino de la colección de base de datos personalizada

Para agregar una base de datos a una colección específica personalizada usar el cmdlet de [**Agregar AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx) .

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Revise las bases de datos dentro de un destino de la colección de base de datos personalizada

Usar el cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para recuperar las bases de datos secundarios dentro de un destino de la colección de base de datos personalizada. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Crear una tarea para ejecutar un script a través de un destino de la colección de base de datos personalizada

Usar el cmdlet [**AzureSqlJob de nuevo**](https://msdn.microsoft.com/library/mt346078.aspx) para crear un trabajo en un grupo de bases de datos definido por un destino de la colección de base de datos personalizada. Elásticos trabajos de base de datos se expanda el trabajo en varios trabajos de secundarios cada correspondiente a una base de datos asociada con el objetivo de la colección de base de datos personalizada y asegurarse de que la secuencia de comandos se ejecuta en cada base de datos. De nuevo, es importante que las secuencias de comandos sean idempotentes ser y son resistentes a reintentos.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Recopilación de datos en bases de datos

Puede usar un trabajo para ejecutar una consulta a través de un grupo de bases de datos y enviar los resultados a una tabla específica. Puede consultar la tabla después del hecho para ver los resultados de la consulta de cada base de datos. Esto proporciona un método asincrónico para ejecutar una consulta a través de muchas bases de datos. Intentos se administran automáticamente a través de reintentos.

La tabla de destino especificada se crearán automáticamente si todavía no existe. La nueva tabla coincide con el esquema del conjunto de resultados devuelto. Si una secuencia de comandos devuelve varios conjuntos de resultados, trabajos de base de datos elástico solo enviar la primera tabla de destino.

El siguiente script de PowerShell ejecuta una secuencia de comandos y recopila sus resultados en una tabla especificada. Esta secuencia de comandos, se supone que se ha creado una secuencia de comandos T SQL que genera un único conjunto de resultados y que se ha creado un destino de la colección de base de datos personalizada.

Esta secuencia de comandos usa el cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Configure los parámetros de script, credenciales y destino de ejecución:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Crear e iniciar un trabajo para escenarios de la colección de datos

Esta secuencia de comandos usa el cmdlet [**AzureSqlJobExecution de inicio**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Para programar un desencadenador de ejecución de trabajo

El siguiente script de PowerShell puede utilizarse para crear una programación periódica. Esta secuencia de comandos usa un intervalo de minutos, pero [**Nuevo AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) también admite parámetros - DayInterval, - HourInterval, - MonthInterval y - WeekInterval. Se pueden crear las programaciones de ejecutarán solo una vez pasando por - tendrá que repetir.

Crear una nueva programación:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Para desencadenar una tarea que se ejecute en una programación de tiempo

Puede definirse un desencadenador de trabajo para que una tarea que se ejecute según una programación de hora. El siguiente script de PowerShell puede utilizarse para crear un desencadenador de trabajo.

Use [AzureSqlJobTrigger de nuevo](https://msdn.microsoft.com/library/mt346069.aspx) y establecer las siguientes variables para que coincidan con el trabajo que desee y la programación:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Para quitar una asociación programada para detener la tarea de ejecución de programación

Para interrumpir la ejecución del trabajo recurrente a través de un desencadenador de trabajo, se puede quitar el desencadenador de trabajo. Quitar un desencadenador de trabajo para detener una tarea se ejecute según una programación mediante el [**cmdlet quitar AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Recuperar desencadenadores de trabajo enlazados a un plan de tiempo

El siguiente script de PowerShell puede utilizarse para obtener y mostrar los desencadenadores de trabajo registrados en la programación de un tiempo determinado.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Para recuperar desencadenadores de trabajo enlazado a un trabajo 

Use [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) para obtener y mostrar las programaciones que contiene un trabajo registrado.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para crear una aplicación de nivel de datos (DACPAC) para la ejecución de bases de datos

Para crear un DACPAC, consulte [aplicaciones de nivel de datos](https://msdn.microsoft.com/library/ee210546.aspx). Para implementar un DACPAC, use el [cmdlet AzureSqlJobContent de nuevo](https://msdn.microsoft.com/library/mt346085.aspx). El DACPAC debe ser accesible para el servicio. Se recomienda cargar una DACPAC creado con el almacenamiento de Azure y crear una [Firma de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md) para el DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Actualizar una aplicación de nivel de datos (DACPAC) para la ejecución de bases de datos

DACPACs existentes registrados en elástico trabajos de base de datos pueden actualizarse para que apunte a nuevo URI. Use el [**cmdlet Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) para actualizar el URI DACPAC en un DACPAC registrado existente:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Para crear una tarea para aplicar una aplicación de nivel de datos (DACPAC) en bases de datos

Después de haber creado un DACPAC dentro de elástico trabajos de base de datos, puede crearse una tarea para aplicar la DACPAC a través de un grupo de bases de datos. El siguiente script de PowerShell puede utilizarse para crear un trabajo DACPAC a través de una colección personalizada de bases de datos:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
