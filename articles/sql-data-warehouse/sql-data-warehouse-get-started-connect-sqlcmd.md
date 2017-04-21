<properties
   pageTitle="Almacén de datos de SQL Azure (sqlcmd) de la consulta | Microsoft Azure"
   description="Consultar el almacén de datos de SQL Azure con la herramienta de línea de comandos sqlcmd."
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
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Almacén de datos de SQL Azure (sqlcmd) de la consulta

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizaje del equipo de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

En este tutorial, se usa la utilidad de línea de comandos [sqlcmd][] para consultar un almacén de datos de SQL Azure.  

## <a name="1-connect"></a>1. conectar

Para empezar a usar [sqlcmd][], abra el símbolo del sistema y escriba **sqlcmd** seguido de la cadena de conexión para la base de datos de almacén de datos de SQL. La cadena de conexión requiere los parámetros siguientes:

+ **Server (-S):** Servidor en el formulario `<`nombre del servidor`>`. database.windows.net
+ **Base de datos (-d.):** Nombre de la base de datos.
+ **Habilitar identificadores entre comillas dobles (-se):** Identificadores entre comillas dobles deben habilitarse para conectarse a una instancia de almacén de datos de SQL.

Para usar autenticación de SQL Server, debe agregar los parámetros de nombre de usuario y contraseña:

+ **Usuario (-U):** En el formulario de usuario del servidor `<`usuario`>`
+ **Contraseña (-P):** Contraseña asociada con el usuario.

Por ejemplo, la cadena de conexión podría ser similar al siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar autenticación de Azure Active Directory integrado, debe agregar los parámetros de Azure Active Directory:

+ **Autenticación de azure Active Directory (-G):** utilizar Azure Active Directory para la autenticación

Por ejemplo, la cadena de conexión podría ser similar al siguiente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Debe [Habilitar la autenticación de Azure Active Directory](sql-data-warehouse-authentication.md) autenticar con Active Directory.

## <a name="2-query"></a>2. consulta de

Después de la conexión, puede emitir las instrucciones de Transact-SQL compatibles con la instancia.  En este ejemplo, se envían las consultas en modo interactivo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Estos ejemplos siguientes muestra cómo puede ejecutar las consultas en modo de proceso por lotes mediante la opción -Q o tuberías su SQL a sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Pasos siguientes

Consulte la [documentación de sqlcmd][sqlcmd] para obtener más información acerca de los detalles sobre las opciones disponibles en sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[Sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
