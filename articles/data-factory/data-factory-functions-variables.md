<properties 
    pageTitle="Funciones de fábrica de datos y las Variables del sistema | Microsoft Azure" 
    description="Proporciona una lista de funciones del generador de datos de Azure y variables del sistema" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Datos de Azure Factory - funciones y Variables del sistema
En este artículo se proporciona información sobre las funciones y variables compatibles con el generador de datos de Azure.
  
## <a name="data-factory-system-variables"></a>Variables del sistema de generador de datos

Nombre de la variable | Descripción | Ámbito de objeto | Ámbito JSON y casos de uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Inicio del intervalo de tiempo para ejecutar la ventana de actividad actual | actividad | <ol><li>Especifique las consultas de selección de datos. Vea los artículos de conector que se hace referencia en el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) .</li><li>Pasar parámetros de script de subárbol (muestra arriba).</li>
WindowEnd | Final del intervalo de tiempo para ejecutar la ventana de actividad actual | actividad | igual que el anterior
SliceStart | Inicio del intervalo de tiempo para el sector de datos que se produce | actividad<br/>conjunto de datos | <ol><li>Especifique los nombres y rutas de carpeta dinámica mientras trabaja con [conjuntos de datos de sistema de archivos](data-factory-onprem-file-system-connector.md)y de [Blobs de Windows Azure](data-factory-azure-blob-connector.md) .</li><li>Especificar las dependencias de entrada con las funciones de fábrica de datos en la colección de entradas de la actividad.</li></ol>
SliceEnd | Final del intervalo de tiempo para el sector actual de datos que se produce | actividad<br/>conjunto de datos | igual que el anterior. 

> [AZURE.NOTE] Actualmente generador de datos requiere que la programación especificada en la actividad exactamente coincida con la programación especificada en la disponibilidad del conjunto de datos de salida. Esto significa que WindowStart, WindowEnd y SliceStart y SliceEnd siempre se asignan al mismo período de tiempo y un sector único resultado.
 
## <a name="data-factory-functions"></a>Funciones del generador de datos 

Puede usar funciones en el generador de datos junto con encima de las variables del sistema mencionada para los siguientes propósitos:

1.  Especificar consultas de selección de datos (consulte los artículos de conector al que hace referencia el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) .

    La sintaxis de invocar una función de generador de datos es: ** $$ ** para las consultas de selección de datos y otras propiedades de la actividad y los conjuntos de datos.  
2. Especificar las dependencias de entrada con las funciones del generador de datos en la actividad entradas colección (consulte el ejemplo anterior).

    no es necesario $ para especificar expresiones de dependencia de entrada.   

En el ejemplo siguiente, la propiedad **sqlReaderQuery** en un archivo JSON se asigna a un valor devuelto por la función **Text.Format** . Este ejemplo también utiliza una variable de sistema denominada **WindowStart**, que representa la hora de inicio de la actividad de ejecutar la ventana.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Funciones

Las tablas siguientes enumeran todas las funciones de generador de datos de Azure:

Categoría | Función | Parámetros | Descripción
-------- | -------- | ---------- | ----------- 
Hora | AddHours(X,Y) | X: fecha y hora de <br/><br/>Y: int | Horas Y se agrega a la X de tiempo determinado. <br/><br/>Ejemplo: 9/5/2013 12:00:00 P.M. + 2 horas = 9/5/2013 2:00:00 P.M.
Hora | AddMinutes(X,Y) | X: fecha y hora de <br/><br/>Y: int | Agrega Y minutos x.<br/><br/>Ejemplo: 9/15/2013 12:00:00 P.M. + 15 minutos = 9/15/2013 12:15:00 P.M.
Hora | StartOfHour(X) | X: fecha y hora de | Obtiene la hora de inicio de la hora representada por el componente de hora de X. <br/><br/>Ejemplo: StartOfHour de 9/15/2013 es 05:10:23 PM 9/15/2013 05:00:00 P.M.
Fecha | AddDays(X,Y) | X: fecha y hora de<br/><br/>Y: int | Agrega días Y x.<br/><br/>Ejemplo: 9/15/2013 12:00:00 P.M. + 2 días = 9/17/2013 12:00:00 P.M.
Fecha | AddMonths(X,Y) | X: fecha y hora de<br/><br/>Y: int | Los meses Y se agrega a la X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 P.M. + 1 mes = 10/15/2013 12:00:00 P.M. 
Fecha | AddQuarters(X,Y) | X: fecha y hora de <br/><br/>Y: int | Agrega Y * 3 meses x.<br/><br/>Ejemplo: 9/15/2013 12:00:00 P.M. + 1 trimestre = 12/15/2013 12:00:00 P.M.
Fecha | AddWeeks(X,Y) | X: fecha y hora de<br/><br/>Y: int | Agrega Y * 7 días x<br/><br/>Ejemplo: 9/15/2013 12:00:00 P.M. + 1 semana = 9/22/2013 12:00:00 P.M.
Fecha | AddYears(X,Y) | X: fecha y hora de<br/><br/>Y: int | Agrega Y años a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 P.M. + 1 año = 9/15/2014 12:00:00 P.M.
Fecha | Day(X) | X: fecha y hora de | Obtiene el componente de día de X.<br/><br/>Ejemplo: Día de 9/15/2013 12:00:00 P.M. es 9. 
Fecha | DayOfWeek(X) | X: fecha y hora de | Obtiene el día del componente de semana de X.<br/><br/>Ejemplo: DayOfWeek de 9/15/2013 12:00:00 P.M. es el domingo.
Fecha | DayOfYear(X) | X: fecha y hora de | Obtiene el día del año representada por el componente de año de X.<br/><br/>Ejemplos:<br/>1/12/2015: día 335 de 2015<br/>31/12/2015: día 365 de 2015<br/>31/12/2016: día 366 de 2016 (salto año)
Fecha | DaysInMonth(X) | X: fecha y hora de | Obtiene los días del mes representado por el componente de mes del parámetro X.<br/><br/>Ejemplo: DaysInMonth de 9/15/2013 son 30 porque hay 30 días en el mes de septiembre.
Fecha | EndOfDay(X) | X: fecha y hora de | Obtiene la fecha y hora que representa el final del día (componente de días) de X.<br/><br/>Ejemplo: EndOfDay de 9/15/2013 es 05:10:23 PM 9/15/2013 11:59:59 PM.
Fecha | EndOfMonth(X) | X: fecha y hora de | Obtiene el extremo del mes representado por el componente de mes de parámetro X. <br/><br/>Ejemplo: EndOfMonth de 9/15/2013 05:10:23 PM es 30/9/2013 11:59:59 P.M. (hora de fecha que representa el final del mes de septiembre)
Fecha | StartOfDay(X) | X: fecha y hora de | Obtiene el inicio del día representado por el componente de día del parámetro X.<br/><br/>Ejemplo: StartOfDay de 9/15/2013 es 05:10:23 PM 9/15/2013 12:00:00 A.M..
Fecha y hora | FROM(X) | X: cadena de | Analizar cadena X a la hora de una fecha.
Fecha y hora | Ticks(X) | X: fecha y hora de | Obtiene las marcas de propiedad del parámetro X. Una marca es igual a 100 nanosegundos. El valor de esta propiedad representa el número de pasos transcurridos desde la medianoche del 1 de enero de 12:00:00 0001. 
Texto | Format(X) | X: variable de cadena de | Da formato al texto.

#### <a name="textformat-example"></a>Ejemplo de Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Vea [cadenas de formato de hora y fecha personalizada](https://msdn.microsoft.com/library/8kb3ddd4.aspx) tema que describe las diferentes opciones de formato puede utilizar (por ejemplo: AA frente aaaa). 

> [AZURE.NOTE] Cuando se usa una función en otra, no necesita usar **$$** prefijo de la función interna. Por ejemplo: $$Text.Format (' EC PartitionKey \\' my_pkey_filter_value\\' y RowKey página \\' {0:yyyy-MM-dd hh}\\", Time.AddHours (SliceStart -6)). En este ejemplo, observe que **$$** prefijo no se usa para la función **Time.AddHours** . 
  

