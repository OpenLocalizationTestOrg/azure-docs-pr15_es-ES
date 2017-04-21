<properties
   pageTitle="Directrices y limitaciones de General de la base de datos SQL Azure"
   description="Esta página describe algunas limitaciones generales para la base de datos de SQL Azure, así como las áreas de interoperabilidad y soporte técnico."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Directrices y limitaciones de General de la base de datos SQL Azure

Este tema proporciona directrices y limitaciones generales para la base de datos de SQL Azure. Para una descripción completa de cuotas, administración de recursos y soporte técnico, consulte los [recursos adicionales](#additional-guidelines) al final de este tema.

## <a name="connectivity-and-authentication"></a>Autenticación y conectividad

  - No se admite la autenticación de Windows. Consulte [Administrar bases de datos y los inicios de sesión de base de datos SQL Azure](sql-database-manage-logins.md). Sin embargo, la autenticación de Azure Active Directory es compatible con algunas limitaciones. Vea [conectarse a la base de datos SQL con la autenticación de Azure Active Directory](sql-database-aad-authentication.md).

  - Base de datos de SQL de Microsoft Azure es compatible con la versión de cliente de protocolo de datos tabulares (TDS) de la secuencia 7.3 o posterior.

  - Solo las conexiones de TCP/IP permitidas.

  - El Explorador de SQL Server 2008, SQL Server no se admite porque la base de datos de SQL de Microsoft Azure no tiene puertos dinámicos, solo puertos 1433.

## <a name="sql-server-agentjobs"></a>Agente o trabajos SQL Server

Base de datos de SQL de Microsoft Azure no admite al agente de SQL Server, sin embargo, puede usar trabajos elásticos para ejecutar trabajos a través de uno a muchos bases de datos. Para obtener más información acerca de los trabajos elásticos, vea [elásticos trabajos](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Compatibilidad de intercalación de SQL Server

La intercalación de base de datos predeterminado usada por la base de datos de SQL de Microsoft Azure es **SQL_LATIN1_GENERAL_CP1_CI_AS**, donde **LATIN1_GENERAL** es inglés (Estados Unidos), **CP1** es la página de códigos 1252, **elementos de configuración** distingue mayúsculas de minúsculas y **AS** es acentos. No es posible modificar la intercalación para V12 bases de datos. Para obtener más información sobre cómo configurar la intercalación, consulte [Intercalar (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Requisitos de los nombres

No se permiten algunos nombres de usuario por motivos de seguridad. No puede usar los siguientes nombres:

 - **Administrador**
 - **Administrador**
 - **invitado**
 - **raíz**
 - **SA**

Nombres de todos los objetos nuevos deben cumplir las reglas de SQL Server para los identificadores. Para obtener más información, vea [identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

Además, los nombres de usuario y de inicio de sesión no pueden contener los \ caracteres (no se admite la autenticación de Windows).

## <a name="additional-guidelines"></a>Directrices adicionales

- Además de las limitaciones generales descritas en este artículo, base de datos de SQL tiene las cuotas de recursos específicos y limitaciones basadas en el **nivel de servicio**. Para obtener información general de los niveles de servicio, vea [niveles de servicio de base de datos de SQL](sql-database-service-tiers.md).

- Para otras limitaciones de la base de datos de SQL, vea [Límites de recursos de base de datos de SQL Azure](sql-database-resource-limits.md).

- Para seguridad directrices relacionadas, vea [directrices de seguridad de base de datos de SQL Azure y limitaciones](sql-database-security-guidelines.md).

- Otra área relacionada rodea la compatibilidad de base de datos de SQL Azure con las versiones locales de SQL Server, como SQL Server 2014 y SQL Server 2016. La última versión de V12 de base de datos de SQL Azure ha realizado muchas mejoras en esta área. Para obtener más detalles, consulte [Novedades de V12 de base de datos de SQL](sql-database-v12-whats-new.md).

- Para obtener información sobre la disponibilidad de controladores y soporte técnico para base de datos de SQL, vea [Bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md).
