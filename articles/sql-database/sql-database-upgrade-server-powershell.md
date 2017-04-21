<properties
    pageTitle="Actualización a V12 de base de datos de SQL Azure con PowerShell | Microsoft Azure"
    description="Se explica cómo actualizar a V12 de base de datos de SQL Azure e incluye cómo actualizar bases de datos Web y empresa y cómo se actualiza un servidor V11 migrar sus bases de datos directamente en un grupo de base de datos elástico con PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Actualización a V12 de base de datos de SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de datos de SQL es la versión más reciente, por lo que se recomienda actualizar a V12 de base de datos de SQL.
V12 de base de datos de SQL tiene incluidos de muchas [ventajas con respecto a la versión anterior](sql-database-v12-whats-new.md) :

- Mayor compatibilidad con SQL Server.
- Rendimiento mejorado premium y nuevos niveles de rendimiento.
- [Grupos de elásticos de la base de datos](sql-database-elastic-pool.md).

Este artículo ofrece instrucciones para actualizar los servidores existentes V11 de base de datos de SQL y bases de datos a V12 de base de datos de SQL.

Durante el proceso de actualización a V12, actualice las bases de datos Web y empresa a un nuevo nivel de servicio para que se incluyen instrucciones para actualizar bases de datos Web y empresa.

Además, puede ser más eficaz que la actualización a niveles de rendimiento individuales (precios niveles) para bases de datos único migrar a un [grupo elásticos de la base de datos](sql-database-elastic-pool.md) . Grupos también simplifican la administración de la base de datos porque solo necesita para administrar la configuración de rendimiento para el grupo en lugar de administrar por separado los niveles de rendimiento de bases de datos individuales. Si tiene bases de datos en varios servidores, considere la posibilidad de ponerlos en el mismo servidor y sacar provecho de ponerlos en un grupo.

Puede seguir los pasos de este artículo para migrar fácilmente las bases de datos de servidores V11 directamente en grupos de elásticos de la base de datos.

Tenga en cuenta que las bases de datos permanecerá en línea y continuar trabajando en toda la operación de actualización. En el momento de la transición real para el nuevo nivel de rendimiento temporal de colocación de las conexiones de la base de datos puede ocurrir durante un período muy pequeño que normalmente es alrededor de 90 segundos pero puede ser 5 minutos. Si su aplicación tiene [errores transitorias tratamiento de interrupción de la conexión](sql-database-connectivity-issues.md) es suficiente para protegerse frente a las conexiones interrumpidas al final de la actualización.

Actualizar a V12 de base de datos de SQL no se puede deshacer. Después de actualizar el servidor no se puede revertir a V11.

Después de actualizar a V12, [recomendaciones de nivel de servicio](sql-database-service-tier-advisor.md) y [las recomendaciones de grupos elástico](sql-database-elastic-pool-create-portal.md) no inmediatamente estarán disponibles hasta que el servicio tenga tiempo para evaluar las cargas de trabajo en el servidor de nuevo. Historial de recomendación V11 server no se aplica a V12 servidores para que no se conserva.  

## <a name="prepare-to-upgrade"></a>Preparar la actualización

- **Actualizar todas las bases de datos Web y empresa**: usar el portal o usar [PowerShell para actualizar el servidor y las bases de datos](sql-database-upgrade-server-powershell.md).
- **Revisar y suspender la replicación Geo**: si la base de datos de SQL Azure está configurada para replicación Geo debe documentar su configuración actual y [Detener la replicación de Geo](sql-database-geo-replication-portal.md#remove-secondary-database). Después de que finalice la actualización de volver a configurar la base de datos para la replicación de Geo.
- **Abrir estos puertos si dispone de clientes en una máquina virtual de Azure**: si el programa cliente se conecta a V12 de base de datos de SQL mientras el cliente se ejecuta en una máquina virtual (VM) en Azure, debe abrir intervalos de puerto 11000 11999 y 14000 a 14999 en la máquina virtual. Para obtener información detallada, consulte [puertos V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Requisitos previos

Para actualizar un servidor a V12 con PowerShell, debe tener la última Azure PowerShell instalado y en ejecución. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurar las credenciales y seleccione la suscripción

Para ejecutar los cmdlets de PowerShell en su suscripción de Azure primero debe establecer acceso a su cuenta de Azure. Ejecute el siguiente y aparecerá una pantalla de inicio de sesión para escribir sus credenciales. Use el mismo correo electrónico y contraseña que utiliza para iniciar sesión en el portal de Azure.

    Add-AzureRmAccount

Tras iniciar sesión correctamente en debería ver información en pantalla que contiene el identificador ha iniciado sesión en con y las suscripciones de Azure que tiene acceso.

Para seleccionar la suscripción que desea trabajar con usted necesita su suscripción Id (**-SubscriptionId**) o suscripción nombre (**-SubscriptionName**). Puede copiar desde el paso anterior, o si tiene varias suscripciones puede ejecutar el cmdlet **Get-AzureRmSubscription** y copie la información de la suscripción que desee del conjunto de resultados.

Ejecute el cmdlet siguiente con su información de suscripción para establecer su suscripción actual:

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Los comandos siguientes se ejecutará en la suscripción que ha seleccionado anteriormente.

## <a name="get-recommendations"></a>Obtener recomendaciones

Para obtener la recomendación para la actualización de servidor que se ejecute el siguiente cmdlet:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Para obtener más información, vea [crear un grupo de la base de datos elásticas](sql-database-elastic-pool-create-portal.md) y [recomendaciones de nivel de precios de base de datos de SQL Azure](sql-database-service-tier-advisor.md).



## <a name="start-the-upgrade"></a>Iniciar la actualización

Para iniciar la actualización del servidor, ejecute el siguiente cmdlet:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Al ejecutar este proceso de actualización de comando comenzará. Puede personalizar el resultado de la recomendación y proporcionar la recomendación editada para este cmdlet.


## <a name="upgrade-a-server"></a>Actualizar un servidor


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Asignación de actualización personalizada

Si las recomendaciones no son adecuadas para su servidor y el análisis de rentabilidad, puede elegir cómo las bases de datos se actualizan y asignan a bases de datos simples o elásticos.

ElasticPoolCollection y DatabaseCollection son opcionales:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Supervisar las bases de datos después de actualizar a V12 de base de datos de SQL


Después de actualizar, se recomienda para supervisar la base de datos activa para asegurarse de aplicaciones se ejecutan en el rendimiento deseado y optimizar uso según sea necesario.

Además, las bases de datos individuales que puede supervisar agrupaciones de elásticos de la base de datos [con el portal](sql-database-elastic-pool-manage-portal.md) de supervisión o con [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Datos de consumo de recursos:** Para bases de datos básico, estándar y Premium están disponibles a través de la [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV en la base de datos de usuario datos de consumo de recursos. Este DMV proporciona cerca de la información de consumo de recursos en tiempo real en detalle segunda 15 para la hora de operación anterior. El consumo de porcentaje DTU por un intervalo se calcula como el consumo de porcentaje máximo de las dimensiones de la CPU, IO y registro. Esta es una consulta para calcular el promedio consumo de porcentaje DTU sobre la última hora:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Información de supervisión adicional:

- [Guía de rendimiento de base de datos de SQL azure para bases de datos](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Consideraciones de precio y rendimiento para un grupo de elásticos de la base de datos](sql-database-elastic-pool-guidance.md).
- [Supervisión con vistas dinámicas de administración de base de datos SQL Azure](sql-database-monitoring-with-dmvs.md)



**Alertas:** Configurar 'Alertas' en el portal de Azure para que le notifique cuando el consumo de DTU para una base de datos actualizado acerca determinado nivel alto. Alertas de base de datos se pueden configurar en el portal de Azure para varias métricas de rendimiento como DTU, CPU, IO y registro. Vaya a la base de datos y seleccione **las reglas de alertas** en el módulo de **configuración** .

Por ejemplo, puede configurar una alerta de correo electrónico en "Porcentaje DTU" si el valor promedio de porcentaje DTU supera el 75% sobre los últimos 5 minutos. Para obtener más información sobre cómo configurar las notificaciones de alerta, consulte [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .



## <a name="next-steps"></a>Pasos siguientes

- [Crear un grupo de la base de datos elásticas](sql-database-elastic-pool-create-portal.md) y agregar algunas o todas las bases de datos en el grupo.
- [Cambiar el nivel de rendimiento y los niveles de servicio de la base de datos](sql-database-scale-up.md).



## <a name="related-information"></a>Información relacionada

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Inicio AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Detener AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
