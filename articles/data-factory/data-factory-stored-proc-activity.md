<properties 
    pageTitle="Actividad de procedimiento almacenados de SQL Server" 
    description="Obtenga información sobre cómo puede usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una base de datos de SQL Azure o el almacén de datos de SQL Azure de una canalización del generador de datos." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="spelluru"/>

# <a name="sql-server-stored-procedure-activity"></a>Actividad de procedimiento almacenados de SQL Server
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

Puede usar la actividad de procedimiento almacenado de SQL Server en un generador de datos [canalización](data-factory-create-pipelines.md) para invocar un procedimiento almacenado en una del siguientes almacena datos: 


- Base de datos SQL Azure 
- Almacén de datos SQL Azure  
- Base de datos SQL Server en su empresa o en una máquina virtual de Azure. Debe instalar Data Management Gateway en el mismo equipo que hospeda la base de datos o en un equipo distinto para evitar una competición de recursos con la base de datos. Data Management Gateway es un software que se conecta alojados en máquinas virtuales de Azure en servicios de nube de forma segura y administrada orígenes de datos de orígenes de datos local. Consulte el artículo de [mover datos entre local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway. 

En este artículo se basa en el artículo de [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que ofrece una descripción general de transformación de datos y las actividades de transformación compatibles.

## <a name="walkthrough"></a>Tutorial

### <a name="sample-table-and-stored-procedure"></a>Tabla de muestra y procedimiento almacenado
1. Cree la siguiente **tabla** en la base de datos de SQL Azure con SQL Server Management Studio o cualquier otra herramienta que se sienta cómodo. La columna datetimestamp es la fecha y hora cuando se genera el identificador correspondiente. 

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    ID es el único identificado y la columna datetimestamp es la fecha y hora cuando se genera el identificador correspondiente.
    ![Datos de ejemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    > [AZURE.NOTE] Este ejemplo usa la base de datos de SQL Azure, pero funciona de la misma manera para el almacén de datos de SQL Azure y base de datos de SQL Server. 
2. Crear el siguiente **procedimiento almacenado** que inserta datos en la **sampletable**.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS
        
        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

    > [AZURE.IMPORTANT] **Nombre** y **mayúsculas y minúsculas** del parámetro (DateTime en este ejemplo) deben coincidir con los del parámetro especificado en la canalización o actividad JSON. En la definición de procedimiento almacenado, asegúrese de que **@** se usa como un prefijo para el parámetro.
    
### <a name="create-a-data-factory"></a>Crear un generador de datos  
4. Inicie sesión en el [portal de Azure](https://portal.azure.com/). 
5. Haga clic en **nuevo** en el menú de la izquierda, haga clic en **inteligencia + análisis**y haga clic en **Generador de datos**.
    
    ![Nuevo generador de datos](media/data-factory-stored-proc-activity/new-data-factory.png)   
4.  En el módulo de **Generador de datos nueva** , escriba **SProcDF** para el nombre. Nombres de fábrica datos Azure son **globalmente únicos**. Debe incluir prefijo en el nombre de la fábrica de datos con su nombre, para permitir la creación correcta de la fábrica.

    ![Nuevo generador de datos](media/data-factory-stored-proc-activity/new-data-factory-blade.png)      
3.  Seleccione la **suscripción de Azure**. 
4.  **Grupo de recursos**, siga uno de los siguientes pasos: 
    1.  Haga clic en **Crear nuevo** y escriba un nombre para el grupo de recursos.
    2.  Haga clic en **Usar existente** y seleccione un grupo de recursos existente.  
5.  Seleccione la **ubicación** para el generador de datos.
6.  Seleccione **Anclar en escritorio** para que pueda ver el generador de datos en el panel de la próxima vez que inicie sesión. 
6.  En el módulo de **nuevo el generador de datos** , haga clic en **crear** .
6.  Consulte el generador de datos que se creó en el **panel** del portal de Azure. Después de que el generador de datos se ha creado correctamente, verá la página de fábrica de datos, que muestra el contenido del generador de datos.
    ![Página de inicio del generador de datos](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Crear un servicio de SQL Azure vinculado  
Después de crear el generador de datos, cree un servicio de SQL Azure vinculado que se vincula la base de datos de SQL Azure con el generador de datos. Esta base de datos contiene la sampletable tabla y sp_sample procedimiento almacenado.

7.  Haga clic en **autor e implementar** en el módulo de **Generador de datos** para **SProcDF** iniciar el Editor del generador de datos.
2.  Haga clic en **almacenan nuevos datos** en la barra de comandos y elija la **Base de datos de SQL Azure**. Debe ver la secuencia de comandos JSON para crear un servicio de SQL Azure vinculado en el editor. 

    ![Nuevo almacén de datos](media/data-factory-stored-proc-activity/new-data-store.png)
4. En la secuencia de comandos JSON, realice los cambios siguientes: 
    1. Reemplazar ** &lt;nombre_de_servidor&gt; ** con el nombre de su servidor de base de datos de SQL Azure.
    2. Reemplazar ** &lt;databasename&gt; ** con la base de datos en la que se creó la tabla y el procedimiento almacenado.
    3. Reemplazar ** &lt; username@servername ** con la cuenta de usuario que tiene acceso a la base de datos.
    4. Reemplazar ** &lt;contraseña&gt; ** con la contraseña de la cuenta de usuario. 

    ![Nuevo almacén de datos](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
5. En la barra de comandos para implementar el servicio vinculado, haga clic en **implementar** . Confirme que ve la AzureSqlLinkedService en la vista de árbol de la izquierda. 

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida
6. Haga clic en **... Más** en la barra de herramientas, haga clic en **nuevo conjunto de datos**y haga clic en **SQL Azure**. **Nuevo conjunto de datos** en la barra de comandos y seleccione **SQL Azure**.

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/new-dataset.png)
7. Copiar y pegar el siguiente script JSON en el Editor de JSON.

        {               
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
7. En la barra de comandos para implementar el conjunto de datos, haga clic en **implementar** . Confirme que ve el conjunto de datos en la vista de árbol. 

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Crear una canalización con actividad SqlServerStoredProcedure
Ahora, vamos a crear una canalización con una actividad SqlServerStoredProcedure.
 
9. Haga clic en **... Más** en el comando de barra y haga clic en **nuevo canal**. 
9. Copiar y pegar el siguiente fragmento JSON. Establece la **storedProcedureName** **sp_sample**. Nombre y mayúsculas y minúsculas del parámetro **DateTime** deben coincidir con el nombre y las mayúsculas y minúsculas del parámetro en la definición de procedimiento almacenado.  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                "start": "2016-08-02T00:00:00Z",
                "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    Si necesita pasar null para un parámetro, use la sintaxis: "param1": null (todo a minúsculas). 
9. En la barra de herramientas para implementar la canalización, haga clic en **implementar** .  

### <a name="monitor-the-pipeline"></a>Supervisar la canalización

6. Haga clic en **X** para cerrar aspas Editor del generador de datos y volver a la hoja de datos fábrica y haga clic en **diagrama**.

    ![mosaico de diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
7. En la **Vista de diagrama**, consulte información general sobre los conjuntos de datos usados en este tutorial y canalizaciones. 

    ![mosaico de diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
8. En la vista de diagrama, haga doble clic en el conjunto de datos **sprocsampleout**. Vea los sectores en estado listo. Debe haber cinco sectores porque se produce un sector para cada hora entre la hora de inicio y hora de fin de la JSON.

    ![mosaico de diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png) 
10. Cuando un sector está **preparada** , ejecute una * *Seleccione* de sampletable** consulta en la base de datos de SQL Azure para comprobar que los datos se insertaron en la tabla por el procedimiento almacenado.

    ![Datos de salida](./media/data-factory-stored-proc-activity/output.png)

    Vea [la canalización de Monitor](data-factory-monitor-manage-pipelines.md) para obtener información detallada acerca de cómo supervisar el generador de datos de Azure canalizaciones.  

> [AZURE.NOTE] En este ejemplo, el SprocActivitySample tiene entradas. Si desea bicicleta esta actividad con una actividad en sentido ascendente (es decir, los procesos anteriores), pueden utilizar los resultados de la actividad precede como entradas en esta actividad. En este caso, esta actividad no se ejecuta hasta que se haya completado la actividad de nivel superior y los resultados de las actividades en sentido ascendente están disponibles (en estado listo). Las entradas no se pueden usar directamente como un parámetro a la actividad de procedimiento almacenado

## <a name="json-format"></a>Formato JSON
    {
        "name": "SQLSPROCActivity",
        "description": "description", 
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## <a name="json-properties"></a>Propiedades JSON

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
nombre | Nombre de la actividad | Sí
Descripción | Texto que describe qué se usa la actividad | No
tipo | SqlServerStoredProcedure | Sí
entradas | Opcional. Si especifica un conjunto de datos de entrada, debe estar disponible (en estado 'Listo') para la actividad de procedimiento almacenado ejecutar. No se puede consumir el conjunto de datos de entrada en el procedimiento almacenado como un parámetro. Solo se utiliza para comprobar la dependencia antes de iniciar la actividad de procedimiento almacenado. | No
resultados | Debe especificar un conjunto de datos de salida para una actividad de procedimiento almacenado. Conjunto de datos de salida especifica la **programación** de la actividad de procedimiento almacenado (cada hora, semanalmente, mensualmente, etcetera). <br/><br/>El conjunto de datos de salida debe usar un **servicio vinculada** que hace referencia a una base de datos de SQL Azure o un almacén de datos de SQL Azure o una base de datos de SQL Server en el que desea que el procedimiento almacenado para ejecutar. <br/><br/>El conjunto de datos de salida puede servir como una manera de pasar el resultado del procedimiento almacenado para posteriores otra actividad ([encadenar actividades](data-factory-scheduling-and-execution.md#chaining-activities)) en la canalización de procesamiento. Sin embargo, el generador de datos escribir automáticamente el resultado de un procedimiento almacenado para este conjunto de datos. Es el procedimiento almacenado que escribe en una tabla SQL que el conjunto de datos de salida de destino. <br/><br/>En algunos casos, el conjunto de datos de salida puede ser un **conjunto de datos ficticia**, que solo se usa para especificar la programación para ejecutar la actividad de procedimiento almacenado. | Sí
storedProcedureName | Especifique el nombre del procedimiento almacenado en la base de datos de SQL Azure o el almacén de datos de SQL Azure representada por el servicio vinculado que usa la tabla de resultados. | Sí
storedProcedureParameters | Especificar valores para los parámetros de procedimiento almacenado. Si necesita pasar null para un parámetro, use la sintaxis: "param1": null (todas las minúsculas). Vea el siguiente ejemplo para obtener información sobre el uso de esta propiedad.| No

## <a name="passing-a-static-value"></a>Envío de un valor estático 
Ahora, veamos agregar otra columna denominada 'Escenario' en la tabla que contiene un valor estático denominado 'Documento muestra'.

![Datos de ejemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
    
    AS
    
    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

Ahora, pase el parámetro de escenario y el valor de la actividad de procedimiento almacenado. La sección typeProperties en el ejemplo anterior es similar a la siguiente fragmento de código:

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": 
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }

