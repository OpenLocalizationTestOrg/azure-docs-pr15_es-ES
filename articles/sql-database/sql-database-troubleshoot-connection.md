<properties
    pageTitle="Base de datos en el servidor no está disponible actualmente, conectarse a la base de datos SQL | Microsoft Azure"
    description="Solucionar problemas de la base de datos de servidor no está disponible actualmente error cuando una aplicación se conecta a la base de datos de SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="base de datos en el servidor no está disponible actualmente, conectarse a la base de datos sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Error "No está disponible actualmente en el servidor de base de datos" al conectarse a la base de datos sql
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Cuando una aplicación se conecta a una base de datos de SQL Azure, recibe el siguiente mensaje de error:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Este mensaje de error es normalmente transitoria (breve).

Este error se produce cuando la base de datos de Azure se mueve (o volver a configurar) y la aplicación pierde la conexión a la base de datos SQL. Eventos de nueva configuración de base de datos SQL se produce debido a un evento planificado (por ejemplo, una actualización de software) o un evento no planificado (por ejemplo, un bloqueo de proceso o equilibrio de carga). La mayoría de los eventos de nueva configuración son de corta duración, en general y se deben completar en menos de 60 segundos como máximo. Sin embargo, estos eventos en ocasiones pueden tarden termine, como cuando una transacción grande hace que una recuperación de ejecución larga.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Pasos para resolver problemas de conectividad transitorias
1.  Comprobar el [Panel de servicio de Microsoft Azure](https://azure.microsoft.com/status) para cualquier interrupciones conocidas que ocurrieron durante el tiempo durante el que se han notificado los errores de la aplicación.
2. Aplicaciones que se conectan a un servicio de nube como base de datos de SQL Azure debe esperar eventos de la nueva configuración periódico e implementar vuelva a intentar la lógica para controlar estos errores en lugar de exponer como errores de aplicación a los usuarios. Revise la sección [transitorios errores](sql-database-connectivity-issues.md) y las mejores prácticas y directrices a [Información general sobre el desarrollo de base de datos de SQL](sql-database-develop-overview.md) para obtener más información y general Reintentar estrategias de diseño. A continuación, vea ejemplos de código en [Bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md) para obtener información específica.
3.  Como los límites de recursos aproxima a una base de datos, puede parecer un problema de conectividad transitorias. Vea [solución de problemas de rendimiento](sql-database-troubleshoot-performance.md).
4.  Si continúan los problemas de conectividad, o la duración que la aplicación detecta el error supera los 60 segundos, o ver varias apariciones del error en un momento determinado, archivo una solicitud de soporte técnico de Azure seleccionando **Obtener soporte técnico** en el sitio de [Soporte técnico de Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Pasos siguientes
- Si recibe un error diferente, evaluar el [mensaje de error](sql-database-develop-error-messages.md) para pistas sobre la causa.
- Si el problema es persistente, visite la guía en [solucionar problemas comunes de conexión a la base de datos de SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
