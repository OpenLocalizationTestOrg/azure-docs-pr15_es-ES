<properties
   pageTitle="Administración de identidades para aplicaciones multiempresa | Microsoft Azure"
   description="Procedimientos recomendados para la administración de autenticación, autorización e identidad de aplicaciones multiempresa."
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

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Administración de identidades para aplicaciones multiempresa en Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Esta serie describe los procedimientos recomendados para varias empresas, cuando se usa Azure AD para la administración de identidad y autenticación.

Cuando se crea una aplicación multiempresa, uno de los primeros desafíos es administración de identidades de usuario, porque ahora todos los usuarios pertenecen a un inquilino. Por ejemplo:

- Los usuarios inicie sesión con sus credenciales de la organización.
- Los usuarios deben tener acceso a datos de la organización, pero no los datos que pertenecen a otros inquilinos.
- Una organización puede iniciar sesión en la aplicación y, a continuación, asignar roles de aplicación a sus miembros.

Azure Active Directory (AD Azure) tiene algunas características que admiten todos estos escenarios.

Para acompañar esta serie de artículos, también creamos una completa, [implementación de llevar a cabo] [ tailspin] de una aplicación multiempresa. Los artículos reflejan lo aprendido en el proceso de creación de la aplicación. Para empezar con la aplicación, vea [ejecutar la aplicación de encuestas](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

A continuación se ofrece la lista de artículos de esta serie:

- [Introducción a la administración de identidades para aplicaciones multiempresa](guidance-multitenant-identity-intro.md)
- [Acerca de la aplicación Tailspin encuestas](guidance-multitenant-identity-tailspin.md)
- [Autenticación con Azure AD y conectar OpenID](guidance-multitenant-identity-authenticate.md)
- [Trabajar con identidades basada en notificaciones](guidance-multitenant-identity-claims.md)
- [Suscripción e integrado de inquilinos](guidance-multitenant-identity-signup.md)
- [Funciones de aplicación](guidance-multitenant-identity-app-roles.md)
- [Autorización basada en roles y recursos](guidance-multitenant-identity-authorize.md)
- [Proteger un web API de back-end](guidance-multitenant-identity-web-api.md)
- [Almacenamiento en caché OAuth2 tokens de acceso](guidance-multitenant-identity-token-cache.md)
- [Federar con AD FS de un cliente para aplicaciones multiempresa en Azure](guidance-multitenant-identity-adfs.md)
- [Usar aserción de cliente para obtener acceso tokens de Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Usar clave depósito para proteger la información confidencial de la aplicación](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
