<properties
    pageTitle="Crear y administrar escala fuera con elásticos trabajos de bases de datos SQL Azure | Microsoft Azure"
    description="Repase la creación y administración de un trabajo elásticos de la base de datos."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Crear y administrar escala a bases de datos de Azure SQL con trabajos elásticos (vista preliminar)

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Trabajos de base de datos elástico** simplifican la administración de grupos de bases de datos mediante la ejecución de las operaciones administrativas como cambios de esquema, administración de credenciales, las actualizaciones de datos de referencia, recopilación de datos de rendimiento o colección de telemetría de inquilinos (cliente). Elásticos trabajos de base de datos está disponible a través del portal de Azure y los cmdlets de PowerShell. Sin embargo, la expone portal Azure reducido funcionalidad limitada en ejecución en todas las bases de datos en un [grupo de base de datos elásticas (vista preliminar)](sql-database-elastic-pool.md). Para obtener acceso a características adicionales y la ejecución de scripts en un grupo de bases de datos como un conjunto personalizado o un conjunto de partes (creada con la [biblioteca de cliente de base de datos elástica](sql-database-elastic-scale-introduction.md)), vea [crear y administrar los trabajos con PowerShell](sql-database-elastic-jobs-powershell.md). Para obtener más información acerca de los trabajos, vea [información general de los trabajos elásticos de la base de datos](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Para una prueba gratuita, vea [prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Un grupo elásticos de la base de datos. Vea [información sobre elástico grupos de base de datos](sql-database-elastic-pool.md)
* Instalación de componentes de servicio del trabajo elásticos de la base de datos. Vea [instalar el servicio de trabajo elásticos de la base de datos](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Creación de trabajos

1. Con el [portal de Azure](https://portal.azure.com), de un grupo de trabajo elásticos de la base de datos existente, haga clic en **Crear tarea**.
2. Escriba el nombre de usuario y la contraseña de administrador de base de datos (creado en la instalación de tareas) para la base de datos de control de trabajos (almacén de metadatos para trabajos).

    ![Asigne un nombre a la tarea, escriba o pegue código y haga clic en ejecutar][1]
2. En el módulo de **Trabajo crear** , escriba un nombre para el trabajo.
3. Escriba el nombre de usuario y contraseña para conectarse a las bases de datos de destino con los permisos necesarios para la ejecución de scripts correctamente.
4. Pegar o escribir en la secuencia de comandos T SQL.
5. Haga clic en **Guardar** y, a continuación, haga clic en **Ejecutar**.

    ![Crear tareas y ejecutar][5]

## <a name="run-idempotent-jobs"></a>Ejecutar trabajos idempotente

Cuando se ejecuta una secuencia de comandos con un conjunto de bases de datos, debe asegurarse de que la secuencia de comandos es idempotente. Es decir, la secuencia de comandos debe poder ejecutar varias veces, aunque no haya antes en un estado incompleto. Por ejemplo, cuando se produce un error en una secuencia de comandos, el trabajo se automáticamente volverá hasta que lo consigue (dentro de límites, como el reintento lógica finalmente dejará la Reintentar). La manera de hacerlo es usar la una cláusula "Si existe" y eliminar cualquier instancia encontrada antes de crear un nuevo objeto. Aquí se muestra un ejemplo:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Como alternativa, puede usar una cláusula "Si no existe" antes de crear una nueva instancia:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Esta secuencia de comandos, a continuación, actualiza la tabla que creó anteriormente.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Comprobar el estado de trabajo

Después de que se ha iniciado un trabajo, puede comprobar su progreso.

1. Desde la página de fondo elásticos de la base de datos, haga clic en **Administrar trabajos**.

    ![Haga clic en "Administrar trabajos"][2]

2. Haga clic en el nombre (a) de una tarea. Puede ser el **estado** "Completada" o "Error". Detalles del trabajo aparecen (b) con su fecha y hora de creación y ejecución. La lista (c) debajo de la muestra el progreso de la secuencia de comandos en cada base de datos en el grupo que le da sus detalles de fecha y hora.

    ![Comprobación de un trabajo finalizado][3]


## <a name="checking-failed-jobs"></a>Error de comprobación trabajos

Si se produce un error en una tarea, puede encontrar un registro de su ejecución. Haga clic en el nombre de la tarea error para ver sus detalles.

![Comprobar un trabajo con error][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
