<properties
    pageTitle="Azure FS AMF y AD | Microsoft Azure"
    description="Esta es la página de autenticación multifactor de Azure que describe cómo empezar a trabajar con Azure AMF y AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Introducción a la autenticación multifactor de Azure y servicios de federación de Active Directory



<center>![Nube](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si su organización ha federados Active Directory local con Azure Active Directory con AD FS, hay dos opciones para el uso de la autenticación multifactor de Azure.

- Proteger recursos de nube con autenticación multifactor de Azure o los servicios de federación de Active Directory
- Proteger recursos de nube y locales con el servidor de autenticación multifactor de Azure

En la tabla siguiente se resume la experiencia de comprobación entre proteger recursos con autenticación multifactor de Azure y AD FS

|Experiencia de verificación - aplicaciones basadas en Examinar|Experiencia de verificación - aplicaciones no se basan en el explorador
:------------- | :------------- | :------------- |
Proteger recursos de Azure AD mediante la autenticación multifactor de Azure |<li>El primer paso de la comprobación se realiza en local con AD FS.</li> <li>El segundo paso es un método basado en teléfono lleva a cabo utilizando la autenticación de la nube.</li>|Los usuarios finales puede usar contraseñas de aplicaciones para iniciar sesión.
Proteger recursos de Azure AD mediante servicios de federación de Active Directory |<li>El primer paso de la comprobación se realiza en local con AD FS.</li><li>El segundo paso es local realizadas por teniendo en cuenta la notificación.</li>|Los usuarios finales puede usar contraseñas de aplicaciones para iniciar sesión.

Advertencias con contraseñas de aplicaciones para los usuarios federados:

- Las contraseñas de la aplicación se comprueban mediante la autenticación de la nube, para omitir la federación. La federación es solo usen al configurar una contraseña de aplicación.
- No se respeta la configuración de Control de acceso de cliente local mediante contraseñas de aplicaciones.
- Perderá local capacidad de registro de autenticación de contraseñas de aplicaciones.
- Deshabilitar o eliminar la cuenta puede tardar hasta tres horas para la sincronización de directorio, que se retrase deshabilitar o eliminación de contraseñas de aplicaciones en la identidad de la nube.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo configurar la autenticación multifactor de Azure o el servidor de autenticación multifactor de Azure con AD FS, consulte los siguientes artículos:

- [Proteger recursos de nube con autenticación multifactor de Azure y AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Proteger recursos de nube y locales con el servidor de autenticación multifactor de Azure con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Proteger recursos de nube y locales con el servidor de autenticación multifactor de Azure con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
