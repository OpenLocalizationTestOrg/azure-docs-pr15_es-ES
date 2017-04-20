## <a name="repeatability-during-copy"></a>Repetibilidad durante la copia

Al copiar datos desde y hasta stores relacionales, debe tener repetibilidad en cuenta para evitar resultados no deseados. 

Un sector puede volver a ejecutar automáticamente en el generador de datos de Azure según la directiva de reintento especificada. Recomendamos que establezca una directiva de reintento a protegerse frente a errores transitorias. Por lo tanto, repetibilidad es un aspecto importante ocuparse de durante el movimiento de datos. 

**Como un origen:**

> [AZURE.NOTE] Los ejemplos siguientes son para SQL Azure, pero son aplicables a cualquier almacén de datos que admita rectangular conjuntos de datos. Puede que tenga que ajustar el **tipo** de origen y la propiedad de **consulta** (por ejemplo: consulta en lugar de sqlReaderQuery) para los datos almacenar.   

Normalmente, al leer de stores relacionales, que desee leer únicamente los datos correspondientes a ese sector. Una manera de hacerlo sería mediante las variables WindowStart y WindowEnd disponibles en el generador de datos de Azure. Obtenga información sobre las variables y funciones en el generador de datos de Azure aquí en el artículo de la [programación y ejecución](../articles/data-factory/data-factory-scheduling-and-execution.md) . Ejemplo: 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Esta consulta lee datos 'MyTable' que se encuentra en el intervalo de duración de la segmentación de datos. Volver a ejecutar de este sector garantizará también siempre este comportamiento. 

En otros casos, puede que desee leer toda la tabla (suponga para una vez mover solo) y puede definir la sqlReaderQuery como sigue:

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
