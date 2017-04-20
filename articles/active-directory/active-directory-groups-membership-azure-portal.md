<properties
    pageTitle="Administrar los grupos de su grupo es un miembro de en la vista previa de Azure Active Directory | Microsoft Azure"
    description="Los grupos pueden contener otros grupos de Azure Active Directory. Aquí le mostramos cómo administrar las pertenencias a grupos."
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


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Administrar los grupos de que su grupo es un miembro de en la vista previa de Azure Active Directory

Los grupos pueden contener otros grupos en la vista previa de Azure Active Directory. [¿Qué es la vista previa?](active-directory-preview-explainer.md) Aquí le mostramos cómo administrar las pertenencias a grupos.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>¿Cómo puedo encontrar los grupos de de que mi grupo es un miembro?

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

  ![Administración de usuarios de apertura](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  En el módulo de **usuarios y grupos** , seleccione **todos los grupos**.

  ![Abrir el módulo de grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. En el módulo de **usuarios y grupos: todos los grupos** , seleccione un grupo.

5. En el *nombre de grupo de *grupo: ** ** módulo, seleccione **agrupar pertenencias **.

  ![Abrir el módulo de miembros del grupo](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Para agregar su grupo como miembro de otro grupo, en el módulo de **grupo - pertenencias a grupos** , seleccione el comando **Agregar** .

7. Seleccione un grupo desde el módulo de **Seleccionar un grupo** y, a continuación, seleccione el botón **Seleccionar** en la parte inferior de la hoja. Puede agregar su grupo a un solo grupo a la vez. El cuadro de **usuario** filtra la presentación basada en la coincidencia de la entrada de cualquier parte de un nombre de usuario o dispositivo. En la casilla, no se aceptarán caracteres comodín.

  ![Agregar la pertenencia a un grupo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Para quitar el grupo como miembro de otro grupo, en el módulo de **grupo - pertenencias a grupos** , seleccione un grupo.

9. En el módulo de ***nombre de grupo*** , seleccione el comando **Quitar** y confirme su elección cuando se le solicite.

  ![quitar el comando de pertenencia](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Cuando termine de modificar la pertenencia a grupos para su grupo, seleccione **Guardar**.


## <a name="additional-information"></a>Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar a miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar reglas dinámicas para usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
