<properties
pageTitle="Aplicación de Azure Active Directory y objetos de servicio Principal | Microsoft Azure"
description="Una discusión de la relación entre la solicitud y objetos principales de servicio de Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Aplicación de servicio objetos y principal de Azure Active Directory
Cuando se leen sobre un Azure Active Directory (AD) "aplicación", no siempre es borrar exactamente lo que se hace referencia por el autor. El objetivo de este artículo es para que sea más clara, definiendo los aspectos de integración de aplicaciones de Azure AD, con un ejemplo de registro y consentimiento para una [aplicación de múltiples arrendatario](active-directory-dev-glossary.md#multi-tenant-application)conceptuales y concretos.

## <a name="overview"></a>Información general
Una aplicación de Azure AD es más amplia que simplemente una parte del software. Es un término conceptual, no solo a las aplicaciones de software, sino también su registro de referencia (también conocido como: configuración de identidad) con Azure AD, lo que permite participar en la autenticación y la autorización "conversaciones" en tiempo de ejecución. Por definición, una aplicación puede funcionar en un rol de [cliente](active-directory-dev-glossary.md#client-application) (consume un recurso), un rol de [servidor de recursos](active-directory-dev-glossary.md#resource-server) (API exponer a los clientes) o incluso ambos. El protocolo de conversación se define mediante un [flujo de concesión de autorización de OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), con el objetivo de permitir que el recurso de cliente de access y proteger los datos de un recurso respectivamente. Ahora vamos a un nivel más profundo y vea cómo el modelo de aplicación de Azure AD representa una aplicación internamente. 

## <a name="application-registration"></a>Registro de aplicaciones
Al registrar una aplicación en el [portal de clásica Azure][AZURE-Classic-Portal], se crean dos objetos de su inquilino de Azure AD: un objeto de la aplicación y a continuación, un objeto principal de servicio.

#### <a name="application-object"></a>Objeto Application
Una aplicación de Azure AD está *definida* por su uno y solo objeto de la aplicación, que reside en el inquilino de Azure AD donde se registró la aplicación, denominados inquilino de "inicio" de la aplicación. El objeto application proporciona información relacionada con la identidad de una aplicación y es la plantilla desde la que sus objetos principal de servicio correspondientes son *derivados* para su uso en tiempo de ejecución. 

Puede considerar que la aplicación como la representación *global* de la aplicación (para su uso en todos los inquilinos) y la principal del servicio como la representación *local* (para su uso en un inquilino específico). La [entidad de aplicación] de Azure AD Graph[ AAD-Graph-App-Entity] define el esquema de un objeto de la aplicación. Un objeto de la aplicación, por tanto, tiene una relación de 1:1 con la aplicación de software y un 1:*n* relación con sus objetos principal del servicio *n* correspondiente.

#### <a name="service-principal-object"></a>Objeto de servicio principal
El objeto principal del servicio define la directiva y los permisos para una aplicación, que proporciona la base de una entidad de seguridad representar la aplicación al acceso a los recursos en tiempo de ejecución. El gráfico de Azure AD [entidad ServicePrincipal] [ AAD-Graph-Sp-Entity] define el esquema de un objeto de principal de servicio. 

Un objeto principal del servicio es necesario en cada inquilino para que una instancia de uso de la aplicación debe representarse, habilitar acceso seguro a los recursos de cuentas de usuario de ese inquilino. Una aplicación de inquilinos solo tendrá un único principal del servicio (en su inquilino principal). Una [aplicación Web](active-directory-dev-glossary.md#web-client) de múltiples arrendatarios también tendrá una entidad de seguridad de servicio en cada inquilino donde un administrador o los usuarios de ese inquilino han dado su consentimiento, para que puedan tener acceso a sus recursos. Tras consentimiento, será consultado el objeto principal del servicio para las solicitudes de autorización futuras. 

> [AZURE.NOTE] Los cambios que realice en el objeto de la aplicación, también se reflejan en el objeto principal de servicio en particular inquilinos la aplicación solo (el inquilino donde se registró). Para las aplicaciones de múltiples arrendatarios, cambios en el objeto de aplicación no se reflejan en objetos principales de servicio de los inquilinos de los consumidores, hasta que el inquilino de consumidor quita el acceso y concede el acceso.

## <a name="example"></a>Ejemplo
El siguiente diagrama muestra la relación entre el objeto application de la aplicación y la correspondiente objetos principales, en el contexto de una aplicación de varios inquilinos de ejemplo denominado **HR aplicación**de servicio. En este escenario, hay tres inquilinos de Azure AD: 

- **Adatum** - el inquilino usado por la empresa que desarrolla la **aplicación de recursos humanos**
- **Contoso** - el inquilino utilizado por la organización de Contoso, que es un consumidor de la **aplicación de recursos humanos**
- **Fabrikam** - el inquilino utilizado por la organización Fabrikam, que también consume la **aplicación de recursos humanos**

![Relación entre un objeto de la aplicación y un objeto principal de servicio](./media/active-directory-application-objects/application-objects-relationship.png)

En el diagrama anterior, paso 1 es el proceso de creación de la aplicación y objetos principales de servicio en el inquilino de inicio de la aplicación.

En el paso 2, cuando los administradores de Contoso y Fabrikam completar consentimiento, un objeto principal de servicio se crea en inquilino de Azure AD de la empresa y se asignan los permisos que le ha concedido el administrador. Tenga en cuenta que la aplicación de recursos humanos podría estar configurado/diseñada para permitir consentimiento por usuarios para uso individual.

En el paso 3, los inquilinos consumidor de la aplicación de recursos humanos (Contoso y Fabrikam) cada tienen su propio objeto principal del servicio. Cada representa su uso de una instancia de la aplicación en tiempo de ejecución regulado por los permisos aceptado por el administrador respectivo.

## <a name="next-steps"></a>Pasos siguientes
Objeto de aplicación de la aplicación se puede acceder a través de la API de Azure AD Graph representada por su OData [entidad de aplicación][AAD-Graph-App-Entity]

Objeto principal de servicio de la aplicación se puede acceder a través de la API de Azure AD Graph, representada por su [entidad ServicePrincipal] de OData[AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com