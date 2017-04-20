

<properties
    pageTitle="Unirse a un dispositivo personal para su empresa | Microsoft Azure"
    description="Se explica cómo los usuarios pueden registrar sus dispositivos de Windows 10 personales a su red corporativa y proporciona los pasos de implementación para un escenario BYOD."
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

# <a name="join-a-personal-device-to-your-organization"></a>Unirse a un dispositivo personal para su organización

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Para unirse a un dispositivo de Windows 10 para su organización

1.  En el menú **Inicio** , seleccione **configuración**.
2.  Seleccione **cuentas**y, a continuación, haga clic en **su cuenta**.
3.  Haga clic en **Agregar trabajo o escuela**y, a continuación, escriba en su cuenta profesional.
4.  En la página de inicio de sesión para su organización, escriba su nombre de usuario y contraseña y, a continuación, haga clic en **Aceptar**.
5.  Se le pedirá para un desafío de autenticación multifactor. (Este desafío es configurable por un administrador de TI).
6.  Azure Active Directory (AD Azure) comprueba si el dispositivo requiere inscripción de administración de dispositivos móviles.
7.  Windows registra el dispositivo en el directorio de la organización en Azure AD e inscribe en administración de dispositivos móviles, si corresponde.
8.  Si es un usuario administrado, Windows le lleva a la versión de escritorio mediante el automático inicio de sesión.
9.  Si es un usuario federado, se le dirigirá a una pantalla de inicio de sesión de Windows para escribir sus credenciales.

## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)
