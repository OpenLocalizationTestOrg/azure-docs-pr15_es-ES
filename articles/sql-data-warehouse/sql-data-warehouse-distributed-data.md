<properties
   pageTitle="Los datos se distribuyen y distribuir opciones de tabla para los sistemas de masivamente paralelo procesamiento (MPP) del almacén de datos de SQL y almacenamiento de datos paralelo | Microsoft Azure"
   description="Obtenga información sobre cómo los datos se distribuyen para masivamente paralelo procesamiento (MPP) y las opciones para distribuir tablas en el almacén de datos de SQL Azure y almacenamiento de datos paralelo."
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
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Datos distribuidos y tablas distribuidas para masivamente paralelo procesamiento (MPP)

Obtenga información sobre cómo se distribuyen los datos de usuario en el almacén de datos de SQL Azure y almacenamiento de datos paralelo, que son sistemas de masivamente paralelo procesamiento (MPP) de Microsoft. Diseñar el almacén de datos para usar de manera eficaz datos distribuidos le ayuda a lograr las beneficios de la arquitectura MPP de procesamiento de consultas. Algunas opciones de diseño de base de datos pueden tener un impacto significativo sobre cómo mejorar el rendimiento de la consulta.  

>[AZURE.NOTE] Almacén de datos de SQL de Azure y almacenamiento de datos paralelo utilizan el mismo diseño de masivamente paralelo procesamiento (MPP), pero tienen algunas diferencias debido a la plataforma subyacente. Almacén de datos de SQL es una plataforma como servicio (PaaS) que se ejecuta en Azure. Almacenamiento de datos paralelo se ejecuta en el análisis de plataforma de sistema (PA), que es un dispositivo local que se ejecuta en Windows Server.

## <a name="what-is-distributed-data"></a>¿Qué es datos distribuidos?

En el almacén de datos SQL y almacenamiento de datos paralelo, datos distribuidos hace referencia a datos de usuario que se almacenaban en varias ubicaciones en el sistema MPP. Cada una de las ubicaciones funciona como una unidad de procesamiento que se ejecute consultas en su parte de los datos y el almacenamiento independiente. Datos distribuidos están fundamentales para ejecutar consultas en paralelo para lograr el rendimiento de las consultas alto.

Para distribuir los datos, el almacén de datos asigna cada fila de una tabla de usuario a una ubicación distribuida.  Puede distribuir tablas con un método de distribución de hash o un turnos. El método de distribución se especifica en la instrucción CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tablas de hash distribuida
  
El siguiente diagrama muestra cómo se almacena un completo (tabla no distribuida) como una tabla hash distribuida. Una función determinista asigna cada fila al que asignará una distribución. En la definición de tabla, una de las columnas está designada como la columna de distribución. La función hash utiliza el valor de la columna de distribución para asignar cada fila de una distribución.

Hay consideraciones de rendimiento para la selección de una columna de distribución, como distinción, sesgo de datos y los tipos de consultas que se ejecutan en el sistema.
  
![Tabla distribuido] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabla distribuido")  
  
-   Cada fila pertenece a una distribución.  
  
-   Algoritmo de hash determinista asigna cada fila de una distribución.  
  
-   El número de filas de tabla por distribución varía según se indica por los distintos tamaños de tablas.

## <a name="round-robin-distributed-tables"></a>Tablas de turnos distribuidos

Una tabla de turnos distribuida distribuye las filas asignando cada fila de una distribución de forma secuencial. Es rápido cargar los datos en una tabla de turnos, pero el rendimiento de la consulta podría ser más lento.  Unirse a una tabla de turnos normalmente requiere reorganización las filas para habilitar la consulta generar un resultado exacto, que se tarda.

## <a name="distributed-storage-locations-are-called-distributions"></a>Ubicaciones de almacenamiento distribuido se denominan distribuciones

Cada ubicación distribuida se denomina una distribución. Cuando se ejecuta una consulta en paralelo, cada distribución realiza una consulta SQL en su parte de los datos. Almacén de datos SQL usa la base de datos de SQL para ejecutar la consulta. Almacenamiento de datos paralelo usa SQL Server para ejecutar la consulta. Este diseño de arquitectura compartir nada es fundamental para lograr la informática en paralelo de escalado.

### <a name="can-i-view-the-distributions"></a>¿Puedo ver las distribuciones?

Cada distribución tiene un identificador de distribución y se ve en vistas del sistema que pertenecen a almacenamiento de datos paralelo y almacén de datos de SQL. Puede usar el identificador de distribución para solucionar problemas de rendimiento de la consulta y otros problemas. Para obtener una lista de las vistas del sistema, consulte la [vista del sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Diferencia entre una distribución y un nodo de cálculo

Una distribución es la unidad básica para almacenar datos distribuidos y procesamiento de consultas en paralelo. Las distribuciones se agrupan en los nodos de cálculo. Cada nodo de cálculo realiza un seguimiento de una o más distribuciones.  

-   Sistema de plataforma de análisis utiliza nodos de cálculo como un componente central de las capacidades de arquitectura y escalado de hardware. Siempre usa ocho distribuciones por nodo de cálculo, como se muestra en el diagrama para tablas hash distribuida. El número de nodos de cálculo y, por lo tanto, el número de distribuciones, se determinan el número de nodos de cálculo que comprar para el dispositivo. Por ejemplo, si compra ocho nodos de cálculo, obtendrá 64 distribuciones (8 nodos x 8 distribuciones/nodo de cálculo). 

-   Almacén de datos de SQL tiene un número fijo de 60 distribuciones y un número flexible de nodos de cálculo. Se implementan los nodos de cálculo con los recursos de cálculo y almacenamiento Azure. Según la carga de trabajo del servicio de back-end y la capacidad informática (DWUs) especificadas para el almacén de datos, puede cambiar el número de nodos de cálculo. Cuando se cambia el número de nodos de cálculo, también se cambia el número de distribuciones por nodo de cálculo. 

### <a name="can-i-view-the-compute-nodes"></a>¿Puedo ver los nodos de cálculo?

Cada nodo de cálculo tiene un identificador de nodo y se ve en las vistas de sistema que pertenecen a almacenamiento de datos paralelo y almacén de datos de SQL.  Puede ver el nodo de cálculo para la columna node_id en vistas del sistema cuyos nombres empiezan por sys.pdw_nodes. Para obtener una lista de las vistas del sistema, consulte la [vista del sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Replicar tablas para el almacén de datos en paralelo 
  
Se aplica a: almacén de datos en paralelo

Además de usar tablas distribuidas, almacenamiento de datos paralelo ofrece una opción para replicar tablas. Una *tabla replicada* es una tabla que se almacena en su totalidad en cada nodo de cálculo. Duplicar una tabla, elimina la necesidad para transferir sus filas de tabla entre los nodos de cálculo antes de usar la tabla en una combinación o agregación. Las tablas duplicadas sólo están factibles con tablas pequeñas por el almacenamiento adicional necesario para almacenar la tabla completa en cada nodo de cálculo.  
  
El siguiente diagrama muestra una tabla replicada que se almacena en cada nodo de cálculo. La tabla replicada se almacena en todos los discos asignados al nodo de cálculo. Esta estrategia de disco se implementa mediante grupos de archivos de SQL Server.  
  
![Tabla replicada] (media/sql-data-warehouse-distributed-data/replicated-table.png "Tabla replicada") 
  
## <a name="next-steps"></a>Pasos siguientes
  
Para usar tablas distribuidos eficaz, consulte [las tablas de distribución en el almacén de datos de SQL](sql-data-warehouse-tables-distribute.md)  
  



  
