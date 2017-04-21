<properties
   pageTitle="Conectarse a la base de datos SQL o almacén de datos SQL mediante la autenticación de Azure Active Directory | Microsoft Azure"
   description="Obtenga información sobre cómo conectarse a la base de datos SQL con autenticación de Azure Active Directory."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Conectarse a la base de datos SQL o almacén de datos SQL mediante la autenticación de Azure Active Directory

Autenticación de Azure Active Directory es un mecanismo de conectarse a la base de datos de SQL de Microsoft Azure y [Almacén de datos de SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando las identidades de Azure Active Directory (AD Azure). Con la autenticación de Azure Active Directory, puede administrar de forma centralizada las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. Administración central de ID le ofrece un lugar único para administrar los usuarios de la base de datos y simplifica la administración de permisos. Las ventajas siguientes:

- Proporciona una alternativa a la autenticación de SQL Server.
- Ayuda a detener la proliferación de identidades de usuario en servidores de base de datos.
- Permite la rotación de contraseña en un único lugar
- Los clientes pueden administrar permisos de base de datos mediante grupos externos (AAD).
- Puede eliminar las contraseñas de almacenamiento al habilitar la autenticación de Windows y otras formas de autenticación compatibles con Azure Active Directory.
- Usos de autenticación de Azure Active Directory contenido los usuarios de la base de datos para autenticar identidades en el nivel de base de datos.
- Azure Active Directory es compatible con la autenticación basada en token para conectarse a la base de datos SQL de aplicaciones.
- Autenticación de Azure Active Directory admite ADFS (federación de dominios) o autenticación nativo de usuario y contraseña para una Azure Active Directory local sin sincronización de dominio.  
- Azure Active Directory es compatible con conexiones desde SQL Server Management Studio que usan Universal autenticación de Active Directory, que incluye la autenticación multifactor (AMF).  AMF incluye autenticación segura con una gama de opciones de comprobación fácil, llamada de teléfono, mensaje de texto, las tarjetas inteligentes con pin o notificación de la aplicación móvil. Para obtener más información, vea [SSMS soporte técnico para Azure AD AMF con la base de datos SQL y almacén de datos de SQL](sql-database-ssms-mfa-authentication.md).

Los pasos de configuración incluyen los siguientes procedimientos para configurar y usar autenticación de Azure Active Directory.

1. Crear y rellenar un Azure Active Directory.
2. Asegúrese de que la base de datos en V12 de base de datos de SQL Azure. (No es necesario para el almacén de datos de SQL.)
3. Opcional: Asociar o cambiar la active directory que está actualmente asociado a su suscripción de Azure.
4. Crear un administrador de Azure Active Directory para el servidor de SQL Azure o [Almacén de datos de SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configurar los equipos cliente.
6. Cree los usuarios de la base de datos incluidas en la base de datos que se asignan a las identidades de Azure AD.
7. Conectarse a la base de datos con las identidades de Azure AD.


## <a name="trust-architecture"></a>Arquitectura de confianza

El siguiente diagrama de alto nivel resume la arquitectura de la solución de autenticación de Azure AD con las bases de datos de SQL Azure. Los mismos conceptos que se aplican al almacén de datos de SQL. Para admitir la contraseña de usuario nativo de Azure Active Directory, se considera solo parte de la nube y base de datos de SQL Azure AD/Azure. Para admitir autenticación federado (o usuario y la contraseña de las credenciales de Windows), se requiere la comunicación con ADFS bloque. Las flechas indican las rutas de comunicación.

![diagrama de auth aad][1]

En el diagrama siguiente indica la federación, confianza y hospedaje relaciones que permiten a un cliente para conectarse a una base de datos mediante el envío de un símbolo. El token se autentica mediante un anuncio de Azure y es de confianza para la base de datos. Cliente 1 puede representar Azure Active Directory con usuarios nativos o Azure Active Directory con usuarios federados. Cliente 2 representa una posible solución, incluidos los usuarios importados; en este ejemplo procedentes de una federados Azure Active Directory con ADFS se está sincronizados con Azure Active Directory. Es importante comprender que el acceso a una base de datos utilizando la autenticación de Azure AD requiere que la suscripción de hospedaje está asociada a Azure Active Directory. La misma suscripción debe usarse para crear la base de datos de SQL Azure o el almacén de datos SQL de host de SQL Server.

![relación de suscripción][2]

## <a name="administrator-structure"></a>Estructura de administrador

Cuando utiliza la autenticación de Azure AD, hay dos cuentas de administrador para el servidor de base de datos SQL; el Administrador de SQL Server original y Azure AD. Los mismos conceptos que se aplican al almacén de datos de SQL. Solo el administrador basado en una cuenta de Azure AD puede crear el primer usuario de la base de datos de Azure AD contenido en una base de datos de usuario. El inicio de sesión del Administrador de Azure AD puede ser un usuario de Azure AD o un grupo de Azure AD. Cuando el administrador es una cuenta de grupo, puede usar cualquier miembro del grupo, lo que permite a varios administradores de Azure AD para la instancia de SQL Server. Con la cuenta de grupo como administrador mejora la capacidad de administración, permitiéndole centralmente agregar y quitar a miembros del grupo de Azure AD sin cambiar los usuarios o los permisos de base de datos de SQL. Sólo un administrador de Azure AD (un usuario o grupo) se puede configurar en cualquier momento.

![estructura de administración][3]

## <a name="permissions"></a>Permisos

Para crear nuevos usuarios, debe tener la `ALTER ANY USER` permisos en la base de datos. La `ALTER ANY USER` se pueden conceder permisos a cualquier usuario de la base de datos. La `ALTER ANY USER` permiso también se tienen cuentas de administrador del servidor y los usuarios de la base de datos con la `CONTROL ON DATABASE` o `ALTER ON DATABASE` permiso para dicha base de datos y los miembros de la `db_owner` rol de base de datos.

Para crear un usuario de la base de datos incluidas en la base de datos de SQL Azure o almacén de datos de SQL, debe conectarse a la base de datos utilizando una identidad de Azure AD. Para crear el primer usuario de contenidos de la base de datos, debe conectarse a la base de datos mediante el uso de un administrador de Azure Active Directory (que es el propietario de la base de datos). Esto se muestra en los pasos 4 y 5 pasos a continuación. Cualquier autenticación de Azure Active Directory solo es posible si el Administrador de Azure Active Directory se creó para la base de datos de SQL Azure o servidor de almacén de datos de SQL. Si el Administrador de Azure Active Directory se ha quitado del servidor, los usuarios existentes de Azure Active Directory creados previamente dentro de SQL Server pueden ya no podrá conectarse a la base de datos usando sus credenciales de Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitaciones y características de azure AD

Los miembros siguientes de Azure Active Directory se pueden aprovisionar en servidor de SQL Azure o almacén de datos SQL:

- Miembros nativos: un miembro creado en Azure AD en el dominio administrado o en un dominio de cliente. Para obtener más información, vea [agregar su propio nombre de dominio para Azure AD](../active-directory/active-directory-add-domain.md).

- Federado miembros de dominio: un miembro creado en Azure AD con un dominio federado. Para obtener más información, vea [Microsoft Azure ahora admite la federación con Active Directory de Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Miembros importados de Azure Active directorios que son miembros de un dominio federado o nativa.

- Grupos de Active Directory creados como grupos de seguridad.

Cuentas de Microsoft (por ejemplo, outlook.com, hotmail.com, live.com) u otras cuentas de invitado (por ejemplo gmail.com, yahoo.com) no son compatibles. Si puede iniciar sesión en [https://login.live.com](https://login.live.com) mediante la cuenta y la contraseña, que está usando una cuenta de Microsoft, que no es compatible con la autenticación de Azure AD para base de datos de SQL Azure o almacén de datos de SQL Azure.

### <a name="additional-considerations"></a>Consideraciones adicionales

- Para mejorar la capacidad de administración, es recomendable que aprovisionar un grupo de Azure Active Directory dedicado como administrador.
- Sólo un administrador de Azure AD (un usuario o grupo) se puede configurar para un servidor de SQL Azure o el almacén de datos de SQL Azure en cualquier momento.
- Sólo un administrador de Azure Active Directory para SQL Server inicialmente puede conectarse al servidor de SQL Azure o almacén de datos de SQL Azure con una cuenta de Azure Active Directory. El Administrador de Active Directory puede configurar los usuarios de la base de datos de Azure Active Directory.
- Se recomienda establecer el tiempo de espera de la conexión a 30 segundos.
- Autenticación de Azure Active Directory de soporte de 2016 de SQL Server Management Studio y SQL Server Data Tools para Visual Studio 2015 (versión 14.0.60311.1April 2016 o posterior). (Autenticación de azure Active Directory es compatible con el **proveedor de datos de .NET Framework para SQL Server**; al menos versión 4.6 de .NET Framework). Por lo tanto, las versiones más recientes de estas herramientas y aplicaciones de nivel de datos (CAD y .bacpac) pueden usar autenticación de Azure Active Directory.
- [Versión ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339) admite la autenticación de Azure Active Directory sin embargo `bcp.exe` no se puede conectar con la autenticación de Azure Active Directory porque utiliza una versión anterior del proveedor ODBC.
- `sqlcmd`es compatible con el principio de la autenticación de Azure Active Directory con versión 13.1 disponible desde el [Centro de descarga](http://go.microsoft.com/fwlink/?LinkID=825643).  
- SQL Server Data Tools para Visual Studio de 2015 requiere al menos la versión de 2016 de abril de las herramientas de datos (versión 14.0.60311.1). Actualmente los usuarios de Azure Active Directory no se muestran en el Explorador de objetos SSDT. Como solución alternativa, ver los usuarios de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Controlador de Microsoft JDBC 6.0 para SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) admite la autenticación de Azure Active Directory. Además, vea [establecer las propiedades de conexión](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase no se puede autenticar mediante la autenticación de Azure Active Directory.
- No se admiten algunas herramientas como BI y Excel.
- Los módulos de **Base de datos de importación** y **Exportación** portal Azure admiten la autenticación de Azure Active Directory para base de datos de SQL. También es posible importar y exportar con la autenticación de Azure Active Directory desde el comando PowerShell.


## <a name="1-create-and-populate-an-azure-ad"></a>1. crear y rellenar un anuncio de Azure

Crear un Azure Active Directory y rellénela con los usuarios y grupos. Azure Active Directory puede ser el dominio inicial Azure AD de dominio administrado. Azure Active Directory también puede ser un local Active Directory Domain Services federado con Azure Active Directory.

Para obtener más información, vea [integrar las identidades locales con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [agregar su propio nombre de dominio a Azure AD](../active-directory/active-directory-add-domain.md), [Microsoft Azure ahora admite la federación con Active Directory de Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrar el directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)y [Administrar Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. asegurarse de que la base de datos de SQL es versión 12

Autenticación de Azure Active Directory es compatible con la última V12 de base de datos de SQL. Para obtener información sobre V12 de base de datos de SQL y para saber si está disponible en su región, consulte [Novedades de la última V12 de actualización de base de datos de SQL](sql-database-v12-whats-new.md). Este paso no es necesario para el almacén de datos de SQL Azure porque solo está disponible en V12 almacén de datos de SQL.

Si tiene una base de datos existente, compruebe que está alojado en V12 de base de datos de SQL mediante una conexión a la base de datos (por ejemplo con SQL Server Management Studio) y ejecutar `SELECT @@VERSION;`. El resultado esperado para una base de datos en V12 de base de datos de SQL es al menos **Microsoft SQL Azure (RTM) - 12.0**. Si la base de datos no está alojado en V12 de base de datos de SQL, vea [Planear y prepararse para actualizar a V12 de base de datos de SQL](sql-database-v12-plan-prepare-upgrade.md)y, a continuación, visite el Portal de Azure clásico para migrar la base de datos a V12 de base de datos de SQL.

Como alternativa, puede crear una nueva base de datos en V12 de base de datos de SQL siguiendo los pasos descritos en [crear la primera base de datos de SQL Azure](sql-database-get-started.md). **Sugerencia**: leer el siguiente paso antes de seleccionar una suscripción para la nueva base de datos.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. opcional: Asociar o cambiar la active directory que está actualmente asociado a su suscripción de Azure

Para asociar la base de datos con el directorio de Azure AD para su organización, realizar el directorio de un directorio de confianza para la suscripción de Azure aloja la base de datos. Para obtener más información, consulte [cómo Azure suscripciones están asociadas a Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Información adicional:** Cada suscripción Azure tiene una relación de confianza con una instancia de Azure AD. Esto significa que confía ese directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero solo directorio confía en una suscripción. Puede ver qué directorio es de confianza para su suscripción en la pestaña **configuración** en [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos en Azure (sitios Web, bases de datos y así sucesivamente), que son más como recursos secundarios de una suscripción. Si una suscripción expira, a continuación, acceso a los otros recursos relacionados con la suscripción también se detiene. Pero el directorio permanece en Azure y puede asociar otra suscripción a ese directorio y continuar administrar los usuarios del directorio. Para obtener más información acerca de los recursos, consulte [acceso a los recursos descripción en Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Los procedimientos siguientes proporcionan instrucciones paso a paso sobre cómo cambiar el directorio de asociados de una suscripción determinada.

1. Conectarse a su [Portal de Azure clásica](https://manage.windowsazure.com/) con un administrador de suscripción de Azure.
2. En el encabezado izquierdo, seleccione **configuración**.
3. Las suscripciones aparecen en la pantalla de configuración. Si no aparece la suscripción que desee, haga clic en **suscripciones** en la parte superior, el cuadro **Filtro por directorio** desplegable y seleccione el directorio que contiene las suscripciones y, a continuación, haga clic en **Aplicar**.

    ![Seleccione la suscripción][4]
4. En el área **configuración** , haga clic en la suscripción y, a continuación, haga clic en **Editar directorio** en la parte inferior de la página.

    ![portal de configuración de AD][5]
5. En el cuadro **Directorio de editar** , seleccione Azure Active Directory está asociado con su SQL Server o el almacén de datos de SQL y, a continuación, haga clic en la flecha de la siguiente.

    ![selección de directorio de edición][6]
6. En el cuadro de diálogo **Confirmar** asignación directorio, compruebe que "**se eliminarán todos los coadministradores.**"

    ![Confirmar de directorio de edición][7]
7. Haga clic en la casilla para volver a cargar el portal.

> [AZURE.NOTE] Cuando se cambia el directorio, el acceso a todos los coadministradores, Azure AD usuarios y grupos y se quitan los usuarios de recurso de copia de directorio y ya no tienen acceso a esta suscripción o sus recursos. Solo, como administrador del servicio, puede configurar el acceso para los principales basadas en el nuevo directorio. Este cambio puede tardar mucho tiempo en propagarse a todos los recursos. Cambiar el directorio, también cambia el Administrador de Azure AD para base de datos SQL y almacén de datos SQL y no permitir el acceso de la base de datos para los usuarios existentes de Azure AD. Debe ser administrador de Azure AD restablecer (tal como se describe a continuación) y Azure nuevos usuarios de AD que deben crearse.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. crear un administrador de Azure AD de servidor de SQL Azure

Cada servidor de SQL Azure (que hospeda una base de datos SQL o el almacén de datos SQL) comienza con una cuenta de administrador de servidor único que es el Administrador de todo el servidor de SQL Azure. Debe crear un segundo administrador de SQL Server, que es una cuenta de Azure AD. Esta entidad de seguridad se crea como un usuario de base de datos incluidas en la base de datos. Como los administradores, las cuentas de administrador del servidor son los miembros de la función **db_owner** en la base de datos de cada usuario y escriba la base de datos de cada usuario como al usuario **propietario** . Para obtener más información acerca de las cuentas de administrador del servidor, vea [Administrar bases de datos e inicios de sesión en la base de datos de SQL Azure](sql-database-manage-logins.md) y la sección **inicios de sesión y los usuarios** de [directrices de seguridad de base de datos de SQL Azure y limitaciones](sql-database-security-guidelines.md).

Con Azure Active Directory replicación Geo, el Administrador de Azure Active Directory debe estar configurado para principal y los servidores secundarios. Si un servidor no tiene un administrador de Azure Active Directory, a continuación, inicios de sesión de Azure Active Directory y los usuarios reciben un "no se puede conectar" Error de servidor.

> [AZURE.NOTE] Los usuarios que no se basan en una cuenta de Azure AD (incluida la cuenta de administrador de servidor de SQL Azure), no puede crear Azure AD los usuarios, porque no tiene permiso para validar a los usuarios de la base de datos propuesto con Azure AD.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Aprovisionar a un administrador de Azure Active Directory para el servidor de SQL Azure a través del portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), en la esquina superior derecha, haga clic en la conexión a la lista desplegable de una lista de posibles directorios activo. Elija correcta Active Directory como el valor predeterminado de Azure AD. Este paso vincula la asociación de suscripción con Active Directory con el servidor de SQL Azure asegurarse de que se usa la misma suscripción ambas Azure AD y SQL Server. (Puede ser hospedando el servidor de SQL Azure base de datos de SQL Azure o almacén de datos de SQL Azure).

    ![Elija de ad][8]
2. En el encabezado izquierdo seleccione **servidores SQL Server**, seleccione el **SQL server**y, a continuación, en el módulo de **SQL Server** , haga clic en **configuración**en la parte superior.

    ![configuración de AD][9]
3. En el módulo de **configuración** , haga clic en ** Administrador de Active Directory.
4. En el módulo de **administración de Active Directory** , haga clic en **Administrador de Active Directory**y, a continuación, en la parte superior, haga clic en **establecer administrador**.
5. En el módulo de **administración de agregar** , buscar un usuario, seleccione el usuario o grupo a ser un administrador y, a continuación, haga clic en **Seleccionar**. (El módulo de administración de Active Directory muestra todos los miembros y grupos de Active Directory. No se pueden seleccionar usuarios o grupos que están atenuados porque no se admiten como administradores de Azure AD. (Consulte la lista de administradores compatibles en **Azure AD las características y limitaciones** anteriores). Control de acceso basado en roles (RBAC) solo se aplica a del portal y no se propaga a SQL Server.
6. En la parte superior del módulo de **administración de Active Directory** , haga clic en **Guardar**.
    ![elija Administrador][10]

    El proceso de cambiar el administrador puede tardar varios minutos. A continuación, nuevo administrador aparece en el cuadro **Administrador de Active Directory** .

> [AZURE.NOTE] Al configurar la administración de Azure AD, el nuevo nombre de administrador (usuario o grupo) no puede estar ya presente en la base de datos maestra virtual como un usuario de autenticación de SQL Server. Si está presente, se producirá un error en la configuración de administración de Azure AD; deshacer su creación y que indica que dicha administrador (nombre) ya existen. Dado que dicho usuario de autenticación de SQL Server no forma parte de Azure AD, se produce un error en cualquier esfuerzo para conectarse al servidor mediante la autenticación de Azure AD.

Para quitar más adelante administrador, en la parte superior del módulo de **administración de Active Directory** , haga clic en **quitar administración**y, a continuación, haga clic en **Guardar**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Aprovisionar a un administrador de Azure AD de servidor de SQL Azure con PowerShell

Para ejecutar los cmdlets de PowerShell, debe tener Azure PowerShell instalado y en ejecución. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Para crear un administrador de Azure AD, ejecute los siguientes comandos de PowerShell de Azure:

- AzureRmAccount agregar
- Seleccione AzureRmSubscription


Cmdlets que se usan para aprovisionar y administrar Azure AD administrador:

| Nombre de cmdlet                                       | Descripción                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Establecer AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Aprovisiona un administrador de Azure Active Directory para el servidor de SQL Azure o almacén de datos de SQL Azure. (Debe ser la suscripción actual). |
| [Quitar AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Quita un administrador de Azure Active Directory para el servidor de SQL Azure o almacén de datos de SQL Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Devuelve información acerca de un administrador de Azure Active Directory actualmente configurado para el servidor de SQL Azure o el almacén de datos de SQL Azure. |

Usar PowerShell de comando obtener ayuda para ver más detalles para cada uno de estos comandos, por ejemplo ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Las siguientes disposiciones de script un grupo de administradores de Azure AD denominado **DBA_Group** (Id. de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para la **demo_server** server en un grupo de recursos con el nombre de **grupo 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

El parámetro de entrada **DisplayName** acepta el nombre para mostrar Azure AD o el nombre del usuario Principal. Por ejemplo, ``DisplayName="John Smith"`` y ``DisplayName="johns@contoso.com"``. Para los grupos de Azure AD AD Azure nombre para mostrar es compatible.

> [AZURE.NOTE] El comando de Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` no impedirá el aprovisionamiento de Azure AD administradores para los usuarios no admitidos. Un usuario no admitido puede aprovisionar, pero no puede conectarse a una base de datos. (Consulte la lista de administradores compatibles en **Azure AD las características y limitaciones** anteriores).

El ejemplo siguiente utiliza opcional **Id. de objeto**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Azure AD **Id. de objeto** es necesario cuando **DisplayName** no es único. Para recuperar los valores de **Id. de objeto** y **DisplayName** , use la sección de Active Directory de Portal clásico de Azure y ver las propiedades de un usuario o grupo.

En el ejemplo siguiente se devuelve información sobre el actual administrador de Azure AD de servidor de SQL Azure:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

En el ejemplo siguiente se quita un administrador de Azure AD:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

También puede proporcionar un administrador de Azure Active Directory usando las API de REST. Para obtener más información, vea [referencia de la API de REST de servicio de administración y operaciones para operaciones de bases de datos de SQL Azure para bases de datos de SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. configurar los equipos cliente

En todos los equipos cliente, desde la que las aplicaciones o los usuarios conectan a la base de datos de SQL Azure o almacén de datos de SQL Azure con las identidades de AD Azure, debe instalar el software siguiente:

- .NET framework 4.6 o posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library para SQL Server (**ADALSQL. DLL**) está disponible en varios idiomas (x86 y amd64) desde el centro de descarga de [Microsoft Active Directory Authentication Library para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Herramientas

- Instalar [2016 de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o [SQL Server Data Tools para Visual Studio de 2015](https://msdn.microsoft.com/library/mt204009.aspx) cumple los requisitos de .NET Framework 4.6.
- Versión de instalaciones la x86 SSMS de **ADALSQL. DLL**.
- SSDT instala la versión amd64 de **ADALSQL. DLL**.
- Las últimas Visual Studio de [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumple los requisitos de .NET Framework 4.6, pero no se instala la versión amd64 necesarios de **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. crear usuarios de base de datos incluidas en la base de datos que se asignan a las identidades de Azure AD

### <a name="about-contained-database-users"></a>Acerca de los usuarios de la base de datos contenido

Autenticación de Azure Active Directory requiere que los usuarios de la base de datos se crean como usuarios de la base de datos contenido. Un usuario de la base de datos contenido basándose en una identidad de Azure AD es un usuario de base de datos que no tiene un inicio de sesión de la base de datos, y que se asigna a una identidad en el directorio de Azure AD que esté asociada a la base de datos. La identidad de Azure AD puede ser una cuenta de usuario individual o un grupo. Para obtener más información acerca de los usuarios de la base de datos contenido, vea [Hacer los usuarios de base de datos de contenidos su base de datos portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Los usuarios de la base de datos (con la excepción de los administradores) no se puede crear con portal. Funciones RBAC no se propagan a SQL Server, la base de datos SQL o el almacén de datos de SQL. Funciones de RBAC Azure se usan para administrar recursos de Azure y no se aplican a los permisos de base de datos. Por ejemplo, el rol de **Colaborador de SQL Server** no conceder acceso para conectarse a la base de datos SQL o el almacén de datos de SQL. Debe tener concedido el permiso de acceso directamente en la base de datos mediante instrucciones Transact-SQL.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Conectar con el almacén de base de datos o datos de usuario mediante SQL Server Management Studio o herramientas de datos de SQL Server

Para confirmar el Administrador de Azure AD está correctamente configurado, conectarse a **la base de datos con la cuenta de administrador de Azure AD** .
Para crear un usuario de base de datos de contenido basado en AD Azure (que no sea el administrador del servidor que es el propietario de la base de datos), conectarse a la base de datos con una identidad de Azure AD que tiene acceso a la base de datos.

> [AZURE.IMPORTANT] Compatibilidad con la autenticación de Azure Active Directory está disponible con [2016 de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [Las herramientas de datos de SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) en Visual Studio de 2015. La versión de 2016 de agosto de SSMS también incluye compatibilidad con Universal autenticación de Active Directory, que permite a los administradores requerir la autenticación multifactor mediante una llamada de teléfono, el mensaje de texto, las tarjetas inteligentes con pin o notificación de la aplicación móvil.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Conectar utilizando la autenticación integrada de Active Directory

Use este método si ha iniciado sesión Windows con sus credenciales de Azure Active Directory de un dominio federado.

1. Inicie Management Studio o herramientas de datos y en el cuadro de diálogo **Conectar con el servidor** (o **conectarse al motor de base de datos**), en el cuadro de **autenticación** , seleccione **Autenticación integrada de Active Directory**. Ninguna contraseña es necesario o se pueden especificar porque se presentará sus credenciales existentes para la conexión.
    ![Seleccione autenticación integrada de Active Directory][11]

2. Haga clic en el botón **Opciones** y a continuación, en la página **Propiedades de conexión** , en el cuadro **conectarse a la base de datos** , escriba el nombre de la base de datos de usuario que desea conectarse.
    ![Seleccione el nombre de la base de datos][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Conectar con la autenticación de contraseña de Active Directory

Use este método al conectar con un nombre principal de Azure AD con Azure AD administra el dominio. También puede usar para la cuenta federado sin acceso al dominio, por ejemplo, cuando se trabaja de forma remota.

Use este método si ha iniciado sesión Windows con las credenciales de un dominio no federado con Azure, o cuando utiliza la autenticación de Azure AD con Azure AD basado en la inicial o el dominio del cliente.

1. Inicie Management Studio o herramientas de datos y en el cuadro de diálogo **Conectar con el servidor** (o **conectarse al motor de base de datos**), en el cuadro de **autenticación** , seleccione **Autenticación de contraseña de Active Directory**.
2. En el cuadro **nombre de usuario** , escriba su nombre de usuario de Azure Active Directory con el formato **username@domain.com**. Debe ser una cuenta de Azure Active Directory o una cuenta de un dominio federarse con Azure Active Directory.
3. En el cuadro **contraseña** , escriba la contraseña de usuario para la cuenta de Azure Active Directory o federado cuenta de dominio.
    ![Seleccione autenticación de contraseña de AD][12]

4. Haga clic en el botón **Opciones** y a continuación, en la página **Propiedades de conexión** , en el cuadro **conectarse a la base de datos** , escriba el nombre de la base de datos de usuario que desea conectarse. (Consulte el gráfico en la opción anterior).


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Crear un usuario de la base de datos de Azure AD contenido en una base de datos de usuario

Para crear un usuario de base de datos de contenido basado en AD Azure (que no sea el administrador del servidor que es el propietario de la base de datos), conectarse a la base de datos con una identidad de Azure AD, como un usuario con al menos el permiso **ALTER ANY USER** . A continuación, use la siguiente sintaxis Transact-SQL:

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* puede ser el nombre de usuario principal de un usuario de Azure AD o el nombre para mostrar de un grupo de Azure AD.

**Ejemplos:** Para crear una base de datos contiene usuario que representa un anuncio Azure federado o administrado usuario de dominio:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Para crear un usuario de base de datos contenido que representa un anuncio Azure o federado grupo de dominio, proporcione el nombre para mostrar de un grupo de seguridad:

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Para crear un usuario de la base de datos contenido que representa una aplicación que se conecta con un símbolo de Azure AD:

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Para obtener más información sobre la creación de usuarios de contenido de la base de datos en función de las identidades de Azure Active Directory, vea [Crear usuario (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Quitar el Administrador de Azure Active Directory para el servidor de SQL Azure impide que cualquier usuario de autenticación de Azure AD conectarse al servidor. Si es necesario, puede utilizar los usuarios de Azure AD pueden eliminarse manualmente por un administrador de la base de datos de SQL.

Cuando se crea un usuario de la base de datos, ese usuario recibe el permiso **CONNECT** y puede conectarse a esa base de datos como miembro de la función **pública** . Inicialmente los permisos disponibles para el usuario son los permisos para la función **pública** o permisos a los grupos de Windows que son miembros de. Una vez que se aprovisiona un usuario de base de datos de contenido basado en AD Azure, puede conceder al usuario permisos adicionales, del mismo modo como conceder permiso a cualquier otro tipo de usuario. Normalmente conceder permisos a las funciones de base de datos y agregar usuarios a funciones. Para obtener más información, vea [Conceptos básicos de permiso de motor de base de datos](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obtener más información acerca de las funciones especiales de base de datos de SQL, vea [Administrar bases de datos e inicios de sesión en la base de datos de SQL Azure](sql-database-manage-logins.md).
Un usuario de dominio federado que se importa en un dominio de administrar, debe usar la identidad de dominio administrado.

> [AZURE.NOTE] Usuarios de Azure AD se marcan en los metadatos de la base de datos con tipo E (EXTERNAL_USER) y para los grupos con tipo X (EXTERNAL_GROUPS). Para obtener más información, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. conectarse con las identidades de Azure AD

Autenticación de Azure Active Directory es compatible con los siguientes métodos para conectarse a una base de datos con las identidades de Azure AD:

- Mediante la autenticación de Windows integrada
- Usar un nombre principal de Azure AD y una contraseña
- Mediante la autenticación de token de aplicación

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Conexión mediante la autenticación integrada (Windows)

Para usar la autenticación de Windows, de Active Directory de su dominio se debe federado con Azure Active Directory. Debe ejecutar la aplicación de cliente (o un servicio) conectarse a la base de datos en un equipo unido al dominio con credenciales de dominio de un usuario.

Para conectarse a una base de datos utilizando la autenticación integrada y una identidad de Azure AD, la palabra clave de autenticación en la cadena de conexión de base de datos debe estar establecida en Active Directory integrado. El siguiente ejemplo de código C# usa ADO .NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Nota que la cadena de conexión palabra clave ``Integrated Security=True`` no se admite para conectarse a la base de datos de SQL Azure.
Tenga en cuenta que al realizar una conexión ODBC debe quitar espacios y configurar la autenticación de 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Conectar con un nombre principal de Azure AD y una contraseña
Para conectarse a una base de datos utilizando la autenticación integrada y una identidad de Azure AD, la palabra clave de autenticación debe estar establecida en contraseña Active Directory. La cadena de conexión debe contener UID o identificador de usuario y palabras clave de contraseña/PWD y valores. El siguiente ejemplo de código C# usa ADO .NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Más información sobre los métodos de autenticación de Azure AD con los ejemplos de código de demostración disponibles en [Azure AD autenticación GitHub demostración](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 conectar con un símbolo de Azure AD
Este método de autenticación permite a los servicios de nivel intermedio para conectarse a la base de datos de SQL Azure o almacén de datos de SQL Azure obteniendo un token de Azure Active Directory (AAD). Le permite escenarios sofisticados, incluida la autenticación de certificado. Debe completar los cuatro pasos básicos para usar la autenticación de token de Azure AD:

1. Registrar la aplicación con Azure Active Directory y obtener el identificador de cliente para el código. 
2. Crear un usuario de la base de datos que representa la aplicación. (Completado en el paso 6).
3. Crear un certificado en el equipo cliente ejecuta la aplicación.
4. Agregar el certificado como una clave para la aplicación.

Cadena de conexión de ejemplo:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Para obtener más información, consulte [El Blog de seguridad de SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Conectar con sqlcmd  
Las siguientes afirmaciones, conectar utilizando versión 13.1 de sqlcmd, que está disponible desde el [Centro de descarga](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Vea también

[Administrar bases de datos y los inicios de sesión de base de datos SQL Azure](sql-database-manage-logins.md)

[Usuarios de la base de datos contenido](https://msdn.microsoft.com/library/ff929071.aspx)

[Crear usuario (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

