<properties
   pageTitle="Extraer datos SQL en Azure evento Hubs | Microsoft Azure"
   description="Importar información general de los centros de evento de ejemplo SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>Extraer datos de SQL a un concentrador de evento de Azure

Una arquitectura típica para una aplicación para el procesamiento de datos en tiempo real implica que primero la presión a un concentrador de evento de Azure. Puede ser un escenario IoT, como la supervisión del tráfico en estira diferentes de una autopista, o un escenario de juegos, supervisar las acciones de un exceso de encargan frenética o un escenario de empresa, como la supervisión de pertenencia de creación. En estos casos, los fabricantes de datos son objetos externos generalmente producir datos de la serie de tiempo que necesita para recopilar, analizar, almacenar y activan el proceso y puede han invertido una gran cantidad de esfuerzo en la creación de la infraestructura para estos procesos. ¿Qué puede hacer si desea incorporar datos de algo similar a una base de datos en lugar de un origen de datos de transmisión y usar junto con otros datos streaming? ¿Considere el caso donde desea usar el análisis de secuencia de Azure, el Explorador de datos remota (RDX) o alguna otra herramienta para analizar y actuar en un sistema de administración de instalaciones personalizado o lentamente cambios en los datos de Microsoft Dynamics AX? Para solucionar este problema, hemos escrito y fuente abierta una muestra de nube pequeña que se puede modificar e implementar que extraer los datos de una tabla SQL y enviarlo a un concentrador de evento de Azure usar como entrada en las aplicaciones analíticas descendentes. Se da cuenta que se trata de un escenario raro y lo opuesto a lo que normalmente haría con un concentrador de evento. Sin embargo, si se encuentra en la situación donde esto es lo tiene que hacer, puede encontrar el código en la Galería de ejemplos de Azure, [aquí](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Observe que el código de este ejemplo es igual, una muestra. Es **no** pretende ser una aplicación de producción y se han realizado ningún intento para que sea adecuado para su uso en un entorno: es stricly una y MANUALIDADES, programador centrados, ejemplo. Debe revisar a todos los tipos de seguridad, rendimiento, funcionalidad y costo factores antes de establecerse en una arquitectura de llevar a cabo.

## <a name="application-structure"></a>Estructura de la aplicación

La aplicación está escrita en C# y el archivo readme.md en los datos de ejemplo contiene toda la información que necesita modificar, crear y publicar la aplicación. Las siguientes secciones proporcionan una descripción general de las funciones de la aplicación.

Empezaremos con la suposición de que tiene acceso a una tabla de SQL Azure. También necesitará ha configurado un concentrador de evento de Azure y sabe la conexión de cadena que es necesario tener acceso a ella.

Cuando se inicia la solución SqlToEventHub, lee un archivo de configuración (App.config) para obtener una serie de cosas, como se describe en el archivo readme.md. Estos son bastante fácil de entender, como el nombre de la tabla de datos, etcetera y no es necesario para rehash las explicaciones aquí. 

Una vez que la aplicación ha leído el archivo de configuración, pasa en un bucle, leer la tabla SQL e insertar registros en el hub de evento, a continuación, espera un intervalo de espera definidas por el usuario antes de hacerlo nuevamente. Algunas cosas son destacar:

1. La aplicación se basa en el supuesto de que se actualiza la tabla SQL por algún proceso externo y que desea enviar todas las y solo las actualizaciones a un concentrador de evento.
2. La tabla SQL debe tener un campo que tiene un número creciente y único, por ejemplo, un número de registro. Esto puede ser tan sencillo como un campo denominado "Id" o nada que se incrementa como lo actualiza dicha base de datos agrega registros como "Creation_time" o "Sequence_number". La aplicación de notas y almacena el valor de ese campo en cada iteración. En cada paso subsiguientes el bucle, la aplicación, esencialmente, consulta la tabla para todos los registros donde el valor de ese campo supera el valor ha visto la última vez el bucle. Llamamos a este último valor "desvío".
3. La aplicación crea una tabla "TableOffsets" cuando se inicia, almacenar los desplazamientos. La tabla se crea con la consulta "CreateOffsetTableQuery" se define en el archivo de configuración. 
4. Hay varias consultas que se utiliza para trabajar con la tabla de desplazamiento, definida en el archivo de configuración como "OffsetQuery", "UpdateOffsetQuery" y "InsertOffsetQuery". No debe cambiar estos.
5. Por último, la consulta "DataQuery" definido en el archivo de configuración es la que se ejecutará para extraer los registros de la tabla SQL. Está actualmente limitado a los registros de la parte superior a 1.000 en cada paso del bucle para optimización - si, por ejemplo, 25.000 registros se han agregado a la base de datos desde la última consulta, podría tardar un tiempo para ejecutar la consulta. Limitar la consulta a 1.000 registros cada vez, las consultas son mucho más rápidas. Selección de la parte superior de 1.000 simple, sucesivos lotes de 1.000 registros inserta en el hub de evento.    

## <a name="next-steps"></a>Pasos siguientes

Para implementar la solución, clonar o descargar la aplicación SqlToEventHub, modifique el archivo App.config, generar y finalmente publicarla. Una vez que haya publicado la aplicación, puede ver en ejecución en el portal de Azure clásico en servicios de nube y supervisar los eventos en cuanto a su centro de evento. Tenga en cuenta que la frecuencia vendrá determinada por dos cosas: la frecuencia de las actualizaciones de la tabla SQL y el intervalo de suspensión especificado en el archivo de configuración de la aplicación.