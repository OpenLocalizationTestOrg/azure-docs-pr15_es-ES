<properties
    pageTitle="Conectarse a la base de datos SQL con Java con JDBC en Windows | Microsoft Azure"
    description="Presenta una muestra de código de Java que puede usar para conectarse a la base de datos de SQL Azure. El ejemplo utiliza JDBC y se ejecuta en un equipo cliente Windows."
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Conectarse a la base de datos SQL con Java con JDBC en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tema presenta una muestra de código de Java que puede usar para conectarse a la base de datos de SQL Azure. Ejemplo de Java se basa en la versión 1,8 del Kit de desarrollo de Java (JDK). En el ejemplo se conecta a una base de datos de SQL Azure con el controlador JDBC.

## <a name="step-1--configure-development-environment"></a>Paso 1: Configurar el entorno de desarrollo

[Configurar el entorno de desarrollo de desarrollo de Java](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>Paso 2: Crear una base de datos SQL

Consulte la [página de introducción](sql-database-get-started.md) para obtener información sobre cómo crear una base de datos.  

## <a name="step-3-get-connection-string"></a>Paso 3: Obtener la cadena de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Si está utilizando el controlador JDBC JTDS, debe agregar "ssl = requerir" a la dirección URL de la conexión de cadena y que necesita establecer la opción siguiente para JVM "-Djsse.enableCBCProtection=false". Esta opción JVM deshabilita una solución para un problema de seguridad, asegúrese de que comprender qué riesgo está implicado antes de establecer esta opción.

## <a name="step-4-run-sample-code"></a>Paso 4: Ejecutar el código de ejemplo

* [Prueba de concepto conectar a SQL con Java](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>Pasos siguientes

* Visite el [Centro para desarrolladores de Java](/develop/java/).
* Revise la [información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md)
* Obtener más información sobre el [Controlador de JDBC de Microsoft para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>Recursos adicionales 

* [Patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con base de datos SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore las [capacidades de base de datos de SQL](https://azure.microsoft.com/services/sql-database/)
