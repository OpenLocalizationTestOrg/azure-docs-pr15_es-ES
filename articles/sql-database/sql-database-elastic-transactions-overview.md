<properties
   pageTitle="Transacciones distribuidas en bases de datos de nube"
   description="Información general sobre las transacciones elásticos de la base de datos con la base de datos SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Transacciones distribuidas en bases de datos de nube

Transacciones de elásticos de la base de datos para la base de datos de Azure SQL (SQL DB) le permiten ejecutar transacciones que abarcan varias bases de datos en la base de datos de SQL. Transacciones elásticos de la base de datos para la base de datos SQL están disponibles para aplicaciones .NET utilizando ADO .NET e integran con la experiencia de programación familiar con las clases [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Para obtener la biblioteca, consulte [.NET Framework 4.6.1 (instalador Web)](https://www.microsoft.com/download/details.aspx?id=49981).

De forma local, este escenario requiere normalmente ejecutando el Coordinador de transacciones distribuidas de Microsoft (MSDTC). Dado que MSDTC no está disponible para la aplicación de la plataforma como servicio en Azure, la capacidad de coordinar transacciones distribuidas ahora está directamente integrada en DB de SQL. Las aplicaciones pueden conectar a cualquier base de datos de SQL para iniciar las transacciones distribuidas y transparente una de las bases de datos coordinar la transacción distribuida, tal como se muestra en la siguiente ilustración. 

  ![Transacciones distribuidas con las transacciones de base de datos elástico de base de datos SQL Azure ][1]

## <a name="common-scenarios"></a>Escenarios comunes

Transacciones de elásticos de la base de datos para la base de datos SQL permiten a aplicaciones realizar cambios atómicas a los datos almacenados en varias bases de datos de SQL diferente. La vista previa se centra en experiencias de desarrollo de cliente en C# y .NET. Una experiencia de servidor con SQL T está planificada para más tarde.  
Transacciones de base de datos elástico está dirigido a las situaciones siguientes:

* Aplicaciones de base de datos de múltiples en Azure: con este escenario, está divididos verticalmente datos en varias bases de datos SQL DB tal que diferentes tipos de datos residen en bases de datos diferentes. Algunas operaciones requieren cambios en los datos que se mantienen en dos o más bases de datos. La aplicación usa las transacciones de base de datos elástica para coordinar los cambios en las bases de datos y garantizar la atomicidad.

* Aplicaciones de base de datos sharded en Azure: con este escenario, el nivel de datos utiliza la [biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md) o self-sharding para dividir horizontalmente los datos a través de muchas bases de datos en la base de datos de SQL. Un caso de uso visibilidad es la necesidad de realizar cambios atómicas para una aplicación de múltiples arrendatario sharded cuando cambios ocupan los inquilinos. Considere por ejemplo una transferencia de un inquilino a otro, tanto que se encuentran en distintas bases de datos. Un segundo caso es poseen sharding para satisfacer las necesidades de capacidad de un inquilino grande que a su vez normalmente implica que debe extender por varias bases de datos utilizados para el mismo inquilino algunas operaciones atómicas. Un tercer caso es atómicas actualizaciones referencia a datos que se duplican en bases de datos. Ahora se pueden coordinar atómicas, transacciones, operaciones en estas líneas entre varias bases de datos mediante la vista previa.
Transacciones de base de datos elástico usar fases para garantizar la atomicidad de transacciones en todas las bases de datos. Es una buena opción para las transacciones con menos de 100 bases de datos a la vez en una sola transacción. Estos límites no se aplican pero uno debe esperar rendimiento y tasas de éxito para transacciones de base de datos elástico sufra cuando superen estos límites.


## <a name="installation-and-migration"></a>Instalación y migración

Las funciones para las transacciones de elásticos de la base de datos en la base de datos de SQL se proporcionan a través de las actualizaciones de las bibliotecas de .NET System.Data.dll y System.Transactions.dll. Los archivos DLL garantizar que confirmación en dos fases se usa cuando sea necesario para garantizar la atomicidad. Para iniciar el desarrollo de aplicaciones con las transacciones de base de datos elástico, instale [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) o una versión posterior. Cuando se ejecuta en una versión anterior de .NET framework, se producirá un error en las transacciones a promover a una transacción distribuida y se inicia una excepción.

Después de la instalación, puede usar la API de transacción distribuida en System.Transactions con conexiones de base de datos de SQL. Si tiene aplicaciones MSDTC existentes mediante estas API, simplemente reconstruir sus aplicaciones existentes para .NET 4.6 después de instalar el 4.6.1 Framework. Si sus proyectos destino .NET 4.6, usarán automáticamente los archivos DLL actualizados de la nueva versión de Framework y transacción distribuida que API llamadas en combinación con conexiones de base de datos SQL ahora se realizará correctamente.

Recuerde que las transacciones de base de datos elásticos no requieren instalar MSDTC. En su lugar, las transacciones de base de datos elástico se administran directamente por y dentro de la base de datos de SQL. Simplifica considerablemente escenarios de la nube desde una implementación de MSDTC no es necesaria utilizar transacciones distribuidas con DB de SQL. 4 de la sección se explica con más detalle cómo implementar transacciones de base de datos elásticos y requiere .NET framework junto con sus aplicaciones de nube para Azure.

## <a name="development-experience"></a>Experiencia de desarrollo

### <a name="multi-database-applications"></a>Aplicaciones de base de datos de múltiples

El código de ejemplo siguiente utiliza la experiencia de programación familiar con .NET System.Transactions. La clase TransactionScope establece una transacción de ambiente en .NET. ("Transacción ambiente" es uno que reside en el subproceso actual). Todas las conexiones abiertas dentro de TransactionScope participan en la transacción. Si participan diferentes bases de datos, la transacción se eleva automáticamente a una transacción distribuida. El resultado de la transacción se controla estableciendo el ámbito para completar para indicar una confirmación.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Aplicaciones de base de datos sharded
 
Transacciones de elásticos de la base de datos para la base de datos SQL también admiten coordinar las transacciones distribuidas que utilizar el método OpenConnectionForKey de la biblioteca de cliente de base de datos elástica para abrir conexiones de datos de escalados a nivel. Considere la posibilidad de casos donde se necesita para garantizar la coherencia de transacciones cambios entre varios valores de la clave sharding diferentes. Conexiones a la shards los valores de clave sharding diferentes de hospedaje se negociada con OpenConnectionForKey. En general, las conexiones pueden resultar shards diferentes que garantizar que transacciones garantías requiere una transacción distribuida. El ejemplo siguiente muestra este enfoque. Se supone que una variable denominada shardmap se usa para representar un mapa de partes de la biblioteca de cliente elásticos de la base de datos:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Instalación de .NET para los servicios de nube de Azure

Azure proporciona varias ofertas para aplicaciones .NET de host. Una comparación de las ofertas diferentes está disponible en [servicio de la aplicación de Azure, servicios de nube y comparación de máquinas virtuales de Windows](../app-service-web/choose-web-site-cloud-service-vm.md). Si el sistema operativo invitado de la oferta es menor que .NET 4.6.1 obligatorio para las transacciones elásticas, debe actualizar al sistema operativo invitado a 4.6.1. 

Para los servicios de aplicación de Azure, actualizaciones para el sistema operativo invitado actualmente no son compatibles. Para Azure máquinas virtuales de Windows, simplemente inicie sesión en la máquina virtual y ejecute al instalador de la versión más reciente de .NET framework. Para los servicios de nube de Azure, debe incluir la instalación de una versión más reciente de .NET en las tareas de inicio de la implementación. Los conceptos y pasos son documentados en [Instalar .NET en una función de servicio de nube](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Tenga en cuenta que el programa de instalación para .NET 4.6.1 puede requerir más espacio de almacenamiento temporal durante el proceso de inicio en servicios de nube de Azure que el programa de instalación para .NET 4.6. Para garantizar una instalación correcta, debe aumentar almacenamiento temporal para el servicio de nube de Azure en el archivo ServiceDefinition.csdef en la sección LocalResources y la configuración del entorno de la tarea de inicio, como se muestra en el ejemplo siguiente:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transacciones en varios servidores

Transacciones de base de datos elásticos son compatibles en diferentes servidores lógicos de base de datos de SQL Azure. Cuando las transacciones entre los límites de servidor lógico, los participantes servidores necesitan de introducirse en una relación de comunicación mutuo. Una vez que se ha establecido la relación de comunicación, cualquier base de datos en cualquiera de los dos servidores puede participar en transacciones elásticas con bases de datos del otro servidor. Con las transacciones que abarcan más de dos servidores lógicos, una relación de comunicación debe establecerse para cualquier par de servidores lógicos.

Use los siguientes cmdlets de PowerShell para administrar las relaciones de la comunicación entre servidores para las transacciones de la base de datos elástico:

* **Nuevo AzureRmSqlServerCommunicationLink**: Utilice este cmdlet para crear una nueva relación de comunicación entre dos servidores lógicos de base de datos de SQL Azure. La relación es simétrica, lo que significa que ambos servidores pueden iniciar transacciones con el otro servidor.
* **Get-AzureRmSqlServerCommunicationLink**: Utilice este cmdlet para recuperar las relaciones existentes de comunicación y sus propiedades.
* **Quitar AzureRmSqlServerCommunicationLink**: Utilice este cmdlet para quitar una relación de comunicación existente. 


## <a name="monitoring-transaction-status"></a>Supervisar el estado de la transacción

Usar vistas de administración dinámicas (DMV) en la base de datos de SQL para supervisar el estado y progreso de las transacciones de la base de datos elásticos en curso. Todas las DMV relacionados con las transacciones es relevantes para las transacciones distribuidas en DB de SQL. Puede encontrar la lista correspondiente de DMV aquí: [transacción relacionados vistas de administración dinámicas y funciones (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Estas DMV es especialmente útiles:

* **sys.dm\_López\_active\_transacciones**: enumera las transacciones activas y su estado. La columna UOW (unidad de trabajo) puede identificar las transacciones de secundarios diferentes que pertenecen a la misma transacción distribuida. Todas las transacciones en la misma transacción distribuida llevan el mismo valor UOW. Consulte la [documentación de DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obtener más detalles.
* **sys.dm\_López\_base de datos\_transacciones**: proporciona información adicional sobre las transacciones, como la ubicación de la transacción en el registro. Consulte la [documentación de DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obtener más detalles.
* **sys.dm\_López\_bloqueos**: proporciona información sobre los bloqueos que actualmente se llevan a cabo por las transacciones en curso. Consulte la [documentación de DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obtener más detalles.

## <a name="limitations"></a>Limitaciones 

Actualmente se aplican las siguientes limitaciones a las transacciones de elásticos de la base de datos en la base de datos SQL:

* Solo las transacciones de bases de datos en la base de datos SQL son compatibles. Otros [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) proveedores de recursos y bases de datos fuera de la base de datos de SQL no pueden participar en transacciones elásticos de la base de datos. Esto significa que no pueden extender las transacciones de base de datos elástico por local de SQL Server y bases de datos de SQL Azure. Para las transacciones distribuidas de forma local, seguir usando MSDTC. 
* Solo las transacciones de coordinados de cliente desde una aplicación .NET son compatibles. Soporte de servidor para T SQL como comenzar transacción distribuida es planeado, pero no está disponible todavía. 
* Sólo bases de datos de Azure SQL DB V12 son compatibles.
* No se admiten las transacciones de los servicios WCF. Por ejemplo, tiene un método de servicio WCF que se ejecuta una transacción. Cierre la llamada en un ámbito de transacción se producirá un error como un [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Recursos adicionales

¿Aún no se ha usando capacidades elástica de la base de datos de las aplicaciones de Azure? Consulte el [Mapa de documentación](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Si tiene preguntas, póngase contactar con nosotros en el [foro de la base de datos SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) y para las solicitudes de la característica de, agréguelas al [foro de comentarios de la base de datos de SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



