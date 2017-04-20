<properties
    pageTitle="Configurar un dispositivo con Azure AD durante la instalación de nuevo | Microsoft Azure"
    description="Tema que explica cómo los usuarios pueden configurar Azure AD unirse durante su primera experiencia de ejecución."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Configurar un dispositivo con Azure AD durante la instalación de nuevo

En Windows 10, los usuarios pueden unirse a sus dispositivos a Azure Active Directory (AD Azure) en la experiencia de primera ejecución (FRX). Esto permite a las organizaciones distribuir dispositivos cubetas a sus empleados o los estudiantes o hacerle elegir sus propios dispositivos (CYOD).
Si las ediciones de Windows 10 Professional o Windows 10 Enterprise está instalado en un dispositivo, la experiencia de valores predeterminados para el proceso de configuración de dispositivos de empresa.

## <a name="to-join-a-device-to-azure-ad"></a>Para unirse a un dispositivo a Azure AD


1. Al activar el nuevo dispositivo e iniciar el proceso de instalación, verá el mensaje **Preparativos** . Siga las indicaciones para configurar el dispositivo.
2. Iniciar personalizando su configuración regional e idioma. A continuación, acepte los términos de licencia del Software de Microsoft.
![Personalizar para su región](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Seleccione la red que desea usar para conectarse a Internet.
4. Seleccione si está usando un dispositivo personal o una propiedad de la empresa. Si es propietario de la empresa, haga clic en **este dispositivo pertenezca a mi organización**. Se inicia la experiencia de Azure AD unirse. A continuación se muestra una pantalla que verá si está usando Windows 10 Professional.
<center>
![Propietario de la pantalla de PC](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Escriba las credenciales que le proporcionó su organización.
<center>
![Pantalla de inicio de sesión](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Después de escribir el nombre de usuario, se encuentra un inquilino coincidente en Azure AD. Si se encuentra en un dominio federado, se le redirigirá a su servidor de servicio de Token seguro (STS) local, por ejemplo, Active Directory Federation Services (AD FS).
7. Si es un usuario en un dominio no federado, escriba sus credenciales directamente en la página de Azure AD hospedado. Si se ha configurado la marca de la empresa, que se ve el logotipo de su organización y admite texto.
8.  Se le solicitará para un desafío de autenticación multifactor. Este desafío es configurable por un administrador de TI.
9.  Azure AD comprueba si este dispositivo requiere inscripción en administración de dispositivos móviles.
10. Windows registra el dispositivo en el directorio de la organización en Azure AD e inscribe en administración de dispositivos móviles, si corresponde.
11. Si es un usuario administrado, Windows le lleva a la versión de escritorio a través del proceso de inicio de sesión automático.
12. Si es un usuario federado, se dirigen a la pantalla de inicio de sesión de Windows para escribir sus credenciales.

> [AZURE.NOTE] Unirse a un dominio de Active Directory de Windows Server local en la experiencia de fuera de la caja de Windows no es compatible. Por tanto, si va a unir un equipo a un dominio, debe activar el vínculo **configuración de Windows con una cuenta local** en su lugar. A continuación, puede unirse al dominio desde la configuración en el equipo como que ha hecho antes.

## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)
