<properties
   pageTitle="Autenticación al almacén de datos SQL Azure | Microsoft Azure"
   description="Autenticación de Active Directory (AAD) y SQL Server Azure al almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Autenticación al almacén de datos SQL Azure

> [AZURE.SELECTOR]
- [Información general de seguridad](sql-data-warehouse-overview-manage-security.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Para conectarse al almacén de datos de SQL, debe pasar las credenciales de seguridad para la autenticación. Después de establecer una conexión, algunas configuraciones de conexión están configurados como parte del establecimiento de la sesión de la consulta.  

Para obtener más información sobre cómo habilitar las conexiones con el almacén de datos y seguridad, vea [proteger una base de datos en el almacén de datos de SQL][].

## <a name="sql-authentication"></a>Autenticación de SQL
Para conectarse al almacén de datos de SQL, debe proporcionar la siguiente información:

- Nombre de servidor completo
- Especificar la autenticación de SQL
- Nombre de usuario
- Contraseña
- Base de datos predeterminada (opcional)

De forma predeterminada la conexión se conecta a *la base de datos* y no la base de datos de usuario. Para conectarse a la base de datos de usuario, puede elegir realizar una de dos cosas:

- Especifique la base de datos de forma predeterminada cuando se registra el servidor con el Explorador de objetos de SQL Server en SSDT, SSMS, o en la cadena de conexión de la aplicación. Por ejemplo, incluya el parámetro InitialCatalog para una conexión ODBC.
- Resalte la base de datos de usuario antes de crear una sesión de SSDT.

> [AZURE.NOTE] No se admite la instrucción de Transact-SQL **USE MiBaseDeDatos;** para cambiar la base de datos para una conexión. Para obtener instrucciones conectarse al almacén de datos de SQL con SSDT, consulte el artículo de la [consulta con Visual Studio][] .

## <a name="azure-active-directory-aad-authentication"></a>Autenticación de Azure Active Directory (AAD)

[Azure Active Directory] [ What is Azure Active Directory] autenticación es un mecanismo de conexión al almacén de datos de SQL de Microsoft Azure mediante las identidades de Azure Active Directory (AD Azure). Con la autenticación de Azure Active Directory, puede administrar de forma centralizada las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. Administración central de ID le ofrece un lugar único para administrar los usuarios del almacén de datos de SQL y simplifica la administración de permisos. 

### <a name="benefits"></a>Ventajas

Ventajas de Azure Active Directory se incluyen:

- Proporciona una alternativa a la autenticación de SQL Server.
- Ayuda a detener la proliferación de identidades de usuario en servidores de base de datos.
- Permite la rotación de contraseña en un único lugar
- Administrar permisos de base de datos mediante grupos externos (AAD).
- Elimina las contraseñas de almacenamiento al habilitar la autenticación de Windows y otras formas de autenticación compatibles con Azure Active Directory.
- Usos contenido los usuarios de la base de datos para autenticar identidades en el nivel de base de datos.
- Es compatible con la autenticación basada en token para aplicaciones que se conectan al almacén de datos de SQL.
- Es compatible con la autenticación multifactor mediante la autenticación Universal de Active Directory para SQL Server Management Studio. Para obtener una descripción de la autenticación multifactor, vea [SSMS soporte técnico para Azure AD AMF con la base de datos SQL y almacén de datos de SQL](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory todavía es relativamente nuevo y tiene algunas limitaciones. Para asegurarse de que Azure Active Directory es una opción adecuada para su entorno, vea [características de Azure AD y limitaciones][], específicamente las consideraciones adicionales.

### <a name="configuration-steps"></a>Pasos de configuración

Siga estos pasos para configurar la autenticación de Azure Active Directory.

1. Crear y rellenar un Azure Active Directory
2. Opcional: Asociar o cambiar la active directory que está actualmente asociado a su suscripción de Azure
3. Crear un administrador de Azure Active Directory para el almacén de datos de SQL Azure.
4. Configurar los equipos cliente
5. Crear usuarios de base de datos incluidas en la base de datos que se asignan a las identidades de Azure AD
6. Conectarse a su almacén de datos con las identidades de Azure AD

Actualmente los usuarios de Azure Active Directory no se muestran en el Explorador de objetos SSDT. Como solución alternativa, ver los usuarios de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Encontrar los detalles
- Complete los pasos detallados. Los pasos detallados para configurar y usar autenticación de Azure Active Directory son casi idénticos para base de datos de SQL Azure y almacenamiento de datos de SQL Azure. Siga los pasos detallados en el tema [Conectar con base de datos SQL o datos almacén por usando Azure Active Directory autenticación de SQL](../sql-database/sql-database-aad-authentication.md).
- Crear funciones de base de datos personalizada y agregar usuarios a los roles. A continuación, conceder permisos específicos para los roles. Para obtener más información, vea [Introducción a los permisos del motor de base de datos](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Pasos siguientes

Para iniciar la consulta el almacén de datos con Visual Studio y otras aplicaciones, consulte [consulta con Visual Studio][].

<!-- Article references -->
[Proteger una base de datos en el almacén de datos de SQL]: ./sql-data-warehouse-overview-manage-security.md
[Consulta con Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Limitaciones y características de azure AD]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
