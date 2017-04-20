<properties
    pageTitle="Información general de registro de Active Directory dispositivo Azure | Microsoft Azure"
    description="es la base para escenarios de acceso condicional basado en el dispositivo. Cuando se registra un dispositivo, registro de Azure Active Directory dispositivo aprovisiona el dispositivo con una identidad que se utiliza para autenticar el dispositivo cuando el usuario inicia sesión."
    services="active-directory"
    keywords="registro de dispositivo, activar registro de dispositivo, registro de dispositivo y MDM"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="Markvi"/>

# <a name="get-started-with-azure-active-directory-device-registration"></a>Empezar a trabajar con el registro de dispositivo de Azure Active Directory

Registro Azure de Active Directory dispositivo es la base para escenarios de acceso condicional basado en el dispositivo. Cuando se registra un dispositivo, registro de dispositivo de Azure Active Directory proporciona el dispositivo con una identidad que se utiliza para autenticar el dispositivo cuando el usuario inicia sesión. El dispositivo autenticado y los atributos del dispositivo, pueden utilizarse para aplicar directivas de acceso condicional para las aplicaciones que se hospedan en la nube y locales.

Cuando se combina con una solución de management(MDM) de dispositivos móviles como Microsoft Intune, se actualizan los atributos del dispositivo de Azure Active Directory con información adicional sobre el dispositivo. Esto le permite crear reglas de acceso condicional exigir acceso desde dispositivos para cumplir con los estándares de seguridad y cumplimiento. Para obtener más información sobre la inscripción de dispositivos de Microsoft Intune, vea [dispositivos de inscripción para la administración de Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Escenarios habilitados por registro de dispositivo de Azure Active Directory

Registro Azure de Active Directory dispositivo incluye compatibilidad con iOS, Android y Windows dispositivos. Los escenarios individuales que utilizan registro de Azure AD dispositivo pueden tener más específica requisitos y plataforma de soporte técnico. Estos escenarios son los siguientes:

- **Condicional tener acceso a aplicaciones que sean local hospedado**: puede usar dispositivos registrados con las directivas de acceso para las aplicaciones que se han configurado para usar AD FS con Windows Server 2012 R2. Para obtener más información sobre cómo configurar el acceso condicional para local, consulte [Configurar acceso condicional locales con Azure Active Directory dispositivo de registro](active-directory-conditional-access-on-premises-setup.md).

- **Acceso condicional para las aplicaciones de Office 365 con Microsoft Intune** : los administradores de TI pueden aprovisionar directivas de dispositivo de acceso condicional para proteger recursos corporativos, mientras que al mismo tiempo que permite que los trabajadores de la información en dispositivos compatibles con acceso a los servicios. Para obtener más información, vea [Directivas de dispositivo de acceso condicional para servicios de Office 365](active-directory-conditional-access-device-policies.md).

##<a name="setting-up-azure-active-directory-device-registration"></a>Configurar el registro de dispositivo de Azure Active Directory

Debe habilitar el registro de Azure AD dispositivo en el Portal de Azure para que los dispositivos móviles pueden detectar el servicio buscando conocidos registros DNS. Configure su compañía DNS para que pueden detectar y usar el servicio de dispositivos de Windows 10, Windows 8.1, Windows 7, iOS y Android.
Puede ver y habilitar o deshabilitar dispositivos registrados con el Portal de administrador de Azure Active Directory.

>[AZURE.NOTE]
 Para ver instrucciones más recientes acerca de cómo configurar el registro de dispositivo automática, [cómo configurar el registro automático del dominio de Windows unido dispositivos con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

### <a name="enable-azure-active-directory-device-registration-service"></a>Habilitar el servicio de registro de dispositivo de Azure Active Directory

1. Inicie sesión como administrador en el portal de Microsoft Azure.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la ficha **directorio** , seleccione el directorio.
4. Seleccione la ficha **Configurar** .
5. Desplácese hasta la sección denominada **dispositivos**.
6. Haga clic en **todos los** **usuarios pueden dispositivos de combinación de área de trabajo**.
7. Seleccione el número máximo de dispositivos que desea autorizar por usuario.

>[AZURE.NOTE]
>La inscripción con Microsoft Intune o administración de dispositivos móviles para Office 365 requiere unirse al área de trabajo. Si ha configurado uno de estos servicios, todo está seleccionada y el botón ninguno está deshabilitado.

De forma predeterminada, la autenticación de dos factores no está habilitada para el servicio. Sin embargo, se recomienda la autenticación de dos factores al registrar un dispositivo.

- Antes de que requieren autenticación de dos factores para este servicio, debe configurar un proveedor de autenticación de dos factores en Azure Active Directory y configure las cuentas de usuario para la autenticación multifactor, vea [Agregar autenticación multifactor Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Si está utilizando AD FS con Windows Server 2012 R2, debe configurar un módulo de autenticación de dos factores de AD FS, vea [Usar autenticación multifactor con servicios de federación de Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurar la detección de registro de dispositivo de Azure Active Directory
Dispositivos de Windows 7 y Windows 8.1 descubrir el servicio de registro de dispositivo combinando el nombre de la cuenta de usuario con un nombre de servidor de registro de dispositivo conocido.

Debe crear un registro CNAME DNS que apunte al registro asociado con el servicio de registro de dispositivo de Azure Active Directory. El registro CNAME debe utilizar el enterpriseregistration prefijo conocido seguido por el sufijo UPN usado por las cuentas de usuario en su organización. Si su organización usa varios sufijos UPN, deben crearse múltiples registros CNAME en DNS.

Por ejemplo, si usa dos sufijos UPN en su organización denominada @contoso.com y @region.contoso.com, creará los siguientes registros DNS.

| Entrada                                     | Tipo  | Dirección                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | CNAME | enterpriseregistration.Windows.NET |
| enterpriseregistration.Region.contoso.com | CNAME | enterpriseregistration.Windows.NET |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Ver y administrar objetos de dispositivo de Azure Active Directory
1. Desde el portal de administrador de Azure, puede ver, bloquear y desbloquear dispositivos. Un dispositivo bloqueado ya no tendrá acceso a las aplicaciones que se haya configurado para permitir que solo los dispositivos registrados.
2. Inicie sesión en el Portal de Microsoft Azure como administrador.
3. En el panel izquierdo, seleccione **Active Directory**.
4. Seleccione el directorio.
5. Seleccione la ficha **usuarios** . A continuación, seleccione un usuario para ver sus dispositivos
6. Seleccione la pestaña **dispositivos** .
7. Seleccione **Dispositivos registrados** desde el menú desplegable.
8. Aquí puede ver, bloquear o desbloquear los dispositivos registrados de los usuarios.

## <a name="additional-topics"></a>Temas adicionales

Puede registrar los dispositivos de Windows 7 y Windows 8.1 dominio unido con el registro de Azure AD dispositivo. Los temas siguientes se proporciona más información sobre los requisitos previos y los pasos necesarios para configurar el registro de dispositivo en dispositivos de Windows 7 y Windows 8.1.

- [Registro de dispositivo automática con Azure Active Directory para dispositivos de dominio de Windows](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar el registro del dispositivo automática para dispositivos de pertenencia a un dominio de Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurar el registro de dispositivo automáticas para dispositivos de pertenencia a un dominio de Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registro de dispositivo automática con dispositivos de dominio de Azure Active Directory para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
