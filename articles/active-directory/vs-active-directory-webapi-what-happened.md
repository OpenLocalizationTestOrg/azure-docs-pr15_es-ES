<properties
    pageTitle="¿Qué ha ocurrido con mi proyecto WebApi (Visual Studio Azure Active Directory conectado servicio) | Microsoft Azure "
    description="Describe lo que pasa a un proyecto MVC WebApi se conecta a Azure AD mediante Visual Studio"
  services="active-directory"
    documentationCenter=""
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

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>¿Qué ha ocurrido con mi proyecto WebApi (Visual Studio Azure Active Directory conectado servicio)

> [AZURE.SELECTOR]
> - [Introducción](vs-active-directory-webapi-getting-started.md)
> - [Qué ha pasado](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>Se han agregado referencias

###<a name="nuget-package-references"></a>Referencias de paquete NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>Referencias.

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>Cambios de código

###<a name="code-files-were-added-to-your-project"></a>Archivos de código agregados a un proyecto

Clase de inicio de autenticación, **App_Start/Startup.Auth.cs** se agregó al proyecto que contiene la lógica de inicio para la autenticación de Azure AD.

###<a name="startup-code-was-added-to-your-project"></a>Código de inicio se ha agregado a su proyecto

Si ya tiene una clase de inicio del proyecto, el método de **configuración** se actualizó para incluir una llamada a `ConfigureAuth(app)`. En caso contrario, se agregó una clase de inicio a su proyecto.


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>El archivo app.config o web.config tiene nuevos valores de configuración.

Se han agregado las siguientes entradas de configuración.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>Se ha creado una aplicación de Azure AD

Una aplicación de AD Azure se creó en el directorio que seleccionó en el asistente.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>¿Si se ha revisado *Deshabilitar la autenticación de cuentas de usuario individuales*, adicionales cambios realizados a mi proyecto?
Se han quitado las referencias de paquete NuGet y archivos se han quitado y copia de seguridad. Dependiendo del estado de su proyecto, debe quitar referencias adicionales o archivos manualmente o modificar código según corresponda.

###<a name="nuget-package-references-removed-for-those-present"></a>Referencias de paquete NuGet quitadas (para los presentes)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>El código de los archivos de copia de seguridad y quite (por los presentes)

Cada uno de los siguientes archivos se realizan copias de seguridad y se quitó del proyecto. Archivos de copia de seguridad se encuentran en una carpeta 'Copia de seguridad' en la raíz del directorio del proyecto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>Copia archivos de código (para los presentes)

Cada uno de los siguientes archivos se realizan copias de seguridad antes de ser reemplazados. Archivos de copia de seguridad se encuentran en una carpeta 'Copia de seguridad' en la raíz del directorio del proyecto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>¿Si se ha revisado *los datos de directorio de lectura*, adicionales cambios realizados a mi proyecto?

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Se han realizado cambios adicionales en su app.config o web.config

Se han agregado las siguientes entradas de configuración adicionales.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>Se actualizó su aplicación de Azure Active Directory
La aplicación de Azure Active Directory se actualizó para incluir el permiso de *leer datos de directorio* y se creó una clave adicional, que se usa como *Ida: contraseña* en el `web.config` archivo.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
