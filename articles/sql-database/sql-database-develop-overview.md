<properties
    pageTitle="Información general de desarrollo de base de datos SQL | Microsoft Azure"
    description="Obtenga información sobre los procedimientos recomendados para conectarse a la base de datos SQL de aplicaciones y bibliotecas de conectividad disponibles."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>Información general sobre el desarrollo de base de datos SQL
En este artículo se recorre las consideraciones básicas que un desarrollador debe tener en cuenta al escribir el código para conectarse a la base de datos de SQL Azure.

## <a name="language-and-platform"></a>Idioma y plataforma
Hay ejemplos de código para distintos idiomas y plataformas de programación. Puede encontrar vínculos a los ejemplos de código en: 

* Más información: [las bibliotecas de conexiones de base de datos de SQL y SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitaciones de los recursos
Base de datos de SQL Azure administra los recursos disponibles para una base de datos con dos mecanismos diferentes: gobernanza de recursos y cumplimiento de los límites.

* Más información: [los límites de recursos de la base de datos de SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Seguridad
Base de datos de SQL Azure proporciona recursos para limitar el acceso, cómo proteger los datos y supervisar las actividades en una base de datos de SQL.

* Más información: [seguridad de la base de datos SQL](sql-database-security.md)

## <a name="authentication"></a>Autenticación
* Base de datos de SQL Azure es compatible con los usuarios de autenticación de SQL Server e inicios de sesión, así como los usuarios de [autenticación de Azure Active Directory](sql-database-aad-authentication.md) tanto inicios de sesión.
* Debe especificar una base de datos determinado, en lugar de utilizar *la base de datos* .
* No puede usar la instrucción Transact-SQL **USE NombreBaseDatos;** en la base de datos SQL para cambiar a otra base de datos.
* Más información: [seguridad de base de datos SQL: administrar la seguridad de access e inicio de sesión de base de datos](sql-database-manage-logins.md)

## <a name="resiliency"></a>Resistencia
Cuando se produce un error temporal mientras se conecta a la base de datos de SQL, el código debe reintentar la llamada.  Recomendamos que vuelva a intentar la lógica de espera de uso de lógica, de modo que no se saturó la base de datos de SQL con varios clientes Reintentar simultáneamente.

* Ejemplos de código: para los ejemplos de código que muestran la lógica de reintento, vea ejemplos para el idioma de su elección en: [bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md)
* Más información: [mensajes de Error de los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Administrar conexiones
* En la lógica de conexión de cliente, reemplace el tiempo de espera predeterminado para que sea 30 segundos.  El valor predeterminado de 15 segundos es demasiado pequeña para conexiones que dependen de internet.
* Si está utilizando un [grupo de conexión](http://msdn.microsoft.com/library/8xx3tyca.aspx), no olvide cerrar la conexión la instantánea no lo está activa utilizando el programa y no se está preparando para volver a usarla.

## <a name="network-considerations"></a>Consideraciones de red
* En el equipo que aloja el programa cliente, asegúrese de que el firewall permite las comunicaciones salientes de TCP en el puerto 1433.  Más información: [configurar un servidor de seguridad de base de datos de SQL Azure](sql-database-configure-firewall-settings.md)
* Si el programa cliente se conecta a V12 de base de datos de SQL mientras el cliente se ejecuta en una máquina virtual (VM) en Azure, debe abrir determinados intervalos de puertos en la máquina virtual. Más información: [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
* Conexiones de cliente a V12 de base de datos de SQL Azure a veces utilizar al servidor proxy e interactúan directamente con la base de datos. Se convierten en puertos distintos 1433 importantes. Más información: [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Sharding de datos con escala elástica
Escala elástico simplifica el proceso de ajuste de escala (y en). 

* [Patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con base de datos SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md)
* [Introducción a la vista previa de escala elástico de base de datos SQL Azure](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Pasos siguientes

Explore las [capacidades de base de datos de SQL](https://azure.microsoft.com/services/sql-database/).
