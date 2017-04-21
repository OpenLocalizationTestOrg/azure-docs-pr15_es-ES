<properties
   pageTitle="Directrices de seguridad de base de datos SQL Azure y limitaciones | Microsoft Azure"
   description="Obtenga información sobre consideraciones de base de datos de SQL de Microsoft Azure y limitaciones relacionados con la seguridad."
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
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Limitaciones y directrices de seguridad de base de datos de SQL azure

Este tema describe las directrices de base de datos de SQL de Microsoft Azure y limitaciones relacionados con la seguridad. Tenga en cuenta lo siguiente al administrar la seguridad de las bases de datos de SQL Azure.

## <a name="access-to-the-virtual-master-database"></a>Acceso a la base de datos maestra virtual

Normalmente, solo los administradores de tener acceso a la base de datos. Se debe rutinario acceso a cada base de datos de usuario a través de los usuarios de base de datos no es administrador contenidas creados en cada base de datos. Al usar los usuarios de la base de datos contenido, no es necesario crear inicios de sesión en la base de datos. Para obtener más información, vea [Contenidos base de datos de usuarios: hacer que su base de datos portátil](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Firewall

El firewall de SQL Server, que es el ámbito de todo el servidor de SQL Azure normalmente se configura a través del portal y solo debe admitir las direcciones IP utilizadas por los administradores. Para crear una regla de firewall de ámbito de la base de datos que se abre el necesario intervalo de direcciones IP para cada base de datos, conectarse a una base de datos de usuario y, a continuación, use la [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) instrucción Transact-SQL.

El servicio de base de datos de SQL Azure sólo está disponible mediante el puerto TCP 1433. Para obtener acceso a una base de datos SQL de su equipo, asegúrese de que el firewall del equipo cliente permite comunicaciones salientes de TCP en el puerto TCP 1433. Si no es necesario para otras aplicaciones, bloquee las conexiones entrantes en el puerto TCP 1433. 

Como parte del proceso de conexión, conexiones desde máquinas virtuales de Windows Azure le redirige a una dirección IP diferente y puerto, único para cada función de trabajo. El número de puerto se encuentra en el intervalo de 11000 a 11999. Para obtener más información acerca de puertos TCP, consulte [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Autenticación

Usar autenticación de Active Directory (seguridad integrada) siempre que sea posible. Para obtener información sobre cómo configurar la autenticación de Active Directory, vea [Conectar con la base de datos por usando Azure Active Directory autenticación de SQL](sql-database-aad-authentication.md)y [Elegir un modo de autenticación](https://msdn.microsoft.com/library/ms144284.aspx) en libros en pantalla de SQL Server. 

Use los usuarios de la base de datos contenido para mejorar la escalabilidad. Para obtener más información, vea [Contenidos base de datos de usuarios: hacer que su base de datos portátil](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)y [Bases de datos de contenido](https://technet.microsoft.com/library/ff929071.aspx).

El motor de base de datos se cierra conexiones que permanezcan inactivas durante más de 30 minutos. La conexión debe volver a iniciar sesión antes de poder usarlo.

Continuamente conexiones activas a la base de datos SQL requieren reautorización (realizada por el motor de base de datos) como mínimo cada 10 horas. El motor de base de datos intenta reautorización utilizando la contraseña originalmente enviada y se requiere ninguna acción del usuario. Por motivos de rendimiento al restablece una contraseña de base de datos de SQL, la conexión no está autenticarse, incluso si se restablece la conexión debido a la agrupación de conexiones. Esto es diferente del comportamiento de la implementación local de SQL Server. Si la contraseña se cambió la conexión está autorizada inicialmente, se debe terminar la conexión y establece una nueva conexión con la nueva contraseña. Un usuario con permisos de eliminar la conexión de base de datos puede terminar explícitamente una conexión a la base de datos de SQL mediante el comando [Eliminar](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Inicios de sesión y los usuarios

Al administrar inicios de sesión y los usuarios de la base de datos SQL, hay restricciones.


- Debe estar conectado a **la base de datos** al ejecutar la ``CREATE/ALTER/DROP DATABASE`` instrucciones. -No se pueden modificar ni colocado el usuario de la base de datos en la base de datos correspondiente al inicio de sesión principal de nivel de servidor. 
- Inglés es el idioma predeterminado del inicio de sesión principal de nivel de servidor.
- Solo los administradores (nivel de servidor de inicio de sesión principal o administrador de Azure AD) y los miembros de la función de base de datos de **dbmanager** en **la base de datos** tienen permiso para ejecutar la ``CREATE DATABASE`` y ``DROP DATABASE`` instrucciones.
- Debe estar conectado a la base de datos al ejecutar la ``CREATE/ALTER/DROP LOGIN`` instrucciones. Sin embargo usando inicios de sesión no se recomienda. Utilice los usuarios de la base de datos contenido.
- Para conectarse a una base de datos de usuario, debe proporcionar el nombre de la base de datos de la cadena de conexión.
- Solo el inicio de sesión principal de nivel de servidor y los miembros de la función de base de datos de **tipo loginmanager** en **la base de datos** tienen permiso para ejecutar el ``CREATE LOGIN``, ``ALTER LOGIN``, y ``DROP LOGIN`` instrucciones.
- Al ejecutar la ``CREATE/ALTER/DROP LOGIN`` y ``CREATE/ALTER/DROP DATABASE`` instrucciones en una aplicación de ADO.NET, con los comandos con parámetros no está permitida. Para obtener más información, vea [comandos y parámetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Al ejecutar la ``CREATE/ALTER/DROP DATABASE`` y ``CREATE/ALTER/DROP LOGIN`` instrucciones, cada una de estas instrucciones deben ser la única instrucción en un lote de Transact-SQL. En caso contrario, se produce un error. Por ejemplo, Transact-SQL comprueba si existe la base de datos. Si existe una ``DROP DATABASE`` se denomina instrucción para quitar la base de datos. Dado que la ``DROP DATABASE`` instrucción no es la única instrucción en el lote, ejecutar la siguiente instrucción Transact-SQL produce un error.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Al ejecutar la ``CREATE USER`` instrucción con la ``FOR/FROM LOGIN`` opción, debe ser la única instrucción en un lote de Transact-SQL.
- Al ejecutar la ``ALTER USER`` instrucción con la ``WITH LOGIN`` opción, debe ser la única instrucción en un lote de Transact-SQL.
- Para ``CREATE/ALTER/DROP`` un usuario requiere la ``ALTER ANY USER`` permiso en la base de datos.
- Cuando el propietario de una función de base de datos intenta agregar o quitar un usuario de base de datos a o desde ese rol de base de datos, puede producirse el siguiente error: **usuario o función 'Nombre' no existe en esta base de datos.** Este error se produce porque el usuario no está visible al propietario. Para resolver este problema, conceda el propietario de la función la ``VIEW DEFINITION`` permiso en el usuario. 

Para obtener más información acerca de los usuarios y los inicios de sesión, vea [Administrar bases de datos e inicios de sesión en la base de datos de SQL Azure](sql-database-manage-logins.md).


## <a name="see-also"></a>Vea también

[Servidor de seguridad de base de datos SQL Azure](sql-database-firewall-configure.md)

[Cómo: establecer la configuración de Firewall (base de datos SQL Azure)](sql-database-configure-firewall-settings.md)

[Administrar bases de datos y los inicios de sesión en la base de datos de SQL Azure](sql-database-manage-logins.md)

[Centro de seguridad de base de datos SQL Azure y motor de base de datos SQL Server](https://msdn.microsoft.com/library/bb510589)
