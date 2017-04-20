<properties
    pageTitle="Administrar los miembros de un grupo en la vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo los usuarios y dispositivos que sean miembros de un grupo de Azure Active Directory"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Administrar a los miembros de un grupo en la vista previa de Azure Active Directory

En este artículo se explica cómo administrar a los miembros de un grupo en la vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>¿Cómo encontrar a los miembros y administrarlos?

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

  ![Administración de usuarios de apertura](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  En el módulo de **usuarios y grupos** , seleccione **todos los grupos**.

  ![Abrir el módulo de grupos](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. En el módulo de **usuarios y grupos: todos los grupos** , seleccione un grupo.

5. En el de *nombre de grupo de *grupo: ** ** módulo, seleccione **los miembros **.

  ![Abrir el módulo de miembros](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Para agregar a miembros al grupo, en el módulo de **grupo - miembros** , seleccione **Agregar miembros**.

  ![Agregar comando de miembros](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. En el módulo de **miembros** , seleccione uno o más usuarios o dispositivos para agregar al grupo y seleccione el botón **Seleccionar** en la parte inferior del módulo para agregarlos al grupo. El cuadro de **usuario** filtra la presentación basada en la coincidencia de la entrada de cualquier parte de un nombre de usuario o dispositivo. En la casilla, no se aceptarán caracteres comodín.

8. Para quitar a miembros del grupo, en el módulo de **grupo - miembros** , seleccione a un miembro.

9. En el módulo de ***nombre de usuario registrado*** , seleccione el comando **Quitar** y confirme su elección cuando se le solicite.

  ![quitar el comando de miembros](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Cuando termine de modificar los miembros del grupo, seleccione **Guardar**.


## <a name="additional-information"></a>Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a un grupo](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas para usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
