Generador de datos de Azure es compatible con las actividades de transformación siguientes que se pueden agregar a canalizaciones individualmente o encadenadas con otra actividad.

Actividad de transformación de datos |  Calcular el entorno 
:----------------------- | :--------------------
[Sección](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Cerdo](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop transmisión](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina actividades de aprendizaje: ejecución por lotes y recursos de actualización](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procedimiento almacenado](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, almacenamiento de datos SQL Azure o SQL Server |
[Datos de análisis de lago U SQL](../articles/data-factory/data-factory-usql-activity.md) | Análisis de datos de Azure lago 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] o lote de Azure
   
> [AZURE.NOTE] 
> Puede usar MapReduce actividad para ejecutar programas de motor en el clúster HDInsight Spark. Consulte [motor invocar programas de la fábrica de datos de Azure](../articles/data-factory/data-factory-spark.md) para obtener información detallada.
> Puede crear una actividad personalizada para ejecutar las secuencias de comandos de R en el clúster HDInsight con R instalado. Consulte [Ejecutar R Script con el generador de datos de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).