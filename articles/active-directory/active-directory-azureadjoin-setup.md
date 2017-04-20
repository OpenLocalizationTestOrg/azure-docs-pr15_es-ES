<properties
    pageTitle="Configurar combinación de Azure AD para sus usuarios | Microsoft Azure"
    description="Explica cómo los administradores pueden configurar Azure AD unirse de directorio local y el registro de dispositivo."
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>Configuración de Azure AD unirse en su organización

Antes de configurar Azure Active Directory unirse (Azure AD unirse a), debe sincronizar el directorio local de los usuarios a la nube o crear manualmente cuentas administradas en Azure AD.

Instrucciones detalladas para la sincronización de los usuarios locales a Azure AD está cubierto en [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).


Para crear y administrar usuarios en Azure AD manualmente, consulte [administración de usuarios en Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurar el registro de dispositivo
1. Inicie sesión como administrador en el portal de Azure.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la ficha **directorio** , seleccione el directorio.
4. Seleccione la ficha **Configurar** .
5. Vaya a la sección de **dispositivos** .
6. En la pestaña **dispositivos** , defina las siguientes opciones:  
   * **Máximo número de dispositivos por usuario**: seleccione el número máximo de dispositivos que puede tener un usuario en Azure AD.  Si un usuario alcanza esta cuota, no podrá agregar dispositivos adicionales hasta que se quitan uno o varios de sus dispositivos existentes.
   * **REQUERIR MULTIFACTOR AUTH a unirse a dispositivos**: establecer si los usuarios deben proporcionar un segundo factor de autenticación para unirse a su dispositivo para Azure AD. Para obtener más información acerca de la autenticación multifactor de Azure, vea [Introducción a la autenticación multifactor de Azure en la nube](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **Los usuarios pueden dispositivos de AZURE AD combinación**: seleccione los usuarios y grupos que tienen permiso para unirse a dispositivos para Azure AD.
   * **Dispositivos de AZURE de ON administradores adicionales AD unido**: con Azure Premium AD o el conjunto de aplicaciones de movilidad de Enterprise (EMS), puede elegir qué usuarios reciben derechos de administrador local en el dispositivo. Los administradores globales y los propietarios del dispositivo se le concede derechos de administrador local de forma predeterminada.

<center>![Configurar el registro de dispositivo](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Después de configurar Azure AD unirse para sus usuarios, puede conectarse a Azure AD a través de sus dispositivos personales o de la empresa.

Continuación se muestran los tres escenarios que puede usar para permitir a los usuarios a configurar Azure AD unirse:

- Los usuarios unirse a un dispositivo propiedad de la empresa directamente a Azure AD.
- Unión al dominio de los usuarios de un dispositivo propiedad de la empresa en Active Directory local y, a continuación, extender el dispositivo a Azure AD.
- Los usuarios agregar cuentas de trabajo o escuela Windows en un dispositivo personal

## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)
