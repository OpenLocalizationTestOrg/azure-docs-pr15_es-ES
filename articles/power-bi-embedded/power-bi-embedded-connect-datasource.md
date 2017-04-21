<properties
   pageTitle="Incrustados de Microsoft Power BI - conectarse a un origen de datos"
   description="Power BI incrustado, conectarse a orígenes de datos"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Conectarse a un origen de datos

Con **Power BI incrustado**, puede incrustar informes en su propia aplicación. Al insertar un informe de Power BI en la aplicación, el informe conecta a los datos subyacentes **importando** una copia de los datos o **conectarse directamente** con el origen de datos mediante **DirectQuery**.

Estas son las diferencias entre usar **Importar** y **DirectQuery**.

|Importar | DirectQuery
|---|---
|Tablas, columnas *y los datos* se importan o se copian en conjunto de datos del informe. Para ver los cambios que ocurrieron con los datos subyacentes, debe actualizar o importar un completo actual conjunto de datos nuevo.|Importar solo *tablas y columnas* o copia en el conjunto de datos del informe. Ver siempre los datos más recientes.
Con Power BI incrustado, puede utilizar DirectQuery con orígenes de datos de nube pero no local orígenes de datos en este momento.

## <a name="benefits-of-using-directquery"></a>Ventajas de usar DirectQuery

Cuando se usa **DirectQuery**, hay dos ventajas principales:

   -    **DirectQuery** le permite crear visualizaciones sobre muy grandes conjuntos de datos, donde lo contrario, sería informáticamente primera importar todos los datos.
   -    Subyacente cambios de datos puede requerir una actualización de datos, y para algunos de los informes, la necesidad de mostrar datos actuales puede requerir grandes transferencias de datos, lo vuelve a importar datos informáticamente. Por el contrario, **DirectQuery** siempre informes de uso de datos actual.

## <a name="limitations-of-directquery"></a>Limitaciones de DirectQuery

   Existen algunas limitaciones sobre cómo usar **DirectQuery**:

   -    Todas las tablas deben proceder de una base de datos.
   -    Si la consulta es demasiado compleja, se producirá un error. Para solucionar el error debe refactorizar la consulta para que sea menos compleja. Si la quuery debe ser compleja, debe importar los datos en lugar de usar **DirectQuery**.
   -    Relación de filtrado se limita a un solo sentido, en lugar de ambas direcciones.
   -    No puede cambiar el tipo de datos de una columna.
   -    De forma predeterminada, las limitaciones se colocan en las expresiones de DAX permitidas en medidas. Consulte [DirectQuery y medidas](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>DirectQuery y medidas

Para asegurarse de que las consultas enviadas al origen de datos subyacente tienen un rendimiento aceptable, se imponen limitaciones sobre medidas. Cuándo usar **Power BI Desktop**, los usuarios avanzados puede optar por omitir esta limitación eligiendo **archivo > Opciones y configuración > opciones**. En el cuadro de diálogo **Opciones** , elija **DirectQuery**y seleccione la opción **Permitir sin restricciones medidas en el modo DirectQuery**. Cuando se selecciona esta opción, puede utilizarse cualquier expresión de DAX que sea válido para una medida. Los usuarios deben tener en cuenta; Sin embargo, que algunas de las expresiones que realizar muy bien cuando se importan los datos puede traducirse en consultas muy lento al origen de back-end cuando se encuentra en el modo **DirectQuery** . 

## <a name="see-also"></a>Vea también
- [Introducción a Microsoft Power BI incrustado](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
