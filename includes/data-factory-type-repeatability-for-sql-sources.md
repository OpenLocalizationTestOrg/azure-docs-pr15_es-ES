## <a name="repeatability-during-copy"></a>Repetibilidad durante la copia

Copiar datos a Azure SQL y SQL Server de otros datos almacena uno debe tener repetibilidad en cuenta para evitar resultados no deseados. 

Al copiar datos a la base de datos de Azure SQL y SQL Server, va a copiar actividad predeterminada ANEXAR el conjunto de datos a la tabla de receptor de forma predeterminada. Por ejemplo, al copiar datos de un origen de archivo CSV (datos de valores separados por comas) que contiene dos registros a la base de datos de Azure SQL y SQL Server, esto es el aspecto de la tabla:
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Supongamos que se encuentra errores en el archivo de origen y actualiza la cantidad de abajo tubo de 2 a 4 en el archivo de origen. Si vuelve a ejecutar la segmentación de datos de datos de ese período, encontrará dos nuevos registros anexados a la base de datos de Azure SQL y SQL Server. El a continuación supone ninguna de las columnas de la tabla tiene la restricción de clave principal.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Para evitar esto, debe especificar semántica UPSERT aprovechando uno de los debajo 2 mecanismos que se indica a continuación.

> [AZURE.NOTE] Un sector se puede volver a ejecutar automáticamente en el generador de datos de Azure según la directiva de reintento especificada.

### <a name="mechanism-1"></a>Mecanismo de 1

Puede aprovechar la propiedad **sqlWriterCleanupScript** para realizar primero la acción de limpieza cuando se ejecuta un sector. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

La secuencia de comandos de limpieza ejecutarán primera durante la copia de un sector dado que desea eliminar los datos de la tabla de SQL correspondiente a ese sector. La actividad insertará posteriormente los datos en la tabla de SQL. 

Si el sector está ahora vuelva a ejecutar y luego encontrará que la cantidad se actualiza como deseado.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Supongamos que el registro de arandela plana se quita de la csv original. A continuación, volver a ejecutar la segmentación de datos ' d dan el resultado siguiente: 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Nada nuevo tenía hacerse. La actividad de copia ejecutó la secuencia de comandos de limpieza para eliminar los datos correspondientes para ese sector. Después de leer la entrada, desde el archivo csv (que, a continuación, incluidos registro solo 1) y se inserta en la tabla. 

### <a name="mechanism-2"></a>Mecanismo de 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName no es compatible para el almacén de datos de SQL Azure en este momento. 

Otro mecanismo para lograr repetibilidad es tener una columna dedicada (**sliceIdentifierColumnName**) en la tabla de destino. Esta columna se usaría generador de datos de Azure para asegurarse de que el origen y destino están sincronizadas. Este método funciona cuando hay flexibilidad para cambiar o definir el esquema de la tabla de SQL de destino. 

Esta columna se usaría generador de datos de Azure fines repetibilidad y en el proceso de fábrica de datos de Azure no realizar cambios de esquema en la tabla. Forma de usar este método:

1.  Definir una columna de tipo binario (32) en la tabla de SQL de destino. No debería restricciones en esta columna. Vamos a nombre esta columna como 'ColumnForADFuseOnly' para este ejemplo.
2.  Usarlo en la actividad de copia como sigue:

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Generador de datos de Azure llenará esta columna según su necesidad de que origen y destino están sincronizadas. Los valores de esta columna no se recomienda fuera de este contexto por el usuario. 

Similar al mecanismo de 1, copiar actividad hará automáticamente limpiar primero los datos de la segmentación de datos determinado de la tabla de SQL de destino y vuelva a ejecutar la actividad de copia normalmente para insertar los datos de origen a destino para ese sector. 
