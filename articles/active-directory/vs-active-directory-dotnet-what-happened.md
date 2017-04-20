<properties
    pageTitle="¿Qué ha ocurrido con mi proyecto MVC (Visual Studio Azure Active Directory conectado servicio) | Microsoft Azure "
    description="Describe lo que sucede al proyecto MVC cuando se conecte a Azure AD mediante servicios de Visual Studio conectado"
    services="active-directory"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>¿Qué ha ocurrido con mi proyecto MVC (Visual Studio Azure Active Directory conectado servicio)?

> [AZURE.SELECTOR]
> - [Introducción](vs-active-directory-dotnet-getting-started.md)
> - [Qué ha pasado](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>Se han agregado referencias

### <a name="nuget-package-references"></a>Referencias de paquete NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Referencias.

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Se ha agregado el código

### <a name="code-files-were-added-to-your-project"></a>Archivos de código agregados a un proyecto

Clase de inicio de autenticación, **App_Start/Startup.Auth.cs** se agregó al proyecto que contiene la lógica de inicio para la autenticación de Azure AD. Además, se ha agregado una clase de controlador, Controllers/AccountController.cs que contiene los métodos **SignIn()** y **SignOut()** . Por último, una vista parcial, **Views/Shared/_LoginPartial.cshtml** se agregó que contiene un vínculo de acción de inicio de sesión/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Código de inicio se ha agregado a su proyecto

Si ya tiene una clase de inicio del proyecto, el método de **configuración** se actualizó para incluir una llamada a **ConfigureAuth(app)**. En caso contrario, se agregó una clase de inicio a su proyecto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Su app.config o web.config tiene nuevos valores de configuración

Se han agregado las siguientes entradas de configuración.


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Se ha creado una aplicación de Azure Active Directory (AD)
Una aplicación de AD Azure se creó en el directorio que seleccionó en el asistente.

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>¿Si se ha revisado *Deshabilitar la autenticación de cuentas de usuario individuales*, adicionales cambios realizados a mi proyecto?
Se han quitado las referencias de paquete NuGet y archivos se han quitado y copia de seguridad. Dependiendo del estado de su proyecto, debe quitar referencias adicionales o archivos manualmente o modificar código según corresponda.

### <a name="nuget-package-references-removed-for-those-present"></a>Referencias de paquete NuGet quitadas (para los presentes)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>El código de los archivos de copia de seguridad y quite (por los presentes)

Cada uno de los siguientes archivos se realizan copias de seguridad y se quitó del proyecto. Archivos de copia de seguridad se encuentran en una carpeta 'Copia de seguridad' en la raíz del directorio del proyecto.

- **App_Start\IdentityConfig.cs**
- **Controllers\ManageController.cs**
- **Models\IdentityModels.cs**
- **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Copia archivos de código (para los presentes)

Cada uno de los siguientes archivos se realizan copias de seguridad antes de ser reemplazados. Archivos de copia de seguridad se encuentran en una carpeta 'Copia de seguridad' en la raíz del directorio del proyecto.

- **Startup.cs**
- **App_Start\Startup.auth.cs**
- **Controllers\AccountController.cs**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>¿Si se ha revisado *los datos de directorio de lectura*, adicionales cambios realizados a mi proyecto?

Se han agregado referencias adicionales.

###<a name="additional-nuget-package-references"></a>Referencias de paquete NuGet adicionales

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>Referencias de .NET adicionales

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>Archivos de código adicionales agregados a un proyecto

Dos archivos se agregaron para admitir el almacenamiento en caché token: **Models\ADALTokenCache.cs** y **Models\ApplicationDbContext.cs**.  Un controlador adicional y la vista se agregaron para ilustrar el acceso a información de perfil de usuario mediante las API de Azure gráfico.  Estos archivos son **Controllers\UserProfileController.cs** y **Views\UserProfile\Index.cshtml**.

###<a name="additional-startup-code-was-added-to-your-project"></a>Código de inicio adicional se ha agregado a su proyecto

En el archivo **startup.auth.cs** , se agregó un nuevo objeto **OpenIdConnectAuthenticationNotifications** al miembro de **notificaciones** de la **OpenIdConnectAuthenticationOptions**.  Esto es habilitar recibir el código de OAuth y exchange para un token de acceso.

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Se han realizado cambios adicionales en su app.config o web.config

Se han agregado las siguientes entradas de configuración adicionales.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Se han agregado las siguientes secciones de configuración y la cadena de conexión.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>Se actualizó su aplicación de Azure Active Directory
La aplicación de Azure Active Directory se actualizó para incluir el permiso de *leer datos de directorio* y se creó una clave adicional, que se usa como la *ida: ClientSecret* en el archivo **web.config** .

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
