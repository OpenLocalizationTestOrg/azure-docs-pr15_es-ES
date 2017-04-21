<properties
   pageTitle="Carga de trabajo de almacén de datos"
   description="Elasticidad del almacén de datos de SQL le permite aumentar, reducir o pausar potencia informática con una escala móvil de unidades de almacén de datos (DWUs). Este artículo explica las estadísticas de almacén de datos y cómo se relacionan DWUs. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Carga de trabajo de almacén de datos
Una carga de trabajo del almacén de datos se refiere a todas las operaciones que pasar en un almacén de datos. La carga de trabajo del almacén de datos abarca todo el proceso de cargar datos en el almacén, realización de análisis de informes en el almacén de datos, administración de datos en el almacén de datos y exportación de datos desde el almacén de datos. La amplitud y estos componentes suelen ser proporcionales con el nivel de madurez del almacén de datos.


## <a name="new-to-data-warehousing"></a>¿Nuevo en el almacenamiento de datos?
Un almacén de datos es un conjunto de datos que se carga desde uno o varios orígenes de datos y se usan para realizar tareas de inteligencia empresarial, como informes y análisis de datos.

Datawarehouses se caracterizan por consultas que examine un mayor número de filas, grandes rangos de datos y pueden devolver resultados relativamente grandes para los fines de informes y análisis. Datawarehouses también se caracterizan por cargas de datos relativamente grande frente a pequeñas transacción nivel inserciones, actualizaciones y eliminaciones.

- Un almacén de datos funciona mejor cuando los datos se almacenan en una forma que optimiza las consultas que necesitan analizar grandes cantidades de filas o grandes rangos de datos. Este tipo de análisis funciona mejor cuando los datos se almacenan y buscar por columnas, en lugar de por filas.

>[AZURE.NOTE] El índice columnstore en memoria, que utiliza el almacenamiento de la columna, proporciona hasta 10 ganancias de compresión y 100 x mejoras de rendimiento de consulta sobre árboles binarios tradicionales para las consultas de creación de informes y análisis. Se considere columnstore índices como estándar para almacenar y análisis de datos de gran tamaño en un almacén de datos.

- Un almacén de datos tiene requisitos distintos de un sistema que se optimiza para las transacciones en línea (OLTP) de procesamiento. El sistema OLTP tiene muchos Insertar, actualizar y eliminar operaciones. Estas operaciones buscan filas específicas de la tabla. Tabla búsquedas realizar mejor cuando los datos se almacenan en forma de fila por fila. Los datos se pueden ordenar y rápidamente la búsqueda con una división y conquistar enfoque llamada una búsqueda binaria de árbol o árbol b.


## <a name="data-loading"></a>Carga de datos
Carga de datos es una gran parte de la carga de trabajo del almacén de datos. Empresas que normalmente cuentan con un sistema OLTP ocupado que realiza un seguimiento de cambios durante el día cuando los clientes generan transacciones de la empresa. Periódicamente, a menudo por la noche durante una ventana de mantenimiento, las transacciones se movidas o copiadas al almacén de datos. Una vez que los datos están en el almacén de datos, analistas pueden realizar análisis y tomar decisiones empresariales en los datos.

- Tradicionalmente, el proceso de carga se denomina ETL para extraer, transformar y cargar. Normalmente, los datos deben transformar para que sea coherente con otros datos en el almacén de datos. Anteriormente, empresas que utilizan servidores ETL dedicados para realizar las transformaciones. Ahora, con dicha transformación rápida masivos en paralelo puede cargar los datos en el almacén de datos SQL en primer lugar y, a continuación, realice las transformaciones. Este proceso se denomina extraer, cargar y transformar (ELT) y se convierte en un nuevo estándar para la carga de trabajo del almacén de datos.

> [AZURE.NOTE] Con SQL Server 2016, ahora puede realizar análisis en tiempo real en una tabla de OLTP. Esta información no reemplaza la necesidad de un almacén de datos almacenar y analizar datos, pero proporciona una manera de realizar análisis en tiempo real.

### <a name="reporting-and-analysis-queries"></a>Consultas de creación de informes y análisis
Las consultas de creación de informes y análisis con qué frecuencia se clasifican en pequeño, mediano y grande según varios criterios, pero normalmente se basa en el tiempo. En la mayoría almacén de datos, hay una carga de trabajo mixta de ejecución fast frente a las consultas de larga ejecución. En cada caso, es importante para determinar esta mezcla y determinar su frecuencia (cada hora, diariamente, fin de mes, fin de trimestre etc.). Es importante comprender que la carga de trabajo mixta de consulta, junto con simultaneidad, clientes potenciales al diseño de un almacén de datos de capacidad adecuada.

- Planificación de capacidad puede ser una tarea compleja para una carga de trabajo mixta de consulta, especialmente cuando necesita una hora de entrega a largo plazo para agregar capacidad al almacén de datos. Almacén de datos SQL quita la urgencia de planificación de capacidad puesto que puede reducir y ampliar la capacidad de cálculo en cualquier momento y desde el almacenamiento y la capacidad de cálculo se tamaño independientemente.

### <a name="data-management"></a>Administración de datos
Administración de datos es importante, especialmente cuando se sabe que podría quedarse sin espacio de espacio en disco en el futuro. Normalmente, datawarehouses dividen los datos en rangos significativos, que se almacenan como particiones en una tabla. Todos los productos de SQL Server le permiten mover particiones dentro y fuera de la tabla. Esta partición cambiar le permite mover los datos más antiguos a almacenamiento menos costoso y mantener los datos más recientes disponibles en el almacenamiento en línea.

- Los índices ColumnStore admiten tablas con particiones. Los índices columnstore, tablas con particiones se usan para la administración de datos y archivado. Para tablas almacenadas por filas, particiones tienen una función más grande en el rendimiento de la consulta.  

- PolyBase desempeña una función importante en la administración de datos. Usa PolyBase, tiene la opción Archivar datos antiguos a Hadoop o Azure almacenamiento de blobs.  Esto ofrece muchas opciones, ya que los datos aún están en línea.  Podría tardar para recuperar datos de Hadoop, pero el compromiso de tiempo de recuperación puede superar el costo de almacenamiento.

### <a name="exporting-data"></a>Exportación de datos
Es una forma de hacer disponible para los informes y análisis de datos enviar datos del almacén de datos a servidores dedicado para ejecutar informes y análisis. Estos servidores se denominan puestos de datos. Por ejemplo, podría procesar previamente los datos del informe y exportar del almacén de datos a muchos servidores todo el mundo que visibilidad para los clientes y analistas.

- Para generar informes, cada noche podría rellenar servidores de informes de sólo lectura con una instantánea de los datos diarias. Esto le da un mayor ancho de banda de los clientes mientras disminuye las necesidades de recursos de cálculo en el almacén de datos. Desde un aspecto de seguridad, puestos de datos le permiten reducir el número de usuarios que tienen acceso al almacén de datos.
- Para análisis, puede generar un cubo de análisis en el almacén de datos y ejecutar análisis en el almacén de datos o procesar previamente los datos y exportar en el servidor de análisis para obtener más análisis.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya conoce un poco de almacén de datos de SQL, obtenga información sobre cómo rápidamente [crear un almacén de datos de SQL][] y [cargar los datos de ejemplo][].

<!--Image references-->

<!--Article references-->
[cargar datos de ejemplo]: ./sql-data-warehouse-load-sample-databases.md
[crear un almacén de datos de SQL]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
