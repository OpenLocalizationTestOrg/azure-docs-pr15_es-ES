
<properties
    pageTitle="Pasos siguientes para uso de los grupos de la administración de acceso | Microsoft Azure"
    description="Cómo avanzada-para administrar grupos de seguridad y cómo usar estos grupos para administrar el acceso a un recurso."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="managing-owners-for-a-group"></a>Administrar propietarios de un grupo
Una vez que el propietario de un recurso ha asignado el acceso a un recurso a un grupo de Azure AD, la pertenencia como miembro del grupo es administrado por el propietario del grupo. El propietario del recurso eficaz delega el permiso para asignar a los usuarios al recurso al propietario del grupo.

## <a name="assigning-group-ownership"></a>Asignación de la propiedad de grupo

**Para agregar un propietario a un grupo**

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, abra el directorio de su organización.

2. Seleccione la ficha **grupos** y, a continuación, abra el grupo que desea agregar a los propietarios.

3. Seleccione **agregar propietarios**.

4. En la página **agregar propietarios** , seleccione el usuario que desea agregar como propietario de este grupo y a continuación, asegúrese de que este nombre se agrega al panel **seleccionadas** .


**Para quitar un propietario de un grupo**

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, abra el directorio de su organización.

2. Seleccione la ficha **grupos** y, a continuación, abra el grupo que desea quitar un propietario de.

4. Seleccione la ficha **propietarios** .

5. Seleccione el propietario de la que desea quitar de este grupo y, a continuación, seleccione **Quitar**.

## <a name="additional-information"></a>Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
