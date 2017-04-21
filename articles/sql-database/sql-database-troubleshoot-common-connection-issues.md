<properties
    pageTitle="Solucionar problemas comunes de conexión a la base de datos de SQL Azure"
    description="Pasos para identificar y resolver los errores comunes de conexión de base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solucionar problemas de conexión a la base de datos de SQL Azure

Cuando se produce un error en la conexión de base de datos de SQL Azure, mensajes de [error](sql-database-develop-error-messages.md). En este artículo es un tema centralizado que le ayuda a solucionar problemas de conectividad de base de datos de SQL Azure. Presenta [hace comunes](#cause) de problemas de conexión, se recomienda [una herramienta de solución de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que le ayuda a identidad el problema y proporciona los pasos de solución de problemas para resolver [errores transitorios](#troubleshoot-transient-errors) y [persistente o no transitorios](#troubleshoot-the-persistent-errors). Por último, se muestran [todos los artículos relevantes para los problemas de conectividad de base de datos de SQL Azure](#all-topics-for-azure-sql-database-connection-problems).

Si tiene problemas de conexión, pruebe los pasos de solución de problemas que se describen en este artículo.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa

Problemas de conexión pueden deberse a uno de estos procedimientos:

- Error al aplicar las mejores prácticas y guías de diseño durante el proceso de diseño de la aplicación.  Vea [Información general sobre el desarrollo de base de datos de SQL](sql-database-develop-overview.md) para empezar.
- Nueva configuración de base de datos de SQL Azure
- Configuración del Firewall
- Tiempo de espera de conexión
- Información de inicio de sesión incorrecto
- Límite máximo en algunos recursos de la base de datos de SQL Azure

En general, problemas de conexión de base de datos de SQL Azure se pueden clasificar como sigue:

- [Errores transitorios (corta duración o intermitente)](#troubleshoot-transient-errors)
- [Persistentes o no transitorios errores (errores con regularidad periodicidad)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Pruebe el Solucionador de problemas para los problemas de conectividad de base de datos de SQL Azure

Si se produce un error de conexión específicas, pruebe [esta herramienta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que le ayudará a rápidamente identidad y resolver el problema.

## <a name="troubleshoot-transient-errors"></a>Solucionar problemas de transitorias
Si la aplicación está experimentando errores transitorios, revise los siguientes temas para obtener sugerencias sobre cómo solucionar problemas y reducir la frecuencia de estos errores:

- [Solución de problemas de base de datos &lt;x&gt; en el servidor &lt;y&gt; no está disponible (Error: 40613)](sql-database-troubleshoot-connection.md)
- [Solucionar problemas, diagnosticar y evitar errores de conexión de SQL y transitorias para base de datos de SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Solucionar errores persistentes (no transitorios errores)

Si la aplicación persistente no puede conectarse a la base de datos de SQL Azure, normalmente indica un problema con uno de estos procedimientos:

- Configuración del firewall. El servidor de seguridad de base de datos o del cliente de SQL Azure está bloqueando las conexiones de base de datos de SQL Azure.
- Nueva configuración del cliente de red: por ejemplo, una nueva dirección IP o un servidor proxy.
- Error de usuario: por ejemplo, mal escrito los parámetros de conexión, como el nombre del servidor en la cadena de conexión.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Pasos para resolver problemas de conectividad persistente

1.  Configurar [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir que al cliente de la dirección IP.
2.  En todos los servidores de seguridad entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes. Revise [configurar Firewall de Windows para permitir el acceso a SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) punteros adicionales.
3.  Compruebe la cadena de conexión y otras opciones de configuración de conexión. Vea la sección de la cadena de conexión en el [tema de problemas de conectividad](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.  Comprobar el estado del servicio en el panel. Si cree que hay una interrupción regional, vea [recuperar de una interrupción](sql-database-disaster-recovery.md) para conocer los pasos para recuperar a una nueva área.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Todos los temas para los problemas de conexión de base de datos de SQL Azure

La siguiente tabla enumera cada tema de problema de conexión que se aplica directamente al servicio de base de datos de SQL Azure.


| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 1 | [Solucionar problemas de conexión a la base de datos de SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Esta es la página de inicio para solucionar problemas de conectividad de base de datos de SQL Azure. Describe cómo identificar y resolver errores transitorios y persistente o no transitorios de base de datos de SQL Azure. |
| 2 | [Solucionar problemas, diagnosticar y evitar errores de conexión de SQL y transitorias para base de datos de SQL](sql-database-connectivity-issues.md) | Obtenga información sobre cómo solucionar problemas, diagnosticar y evitar que un error de conexión de SQL o transitorias en base de datos de SQL Azure. |
| 3 | [Directrices de control de errores generales transitorias](best-practices-retry-general.md) | Proporciona instrucciones generales para el control de errores transitorias al conectarse a la base de datos de SQL Azure. |
| 4 | [Solucionar problemas de conectividad de base de datos de SQL de Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Esta herramienta ayuda a identidad su problema solucionar errores de conexión. |
| 5 | [Solucionar problemas de "base de datos &lt;x&gt; en el servidor &lt;y&gt; no está disponible actualmente. Vuelva a intentar la conexión más tarde"error](sql-database-troubleshoot-connection.md) | Describe cómo identificar y resolver un error 40613: "base de datos &lt;x&gt; en el servidor &lt;y&gt; no está disponible actualmente. Vuelva a intentar la conexión más adelante." |
| 6 | [Códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos](sql-database-develop-error-messages.md) | Proporciona información acerca de los códigos de error SQL para las aplicaciones de cliente de base de datos SQL, como los errores comunes de conexión de base de datos, problemas de copia de la base de datos y errores generales. |
| 7 | [Guía de rendimiento de base de datos de SQL Azure para bases de datos](sql-database-performance-guidance.md) | Proporciona instrucciones para ayudarle a determinar qué nivel de servicio es adecuado para la aplicación. También se ofrecen recomendaciones para ajustar su aplicación para obtener el máximo partido a su base de datos de SQL Azure. |
| 8 | [Información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md) | Proporciona vínculos a ejemplos de código para diferentes tecnologías que puede usar para conectarse a e interactuar con la base de datos de SQL Azure. |
| 9 | Actualizar a la página de base de datos de SQL Azure v12 ([portal de Azure](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Proporciona instrucciones para actualizar servidores V11 de base de datos de SQL Azure y bases de datos existentes a V12 de base de datos de SQL Azure con Azure portal o PowerShell. |


## <a name="next-steps"></a>Pasos siguientes

- [Solucionar problemas de rendimiento de la base de datos de SQL Azure](sql-database-troubleshoot-performance.md)
- [Solucionar problemas de permisos de base de datos de SQL Azure](sql-database-troubleshoot-permissions.md)
- [Ver todos los temas para el servicio de base de datos de SQL Azure](sql-database-index-all-articles.md)
- [Busque la documentación de Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Ver las últimas actualizaciones del servicio de base de datos de SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>Recursos adicionales

- [Información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md)
- [Directrices de control de errores generales transitorias](../best-practices-retry-general.md)
- [Bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md)
- [La ruta de aprendizaje para el uso de la base de datos de SQL Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [La ruta de aprendizaje para el uso de herramientas y características de base de datos elásticas](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 
