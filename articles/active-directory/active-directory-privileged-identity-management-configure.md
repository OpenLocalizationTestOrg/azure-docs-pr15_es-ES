<properties
    pageTitle="Administración de identidades con privilegios de AD Azure | Microsoft Azure"
    description="Un tema que explica qué es la administración de identidades con privilegios de Azure AD y cómo usar PIM para mejorar la seguridad de la nube."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Administración de identidades de Azure con privilegios de AD

Con la administración de identidades con privilegios de Azure Active Directory (AD), puede administrar, controlar y supervisar el acceso de la organización. Esto incluye el acceso a los recursos en Azure AD y otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.  

> [AZURE.NOTE] La administración de identidades con privilegios solo está disponible con la edición Premium P2 de Azure Active Directory. Para obtener más información, vea [ediciones de Azure Active Directory](active-directory-editions.md).

Las organizaciones desean minimizar el número de personas que tienen acceso a proteger la información o recursos, ya reduce la probabilidad de que un usuario malintencionado obtener acceso. Sin embargo, los usuarios aún necesitan para llevar a cabo operaciones con privilegios en las aplicaciones de Azure, Office 365 o SaaS. Las organizaciones dar acceso de usuarios con privilegios de Azure AD sin supervisión qué hacen los usuarios con los privilegios de administrador. Administración de identidades de AD privilegios Azure ayuda a resolver este riesgo.  

Administración de identidades de AD privilegios Azure le ayuda a:  

- Ver los usuarios que sean administradores de Azure AD
- Habilitar a petición "en el momento" acceso administrativo a Microsoft Online Services, como Office 365 y Intune
- Obtener informes sobre el historial de acceso de administrador y los cambios en las asignaciones de administrador
- Recibir alertas sobre el acceso a una función privilegios

Azure AD de administración de identidades con privilegios pueden administrar los integrados funciones organizativas de Azure AD, incluidos:  

- Administrador global
- Administrador de facturación
- Administrador del servicio  
- Administrador de usuarios
- Administrador de contraseñas

## <a name="just-in-time-administrator-access"></a>En acceso de administrador de tiempo

Tradicionalmente, podría asignar a un usuario a un rol de administrador a través del portal clásica Azure o Windows PowerShell. Como resultado, ese usuario pasa a ser un **Administrador permanente**, siempre activo en el rol asignado. Administración de identidades de AD privilegios Azure presenta el concepto de **administración válida**. Los administradores pueden debería usuarios que necesiten acceso con privilegios ahora y, a continuación, pero no todos los días. El rol está inactivo hasta que el usuario necesita tener acceso, completar un proceso de activación y convertirse en administrador activo durante un período de tiempo predeterminado.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Habilitar la administración de identidades con privilegios para el directorio

Puede comenzar a usar la administración de identidades con privilegios de Azure AD en el [portal de Azure](https://portal.azure.com/).

>[AZURE.NOTE] Debe ser administrador global con una cuenta de la organización (por ejemplo, @yourdomain.com), no es una cuenta de Microsoft (por ejemplo, @outlook.com), para habilitar la administración de identidades con privilegios de Azure AD para un directorio.

1. Inicie sesión como administrador global del directorio en el [portal de Azure](https://portal.azure.com/) .
2. Si su organización tiene más de un directorio, seleccione el nombre de usuario en la esquina superior derecha del portal de Azure. Seleccione el directorio donde va a utilizar la administración de identidades con privilegios de Azure AD.
3. Seleccione **más servicios** y use el cuadro de texto de filtro para buscar **la administración de identidades con privilegios de Azure AD**.
4. Compruebe el **Pin al panel** y, a continuación, haga clic en **crear**. Se abre la aplicación de la administración de identidades con privilegios.

Si es la primera persona que use la administración de identidades con privilegios de Azure AD en el directorio, el [Asistente para seguridad](active-directory-privileged-identity-management-security-wizard.md) le guiará a través de la experiencia de asignación inicial. Después de que se convierte automáticamente en el primer **Administrador de seguridad** y el **Administrador de la función privilegios** del directorio.

Un administrador de la función privilegios puede administrar el acceso de otros administradores. Puede [dar a otros usuarios la capacidad de administrar en PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Panel de administración de identidades con privilegios

Administrador de identidades de AD privilegios Azure proporciona un panel que le ofrece información importante como:

- Alertas que destacan oportunidades para mejorar la seguridad
- El número de usuarios que están asignados a cada función con privilegios  
- El número de administradores elegibles y permanentes
- Revisa un acceso continuo

![Panel PIM - captura de pantalla][2]

## <a name="privileged-role-management"></a>Administración de funciones con privilegios

Con la administración de identidades con privilegios de Azure AD, puede administrar los administradores agregando o quitando los administradores permanentes o elegibles para cada rol.

![Agregar o quitar administradores de PIM - captura de pantalla][3]

## <a name="configure-the-role-activation-settings"></a>Configurar las opciones de activación de función

Uso de la [configuración de la función](active-directory-privileged-identity-management-how-to-change-default-settings.md) puede configurar las propiedades de activación de función elegible incluidos:

- La duración del período de activación de función
- La notificación de activación de función
- La información de que un usuario debe proporcionar durante el proceso de activación de función  

![Captura de pantalla de configuración - activación administrador - PIM][4]

Tenga en cuenta que en la imagen, los botones para la **Autenticación multifactor** están deshabilitados. Para determinadas, altamente con privilegios roles, necesitamos AMF para mayor protección.

## <a name="role-activation"></a>Activación de la función  

Para [activar un rol](active-directory-privileged-identity-management-how-to-activate-role.md), un administrador elegible solicita un límite de tiempo de "activación" de la función. Puede solicitar la activación mediante la opción **Activar mi función** en la administración de identidades con privilegios de Azure AD.

Que desea activar un rol de administrador debe iniciar la administración de identidades con privilegios de Azure AD en el portal de Azure.

Activación de la función es personalizable. En la configuración de PIM, puede determinar la longitud de la activación y el administrador debe proporcionar para activar la función de información.

![Activación de función de solicitud de administrador PIM - captura de pantalla][5]

## <a name="review-role-activity"></a>Revisar la actividad de función

Hay dos formas de realizar un seguimiento de cómo los empleados y administradores están usando funciones con privilegios. La primera opción está utilizando el [historial de auditoría](active-directory-privileged-identity-management-how-to-use-audit-log.md). El historial de auditoría registra el control de cambios en las asignaciones de roles con privilegios e historial de activación de función.

![Historial de activación de PIM: captura de pantalla][6]

La segunda opción es configurar regular [access revisa](active-directory-privileged-identity-management-how-to-start-security-review.md). Estas revisiones de access pueden realizar y asignadas a revisor (por ejemplo, un administrador de grupo) o pueden revisar los empleados a sí mismos. Esta es la mejor manera de supervisar que aún requiere acceso y que ya no funciona.


## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
