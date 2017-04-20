<properties
    pageTitle="Cambiar el nombre o el logotipo de una aplicación de empresa en vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo cambiar el nombre o logotipo para una aplicación empresarial personalizada en Azure Active Directory"
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

# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory-preview"></a>Cambiar el nombre o el logotipo de una aplicación de empresa en vista previa de Azure Active Directory

Es fácil cambiar el nombre o logotipo para una aplicación empresarial personalizada en vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Debe tener los permisos adecuados para realizar estos cambios. En la vista previa actual, debe ser el creador de la aplicación personalizada.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>¿Cómo puedo cambiar el nombre o el logotipo de una aplicación de empresa?

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2. Seleccione **más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, a continuación, presione **ENTRAR**.

3. En la *directoryname de *Azure Active Directory - ** ** placa (es decir, el Azure AD módulo para el directorio administra), seleccione **Enterprise aplicaciones **.

    ![Aplicaciones empresariales de apertura](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)

4. En el módulo de **aplicaciones empresariales** , seleccione **todas las aplicaciones**. Verá una lista de las aplicaciones que se puede administrar.

5. En el módulo de **aplicaciones empresariales - todas las aplicaciones** , seleccione una aplicación.

6. En el módulo de la ***aplicación*** (es decir, el módulo con el nombre de la aplicación seleccionada en el título), seleccione **Propiedades**.

    ![Seleccionar el comando Propiedades](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)

7. En el ***nombre de aplicación*** **-Propiedades** módulo, seleccione un archivo utilizarlo como un nuevo logotipo, o editar el nombre de la aplicación, o ambos.

    ![Cambiar el logotipo o nameproperties comando de la aplicación](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)

8. Seleccione el comando **Guardar** .

## <a name="next-steps"></a>Pasos siguientes

- [Ver todas mis grupos](active-directory-groups-view-azure-portal.md)
- [Asignar un usuario o grupo a una aplicación de empresa](active-directory-coreapps-assign-user-azure-portal.md)
- [Quitar una asignación de usuario o grupo de una aplicación de empresa](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Deshabilitar los complementos de inicio de sesión de usuario para una aplicación de empresa](active-directory-coreapps-disable-app-azure-portal.md)
