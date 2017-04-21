<properties
    pageTitle="Nivel de compatibilidad, cómo evaluar | Microsoft Azure"
    description="Pasos y herramientas para determinar qué nivel de compatibilidad es la mejor para la base de datos en la base de datos de SQL Azure o Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Mejora el rendimiento de la consulta con 130 de nivel de compatibilidad de base de datos de SQL Azure


Base de datos de SQL Azure se ejecuta transparente cientos de miles de bases de datos en muchos niveles de compatibilidad diferentes, conservar y garantizar la compatibilidad con versiones anteriores a la versión correspondiente de Microsoft SQL Server para todos sus clientes!

Por lo tanto, nada impide que a los clientes que alteran las bases de datos existentes en el nivel de compatibilidad más reciente de beneficiarse de las características de procesador de consulta y el nuevo optimizador de consultas. Como un aviso de historial, la alineación de las versiones SQL para niveles de compatibilidad de predeterminados son los siguientes:

- 100: en SQL Server 2008 y SQL Azure V11 de base de datos.
- 110: en SQL Server 2012 y SQL Azure V11 de base de datos.
- 120: en SQL Server 2014 y SQL Azure V12 de base de datos.
- 130: en SQL Server 2016 y SQL Azure V12 de base de datos.


> [AZURE.IMPORTANT] A partir de **mediados de junio 2016**, en la base de datos de SQL Azure, el nivel de compatibilidad predeterminado será 130 en lugar de 120 para bases de datos **recién creado** .
> 
> Bases de datos creadas antes de mediados de junio de 2016 le *no* se verá afectado y mantendrá su nivel de compatibilidad actual (100, 110 o 120). Bases de datos que migración desde la versión de la base de datos de SQL Azure que V11 a V12 no tendrá su nivel de compatibilidad ha cambiado uno.


En este artículo se exploración los beneficios de nivel de compatibilidad 130 y cómo aprovechar las ventajas. Dirección de los efectos secundarios posibles en el rendimiento de la consulta para las aplicaciones de SQL existentes.


## <a name="about-compatibility-level-130"></a>Sobre el nivel de compatibilidad 130


En primer lugar, si desea conocer el nivel de compatibilidad actual de la base de datos, ejecute la siguiente instrucción de Transact-SQL.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Antes de que este cambio a nivel 130 ocurre para bases de datos **recién** creado, vamos a revisar qué este es todo sobre algunos ejemplos de consulta muy básica y vea cómo todos los usuarios pueden beneficiarse de él.

Procesamiento de consultas de bases de datos relacionales puede ser muy complejo y puede provocar una gran cantidad de informática y matemáticas para conocer las opciones de diseño inherente y comportamientos. En este documento, el contenido se ha simplificado deliberadamente para asegurarse de que todos los usuarios con información técnica mínimo pueden comprender el impacto del cambio de nivel de compatibilidad y determinar cómo pueden beneficiarse aplicaciones.

Echemos un vistazo a lo que proporciona el nivel de compatibilidad 130 a la tabla.  Puede encontrar más detalles en [Modificar el nivel de compatibilidad de base de datos (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), pero este es un breve resumen:

- La operación de inserción de una instrucción Insert-select pueden ser multiproceso o puede tener un plan en paralelo, mientras que antes de un único subproceso esta operación.
- Memoria optimizado tabla y consultas de variables de tabla pueden tener ahora planes en paralelo, mientras que antes de que esta operación se también un único subproceso.
- Estadísticas de memoria optimizado tabla pueden tomarse y están actualizadas de forma automática. Consulte [Novedades de motor de base de datos: OLTP en memoria](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) para obtener más detalles.
- Modo de lote v/s fila modo cambia con los índices de columna de la tienda
  - Ordena en una tabla con un índice de columna de la tienda está en modo por lotes.
  - Agregados de ventanas ahora funcionan en modo de proceso por lotes como las instrucciones de ADELANTO o POSPOSICIÓN de TSQL.
  - Trabajar con consultas en tablas de almacén de columna con varias cláusulas distinct en modo por lotes.
  - Consultas en ejecución en DOP = 1 o con un plan en serie también ejecutar en modo por lotes.
- Por último, mejoras de cardinalidad estimación realmente vienen con el nivel de compatibilidad 120, pero para los que se ejecuta en un nivel de compatibilidad inferior (es decir, 100 o 110), vaya a la compatibilidad nivel 130 también aparecerá estas mejoras y estos pueden beneficiarse también el rendimiento de la consulta de las aplicaciones.


## <a name="practicing-compatibility-level-130"></a>Nivel de compatibilidad de práctica 130


Primero vamos a obtener algunas tablas, índices y datos aleatorios creados para practicar algunas de estas nuevas capacidades. Los ejemplos de secuencia de comandos TSQL se pueden ejecutar en SQL Server 2016 o en la base de datos de SQL Azure. Sin embargo, cuando se crea una base de datos de SQL Azure, asegúrese de que elegir como mínimo una base de datos de P2 porque necesita al menos un par de núcleos permitir subprocesos múltiples y, por tanto, beneficiarse de estas características.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Ahora, echemos un vistazo a algunas de las características de procesamiento de consultas procedentes de nivel de compatibilidad 130.


## <a name="parallel-insert"></a>Insertar paralela


Ejecutar las instrucciones de TSQL a continuación, ejecuta la operación de INSERCIÓN en el nivel de compatibilidad 120 y 130, que ejecuta respectivamente la operación de INSERCIÓN en un solo modelo encadenado (120) y en un modelo de multiproceso (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Solicitando los datos reales del plan de consulta, mostrando su representación gráfica o su contenido XML, puede determinar qué estimación de cardinalidad función es en reproducir. Buscando lo planes en paralelo en la figura 1, podemos ver claramente que la ejecución de insertar de la tienda en la columna se va de serie en 120 en paralelo en 130. Recuerde que el cambio del icono iterador en el plan de 130 que muestra dos flechas paralelas, que ilustra el hecho de que ahora la ejecución del iterador es en realidad paralelo. Si tiene grandes operaciones de INSERCIÓN para completar, la ejecución en paralelo, vinculada al número del núcleo que tiene a su disposición para la base de datos funcionan mejor; hasta un 100 veces más rápido según su situación.


*Figura 1: Insertar operación cambios serie a paralelo 130 de nivel de compatibilidad con versiones anteriores.*


![Figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>Modo por lotes serie


Del mismo modo, permite procesamiento de modo por lotes mover a nivel de compatibilidad 130 al procesamiento de filas de datos. En primer lugar, operaciones de modo por lotes solo están disponibles cuando tiene un índice de columna de la tienda en su lugar. En segundo término, un lote normalmente representa aproximadamente 900 filas y usa una lógica de código optimizada para CPU multinúcleo, mayor rendimiento de memoria y aprovecha directamente los datos comprimidos de la tienda de columna siempre que sea posible. En estas condiciones, SQL Server 2016 puede procesar aproximadamente 900 filas a la vez, en lugar de la 1 fila en el momento y en consecuencia, todo el lote, lo que reduce el costo por fila general ahora comparten el costo total de la operación. Esta cantidad compartida de operaciones combinadas con la compresión de la tienda de columna básicamente reduce la latencia implicados en una operación de modo por lotes SELECT. Puede encontrar más detalles acerca de la tienda de columna y por lotes el modo de la [Guía de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Como visible, a continuación, observando la consulta planes en paralelo en la figura 2, podemos ver que el modo de procesamiento ha cambiado con el nivel de compatibilidad y, en consecuencia, al ejecutar las consultas en el nivel de compatibilidad de ambos totalmente, podemos ver que la mayor parte de la hora de procesamiento se dedica en modo de fila (86%) en comparación con el modo por lotes (14%), donde se han procesado 2 lotes. Aumentar el conjunto de datos, aumentará el beneficio.


*Figura 2: Seleccione cambios de operación de serie en modo por lotes con el nivel de compatibilidad 130.*


![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Modo por lotes en ejecución de ordenación


Similar a la anterior, pero aplicado a una operación de ordenación, la transición de modo de fila (nivel de compatibilidad 120) de modo por lotes (nivel de compatibilidad 130) mejora el rendimiento de la operación de ordenación por las mismas razones.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Visible de forma simultánea en figura 3, podemos ver que la operación de ordenación en el modo de fila representa 81% de costo, mientras que el modo por lotes solo representa 19% del costo (respectivamente 81% y 56% en el criterio de ordenación).


*Figura 3: Ordenar los cambios de la operación de fila en modo por lotes con el nivel de compatibilidad 130.*


![Figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Evidentemente, estos ejemplos sólo contienen miles de filas, que es nada cuando examina los datos disponibles en la mayoría de los servidores de SQL estos días. Solo estos en millones de filas en su lugar del proyecto y, a continuación, se puede traducir en unos minutos de ejecución repuesto diario pendiente de la naturaleza de la carga de trabajo.


## <a name="cardinality-estimation-ce-improvements"></a>Mejoras de cardinalidad estimación (CE)


Introdujo con SQL Server 2014, cualquier base de datos que se ejecuta en un nivel de compatibilidad 120 o por encima de hará que el uso de la estimación de cardinalidad nueva funcionalidad. Básicamente, estimación de cardinalidad es la lógica que se utiliza para determinar cómo SQL server ejecutará una consulta basada en su costo estimado. La estimación se calcula con la entrada de estadísticas asociadas con objetos implicados en esa consulta. Prácticamente, de alto nivel, funciones de cardinalidad estimación son las estimaciones de recuento de fila junto con información sobre la distribución de los valores de valor distinto de la cuenta, y recuentos duplicados contenidos en las tablas y los objetos que se hace referencia en la consulta. Recibe estas estimaciones incorrecta, puede producir disco innecesaria i/OS debido a concede de memoria insuficiente (es decir, TempDB vertidos), o a una selección de la ejecución de un plan en serie sobre la ejecución de un plan en paralelo, entre otros. Conclusión, calcula incorrecta puede producir una degradación del rendimiento general de la ejecución de consultas. En el otro lado, calcula mejor, estimaciones más precisas, clientes potenciales con la ejecución de consulta mejor!

Como se mencionó anteriormente, estimaciones y las optimizaciones de consulta son un asunto complejo, pero si desea obtener más información sobre los planes de consulta y Estimador de cardinalidad, puede hacer referencia al documento en [Optimizar sus planes de consultas con el Estimador de cardinalidad 2014 de SQL Server](https://msdn.microsoft.com/library/dn673537.aspx) para un análisis más profundo.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>¿Qué estimación de cardinalidad utiliza actualmente?


Para determinar en qué estimación de cardinalidad se están ejecutando las consultas, vamos a simplemente use los ejemplos de consulta siguiente. Tenga en cuenta que en el primer ejemplo se ejecutará en el nivel de compatibilidad 110, lo que implica el uso de las funciones de cardinalidad estimación anteriores.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Una vez completada la ejecución, haga clic en el vínculo XML y ver las propiedades del primer iterador como se muestra a continuación. Anote el nombre de propiedad denominado CardinalityEstimationModelVersion establecida actualmente en 70. No significa que el nivel de compatibilidad de base de datos se establece en la versión de SQL Server 7.0 (se establece en 110 como visible en las instrucciones de TSQL arriba), pero el valor 70 simplemente representa la funcionalidad de cardinalidad estimación heredada disponible desde SQL Server 7.0, que no hay revisiones principales hasta SQL Server 2014 (que se suministra con un nivel de compatibilidad de 120).


*Figura 4: El CardinalityEstimationModelVersion se establece en 70 cuando se usa un nivel de compatibilidad de 110 o inferior.*


![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Como alternativa, puede cambiar el nivel de compatibilidad a 130 y deshabilitar el uso de la nueva función estimación de cardinalidad mediante la LEGACY_CARDINALITY_ESTIMATION establece en activado con [ALTERAR la configuración del ámbito de la base de datos](https://msdn.microsoft.com/library/mt629158.aspx). Se trata exactamente lo mismo que utilizar 110 desde el punto de vista de la función de cardinalidad estimación, mientras utiliza el nivel de compatibilidad de procesamiento de consultas más reciente. De este modo, puede aprovecharse de la nueva consulta procesamiento características llegar con el nivel de compatibilidad más reciente (es decir, el modo por lotes), pero aún dependen de la funcionalidad de cardinalidad estimación anterior si es necesario.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Simplemente mover al nivel de compatibilidad 120 o 130 permite la nueva funcionalidad de cardinalidad estimación. En este caso, el valor predeterminado CardinalityEstimationModelVersion se establecerá en consecuencia a 120 o 130 como visible debajo.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: Cuando se usa un nivel de compatibilidad de 130 se establece la CardinalityEstimationModelVersion a 130.*


![Figura 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Investigación de testimonios recogidos las diferencias de cálculo de cardinalidad


Ahora, vamos a ejecutar un poco más complejos relacionados con una operación INNER JOIN con una cláusula WHERE con algunas predicados de consulta y veamos la estimación de número de fila de la función estimación de cardinalidad antigua en primer lugar.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Ejecutar la consulta de forma eficaz devuelve 200.704 filas, mientras la estimación de la fila con la funcionalidad de cardinalidad estimación antigua reclamaciones 194,284 filas. Evidentemente, como dicho antes, estos resultados del recuento de fila también dependerá de la frecuencia con ha encontrado los ejemplos anteriores, que rellena las tablas de ejemplo y otra vez en cada ejecución. Evidentemente, los predicados de la consulta también tendrá una influencia sobre la estimación real aparte de la forma de tabla, datos de contenido, y cómo estos datos realmente relacionar entre sí.


*Figura 6: La estimación de número de fila es 194,284 o 6.000 filas desactivar desde las 200.704 filas lo esperadas.*


![Figura 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


En la misma manera, vamos a ahora ejecutar la misma consulta con la nueva funcionalidad de cardinalidad estimación.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Mirar el a continuación, ahora vemos que la estimación de fila es 202,877, o mucho más detalles y superior de la estimación de cardinalidad antiguo.

*Figura 7: La estimación de número de fila es ahora 202,877, en lugar de 194,284.*


![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


En realidad, el resultado obtenido es 200.704 filas (pero todo depende de la frecuencia con ejecutó las consultas de los ejemplos anteriores, pero lo más importante, porque TSQL utiliza la instrucción RAND, los valores devueltos pueden variar de una ejecución a la siguiente). Por lo tanto, en este ejemplo concreto, el nuevo cálculo de cardinalidad realiza un mejor trabajo estimar el número de filas porque es mucho más cerca de 200,704, que 194,284 202,877! Por último, si cambia predicados de la cláusula WHERE para igualdad (en lugar de ">" por ejemplo), esto podría realizar las estimaciones entre la antigua y nueva función cardinalidad aún más diferente, dependiendo de cuántos coincide con la puede obtener.

Evidentemente, en este caso, está ~ 6000 filas desactivar de recuento real no representa una gran cantidad de datos en algunos casos. Ahora, transponer para millones de filas a través de varias tablas y consultas más complejas y, a veces, la estimación puede estar desactivada por millones de filas y por tanto, el riesgo de que el plan de ejecución de un problema de seguridad de selección o solicitando concede de memoria insuficiente llevando a TempDB vertidos e i/OS más por lo tanto, son mucho más alto.

Si tiene la oportunidad de práctica esta comparación con sus consultas y conjuntos de datos, más habitual para comprobar por la cantidad de algunas de las estimaciones nuevas y antiguas se ven afectadas, mientras algunos ha convertido más fuera de la realidad o algunos otros simplemente simplemente más cerca a la fila real cuenta realmente devuelto en los conjuntos de resultados. Todo dependerá de la forma de las consultas, las características de base de datos de SQL Azure, la naturaleza y el tamaño de los conjuntos de datos y las estadísticas disponibles acerca de ellos. Si acaba de crear la instancia de base de datos de SQL Azure, tendrá el optimizador de consultas generar su conocimiento desde cero en lugar de volver a utilizar las estadísticas de la consulta se ejecuta anterior. Por lo tanto, las estimaciones son muy contextuales y casi específicos para cada situación de servidor y la aplicación. Es un aspecto importante a tener en cuenta.


## <a name="some-considerations-to-take-into-account"></a>Algunas consideraciones a tener en cuenta


Aunque la mayoría de las cargas de trabajo debería beneficiarse desde el nivel de compatibilidad 130, antes de adoptar el nivel de compatibilidad en su entorno de producción, básicamente tiene 3 opciones:

1. Desplazarse hasta el nivel de compatibilidad 130 y vea cómo realizan acciones. En caso de que observe algunas retrocesos, simplemente la compatibilidad volver a establecer nivel su nivel original o mantener 130 y solo inversa copia de la estimación de cardinalidad en el modo heredado (como se explica anteriormente, esto solo puede solucionar el problema).
2. Probar las aplicaciones existentes en la carga de producción similares, ajustar y validar el rendimiento antes de pasar a producción. En caso de problemas, igual que el anterior, puede volver siempre al nivel de compatibilidad original, o simplemente invertir la estimación de cardinalidad volver al modo heredado.
3. Como una opción de final y la manera más reciente a estas preguntas, es aprovechar el almacén de consulta. Eso es la opción recomendada de hoy. Para facilitar el análisis de las consultas en compatibilidad redistribuir 120 o por debajo de frente a 130, podemos le recomendamos lo suficientemente usar almacén de consulta. Almacén de consulta está disponible con la versión más reciente de V12 de base de datos de SQL Azure, y está diseñado para ayudarle con la solución de problemas de rendimiento de consulta. Piense en la tienda de consulta como una grabadora de datos de vuelo para la base de datos recopilar y presentar la información de historial detallada acerca de todas las consultas. Esto simplifica en gran medida el análisis del rendimiento reduciendo el tiempo para diagnosticar y solucionar problemas. Puede encontrar más información en [almacén de consulta: un registrador de datos de vuelo para la base de datos](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


Al alto nivel, si ya tiene un conjunto de bases de datos que se ejecuta en el nivel de compatibilidad 120 o por debajo y planea mover algunos de ellos a 130 o porque la carga de trabajo automáticamente aprovisionar nuevas bases de datos que se pueden establecer pronto a 130, de forma predeterminada, considere las siguientes:

- Antes de cambiar al nuevo nivel de compatibilidad en producción, habilite el almacén de consulta. Puede hacer referencia a [cambiar el modo de compatibilidad de base de datos y usar el almacén de consulta](https://msdn.microsoft.com/library/bb895281.aspx) para obtener más información.
- A continuación, pruebe críticas todas las cargas de trabajo con el representante de datos y consultas de un entorno de producción y comparar el rendimiento experimentado y como notificado por almacén de consulta. Si experimenta algunos retrocesos, puede identificar las consultas regressed en la tienda de consulta y usar el plan forzar la opción de almacén de consulta (también conocido como plan anclada). En este caso, definitivamente mantenerse con el nivel de compatibilidad 130 y usar el plan de consulta antiguo como sugerido por el almacén de consulta.
- Si desea aprovechar las nuevas características y funciones de base de datos SQL Azure (que se ejecuta SQL Server 2016), pero dependen de los cambios realizados por el nivel de compatibilidad 130, como último recurso, puede considerar la posibilidad de forzar el nivel de compatibilidad de nuevo en el nivel que se adapte a su carga de trabajo mediante una instrucción ALTER DATABASE. Pero en primer lugar, tenga en cuenta que el plan de consulta de la tienda anclada opción es la mejor opción porque no utiliza 130 básicamente es permanecer en el nivel de la funcionalidad de una versión anterior de SQL Server.
- Si tiene aplicaciones multiempresa que abarcan varias bases de datos, puede ser necesario actualizar la lógica de aprovisionamiento de las bases de datos para garantizar un nivel de compatibilidad coherente en todas las bases de datos; los antiguos y recientemente creados. El rendimiento de carga de trabajo de la aplicación podría ser confidencial al hecho de que algunas bases de datos que se están ejecutando en niveles de compatibilidad diferente y, por lo tanto, podría ser necesario para proporcionar la misma experiencia a sus clientes todos en el panel de la coherencia de nivel de compatibilidad en cualquier base de datos. Tenga en cuenta que no es obligatorio, realmente depende de cómo afecta el nivel de compatibilidad de la aplicación.
- Por último, sobre la estimación de cardinalidad e igual que cambiar el nivel de compatibilidad, antes de continuar en producción, se recomienda probar la carga de trabajo de producción en las condiciones nuevas para determinar si la aplicación de beneficios de las mejoras de cardinalidad estimación.


## <a name="conclusion"></a>Conclusión


Usar la base de datos de SQL Azure para aprovechar todas las mejoras de SQL Server 2016 claramente puede mejorar la ejecución de consulta. Al igual que-es! Por supuesto, como cualquier nueva función, debe hacerse una evaluación adecuada para determinar las condiciones exactas en el que la carga de trabajo de la base de datos funciona mejor. Experiencia muestra que la mayoría de la carga de trabajo se espera al menos ejecuten transparente en el nivel de compatibilidad 130, aprovechando nueva consulta procesamiento funciones y nueva estimación de cardinalidad. Que dice realista, siempre hay algunas excepciones y realizar vencimiento NOMPROPIO diligencia es una evaluación para determinar cuánto puede beneficiarse de estas mejoras importante. Y vuelva a la tienda de consulta puede ser de una gran ayuda para hacer este trabajo!

Como evolucionar SQL Azure, puede esperar un nivel de compatibilidad 140 en el futuro. Cuando la hora es apropiada, Comenzaremos hablando de lo que le llevará este nivel de compatibilidad futura 140 como brevemente explicamos aquí qué nivel de compatibilidad 130 es poner hoy.

Por ahora, no se olvide, a partir de junio de 2016, base de datos de SQL Azure cambiará el nivel de compatibilidad predeterminado de 120 a 130 para bases de datos recién creados. ¡Tenga en cuenta!


## <a name="references"></a>Referencias


- [Novedades de motor de base de datos](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog: Almacén de consulta: una grabadora de datos de vuelo para la base de datos, por Borko Novakovic, 8 de junio de 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Nivel de compatibilidad de base de datos de ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [MODIFICAR LA CONFIGURACIÓN DE ÁMBITO DE LA BASE DE DATOS](https://msdn.microsoft.com/library/mt629158.aspx)

- [Nivel de compatibilidad 130 para V12 de base de datos SQL Azure](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Optimizar la consulta planes con SQL Server Estimador de cardinalidad de 2014](https://msdn.microsoft.com/library/dn673537.aspx)

- [Guía de índices ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog: Mejor rendimiento de la consulta con nivel de compatibilidad 130 de base de datos SQL Azure, por Alain Lissoir, 6 de mayo de 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
