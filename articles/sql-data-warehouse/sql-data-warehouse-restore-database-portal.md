<properties
   pageTitle="Restaurar un almacén de datos SQL Azure (Portal) | Microsoft Azure"
   description="Azure portal las tareas para restaurar un almacén de datos de SQL Azure."
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

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Restaurar un almacén de datos SQL Azure (Portal)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo aprenderá cómo restaurar un almacén de datos de SQL Azure con el Portal de Azure.

## <a name="before-you-begin"></a>Antes de empezar

**Comprobar la capacidad DTU.** Cada almacén de datos de SQL está alojado en un servidor de SQL server (por ejemplo, myserver.database.windows.net) que tiene una cuota DTU predeterminada.  Antes de poder restaurar un almacén de datos de SQL, compruebe que el servidor SQL server tiene suficiente cuota DTU restante que se está restaurando la base de datos. Para obtener información sobre cómo calcular DTU es necesario o solicitar más DTU, consulte [Solicitar un cambio de cuota DTU][].


## <a name="restore-an-active-or-paused-database"></a>Restaurar una base de datos activa o en pausa

Para restaurar una base de datos:

1. Inicie sesión en el [portal de Azure][]
2. En el lado izquierdo de la pantalla, seleccione **Examinar** y, a continuación, seleccione **servidores SQL Server**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Vaya al servidor y seleccionarlo
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Busque el almacén de datos de SQL que desea restaurar a partir de y selecciónelo
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En la parte superior de la hoja de almacén de datos, haga clic en **Restaurar**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Especifique un nuevo **nombre de la base de datos**
7. Seleccione el último **Punto de restauración**
    1. Asegúrese de que elegir el punto de restauración más reciente.  Como puntos de restauración se muestran en UTC, a veces, la opción predeterminada que se muestra no es el punto de restauración más reciente.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Haga clic en **Aceptar**
9. El proceso de restauración de la base de datos se iniciará y se puede supervisar mediante **notificaciones**

>[AZURE.NOTE] Una vez se haya completado, puede configurar la base de datos recuperado por siguientes [Configurar la base de datos después de recuperación][].


## <a name="restore-a-deleted-database"></a>Restaurar una base de datos eliminado

Para restaurar una base de datos eliminado:

1. Inicie sesión en el [portal de Azure][]
2. En el lado izquierdo de la pantalla, seleccione **Examinar** y, a continuación, seleccione **servidores SQL Server**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Vaya al servidor y seleccionarlo
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Desplácese hacia abajo hasta la sección de operaciones en el módulo del servidor
5. Haga clic en el icono **Eliminar bases de datos**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Seleccione la base de datos eliminado que desea restaurar
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Especifique un nuevo **nombre de la base de datos**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Haga clic en **Aceptar**
9. El proceso de restauración de la base de datos se iniciará y se puede supervisar mediante **notificaciones**

>[AZURE.NOTE] Para configurar la base de datos después de que se haya completado, vea [Configurar la base de datos después de recuperación][]. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre las características de continuidad empresarial de ediciones de base de datos de SQL Azure, lea la [Introducción de continuidad empresarial de base de datos de SQL Azure][].

<!--Image references-->

<!--Article references-->
[Información general de continuidad de negocio base de datos de SQL Azure]: ./sql-database-business-continuity.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurar la base de datos después de la recuperación]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Solicitar un cambio de cuota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Portal de Azure]: https://portal.azure.com/
