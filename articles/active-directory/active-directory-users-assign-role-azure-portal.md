<properties
    pageTitle="Asignar un usuario a los roles de administrador de vista previa de Azure Active Directory | Microsoft Azure"
    description="Explica cómo cambiar la información de usuario administrativo en Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory-preview"></a>Asignar a un usuario a los roles de administrador de vista previa de Azure Active Directory

En este artículo se explica cómo asignar un rol administrativo a un usuario en la vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Para obtener información sobre cómo agregar nuevos usuarios de su organización, vea [Agregar nuevos usuarios a Azure Active Directory](active-directory-users-create-azure-portal.md). Usuarios agregados no tienen permisos de administrador de forma predeterminada, pero puede asignar roles a ellos en cualquier momento.

## <a name="assign-a-role-to-a-user"></a>Asignar un rol a un usuario

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3.  En el módulo de **usuarios y grupos** , seleccione **todos los usuarios**.

    ![Abrir la todos los módulos de usuarios](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)

4. En el módulo de **usuarios y grupos: todos los usuarios** , seleccione un usuario de la lista.

5. En el módulo para el usuario seleccionado, seleccione **el rol de directorio**y, a continuación, asignar al usuario a una función de la lista de **funciones de directorio** . Para obtener más información acerca de los roles de usuario y administrador, vea [asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md).

      ![Asignación de un usuario a un rol](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

6. Seleccione **Guardar**.


## <a name="whats-next"></a>¿Qué es la siguiente

- [Agregar un usuario](active-directory-users-create-azure-portal.md)
- [Restablecer la contraseña de un usuario en el nuevo portal de Azure](active-directory-users-reset-password-azure-portal.md)
- [Cambiar la información del trabajo de un usuario](active-directory-users-work-info-azure-portal.md)
- [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
- [Eliminar un usuario en su Azure AD](active-directory-users-delete-user-azure-portal.md)
