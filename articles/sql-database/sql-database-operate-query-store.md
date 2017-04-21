<properties
   pageTitle="Funcionamiento de la tienda de consulta de base de datos SQL Azure"
   description="Obtenga información sobre cómo trabajar con el almacén de consulta de base de datos de SQL Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Funcionamiento de la tienda de consulta de base de datos SQL Azure 

Almacén de consulta en Azure es una característica de base de datos totalmente administrada que continuamente recopila y presenta la información de historial detallada acerca de todas las consultas. Puede pensar almacén de consulta como una grabadora de datos de vuelo de un avión que significativamente simplifica el rendimiento de la consulta solución de problemas tanto para la nube y los clientes locales. En este artículo se explica aspectos específicos del funcionamiento de la tienda de consulta en Azure. Con estos datos recopilados previamente consulta, puede diagnosticar rápidamente y resolver problemas de rendimiento y, por tanto, perder tiempo más centrarse en su negocio. 

Almacén de consulta está [disponible globalmente](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) en la base de datos de SQL Azure desde noviembre de 2015. Almacén de consulta es la base para el análisis de rendimiento y optimización de características, como [Asesor de base de datos de SQL y panel de rendimiento](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). En el momento de la publicación de este artículo, el almacén de consulta se ejecuta en más de 200.000 bases de datos de usuario en Azure, recopilar información relacionada con la consulta de varios meses, sin interrupciones.

> [AZURE.IMPORTANT] Microsoft está en el proceso de activación de almacén de consulta para todas las bases de datos de SQL Azure (nuevos y existentes). 

## <a name="optimal-query-store-configuration"></a>Configuración del almacén de consulta óptimo

Esta sección describe los valores predeterminados de configuración óptima están diseñados para asegurar la operación confiable del almacén de consulta y características dependientes, como [Asesor de base de datos de SQL y panel de rendimiento](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configuración predeterminada está optimizada para una recopilación de datos, que es el mínimo tiempo invertido en Estados OFF/READ_ONLY.

| Configuración | Descripción | Predeterminado | Comentario |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Especifica el límite para el espacio de datos que puede tomar el almacén de consulta dentro de la base de datos de clientes de z | 100 | Aplicar para nuevas bases de datos |
| INTERVAL_LENGTH_MINUTES | Define el tamaño de la ventana de tiempo durante el cual se agregado y se almacenan las estadísticas recopiladas runtime planes de consulta. Cada plan de consulta activa tiene al menos una fila durante un período de tiempo definido con esta configuración | 60   | Aplicar para nuevas bases de datos |
| STALE_QUERY_THRESHOLD_DAYS | Directiva de limpieza basada en el tiempo que controla el período de retención de estadísticas de runtime persistentes y consultas inactivas | 30 | Aplicar para nuevas bases de datos y bases de datos con la configuración predeterminada anterior (367) |
| SIZE_BASED_CLEANUP_MODE | Especifica si el Liberador de espacio de datos automática tiene lugar cuando el límite aproxima a tamaño del almacén de consulta de datos | Automático | Aplica para todas las bases de datos |
| QUERY_CAPTURE_MODE | Especifica si se controlan todas las consultas o solo un subconjunto de consultas | Automático | Aplica para todas las bases de datos |
| FLUSH_INTERVAL_SECONDS | Especifica el plazo máximo durante el cual genera runtime estadísticas se guardan en la memoria, antes de vaciar en el disco | 900 | Aplicar para nuevas bases de datos |
||||||

> [AZURE.IMPORTANT] Estos valores predeterminados se aplican automáticamente en la fase final de la activación de la tienda de consulta en todas las bases de datos de SQL Azure (vea anterior Nota importante). Después de esta luz hacia arriba, base de datos de SQL Azure no cambiar los valores de configuración establecidos por los clientes, a menos que un impacto negativo en carga de trabajo principal u operaciones confiables de la tienda de consulta.

Si desea mantener la configuración personalizada, utilice [ALTER DATABASE con opciones de almacenamiento de la consulta](https://msdn.microsoft.com/library/bb522682.aspx) para revertir configuración al estado anterior. Consulte [Los procedimientos recomendados en la tienda de consulta](https://msdn.microsoft.com/library/mt604821.aspx) para aprender cómo principio eligió parámetros de configuración óptima.

## <a name="next-steps"></a>Pasos siguientes

[Información de rendimiento de base de datos SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionales

Para más información desproteger los siguientes artículos:

- [Una grabadora de datos de vuelo para la base de datos](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Supervisar el rendimiento mediante el uso de la tienda de consulta](https://msdn.microsoft.com/library/dn817826.aspx)

- [Escenarios de uso de la tienda de consulta](https://msdn.microsoft.com/library/mt614796.aspx)

- [Supervisar el rendimiento mediante el uso de la tienda de consulta](https://msdn.microsoft.com/library/dn817826.aspx) 
