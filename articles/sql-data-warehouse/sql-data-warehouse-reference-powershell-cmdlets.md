<properties
   pageTitle="Cmdlets de PowerShell para el almacén de datos de SQL Azure"
   description="Encontrar los cmdlets de PowerShell superiores para el almacén de datos de SQL Azure incluido cómo pausar y reanudar una base de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Cmdlets de PowerShell y las API de REST de almacén de datos SQL

Usar cmdlets de PowerShell de Azure o las API de REST, se pueden administrar muchas de las tareas de administración de almacén de datos de SQL.  A continuación se muestran algunos ejemplos de cómo usar los comandos de PowerShell para automatizar tareas comunes en el almacén de datos de SQL.  Para ver algunos ejemplos de resto buenas, vea el artículo [escalabilidad de administrar con el resto][].

> [AZURE.NOTE]  Para usar PowerShell Azure con el almacén de datos de SQL, debe Azure PowerShell versión 1.0.3 o posterior.  Puede comprobar la versión ejecutando **módulo Get - ListAvailable-Azure nombre**.  Desde el [Instalador de plataforma Web de Microsoft][], se puede instalar la versión más reciente.  Para obtener más información sobre cómo instalar la última versión, consulte [cómo instalar y configurar Azure PowerShell][].

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introducción a los cmdlets de PowerShell de Azure

1. Abrir Windows PowerShell. 
2. En el símbolo del sistema de PowerShell, ejecute los comandos para iniciar sesión en el Administrador de recursos de Azure y seleccione la suscripción.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Ejemplo de almacén de datos SQL de pausa

Pausar la base de datos denominada "Database02" alojado en un servidor denominado "Server01."  El servidor no está en un grupo de recursos de Azure denominado "ResourceGroup1". 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Una variación de este ejemplo canalizaciones el objeto recuperado para [Suspender AzureRmSqlDatabase][].  Como resultado, la base de datos está en pausa. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Ejemplo de almacén de datos SQL de inicio

Reanudar la operación de base de datos denominada "Database02" alojado en un servidor denominado "Server01." El servidor está contenido en un grupo de recursos denominado "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Este ejemplo de una variación recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" que se encuentra en un grupo de recursos denominado "ResourceGroup1." Envía el objeto recuperado para [Reanudar AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Tenga en cuenta que si el servidor es foo.database.windows.net, usar "foo" como nombre de servidor - en los cmdlets de PowerShell.

## <a name="frequently-used-powershell-cmdlets"></a>Con frecuencia los cmdlets de PowerShell usa

Estos cmdlets de PowerShell con frecuencia se usan con el almacenamiento de datos de SQL Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Nueva AzureRmSqlDatabase][]
- [Quitar AzureRmSqlDatabase][]
- [Restaurar AzureRmSqlDatabase][] 
- [Reanudar AzureRmSqlDatabase][]
- [Seleccione AzureRmSubscription][]
- [Establecer AzureRmSqlDatabase][]
- [AzureRmSqlDatabase suspender][]

## <a name="next-steps"></a>Pasos siguientes
Para consultar más ejemplos de PowerShell, vea:

- [Crear un almacén de datos de SQL con PowerShell][]
- [Restaurar base de datos][]

Para obtener una lista de todas las tareas que se pueden automatizar con PowerShell, vea [Cmdlets de base de datos de SQL Azure][].  Para obtener una lista de tareas que se pueden automatizar con el resto, vea [operaciones de bases de datos de SQL Azure][].

<!--Image references-->

<!--Article references-->
[Cómo instalar y configurar PowerShell de Azure]: ./powershell-install-configure.md
[Crear un almacén de datos de SQL con PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restaurar base de datos]: ./sql-data-warehouse-restore-database-powershell.md
[Administrar escalabilidad con el resto]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Cmdlets de base de datos SQL Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Operaciones de bases de datos SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Nueva AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Quitar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restaurar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Reanudar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Seleccione AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Establecer AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[AzureRmSqlDatabase suspender]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Instalador de plataforma Web de Microsoft]: https://aka.ms/webpi-azps
