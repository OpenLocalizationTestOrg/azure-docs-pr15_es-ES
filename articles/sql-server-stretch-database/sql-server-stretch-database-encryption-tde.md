<properties
   pageTitle="Habilitar el cifrado de datos transparente (TDE) para SQL Server Database estiramiento en Azure | Microsoft Azure"
   description="Habilitar el cifrado de datos transparente (TDE) para SQL Server Database estiramiento en Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Habilitar el cifrado de datos transparente (TDE) para expandir base de datos de Azure
> [AZURE.SELECTOR]
- [Portal de Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Cifrado de datos transparente (TDE) ayuda a proteger contra la amenaza de actividad malintencionada realizando en tiempo real cifrado y descifrado de la base de datos, las copias de seguridad asociados y los archivos de registro de transacciones en el resto sin necesidad de cambios en la aplicación.

TDE cifra el almacenamiento de toda una base de datos mediante una clave simétrica denominada la clave de cifrado de base de datos. La clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de Azure. Microsoft gira automáticamente estos certificados al menos cada 90 días. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente (TDE)].

##<a name="enabling-encryption"></a>Habilitar el cifrado

Para habilitar TDE para un Azure base de datos que almacena los datos migra desde una base de datos de SQL Server compatibles con estirar, realice lo siguiente:

1. Abra la base de datos en el [portal de Azure](https://portal.azure.com)
2. En el módulo de base de datos, haga clic en el botón **configuración**
3. Seleccione la opción de **cifrado de datos transparente**![][1]
4. Seleccione la configuración **en** y, a continuación, seleccione **Guardar**
![][2]


##<a name="disabling-encryption"></a>Deshabilitar el cifrado

Para deshabilitar TDE para un Azure base de datos que almacena los datos migra desde una base de datos de SQL Server compatibles con estirar, realice lo siguiente:

1. Abra la base de datos en el [portal de Azure](https://portal.azure.com)
2. En el módulo de base de datos, haga clic en el botón **configuración**
3. Seleccione la opción de **cifrado de datos transparente**
4. Seleccione la opción **desactivar** y, a continuación, seleccione **Guardar**




<!--Anchors-->
[Cifrado de datos transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
