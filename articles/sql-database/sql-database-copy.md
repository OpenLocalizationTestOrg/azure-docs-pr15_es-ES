<properties
    pageTitle="Copiar una base de datos de SQL Azure | Microsoft Azure"
    description="Crear una copia de una base de datos de SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Copiar una base de datos SQL Azure

> [AZURE.SELECTOR]
- [Información general](sql-database-copy.md)
- [Portal de Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Puede usar la [copias de seguridad de había automatizado de base de datos de SQL](sql-database-automated-backups.md) Azure para crear una copia de la base de datos SQL. La copia de la base de datos utiliza la misma tecnología como la característica de replicación geo. Pero a diferencia de replicación geo termina el vínculo de réplica como una vez completada la fase de inicio. Por lo tanto, la base de datos de copia es una instantánea de la base de datos de origen en el momento de la solicitud de copia.  
Puede crear la copia de la base de datos en el mismo servidor o en un servidor distinto. El nivel y rendimiento de nivel de servicio (nivel de precios) de la copia de la base de datos es la misma que la base de datos de origen de forma predeterminada. Cuando se usa la API, puede seleccionar un nivel de rendimiento diferentes dentro del mismo nivel de servicio (edición). Una vez completada la copia, la copia se convierte en una base de datos independiente completamente funcional. En este momento, puede actualizar o degradar a cualquier edición. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.  

Cuando copia una base de datos en el mismo servidor lógico, los mismos inicios de sesión pueden usarse en ambas bases de datos. El principal que utilizar para copiar la base de datos de seguridad se convierte en la base de datos propietario de la base de datos nueva. Todos los usuarios de la base de datos, los permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.  

Cuando copia una base de datos a otro servidor lógico, el principal en el nuevo servidor de seguridad se convierte en el propietario de la base de datos en la nueva base de datos. Si usa el [contenido los usuarios de la base de datos](sql-database-manage-logins.md) de acceso a datos garantiza bases de datos principales y secundarios siempre tienen las mismas credenciales de usuario para finalizar la copia inmediatamente pueda acceder con las mismas credenciales. Si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), puede eliminar completamente la necesidad de administrar credenciales en la copia. Sin embargo, cuando copie la base de datos a un nuevo servidor, el acceso de inicio de sesión según generalmente no funcionará porque los inicios de sesión no existe en el servidor nuevo. Consulte [cómo administrar la seguridad de la base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md) para obtener información acerca de cómo administrar inicios de sesión al copiar una base de datos a otro servidor lógico. 

Para copiar una base de datos SQL, se necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción de Azure simplemente haga clic en la **Versión de prueba gratuita** en la parte superior de esta página y, a continuación, vuelva a fin de este artículo.
- Base de datos SQL para copiar. Si no tiene una base de datos SQL, crear una siguiendo los pasos de este artículo: [crear la primera base de datos de SQL Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Copiar una base de datos de SQL Azure con el portal de Azure](sql-database-copy-portal.md) para copiar una base de datos con el portal de Azure.
- Consulte [Copiar una base de datos de SQL Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos con PowerShell.
- Consulte [Copiar una base de datos de SQL Azure con SQL T](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
- Consulte [cómo administrar la seguridad de la base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md) para obtener información acerca de cómo administrar usuarios y los inicios de sesión al copiar una base de datos a otro servidor lógico.



## <a name="additional-resources"></a>Recursos adicionales

- [Administrar inicios de sesión](sql-database-manage-logins.md)
- [Conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a una MOCHILA](sql-database-export.md)
- [Información general de continuidad empresarial](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
