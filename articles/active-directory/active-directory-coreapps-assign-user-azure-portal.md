<properties
    pageTitle="Asignar un usuario o grupo a una aplicación de empresa en vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo seleccionar una aplicación de empresa para asignar un usuario o grupo a ella de Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Asignar un usuario o grupo a una aplicación de empresa en vista previa de Azure Active Directory

Es fácil asignar un usuario o grupo a sus aplicaciones empresariales de vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Debe tener los permisos adecuados para administrar la aplicación de empresa. En la vista previa actual, debe ser administrador global para el directorio.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>¿Cómo asignar acceso de usuario a una aplicación de empresa?

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2. Seleccione **más servicios**, escriba Azure Active Directory en el cuadro de texto y, a continuación, presione **ENTRAR**.

3. En la *directoryname de *Azure Active Directory - ** ** placa (es decir, el Azure AD módulo para el directorio administra), seleccione **Enterprise aplicaciones **.

    ![Aplicaciones empresariales de apertura](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. En el módulo de **aplicaciones empresariales** , seleccione **todas las aplicaciones**. Verá una lista de las aplicaciones que se puede administrar.

5. En el módulo de **aplicaciones empresariales - todas las aplicaciones** , seleccione una aplicación.

6. En el módulo de la ***aplicación*** (es decir, el módulo con el nombre de la aplicación seleccionada en el título), seleccione **usuarios y grupos**.

    ![Seleccionar el comando de todas las aplicaciones](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. En el ***nombre de aplicación*** **-User & asignación a un grupo** módulo, seleccione el comando **Agregar** .

8. En el módulo de **Agregar asignación** , seleccione **usuarios y grupos**.

    ![Asignar un usuario o grupo a la aplicación](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. En el módulo de **usuarios y grupos** , seleccione uno o más usuarios o grupos de la lista y, a continuación, seleccione el botón **Seleccionar** en la parte inferior de la hoja.

10. En el módulo de **Agregar asignación** , seleccione el **rol**. A continuación, en el módulo de **Seleccionar función** , seleccione una función que se aplicará a los usuarios o grupos seleccionados y, a continuación, seleccione el botón **Aceptar** en la parte inferior de la hoja.

11. En el módulo de **Agregar asignación** , seleccione el botón **asignar** en la parte inferior de la hoja. Los usuarios asignados o grupos tendrán los permisos definidos por el rol seleccionado para esta aplicación de empresa.

## <a name="next-steps"></a>Pasos siguientes

- [Ver todas mis grupos](active-directory-groups-view-azure-portal.md)
- [Quitar una asignación de usuario o grupo de una aplicación de empresa](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Deshabilitar los complementos de inicio de sesión de usuario para una aplicación de empresa](active-directory-coreapps-disable-app-azure-portal.md)
- [Cambiar el nombre o el logotipo de una aplicación de empresa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
