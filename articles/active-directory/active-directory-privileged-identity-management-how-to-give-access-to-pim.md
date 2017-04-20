<properties
   pageTitle="Cómo dar acceso a PIM | Microsoft Azure"
   description="Obtenga información sobre cómo agregar funciones a los usuarios con la extensión de Azure Active Directory con privilegios la administración de identidades para que puedan administrar PIM."
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
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Cómo dar acceso a administrar la administración de identidades con privilegios de Azure AD

El administrador global que permite la administración de identidades con privilegios de Azure AD (PIM) para una organización automáticamente obtener las asignaciones de roles y acceso a PIM. Nadie obtiene acceso de escritura de forma predeterminada, sin embargo, incluidos otros administradores globales. Otros administradores globales, los administradores de seguridad y los lectores de seguridad tienen acceso de solo lectura a Azure AD PIM. Para conceder acceso a PIM, el primer usuario puede asignar a otros usuarios a la función de **Administrador de la función privilegios** . Esta asignación debe realizarse desde dentro de PIM propio y no se puede cambiar mediante PowerShell u otros portales.

> [AZURE.NOTE] Administración de Azure AD PIM requiere AMF de Azure. Puesto que no se pueden registrar cuentas de Microsoft Azure AMF, un usuario que inicia sesión con una cuenta de Microsoft no puede acceder a Azure AD PIM.

Asegúrese de que siempre hay al menos dos usuarios en una función de administrador de la función privilegios, en caso de que un usuario se bloquea o se elimina su cuenta.

## <a name="give-another-user-access-to-manage-pim"></a>Conceder acceso de otro usuario para administrar PIM

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/) y seleccione la aplicación de **administración de identidades de AD con privilegios de Azure** en el panel.
2. Seleccione **Administrar roles con privilegios** > **Administrador de la función privilegios** > **Agregar**.

    ![Agregar administradores de la función privilegios - captura de pantalla][1]

4. En el módulo de agregar usuarios administrado, paso 1 ya está completo. Seleccione el paso 2, **Seleccione usuarios** y busque el usuario que desea agregar.

    ![Seleccione usuarios - captura de pantalla][2]

6. Seleccione el usuario en los resultados de búsqueda y haga clic en **Listo**.
7. Haga clic en **Aceptar** para guardar la selección. El usuario que ha seleccionado aparecerá en la lista de administradores de la función privilegios.

    - Cuando se asigna una nueva función a alguien, se configuran automáticamente como elegibles para activar la función. Si desea que sean permanentes en la función, haga clic en el usuario en la lista. En el menú de información de usuario, seleccione **hacer permiso** .

8. Enviar al usuario un vínculo a [Introducción a la administración de identidades con privilegios de Azure AD](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Quitar derechos de acceso de otro usuario para administrar PIM

Antes de quitar a alguien de la función de administrador de la función privilegios, asegúrese siempre de aún habrá dos usuarios asignados a ella.

1. En el panel de PIM, haga clic en el rol de **Administrador de la función privilegios**.  Se mostrará la lista de usuarios actualmente en esa función.
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**.  Aparecerá un mensaje de confirmación.
4. Haga clic en **Sí** para quitar el usuario de la función.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
