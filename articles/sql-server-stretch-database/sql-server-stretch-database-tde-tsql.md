<properties
   pageTitle="Habilitar el cifrado de datos transparente (TDE) para SQL Server Database estiramiento en Azure TSQL | Microsoft Azure"
   description="Habilitar el cifrado de datos transparente (TDE) para SQL Server Database estiramiento en Azure TSQL"
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
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Habilitar el cifrado de datos transparente (TDE) para expandir base de datos de Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Portal de Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Cifrado de datos transparente (TDE) ayuda a proteger contra la amenaza de actividad malintencionada realizando en tiempo real cifrado y descifrado de la base de datos, las copias de seguridad asociados y los archivos de registro de transacciones en el resto sin necesidad de cambios en la aplicación.

TDE cifra el almacenamiento de toda una base de datos mediante una clave simétrica denominada la clave de cifrado de base de datos. La clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de Azure. Microsoft gira automáticamente estos certificados al menos cada 90 días. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente (TDE)].

##<a name="enabling-encryption"></a>Habilitar el cifrado

Para habilitar TDE para un Azure base de datos que almacena los datos migra desde una base de datos de SQL Server compatibles con estirar, realice lo siguiente:

1. Conectarse a *la base de datos en el servidor de Azure aloja la base de datos con un inicio de sesión es un administrador o un miembro de la función **dbmanager** en la base de datos*
2. Ejecute la instrucción siguiente para cifrar la base de datos.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>Deshabilitar el cifrado

Para deshabilitar TDE para un Azure base de datos que almacena los datos migra desde una base de datos de SQL Server compatibles con estirar, realice lo siguiente:

1. Conectarse a *la base de datos con un inicio de sesión es un administrador o un miembro de la función **dbmanager** en la base de datos*
2. Ejecute la instrucción siguiente para cifrar la base de datos.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Comprobación de cifrado

Para comprobar el estado de cifrado de una base de datos de Azure que almacena los datos que se migra desde una base de datos de SQL Server compatibles con estirar, realice lo siguiente:

1. Conectarse a la base de datos *principal* o instancia mediante un inicio de sesión es un administrador o un miembro de la función **dbmanager** en la base de datos
2. Ejecute la instrucción siguiente para cifrar la base de datos.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Un resultado de ```1``` indica una base de datos cifrada ```0``` indica una base de datos sin cifrar.


<!--Anchors-->
[Cifrado de datos transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
