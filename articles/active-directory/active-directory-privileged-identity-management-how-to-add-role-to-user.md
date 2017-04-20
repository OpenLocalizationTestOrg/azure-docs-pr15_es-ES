<properties
   pageTitle="Cómo agregar o quitar un rol de usuario | Microsoft Azure"
   description="Obtenga información sobre cómo agregar funciones a identidades con privilegios con la aplicación de Azure Active Directory con privilegios la administración de identidades."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD con privilegios la administración de identidades: Cómo agregar o quitar una función de usuario

Con Azure Active Directory (AD), un administrador global (o administrador de la compañía) puede actualizar los usuarios que están **permanentemente** asignados a roles en Azure AD. Esto se hace con los cmdlets de PowerShell como `Add-MsolRoleMember` y `Remove-MsolRoleMember`. O bien, puede usar el portal de clásico Azure tal como se describe en [asignar roles de administrador de Azure Active Directory](active-directory-assign-admin-roles.md).

La aplicación de la administración de identidades con privilegios de Azure AD permite a los administradores de la función privilegios hacer asignaciones de roles permanente. Además, los administradores de función privilegios pueden hacer los usuarios **elegible** para roles de administrador. Un administrador elegible puede activar la función cuando lo necesiten y, a continuación, sus permisos expiren una vez que haya terminado.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Administrar roles con PIM en el portal de Azure

En la organización, puede asignar a usuarios a funciones administrativas diferentes en Azure AD, aplicaciones y servicios de Office 365 y otros de Microsoft.  Encontrará más detalles sobre las funciones disponibles en [Roles en Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Para agregar o quitar un usuario de una función de la administración de identidades con privilegios, mostrar el panel PIM. A continuación, haga clic el botón **usuarios en Roles de administrador** , o seleccione un rol específico (como administrador Global) de la tabla de funciones.

> [AZURE.NOTE] Si aún no ha habilitado PIM en el portal de Azure, vaya a [Introducción a Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) para obtener información detallada.

Si desea que otro usuario acceda a PIM propio, los roles que PIM requiere que el usuario se describen en [cómo dar acceso a PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Agregar un usuario a una función

1. En el [portal de Azure](https://portal.azure.com/), seleccione el icono de **la administración de identidades con privilegios de Azure AD** en el panel.
2. Seleccione **Administrar roles con privilegios**.
3. En la tabla de la **función de resumen** , seleccione el rol que desee administrar.
4. En el módulo de rol, seleccione **Agregar**.
5. Haga clic en **Seleccionar usuarios** y busque el usuario en el módulo **Seleccionar usuarios** .  
6. Seleccione el usuario de la lista de resultados de búsqueda y haga clic en **Listo**.
4. Haga clic en **Aceptar** para guardar la selección. El usuario que ha seleccionado aparecerá en la lista como elegible para la función.

> [AZURE.NOTE]
>Nuevos usuarios en una función solo son válidos para la función predeterminada. Si desea que la función permanente, haga clic en el usuario en la lista. Aparecerá la información del usuario en un nuevo módulo. En el menú de información de usuario, seleccione **hacer permiso** .  
>Si un usuario no se puede registrar para la autenticación de varios factores de Azure (AMF) o está utilizando una cuenta de Microsoft (normalmente @outlook.com), debe hacerlas permanentes en todas sus funciones. Los administradores pueden le pedirá que registrarse en AMF durante la activación.

Ahora que el usuario es elegible para una función, hacerle saber que puede activarlo según las instrucciones de [cómo activar o desactivar un rol](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Quitar un usuario de una función

Puede quitar usuarios de las asignaciones de roles válida, pero asegúrese de que siempre hay al menos un usuario que sea un administrador global permanente.

Siga estos pasos para quitar un usuario específico de una función:

1. Vaya a la función en la lista función, seleccione una función en el panel de Azure AD PIM o haciendo clic en el botón de **usuarios en Roles de administrador** .
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**. Un mensaje le pida que confirme.
4. Haga clic en **Sí** para quitar el rol de usuario.

Si no está seguro de qué usuarios necesitan sus asignaciones de roles, a continuación, puede [iniciar una revisión de access para la función](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
