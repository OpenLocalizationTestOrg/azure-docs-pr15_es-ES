<properties
    pageTitle="Base de datos de SQL Azure y el rendimiento para bases de datos | Microsoft Azure"
    description="En este artículo puede ayudarle a determinar qué nivel de servicio para elegir para su aplicación. También se recomienda métodos para optimizar la aplicación para obtener el máximo partido de base de datos de SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Base de datos de SQL Azure y el rendimiento para bases de datos

Base de datos de SQL Azure ofrece tres [niveles de servicio](sql-database-service-tiers.md): básico, estándar y Premium. Cada nivel de servicio estrictamente aísla los recursos que puede usar la base de datos SQL y garantiza performance predecible para ese nivel de servicio. En este artículo, le ofrecemos orientación que puede ayudarle a elegir el nivel de servicio para la aplicación. También trataremos maneras que puede ajustar la aplicación para obtener el máximo provecho de base de datos de SQL Azure.

>[AZURE.NOTE] En este artículo se centra en la Guía de rendimiento de bases de datos en la base de datos de SQL Azure. Para obtener instrucciones de rendimiento relacionados con grupos de elásticos de la base de datos, vea [precio y rendimiento consideraciones de grupos de elásticos de la base de datos](sql-database-elastic-pool-guidance.md). No obstante, tenga en cuenta que muchas de las recomendaciones de ajuste en este artículo se aplican a bases de datos en un grupo de elásticos de la base de datos y obtener los beneficios de rendimiento similares.

Estos son los tres niveles de servicio de base de datos de SQL Azure que puede elegir entre (rendimiento se mide en unidades de rendimiento de base de datos o [DTUs](sql-database-what-is-a-dtu.md):

- **Básica**. La capacidad de previsión de servicio básico nivel ofrece un buen rendimiento para cada base de datos, la hora más de hora. En una base de datos básico suficientes recursos de soporte técnico buen rendimiento en una base de datos pequeña que no tiene varias solicitudes simultáneas.
- **Estándar**. El nivel de servicio estándar ofrece mejor rendimiento predicción y genera la barra de bases de datos que tienen varias solicitudes simultáneas, como aplicaciones web y de grupo de trabajo. Al elegir una base de datos de nivel de servicio estándar, puede cambiar el tamaño de la aplicación de la base de datos según el rendimiento predecible, minuto más minuto.
- **Premium**. El nivel de servicio Premium proporciona performance predecible, segundo en segundo término, para cada base de datos de Premium. Al elegir el nivel de servicio Premium, puede cambiar el tamaño de la aplicación de la base de datos en función de la carga de recursos asignadas para dicha base de datos. El plan quita casos en qué rendimiento varianza puede provocar consultas pequeñas a tardar más de lo esperado en operaciones latencia. En este modelo puede simplificar en gran medida los ciclos de validación de desarrollo y producto para las aplicaciones que necesitan realizar fuerte instrucciones acerca de las necesidades de recursos de recursos asignadas, variación de rendimiento o latencia de consultas.

En cada nivel de servicio, establezca el nivel de rendimiento, por lo que tendrá la flexibilidad para pagar solo la capacidad que necesita. Puede [ajustar la capacidad](sql-database-scale-up.md), hacia arriba o hacia abajo, como los cambios de carga de trabajo. Por ejemplo, si la carga de trabajo de la base de datos es alta durante la temporada de compra de vuelta al Cole, puede aumentar el nivel de rendimiento de la base de datos por un tiempo establecido, julio a septiembre. Se puede reducir cuando finaliza la temporada de recursos asignadas. Puede minimizar paga mediante la optimización de su entorno de nube para la estacionalidad de su empresa. Este modelo también funciona bien para ciclos de lanzamiento del producto de software. Un equipo de prueba podría asignar capacidad ejecuciones de prueba, y a continuación, suelte esa capacidad cuando terminan de pruebas. En un modelo de solicitud de capacidad pagar por la capacidad que necesite y evitar gastos en recursos dedicados que puede usar casi nunca.

## <a name="why-service-tiers"></a>¿Por qué niveles de servicio?

Aunque puede variar cada carga de trabajo de la base de datos, es la finalidad de niveles de servicio proporcionar predicción de rendimiento en varios niveles de rendimiento. Los clientes con los requisitos de recursos de base de datos a gran escala pueden trabajar en un entorno más dedicado.

### <a name="common-service-tier-use-cases"></a>Casos de uso de nivel de servicio comunes

#### <a name="basic"></a>Básico

- **Está comenzando a con la base de datos de SQL Azure**. Las aplicaciones que están en desarrollo a menudo no necesitan niveles de alto rendimiento. Bases de datos básicos son un entorno ideal para el desarrollo de la base de datos, en un punto de precio económico.
- **La base de datos con un solo usuario**. Aplicaciones que asociar un único usuario con una base de datos normalmente no tienen requisitos de simultaneidad y rendimiento alto. Estas aplicaciones son candidatos para el nivel de servicio básico.

#### <a name="standard"></a>Estándar

- **La base de datos tiene varias solicitudes simultáneas**. Las aplicaciones que más de un usuario del servicio a la vez normalmente necesitan niveles superiores de rendimiento. Por ejemplo, sitios Web que recibe tráfico moderado o aplicaciones departamentales que requieren más recursos es buenos candidatos para el nivel de servicio estándar.

#### <a name="premium"></a>Premium

La mayoría de los casos de uso Premium servicio nivel tienen uno o más de estas características:

- **Alto máximo de carga**. Una aplicación que requiere una gran cantidad de CPU, memoria o (i/OS) de entrada y salida para completar las operaciones requiere un nivel de dedicada de alto rendimiento. Por ejemplo, una operación de base de datos conocida consumir varios núcleos de CPU durante un tiempo prolongado es un candidato para el nivel de servicio Premium.
- **Número de solicitudes simultánea**. Algunas aplicaciones de base de datos del servicio muchas solicitudes simultáneas, por ejemplo, cuando servir un sitio Web que tiene un alto volumen de tráfico. Básicos y estándar niveles de servicio limitan el número de solicitudes simultáneas por base de datos. Aplicaciones que requieren más conexiones necesarias para elegir un tamaño de reserva correspondiente para controlar el número máximo de solicitudes de sea necesarios.
- **Latencia baja**. Algunas aplicaciones necesitan garantizar una respuesta de la base de datos en un tiempo mínimo. Si se llama a un procedimiento almacenado específico como parte de una operación de cliente más amplia, es posible que tenga un requisito tener una devolución de llamada en no más de 20 milisegundos, 99% del tiempo. Este tipo de beneficios de la aplicación desde el nivel de servicio Premium, para asegurarse de que la potencia requerida está disponible.

El nivel de servicio que necesita para la base de datos SQL depende de los requisitos de carga de recursos asignadas para cada dimensión de recursos. Algunas aplicaciones de usar una cantidad de un único recurso trivial, pero tienen requisitos significativos para otros recursos.

## <a name="service-tier-capabilities-and-limits"></a>Límites y las capacidades de nivel de servicio
Cada nivel de rendimiento y de nivel de servicio está asociado con diferentes límites y las características de rendimiento. Esta tabla describen estas características para una base de datos.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

Las siguientes secciones contienen más información sobre cómo ver el uso de estos límites.

### <a name="maximum-in-memory-oltp-storage"></a>Almacenamiento máximo OLTP en memoria

Puede usar la vista de **sys.dm_db_resource_stats** para supervisar el uso de almacenamiento de Azure en la memoria. Para obtener más información acerca de cómo supervisar, vea [almacenamiento de Monitor en memoria OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Actualmente, preview (OLTP) de procesamiento de transacciones en línea de Azure en la memoria sólo se admiten para bases de datos. No puede usarlo en bases de datos en grupos de elásticos de la base de datos.

### <a name="maximum-concurrent-requests"></a>Número máximo de solicitudes simultáneo

Para ver el número de solicitudes simultáneas, ejecute esta consulta Transact-SQL en la base de datos SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Para analizar la carga de trabajo de una base de datos de SQL Server local, modificar esta consulta para filtrar en la base de datos específico que desea analizar. Por ejemplo, si tiene una base de datos local denominado MiBaseDeDatos, esta consulta Transact-SQL devuelve el recuento de solicitudes simultáneas de dicha base de datos:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Esto es solo una instantánea en un momento determinado. Para obtener una mejor comprensión de los requisitos de solicitudes simultáneas y la carga de trabajo, debe recopilar muchos ejemplos a lo largo del tiempo.

### <a name="maximum-concurrent-logins"></a>Máximos inicios de sesión simultáneas

Puede analizar los patrones de usuario y la aplicación para hacerse una idea de la frecuencia de inicios de sesión. También puede ejecutar cargas reales en un entorno de prueba para asegurarse de que no están cumpliendo esta u otros límites que trataremos en este artículo. No hay una única consulta o la vista de administración dinámica (DMV) que puede mostrar simultáneas cuenta de inicio de sesión o el historial.

Si varios clientes usan la misma cadena de conexión, el servicio autentica cada inicio de sesión. Si 10 usuarios simultáneamente conectan a una base de datos con el mismo nombre de usuario y contraseña, sería 10 inicios de sesión simultáneas. Este límite se aplica solo a la duración de la sesión y la autenticación. Si el mismos 10 usuarios conectan a la base de datos secuencialmente, el número de inicios de sesión simultáneas nunca sea mayor que 1.

>[AZURE.NOTE] Actualmente, este límite no se aplica a bases de datos en grupos de elásticos de la base de datos.

### <a name="maximum-sessions"></a>Número máximo de sesiones

Para ver el número de sesiones activas actuales, ejecute esta consulta Transact-SQL en la base de datos SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si está analizando una carga de trabajo de SQL Server local, modifique la consulta para centrarse en una base de datos. Esta consulta le ayuda a determinar la necesidad de sesión de la base de datos si está pensando en mover a la base de datos de SQL Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Nuevamente, estas consultas devuelven un número de punto en el tiempo. Si recopilar varias muestras con el tiempo, deberá usar la mejor comprensión de la sesión.

Para el análisis de la base de datos de SQL, puede obtener estadísticas históricas en sesiones. **Sys.resource_stats**de la consulta y use la columna **active_session_count** . Vea la siguiente sección para obtener más información sobre el uso de esta vista.

## <a name="monitor-resource-use"></a>Supervisar el uso de recursos
Dos vistas pueden ayudarle a supervisar el uso de recursos para una base de datos SQL en relación con su nivel de servicio:

- [Sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>Sys.dm_db_resource_stats
Puede usar la vista de [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) en cada base de datos SQL. La vista **sys.dm_db_resource_stats** muestra datos de uso de recursos recientes en relación con el nivel de servicio. Promedio porcentajes de CPU, i/OS de datos, escribe de registro y memoria se registran cada 15 segundos y se mantienen para 1 hora.

Debido a esta vista proporciona un aspecto más granular uso de recursos, use **sys.dm_db_resource_stats** primera para los análisis de estado actual o para solucionar problemas. Por ejemplo, esta consulta muestra el uso de recursos de promedio y máximo para la base de datos actual sobre la última hora:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Para otras consultas, vea los ejemplos de [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>Sys.resource_stats

La vista de [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) en **la base de datos** contiene información adicional que puede ayudarle a supervisar el rendimiento de la base de datos SQL en su nivel de rendimiento y de nivel de servicio específico. Los datos se recopilan cada 5 minutos y se mantienen aproximadamente 35 días. Esta vista es útil para un análisis histórico a largo plazo de cómo utiliza los recursos en la base de datos SQL.

El siguiente gráfico muestra el recurso uso de CPU para una base de datos Premium con el nivel de rendimiento P2 para cada hora de una semana. Este gráfico comienza en lunes, muestra 5 días de trabajo y, a continuación, muestra un fin de semana, cuando pasa mucho menos en la aplicación.

![Uso de recursos de base de datos SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

De los datos, esta base de datos tiene una carga de CPU máxima de más de 50 por ciento uso de CPU en relación con el nivel de rendimiento de P2 (mediodía martes). Si la CPU es el factor principal en el perfil de recursos de la aplicación, puede decidir que P2 es el nivel de rendimiento derecha para garantizar que siempre ajusta la carga de trabajo. Si espera una aplicación para aumentar el tiempo, es aconsejable tener un búfer de recursos adicionales para que la aplicación nunca no alcanza el límite de nivel de rendimiento. Si aumentar el nivel de rendimiento, puede ayudar a evitar errores de cliente visibles que pueden surgir cuando una base de datos no tiene suficiente potencia para procesar las solicitudes de hecho, especialmente en entornos con latencia. Un ejemplo es una base de datos compatible con una aplicación que dibuja basándose en los resultados de las llamadas de base de datos de las páginas Web.

Tenga en cuenta que otros tipos de aplicaciones pueden interpretar el mismo gráfico diferente. Por ejemplo, si una aplicación intenta procesar datos de nóminas cada día y tiene el mismo gráfico, este tipo de modelo de "proceso" puede hacer bien en un nivel de rendimiento de P1. El nivel de rendimiento de P1 tiene 100 DTUs en comparación con 200 DTUs en el nivel de rendimiento P2. El nivel de rendimiento P1 proporciona la mitad el rendimiento del nivel de rendimiento P2. Por lo tanto, 50 por ciento de uso de CPU en P2 es igual a 100 por cien de uso de CPU en P1. Si la aplicación no tiene tiempo de espera, podría no importa si una tarea tarda 2 ó 2.5 horas para finalizar, si se realiza de hoy. Una aplicación de esta categoría probablemente puede utilizar un nivel de rendimiento de P1. Puede aprovechar el hecho de que hay períodos de tiempo durante el día cuando es menor, uso de recursos de modo que cualquier "máximo grande" podría sobrepase en uno de los mínimos más adelante en el día. El nivel de rendimiento P1 podría ser conveniente para ese tipo de aplicación (y ahorro de dinero), como los trabajos pueden finalizar el tiempo cada día.

Expone de base de datos de SQL Azure habían consumida información del recurso para cada base de datos activa en la vista de **sys.resource_stats** de **la base de datos en cada servidor** . Los datos de la tabla se agregan a intervalos de 5 minutos. Con los niveles de servicio básico, estándar y Premium, los datos pueden tardar más de cinco minutos aparezca en la tabla, por lo que estos datos están más útiles para análisis histórico en lugar de análisis de casi en tiempo real. Consultar la vista de **sys.resource_stats** para ver el historial de una base de datos reciente y para validar la reserva elegido entreguen el rendimiento que cuando sea necesario.

>[AZURE.NOTE] Debe estar conectado a **la base de datos de su servidor de base de datos SQL lógico para consultar **sys.resource_stats** en los ejemplos siguientes** .

Este ejemplo muestra cómo se expongan los datos en esta vista:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vista de catálogo sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

En el ejemplo siguiente muestra diferentes maneras que puede usar la vista de catálogo **sys.resource_stats** para obtener información acerca de cómo utiliza los recursos en la base de datos SQL:

1. Para ver los recursos de la semana pasada utilizar para userdb1 de base de datos, puede ejecutar esta consulta:

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. Para evaluar cómo la carga de trabajo ajusta el nivel de rendimiento, debe profundizar en cada aspecto de las mediciones de recursos: CPU, lee, escribe, número de trabajadores y número de sesiones. Esta es una consulta revisada con **sys.resource_stats** para informar de los valores máximos y promedio de estas métricas de recursos:

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Con esta información acerca de los valores de promedio y máximos de cada métrica de recursos, puede evaluar cómo se ajusta la carga de trabajo en el nivel de rendimiento elegido. Normalmente, los valores promedio de **sys.resource_stats** dar una buena base para usar con el tamaño de destino. Debe ser el palo de medición primaria. Por ejemplo, puede que esté utilizando el nivel de servicio estándar con el nivel de rendimiento de S2. El promedio usar porcentajes de CPU e i/OS lee y escribe está por debajo de 40 por ciento, el promedio de los trabajadores está por debajo de 50 y el promedio de las sesiones está por debajo de 200. Puede ajustar la carga de trabajo en el nivel de rendimiento S1. Es más fácil ver si la base de datos se ajusta en los límites de trabajo y sesión. Para ver si se ajusta una base de datos en un nivel de rendimiento inferior con respecto a la CPU, lee y escribe, divida el número DTU del nivel de rendimiento inferior por el número DTU de su nivel de rendimiento actual y, a continuación, multiplique el resultado por 100:

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 **

    El resultado es la diferencia de rendimiento relativo entre los niveles de dos rendimiento en porcentaje. Si el uso de recursos no exceda este importe, puede ajustar la carga de trabajo en el nivel de rendimiento inferior. Sin embargo, debe buscar en todos los rangos de valores de uso de recursos y determinar, por porcentaje, ¿con qué frecuencia debería ajustar la carga de trabajo de la base de datos en el nivel de rendimiento inferior. La siguiente consulta genera el porcentaje de ajuste por dimensión de recursos, según el umbral de 40 por ciento que se calcula en este ejemplo:

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    En función de su objetivo de nivel de servicio (SLO) de base de datos, puede decidir si se ajusta la carga de trabajo en el nivel de rendimiento inferior. Si la carga de trabajo de base de datos SLO es 99,9 por ciento y la consulta anterior devuelve valores mayores 99,9 por ciento para todas las dimensiones de tres recursos, la carga de trabajo es muy probable que caben en el nivel de rendimiento inferior.

    Mirar el porcentaje de ajuste también proporciona información sobre si tiene para desplazarse al siguiente nivel de rendimiento superior para satisfacer su SLO. Por ejemplo, userdb1 muestra el uso de CPU siguiente de la semana anterior:

  	| Porcentaje de CPU promedio | Porcentaje de CPU máximo |
  	|---|---|
  	| 24,5 | 100,00 |

    Media de la CPU es acerca de un trimestre del límite del nivel de rendimiento, que se adapta bien el nivel de rendimiento de la base de datos. Sin embargo, se muestra el valor máximo que la base de datos alcanza el límite del nivel de rendimiento. ¿Necesita mover al siguiente nivel superior de rendimiento? Debe ver cómo muchas veces su alcance de carga de trabajo 100 por cien y compárelo con la carga de trabajo de base de datos SLO.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Si esta consulta devuelve un valor menor que el 99,9 por ciento por cualquiera de las dimensiones de tres recursos, considere alguno mover al siguiente nivel de rendimiento superior o utilizar técnicas de optimización de aplicaciones para reducir la carga de la base de datos SQL.

4. Este ejercicio también tiene en cuenta el aumento de carga de trabajo proyectado en el futuro.

## <a name="tune-your-application"></a>Ajustar la aplicación

En local tradicional de SQL Server, con qué frecuencia se separa el proceso de planificación de capacidad inicial del proceso de ejecución de una aplicación de producción. Licencias de hardware y producto se adquieren en primer lugar y ajustar el rendimiento se realiza más adelante. Al usar la base de datos de SQL Azure, es una buena idea entrelazar el proceso de ejecución de una aplicación y ajuste. Con el modelo de pagar capacidad a petición, puede ajustar la aplicación para utilizar los recursos mínimos necesarios ahora, en lugar de para otros fines en hardware basado en sugerencias de planes de crecimiento futuro para una aplicación, que a menudo son incorrectos. Algunos clientes pueden optar por no ajustar una aplicación y elija en su lugar provisioning de recursos de hardware en. Este enfoque podría ser una buena idea si no desea cambiar una aplicación clave durante un período no disponible. Pero ajustar una aplicación puede minimizar los requisitos de recursos y disminuir sus facturas mensuales al usar los niveles de servicio en la base de datos de SQL Azure.

### <a name="application-characteristics"></a>Características de la aplicación

Aunque los niveles de servicio de base de datos de SQL Azure están diseñados para mejorar el rendimiento estabilidad y la predicción de una aplicación, algunas prácticas recomendadas pueden ayudarle a ajustar la aplicación mejor aprovechar las ventajas de los recursos en un nivel de rendimiento. Aunque muchas aplicaciones tienen rendimiento significativa simplemente cambiando a un nivel de rendimiento superior o algunas aplicaciones de nivel de servicio, necesitan ajustes adicionales para beneficiarse de un nivel de servicio. Para mejorar el rendimiento, considere la optimización de las aplicaciones adicionales para las aplicaciones que tienen las siguientes características:

- **Aplicaciones que tienen un rendimiento lento debido a comportamiento "conversadoras"**. Aplicaciones conversadoras realizar operaciones de acceso a datos excesivo que dependen de latencia de red. Debe modificar estos tipos de aplicaciones para reducir el número de operaciones de acceso de datos a la base de datos SQL. Por ejemplo, puede mejorar el rendimiento de la aplicación mediante técnicas como lotes consultas ad-hoc o mover las consultas a procedimientos almacenados. Para obtener más información, consulte [consultas por lotes](#batch-queries).
- **Bases de datos con una carga de trabajo intensivo que no sea compatible con toda una sola máquina**. Bases de datos que superan los recursos del nivel de rendimiento superior Premium pueden beneficiarse de escalado de la carga de trabajo. Para obtener más información, consulte [sharding entre bases de datos](#cross-database-sharding) y [particiones funcionales](#functional-partitioning).
- **Aplicaciones que disponen de consultas más apropiadas**. Aplicaciones, especialmente los de la capa de acceso a datos, que se adaptan mal consultas no pueden beneficiarse de un nivel de rendimiento superior. Esto incluye consultas que falta una cláusula WHERE, tienen que faltan índices o esté obsoleta estadísticas. Estas aplicaciones se benefician técnicas de optimización del rendimiento de consulta estándar. Para obtener más información, vea [índices de falta](#missing-indexes) y [ajuste de consulta y sugerencias](#query-tuning-and-hinting).
- **Diseño de acceso a aplicaciones que tienen datos más apropiados**. Las aplicaciones que tienen problemas de simultaneidad de acceso de datos inherente, por ejemplo interbloqueos, podrían no beneficiarse de un nivel superior de rendimiento. Considere la posibilidad de reducción de ida y vuelta con la base de datos de SQL Azure en los datos en caché en el cliente con el servicio de almacenamiento en caché Azure u otra tecnología de almacenamiento en caché. Vea [almacenamiento en caché de nivel de aplicación](#application-tier-caching).

## <a name="tuning-techniques"></a>Técnicas de ajuste
En esta sección, veremos algunas técnicas que puede usar para ajustar la base de datos de Azure SQL para obtener el mejor rendimiento de la aplicación y ejecutar en el nivel de rendimiento posible inferior. Algunas de estas técnicas que coincida con las prácticas recomendadas de ajuste tradicional de SQL Server, pero otras son específicas de la base de datos de SQL Azure. En algunos casos, puede examinar los recursos utilizados para una base de datos buscar áreas para ajustar y ampliar las técnicas tradicionales de SQL Server para que funcione en la base de datos de SQL Azure.

### <a name="azure-portal-tools"></a>Herramientas de portal de Azure
Encontrará dos herramientas en el portal de Azure que pueden ayudar a analizar y solución problemas de rendimiento con la base de datos SQL:

- [Información de rendimiento de consulta](sql-database-query-performance.md)
- [Asesor de base de datos SQL](sql-database-advisor.md)

El portal de Azure tiene más información acerca de estas herramientas y cómo usarlas. Para eficazmente diagnosticar y corregir problemas, le recomendamos que primero pruebe las herramientas en el portal de Azure. Le recomendamos que use el manual ajuste enfoques que trataremos a continuación, si faltan índices y ajuste de consultas, en casos especiales.

### <a name="missing-indexes"></a>Índices perdidos
Un problema común en el rendimiento de la base de datos OLTP está relacionado con el diseño de base de datos física. A menudo, esquemas de base de datos están diseñados y envían sin pruebas a escala (ya sea en la carga o volumen de datos). Desgraciadamente, el rendimiento de un plan de consulta puede ser aceptable en una escala pequeña pero degradar considerablemente en volúmenes de datos de nivel de producción. El origen más común de este problema es la falta de índices adecuados para cumplir con los filtros u otras restricciones en una consulta. A menudo, falta de índices de manifiestos, como una tabla digitalizar cuando una búsqueda de índice podría ser suficiente.

En este ejemplo, el plan de la consulta seleccionada utiliza un análisis al buscar un sería suficiente:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Un plan de consulta con índices perdidos](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Base de datos de SQL Azure puede ayudarle a buscar y corregir comunes falta condiciones de índice. DMV integradas en la base de datos de SQL Azure mire compilaciones de consulta en la que un índice reduciría significativamente el costo estimado para ejecutar una consulta. Durante la ejecución de consulta, base de datos SQL realiza un seguimiento de la frecuencia con cada plan de consulta se ejecuta y realiza un seguimiento de la separación estimada entre el plan de consulta está ejecutando y el imaginario donde existía ese índice. Puede usar estas DMV adivinar rápidamente los cambios en el diseño de la base de datos física pueden mejorar el costo total de carga de trabajo para una base de datos y su carga de trabajo real.

Puede usar esta consulta para evaluar posibles índices perdidos:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

En este ejemplo, la consulta ha generado esta sugerencia:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Después de crearlo, esa misma instrucción SELECT selecciones un plan diferente, que utiliza una búsqueda en lugar de un análisis y, a continuación, ejecuta el plan de forma más eficaz:

![Un plan de consulta corregido índices](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

El concepto central es la capacidad de i/OS de un sistema de mercancías compartido, más limitada a la de un equipo servidor dedicado. Hay un premium en minimizar innecesarios i/OS para sacar el máximo partido del sistema en el DTU de cada nivel de rendimiento de los niveles de servicio de base de datos de SQL Azure. Diseño de base de datos física adecuada opciones pueden mejorar significativamente la latencia de consultas individuales, mejorar el rendimiento de solicitudes simultáneas administradas por unidad de escala y minimizar los gastos necesarios para cumplir con la consulta. Para obtener más información acerca del índice faltando DMV, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Sugerencias y ajuste de consultas
El optimizador de consultas de base de datos de SQL Azure es similar al optimizador de consultas de SQL Server tradicional. La mayoría de los procedimientos recomendados para optimizar consultas y comprender el razonamiento limitaciones de modelo para el optimizador de consultas también se aplican a la base de datos de SQL Azure. Si bien ajustar las consultas de base de datos de SQL Azure, puede que aparezca el beneficio adicional de reducir las peticiones de recursos agregado. La aplicación posible que pueda ejecutar en un costo menor que equivalente no sintonizado porque se pueden ejecutar en un nivel de rendimiento inferior.

Un ejemplo que es común en SQL Server y que también se aplica a la base de datos de SQL Azure es cómo el optimizador "descubre" parámetros. Durante la compilación, el optimizador evalúa el valor actual de un parámetro para determinar si puede generar un plan de consulta más óptimo. Aunque esta estrategia a menudo puede llevar a un plan de consulta que es mucho más rápido que un plan compilado sin valores de parámetro conocidos, actualmente funciona no entienden bien ambos en SQL Server y en la base de datos de SQL Azure. A veces el parámetro no se descubren y a veces se descubren el parámetro pero el plan generado es más apropiado para el conjunto completo de valores de parámetro en una carga de trabajo. Microsoft incluye sugerencias de consulta (directivas) para que pueda especificar finalidad más deliberadamente y reemplazar el comportamiento predeterminado de descubrimiento de parámetros. A menudo, si utiliza sugerencias, puede solucionar los casos en que el comportamiento predeterminado de SQL Server o base de datos de SQL Azure es deficiente para una carga de trabajo de cliente específico.

En el ejemplo siguiente se muestra cómo el procesador de consultas puede generar un plan de más apropiado para rendimiento y requisitos de recursos. Este ejemplo muestra también que, si usa una sugerencia de consulta, puede reducir los requisitos de recursos y tiempo de ejecución de consultas para la base de datos SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

El código de configuración crea una tabla que ha sesgada distribución de datos. El plan de consulta óptimo varía según el parámetro que está seleccionado. Desgraciadamente, el plan de almacenamiento en caché comportamiento no compilar siempre la consulta según el valor del parámetro más comunes. Por lo tanto, es posible que un plan más apropiado en caché y utilizar para muchos valores, incluso cuando un plan diferente podría ser mejor opción plan Media. A continuación, en el plan de consulta se crea dos procedimientos almacenados que son idénticos, salvo que uno tiene una sugerencia de consulta especial.

**Ejemplo, parte 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Ejemplo, parte 2**

(Se recomienda que espere al menos 10 minutos antes de comenzar la parte 2 del ejemplo, para que los resultados son distintos de los datos de telemetría resultante).

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Cada parte de este ejemplo intenta ejecutar una instrucción insert parametrizadas 1.000 veces (para generar una carga suficiente para usarlo como un conjunto de datos de prueba). Cuando se ejecuta procedimientos almacenados, el procesador de consultas examina el valor del parámetro que se pasa al procedimiento durante la primera compilación ("descubrimiento de parámetros"). El procesador se almacena en caché el plan resultante y usa para las llamadas a una versión posterior, incluso si el valor del parámetro es diferente. El plan óptimo no puede utilizarse en todos los casos. A veces necesite guiar el optimizador para elegir un plan que es mejor para los casos promedio, en lugar de la caja específica de cuando se compila en primer lugar la consulta. En este ejemplo, el plan inicial genera un plan de "análisis" que lea todas las filas para buscar cada valor que coincida con el parámetro:

![Optimización mediante un plan de análisis de la consulta](./media/sql-database-performance-guidance/query_tuning_1.png)

Dado que se ejecuta el procedimiento mediante el valor 1, el plan resultante fue óptimo para el valor 1, pero sí más apropiado para todos los demás valores de la tabla. El resultado es muy probable que no es lo que quiera que si estuviera elegir cada plan aleatoriamente, porque el plan tiene un rendimiento más lento y utiliza más recursos.

Si ejecuta la prueba con `SET STATISTICS IO` establecido en `ON`, el trabajo de recorrido lógico en este ejemplo se realiza en segundo plano. Puede ver que hay 1.148 lecturas realizadas por el plan (que es ineficaz, si es el caso de promedio para devolver una sola fila):

![Ajustar usando un análisis lógico de consulta](./media/sql-database-performance-guidance/query_tuning_2.png)

La segunda parte del ejemplo usa una sugerencia de consulta para indicar el optimizador de utilizar un valor específico durante el proceso de compilación. En este caso, fuerza el procesador de consultas para omitir el valor que se pasa como parámetro, y en su lugar asumir `UNKNOWN`. Esta opción hace referencia a un valor que tiene la frecuencia de Media de la tabla (omitiendo el sesgo). El plan resultante es un plan de búsqueda que es más rápido y usa menos recursos, Media, que el plan en la parte 1 de este ejemplo:

![Ajuste de las consultas usando una sugerencia de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Puede ver el efecto en la tabla **sys.resource_stats** (hay un retardo desde el momento en que se ejecutan la prueba y cuando la tabla rellenan los datos). Para este ejemplo, parte 1 ejecutadas durante el período de tiempo 22:25:00 y parte 2 ejecutado en 22:35:00. Observe que la ventana de tiempo anterior utiliza más recursos en dicha ventana de tiempo que la posterior (debido a las mejoras de eficiencia de plan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Optimización de ejemplo de resultados de la consulta](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Aunque el volumen en este ejemplo es deliberadamente pequeño, el efecto de los parámetros más apropiados puede ser importante, especialmente en bases de datos mayores. La diferencia en casos extremos, puede ser entre segundos en caso de rápida y las horas en caso de lenta.

Puede examinar **sys.resource_stats** para determinar si el recurso para una prueba utiliza más o menos recursos que otra prueba. Cuando se comparan datos, separar los intervalos de las pruebas de modo que no se encuentran en la misma ventana de 5 minutos en la vista **sys.resource_stats** . El objetivo del ejercicio es para minimizar la cantidad total de los recursos utilizados y no para minimizar los recursos de recursos asignadas. En general, también optimizar un fragmento de código de latencia reduce el consumo de recursos. Asegúrese de que los cambios que realice a una aplicación son necesarios y que los cambios no afectan de la experiencia del cliente para alguien que pueda estar usando sugerencias de consulta en la aplicación.

Si una carga de trabajo tiene un conjunto de consultas de repetición, a menudo tiene sentido capturar y validar el estado óptimo de las opciones del plan porque unidades de la unidad de tamaño de recursos mínimos necesaria para hospedar la base de datos. Después de validarla, en ocasiones examinar los planes para ayudarle a asegurarse de que no haya degradado. Puede obtener más información acerca de las [sugerencias de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Sharding entre bases de datos
Como base de datos de SQL Azure se ejecuta en hardware básico, los límites de capacidad para una base de datos son menores que para una instalación de SQL Server local tradicional. Algunos clientes utilizan técnicas de sharding distribuyan operaciones de base de datos en varias bases de datos cuando no se ajustan a las operaciones dentro de los límites de una base de datos en la base de datos de SQL Azure. La mayoría de los clientes que utilizan técnicas de sharding de base de datos de SQL Azure dividen sus datos en una única dimensión múltiples bases de datos. Para este método, necesitará saber que las aplicaciones de OLTP a menudo realizan transacciones que se aplican a solo una fila o un pequeño grupo de filas en el esquema.

>[AZURE.NOTE] Base de datos SQL ahora proporciona una biblioteca para ayudar a sharding. Para obtener más información, vea [información general sobre la biblioteca de cliente elásticos de la base de datos](sql-database-elastic-database-client-library.md).

Por ejemplo, si una base de datos tiene el nombre del cliente, orden y detalles de pedido (por ejemplo, la base de datos de Northwind de ejemplo tradicional que se incluye con SQL Server), puede dividir estos datos en varias bases de datos mediante la agrupación de un cliente con el orden relacionado y la información detallada del pedido. Puede garantizar que los datos del cliente permanecen en una base de datos. La aplicación debería dividir a diferentes clientes en bases de datos eficaz reparte la carga en varias bases de datos. Con sharding, los clientes no solo pueden evitar el límite de tamaño máximo de la base de datos, pero también base de datos de SQL Azure puede procesar cargas de trabajo que son mucho mayores que los límites de los niveles de rendimiento diferentes, como cada base de datos se ajusta a su DTU.

Aunque sharding de base de datos no reduce la capacidad de recursos agregados para una solución, es muy eficaces en soporte de las soluciones muy grandes y que se extienden a varias bases de datos. Cada base de datos se puede ejecutar en un nivel de rendimiento diferentes para admitir muy grande, "efectiva" bases de datos con los requisitos de recursos de alto.

### <a name="functional-partitioning"></a>Partición funcional
Los usuarios de SQL Server suelen combinan muchas funciones en una base de datos. Por ejemplo, si una aplicación tiene lógica para administrar el inventario de una tienda, dicha base de datos podría tener lógica asociada con seguimiento de inventario, órdenes de compra, procedimientos almacenados y vistas indizadas o materializadas que administración informes de fin de mes. Esta técnica hace que sea más fácil administrar la base de datos para operaciones como copia de seguridad, pero también requiere el hardware para controlar la carga de recursos asignadas en todas las funciones de una aplicación de cambio de tamaño.

Si usa una arquitectura de escalado de base de datos de SQL Azure, es una buena idea dividir funciones diferentes de una aplicación a diferentes bases de datos. Mediante esta técnica, cada aplicación escala de forma independiente. Como una aplicación está demasiado ocupada (y aumenta la carga de la base de datos), el administrador puede elegir los niveles de rendimiento independientes para cada función en la aplicación. En el límite, con esta arquitectura, una aplicación puede ser mayor de lo que puede controlar una máquina mercancías solo porque la carga se distribuye en varios equipos.

### <a name="batch-queries"></a>Consultas por lotes
Para las aplicaciones que tienen acceso a datos mediante el uso de gran volumen, frecuentes, consultas ad hoc, mucho tiempo de respuesta está dedicado a la comunicación de red entre el nivel de aplicación y la base de datos de SQL Azure. Incluso cuando la aplicación y la base de datos de SQL Azure en el mismo centro de datos, la latencia de red entre los dos puede ser amplía la opción por una gran cantidad de datos operaciones de access. Para reducir la red redondear viajes para las operaciones de acceso a datos, piense en usar la opción ya sea por lotes las consultas ad hoc o para compilar su forma de procedimientos almacenados. Si el lote de las consultas ad hoc puede enviar varias consultas como un lote de gran tamaño en un solo viaje a la base de datos de SQL Azure. Si compilar consultas ad hoc en un procedimiento almacenado, podría conseguir el mismo resultado, como si un lote. Mediante un procedimiento almacenado también le ofrece la ventaja de aumenta las posibilidades de almacenamiento en caché los planes de consulta de base de datos de SQL Azure, por lo que puede utilizar el procedimiento almacenado de nuevo.

Algunas aplicaciones son escritura intensiva. A veces puede reducir la carga de i/OS total en una base de datos partiendo de cómo procesar por lotes escribe juntas. A menudo, esto es tan sencillo como el uso de transacciones explícitas en lugar de las transacciones de confirmación automática en procedimientos almacenados y lotes ad hoc. Para una evaluación de las distintas técnicas que puede usar, consulte [técnicas de procesamiento por lotes para aplicaciones de base de datos de SQL Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentar con su propia carga de trabajo para buscar el modelo correcto de procesamiento por lotes. Asegúrese de saber que un modelo puede tener la garantía de coherencia de transacciones ligeramente diferente. Buscar la carga de trabajo derecho que minimice el uso de recursos requiere encontrar la combinación adecuada de coherencia y rendimiento ventajas y desventajas.

### <a name="application-tier-caching"></a>Nivel de aplicación de almacenamiento en caché
Algunas aplicaciones de base de datos tienen cargas de trabajo de lectura visible. Almacenamiento en caché capas puede reducir la carga de la base de datos y puede reducir el nivel de rendimiento necesario para admitir una base de datos mediante el uso de la base de datos de SQL Azure. Con [Azure Redis caché](https://azure.microsoft.com/services/cache/), si tiene una carga de trabajo visible de lectura, puede leer los datos una vez (o tal vez una vez por el equipo de nivel de aplicación, dependiendo de cómo se configura) y, a continuación, almacenar datos fuera de la base de datos SQL. Esta es una manera de reducir la carga de la base de datos (CPU e i/OS de lectura), pero hay un efecto de la coherencia de transacciones porque la lectura de la memoria caché de datos podrían ser sincronizados con los datos de la base de datos. Aunque en muchas aplicaciones algún nivel de incoherencia es aceptable, que no es true para todas las cargas de trabajo. Debe comprender los requisitos de la aplicación antes de implementar una estrategia de almacenamiento en caché de nivel de aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los niveles de servicio, consulte [rendimiento y opciones de la base de datos SQL](sql-database-service-tiers.md)
- Para obtener más información acerca de los grupos de elásticos de la base de datos, vea [¿Qué es un grupo de base de datos elástico Azure?](sql-database-elastic-pool.md)
- Para obtener información sobre el rendimiento y agrupaciones elásticos de la base de datos, vea [Cuándo considerar un grupo elásticos de la base de datos](sql-database-elastic-pool-guidance.md)
