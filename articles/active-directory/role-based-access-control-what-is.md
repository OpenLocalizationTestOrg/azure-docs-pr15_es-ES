<properties
    pageTitle="Control de acceso basado en roles | Microsoft Azure"
    description="Introducción en administración de access con el control de acceso basado en roles de Azure en el Portal de Azure. Utilice las asignaciones de roles para asignar permisos en el directorio."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>Introducción a la administración de acceso en el portal de Azure

Empresas de seguridad deben centrarse en los empleados los permisos exactos que necesitan. Demasiados permisos expone una cuenta a ataques. Demasiado pocos permisos significa que los empleados no pueden obtener acceso a su trabajo de forma eficaz. Azure Control de acceso basado en roles (RBAC) le ayuda a resolver este problema mediante la oferta de administración de acceso específico para Azure.

RBAC puede dividir tareas en su equipo y conceder solo la cantidad de acceso a los usuarios que necesitan para realizar su trabajo. En lugar de darle a todo el mundo sin restricciones de permisos en su suscripción de Azure o recursos, puede permitir que solo determinadas acciones. Por ejemplo, use RBAC para permitir que un empleado administrar máquinas virtuales de una suscripción, mientras que otro puede administrar bases de datos SQL en la misma suscripción.

## <a name="basics-of-access-management-in-azure"></a>Conceptos básicos de administración de acceso en Azure
Cada suscripción Azure está asociado con un directorio de Azure Active Directory (AD). Usuarios, grupos y aplicaciones de ese directorio pueden administrar los recursos en la suscripción de Azure. Asignar estos derechos de acceso mediante el portal de Azure, Azure herramientas de línea de comandos y las API de administración de Azure.

Conceder acceso al asignar el rol RBAC correspondiente a los usuarios, grupos y aplicaciones en un determinado ámbito. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un recurso. Una función asignada en un ámbito principal también concede acceso a los elementos secundarios dentro del mismo. Por ejemplo, un usuario con acceso a un grupo de recursos puede administrar todos los recursos que contiene, como sitios Web, máquinas virtuales y subredes.

![Relación entre los elementos de Azure Active Directory - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

La función RBAC que asigne determina qué recursos puede administrar el usuario, grupo o aplicación dentro de ese ámbito.

## <a name="built-in-roles"></a>Funciones integradas
RBAC Azure tiene tres funciones básicas que se aplican a todos los tipos de recursos:

- **Propietario** tiene acceso completo a todos los recursos, incluido el derecho de acceso de delegado a otras personas.
- **Colaborador** puede crear y administrar todos los tipos de recursos de Azure, pero no puede conceder acceso a otras personas.
- **Lector** puede ver los recursos existentes de Azure.

El resto de las funciones RBAC en Azure permitir la administración de recursos de Azure específicos. Por ejemplo, el rol de colaborador de máquina Virtual permite al usuario crear y administrar máquinas virtuales de Windows. No darles acceso a la red virtual o la subred que se conecta la máquina virtual.

[Funciones integradas de RBAC](role-based-access-built-in-roles.md) enumera las funciones disponibles en Azure. Especifica las operaciones y el ámbito de cada rol integrada otorga a los usuarios. Si está buscando para definir sus propios roles aún más control, consulte cómo crear [funciones personalizadas en Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Herencia de jerarquía y acceso a los recursos
- Cada **suscripción** en Azure pertenece a un directorio.
- Solo una suscripción al que pertenece cada **grupo de recursos** .
- Cada **recurso** pertenece a un grupo de recursos.

Acceso a los que desea conceder en ámbitos primario se hereda en ámbitos secundarios. Por ejemplo:

- Asignar la función de lector a un grupo de Azure AD en el ámbito de la suscripción. Los miembros del grupo pueden ver todos los grupos de recursos y recursos en la suscripción.
- Asignar el rol de colaborador a una aplicación en el ámbito de grupo de recursos. Puede administrar los recursos de todos los tipos de ese grupo de recursos, pero no otros grupos de recursos en la suscripción.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC frente a los administradores de suscripción clásica
Los administradores de suscripción clásica y co administradores tienen acceso completo a la suscripción de Azure. Pueden administrar recursos con el [portal de Azure](https://portal.azure.com) con la API del Administrador de recursos de Azure o el modelo de implementación clásica [portal clásica Azure](https://manage.windowsazure.com) y Azure. En el modelo RBAC, los administradores clásicos le asigna el rol de propietario en el ámbito de la suscripción.

Solo el portal de Azure y las nuevas API de administrador de recursos de Azure admiten RBAC de Azure. Los usuarios y las aplicaciones que están asignadas a roles RBAC no pueden usar el portal de administración clásica y el modelo de implementación clásica Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorización para la administración de frente a las operaciones de datos
Azure RBAC solo es compatible con las operaciones de administración de los recursos de Azure en el portal de Azure y las API de administrador de recursos de Azure. No puede autorizar a todas las operaciones de nivel de datos para los recursos de Azure. Por ejemplo, puede autorizar a un usuario para administrar las cuentas de almacenamiento, pero no a las tablas dentro de una cuenta de almacenamiento o BLOB no. Asimismo, una base de datos SQL puede administrarse, pero no las tablas dentro de ella.

## <a name="next-steps"></a>Pasos siguientes
- Introducción a [Control de acceso basado en roles en el portal de Azure](role-based-access-control-configure.md).
- Vea las [funciones integradas de RBAC](role-based-access-built-in-roles.md)
- Definir sus propios [roles personalizados en Azure RBAC](role-based-access-control-custom-roles.md)
