<properties
    pageTitle="Configurar un dispositivo de Windows 10 con Azure AD de configuración | Microsoft Azure"
    description="Explica cómo los usuarios pueden unirse a Azure AD a través del menú de configuración."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurar un dispositivo de Windows 10 con Azure AD de configuración
Si ya está usando Windows 7 o Windows 8 y su equipo o dispositivo se actualizó a Windows 10, puede unirse a Azure Active Directory (AD Azure) a través del menú de configuración.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Para unirse a Azure AD desde el menú Configuración


1. En el menú **Inicio** , haga clic en el acceso a **configuración** .
2. En **configuración**, seleccione **sistema**->**sobre**->**unirse a Azure AD**.
<center>
![Unirse a Azure AD desde el menú configuración](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Haga clic en **continuar** en la ventana de mensaje Azure AD unirse.
<center>
![Ventana de mensaje de combinación Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Proporcionar sus credenciales de inicio de sesión. Esta experiencia de inicio de sesión incluye todos los pasos necesarios para la autenticación completo. Si forma parte de un inquilino federado, su administrador le proporcionará la experiencia de federación hospedada en su organización.
<center>
![Proporcionar las credenciales de inicio de sesión](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Si su organización ha configurado la autenticación multifactor de Azure para unirse a Azure AD, proporcione el segundo factor antes de continuar.
6. Haga clic en **Aceptar** en la pantalla de **Permitir que este dispositivo administrarse** .
7. Verá el mensaje "el dispositivo ahora está unido a la organización en Azure AD".


## <a name="additional-information"></a>Información adicional
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
