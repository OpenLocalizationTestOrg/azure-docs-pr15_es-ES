<properties
    pageTitle="Identificar bases de datos y tablas de base de datos de estirar ejecutando Asesor de base de datos de estirar | Microsoft Azure"
    description="Obtenga información sobre cómo identificar bases de datos y las tablas que son candidatos para expandir base de datos."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificar bases de datos y tablas de base de datos de estirar ejecutando estirar Asesor de base de datos

Para identificar las bases de datos y las tablas que son candidatos para la base de datos de estirar, descargar Asesor de actualización de SQL Server 2016 y ejecute el Asesor de base de datos de estirar. Estirar Asesor de base de datos también identifica los problemas de bloqueo.

## <a name="download-and-install-upgrade-advisor"></a>Descargar e instalar el Asesor de actualizaciones
Descargue e instale el Asesor de actualizaciones desde [aquí](http://go.microsoft.com/fwlink/?LinkID=613421). Esta herramienta no se incluye en el medio de instalación de SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Ejecutar el Asesor de expansión de la base de datos

1.  Ejecutar el Asesor de actualización.

2.  Seleccione **escenarios**y, a continuación, seleccione **Ejecutar ASESOR de base de datos de ESTIRAR**.

3.  En el módulo de **Ejecutar Asesor de estiramiento de base de datos** , haga clic en **Seleccionar bases de datos para analizar**.

4.  En el módulo **Seleccione bases de datos** , escriba o seleccione el nombre del servidor y la información de autenticación. Haga clic en **Conectar**.

5.  Aparece una lista de bases de datos en el servidor seleccionado. Seleccione las bases de datos que desea analizar. Haga clic en **Seleccionar**.

6.  En el módulo de **Ejecutar Asesor de estiramiento de base de datos** , haga clic en **Ejecutar**.  Se ejecuta el análisis.

## <a name="review-the-results"></a>Revise los resultados

1.  Cuando haya finalizado el análisis, en el módulo **Analyzed bases de datos** , seleccione una de las bases de datos que se analizan para mostrar el módulo de **resultados del análisis** .

    El módulo de **resultados del análisis** listas recomendadas tablas en la base de datos que cumplen los criterios de recomendación de forma predeterminada.

2.  En la lista de tablas en el módulo de **resultados del análisis** , seleccione una de las tablas recomendadas para mostrar el módulo de **resultados de la tabla** .

    Si hay problemas de bloqueo, el módulo de **resultados de la tabla** enumera los problemas de bloqueo de la tabla seleccionada. Para obtener información sobre problemas de bloqueo detectado estirar Asesor de base de datos, vea [limitaciones para expandir base de datos](sql-server-stretch-database-limitations.md).

3.  En la lista de problemas de bloqueo en el módulo de **resultados de la tabla** , seleccione uno de los problemas para mostrar más información sobre el problema seleccionado y propone pasos de mitigación. Implementar los pasos sugeridos mitigación si desea configurar la tabla seleccionada para expandir base de datos.

## <a name="next-step"></a>Siguiente paso
Habilitar expansión de la base de datos.

-   Para habilitar estiramiento de base de datos en una **base de datos**, vea [Habilitar estiramiento de base de datos para una base de datos](sql-server-stretch-database-enable-database.md).

-   Para habilitar estiramiento de base de datos en otra **tabla**, al estirar ya está habilitada en la base de datos, vea [Habilitar estiramiento de base de datos de una tabla](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Vea también

[Limitaciones de la base de datos extendido](sql-server-stretch-database-limitations.md)

[Habilitar estiramiento de base de datos para una base de datos](sql-server-stretch-database-enable-database.md)

[Habilitar la base de datos de ampliación de una tabla](sql-server-stretch-database-enable-table.md)

[Todos los temas de servicio de base de datos de Azure SQL Server estirar](sql-server-stretch-database-index-all-articles.md)
