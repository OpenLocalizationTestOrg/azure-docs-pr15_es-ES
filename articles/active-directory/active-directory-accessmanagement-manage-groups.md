<properties
    pageTitle="Administrar grupos de Azure Active Directory | Microsoft Azure"
    description="Cómo crear y administrar grupos para administrar los usuarios de Azure con Azure Active Directory."
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
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Administrar grupos de Azure Active Directory

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-groups-create-azure-portal.md)
- [Portal de clásico de Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


Una de las características de administración de usuarios de Azure Active Directory (AD Azure) es la capacidad para crear grupos de usuarios. Usar un grupo para realizar tareas de administración, como asignar licencias o permisos a un número de usuarios a la vez. También puede usar los grupos para asignar permisos de acceso a

- Recursos como objetos en el directorio
- Recursos externos en el directorio como aplicaciones SaaS, servicios de Azure, sitios de SharePoint, o local

Además, el propietario de un recurso también puede asignar acceso a un recurso a un grupo de Azure AD que pertenezca a otra persona. Esta asignación concede a los miembros de ese grupo de acceso al recurso. A continuación, el propietario del grupo administra la pertenencia en el grupo. De hecho, el propietario del recurso delega al propietario del grupo el permiso para asignar a los usuarios a sus recursos.

## <a name="how-do-i-create-a-group"></a>¿Cómo creo un grupo?

Según los servicios a los que se ha suscrito su organización, puede crear un grupo mediante uno de estos procedimientos:
- el portal de clásico de Azure
- el portal de la cuenta de Office 365
- el portal de la cuenta de Windows Intune

Describiremos tareas como realizar en el portal de clásico de Azure. Para obtener más información sobre el uso de Azure no portales para administrar el directorio de Azure AD, vea [administrar el directorio de Azure AD](active-directory-administer.md).

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, seleccione el nombre del directorio de su organización.

2. Seleccione la ficha **grupos** .

3. Seleccione **Agregar grupo**.

4. En la ventana **Agregar grupo** , especifique el nombre y la descripción de un grupo.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>¿Cómo agregar o quitar usuarios individuales en un grupo de seguridad?

**Para agregar un usuario individual a un grupo**

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, seleccione el nombre del directorio de su organización.

2. Seleccione la ficha **grupos** .

3. Abra el grupo al que desea agregar a miembros. Abrir la pestaña de **miembros** del grupo seleccionada si ya no se muestra.

4. Seleccione **Agregar a miembros**.

5. En la página **Agregar miembros** , seleccione el nombre de usuario o un grupo al que desea agregar como miembro de este grupo. Asegúrese de que este nombre se agrega al panel **seleccionadas** .


**Para quitar un usuario individual de un grupo**

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, seleccione el nombre del directorio de su organización.

2. Seleccione la ficha **grupos** .

3. Abra el grupo del que desea quitar a miembros.

4. Seleccione la ficha **miembros** , seleccione el nombre del miembro que desee quitar de este grupo y, a continuación, haga clic en **Quitar**.

6. Cuando se le solicite, confirme que desea quitar a este miembro del grupo.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>¿Cómo puedo administrar la pertenencia a un grupo dinámicamente?

En Azure AD, puede configurar una regla simple para determinar qué usuarios deben ser miembros del grupo muy fácilmente. Una regla sencilla es uno que realiza solo una comparación único. Por ejemplo, si un grupo está asignado a una aplicación de SaaS, puede configurar una regla para agregar los usuarios que tienen un puesto de "Representante de ventas". Esta regla, a continuación, conceda acceso a la aplicación de SaaS para todos los usuarios con ese puesto en el directorio.

Cuando se evalúa el sistema de los atributos de un cambio de usuario, todas las reglas de grupo dinámico en un directorio para ver si el cambio de atributo del usuario activaría cualquier grupo agrega o quita. Si un usuario cumple una regla en un grupo, que se hayan agregado como miembro al grupo. Si ya no cumplen la regla de un grupo que están miembro, se eliminan como miembros de ese grupo.

> [AZURE.NOTE] Puede configurar una regla de pertenencia dinámica en grupos de seguridad o grupos de Office 365. Pertenencia a grupos anidados no se admite actualmente de asignación basado en el grupo de aplicaciones.
>
> Dinámicos pertenencias a grupos requieren que se asignará a una licencia de Azure AD Premium
>
> - El administrador que administra la regla en un grupo
> - Todos los miembros del grupo

**Habilitar dinámica pertenencia a un grupo**

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, seleccione el nombre del directorio de su organización.

2. Seleccione la ficha **grupos** y abra el grupo que desea editar.

3. Seleccione la ficha **Configurar** y, a continuación, establezca **Habilitar dinámica pertenencias a grupos** en **Sí**.

4. Configurar una regla simple solo para el grupo de pertenencia cómo dinámica para las funciones de este grupo de control. Asegúrese de que el **Agregar usuarios donde** opción está seleccionada y, a continuación, seleccione una propiedad de usuario de la lista (por ejemplo, departamento, puesto temporal, etcetera),

5. A continuación, seleccione una condición (no es igual a, igual a, no empieza por, empieza por, no contiene, contiene, no coinciden, coincidir).

6. Especifique un valor de comparación para la propiedad del usuario seleccionado.

Para obtener información sobre cómo crear reglas *Avanzadas* (reglas que pueden contener varios comparaciones) para la pertenencia a grupos dinámicos, consulte [uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
