<properties
    pageTitle="Administrar el acceso a los recursos con grupos de Azure Active Directory | Microsoft Azure"
    description="Cómo usar los grupos de Azure Active Directory para administrar el acceso de usuario locales y aplicaciones de la nube y recursos."
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


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Administrar el acceso a los recursos con grupos de Azure Active Directory

Azure Active Directory (AD Azure) es una solución de administración completa de acceso e identidad que proporciona un conjunto sólido de capacidades para administrar el acceso a los recursos de Microsoft online services como Office 365 y un mundo de aplicaciones SaaS que no sean de Microsoft y aplicaciones de nube y local. Este artículo proporciona una descripción general, pero si desea empezar a usar los grupos de Azure AD ahora mismo, siga las instrucciones de [administración de grupos de seguridad en Azure AD](active-directory-accessmanagement-manage-groups.md). Si desea ver cómo puede usar PowerShell para administrar grupos de Azure Active directory puede leer en más [cmdlets de vista previa de Azure Active Directory para la administración de grupo](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Para usar Azure Active Directory, necesita una cuenta de Azure. Si no tiene una cuenta, puede [suscribirse a una cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).


Dentro de Azure AD, una de las principales características es la capacidad para administrar el acceso a los recursos. Estos recursos pueden ser parte del directorio, como en el caso de permisos para administrar objetos a través de funciones en el directorio o recursos externos en el directorio, como aplicaciones SaaS, servicios de Azure y los sitios de SharePoint o en los recursos locales. Hay cuatro formas de que un usuario se puede asignar derechos de acceso a un recurso:


1. Asignación directa

    Los usuarios pueden asignarse directamente a un recurso por el propietario de ese recurso.

2. Pertenencia a grupos

    Un grupo se puede asignar a un recurso por el propietario del recurso y de este modo, conceder a los miembros de ese grupo de acceso al recurso. Miembros del grupo pueden administrarse por el propietario del grupo. De hecho, el propietario del recurso delega el permiso para asignar a los usuarios a sus recursos al propietario del grupo.

3. Basado en reglas

    El propietario del recurso puede usar una regla para expresar deben asignarse a los usuarios acceso a un recurso. El resultado de la regla depende de los atributos utilizados en esa regla y sus valores para usuarios específicos y de esta forma, el propietario del recurso eficaz delega el derecho para administrar el acceso a sus recursos al origen relevantes para los atributos que se usan en la regla. El propietario del recurso todavía administra la propia regla y determina qué atributos y valores proporcionan acceso a sus recursos.

4. Entidad emisora externa

    El acceso a un recurso procede de un origen externo; Por ejemplo, un grupo que se sincroniza desde un origen autorizado como un directorio local o con una aplicación de SaaS como día laborable. El propietario del recurso asigna el grupo para proporcionar acceso al recurso y, a continuación, el origen externo administra a los miembros del grupo.

  ![Información general de diagrama de administración de acceso](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Vea un vídeo que explica la administración de acceso

Puede ver un breve vídeo que explica más sobre esto:

**Azure AD: Introducción a pertenencia dinámica para grupos**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>¿Cómo tener acceso a la administración de trabajo de Azure Active Directory?
En el centro de Azure AD solución de administración de acceso es el grupo de seguridad. Usar un grupo de seguridad para administrar el acceso a los recursos es un paradigma conocido, lo que permite una forma flexible y fácil de entender proporcionar acceso a un recurso para el grupo de usuarios previsto. El propietario del recurso (o el administrador del directorio) puede asignar un grupo para proporcionar un acceso determinado de derecha a los recursos que poseen. Los miembros del grupo se proporcionará el acceso y el propietario del recurso puede delegar el derecho para administrar la lista de miembros de un grupo a otra persona, por ejemplo, un administrador del departamento de soporte técnico o departamento.

![Diagrama de administración de acceso de Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

El propietario de un grupo también puede hacer que ese grupo disponible para las solicitudes de autoservicio. En este modo, un usuario final puede buscar y busque el grupo y realizar una solicitud para unirse a, búsqueda eficaz de permiso de acceso a los recursos que se administran a través del grupo. El propietario del grupo puede configurar el grupo para que las solicitudes de combinación se aprueban automáticamente o requieran la aprobación del propietario del grupo. Cuando un usuario realiza una solicitud para unirse a un grupo, se reenvía la solicitud unirse a los propietarios del grupo. Si uno de los propietarios aprueba la solicitud, se notifica al usuario que solicita y el usuario se ha unido al grupo. Si uno de los propietarios rechaza la solicitud, el usuario que solicita una notificación pero no unido al grupo.


## <a name="getting-started-with-access-management"></a>Introducción a la administración de acceso
¿Listo para empezar? Pruebe algunas de las tareas básicas que puede hacer con grupos de Azure AD. Utilice estas funciones para proporcionar acceso especializado a diferentes grupos de personas para los distintos recursos de su organización. A continuación se muestran una lista de los primeros pasos básicos.

* [Crear una regla simple para configurar dinámico pertenencia a un grupo](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [Uso de un grupo para administrar el acceso a las aplicaciones de SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Hacer que un grupo estén disponibles para el usuario final autoservicio](active-directory-accessmanagement-self-service-group-management.md)

* [Sincronización de un grupo local a Azure con Azure AD Connect](active-directory-aadconnect.md)

* [Administrar propietarios de un grupo](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Pasos siguientes para la administración de acceso
Ahora que ha comprendido los conceptos básicos de administración de acceso, hay algunas capacidades avanzadas adicionales disponibles en Azure Active Directory para administrar el acceso a las aplicaciones y recursos.

* [Usar atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Administrar grupos de seguridad en Azure AD](active-directory-accessmanagement-manage-groups.md)

* [Configurar grupos dedicados en Azure AD](active-directory-accessmanagement-dedicated-groups.md)

* [Referencia de la API de gráfico para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
