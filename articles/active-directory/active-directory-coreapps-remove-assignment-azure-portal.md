<properties
    pageTitle="Quitar una asignación de usuario o grupo de una aplicación de empresa en vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo quitar la asignación de acceso de un usuario o grupo de una aplicación de empresa en Azure Active Directory"
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
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Quitar un usuario o una asignación a un grupo de una aplicación de empresa en vista previa de Azure Active Directory

Es fácil quitar un usuario o un grupo desde el que se asigna el acceso a uno de sus aplicaciones empresariales de vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Debe tener los permisos adecuados para administrar la aplicación de empresa. En la vista previa actual, debe ser administrador global para el directorio.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>¿Cómo puedo quitar un usuario o una asignación a un grupo?

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2. Seleccione **más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, a continuación, presione **ENTRAR**.

3. En la *directoryname de *Azure Active Directory - ** ** placa (es decir, el Azure AD módulo para el directorio administra), seleccione **Enterprise aplicaciones **.

    ![Aplicaciones empresariales de apertura](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. En el módulo de **aplicaciones empresariales** , seleccione **todas las aplicaciones**. Verá una lista de las aplicaciones que se puede administrar.

5. En el módulo de **aplicaciones empresariales - todas las aplicaciones** , seleccione una aplicación.

6. En el módulo de la ***aplicación*** (es decir, el módulo con el nombre de la aplicación seleccionada en el título), seleccione **usuarios y grupos**.

    ![Seleccione los usuarios o grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. En el ***nombre de aplicación*** **-User & asignación a un grupo** módulo, seleccione uno de varios usuarios o grupos y, a continuación, seleccione el comando **Quitar** . Confirme la decisión en el símbolo del sistema.

    ![Seleccionar el comando Quitar](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Pasos siguientes

- [Ver todas mis grupos](active-directory-groups-view-azure-portal.md)
- [Asignar un usuario o grupo a una aplicación de empresa](active-directory-coreapps-assign-user-azure-portal.md)
- [Deshabilitar los complementos de inicio de sesión de usuario para una aplicación de empresa](active-directory-coreapps-disable-app-azure-portal.md)
- [Cambiar el nombre o el logotipo de una aplicación de empresa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
