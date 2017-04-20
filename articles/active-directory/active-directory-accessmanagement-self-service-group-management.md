<properties
    pageTitle="Configuración de Azure Active Directory para la administración de acceso de aplicación de autoservicio | Microsoft Azure"
    description="Administración de grupos de autoservicio permite a los usuarios crear y administrar grupos de seguridad o grupos de Office 365 en Azure Active Directory y ofrece a los usuarios la posibilidad de grupo de seguridad de la solicitud o la pertenencia a grupos de Office 365"
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
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Configuración de Azure Active Directory para la administración de grupos de autoservicio

Administración de grupos de autoservicio permite a los usuarios crear y administrar grupos de seguridad o grupos de Office 365 en Azure Active Directory (AD Azure). Los usuarios también pueden solicitar pertenencias a grupos de Office 365 o grupo de seguridad y, a continuación, el propietario del grupo puede aprobar o rechazar tu pertenencia. De este modo, se puede delegar control diarias de pertenencia a las personas que entender el contexto de la empresa para esa pertenencia. Características de administración de grupo de autoservicio están disponibles solo para los grupos de seguridad y Office 365, pero no para los grupos de seguridad habilitados para correo o listas de distribución.

Administración de grupos de autoservicio actualmente compone de dos escenarios esenciales: delegado la administración de grupo y administración de autoservicio de grupo.

- **Delegar la administración de grupos** 
   un ejemplo es un administrador que va a administrar el acceso a una aplicación de SaaS que está utilizando la compañía. Administrar estos derechos de acceso será complicado, por lo que este administrador solicita el propietario de la empresa para crear un nuevo grupo. El administrador asigna el acceso de la aplicación para el nuevo grupo y agrega al grupo todas las personas que ya tener acceso a la aplicación. El propietario de la empresa, a continuación, puede agregar más usuarios y dichos usuarios aprovisionados automáticamente a la aplicación. El propietario de la empresa no tiene que esperar para que el administrador administrar el acceso de los usuarios. Si el administrador concede el mismo permiso a un administrador de un grupo de empresa diferente, esa persona también puede administrar el acceso a sus propios usuarios. El propietario de la empresa ni el administrador puede ver o administrar de los demás usuarios. El administrador puede ver todos los usuarios que tienen acceso a la aplicación y derechos de acceso de bloque si es necesario.

- **Administración de grupos de autoservicio** 
   un ejemplo de este escenario es dos usuarios que tienen los sitios de SharePoint Online que configuración independientemente. Desea conceder acceso de equipos de los demás a sus sitios. Para ello, puede crear un grupo en Azure AD y en SharePoint Online, cada uno de ellos selecciona ese grupo para proporcionar acceso a sus sitios. Cuando alguien desea tener acceso, que solicitan desde el Panel de acceso y después de aprobación puede obtener acceso a los sitios de SharePoint Online automáticamente. Más adelante, uno de ellos decide que todas las personas que tienen acceso al sitio también deben obtener acceso a una aplicación de SaaS determinada. El Administrador de la aplicación de SaaS puede agregar derechos de acceso de la aplicación para el sitio de SharePoint Online. Desde ese momento, las solicitudes que obtengan aprobadas proporciona acceso a los dos sitios de SharePoint Online y también a esta aplicación SaaS.

## <a name="making-a-group-available-for-end-user-self-service"></a>Hacer que un grupo estén disponibles para el usuario final autoservicio

1. En el [portal de clásica Azure](https://manage.windowsazure.com), abra el directorio de Azure AD.

2. En la ficha **Configurar** , establezca **delegada grupo administración** en habilitado.

3. Configurar **los usuarios pueden crear grupos de seguridad** o **los usuarios pueden crear grupos de Office** en habilitado.

Cuando **que los usuarios pueden crear grupos de seguridad** está habilitado, todos los usuarios en el directorio tiene permiso para crear nuevos grupos de seguridad y agregar a miembros a estos grupos. Estos nuevos grupos también podrían mostrarse en el Panel de acceso para todos los demás usuarios. Si la configuración de directiva en el grupo lo permite, los demás usuarios puedan crear solicitudes para unirse a estos grupos. Si **los usuarios pueden crear grupos de seguridad** está deshabilitado, los usuarios no pueden crear grupos y no pueden cambiar los grupos existentes para que sea propietario. Sin embargo, aún pueden administrar la pertenencia a los grupos y aprobar las solicitudes de otros usuarios a unirse a los grupos.

También puede usar **los usuarios que pueden usar autoservicio para grupos de seguridad** para lograr un control de acceso más detallado sobre la administración de grupo de autoservicio para los usuarios. Cuando **que los usuarios pueden crear grupos** está habilitado, todos los usuarios en el directorio tiene permiso para crear nuevos grupos y agregar a miembros a estos grupos. Estableciendo también **quién pueden usar el autoservicio para grupos de seguridad de los usuarios** a algunos, es restringir administración a solo un grupo limitado de usuarios del grupo. Cuando se establece este modificador a algunos, debe agregar usuarios al grupo SSGMSecurityGroupsUsers antes de que pueden crear nuevos grupos y agregar a miembros a ellos. Mediante la configuración **que pueden usar autoservicio para grupos de seguridad de los usuarios** a todos, habilitar a todos los usuarios en el directorio para crear nuevos grupos.

También puede usar el cuadro de **grupo que puede usar autoservicio para grupos de seguridad** para especificar un nombre personalizado para un grupo cuyos miembros pueden utilizar autoservicio.

## <a name="additional-information"></a>Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
