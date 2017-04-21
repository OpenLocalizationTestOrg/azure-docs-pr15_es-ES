
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparar la cadena de conexión


La siguiente tabla compara las cadenas de conexión que el programa de C# debe conectarse al servidor SQL local frente a la base de datos de SQL Azure en la nube. Las diferencias aparecen en negrita.


| Cadena de conexión<br/>Base de datos SQL Azure | Cadena de conexión<br/>Microsoft SQL Server |
| :-- | :-- |
| Servidor =**tcp:**{your_serverName_here}**. database.windows.net,1433**;<br/>Identificador de usuario = {your_loginName_here}**@{your_serverName_here}**;<br/>Contraseña = {your_password_here};<br/>**Base de datos = {your_databaseName_here};**<br/>**Tiempo de espera de conexión = 30**;<br/>**Cifrar = True**;<br/>**TrustServerCertificate = False**; | Servidor = {your_serverName_here};<br/>Identificador de usuario = {your_loginName_here};<br/>Contraseña = {your_password_here}; |


La **base de datos =** es opcional para SQL Server, pero es necesario para la base de datos de SQL.


[Propiedades de .NET ADO SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) : analiza todos los parámetros en detalle.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
