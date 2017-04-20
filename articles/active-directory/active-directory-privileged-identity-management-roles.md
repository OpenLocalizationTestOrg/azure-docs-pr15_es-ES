<properties
   pageTitle="Funciones en PIM | Microsoft Azure"
   description="Obtenga información sobre qué funciones se utilizan para identidades con privilegios con la extensión de administración de identidades con privilegios de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Funciones de Azure AD con privilegios de administración de identidades

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Puede asignar a los usuarios de su organización a las diferentes funciones administrativas en Azure AD. Estas asignaciones de funciones controlan qué tareas, como agregar o quitar usuarios o cambiar la configuración del servicio, los usuarios pueden realizar en Azure AD, Office 365 y otros servicios en línea de Microsoft y aplicaciones conectadas.  

Un administrador global puede actualizar los usuarios que están **permanentemente** asignados a roles en Azure AD, con los cmdlets de PowerShell como `Add-MsolRoleMember` y `Remove-MsolRoleMember`, o a través del portal clásico como se describe en [asignar roles de administrador de Azure Active Directory](active-directory-assign-admin-roles.md).

Administración de identidades de Azure AD privilegios (PIM) administra las directivas para el acceso para los usuarios con privilegios de Azure AD. PIM asigna a los usuarios a una o varias funciones de Azure AD y se puede asignar a alguien a ser permanentemente de la función o elegible para la función. Cuando un usuario permanentemente está asignado a una función o una asignación de roles válida, activa, a continuación, pueden administrar Azure Active Directory, Office 365 y otras aplicaciones con los permisos asignados a sus funciones.

No hay ninguna diferencia en el acceso de alguien con permanente frente a una asignación de roles elegible. La única diferencia es que algunas personas no es necesario que el acceso todo el tiempo. Que se realizan elegibles para la función y pueden activar y desactivar cada vez que necesitan.

## <a name="roles-managed-in-pim"></a>Roles administrados en PIM

La administración de identidades con privilegios le permite asignar a usuarios a los roles de administrador comunes, incluidos:


- **Administrador global** (también conocido como administrador de la compañía) tiene acceso a todas las funciones administrativas. Puede tener más de un administrador global de su organización. La persona que inscribe para comprar Office 365 automáticamente pasa a ser un administrador global.
- **Administrador de la función privilegios** administra Azure AD PIM y actualiza las asignaciones de roles para otros usuarios.  
- **Administrador de facturación:** realiza compras, administra suscripciones, administra vales de soporte técnico y supervisa el estado del servicio.
- **Administrador de contraseñas** restablece las contraseñas, administra solicitudes de servicio y supervisa el estado del servicio. Los administradores de contraseñas están limitados a restablecer las contraseñas de los usuarios.
- **Administrador del servicio** administra solicitudes de servicio y monitores de estado del servicio.

  > [AZURE.NOTE] Si está utilizando Office 365, a continuación, antes de asignar el rol de administrador del servicio a un usuario, primero asignar al usuario permisos administrativos a un servicio, como Exchange Online.

- **Administrador del usuario** restablece las contraseñas, supervisa el estado del servicio y administra las solicitudes de servicio, cuentas de usuarios y grupos de usuarios. El Administrador de administración de usuario no puede eliminar un administrador global, crear otros roles de administrador o restablecer las contraseñas de facturación, globales y administradores del servicio.
- **Administrador de Exchange** tiene acceso administrativo a Exchange Online a través del centro de administración de Exchange (cae) y puede realizar casi cualquier tarea en Exchange Online.
- **Administrador de SharePoint** tiene acceso administrativo a SharePoint Online a través del centro de administración de SharePoint Online y puede realizar casi cualquier tarea en SharePoint Online.
- **Skype de administrador de empresa** tiene acceso administrativo a Skype empresarial a través de la Skype centro de administración de la empresa y puede realizar casi cualquier tarea en Skype empresarial Online.

Lea estos artículos para obtener más detalles sobre cómo [asignar roles de administrador en Azure AD](active-directory-assign-admin-roles.md) y [asignar roles de administrador en Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Desde PIM, puede [asignar estas funciones a un usuario](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que el usuario puede [activar la función cuando sea necesario](active-directory-privileged-identity-management-how-to-activate-role.md).

Si desea conceder acceso de otro usuario para administrar en PIM propio, los roles que PIM requiere que el usuario se describen en [cómo dar acceso a PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Funciones no administradas en PIM

Roles en Exchange Online o SharePoint Online, excepto los mencionados arriba, no se representan en Azure AD y lo que no son visibles en PIM. Para obtener más información sobre cómo cambiar las asignaciones de roles poseen estos servicios de Office 365, consulte [permisos en Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Suscripciones de Azure y grupos de recursos también no se representan en Azure AD. Para administrar suscripciones Azure, consulte [cómo agregar o cambiar los roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md) y para obtener más información sobre RBAC Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Roles de usuario e iniciar sesión en
Para algunas aplicaciones y servicios de Microsoft, la asignación de un usuario a un rol no sean suficiente para permitir que el usuario será un administrador.

Acceder al portal clásico Azure requiere el usuario que sea administrador del servicio o administrador de colaboración en una suscripción de Azure, incluso si el usuario no es necesario administrar las suscripciones de Azure.  Por ejemplo, para administrar la configuración de Azure AD en el portal de clásico, un usuario debe ser un administrador global de Azure AD y un administrador de la suscripción en una suscripción de Azure.  Para obtener información sobre cómo agregar usuarios a Azure suscripciones, vea [cómo agregar o cambiar los roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md).

Acceso a servicios en línea de Microsoft puede requerir que el usuario también se pueden asignar una licencia para poder abrir el portal del servicio o realizar tareas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Asignar una licencia a un usuario en Azure AD

1. Inicie sesión en la [portal clásico Azure] (http://manage.windowsazure.com) con una cuenta de administrador global o de una cuenta de administrador de compañeros.
2. Seleccione **Todos los elementos** desde el menú principal.
3. Seleccione el directorio que desea trabajar con y que tiene licencias asociadas.
4. Seleccione **licencias**. Aparecerá la lista de licencias disponibles.
5. Seleccione el plan de licencia que contiene las licencias que desea distribuir.
6. Seleccione **asignar a los usuarios**.
7. Seleccione el usuario que desea asignar una licencia.
8. Haga clic en el botón **asignar** .  El usuario puede ahora inicie sesión en Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
