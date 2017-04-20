<properties
    pageTitle="Crear un grupo nuevo en vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo crear un grupo de Azure Active Directory y agregar usuarios (miembros) al grupo"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Crear un grupo nuevo en vista previa de Azure Active Directory

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-groups-create-azure-portal.md)
- [Portal de clásico de Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

En este artículo se explica cómo crear y rellenar un grupo nuevo en la vista previa de Azure Active Directory (AD Azure). [¿Qué es la vista previa?](active-directory-preview-explainer.md) Usar un grupo para realizar tareas de administración, como asignar licencias o permisos a un número de usuarios o dispositivos a la vez.

## <a name="how-do-i-create-a-group"></a>¿Cómo creo un grupo?

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2. Seleccione **más servicios**, escriba **usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

  ![Administración de usuarios de apertura](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. En el módulo de **usuarios y grupos** , seleccione **todos los grupos**.

  ![Abrir el módulo de grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. En el módulo de **usuarios y grupos: todos los grupos** , seleccione el comando **Agregar** .

  ![Seleccionar el comando Agregar](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. En el módulo de **grupo** , agregue un nombre y una descripción para el grupo.

6. Para seleccionar los miembros para agregar al grupo, seleccione **asignado** en el cuadro **tipo de suscripción** y, a continuación, seleccione **los miembros**. Para obtener más información sobre cómo administrar la pertenencia a un grupo dinámicamente, vea [uso de atributos para crear reglas avanzadas para la pertenencia a grupos](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Seleccionar miembros para agregar](./media/active-directory-groups-create-azure-portal/select-members.png)

5. En el módulo de **miembros** , seleccione uno o más usuarios o dispositivos para agregar al grupo y seleccione el botón **Seleccionar** en la parte inferior del módulo para agregarlos al grupo. El cuadro de **usuario** filtra la presentación basada en la coincidencia de la entrada de cualquier parte de un nombre de usuario o dispositivo. En la casilla, no se aceptarán caracteres comodín.

6. Cuando haya terminado de agregar miembros al grupo, seleccione **crear** en el módulo de **grupo** .    

  ![Crear la confirmación de grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar a miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar la pertenencia a un grupo](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas para usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
