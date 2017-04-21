<properties
   pageTitle="Proteger una base de datos en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para proteger una base de datos en el almacén de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger una base de datos en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general de seguridad](sql-data-warehouse-overview-manage-security.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

En este artículo se recorre los conceptos básicos de la seguridad de la base de datos de almacén de datos de SQL Azure. En particular, este artículo le ayudarán a empezar con recursos para limitar el acceso, cómo proteger los datos y supervisar las actividades en una base de datos.

## <a name="connection-security"></a>Seguridad de conexión

Seguridad de la conexión se refiere a cómo restringir y conexiones seguras a la base de datos usando las reglas de firewall y cifrado de la conexión.

Las reglas de Firewall se usan por el servidor y la base de datos para rechazar intentos de conexión de las direcciones IP que no se han explícitamente en la lista blanca. Para permitir conexiones desde la aplicación o la dirección IP pública del equipo cliente, primero debe crear una regla de firewall de nivel de servidor con el Portal de Azure, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP permitidos a través del firewall de servidor tanto como sea posibles.  Para obtener acceso a almacenamiento de datos de SQL Azure desde el equipo local, asegúrese de que el firewall de la red y el equipo local permite comunicaciones salientes en el puerto TCP 1433.  Para obtener más información, consulte [firewall de base de datos de SQL Azure][], [sp_set_firewall_rule][]y [sp_set_database_firewall_rule][].

Conexiones con el almacén de datos de SQL se cifran de forma predeterminada.  Modificar la configuración de conexión para deshabilitar el cifrado se pasan por alto.

## <a name="authentication"></a>Autenticación

Autenticación se refiere a cómo acredita su identidad al conectarse a la base de datos. Almacén de datos SQL es compatible actualmente con autenticación de SQL Server con un nombre de usuario y contraseña, así como un Azure Active Directory. 

Cuando creó el servidor lógico para la base de datos, especificar un inicio de sesión "Administrador de servidor" con un nombre de usuario y contraseña. Usa estas credenciales, puede autenticar a cualquier base de datos en el servidor como el propietario de la base de datos, o "dbo" mediante la autenticación de SQL Server.

Sin embargo, como práctica recomendada, los usuarios de su organización deben utilizar una cuenta diferente para autenticar. De esta forma puede limitar los permisos para la aplicación y reducir el riesgo de actividad malintencionada en caso de que el código de la aplicación es vulnerable a un ataque de inserción de SQL. 

Para crear un usuario autenticado de SQL Server, conectarse a **la base de datos en el servidor con el inicio de sesión de administración de servidor** y crear un nuevo inicio de sesión de servidor.  Además, es una buena idea crear un usuario en la base de datos para los usuarios del almacén de datos de SQL Azure. Crear un usuario en el patrón permite a un usuario iniciar sesión utilizando herramientas como SSMS sin especificar un nombre de base de datos.  También les permite utilizar el Explorador de objetos para ver todas las bases de datos en un servidor de SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

A continuación, conectarse a la **base de datos de almacén de datos de SQL** con el inicio de sesión de administración de servidor y crear un usuario de la base de datos basándose en el inicio de sesión de servidor que acaba de crear.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Si un usuario se pueden realizar una operación adicional como crear inicios de sesión o crear nuevas bases de datos, también tendrá que se asignará a la `Loginmanager` y `dbmanager` roles en la base de datos. Para obtener más información sobre estas funciones adicionales y autenticar a una base de datos de SQL, vea [Administrar bases de datos e inicios de sesión en la base de datos de SQL Azure][].  Para obtener más detalles en Azure AD para el almacén de datos de SQL, vea [conectarse a datos almacén por usando Azure Active Directory autenticación de SQL][].


## <a name="authorization"></a>Autorización

Autorización hace referencia a lo que puede hacer en una base de datos de almacén de datos de SQL Azure, y se controla mediante los permisos y pertenencias a grupos de roles de su cuenta de usuario. Como práctica recomendada, debe conceder a los usuarios los privilegios mínimos necesarios. Almacén de datos de SQL Azure hace que sea fácil de administrar con funciones en T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Se conecta con la cuenta de administrador de servidor es un miembro de db_owner, que tiene autoridad para hacer nada dentro de la base de datos. Guardar esta cuenta para implementar las actualizaciones de esquema y otras operaciones de administración. Usar la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos necesarios para la aplicación.

Existen maneras de limitar más de un usuario que puede hacer con la base de datos de SQL Azure:

- Granulares [permisos][] le permiten controlar las operaciones que se puede hacer en columnas individuales, tablas, vistas, procedimientos y otros objetos en la base de datos. Usar permisos específicos para tener mayor control y conceder los permisos mínimos necesarios. El sistema de permisos granulares es algo complicado y requerirá algunos práctico utilizar de forma eficaz.
- [Funciones de base de datos][] que no sea db_datareader y db_datawriter puede utilizarse para crear cuentas de usuario de aplicación más eficaces o menos eficaces cuentas de administración. Las funciones de base de datos fija integrados proporcionan una forma sencilla para conceder permisos, pero pueden dar lugar a otorgar más permisos que son necesarios.
- [Procedimientos almacenados][] puede utilizarse para limitar las acciones que pueden realizar en la base de datos.

Administrar bases de datos y servidores lógicos desde el Portal de clásico de Azure o con la API del Administrador de recursos de Azure está controlado por las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, vea [control de acceso basado en roles en el Portal de Azure][].

## <a name="encryption"></a>Cifrado

Azure SQL datos almacén transparente datos cifrado (TDE) ayuda a proteger contra la amenaza de actividad malintencionada realizando en tiempo real cifrado y descifrado de los datos en el resto.  Cuando cifrar la base de datos, las copias de seguridad asociados y los archivos de registro de transacciones se cifran sin necesidad de cambios en las aplicaciones. TDE cifra el almacenamiento de toda una base de datos mediante una clave simétrica denominada la clave de cifrado de base de datos. En la base de datos SQL la clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de base de datos de SQL. Microsoft gira automáticamente estos certificados al menos cada 90 días. El algoritmo de cifrado usado por el almacén de datos SQL es AES-256. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente][].

Puede cifrar la base de datos con el [Portal de Azure] [ Encryption with Portal] o [T SQL][Encryption with TSQL].

## <a name="next-steps"></a>Pasos siguientes

Para obtener más detalles sobre la conexión a su almacén de datos de SQL con distintos protocolos, vea [conectarse al almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Conectarse al almacén de datos SQL]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Conectarse al almacén de datos SQL mediante la autenticación de Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Servidor de seguridad de base de datos de SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Funciones de base de datos]: https://msdn.microsoft.com/library/ms189121.aspx
[Administrar bases de datos y los inicios de sesión en la base de datos de SQL Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Permisos]: https://msdn.microsoft.com/library/ms191291.aspx
[Procedimientos almacenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Cifrado de datos transparente]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Control de acceso basado en roles en el Portal de Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
