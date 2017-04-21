<properties
   pageTitle="Administración de identidades para aplicaciones multiempresa | Microsoft Azure"
   description="Introducción a la administración de identidades en aplicaciones multiempresa"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="introduction-to-identity-management-for-multitenant-applications-in-microsoft-azure"></a>Introducción a la administración de identidades para aplicaciones multiempresa en Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

Supongamos que está escribiendo una aplicación de SaaS a estar hospedadas en la nube de empresa. Por supuesto, la aplicación tendrá los usuarios:

![Usuarios](media/guidance-multitenant-identity/users.png)

Pero esos usuarios pertenecen a organizaciones:

![Usuarios de la organización](media/guidance-multitenant-identity/org-users.png)

Ejemplo: Tailspin vende suscripciones a su aplicación SaaS. Contoso y Fabrikam registrarse para la aplicación. Cuando Ana (`alice@contoso`) signos en, la aplicación debe tener en cuenta que Ana es parte de Contoso.

- Ana _debería_ tener acceso a datos de Contoso.
- Ana _no debería_ tener acceso a datos de Fabrikam.

Esta guía le mostrará cómo administrar las identidades de usuario en una aplicación de multiempresa con [Azure Active Directory] [ AzureAD] (Azure AD) para controlar el inicio de sesión y la autenticación.

## <a name="what-is-multitenancy"></a>¿Qué es multitenancy?

Un _inquilino_ es un grupo de usuarios. En una aplicación de SaaS, el inquilino es un suscriptor o un cliente de la aplicación. _Varias empresas_ es una arquitectura donde varios inquilinos de compartan la misma instancia física de la aplicación. Aunque los inquilinos compartan recursos físicos (como máquinas virtuales o almacenamiento), cada inquilino obtiene su propia instancia lógica de la aplicación.

Normalmente, los datos de la aplicación se comparten entre los usuarios dentro de un inquilino, pero no con otros inquilinos.

![Multitenant](media/guidance-multitenant-identity/multitenant.png)

Compare esta arquitectura con una arquitectura solo inquilino, donde cada inquilino tiene una instancia física dedicada. En una arquitectura solo inquilino, para agregar los inquilinos, poner en marcha nuevas instancias de la aplicación.

![Solo inquilino](media/guidance-multitenant-identity/single-tenant.png)

### <a name="multitenancy-and-horizontal-scaling"></a>Escala multitenancy y horizontal

Para lograr la escala en la nube, es común para agregar más físicos instancias. Esto se conoce como _escala horizontal_ u _horizontal_. Considere la posibilidad de una aplicación web. Para controlar más tráfico, puede agregar más máquinas virtuales de servidor y colocarlos detrás de un equilibrador de carga. Cada VM ejecuta una instancia física independiente de la aplicación web.

![Un sitio web de equilibrio de carga](media/guidance-multitenant-identity/load-balancing.png)

Cualquier solicitud se puede enrutar a cualquier instancia. Juntos, el sistema funciona como una única instancia lógica. Puede eliminar una máquina virtual o control de una nueva máquina virtual sin afectar a los usuarios. En esta arquitectura, cada instancia física es múltiple inquilino y escalar agregando más instancias. Si una instancia se desconecta, no debe afectar a los inquilinos.

## <a name="identity-in-a-multitenant-app"></a>Identidad en una aplicación multiempresa

En una aplicación multiempresa, debe tener en cuenta los usuarios en el contexto de inquilinos.

**Autenticación**

- Los usuarios iniciar sesión en la aplicación con sus credenciales de la organización. No tienen que crear nuevos perfiles de usuario para la aplicación.
- Los usuarios dentro de la misma organización forman parte del mismo inquilino.
- Cuando un usuario inicia sesión, la aplicación sabe qué inquilino al que pertenece el usuario.

**Autorización**

- Cuando autorizar acciones del usuario (por ejemplo, ver un recurso), la aplicación debe tener en cuenta de inquilino de usuario.
- Los usuarios pueden asignarse funciones dentro de la aplicación, como "Administrador" o "Usuario estándar". Las asignaciones de roles deben administrarse por el cliente, no por el proveedor de SaaS.

**Ejemplo.** Ana, un empleado de Contoso, se desplaza a la aplicación en su explorador y hace clic en el botón "Iniciar sesión". Se redirige a una pantalla de inicio de sesión donde escribe sus credenciales corporativas (nombre de usuario y contraseña). En este momento, ha iniciado sesión en la aplicación como `alice@contoso.com`. La aplicación también sabe que Ana es un usuario de administración de esta aplicación. Porque es un administrador, puede ver una lista de todos los recursos que pertenecen a Contoso. Sin embargo, no puede ver los recursos de Fabrikam, porque es un administrador solo dentro de su inquilino.

En esta guía, veremos específicamente utilizando Azure AD para la administración de identidades.

- Suponemos que el cliente almacena sus perfiles de usuario en Azure AD (incluidos a los inquilinos de Office 365 y Dynamics CRM)
- Los clientes con local Active Directory (AD) pueden usar [Azure AD Connect] [ ADConnect] para sincronizar su AD locales con Azure AD.

Si un cliente con AD local no puede usar Azure AD Connect (debido a la directiva corporativa de TI o cualquier otro motivo), el proveedor puede federarse con el cliente de SaaS está AD a través de los servicios de federación de Active Directory (AD FS). Esta opción se describe en [Federating con AD FS de un cliente].

Esta guía no tiene en cuenta otros aspectos de varias empresas como partición de datos, configuración por inquilino y así sucesivamente.

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [aplicación sobre la Tailspin encuestas][tailpin]

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[parte de una serie]: guidance-multitenant-identity.md
[Federar con AD FS de un cliente]: guidance-multitenant-identity-adfs.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[tailpin]: guidance-multitenant-identity-tailspin.md
