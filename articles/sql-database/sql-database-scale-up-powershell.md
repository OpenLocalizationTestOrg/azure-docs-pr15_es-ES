<properties 
    pageTitle="Cambiar el nivel de rendimiento y niveles de servicio de una base de datos de SQL Azure con PowerShell | Microsoft Azure" 
    description="Cambiar el nivel de servicio y cómo ampliar su base de datos SQL hacia arriba o hacia abajo con PowerShell muestra el nivel de rendimiento de una base de datos de SQL Azure. Cambiar el nivel de precio de una base de datos de SQL Azure con PowerShell." 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Niveles de servicio y los niveles de rendimiento describen las características y recursos disponibles para la base de datos SQL y se pueden actualizar como las necesidades de la aplicación cambie. Para obtener más información, vea [Niveles de servicio](sql-database-service-tiers.md).

Tenga en cuenta que cambiar el nivel de servicio o nivel de rendimiento de una base de datos crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, cambia conexiones a la réplica. No se pierden datos durante este proceso pero durante brevemente cuando se cambie el a la réplica, están deshabilitadas conexiones a la base de datos, por lo que se pueden deshacer algunas transacciones en vuelo. Esta ventana varía, pero es Media en 4 segundos y en más del 99% de los casos es menor que 30 segundos. Con frecuencia, especialmente si existen grandes números de transacciones en vuelo en las conexiones de momento están deshabilitados, esta ventana puede ser mayor.  

La duración de todo el proceso de escalado depende en el nivel de servicio y tamaño de la base de datos antes y después del cambio. Por ejemplo, una base de datos de 250 GB que está cambiando a, desde o dentro de un nivel de servicio estándar, debe completar dentro de 6 horas. Para una base de datos del mismo tamaño que está cambiando los niveles de rendimiento en el nivel de servicio Premium, debe completarse dentro de 3 horas.


- Para degradar una base de datos, la base de datos debe ser menor que el tamaño máximo permitido de los niveles de servicio de destino. 
- Al actualizar una base de datos con la [Replicación de Geo](sql-database-geo-replication-portal.md) habilitada, primero debe actualizar sus bases de datos secundarios en el nivel de rendimiento deseado antes de actualizar la base de datos principal.
- Cuando cambio de un nivel de servicio Premium, primero debe finalizar todas las relaciones de replicación Geo. Puede seguir los pasos descritos en el tema de [recuperarse de una interrupción](sql-database-disaster-recovery.md) para detener el proceso de replicación entre el principal y las bases de datos secundarios activos.
- Las ofertas de servicio de restauración son diferentes para los distintos niveles de servicio. Si se cambio puede perder la posibilidad de restaurar a un punto en el tiempo o tienen un período de retención de copia de seguridad inferior. Para obtener más información, vea [copia de seguridad de SQL Azure y restaurar](sql-database-business-continuity.md).
- No se aplican a las nuevas propiedades de la base de datos hasta que se completan los cambios.



**Para completar este artículo, se necesita lo siguiente:**

- Una suscripción de Azure. Si necesita una suscripción de Azure simplemente haga clic en **Cuenta gratuita** en la parte superior de esta página y, a continuación, vuelva a fin de este artículo.
- Una base de datos de SQL Azure. Si no tiene una base de datos SQL, crear una siguiendo los pasos de este artículo: [crear la primera base de datos de SQL Azure](sql-database-get-started.md).
- PowerShell Azure.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Cambiar el nivel de rendimiento y niveles de servicio de la base de datos SQL

Ejecutar [Set-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet y establecer la **-RequestedServiceObjectiveName** al nivel de rendimiento del nivel de precio que desee; por ejemplo *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Ejemplo de script de PowerShell para cambiar el nivel de rendimiento y los niveles de servicio de la base de datos SQL

Reemplazar ```{variables}``` con los valores (no incluye las llaves).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Pasos siguientes

- [Cambiar la escala y en](sql-database-elastic-scale-get-started.md)
- [Conectarse y consultar una base de datos SQL con SSMS](sql-database-connect-query-ssms.md)
- [Exportar una base de datos de SQL Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Recursos adicionales

- [Información general de continuidad empresarial](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlets de base de datos SQL azure] (http://msdn.microsoft.com/library/azure/mt574084https :/ / msdn.microsoft.com/biblioteca/azure/mt619433(v=azure.300\).aspx.aspx)