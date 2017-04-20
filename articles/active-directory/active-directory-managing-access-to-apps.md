<properties
  pageTitle="Administrar el acceso a aplicaciones mediante Azure AD |  Microsoft Azure"
  description="Describe cómo Azure Active Directory permite a las organizaciones especificar las aplicaciones a los que cada usuario tiene acceso."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Administrar el acceso a aplicaciones

Administración del acceso continuo, evaluación de uso, informes y continuarán a ser un desafío después de una aplicación está integrada en el sistema de identidad de su organización. En muchos casos, los administradores de TI o el departamento de soporte técnico debe tener un rol activo en curso para administrar el acceso a sus aplicaciones. A veces, la asignación se realiza por el equipo de TI general o de división. A menudo, la decisión de asignación pretende delegar a las decisiones, que requieren su aprobación antes de TI facilita la asignación.  Otras organizaciones inviertan en integración con un automatizado identidad y acceso sistema de administración, como Control de acceso basado en roles (RBAC) o Control de acceso basado en atributos (ABAC). La integración y el desarrollo de regla suelen ser muy costosas y especializado. Supervisión o de informar sobre uno de los métodos administración es su propio inversión independiente, costosa y complejo.

## <a name="how-does-azure-active-directory-help"></a>¿Cómo ayuda Azure Active Directory?

 Azure AD es compatible con la administración del acceso amplio para aplicaciones configuradas, permite que las organizaciones pueden obtener fácilmente las directivas de acceso derecha comprendido entre automática, basado en atributos de asignación (escenarios ABAC o RBAC) a través de la delegación e incluida la administración de administrador. Con Azure AD puede lograr fácilmente directivas complejas, combinar varios modelos de administración para una sola aplicación y puede incluso volver a utilizar las reglas de administración entre las aplicaciones con el mismo audiencias.

 - [Agregar un nuevo o existente de aplicaciones](active-directory-sso-integrate-saas-apps.md)


 Asignación de aplicaciones de Azure AD se centra en dos modos de asignación principal:

- **Asignación individual** Un administrador de TI con permisos de administrador Global de directorio puede seleccionar las cuentas de usuario individual y concederles acceso a la aplicación.
- **Asignación basada en el grupo (pagada Azure AD solo)** Un administrador de TI con permisos de administrador Global de directorio puede asignar a un grupo a la aplicación. Acceso a los usuarios específico depende de si son miembros del grupo en el momento de intentar obtener acceso a la aplicación. En otras palabras, un administrador puede crear una regla de asignación que indica "cualquier miembro del grupo asignado actual tiene acceso a la aplicación" de forma eficaz. Con esta opción de asignación, los administradores pueden beneficiarse de cualquiera de las opciones de administración de grupo de Azure AD, incluyendo [grupos dinámicos basada en atributos](active-directory-accessmanagement-manage-groups.md), grupos de sistema externo (por ejemplo, local de Active Directory o día laborable) o grupos administradas por el administrador o administradas de servicio automáticamente. Un solo grupo puede asignarse fácilmente a varias aplicaciones, asegurarse de que las aplicaciones con afinidad de asignación pueden comparten las reglas de asignación, reduce la complejidad de la administración general. Tenga en cuenta ese grupo anidado pertenencias a grupos no se admiten para la asignación basada en el grupo para las aplicaciones en este momento.

El uso de estos modos de dos asignación administradores pueden lograr cualquier enfoque de administración de la asignación conveniente.

Con Azure AD, informes de asignación y uso está totalmente integrado, permitir a los administradores fácilmente informar sobre el estado de asignación, errores de asignación y el uso par.

## <a name="complex-application-assignment-with-azure-ad"></a>Asignación de aplicación compleja con Azure AD

Considere la posibilidad de una aplicación como Salesforce. En muchas organizaciones, Salesforce usada principalmente por las organizaciones de ventas y marketing. A menudo, los miembros del equipo de marketing muy disponen de acceso a Salesforce, mientras que los miembros del equipo de venta tienen acceso limitado. En muchos casos, una amplia población de trabajadores tienen acceso restringido a la aplicación. Excepciones a estas reglas dificultar la materia. A menudo es la prerogative de los equipos de liderazgo de marketing o ventas para conceder acceso de un usuario o cambiar sus funciones independientemente de estas reglas genéricas.

Con Azure AD, aplicaciones como Salesforce pueden preconfiguradas para inicio de sesión único (SSO) y aprovisionamiento automatizado. Una vez configurada la aplicación, un administrador puede realizar la acción única para crear y asignar a los grupos correspondientes. En este ejemplo, un administrador puede ejecutar las siguientes asignaciones:

- Puede definir [grupos dinámicos](active-directory-accessmanagement-manage-groups.md) para representar automáticamente todos los miembros de los equipos de ventas y marketing con atributos como el departamento o el rol:

    - Todos los miembros de grupos de marketing podrían asignarse a la función "marketing" de Salesforce

    - Todos los miembros del equipo de ventas grupos podrían asignarse a la función de Salesforce "venta". Una mejora podría usar varios grupos que representan los equipos de ventas regionales asignados a roles de Salesforce diferentes.

- Para habilitar el mecanismo de excepciones, se puede crear un grupo de autoservicio para cada rol. Por ejemplo, el grupo "Excepción de marketing de Salesforce" puede crearse como un grupo de autoservicio. El grupo se puede asignar a la función de marketing de Salesforce y pueden realizarse en el equipo de liderazgo marketing propietarios. Los miembros del equipo de marketing liderazgo podrían agregar o quitar usuarios, establecida una directiva de combinación, o incluso aprobar o rechazar solicitudes de los usuarios individuales para unirse a. Esto es compatible a través de una experiencia adecuado de trabajo información que no requiere aprendizaje especializada para propietarios o miembros.

En este caso, todos los usuarios asignados ¿automáticamente proporcionar a Salesforce, mientras que se hayan agregado a diferentes grupos de que su asignación de roles se actualizará en Salesforce. Los usuarios podrían descubrir y tener acceso a Salesforce mediante el panel de acceso de aplicación de Microsoft, los clientes de Office web, o incluso por navegar a su página de inicio de sesión de Salesforce organizativa. Los administradores podrán ver fácilmente el estado de asignación y uso utilizando informes de Azure AD.

Los administradores pueden emplear [acceso condicional de Azure AD](active-directory-conditional-access.md) a configurar directivas de acceso a funciones específicas. Si se permite el acceso fuera del entorno corporativo e incluso los requisitos de autenticación multifactor o dispositivo para lograr acceso en varios casos, pueden incluir estas directivas.

## <a name="how-can-i-get-started"></a>¿Cómo puedo empezar?

En primer lugar, si no está usando Azure AD y es un administrador de TI:

 - [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory/) -puede registrarse para una prueba gratuita de 30 días hoy e implementar la solución de nube primera en menos de 5 minutos con este vínculo

Azure AD que habilitar el uso compartido de la cuenta incluye:

- [Asignación a un grupo](active-directory-accessmanagement-self-service-group-management.md)
- Agregar aplicaciones a Azure AD
- Introducción a la asignación
- Preguntas más frecuentes de asignación de aplicaciones
- [Informes y paneles de uso de aplicación](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>¿Dónde puedo obtener más información?

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Proteger aplicaciones con acceso condicional](active-directory-conditional-access.md)
- [Grupo de autoservicio administración/SSAA](active-directory-accessmanagement-self-service-group-management.md)
