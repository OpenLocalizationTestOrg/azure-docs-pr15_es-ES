<properties
    pageTitle="Usar tablas de búsqueda y datos de referencia en el análisis de secuencia | Microsoft Azure"
    description="Usar datos de referencia en una consulta de análisis de secuencia"
    keywords="tabla de búsqueda, datos de referencia"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Uso de tablas de datos o búsquedas de referencia en una secuencia de entrada de análisis de secuencia

Datos de referencia (también conocido como una tabla de búsqueda) están un conjunto de datos finito estáticos o ralentizar cambiando en naturaleza, utilizada para realizar una búsqueda o relacionar con el flujo de datos. Para utilizar datos de referencia en el trabajo de análisis de secuencia de Azure generalmente utiliza una [Combinación de datos de referencia](https://msdn.microsoft.com/library/azure/dn949258.aspx) en la consulta. Análisis de secuencia usa almacenamiento de blobs de Windows Azure como la capa de almacenamiento de datos de referencia y con referencia del generador de datos de Azure de transformación o copiados al almacenamiento de blobs de Windows Azure, para usar como referencia de datos de [cualquier número de nube según y almacena datos locales](../data-factory/data-factory-data-movement-activities.md)datos. Datos de referencia es modelar como una secuencia de BLOB (definido en la configuración de entrada) en orden ascendente de la fecha y hora especificada en el nombre de blobs de Windows. **Sólo** admite agregar al final de la secuencia mediante una fecha y hora **mayor** que el especificado por el último blob en la secuencia.

## <a name="configuring-reference-data"></a>Configurar los datos de referencia

Para configurar los datos de referencia, debe crear una entrada de tipo de **Datos de referencia**. La siguiente tabla explica todas las propiedades que debe proporcionar al crear los datos de referencia de entrada con su descripción:

<table>
<tbody>
<tr>
<td>Nombre de propiedad</td>
<td>Descripción</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Un nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento donde se encuentran los archivos de blobs de Windows. Si está en la misma suscripción como el trabajo de análisis de secuencia, puede seleccionar de la lista hacia abajo.</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento. Esto se rellena automáticamente si la cuenta de almacenamiento en la misma suscripción como el trabajo de análisis de secuencia.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica para blobs almacenado en el servicio de Microsoft Azure Blob. Cuando se carga un blob al servicio Blob, debe especificar un contenedor de blob.</td>
</tr>
<tr>
<td>Trama de ruta de acceso</td>
<td>La ruta de acceso para localizar el BLOB dentro del contenedor especificado. Dentro de la ruta de acceso, puede especificar una o varias instancias de las siguientes 2 variables:<BR>{date}, {time}<BR>Ejemplo 1: products/{date}/{time}/product-list.csv<BR>Ejemplo 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si ha utilizado {date} dentro del modelo de ruta de acceso que ha especificado, puede seleccionar el formato de fecha en la que se organizan los archivos en la lista desplegable de formatos admitidos. Ejemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si ha utilizado {time} dentro del modelo de ruta de acceso que ha especificado, puede seleccionar el formato de hora en que se organizan los archivos en la lista desplegable de formatos admitidos. Ejemplo: HH</td>
</tr>
<tr>
<td>Formato de serialización de evento</td>
<td>Para asegurarse de que las consultas funcionan del modo esperado, análisis de secuencia necesita saber qué formato de serialización usa para las secuencias de datos entrantes. Datos de referencia, los formatos admitidos son CSV y JSON.</td>
</tr>
<tr>
<td>Codificación</td>
<td>UTF-8 es el formato de codificación compatible solo en este momento</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Generación de datos de referencia en una programación

Si los datos de la referencia están un conjunto de datos cambian lentamente, a continuación, soporte técnico para actualizar la referencia de datos está habilitados especificando una trama de ruta de acceso en la configuración de entrada con la fecha de {} y {hora} símbolos de sustitución. Análisis de secuencia seleccionará las definiciones de datos de referencia actualizada basadas en este modelo de ruta de acceso. Por ejemplo, un modelo de `sample/{date}/{time}/products.csv` con un formato de fecha **"dd-MM-aaaa"** y la hora formato de **"Hh: mm"** indica que el análisis de secuencia a recoger el blob actualizado `sample/2015-04-16/17:30/products.csv` a 5:30 P.M. 16 de abril de 2015 UTC zona horaria.

> [AZURE.NOTE] Actualmente trabajos de análisis de secuencia busque la actualización de blobs solo cuando el tiempo de máquina avanza a la vez codificada en el nombre de blobs. Por ejemplo buscará el trabajo `sample/2015-04-16/17:30/products.csv` tan pronto como sea posible, pero no una versión anterior a 5:30 P.M. 16 de abril de 2015 UTC zona horaria. Lo hará *nunca* , busque un archivo con una versión anterior a la última que se detecta un tiempo codificado.
> 
> Por ejemplo una vez que el trabajo encuentra el blob `sample/2015-04-16/17:30/products.csv` omitirá cualquier archivo con una codificación fecha anterior a 5:30 P.M. 16 de abril de 2015 por lo que si un límite que llegan `sample/2015-04-16/17:25/products.csv` blob se creará en el mismo contenedor el trabajo no usarán.
> 
> Del mismo modo si `sample/2015-04-16/17:30/products.csv` sólo se produce en 10:03 P.M. 16 de abril de 2015 pero ningún blob con una fecha anterior está presente en el contenedor, el trabajo utilizará este archivo comenzando en 10:03 P.M. 16 de abril de 2015 y usar los datos de referencia anterior hasta ese momento.
> 
> Una excepción a esta regla se inicia cuando el trabajo debe volver a procesar datos en el tiempo o el trabajo es la primero. En hora de inicio que del trabajo está buscando el más reciente blob fabrican antes el trabajo Iniciar hora especificada. Para ello, para asegurarse de que hay un conjunto de datos de referencia **no vacíos** cuando se inicia el trabajo. Si no se encuentra uno, el trabajo mostrará el siguiente diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.


[Generador de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) puede usarse para organizar la tarea de crear el actualizado BLOB necesarios para el análisis de secuencia actualizar definiciones de datos de referencia. Generador de datos es un servicio de integración de datos basados en la nube que organiza y automatiza el movimiento y la transformación de datos. Generador de datos es compatible con [conexión a una gran cantidad de basada en la nube y almacena de datos local](../data-factory/data-factory-data-movement-activities.md) y mover datos fácilmente de forma regular que especifique. Para obtener más información e instrucciones paso a paso configurar una canalización del generador de datos para generar los datos de referencia para el análisis de flujo que se actualiza en una programación predefinida, consulte este [ejemplo GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Sugerencias sobre cómo actualizar los datos de referencia ##

1. Sobrescribir BLOB de datos de referencia no hará que el análisis de secuencia volver a cargar el blob y en algunos casos puede hacer el trabajo no se realice correctamente. Es la manera recomendada para cambiar los datos de referencia agregar un nuevo blob con el mismo contenedor definido en la entrada de trabajo de trama de ruta de acceso y utilizar una fecha y hora **mayor** que el especificado por el último blob en la secuencia.
2.  Datos de referencia BLOB no está ordenado por el tiempo de "Última modificación" de blobs sino solo por la hora y la fecha especificada en el objeto binario de nombre con la fecha de {} y sincronizar {} sustituciones.
3.  En algunas ocasiones que un trabajo debe ir hacia atrás, por lo tanto, referencia datos BLOB debe no puede modificar o eliminar.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce a análisis de secuencia, un servicio de transmisión de análisis de datos de Internet de lo administrado. Para obtener más información sobre este servicio, consulte:

- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
