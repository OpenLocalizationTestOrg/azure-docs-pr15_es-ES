<properties
    pageTitle="Script de PowerShell para identificar las bases de datos apropiados para un grupo | Microsoft Azure"
    description="Un grupo de la base de datos elástico es una colección de recursos disponibles que están compartidos por un grupo de bases de datos elásticos. Este documento proporciona un script de Powershell para ayudar a evaluar la idoneidad de uso de un grupo de elásticos de la base de datos para un grupo de bases de datos."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/28/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>

# <a name="powershell-script-for-identifying-databases-suitable-for-an-elastic-database-pool"></a>Script de PowerShell para identificar las bases de datos adecuados para un grupo de elásticos de la base de datos

La secuencia de comandos de PowerShell en este artículo calcula los valores de agregado eDTU de bases de datos de usuario en un servidor de base de datos de SQL. La secuencia de comandos recopila datos mientras se ejecuta y, a continuación, para una carga de trabajo típica, debe ejecutar la secuencia de comandos para al menos un día. Lo ideal es que desea ejecutar la secuencia de comandos para una duración que representa la carga de trabajo típica de las bases de datos. Ejecute el script tiempo suficiente para captura los datos que representa la utilización normal a nivel máximo para las bases de datos. Ejecutando el script de una semana o incluso más probable es que hará una estimación más precisa.

Esta secuencia de comandos es útil para evaluar bases de datos v11 para la migración a servidores v12, que son compatibles con los grupos. En servidores de v12, base de datos de SQL tiene inteligencia incorporada analiza telemetría uso histórico y recomienda un fondo cuando estará más rentable. Para obtener más información, consulte [Monitor, administrar y cambiar el tamaño de un grupo de elásticos de la base de datos](sql-database-elastic-pool-manage-portal.md)

> [AZURE.IMPORTANT] Mantener la ventana de PowerShell abierta mientras se ejecuta la secuencia de comandos. No se cierra la ventana de PowerShell hasta que se ha ejecutado la secuencia de comandos para la cantidad de tiempo necesario. 

## <a name="prerequisites"></a>Requisitos previos 

Instale lo siguiente antes de ejecutar la secuencia de comandos:

- La última PowerShell de Azure. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
- El [feature pack de SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).

## <a name="script-details"></a>Detalles de la secuencia de comandos

Puede ejecutar la secuencia de comandos desde el equipo local o en una máquina virtual en la nube. Cuando se ejecute desde el equipo local, pueden implicar gastos de salida porque la secuencia de comandos que necesita descargar los datos de las bases de datos de destino. A continuación muestra la estimación de volumen de datos basándose en el número de bases de datos de destino y la duración de la ejecución de la secuencia de comandos. Para los costes de transferencia de datos de Azure, consulte [Detalles de precios de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     Una base de datos por hora = 38 KB
 -     Una base de datos por día = 900 KB
 -     Una base de datos por semana = 6 MB
 -     bases de datos de 100 por día = 90 MB
 -     bases de datos de 500 por semana = 3 GB

La secuencia de comandos no compilar información de las bases de datos siguientes:

* Elásticos bases de datos (bases de datos ya están en un grupo de elástico)
* Base de datos principal del servidor

Si necesita excluir bases de datos adicionales desde el servidor de destino, cambie la secuencia de comandos para que se cumplen los criterios. De forma predeterminada.

La secuencia de comandos necesita una base de datos de salida para almacenar datos intermedios para el análisis. Puede usar una base de datos nueva o existente. Aunque técnicamente no es necesario ejecutar la herramienta, debe ser la base de datos de salida en un servidor distinto para evitar que afectan a los resultados del análisis. El nivel de rendimiento de la base de datos de salida debe tener al menos S0 o superior. Al recopilar datos de muchas bases de datos durante un largo período de tiempo, considere la posibilidad de actualizar la base de datos de salida a un nivel superior de rendimiento.

La secuencia de comandos debe proporcionar las credenciales para conectarse al servidor de destino (el candidato del grupo elásticos de la base de datos) con un nombre completo del servidor, <*dbname*>**. database.windows.net**. La secuencia de comandos no admite analizar más de un servidor a la vez.

Después de enviar valores para el conjunto de parámetros inicial, se le pide que inicie sesión su cuenta de Azure. Esto es para iniciar sesión en su servidor de destino, no el servidor de base de datos de salida.
    
Si se queda en las siguientes advertencias mientras se ejecuta la secuencia de comandos que puede ignorar:

- Advertencia: El cmdlet de conmutador AzureMode es obsoleto.
- Advertencia: No se puede obtener información de servicio de SQL Server. Error al intentar conectarse a WMI en 'Microsoft.Azure.Commands.Sql.dll' con el siguiente error: el servidor RPC no está disponible.

Cuando finalice la secuencia de comandos, envía el número estimado de eDTUs necesarias para que un grupo que contenga todas las bases de datos de candidatos en el servidor de destino. Este estimado eDTU puede utilizarse para crear y configurar el grupo. Una vez que se crea el grupo y bases de datos se mueven a la agrupación, supervisar el grupo estrechamente unos días y realizar ajustes en la configuración del grupo eDTU según sea necesario. Vea [el Monitor, administrar y cambiar el tamaño de un grupo de elásticos de la base de datos](sql-database-elastic-pool-manage-portal.md).


    
```
param (
[Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
[Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
[Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
[Parameter(Mandatory=$true)][string]$username, # user name
[Parameter(Mandatory=$true)][string]$serverPassword, # password
[Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
[Parameter(Mandatory=$true)][string]$outputdatabaseName,
[Parameter(Mandatory=$true)][string]$outputDBUsername,
[Parameter(Mandatory=$true)][string]$outputDBpassword,
[Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
)

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionName $AzureSubscriptionName

$server = Get-AzureRmSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName

# Check version/upgrade status of the server
$upgradestatus = Get-AzureRmSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
$version = ""
if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
{
$version = $upgradestatus.Status
}
else
{
$version = $server.ServerVersion
}

# For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
$ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}

$outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
$destinationTableName = "resource_stats_output"

# Create a table in output database for metrics collection
$sql = "
IF  NOT EXISTS (SELECT * FROM sys.objects 
WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))

BEGIN
Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
END
TRUNCATE TABLE $($destinationTableName);
"
Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 

# waittime (minutes) is interval between data collection queries in the loop below.
$Waittime = 10
$end_Time = [DateTime]::UtcNow
$start_time = $end_time.AddMinutes(-$Waittime)
$finish_time = $end_Time.AddMinutes($duration_minutes)

While ($end_time -lt $finish_time)
{
Write-Host "Collecting metrics..." 
foreach ($db in $ListOfDBs)
{
if ($version -in ("12.0", "Completed")) # for V12 databases 
{
$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
$sql+= "
Declare @DTU_cap int, @db_size float;
Select @DTU_cap = CASE @SLO 
WHEN 'Basic' THEN 5
WHEN 'S0' THEN 10
WHEN 'S1' THEN 20
WHEN 'S2' THEN 50
WHEN 'S3' THEN 100
WHEN 'P1' THEN 125
WHEN 'P2' THEN 250
WHEN 'P4' THEN 500
WHEN 'P6' THEN 1000
WHEN 'P11' THEN 1750
WHEN 'P15' THEN 4000
ELSE 50 -- assume Web/Business DBs
END
SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
" 
}
else
{
$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
$sql+= "
Declare @DTU_cap int, @db_size float;
Select @DTU_cap = CASE @SLO 
WHEN 'Basic' THEN 5
WHEN 'S0' THEN 10
WHEN 'S1' THEN 20
WHEN 'S2' THEN 50
WHEN 'P1' THEN 100
WHEN 'P2' THEN 200
WHEN 'P3' THEN 800
ELSE 50 -- assume Web/Business DBs
END
SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
" 
}

$result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 240 -QueryTimeout 3600 
#bulk copy the metrics to output database
$bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
$bulkCopy.BulkCopyTimeout = 600
$bulkCopy.DestinationTableName = "$destinationTableName";
$bulkCopy.WriteToServer($result);

}

$start_time = $start_time.AddMinutes($Waittime)
$end_time = $end_time.AddMinutes($Waittime)
Write-Host $start_time
Write-Host $end_time
do {
Start-Sleep 1
   }
until (([DateTime]::UtcNow) -ge $end_time)
}

Write-Host "Analyzing the collected metrics...."
# Analysis query that does aggregation of the resource metrics to calculate pool size.
$sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
WITH group_stats AS
(
SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
FROM resource_stats_output 
WHERE slo LIKE '

$sql2 = '
GROUP BY end_time
)
-- calculate aggregate storage and DTUs for all DBs in the group
, group_DTU AS
(
SELECT end_time, avg_group_Storage, 
(SELECT Max(v)
   FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
FROM group_stats
)
-- Get top 1 percent of the storage and DTU utilization samples.
, top1_percent AS (
SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
)

-- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
--SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
IF @DTU_Storage > @DTU_Perf_99 
SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
ELSE 
SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'

#check if there are any web/biz edition dbs in the collected metrics
$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
if ($output -like "Shared*")
{
write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
$sql = $sql1 + "'Shared%'"  + $sql2
$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
$data | %{'{0}' -f $_[0]}
}

#check if there are any basic edition dbs in the collected metrics
$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
if ($output -like "Basic*")
{
write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
$sql = $sql1 + "'Basic%'"  + $sql2
$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
$data | %{'{0}' -f $_[0]} 
}

#check if there are any standard edition dbs in the collected metrics
$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
if ($output -like "S*")
{
write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
$sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
$data | %{'{0}' -f $_[0]}
}

#check if there are any premium edition dbs in the collected metrics
$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
if ($output -like "P*")
{
write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
$sql = $sql1 + "'P%'"  + $sql2
$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
$data | %{'{0}' -f $_[0]}
}
```
        

