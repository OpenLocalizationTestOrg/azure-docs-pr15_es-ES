<properties
   pageTitle="Glosario de Azure Active Directory programador | Microsoft Azure"
   description="Una lista de condiciones de las características y los conceptos de desarrollador de Azure Active Directory utilizados con frecuencia."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Glosario de desarrollador de Azure Active Directory
Este artículo contiene definiciones de algunos de los conceptos básicos de desarrollador de Azure Active Directory (AD), que son útiles para obtener información sobre el desarrollo de aplicaciones para Azure AD.

## <a name="access-token"></a>token de acceso
Tipo de [token de seguridad](#security-token) emitido por un [servidor de autorización](#authorization-server)y utilizada por la [aplicación cliente](#client-application) para tener acceso a un [protegido por el servidor de recursos](#resource-server). Normalmente en el formulario de un [Token de JSON Web (JWT)][JWT], el token incorpora la autorización concedida al cliente por el [propietario del recurso](#resource-owner), para un nivel de acceso solicitado. El token contiene aplicables todas las [notificaciones](#claim) sobre el asunto, lo que permite la aplicación de cliente usar como formulario de credenciales al tener acceso a un recurso. Esto también elimina la necesidad de que el propietario del recurso exponer credenciales al cliente.

Símbolos de acceso también se conoce como "Usuario + aplicación" o "Aplicación sólo", dependiendo de las credenciales que se representa. Por ejemplo, cuando se utiliza una aplicación de cliente la:

- [Conceder autorización de "Código de autorización"](#authorization-grant), el usuario final se autentica primero como el propietario del recurso, delegar la autorización del cliente para tener acceso al recurso. El cliente autentica posteriormente cuando se obtiene el token de acceso. El token puede a veces se denomina específicamente un símbolo "+ aplicación de usuario", que representa tanto el usuario que autorizados la aplicación cliente y la aplicación.
- [Conceder autorización "Credenciales de cliente"](#authorization-grant), el cliente proporciona la autenticación única, funciona sin autenticación/autorización del propietario recursos, por lo que el token a veces puede hacer referencia a como un símbolo de "Sólo aplicación".

Vea [referencia de Azure AD Token] [ AAD-Tokens-Claims] para obtener más detalles.

## <a name="application-manifest"></a>manifiesto de aplicación  
Una característica proporcionada por el [portal clásica Azure][AZURE-classic-portal], que genera una representación JSON de configuración de identidad de la aplicación, utilizada como mecanismo para actualizar su asociado [aplicación] [ AAD-Graph-App-Entity] y [ServicePrincipal] [ AAD-Graph-Sp-Entity] entidades. Consulte [Descripción de manifiesto de aplicación de Azure Active Directory] [ AAD-App-Manifest] para obtener más detalles.

## <a name="application-object"></a>objeto Application  
Cuando se registrar o actualizar una aplicación en el [portal de clásica Azure][AZURE-classic-portal], el portal crea o actualizaciones de un objeto application y correspondiente [objeto principal de servicio](#service-principal-object) para ese inquilino. *Define* la aplicación de objetos de la aplicación de configuración de identidad globalmente (a través de todos los inquilinos donde tiene acceso), proporcionar una plantilla desde la que sus objetos principal de servicio correspondientes son *derivadas* para usar localmente en tiempo de ejecución (en un inquilino específico).

Vea [objetos principales de servicio y aplicación] [ AAD-App-SP-Objects] para obtener más información.

## <a name="application-registration"></a>registro de aplicaciones  
Para permitir una aplicación Intégrelo y funciones de administración de identidades y acceso a Azure AD de delegado, debe estar registrado con un [inquilino](#tenant)de Azure AD. Cuando se registra la aplicación con Azure AD, proporciona una configuración de la identidad de la aplicación, lo que le permite integrar con Azure AD y usar características como:

- Sólida administración de inicio de sesión único con la administración de identidades de AD Azure y [Conectar OpenID] [ OpenIDConnect] implementación de protocolo
- El acceso a [los recursos protegidos](#resource-server) por [aplicaciones cliente](#client-application)a través de la implementación del [servidor de autorización](#authorization-server) de OAuth 2.0 de Azure AD
- [Consentimiento framework](#consent) para administrar el acceso de cliente a los recursos protegidos, en función de autorización de propietario de recursos.

Consulte [aplicaciones de integración con Azure Active Directory] [ AAD-Integrating-Apps] para obtener más detalles.

## <a name="authentication"></a>autenticación
La acción de un reto a una fiesta para credenciales legítimas, lo que proporciona la base para la creación de una entidad de seguridad que se usará para identidad y control de acceso. Por ejemplo, durante una [concesión de autorización de OAuth2](#authorization-grant) la fiesta autenticar es rellenar el rol de [propietario del recurso](#resource-owner) o la [aplicación cliente](#client-application), dependiendo de la concesión utilizada.

## <a name="authorization"></a>autorización
Acción de conceder un permiso de principal de seguridad autenticado que hacer algo. Hay dos casos de uso principal en el modelo de programación de Azure AD:

- Durante un flujo de [autorización de OAuth2 conceder](#authorization-grant) : cuando el [propietario del recurso](#resource-owner) autoriza a la [aplicación cliente](#client-application), para permitir que el cliente de acceso a los recursos del propietario de recursos.
- Durante el acceso a los recursos por el cliente: implementada por el [servidor de recursos](#resource-server), con la [Reclamación](#claim) valores presenten en el [token de acceso](#access-token) para tomar decisiones de control de acceso basadas en ellos.

## <a name="authorization-code"></a>código de autorización
Una corta duración "token" proporciona a una [aplicación de cliente](#client-application) por el [extremo de autorización](#authorization-endpoint), como parte del flujo de "código de autorización", uno de los cuatro de OAuth2 [concede autorización](#authorization-grant). El código se devuelve a la aplicación cliente en respuesta a la autenticación de un [propietario del recurso](#resource-owner), que indica que el propietario del recurso ha delegado la autorización para acceder a los recursos solicitados. Como parte del flujo, el código más adelante se canjea por un [token de acceso](#access-token).

## <a name="authorization-endpoint"></a>extremo de autorización
Uno de los extremos implementados por el [servidor de autorización](#authorization-server), utilizado para interactuar con el [propietario del recurso](#resource-owner) para proporcionar un [conceder autorización](#authorization-grant) durante una autorización OAuth2 conceder flujo. Según el flujo de conceder autorización utilizado, puede variar la concesión real proporcionada, incluso un [código de autorización](#authorization-code) o el [token de seguridad](#security-token).

Consulte de la especificación OAuth2 [autorización conceder tipos] [ OAuth2-AuthZ-Grant-Types] y [autorización extremo] [ OAuth2-AuthZ-Endpoint] secciones y la [especificación de OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] para obtener más detalles.

## <a name="authorization-grant"></a>concesión de autorización
Credencial que representa el [propietario de recurso](#resource-owner) [autorización](#authorization) para tener acceso a sus recursos protegidos, concedidos a una [aplicación de cliente](#client-application). Una aplicación de cliente puede usar uno de los [cuatro tipos de conceder definidos por el marco de autorización de OAuth2] [ OAuth2-AuthZ-Grant-Types] para obtener una concesión, según los requisitos de tipo de cliente: "concesión de autorización de código", "concesión de credenciales de cliente", "conceder implícito" y "concesión de credenciales de contraseña de propietario de recursos". La credencial devuelve al cliente es un [token de acceso](#access-token)o un [código de autorización](#authorization-code) (intercambiar más adelante para un token de acceso), según el tipo de concesión de autorización de utilizar.

## <a name="authorization-server"></a>servidor de autorización
Definida por el [Marco de autorización de OAuth2][OAuth2-Role-Def], el servidor responsable de emitir acceso tokens al [cliente](#client-application) después de autenticar el [propietario del recurso](#resource-owner) y su autorización correctamente. Una [aplicación de cliente](#client-application) interactúa con el servidor de autorización en tiempo de ejecución a través de su [autorización](#authorization-endpoint) y extremos [token](#token-endpoint) , según la OAuth2 definen [concede autorización](#authorization-grant).

En el caso de integración de aplicaciones de Azure AD Azure AD implementa el rol de servidor de autorización para las aplicaciones de Azure AD y Microsoft API del servicio, por ejemplo, [Las API de Microsoft Graph][Microsoft-Graph].

## <a name="claim"></a>reclamar
Un [token de seguridad](#security-token) contiene reclamaciones, que proporcionan aserciones una entidad (por ejemplo, una [aplicación de cliente](#client-application) o [propietario de recursos](#resource-owner)) a otra entidad (por ejemplo, el [servidor de recursos](#resource-server)). Reclamaciones son pares de nombre y valor que retransmisión hechos sobre el asunto token (por ejemplo, la entidad de seguridad que ha sido autenticado por el [servidor de autorización](#authorization-server)). Las notificaciones en un determinado token dependen de varias variables, incluido el tipo de token, el tipo de credenciales que se utilizan para autenticar el asunto, la configuración de la aplicación, etcetera.

Vea la [referencia de token de Azure AD] [ AAD-Tokens-Claims] para obtener más detalles.

## <a name="client-application"></a>aplicación de cliente  
Definida por el [Marco de autorización de OAuth2][OAuth2-Role-Def], una aplicación que hace protegido las solicitudes de recursos en nombre del [propietario del recurso](#resource-owner). El término "cliente" no implica las características de implementación de hardware concreto (por ejemplo, si la aplicación se ejecuta en un servidor, un escritorio o en otros dispositivos).  

Una aplicación de cliente solicita [autorización](#authorization) de propietario de un recurso a participar en un flujo de [autorización de OAuth2 conceder](#authorization-grant) y puede tener acceso a datos y las API en nombre del propietario de recursos. El marco de autorización de OAuth2 [define dos tipos de clientes de][OAuth2-Client-Types], "confidencial" y "público", en función de la capacidad del cliente para mantener la confidencialidad de sus credenciales. Las aplicaciones pueden implementar un [cliente de web (confidencial)](#web-client) que se ejecuta en un servidor web, un [cliente nativo (pública)](#native-client) instalado en un dispositivo o un [cliente basada en el agente de usuario (pública)](#user-agent-based-client) que se ejecuta en el Explorador de un dispositivo.

## <a name="consent"></a>consentimiento
El proceso de un [propietario del recurso](#resource-owner) conceder autorización para una [aplicación de cliente](#client-application), específicos [permisos](#permissions) para tener acceso a los recursos protegidos, en nombre del propietario del recurso. Según los permisos solicitados por el cliente, un administrador o el usuario tendrá que consentimiento para permitir el acceso a los datos de su organización o personas respectivamente. Tenga en cuenta, en un escenario de [varios inquilinos](#multi-tenant-application) de la aplicación [principal del servicio](#service-principal-object) también está registrado en el inquilino del usuario consenting.

## <a name="id-token"></a>Símbolo del Id.
Un [OpenID conectar] [ OpenIDConnect-ID-Token] [token de seguridad](#security-token) proporcionado por un [extremo de autorización](#authorization-endpoint), que contiene [reclamaciones](#claim) relacionados con la autenticación de un usuario final [propietario del recurso [servidor de autorización](#authorization-server) ](#resource-owner). Al igual que un token de acceso, tokens ID también se representan como un firmado digitalmente [JSON Web Token (JWT)][JWT]. A diferencia de un token de acceso, notificaciones del token de un identificador no se usan para fines relacionados con el acceso a los recursos y control de acceso específicamente.

Vea la [referencia de token de Azure AD] [ AAD-Tokens-Claims] para obtener más detalles.

## <a name="multi-tenant-application"></a>aplicación de múltiples arrendatario
Una clase de [aplicación de cliente](#client-application) que permite iniciar sesión y [consentimiento](#consent) por usuarios aprovisionados en cualquier Azure AD [inquilino](#tenant), incluidos los inquilinos distinto del dónde está registrado el cliente. Por el contrario, una aplicación registrada como único-inquilino, ¿solo permite a inicios de sesión de cuentas de usuario se aprovisione en el mismo inquilino como el donde se registra la aplicación. Aplicaciones [cliente nativo](#native-client) son varios inquilinos de forma predeterminada, mientras que las aplicaciones [cliente de web](#web-client) tienen la capacidad para seleccionar entre único y múltiple inquilino.

Vea [cómo iniciar sesión en cualquier usuario de Azure AD utilizando el modelo de aplicación de múltiples arrendatarios] [ AAD-Multi-Tenant-Overview] para obtener más detalles.

## <a name="native-client"></a>cliente nativo
Tipo de [aplicación cliente](#client-application) que se instala de forma nativa en un dispositivo. Dado que todo el código se ejecuta en un dispositivo, se considera a un cliente "público" al no poder almacenar credenciales en privado o confidencial. Consulte [perfiles y tipos de cliente de OAuth2] [ OAuth2-Client-Types] para obtener más detalles.

## <a name="permissions"></a>permisos
Una [aplicación de cliente](#client-application) obtiene acceso a un [servidor de recursos](#resource-server) mediante la declaración de las solicitudes de permiso. Existen dos tipos:

- Permisos "Delegados", que solicitan acceso [basado en el ámbito](#scopes) en delegados autorización firmado en del [propietario del recurso](#resource-owner), se presentan al recurso en tiempo de ejecución como ["scp" reclamaciones](#claim) del cliente [token de acceso](#access-token).
- Permisos de "Aplicación", que solicitan acceso [basado en roles](#roles) en las credenciales de identidad de la aplicación cliente, se presentan al recurso en tiempo de ejecución como ["funciones" notificaciones](#claim) de token de acceso del cliente.

Que también superficie durante el proceso de [consentimiento](#consent) , ofreciendo el administrador o el propietario del recurso, la oportunidad de conceder o denegar el acceso a los recursos en su inquilino del cliente.

Las solicitudes de permiso se configuran en las aplicaciones de"" / "Configurar" pestaña en el [portal de clásica Azure][AZURE-classic-portal], bajo "Permisos para otras aplicaciones", seleccionando la deseado "delegar permisos" y "aplicación" (el último requiere la pertenencia a la función de administrador Global). Dado que un [cliente público](#client-application) no se mantiene credenciales, solo puede solicitar permisos delegados, mientras un [cliente confidencial](#client-application) tiene la capacidad de solicitud delegados y los permisos de aplicación. [Objeto application](#application-object) del cliente almacena los permisos declarados en su [propiedad requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>propietario de recursos
Definida por el [Marco de autorización de OAuth2][OAuth2-Role-Def], una entidad puede conceder acceso a un recurso protegido. Si el propietario del recurso es una persona, se conoce como un usuario final. Por ejemplo, cuando una [aplicación de cliente](#client-application) desea tener acceso a un buzón de usuario a través de la [API de Microsoft Graph][Microsoft-Graph], requiere permiso del propietario de los recursos del buzón de correo.

## <a name="resource-server"></a>servidor de recursos
Definida por el [Marco de autorización de OAuth2][OAuth2-Role-Def], un servidor que hosts protegida, capaz de Aceptar y responder a los recursos protegidos las solicitudes de recursos por [aplicaciones cliente](#client-application) que presentan un [token de acceso](#access-token). También conocido como un recurso protegido servidor o recurso aplicación.

Un servidor de recursos expone API y fuerza el acceso a sus recursos protegidos a través de [ámbitos](#scopes) y [roles](#roles), con el marco de autorización de OAuth 2.0. Por ejemplo, la API de Azure AD gráfico que proporciona acceso a datos de Azure AD inquilino y las API de Office 365 que proporcionan acceso a datos como correo y calendario. Ambas también están disponibles a través de la [API de Microsoft Graph][Microsoft-Graph].  

Como una aplicación de cliente, se establece la configuración de identidad de aplicación de recursos a través del [registro](#application-registration) en un inquilino de Azure AD, lo que proporciona la aplicación y el objeto principal del servicio. Algunas API proporcionados por Microsoft, como la API de Azure AD Graph previamente ha registrado a principales de servicio están disponibles en todos los inquilinos durante el aprovisionamiento.

## <a name="roles"></a>roles
Como [ámbitos](#scopes), roles proporcionan una manera para un [servidor de recursos](#resource-server) controlar el acceso a los recursos protegidos. Existen dos tipos: una función de "usuario" implementa el control de acceso basado en roles para los usuarios o grupos que requieren acceso al recurso, mientras que una función de "aplicación" implementa el mismo para [las aplicaciones cliente](#client-application) que requieren acceso.

Roles son cadenas definidas por el recurso (por ejemplo "aprobador de gastos," "Sólo lectura", "Directory.ReadWrite.All") y administradas en el [portal de clásica Azure] [ AZURE-classic-portal] a través de [manifiestos de aplicación](#application-manifest)del recurso y se almacenan en la del recurso [appRoles propiedad][AAD-Graph-Sp-Entity]. El portal clásico Azure también se usa para asignar a usuarios a roles de "usuario" y configurar los [permisos de aplicación](#permissions) del cliente para acceder a una función de "aplicación".

Para obtener información detallada de las funciones de aplicación expuestas por las API del Azure AD gráfico, vea [Ámbitos de permiso de Graph API][AAD-Graph-Perm-Scopes]. Para obtener un ejemplo de implementación paso a paso, vea [control de acceso en aplicaciones de nube con Azure AD basado en roles][Duyshant-Role-Blog].

## <a name="scopes"></a>ámbitos
Como [las funciones](#roles), ámbitos proporcionan una manera para un [servidor de recursos](#resource-server) controlar el acceso a los recursos protegidos. Ámbitos se utilizan para implementar [basada en ámbito] [ OAuth2-Access-Token-Scopes] control de acceso, para una [aplicación de cliente](#client-application) que se ha dado acceso delegado al recurso por su propietario.

Ámbitos son definidos por el recurso cadenas (por ejemplo "Mail.Read", "Directory.ReadWrite.All"), administradas en el [portal de clásica Azure] [ AZURE-classic-portal] a través de [manifiestos de aplicación](#application-manifest)del recurso y se almacenan en la del recurso [oauth2Permissions propiedad][AAD-Graph-Sp-Entity]. El portal clásico Azure también sirve para configurar el cliente aplicación [permisos de delegado](#permissions) para obtener acceso a un ámbito.

Una convención de nomenclatura de prácticas recomendada, es usar un formato de "resource.operation.constraint". Para obtener información detallada de los ámbitos expuestas por las API del Azure AD gráfico, vea [Ámbitos de permiso de Graph API][AAD-Graph-Perm-Scopes]. Para ámbitos expuestos por los servicios de Office 365, vea [referencia de permisos de Office 365 API][O365-Perm-Ref].

## <a name="security-token"></a>token de seguridad
Un documento firmado que contiene las notificaciones, por ejemplo, un token OAuth2 o aserción SAML 2.0. Para un OAuth2 [conceder autorización](#authorization-grant), un [token de acceso](#access-token) (OAuth2) y un [Identificador de Token](OpenID Connect) son tipos de token de seguridad, que se implementan como un [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objeto de servicio principal
Cuando se registrar o actualizar una aplicación en el [portal de clásica Azure][AZURE-classic-portal], el portal crea o actualizaciones de un [objeto de la aplicación](#application-object) y un objeto de servicio principal correspondiente para ese inquilino. El objeto de la aplicación *define* la configuración de identidad de la aplicación globalmente (a través de todos los inquilinos donde la aplicación asociada se ha concedido acceso), y es la plantilla desde la que sus objetos principal de servicio correspondientes son *derivadas* para usar localmente en tiempo de ejecución (en un inquilino específico).

Vea [objetos principales de servicio y aplicación] [ AAD-App-SP-Objects] para obtener más información.

## <a name="sign-in"></a>inicio de sesión
El proceso de una [aplicación cliente](#client-application) de iniciar la autenticación de usuario final y capturar relacionados con el estado, con el fin de adquirir un [token de seguridad](#security-token) y especificar el ámbito de la sesión de la aplicación a ese estado. Mencionar puede incluir artefactos como información de perfil de usuario e información deriva de reclamaciones tokens.

La función de inicio de sesión de una aplicación normalmente se utiliza para implementar un solo inicio de sesión único (SSO). También se puede precedido por una función "suscripción", como el punto de entrada para un usuario final obtener acceso a la aplicación (después del primer inicio de sesión). La función suscripción se usa para recopilar y conservar el estado adicional específica del usuario y puede requerir [consentimiento del usuario](#consent).

## <a name="sign-out"></a>Cierre de sesión
El proceso de autenticación sin un usuario final, desasociar el estado del usuario asociado a la sesión de la [aplicación cliente](#client-application) durante el [Inicio de sesión](#sign-in)

## <a name="tenant"></a>inquilino
Una instancia de un directorio de Azure AD se denomina un inquilino de Azure AD. Proporciona una gran variedad de características, como:

- un servicio de registro para aplicaciones integradas
- autenticación de cuentas de usuario y aplicaciones registradas
- Extremos REST necesarios para admitir varios protocolos incluidos OAuth2 y SAML, incluidos el [extremo de autorización](#authorization-endpoint), [extremo token](#token-endpoint) y el extremo "comunes" usado por [aplicaciones de múltiples usuarios](#multi-tenant-application).

Un inquilino también está asociado con un anuncio Azure o suscripción de Office 365 durante el aprovisionamiento de la suscripción, que permite la identidad y administración de acceso a características de la suscripción. Vea [cómo obtener un inquilino de Azure Active Directory] [ AAD-How-To-Tenant] para obtener más información sobre las diversas maneras puede obtener acceso a un inquilino. Vea [cómo Azure suscripciones están asociadas a Azure Active Directory] [ AAD-How-Subscriptions-Assoc] para obtener más información sobre la relación entre las suscripciones y un inquilino de Azure AD.

## <a name="token-endpoint"></a>extremo de token
Uno de los extremos implementados por el [servidor de autorización](#authorization-server) para admitir OAuth2 [concede autorización](#authorization-grant). Dependiendo de la concesión, puede usarlo para adquirir un [token de acceso](#access-token) (y relacionados token "Actualizar") a un [cliente](#client-application)o [token de identificador](#ID-token) cuando se utiliza con el [OpenID conectar] [ OpenIDConnect] protocolo.

## <a name="user-agent-based-client"></a>Cliente basada en el agente de usuario
Tipo de [aplicación cliente](#client-application) que descargas de código desde un servidor web y se ejecuta dentro de un agente de usuario (por ejemplo, un explorador web), como una aplicación de página único (SPA). Dado que todo el código se ejecuta en un dispositivo, se considera a un cliente "público" al no poder almacenar credenciales en privado o confidencial. Consulte [perfiles y tipos de cliente de OAuth2] [ OAuth2-Client-Types] para obtener más detalles.

## <a name="user-principal"></a>principal de usuario
De forma similar a como un objeto principal de servicio se utiliza para representar una instancia de aplicación, un objeto principal de usuario es otro tipo de entidad de seguridad, que representa un usuario. El gráfico de Azure AD [entidad usuario] [ AAD-Graph-User-Entity] define el esquema de un objeto de usuario, incluidas las propiedades relacionadas con el usuario, como el nombre y apellido, nombre principal de usuario, pertenencia a la función de directorio, etcetera. Proporciona la configuración de la identidad de usuario de Azure AD establecer una entidad de seguridad de usuario en tiempo de ejecución. Principal de usuario se usa para representar un usuario autenticado para inicio de sesión único, [consentimiento](#consent) delegación de grabación, realizar las decisiones de control de acceso, etcetera.

## <a name="web-client"></a>cliente de Web
Tipo de [aplicación de cliente](#client-application) que ejecuta todo el código en un servidor web y pueden funcionar como un cliente "confidencial" por almacenar sus credenciales de forma segura en el servidor. Consulte [perfiles y tipos de cliente de OAuth2] [ OAuth2-Client-Types] para obtener más detalles.

## <a name="next-steps"></a>Pasos siguientes
La [Guía del desarrollador de Azure AD] [ AAD-Dev-Guide] es el portal para todo el desarrollo de Azure AD relacionadas con temas, incluida una descripción general de [integración] de aplicaciones[ AAD-How-To-Integrate] y los conceptos básicos de [autenticación de Azure AD y escenarios de autenticación compatibles con][AAD-Auth-Scenarios].

Utilice la siguiente sección de comentarios de Disqus para proporcionar comentarios y ayudar a perfeccionar y dar forma a nuestro contenido.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
