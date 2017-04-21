<properties
   pageTitle="Conectarse al almacén de datos SQL Azure | Microsoft Azure"
   description="Cómo encontrar el servidor de nombre y cadena de conexión para el almacén de datos de SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>Conectarse al almacén de datos SQL Azure

Este artículo le ayudará a conectarse al almacén de datos de SQL por primera vez.

## <a name="find-your-server-name"></a>Buscar el nombre de servidor

El primer paso para conectarse al almacén de datos de SQL es saber cómo encontrar el nombre del servidor.  Por ejemplo, el nombre del servidor en el ejemplo siguiente es sample.database.windows.net. Para buscar el nombre completo del servidor:

1. Vaya al [portal de Azure][].
2. Haga clic en **bases de datos SQL** 
3. Haga clic en la base de datos que desea conectarse.
4. Busque el nombre completo del servidor.

    ![Nombre completo del servidor][1]

## <a name="supported-drivers-and-connection-strings"></a>Controladores admitidos y cadenas de conexión

Almacén de datos de SQL Azure es compatible con [ADO.NET][], [ODBC][], [PHP][]y [JDBC][]. Haga clic en uno de los controladores anteriores para buscar la última versión y documentación. Para generar automáticamente la cadena de conexión para el controlador que está utilizando desde el portal de Azure, puede hacer clic en **mostrar cadenas de conexión de base de datos** del ejemplo anterior.  Siguiente también es algunos ejemplos del aspecto de una cadena de conexión para cada controlador.

> [AZURE.NOTE] Considere la posibilidad de configurar el tiempo de espera de la conexión a 300 segundos para permitir la conexión a superar breves períodos de disponibilidad.

### <a name="adonet-connection-string-example"></a>Ejemplo de cadena de conexión de ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Ejemplo de cadena de conexión ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Ejemplo de cadena de conexión de PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Ejemplo de cadena de conexión JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Configuración de conexión

Almacén de datos SQL estandariza algunas opciones de configuración durante la conexión y la creación de objetos. Esta configuración no se puede reemplazar e incluye:

| Configuración de la base de datos       | Valor                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ACTIVADO                           |
| [QUOTED_IDENTIFIERS][] | ACTIVADO                           |
| [FORMATO_FECHA][]         | MDA                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>Pasos siguientes

Para conectar y la consulta con Visual Studio, vea [consulta con Visual Studio][]. Para obtener más información acerca de las opciones de autenticación, vea [autenticación al almacén de datos de SQL Azure][].

<!--Articles-->
[Consulta con Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticación al almacén de datos SQL Azure]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[FORMATO_FECHA]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal de Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


