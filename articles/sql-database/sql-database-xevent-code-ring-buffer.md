<properties 
    pageTitle="Código de XEvent búfer de base de datos SQL | Microsoft Azure" 
    description="Proporciona un ejemplo de código de Transact-SQL que se realiza fácil y rápida mediante el uso de destino de búfer de base de datos de SQL Azure." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Llamar a código de destino de búfer de eventos ampliados de base de datos de SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Un ejemplo de código completo que desee de la manera más sencilla y rápida de capturar e informe información para un evento ampliado durante una prueba. El destino más sencillo para los datos de evento ampliada es el [destino de búfer](http://msdn.microsoft.com/library/ff878182.aspx).


Este tema presenta una muestra de código de Transact-SQL:


1. Crea una tabla con los datos para demostrar con.

2. Crea una sesión de evento ampliado existente, es decir, **sqlserver.sql_statement_starting**.
    - El evento se limita a las instrucciones SQL que contienen una determinada cadena de actualización: **instrucción como 'actualización tabEmployee %'**.
    - Elige esta opción Enviar el resultado del evento a un destino de tipo búfer, es decir, **package0.ring_buffer**.

3. Inicia la sesión de evento.

4. Emite un par de instrucciones de actualización SQL simples.

5. Emite una instrucción SELECT para recuperar el resultado de evento desde el búfer.
    - se unen **Sys.dm_xe_database_session_targets** y otras vistas de administración dinámica (DMV).

6. Detiene la sesión de evento.

7. Quita el destino de búfer para liberar los recursos.

8. Quita la sesión de evento y la tabla de demostración.


## <a name="prerequisites"></a>Requisitos previos


- Una cuenta de Azure y de suscripción. Puede suscribirse a una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Puede crear una tabla en una base de datos.
 - Si lo desea, puede [crear una base de datos de demostración de **AdventureWorksLT** ](sql-database-get-started.md) en minutos.


- SQL Server Management Studio (ssms.exe), lo ideal es que su última versión actualización mensual. Puede descargar la última ssms.exe desde:
 - [Descargar SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx)tema.
 - [Un vínculo directo a la descarga.](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>Código de ejemplo


Con la modificación muy pequeños, el siguiente ejemplo de código de búfer se puede ejecutar en la base de datos de SQL Azure o Microsoft SQL Server. La diferencia es la presencia del nodo '_database' en el nombre de algunas vistas de administración dinámica (DMV), utilizado en la cláusula FROM en el paso 5. Por ejemplo:

- Sys.dm_xe**_database**_session_targets
- Sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>Contenido de búfer de anillo


Hemos usado ssms.exe para ejecutar el código de ejemplo.


Para ver los resultados, se hace clic en la celda en el encabezado de columna **target_data_XML**.

A continuación, en el panel de resultados se hace clic en la celda en el encabezado de columna **target_data_XML**. Haga clic en crear otra pestaña archivo en ssms.exe en que se muestra el contenido de la celda de resultado, como XML.


El resultado se muestra en el siguiente bloque. Busca largo, pero es tan solo dos **<event>** elementos.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Liberar los recursos mantenidos por el búfer


Cuando haya terminado con el búfer, puede quitarlo y liberar sus recursos emitir una **ALTER** como la siguiente:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Se actualiza la definición de la sesión de evento, pero no se eliminan. Más adelante, puede agregar otra instancia del búfer a la sesión de evento:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Más información


El tema principal para eventos ampliados en base de datos de SQL Azure es:


- [Consideraciones de evento extendida de base de datos de SQL](sql-database-xevent-db-diff-from-svr.md), ocurre lo mismo algunos aspectos de eventos ampliados que difieren entre la base de datos de Azure SQL frente a Microsoft SQL Server.


Otros temas de ejemplo de código para eventos ampliados están disponibles en los siguientes vínculos. Sin embargo, debe comprobar con regularidad cualquier ejemplo para ver si los datos de ejemplo está destinado a Microsoft SQL Server frente a la base de datos de SQL Azure. A continuación, puede decidir si los pequeños cambios son necesarios para ejecutar el ejemplo.


- Código de ejemplo para la base de datos de SQL Azure: [código de destino de archivo de evento para eventos ampliados de base de datos de SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
