<properties
   pageTitle="Cifrado de datos transparente en el almacén de datos SQL (Portal) | Microsoft Azure"
   description="Cifrado de datos transparente (TDE) en el almacén de datos SQL"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Introducción a cifrado con datos transparente (TDE) en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general de seguridad](sql-data-warehouse-overview-manage-security.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Permisos necesarios

Para habilitar el cifrado de datos transparente (TDE), debe ser un administrador o un miembro de la función dbmanager.

## <a name="enabling-encryption"></a>Habilitar el cifrado

Para habilitar TDE para un almacén de datos de SQL, siga los pasos siguientes:

1. Abra la base de datos en el [portal de Azure](https://portal.azure.com)
2. En el módulo de base de datos, haga clic en el botón **configuración**
3. Seleccione la opción de **cifrado de datos transparente**![][1]
4. Seleccione la configuración **en**![][2]
5. Seleccione **Guardar**
![][3]  

## <a name="disabling-encryption"></a>Deshabilitar el cifrado

Para deshabilitar TDE para un almacén de datos de SQL, siga los pasos siguientes:

1. Abra la base de datos en el [portal de Azure](https://portal.azure.com)
2. En el módulo de base de datos, haga clic en el botón **configuración**
3. Seleccione la opción de **cifrado de datos transparente**![][1]
4. Seleccione la opción **desactivar**![][4]
5. Seleccione **Guardar**
![][5]  

## <a name="encryption-dmvs"></a>Cifrado DMV

Puede confirmar cifrado con las siguientes DMV:

- [Sys.Databases]
- [Sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
