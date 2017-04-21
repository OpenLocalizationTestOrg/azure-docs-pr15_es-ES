<properties
   pageTitle="No se admite en la base de datos SQL Azure T-SQL | Microsoft Azure"
   description="Instrucciones de Transact-SQL que son menores totalmente compatibles con la base de datos de SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Diferencias de Transact-SQL de base de datos de SQL Azure


La mayoría de las características de Transact-SQL que dependen de las aplicaciones es compatibles con Microsoft SQL Server y base de datos de SQL Azure. A continuación se muestra una lista parcial de las características compatibles para las aplicaciones:

- Tipos de datos.
- Operadores.
- Funciones de cadena, aritméticos, lógicos y cursor.

Sin embargo, la base de datos de SQL Azure está diseñado para aislar características de cualquier dependencia de **la base de datos** . En consecuencia muchas actividades de nivel de servidor no son apropiadas para la base de datos SQL y no son compatibles. Características que están obsoletos en SQL Server no son compatibles, en general, en la base de datos de SQL.

> [AZURE.NOTE]
> Este tema describen las características que están disponibles con la base de datos de SQL al actualizar a la versión actual; V12 de base de datos SQL. Para obtener más información sobre V12, consulte [del SQL base de datos V12 qué nuevo](sql-database-v12-whats-new.md).

Las siguientes secciones de la lista características que son parcialmente compatibles y las características que no son totalmente compatibles.


## <a name="features-partially-supported-in-sql-database-v12"></a>Características parcialmente compatibles con V12 de base de datos de SQL

V12 de base de datos de SQL es compatible con algunos pero no todos los argumentos que existen en las instrucciones SQL 2016 Transact-SQL Server correspondientes. Por ejemplo, la instrucción CREATE PROCEDURE está disponible a pesar de que todas las opciones de CREATE PROCEDURE no están disponibles. Consulte los temas de sintaxis vinculadas para obtener más información acerca de las áreas de cada instrucción compatibles.

- Bases de datos: [crear](https://msdn.microsoft.com/library/dn268335.aspx )/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV está generalmente disponibles para las características que están disponibles.
- Funciones: [crear](https://msdn.microsoft.com/library/ms186755.aspx)/[modificar función](https://msdn.microsoft.com/library/ms186967.aspx)
- [ELIMINAR](https://msdn.microsoft.com/library/ms173730.aspx) 
- Inicios de sesión: [crear](https://msdn.microsoft.com/library/ms189751.aspx)/[Modificar inicio de sesión](https://msdn.microsoft.com/library/ms189828.aspx)
- Procedimientos almacenados: [crear](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tablas: [crear](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipos (personalizado): [Crear un tipo](https://msdn.microsoft.com/library/ms175007.aspx)
- Usuarios: [crear](https://msdn.microsoft.com/library/ms173463.aspx)/[Modificar usuario](https://msdn.microsoft.com/library/ms176060.aspx)
- Vistas: [crear](https://msdn.microsoft.com/library/ms187956.aspx)/[Modificar vista](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Funciones no compatibles con la base de datos SQL

- Intercalación de objetos del sistema
- Conexión relacionada: instrucciones de extremo, ORIGINAL_DB_NAME. Base de datos SQL no admite la autenticación de Windows, pero es compatible con la autenticación de Azure Active Directory similar. Algunos tipos de autenticación requieren la última versión de SSMS. Para obtener más información, vea [Conectar con la base de datos SQL o datos almacén por usando Azure Active Directory autenticación de SQL](sql-database-aad-authentication.md).
- Combinación cruzada consultas de base de datos con tres o cuatro nombres de elemento. (Consultas entre bases de datos de solo lectura son compatibles con [consulta elásticos de la base de datos](sql-database-elastic-query-overview.md)).
- La propiedad de base de datos cruzado encadenar, configuración de confianza
- Selector de datos
- Diagramas de base de datos
- Correo de la base de datos
- DATABASEPROPERTY (use DATABASEPROPERTYEX en su lugar)
- Inicios de sesión de EXECUTE AS
- Cifrado: administración de claves extensible
- Eventos: eventos, las notificaciones de eventos, las notificaciones de consulta
- Características relacionadas con la ubicación del archivo de base de datos, el tamaño y los archivos de base de datos que se administran automáticamente por Microsoft Azure.
- Características relacionadas con la alta disponibilidad, que se administra a través de su cuenta de Microsoft Azure: copia de seguridad, restaurar, AlwaysOn, reflejo de base de datos, el registro de envío, modos de recuperación. Para obtener más información, consulte copia de seguridad SQL Azure y restaurar.
- Características que se basan en el lector del registro ejecutando en la base de datos SQL: replicación de inserción, cambiar la captura de datos.
- Características que se basan en el agente de SQL Server o la base de datos MSDB: trabajos, alertas y operadores, administración de la directiva, correo de la base de datos, servidores de administración central.
- FILESTREAM
- Funciones: fn_get_sql fn_virtualfilestats, fn_virtualservernodes
- Tablas temporales globales
- Configuración del servidor relacionados con el hardware: memoria, subprocesos de trabajo, la afinidad de CPU, marcas de seguimiento, etcetera. Utilizar niveles de servicio.
- HAS_DBACCESS
- ELIMINAR EL TRABAJO DE ESTADÍSTICAS
- Servidores vinculados, OPENQUERY, OPENROWSET, OPENDATASOURCE, INSERCIÓN masiva y nombres de cuatro partes
- Servidores principales o de destino
- .NET framework [integración de CLR con SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Controlador de recursos
- Búsqueda semántico
- Credenciales de servidor. Usar base de datos de ámbito credenciales en su lugar.
- Elementos de nivel de servidor: servidor roles, IS_SRVROLEMEMBER, sys.login_token. Permisos de nivel de servidor no están disponibles, aunque algunas se sustituyen por los permisos de nivel de base de datos. Algunos DMV de nivel de servidor no está disponibles, aunque algunas se sustituyen por DMV de nivel de base de datos.
- Sin servidor express: localdb, instancias de usuario
- Agente de servicio
- ESTABLECER REMOTE_PROC_TRANSACTIONS
- CIERRE
- sp_addmessage
- Opciones de sp_configure y vuelva a configurar. Algunas opciones están disponibles mediante [ALTERAR la configuración del ámbito de la base de datos](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Auditoría de SQL Server. Usar la base de datos SQL de auditoría en su lugar.
- Analizador de SQL Server
- Traza de SQL Server
- Marcas de seguimiento. Algunos elementos de la marca de seguimiento se ha movido a los modos de compatibilidad.
- Depuración de Transact-SQL
- Desencadenadores: Ámbito de servidor o desencadenadores de inicio de sesión
- Instrucción USE: para cambiar el contexto de la base de datos a una base de datos diferente, debe realizar una nueva conexión a la base de datos nueva.


## <a name="full-transact-sql-reference"></a>Referencia de Transact-SQL completa

Para obtener más información acerca de los ejemplos, el uso y la gramática de Transact-SQL, consulte [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx) en libros en pantalla de SQL Server. 

### <a name="about-the-applies-to-tags"></a>Acerca de las etiquetas "Se aplica a"

La referencia de Transact-SQL incluye temas relacionados con las versiones de SQL Server 2008 hasta el presente. Debajo del título del tema hay es un icono en la barra, enumerando las cuatro plataformas de SQL Server y que indica la aplicación. Por ejemplo, los grupos de disponibilidad introducidos en SQL Server 2012. El tema de [Crear el grupo de disponibilidad de información](https://msdn.microsoft.com/library/ff878399.aspx) indica que la instrucción se aplica a ** SQL Server (a partir de 2012). La instrucción no se aplica a SQL Server 2008, SQL Server 2008 R2, base de datos de SQL Azure, almacenamiento de datos de SQL Azure o paralelo almacén de datos.

En algunos casos, el asunto de un tema general puede usarse en un producto, pero hay diferencias menores entre productos. Las diferencias se indican en puntos de medios en el tema según corresponda.

