<properties
    pageTitle="Agregar nuevos usuarios a la vista previa de Azure Active Directory | Microsoft Azure"
    description="Se explica cómo agregar nuevos usuarios o cambiar la información de usuario en Active Directory de Azure."
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


# <a name="add-new-users-to-azure-active-directory-preview"></a>Agregar nuevos usuarios a la vista previa de Azure Active Directory

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-users-create-azure-portal.md)
- [Portal de clásico de Azure](active-directory-create-users.md)

En este artículo se explica cómo agregar nuevos usuarios de su organización en la vista previa de Azure Active Direstory (Azure AD). [¿Qué es la vista previa?](active-directory-preview-explainer.md)

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  En el módulo de **usuarios y grupos** , seleccione **todos los usuarios**y, a continuación, seleccione **Agregar**.

    ![Seleccionar el comando Agregar](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Escriba los detalles del usuario, como el **nombre** y el **nombre de usuario**. La parte del nombre de dominio del nombre de usuario debe ser el nombre de dominio predeterminado inicial foo.onmicrosoft.com"nombre de dominio" o un nombre de dominio comprobado, no federado como "contoso.com".

5. Copie o anote en caso contrario, la contraseña de usuario generada para que puedan proporcionar al usuario una vez completado este proceso.

6. Si lo desea, puede abrir y rellene la información en el módulo de **perfil** , en el módulo de **grupos** o el módulo de **rol Directory** para el usuario. Para obtener más información acerca de los roles de usuario y administrador, vea [asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md).

7.  En el módulo de **usuario** , seleccione **crear**.

8. Distribuir segura la contraseña generada al nuevo usuario para que el usuario puede iniciar sesión.

## <a name="whats-next"></a>¿Qué es la siguiente

- [Agregar un usuario externo](active-directory-users-create-external-azure-portal.md)
- [Restablecer la contraseña de un usuario en el nuevo portal de Azure](active-directory-users-reset-password-azure-portal.md)
- [Cambiar la información del trabajo de un usuario](active-directory-users-work-info-azure-portal.md)
- [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
- [Eliminar un usuario en su Azure AD](active-directory-users-delete-user-azure-portal.md)
- [Asignar a un usuario a una función en su Azure AD](active-directory-users-assign-role-azure-portal.md)
