<properties
   pageTitle="Solución de problemas de almacén de datos SQL Azure | Microsoft Azure"
   description="Solución de problemas de almacén de datos SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solución de problemas de almacén de datos SQL Azure

En este tema se enumeran algunas de las preguntas más frecuentes de solución de problemas que se oír de nuestros clientes.

## <a name="connecting"></a>Conectarse

| Problema                              | Resolución                                      |
| :----------------------------------| :---------------------------------------------- |
| Error de inicio de sesión de usuario de 'Inicio de sesión anónimo/autoridad de NT'. (Microsoft SQL Server, Error: 18456) | Este error se produce cuando un usuario AAD intenta conectarse a la base de datos, pero no tiene un usuario de patrón.  Para corregir este problema, especifique el almacén de datos de SQL desea conectarse en el momento de la conexión o agregar al usuario a la base de datos.  Vea el artículo [Introducción a la seguridad][] para obtener más detalles.|
|El servidor principal "MyUserName" no está teniendo acceso a la base de datos "principal" en el contexto actual de seguridad. No puede abrir la base de datos de usuario predeterminada. Error de inicio de sesión. Error de inicio de sesión para usuario 'MyUserName'. (Microsoft SQL Server, Error: 916) | Este error se produce cuando un usuario AAD intenta conectarse a la base de datos, pero no tiene un usuario de patrón.  Para corregir este problema, especifique el almacén de datos de SQL desea conectarse en el momento de la conexión o agregar al usuario a la base de datos.  Vea el artículo [Introducción a la seguridad][] para obtener más detalles.|
| Error CTAIP                        | Este error puede producirse cuando se ha creado un inicio de sesión en la base de datos SQL server principal, pero no en la base de datos de almacén de datos de SQL.  Si aparece este error, vea el artículo [Introducción a la seguridad][] .  En este artículo se explica cómo crear crear un usuario y el inicio de sesión en el patrón y, a continuación, cómo crear un usuario en la base de datos de almacén de datos de SQL.|
| Bloqueado por el Firewall                |Bases de datos SQL Azure están protegidos por el servidor y base de datos de los firewalls de nivel para asegurarse de solo conocidos direcciones IP para tener acceso a una base de datos. Los servidores de seguridad son seguras de forma predeterminada, lo que significa que debe habilitar explícitamente y dirección IP o intervalo de direcciones para poder conectarse.  Para configurar el firewall para access, siga los pasos en [Configurar el acceso de firewall de servidor para su dirección IP de cliente][] en las [instrucciones de aprovisionamiento][].|
| No se puede conectar con la herramienta o controlador | Almacén de datos de SQL, se recomienda usar [SSMS][], [SSDT de Visual Studio de 2015][]o [sqlcmd][] para consultar los datos. Para obtener más detalles sobre los controladores y conectarse al almacén de datos de SQL, vea los artículos de [controladores de almacén de datos de SQL Azure][] y [Conectar con el almacén de datos de SQL Azure][] .|


## <a name="tools"></a>Herramientas

| Problema                              | Resolución                                      |
| :----------------------------------| :---------------------------------------------- |
| Explorador de objetos de Visual Studio faltan los usuarios AAD | Se trata de un problema conocido.  Como solución alternativa, ver los usuarios de [sys.database_principals][].  Consulte [autenticación al almacén de datos de SQL Azure][] para más información sobre el uso de Azure Active Directory con el almacén de datos de SQL.|

## <a name="performance"></a>Rendimiento

|  Problema                             | Resolución                                      |
| :----------------------------------| :---------------------------------------------- |
| Solución de problemas de rendimiento de la consulta  | Si está intentando solucionar problemas de una consulta determinada, comenzar a [aprender a supervisar las consultas][].|
| Planes y rendimiento de la consulta deficiente a menudo es el resultado de las estadísticas que faltan   | La causa más común de un mal rendimiento es la falta de estadísticas de las tablas.  Ver las [estadísticas de la tabla de mantenimiento] [ Statistics] para obtener más información sobre cómo crear estadísticas y por qué son fundamentales para su desempeño.|
| Simultaneidad baja / consultas en cola   | Descripción de la [administración de carga de trabajo][] es importante para comprender cómo equilibrar la asignación de memoria simultaneidad.|
| Cómo implementar los procedimientos recomendados    | El mejor lugar para empezar a aprender formas de mejorar el rendimiento de la consulta es el artículo de [prácticas recomendadas de almacén de datos de SQL][] .|
| Cómo mejorar el rendimiento con ajuste de escala  | A veces la solución para mejorar el rendimiento es simplemente agregar que más potencia a las consultas ajustando [El almacén de datos de SQL][].|
| Rendimiento de las consultas de una mala como resultado de calidad deficiente índice | Algunas veces consultas pueden retraso debido a [columnstore una mala calidad de índice][].  Consulte este artículo para obtener más información y cómo volver a [generar índices para mejorar la calidad de segmento][].|

## <a name="system-management"></a>Administración del sistema

|  Problema                             | Resolución                                      |
| :----------------------------------| :---------------------------------------------- |
| Msj 40847: No se pudo realizar la operación porque el servidor ' d supere la cuota de unidad de la transacción de base de datos permitida de 45000. | Reducir el [DWU][] de la base de datos que está intentando crear o [Solicitar un aumento de cuota][].|
| Investigar utilización del espacio    | Ver [tamaño de tabla][] para comprender la utilización del espacio de su sistema.|
| Ayuda con la administración de tablas          | Vea la [información general de la tabla] [ Overview] artículo para obtener ayuda con la administración de las tablas.  En este artículo también incluye vínculos a temas más detallados como [tipos de datos de tabla][Data types], [distribuir una tabla][Distribute], [indización de una tabla][Index], [una tabla de partición][Partition], [estadísticas de la tabla de mantenimiento] [ Statistics] y [tablas temporales][Temporary].|

## <a name="polybase"></a>Polybase

|  Problema                             | Resolución                                      |
| :----------------------------------| :---------------------------------------------- |
| Error de UTF-8                        |  Actualmente PolyBase solo admite la carga de archivos de datos que se han codificada UTF-8.  Para obtener instrucciones sobre cómo evitar esta limitación, consulte [trabajar con el requisito de PolyBase UTF-8][] .|
| Se produce un error de carga debido a filas grandes   | Actualmente no está disponible para Polybase de soporte técnico de fila grande.  Esto significa que si la tabla contiene varchar (max), nvarchar (max) o varbinary (max), tablas externas no se pueden usar para cargar los datos.  Cargas de gran tamaño filas está actualmente solo se admiten a través del generador de datos de Azure (con BCP), el análisis de secuencia de Azure, SSIS, BCP o la clase SQLBulkCopy .NET. Compatibilidad con PolyBase filas grandes se agregará en una versión futura.|
| no puede cargar bcp de tabla con el tipo de datos MAX | Hay un problema conocido que requiere colocar varchar (max), nvarchar (max) o varbinary (max) al final de la tabla en algunos escenarios.  Pruebe a mover el número máximo de columnas al final de la tabla.|

## <a name="differences-from-sql-database"></a>Diferencias de base de datos SQL

|  Problema                             | Resolución                                      |
| :----------------------------------| :---------------------------------------------- |
| Características de base de datos SQL no admitidas  | Vea [características de tabla no compatibles][].|
| Tipos de datos de base de datos SQL no admitidos  | Vea [tipos de datos no admitidos][].|
| ELIMINAR y limitaciones de actualización      | Vea [alternativas de actualizar][], [Eliminar alternativas][] y [Usando CTAS para evitar la sintaxis de la actualización y eliminación no admitidas][].  |
| No se admite la instrucción de combinación   | Vea [Combinar alternativas][].|
| Limitaciones de procedimiento almacenado       | Consulte [limitaciones de procedimiento almacenado][] conocer algunas de las limitaciones de los procedimientos almacenados.|
| UDF no son compatibles con las instrucciones SELECT | Esta es una limitación de nuestras UDF actual.  Consulte [Crear función][] para la sintaxis de la que se admiten.   |

## <a name="next-steps"></a>Pasos siguientes

Si se encuentra fuera no se puede encontrar una solución a su problema anterior, aquí tiene algunos otros recursos que puede probar.

- [Blogs]
- [Solicitudes de características]
- [Vídeos]
- [Blogs del equipo de gato]
- [Crear incidencia de soporte técnico]
- [Foro de MSDN]
- [Foro de desbordamiento de pila]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Información general de seguridad]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT de Visual Studio de 2015]: ./sql-data-warehouse-install-visual-studio.md
[Controladores de almacén de datos SQL Azure]: ./sql-data-warehouse-connection-strings.md
[Conectarse al almacén de datos SQL Azure]: ./sql-data-warehouse-connect-overview.md
[Crear incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Escala el almacén de datos SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[solicitar un aumento de cuota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Obtener información sobre cómo supervisar las consultas]: ./sql-data-warehouse-manage-monitor.md
[Instrucciones de aprovisionamiento]: ./sql-data-warehouse-get-started-provision.md
[Configurar el acceso de firewall de servidor para su dirección IP de cliente]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md
[Tamaño de tabla]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Características de tabla no compatibles]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipos de datos no admitidos]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Columnstore una mala calidad de índice]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Volver a crear índices para mejorar la calidad del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Administración de carga de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[Usar CTAS para evitar la sintaxis de la actualización y eliminación no admitidas]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Alternativas de actualización]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ELIMINAR alternativas]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[COMBINAR alternativas]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitaciones de procedimiento almacenado]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticación al almacén de datos SQL Azure]: ./sql-data-warehouse-authentication.md
[Evitar el requisito de PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[Sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREAR FUNCIÓN]: https://msdn.microsoft.com/library/mt203952.aspx
[Sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de gato]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitudes de características]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Foro de desbordamiento de pila]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

