<properties
   pageTitle="Cifrado de datos transparente en el almacén de datos SQL (T-SQL) | Microsoft Azure"
   description="Cifrado de datos transparente (TDE) en el almacén de datos SQL (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>Empezar a trabajar con el cifrado de datos transparente (TDE)


> [AZURE.SELECTOR]
- [Información general de seguridad](sql-data-warehouse-overview-manage-security.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Permisos necesarios

Para habilitar el cifrado de datos transparente (TDE), debe ser un administrador o un miembro de la función dbmanager.

## <a name="enabling-encryption"></a>Habilitar el cifrado

Siga estos pasos para habilitar TDE para un almacén de datos de SQL:

1. Conectarse a *la base de datos en el servidor que hospeda la base de datos con un inicio de sesión es un administrador o un miembro de la función **dbmanager** en la base de datos*
2. Ejecute la instrucción siguiente para cifrar la base de datos.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Deshabilitar el cifrado

Siga estos pasos para deshabilitar TDE para un almacén de datos de SQL:

1. Conectarse a *la base de datos con un inicio de sesión es un administrador o un miembro de la función **dbmanager** en la base de datos*
2. Ejecute la instrucción siguiente para cifrar la base de datos.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Antes de realizar cambios en la configuración de TDE se debe reanudar un almacén de datos de SQL en pausa.

## <a name="verifying-encryption"></a>Comprobación de cifrado

Para comprobar el estado de cifrado para un almacén de datos de SQL, siga los pasos siguientes:

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

## <a name="encryption-dmvs"></a>Cifrado DMV  

- [Sys.Databases][] 
- [Sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
