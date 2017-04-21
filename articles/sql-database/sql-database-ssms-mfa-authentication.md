<properties
   pageTitle="De Azure AD AMF con la base de datos de SQL y almacén de datos SQL de soporte de SSMS | Microsoft Azure"
   description="Usar autenticación de factores múltiples con SSMS para base de datos SQL y almacén de datos SQL."
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
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Compatibilidad con SSMS Azure AD AMF con la base de datos de SQL y almacén de datos SQL

Azure base de datos de SQL y almacenamiento de datos de SQL Azure ahora compatible con conexiones de SQL Server Management Studio (SSMS) con *La autenticación Universal de Active Directory*. Autenticación Universal de Active Directory es un flujo de trabajo interactiva que admita *Azure con autenticación multifactor* (AMF). Azure AMF ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple. Ofrece autenticación segura con una gama de opciones de comprobación fácil, llamada de teléfono, mensaje de texto, las tarjetas inteligentes con pin o notificación de la aplicación móvil: permitir a los usuarios elegir el método prefieran. Para obtener una descripción de la autenticación multifactor, vea [Autenticación multifactor](../multi-factor-authentication/multi-factor-authentication.md).

SSMS admite:

- AMF interactiva con Azure AD con la posibilidad de validación de cuadro de diálogo emergente.
- No interactivo contraseña Active Directory y la autenticación integrada de Active Directory métodos que pueden usar en muchas aplicaciones diferentes (ADO.NET, JDBC, ODBC, etcetera). Estos dos métodos nunca resultado en cuadros de diálogo emergente.

Cuando se configura la cuenta de usuario para MFA el flujo de trabajo de autenticación interactiva requiere interacción de usuario adicionales a través de los cuadros de diálogo emergentes, uso de tarjetas inteligentes, etcetera. Cuando la cuenta de usuario está configurada para MFA, el usuario debe seleccionar autenticación Universal de Azure para conectarse. Si la cuenta de usuario no requiere AMF, el usuario aún puede usar las otras dos opciones de autenticación de Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitaciones de autenticación universales para la base de datos de SQL y almacén de datos SQL

- SSMS es la única herramienta actualmente habilitada para AMF a través de autenticación Universal de Active Directory.
- Una sola cuenta de Azure Active Directory puede iniciar sesión de una instancia de SSMS utilizando la autenticación Universal. Para iniciar sesión como otra cuenta de Azure AD, debe utilizar otra instancia de SSMS. (Esta restricción se limita a autenticación Universal de Active Directory; puede iniciar sesión en diferentes servidores con autenticación de contraseña de Active Directory, la autenticación integrada de Active Directory o la autenticación de SQL Server).
- SSMS es compatible con la autenticación Universal de Active Directory para visualización del explorador de objetos, el Editor de consultas y el almacenamiento de consulta.
- DacFx ni el Diseñador de esquemas admitir autenticación Universal.
- Cuentas MSA no son compatibles para la autenticación Universal de Active Directory.
- Autenticación Universal de Active Directory no es compatible con SSMS para los usuarios que se importan en Active Directory desde otros directorios de Azure Active actual. Estos usuarios no son compatibles, porque requeriría un identificador de inquilinos validar las cuentas y no hay ningún mecanismo proporcione.
- No hay ningún requisitos de software adicional para la autenticación Universal de Active Directory excepto en que debe usar una versión compatible de SSMS.

## <a name="configuration-steps"></a>Pasos de configuración

Implementación de la autenticación multifactor requiere cuatro pasos básicos.

1. **Configurar un Azure Active Directory** : para obtener más información, vea [integrar las identidades locales con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [agregar su propio nombre de dominio a Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure ahora admite la federación con Active Directory de Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrar el directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)y [Administrar Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurar AMF** – instrucciones detalladas, vea [Configurar la autenticación multifactor Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Configurar la base de datos SQL o almacén de datos de SQL Azure autenticación de AD** : para obtener instrucciones detalladas, vea [Conectar con base de datos SQL o datos almacén por usando Azure Active Directory autenticación de SQL](sql-database-aad-authentication.md).

4. **Descargar SSMS** : en el equipo cliente, descargue la última SSMS (al menos de 2016 de agosto), de [Descargar SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conexión mediante autenticación Universal con SSMS

Los pasos siguientes muestran cómo conectarse a la base de datos SQL o almacén de datos de SQL con la última SSMS.

1. Para conectarse mediante la autenticación Universal, en el cuadro de diálogo **conectarse al servidor** , seleccione **Autenticación Universal de Active Directory**.
![universal de 1mfa conectar][1]

2. Como de costumbre para la base de datos SQL y almacén de datos SQL debe haga clic en **Opciones** y especificar la base de datos en el cuadro de diálogo de **Opciones** . A continuación, haga clic en **Conectar**.
3. Cuando aparece el cuadro de diálogo **iniciar sesión en su cuenta** , proporcionar la cuenta y la contraseña de su identidad de Azure Active Directory.
![sesión 2mfa][2]

    > [AZURE.NOTE] Para la autenticación Universal con una cuenta que no necesita AMF, debe conectarse en este momento. Para usuarios que necesiten AMF, continúe con los pasos siguientes.
 
4. Pueden aparecer dos cuadros de diálogo de configuración MFA. Una vez operación depende de la configuración de administrador AMF y, por tanto, puede ser opcional. Para un dominio AMF habilitado este paso a veces es predefinido (por ejemplo, el dominio exige a los usuarios usar una tarjeta inteligente y un pin).  
![configuración de 3mfa][3]

5. La segunda posible una vez que el cuadro de diálogo le permite seleccionar los detalles de su método de autenticación. Las posibles opciones están configuradas por el administrador.
![1 Comprobar 4mfa][4]
 
6. Azure Active Directory envía la información de confirmación. Cuando reciba el código de verificación, escriba en el cuadro **código de comprobación de entrar** y haga clic en **iniciar sesión**.
![2-Comprobar 5mfa][5]

Cuando finalice la comprobación, conecta SSMS normalmente suponiendo que el acceso de firewall y credenciales válidas.

##<a name="next-steps"></a>Pasos siguientes  

Conceder a otros usuarios acceso a la base de datos: [autenticación de base de datos de SQL y la autorización: conceder acceso](sql-database-manage-logins.md)  
Asegúrese de que otros usuarios pueden conectarse a través del firewall: [Configurar una regla de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

