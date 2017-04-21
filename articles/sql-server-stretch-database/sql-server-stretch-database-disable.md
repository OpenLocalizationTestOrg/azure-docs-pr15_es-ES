<properties
    pageTitle="Deshabilitar la base de datos de Estirar y ponerlas datos remotos | Microsoft Azure"
    description="Obtenga información sobre cómo deshabilitar estiramiento de base de datos de una tabla y, opcionalmente, traer datos remotos."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Deshabilitar la base de datos de Estirar y ponerlas remota de datos

Para deshabilitar estiramiento de base de datos de una tabla, seleccione **estirar** para una tabla de SQL Server Management Studio. Seleccione una de las siguientes opciones.

-   Deshabilitar **| Devolver datos de Azure**. Copiar los datos remotos para la tabla de Azure vuelven a SQL Server, a continuación, deshabilitar estiramiento de base de datos de la tabla. Esta operación incurre en costos de transferencia de datos y no se puede cancelar.

-   Deshabilitar **| Deje los datos en Azure**. Deshabilitar la base de datos de ampliación de la tabla.  Abandonar los datos remotos para la tabla en Azure.

También puede usar Transact\-SQL para deshabilitar estiramiento de base de datos para una tabla o una base de datos.

Después de deshabilitar estiramiento de base de datos de una tabla, deja de migración de datos y los resultados de la consulta ya no incluyan los resultados de la tabla remota.

Si simplemente desea pausar la migración de datos, vea [Pausar y reanudar estiramiento de base de datos](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Deshabilitación de la base de datos de estirar para una tabla o una base de datos no elimina el objeto remoto. Si desea eliminar la tabla remota o la base de datos remota, tiene que ocupa a través del portal de administración de Azure. Los objetos remotos seguirán contraer costos Azure hasta que se eliminan. Para obtener más información, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Deshabilitar estiramiento de base de datos para una tabla

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Usar SQL Server Management Studio para deshabilitar estiramiento de base de datos para una tabla

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla para la que desea deshabilitar estiramiento de base de datos.

2.  Derecha\-seleccione **estirar**, haga clic en y, a continuación, seleccione una de las siguientes opciones.

    -   Deshabilitar **| Devolver datos de Azure**. Copiar los datos remotos para la tabla de Azure vuelven a SQL Server, a continuación, deshabilitar estiramiento de base de datos de la tabla. Este comando no se puede cancelar.

        >   [AZURE.NOTE] Copiar los datos remotos para la tabla de Azure atrás a SQL Server incurre en costos de transferencia de datos. Para obtener más información, vea los [Detalles de precios de transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/).

        Después de que todos los datos remotos se ha copiado de Azure vuelta a SQL Server, estirar está deshabilitado para la tabla.

    -   Deshabilitar **| Deje los datos en Azure**. Deshabilitar la base de datos de ampliación de la tabla.  Abandonar los datos remotos para la tabla en Azure.

    >   [AZURE.NOTE] Deshabilitar estiramiento de base de datos de una tabla no elimina los datos remotos o la tabla remota. Si desea eliminar la tabla remota, tiene que ocupa a través del portal de administración de Azure. La tabla remota sigue contraer costos Azure hasta que se elimine. Para obtener más información, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Usar Transact\-SQL para deshabilitar estiramiento de base de datos de una tabla

-   Para deshabilitar estirar para una tabla y copiar que los datos remotos para la tabla de Azure vuelven a SQL Server, ejecute el siguiente comando. Después de que todos los datos remotos se ha copiado de Azure vuelta a SQL Server, estirar está deshabilitado para la tabla.

    Este comando no se puede cancelar.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Copiar los datos remotos para la tabla de Azure atrás a SQL Server incurre en costos de transferencia de datos. Para obtener más información, vea los [Detalles de precios de transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Para deshabilitar estirar para una tabla y abandonar los datos remotos, ejecute el siguiente comando.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Deshabilitar estiramiento de base de datos de una tabla no elimina los datos remotos o la tabla remota. Si desea eliminar la tabla remota, tiene que ocupa a través del portal de administración de Azure. La tabla remota sigue contraer costos Azure hasta que se elimine. Para obtener más información, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Deshabilitar estiramiento de base de datos para una base de datos
Antes de que puede deshabilitar estiramiento de base de datos para una base de datos, debe deshabilitar la base de datos de estiramiento en el estiramiento individual\-habilitado para tablas en la base de datos.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Usar SQL Server Management Studio para deshabilitar estiramiento de base de datos para una base de datos

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la base de datos para el que desea deshabilitar estiramiento de base de datos.

2.  Derecha\-haga clic en y seleccione **las tareas**, a continuación, seleccione **Expandir**y, a continuación, seleccione **Deshabilitar**.

>   [AZURE.NOTE] Deshabilitar estiramiento de base de datos para una base de datos no elimina la base de datos remota. Si desea eliminar la base de datos remota, tiene que ocupa a través del portal de administración de Azure. La base de datos remota continuará contraer costos Azure hasta que se elimine. Para obtener más información, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Usar Transact\-SQL para deshabilitar estiramiento de base de datos para una base de datos
Ejecute el siguiente comando.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Deshabilitar estiramiento de base de datos para una base de datos no elimina la base de datos remota. Si desea eliminar la base de datos remota, tiene que ocupa a través del portal de administración de Azure. La base de datos remota continuará contraer costos Azure hasta que se elimine. Para obtener más información, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Vea también

[Opciones de conjunto de base de datos de ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Pausar y reanudar la base de datos de estirar](sql-server-stretch-database-pause.md)
