<properties
    pageTitle="Dedicado grupos de Azure Active Directory | Microsoft Azure"
    description="Información general sobre cómo dedicada grupos de trabajo en Azure Active Directory y cómo se crean."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Grupos dedicados en Azure Active Directory

En Azure Active Directory (AD Azure), la característica de grupos dedicados automáticamente crea y rellena la pertenencia a grupos de Azure AD predefinido. Los miembros de grupos dedicados no se pueden agregar o quitar usando el portal clásica Azure, los cmdlets de Windows PowerShell, o mediante programación.

>[AZURE.NOTE] Grupos dedicados requieren que se asigne una licencia de Azure AD Premium para
>- el administrador que administra la regla en un grupo
>- todos los usuarios que se seleccionan la regla para ser miembro del grupo

**Para habilitar a grupos dedicados**

1. En el [portal de clásica Azure](https://manage.windowsazure.com), seleccione **Active Directory**y, a continuación, abra el directorio de su organización.

2. Seleccione la ficha **grupos** y, a continuación, abra el grupo que desea editar.

3. Seleccione la ficha **Configurar** y después establezca **Habilitar grupos dedicado** a **Sí**.

Cuando el conmutador habilitar grupos dedicados se establece en **Sí**, puede habilitar aún más el directorio crear automáticamente el grupo dedicado de todos los usuarios estableciendo la **Habilitar "Todos los usuarios" grupo** cambiar a **Sí**. También, a continuación, puede editar el nombre de este grupo dedicado escribiéndola de nuevo en el **nombre para mostrar para "Todos los usuarios" grupo** campo.

El grupo de todos los usuarios puede usarse para asignar los mismos permisos a todos los usuarios en el directorio. Por ejemplo, puede conceder todos los usuarios en el acceso de directorio a una aplicación de SaaS asignando acceso para el grupo de todos los usuarios dedicado a esta aplicación.

El grupo de todos los usuarios dedicado incluye todos los usuarios en el directorio, incluidos los invitados y los usuarios externos. Si necesita un grupo que excluya los usuarios externos, a continuación, puede hacerlo mediante la creación de un grupo con una regla basada en atributos dinámica como la siguiente:

                (user.userPrincipalName -notContains "#EXT#@")

Para un grupo que excluya a todos los invitados, utilice una regla como la siguiente:

                (user.userType -ne "Guest")

Para obtener información sobre cómo crear reglas *Avanzadas* (reglas que pueden contener varios comparaciones) para la pertenencia a grupos dinámicos, consulte [uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md).


Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
