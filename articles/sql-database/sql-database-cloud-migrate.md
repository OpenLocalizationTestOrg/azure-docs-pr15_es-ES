<properties
   pageTitle="Migración de base de datos de SQL Server a la base de datos SQL | Microsoft Azure"
   description="Obtenga información sobre cómo se pueden local base de datos migración de SQL Server a la base de datos de SQL de Azure en la nube. Usar herramientas de migración de base de datos para probar la compatibilidad antes de la migración de base de datos."
   keywords="migración, migración de base de datos de sql server, herramientas de migración de base de datos de la base de datos, migrar la base de datos, migrar la base de datos sql"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migración de base de datos de SQL Server a la base de datos de SQL en la nube

En este artículo, obtenga información sobre cómo migrar un local de SQL Server 2005 o una versión posterior a la base de datos de SQL Azure. En este proceso de migración de base de datos, migrar su esquema y los datos de la base de datos de SQL Server en su entorno actual a base de datos de SQL. Para tener éxito, la base de datos existente en primer lugar debe pasar de una prueba de compatibilidad. Con [V12 de base de datos de SQL](sql-database-v12-whats-new.md), hay algunos problemas de compatibilidad restante, que no sea el problema relacionados con las operaciones de nivel de servidor y entre bases de datos. Bases de datos y las aplicaciones que se basan en [parcialmente o no se admite funciones](sql-database-transact-sql-information.md) necesita algunos volver a ingeniería para solucionar estos incompatibilidad antes de que se pueden migrar la base de datos de SQL Server.

Para migrar, los siguientes son los pasos que realice:

- **Prueba de compatibilidad**: validar la compatibilidad de la base de datos con [V12 de base de datos de SQL](sql-database-v12-whats-new.md). 
- **Solucionar problemas de compatibilidad, si hay alguna**: si se produce un error de validación, debe corregir los errores de validación.  
- **Realizar la migración** Una vez que la base de datos es compatible, puede usar uno o varios métodos para realizar la migración. 

SQL Server proporciona varios métodos para llevar a cabo cada una de estas tareas. Este artículo proporciona una descripción general de los métodos disponibles para cada tarea. El siguiente diagrama muestra los pasos y los métodos.

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Para migrar una base de datos no es de SQL Server, incluidos Microsoft Access, Sybase, MySQL Oracle y DB2 a la base de datos de SQL Azure, consulte el [Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Herramientas de migración de base de datos probar la compatibilidad de base de datos de SQL Server con la base de datos SQL

Para probar si hay problemas de compatibilidad de base de datos SQL antes de iniciar el proceso de migración de base de datos, use uno de los métodos siguientes:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Asesor de actualizaciones](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT usa las reglas de compatibilidad más recientes para detectar incompatibilidad V12 de base de datos de SQL. Si se detecta incompatibilidad, puede solucionar problemas detectados directamente en esta herramienta. Este método es el método recomendado para probar y solucionar problemas de compatibilidad de V12 de base de datos de SQL. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage es una herramienta de línea de comandos que comprobaciones para los problemas de compatibilidad y genera un informe que contiene los problemas de compatibilidad detectados. Si usa esta herramienta, asegúrese de que usar la versión más reciente a utilizar las reglas de compatibilidad más recientes. Si se detectan errores, debe usar otra herramienta para solucionar los problemas de compatibilidad detectados - SSDT se recomienda.  
- [Asistente de aplicación de la exportación de nivel de datos en SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): este asistente detecta e informa de errores en la pantalla. Si no se detectan errores, puede continuar y completar la migración de la base de datos de SQL. Si se detectan errores, debe usar otra herramienta para solucionar los problemas de compatibilidad detectados - SSDT se recomienda.
- [El Microsoft SQL Server 2016 actualizar Asesor de vista previa](http://www.microsoft.com/download/details.aspx?id=48119): esta herramienta independiente, que está en vista previa, detecta y genera un informe de incompatibilidad V12 de base de datos de SQL. Esta herramienta todavía no tiene las reglas de compatibilidad más recientes. Si no se encuentran errores, puede continuar y completar la migración de la base de datos de SQL. Si se detectan errores, debe usar otra herramienta para solucionar los problemas de compatibilidad detectados - SSDT se recomienda. 
- [El Asistente para la migración de SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW es una herramienta de codeplex que usa las reglas de compatibilidad V11 de base de datos de SQL Azure para detectar incompatibilidad V12 de base de datos de SQL Azure. Si se detecta incompatibilidad, algunos problemas pueden solucionarse directamente en esta herramienta. Esta herramienta puede encontrarse incompatibilidad que no deben corregirse. Fue la primera herramienta de asistencia de migración de base de datos de SQL Azure disponible y conocidas es compatible con la Comunidad de SQL Server. Además, esta herramienta puede completar la migración desde dentro de la propia herramienta. 

## <a name="fix-database-migration-compatibility-issues"></a>Solucionar problemas de compatibilidad de migración de base de datos

Si se detectan problemas de compatibilidad, debe corregirlos antes de continuar con la migración de base de datos de SQL Server. Hay una gran variedad de problemas de compatibilidad que pueden surgir, según tanto en la versión de SQL Server en la base de datos de origen y la complejidad de la base de datos que se va a migrar. Las versiones anteriores de SQL Server tienen más problemas de compatibilidad. Use los siguientes recursos, además de una búsqueda de Internet orientada mediante el motor de búsqueda de opciones:

- [Funciones de base de datos de SQL Server no se admite en la base de datos de SQL Azure](sql-database-transact-sql-information.md)
- [Funcionalidad de motor de base de datos que ya no está en SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidad de motor de base de datos que ya no está en SQL Server de 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidad de motor de base de datos que ya no está en SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funcionalidad de motor de base de datos que ya no está en SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funcionalidad de motor de base de datos que ya no está en SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Además de buscar en Internet y el uso de estos recursos, use los [foros de la Comunidad de MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o [StackOverflow](http://stackoverflow.com/).

Utilice una de las siguientes herramientas de migración de base de datos para solucionar los problemas detectados:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Usar [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para usar SSDT, importar su esquema de base de datos en SQL Server Data Tools para Visual Studio "SSDT") y genere el proyecto para una implementación V12 de base de datos de SQL. A continuación, solucionar los problemas de compatibilidad detectados en SSDT. Cuando haya terminado, sincronizar los cambios a la base de datos de origen (o una copia de la base de datos de origen. SSDT actualmente es el método recomendado para probar y solucionar problemas de compatibilidad de V12 de base de datos de SQL. Siga el vínculo de un [tutorial con SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Usar [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para usar SSMS, ejecute los comandos de Transact-SQL para corregir los errores detectados mediante la herramienta. Este método es principalmente para que usuarios avanzados modificar el esquema de la base de datos directamente en la base de datos de origen. 
- Usar [El Asistente para la migración de SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para usar SAMW, generar una secuencia de comandos de Transact-SQL de la base de datos de origen. El Asistente para las transformaciones de la secuencia de comandos, siempre que sea posible, para que el esquema sea compatible con la V12 de base de datos de SQL. Cuando haya terminado, puede conectarse SAMW a V12 de base de datos de SQL para ejecutar la secuencia de comandos. Esta herramienta también analiza los archivos de seguimiento para determinar los problemas de compatibilidad. La secuencia de comandos puede generar con solo esquema o puede incluir datos en formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrar una base de datos de SQL Server compatible para la base de datos SQL

Para migrar una base de datos de SQL Server compatible, Microsoft proporciona varios métodos de migración para diversos escenarios. El método que elija depende de su tolerancia para el tiempo de inactividad, el tamaño y la complejidad de la base de datos de SQL Server y la conectividad a la nube de Microsoft Azure.  

> [AZURE.SELECTOR]
- [Asistente para la migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar a archivo MOCHILA](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar desde archivo MOCHILA](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicación de transacciones](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Para elegir el método de migración, la primera pregunta es si se puede permitir poner la base de datos de producción durante la migración. Migrar una base de datos mientras se lleva a cabo las transacciones activas puede dar lugar a incoherencias de base de datos y daños de la base de datos posible. Hay muchos métodos para poner en modo inactivo una base de datos de deshabilitación de la conectividad de cliente con la creación de una [instantánea de la base de datos](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar el tiempo de inactividad mínimo, utilice [replicación de transacciones de SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si la base de datos cumple los requisitos para la replicación de transacciones. Si se puede permitir el tiempo de inactividad o va a realizar una migración de prueba de una base de datos de producción para migración posterior, considere la posibilidad de uno de los tres métodos siguientes:

- [Asistente para la migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): para pequeñas y medianas bases de datos, migrar una base compatible con SQL Server 2005 o posterior es tan sencillo como ejecutando la [Base de datos implementar para Microsoft Azure bases de datos](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) de SQL Server Management Studio.
- [Exportar a archivo MOCHILA](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) y, a continuación, en [Importar desde archivo MOCHILA](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): si tiene conectividad desafíos (sin conectividad, ancho de banda bajo o problemas de tiempo de espera) y medianas a grandes bases de datos, use un archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Con este método, exportar el esquema de SQL Server y los datos a un archivo de MOCHILA. A continuación, importar el archivo MOCHILA con el nivel de aplicación Asistente para exportar datos de SQL Server Management Studio o la utilidad de la línea de comandos de [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) de base de datos de SQL.
- Usar MOCHILA y BCP juntos: usar un archivo [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) y [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bases de datos mayores para lograr mayor puesta en paralelo para mejora el rendimiento, aunque con mayor complejidad. Con este método, migrar el esquema y los datos por separado.
 - [Exportar el esquema solo a un archivo de MOCHILA](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importar el esquema del archivo de MOCHILA solo](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) en la base de datos de SQL.
 - Usar [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extraer los datos en archivos sin formato y, a continuación, [cargar paralelo](https://technet.microsoft.com/library/dd425070.aspx) estos archivos en la base de datos de SQL Azure.

     ![SQL Server migración de base de datos: migrar la base de datos SQL en la nube.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Pasos siguientes

- [La vista previa de Microsoft SQL Server 2016 Asesor de actualizaciones](http://www.microsoft.com/download/details.aspx?id=48119)
- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos de SQL](sql-database-v12-whats-new.md)
[Transact-SQL parcialmente o no se admite funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
