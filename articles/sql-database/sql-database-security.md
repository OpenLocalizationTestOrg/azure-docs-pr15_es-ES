<properties
   pageTitle="Información general de seguridad de base de datos SQL"
   description="Obtenga más información sobre la seguridad de base de datos de SQL Azure y SQL Server, incluidas las diferencias entre la nube y SQL Server local cuando se trata de autenticación, autorización, seguridad de la conexión, el cifrado y cumplimiento."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>Proteger la base de datos SQL

## <a name="overview"></a>Información general

En este artículo se recorre los conceptos básicos de seguridad en el nivel de datos de una aplicación con la base de datos de SQL Azure. En particular, este artículo le ayudarán a empezar con recursos para limitar el acceso, cómo proteger los datos, y seguimiento de las actividades en una base de datos creado en la [Introducción con el tutorial de base de datos de SQL](sql-database-get-started.md). Para obtener una descripción completa de características de seguridad disponibles en todas las versiones de SQL, consulte el [Centro de seguridad para el motor de base de datos de SQL Server y base de datos de SQL Azure](https://msdn.microsoft.com/library/bb510589). Información adicional también está disponible en el [documento técnico seguridad y base de datos de SQL Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Seguridad de conexión

Seguridad de la conexión se refiere a cómo restringir y conexiones seguras a la base de datos usando las reglas de firewall y cifrado de la conexión.

Las reglas de Firewall se usan por el servidor y la base de datos para rechazar intentos de conexión de las direcciones IP que no se han explícitamente en la lista blanca. Para permitir que la aplicación o la dirección IP pública del equipo cliente intente conectarse a una base de datos, primero debe crear una regla de firewall de nivel de servidor con el Portal de Azure clásica, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP permitidos a través del firewall de servidor tanto como sea posibles. Para obtener más información, consulte [Firewall de base de datos de SQL Azure](https://msdn.microsoft.com/library/ee621782).

Todas las conexiones de base de datos de SQL Azure requieren cifrado (SSL/TLS) en todo momento mientras datos están "en tránsito" a y desde la base de datos. En la cadena de conexión de la aplicación, debe especificar los parámetros para cifrar la conexión y *no* debe confiar en el certificado de servidor (Esto se realiza automáticamente si copia la cadena de conexión en el Portal de clásico de Azure), en caso contrario, la conexión no comprobar la identidad del servidor y será susceptible a ataques "intermediario". Para el controlador ADO.NET, por ejemplo, estos parámetros de cadena de conexión son **cifrar = True** y **TrustServerCertificate = False**. Para obtener más información, vea [validación de certificado y el cifrado de conexión de base de datos de SQL Azure](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## <a name="authentication"></a>Autenticación

Autenticación se refiere a cómo acredita su identidad al conectarse a la base de datos. Base de datos SQL es compatible con dos tipos de autenticación:

 - **Autenticación de SQL**, que utiliza un nombre de usuario y contraseña
 - **Autenticación de azure Active Directory**, que utiliza identidades administradas por Azure Active Directory y es compatible con dominios administrados e integrados

Cuando creó el servidor lógico para la base de datos, especificar un inicio de sesión "Administrador de servidor" con un nombre de usuario y contraseña. Usa estas credenciales, puede autenticar a cualquier base de datos en el servidor como el propietario de la base de datos, o "dbo." Si desea usar autenticación de Azure Active Directory, debe crear otro administrador del servidor llamado el "Administrador de Azure AD", que se puede administrar grupos y usuarios de Azure AD. Este administrador también puede realizar todas las acciones que puede un administrador de servidor normal. Para un tutorial acerca de cómo crear un administrador de Azure AD para habilitar la autenticación de Azure Active Directory, vea [Conectar con la base de datos por usando Azure Active Directory autenticación de SQL](sql-database-aad-authentication.md) .

Como práctica recomendada su aplicación debe utilizar una cuenta diferente para autenticar--esta forma puede limitar los permisos para la aplicación y reducir el riesgo de actividad malintencionada en caso de que el código de la aplicación es vulnerable a un ataque de inserción de SQL. El enfoque recomendado es crear un [usuario de la base de datos de contenido](https://msdn.microsoft.com/library/ff929188), que permite a su aplicación autenticar directamente a una base de datos. Puede crear un usuario de base de datos contenido que utiliza la autenticación de SQL ejecutando el siguiente comando SQL T mientras está conectado a la base de datos de usuario como el inicio de sesión de administrador del servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Si ha creado un administrador de Azure AD, puede crear un usuario de base de datos contenido que utiliza la autenticación de Azure Active Directory ejecutando el siguiente comando SQL T mientras está conectado a la base de datos de usuario como administrador de Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

En cualquier caso, la cadena de conexión de la aplicación debe especificar estas credenciales de usuario, en lugar de con el inicio de sesión de la administración del servidor, para conectarse a la base de datos.

Para obtener más información acerca de cómo autenticar a una base de datos de SQL, vea [Administrar bases de datos e inicios de sesión en la base de datos de SQL Azure](sql-database-manage-logins.md).


## <a name="authorization"></a>Autorización
Autorización hace referencia a lo que puede hacer en una base de datos de SQL Azure, y se controla mediante los permisos y pertenencias a grupos de roles de su cuenta de usuario. Como práctica recomendada, debe conceder a los usuarios los privilegios mínimos necesarios. Base de datos de SQL Azure hace que sea fácil de administrar con funciones en T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Se conecta con la cuenta de administrador de servidor es un miembro de db_owner, que tiene autoridad para hacer nada dentro de la base de datos. Guardar esta cuenta para implementar las actualizaciones de esquema y otras operaciones de administración. Usar la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos necesarios para la aplicación.

Existen maneras de limitar más de un usuario que puede hacer con la base de datos de SQL Azure:

* [Funciones de base de datos](https://msdn.microsoft.com/library/ms189121) que no sea db_datareader y db_datawriter puede utilizarse para crear cuentas de usuario de aplicación más eficaces o menos eficaces cuentas de administración.
* Granulares [permisos](https://msdn.microsoft.com/library/ms191291) le permiten controlar las operaciones que se puede hacer en columnas individuales, tablas, vistas, procedimientos y otros objetos en la base de datos.
* [Suplantación](https://msdn.microsoft.com/library/vstudio/bb669087) y [firma de módulo](https://msdn.microsoft.com/library/bb669102) pueden utilizarse con seguridad temporalmente a conceder permisos.
* [Seguridad a nivel de fila](https://msdn.microsoft.com/library/dn765131) puede usarse límite que filas de un usuario puede tener acceso.
* [Masking de datos](sql-database-dynamic-data-masking-get-started.md) puede utilizarse para limitar la exposición de información confidencial.
* [Procedimientos almacenados](https://msdn.microsoft.com/library/ms190782) puede utilizarse para limitar las acciones que pueden realizar en la base de datos.

Administrar bases de datos y servidores lógicos desde el Portal de clásico de Azure o con la API del Administrador de recursos de Azure está controlado por las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, vea [control de acceso basado en roles en el Portal de Azure](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Cifrado

Base de datos de SQL Azure puede ayudar a proteger los datos mediante el cifrado de los datos cuando sea "en descanso" o almacenados en archivos de base de datos y copias de seguridad mediante el [Cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242). Para cifrar la base de datos, conéctese como propietario de una base de datos y ejecutar:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Para que conocer otras maneras de cifrar la información confidencial de datos, considere la posibilidad de:

* [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas específicas o las celdas de datos con claves de cifrado diferentes.
* Si necesita un módulo de seguridad de Hardware o de la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar [Azure clave depósito con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Siempre cifrados](https://msdn.microsoft.com/library/mt163865.aspx) (en la vista previa) permite cifrado transparente a aplicaciones y permite a los clientes cifrar datos confidenciales dentro de las aplicaciones cliente sin tener que compartir las claves de cifrado con la base de datos de SQL.

## <a name="auditing"></a>Auditoría

Auditoría y el seguimiento de eventos de base de datos pueden ayudar a mantener el cumplimiento de reglamentaciones y a identificar la actividad sospechosa. Auditoría de base de datos de SQL permite que a los eventos de registro en la base de datos a una auditoría iniciar sesión en su cuenta de almacenamiento de Azure. Auditoría de base de datos de SQL también se integra con Microsoft Power BI para facilitar el análisis e informes de detalle. Para obtener más información, vea [Introducción a la auditoría de la base de datos de SQL](sql-database-auditing-get-started.md).

Detección de amenaza de base de datos de SQL proporciona una capa adicional de seguridad además de auditoría. Le permite responder a las amenazas según se van produciendo proporcionando las alertas de seguridad en actividades irregulares. Para obtener más información, vea [Introducción a la detección de amenaza de base de datos de SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Cumplimiento

Además de las características y funcionalidades que pueden ayudar a su aplicación para satisfacer diversos requisitos de cumplimiento de seguridad, base de datos de SQL Azure también anteriores participa en auditorías periódicas y ha sido certificado con un número de estándares de cumplimiento. Para obtener más información, consulte el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/), donde puede encontrar una lista actualizada de [certificados de cumplimiento de base de datos de SQL](https://azure.microsoft.com/support/trust-center/services/).
