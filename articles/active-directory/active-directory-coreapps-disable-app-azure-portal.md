<properties
    pageTitle="Deshabilitar los complementos de inicio de sesión de usuario para una aplicación de empresa en vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo deshabilitar una aplicación empresarial para que ningún usuario puede iniciar sesión en ella de Azure Active Directory"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Deshabilitar los complementos de inicio de sesión de usuario para una aplicación de empresa en vista previa de Azure Active Directory

Es fácil deshabilitar una aplicación empresarial para que ningún usuario puede iniciar sesión en él en la vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Debe tener los permisos adecuados para administrar la aplicación de empresa. En la vista previa actual, debe ser administrador global para el directorio.

## <a name="how-do-i-disable-user-sign-ins"></a>¿Cómo desactivo los inicios de sesión de usuario?

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2. Seleccione **más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, a continuación, presione **ENTRAR**.

3. En la *directoryname de *Azure Active Directory - ** ** placa (es decir, el Azure AD módulo para el directorio administra), seleccione **Enterprise aplicaciones **.

    ![Aplicaciones empresariales de apertura](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. En el módulo de **aplicaciones empresariales** , seleccione **todas las aplicaciones**. Vea una lista de las aplicaciones que se puede administrar.

5. En el módulo de **aplicaciones empresariales - todas las aplicaciones** , seleccione una aplicación.

6. En el módulo de la ***aplicación*** (es decir, el módulo con el nombre de la aplicación seleccionada en el título), seleccione **Propiedades**.

    ![Seleccionar el comando de todas las aplicaciones](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. En el ***nombre de aplicación*** **-Propiedades** módulo, seleccione **No** para **habilitada para que los usuarios de inicio de sesión?**.

8. Seleccione el comando **Guardar** .

## <a name="next-steps"></a>Pasos siguientes

- [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
- [Asignar un usuario o grupo a una aplicación de empresa](active-directory-coreapps-assign-user-azure-portal.md)
- [Quitar una asignación de usuario o grupo de una aplicación de empresa](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Cambiar el nombre o el logotipo de una aplicación de empresa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
