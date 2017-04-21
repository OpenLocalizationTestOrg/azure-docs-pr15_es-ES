<properties
   pageTitle="Restaurar un almacén de datos SQL Azure (API de REST) | Microsoft Azure"
   description="API de REST las tareas para restaurar un almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restaurar un almacén de datos SQL Azure (API de REST)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo aprenderá cómo restaurar un almacén de datos de SQL Azure con la API de REST.

## <a name="before-you-begin"></a>Antes de empezar

**Comprobar la capacidad DTU.** Cada almacén de datos de SQL está alojado en un servidor de SQL server (por ejemplo, myserver.database.windows.net) que tiene una cuota DTU predeterminada.  Antes de poder restaurar un almacén de datos de SQL, compruebe que el servidor SQL server tiene suficiente cuota DTU restante que se está restaurando la base de datos. Para obtener información sobre cómo calcular DTU es necesario o solicitar más DTU, consulte [Solicitar un cambio de cuota DTU][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar una base de datos activa o en pausa

Para restaurar una base de datos:

1. Obtener la lista de puntos de restauración de base de datos mediante la operación de obtener puntos de restauración de base de datos.
2. Comenzar la restauración mediante la operación de [Crear base de datos restaurar la solicitud][] .
3. Realizar un seguimiento del estado de la restauración mediante la operación de [estado de la operación de base de datos][] .

>[AZURE.NOTE] Una vez se haya completado, puede configurar la base de datos recuperado por siguientes [Configurar la base de datos después de recuperación][].

## <a name="restore-a-deleted-database"></a>Restaurar una base de datos eliminado

Para restaurar una base de datos eliminado:

1.  Lista de todas las bases de datos eliminados pueden restaurar mediante la operación de [lista pueda restaurar colocadas las bases de datos][] .
2.  Obtener los detalles de la base de datos eliminado que desea restaurar mediante la operación [obtener pueda restaurar base de datos colocado][] .
3.  Comenzar la restauración mediante la operación de [Crear base de datos restaurar la solicitud][] .
4.  Realizar un seguimiento del estado de la restauración mediante la operación de [estado de la operación de base de datos][] .

>[AZURE.NOTE] Para configurar la base de datos después de que se haya completado, vea [Configurar la base de datos después de recuperación][]. 


## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre las características de continuidad empresarial de ediciones de base de datos de SQL Azure, lea la [Introducción de continuidad empresarial de base de datos de SQL Azure][].

<!--Image references-->

<!--Article references-->
[Información general de continuidad de negocio base de datos de SQL Azure]: ./sql-database-business-continuity.md
[Solicitar un cambio de cuota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar la base de datos después de la recuperación]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Crear solicitud de restauración de base de datos]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado de la operación de base de datos]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtener pueda restaurar base de datos colocado]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista pueda restaurar colocado bases de datos]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
