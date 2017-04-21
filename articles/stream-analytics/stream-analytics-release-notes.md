<properties 
    pageTitle="Notas de la versión de análisis de secuencia | Microsoft Azure" 
    description="Notas de la versión de secuencia Analytics" 
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

#<a name="stream-analytics-release-notes"></a>Notas de la versión de análisis de secuencia

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notas de la versión 04/15/2016 de análisis de secuencia ##

Esta versión contiene la siguiente actualización.

Título | Descripción
---|---
Disponibilidad general de los resultados de Power BI  | [Resultados de Power BI](stream-analytics-power-bi-dashboard.md) está disponible de forma general. Se ha quitado la caducidad de la autorización de 90 días para Power BI. Para obtener más información sobre los escenarios donde debe renovarse autorización consulte la sección de [autorización de renovación](stream-analytics-power-bi-dashboard.md#Renew-authorization) de la creación de un panel de Power BI.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notas de versión de 03/03/2016 de análisis de secuencia ##

Esta versión contiene la siguiente actualización.

Título | Descripción
---|---
Nuevos elementos de lenguaje de consulta de análisis de secuencia  | SAQL tiene ahora [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN página"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN página") y [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN página").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notas de la versión 10/12/2015 de análisis de secuencia ##

Esta versión contiene la siguiente actualización.

Título | Descripción
---|---
Actualización de la versión de API de REST | La versión de la API de REST se ha actualizado a 2015-10-01. Encontrará detalles en MSDN en la [Referencia de la API de REST de secuencia de análisis de administración](https://msdn.microsoft.com/library/azure/dn835031.aspx) y la [integración de aprendizaje de equipo en el análisis de secuencia](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Integración de aprendizaje de máquina Azure | Con esta versión viene Azure funciones definidas por el usuario de aprendizaje del equipo de soporte técnico. Consulte el [tutorial](stream-analytics-machine-learning-integration-tutorial.md) para obtener más información, así como el [anuncio de blog general](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notas de versión de 12/11/2015 de análisis de secuencia ##

Esta versión contiene la siguiente actualización.

Título | Descripción
---|---
Nuevo comportamiento de selección | Seleccione en el análisis de secuencia se ha ampliado para permitir * como un descriptor de acceso de la propiedad de un registro anidado. Para obtener más información, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipos de datos complejos").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notas para 22/10/2015 versión de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.

Título | Descripción
---|---
Características de lenguaje de consulta adicionales | Análisis de secuencia ha ampliado el lenguaje de consulta, incluyendo las siguientes características: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [Inicio de sesión](https://msdn.microsoft.com/library/azure/mt605290.aspx), [cuadrado](https://msdn.microsoft.com/library/azure/mt605288.aspx)y [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Quitar las limitaciones agregadas  | Esta versión elimina la limitación de 15 agregados en una consulta. Ahora es sin límite en el número de agregados por la consulta.
Característica de grupo por System.Timestamp agregada | La función [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) ahora permite window_type o [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
DESPLAZAMIENTO de agregado de rotación y recursos de windows | De forma predeterminada, windows [rotación](https://msdn.microsoft.com/library/azure/dn835055.aspx) y [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) están alineados con un tiempo de cero (1/1/0001 12:00:00 A.M. UTC). El nuevo parámetro (opcional) 'offsetsize' permite especificar un desplazamiento personalizado (o alineación).


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notas para 29/09/2015 versión de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.

Título | Descripción
---|---
Vista previa de Azure IoT Suite público | Análisis de secuencia se incluye en la versión preliminar pública de Azure IoT Suite.
Integración de Portal de Azure | Además de mantener la presencia en el portal de administración de Azure, análisis de secuencia ahora está integrado en el [Portal de Azure](https://azure.microsoft.com/overview/preview-portal/). Nota que análisis de flujo de funciones en el portal de vista previa está actualmente un subconjunto de la funcionalidad ofrecida en el portal de administración de Azure, sin soporte para probar la consulta en el explorador, Power BI salida configuración y la exploración a o crear nueva entrada y salida recursos en suscripciones que tiene acceso.
Soporte técnico para la salida de DocumentDB | Ahora pueden enviar trabajos de análisis de secuencia a [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Compatibilidad con entrada de concentrador IoT | Tareas de análisis de secuencia ahora pueden recopilar datos de Hubs IoT.
Marca de tiempo por eventos heterogéneos | Cuando una única secuencia de datos contiene varios tipos de eventos con marcas de tiempo en distintos campos, puede ahora usar [Por la marca de tiempo](http://msdn.microsoft.com/library/mt573293.aspx) con expresiones para especificar los campos de marca de tiempo diferente para cada caso.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notas de la versión 10/09/2015 de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.

Título|Descripción
---|---
Soporte técnico para grupos de PowerBI|Para habilitar el uso compartido de datos con otros usuarios de Power BI, ahora pueden escribir trabajos de análisis de secuencia a [grupos de PowerBI](stream-analytics-define-outputs.md#power-bi) dentro de su cuenta de Power BI.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notas de versión de 20/08/2015 de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.

Título|Descripción
---|---
ÚLTIMA función de agregado |La [última](http://msdn.microsoft.com/library/mt421186.aspx) función ahora está disponible en los trabajos de análisis de secuencia, lo que le permite recuperar el evento más reciente en una secuencia de eventos dentro de un intervalo de tiempo determinado.
Nuevas funciones de matriz|Funciones de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) y [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) ahora están disponibles.
Nuevas funciones de registro|Funciones de registro [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) y [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) ahora están disponibles.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notas de versión de 30/07/2015 de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.

Título|Descripción
---|---
Identificador de organización de Power BI desacoplado de Id. de Azure|Esta característica permite a [los resultados de Power BI](stream-analytics-power-bi-dashboard.md) para trabajos ASA en cualquier tipo de cuenta de Azure (Live Id o ID Org.). Además, puede tener un identificador de organización para su cuenta de Azure y utilizar uno diferente para autorizar el resultado de Power BI.
Soporte técnico para la salida de colas de Bus de servicio|[Colas de Bus de servicio](stream-analytics-define-outputs.md#service-bus-queues) salidas ahora están disponibles en los trabajos de análisis de secuencia.
Soporte técnico para la salida de temas de Bus de servicio|Resultados de [Temas de Bus de servicio](stream-analytics-define-outputs.md#service-bus-topics) están ahora disponibles en los trabajos de análisis de la secuencia.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notas para 09/07/2015 versión de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.


Título|Descripción
---|---
Personalizar Blob partición de salida|Salidas de almacenamiento de blobs permiten ahora una opción para especificar la frecuencia con que se escribe BLOB los resultados y la estructura y el formato de la estructura de carpetas de ruta de acceso de datos de salida. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notas para 05/03/2015 versión de análisis de secuencia ##

Esta versión contiene las siguientes actualizaciones.


Título|Descripción
---|---
Mayor valor máximo fuera de lugar tolerancia ventana|El tamaño máximo de la ventana de tolerancia fuera de orden es ahora 59:59 (SS)
Formato de salida JSON: Línea separados o una matriz.|Ahora hay una opción cuando se muestren al almacenamiento de blobs o concentrador de evento resultados como una matriz de objetos JSON o separando objetos JSON con una nueva línea. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notas de la versión 16/04/2015 de análisis de secuencia ##


Título|Descripción
---|---
Retraso en la configuración de la cuenta de almacenamiento de Azure|Al crear una tarea de análisis de secuencia en una región por primera vez, le pedirá crear una nueva cuenta de almacenamiento o especificar una cuenta existente para la supervisión de trabajos de análisis de secuencia de esa región. Debido a la latencia en la configuración de supervisión, crear otro trabajo de análisis de secuencia de la misma región en 30 minutos pedirá la especificación de una segunda cuenta de almacenamiento en lugar de mostrar el recientemente configurado uno en la lista desplegable de supervisión de la cuenta de almacenamiento. Para evitar la creación de una cuenta de almacenamiento innecesaria, espere 30 minutos después de crear un trabajo en una región por primera vez antes de aprovisionar trabajos adicionales de esa región.
Actualización de trabajo|En este momento, el análisis de secuencia no admite modificaciones directo a la definición o la configuración de un trabajo en ejecución. Para cambiar la entrada, salida, consulta, escala o configuración de un trabajo en ejecución, primero debe detener el trabajo.
Tipos de datos que se infieren de origen de entrada|Si no se utiliza una instrucción CREATE TABLE, el tipo de entrada se deriva de formato de entrada, por ejemplo, todos los campos de CSV son cadena. Campos deben convertir explícitamente al tipo adecuado utilizando la función de conversión para evitar errores de tipo no coinciden.
Campos que faltan se va a producir como valores nulos|Hacer referencia a un campo que no está en el origen de entrada se traducirán en valores nulos en el evento de salida.
CON las instrucciones deben preceder a las instrucciones SELECT|En la consulta, las instrucciones SELECT deben seguir subconsultas definidas con instrucciones.
Problema de la memoria|Reinicie Streaming trabajos de análisis con una tolerancia grande para eventos fuera de orden o mantener que una gran cantidad de estado puede causar el trabajo de quedarse sin espacio de memoria, lo que un trabajo de consultas complejas. Las operaciones de inicio y detención se podrán ver en los registros de operación del trabajo. Para evitar este comportamiento, escalar la consulta a varias particiones. En una versión futura, esta limitación se solucionará mediante afecten al rendimiento en afectada trabajos en lugar de reiniciar ellos.
Entradas de grandes BLOBs sin marca de tiempo de carga pueden causar el problema de la memoria|Consumo archivos grandes de almacenamiento de blobs de Windows podría provocar trabajos de análisis de secuencia bloqueo si no se especifica a través de marca de tiempo por un campo de marca de tiempo. Para evitar este problema, mantener cada blob menos de 10MB de tamaño.
Limitación de volumen de eventos de base de datos de SQL|Cuando usa la base de datos de SQL como un destino de salida, muy grandes volúmenes de datos de salida pueden hacer el trabajo de análisis de secuencia hasta que desaparezca. Para resolver este problema, reduzca el volumen de salida mediante el uso de operadores de filtro o agregados o elija almacenamiento de blobs de Windows Azure o evento Hubs como un destino de salida.
Conjuntos de datos de PowerBI sólo pueden contener una tabla|PowerBI no admite más de una tabla en un conjunto de datos determinado.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
