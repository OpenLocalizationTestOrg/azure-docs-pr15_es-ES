<properties
    pageTitle="Supervisar el almacenamiento en memoria de XTP | Microsoft Azure"
    description="Estimación y monitor de almacenamiento en memoria XTP usan, capacidad; resolver el error de capacidad 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Almacenamiento en memoria OLTP de Monitor

Cuando se usa [En memoria OLTP](sql-database-in-memory.md), datos de tablas optimizado de memoria y variables de tabla residen en almacenamiento en memoria OLTP. Cada nivel de servicio Premium tiene un tamaño de almacenamiento en memoria OLTP máximo, que se describe en el [artículo de niveles de servicio de base de datos de SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Una vez que se supera este límite, insertar y actualizar las operaciones pueden iniciar (con el error 41823). En ese momento se necesita para eliminar los datos para reclamar memoria, o actualice el nivel de rendimiento de la base de datos.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinar si los datos se ajustan dentro del límite de almacenamiento en memoria

Determinar el límite de almacenamiento: consulte el [artículo de niveles de servicio de base de datos de SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) de los límites de almacenamiento de los diferentes niveles de servicio Premium.

Calcular los requisitos de la memoria para un works tabla optimizado de memoria del mismo modo para SQL Server tal y como se hace en base de datos de SQL Azure. Pasar unos minutos para revisar ese tema en [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tenga en cuenta que la tabla y de las filas de tabla variables, así como índices, contarán hacia el tamaño de datos de usuario max. Además, ALTER TABLE tiene suficiente espacio para crear una nueva versión de toda la tabla y sus índices.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas

Puede supervisar el uso de almacenamiento en memoria como un porcentaje de la [tapa de almacenamiento para el nivel de rendimiento](sql-database-service-tiers.md#service-tiers-for-single-databases) en el [portal](https://portal.azure.com/)de Azure: 

- En el módulo de la base de datos, busque el cuadro de uso de recursos y haga clic en Editar.
- A continuación, seleccione la métrica `In-Memory OLTP Storage percentage`.
- Para agregar una alerta, haga clic en el cuadro de uso de recursos para abrir el módulo métrico y luego haga clic en Agregar aviso.

O bien, use la siguiente consulta para mostrar el uso de almacenamiento en memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corregir memoria agotada situaciones - Error 41823

Ejecución de memoria agotada en Insertar, actualizar y crear operaciones con el mensaje de error 41823.

Mensaje de error 41823 indica que las variables de tabla y tablas optimizar memoria han superado el tamaño máximo.

Para resolver este error, o bien:


- Eliminar datos de tablas optimizado de memoria, potencialmente descargar los datos de tablas tradicionales basada en disco; o bien,
- Actualizar el nivel de servicio a otra con suficiente almacenamiento en memoria para los datos que necesite para mantener en tablas optimizadas de memoria.

## <a name="next-steps"></a>Pasos siguientes
Recursos adicionales sobre el [uso de base de datos de SQL de Azure supervisión vistas dinámicas de administración](sql-database-monitoring-with-dmvs.md)
