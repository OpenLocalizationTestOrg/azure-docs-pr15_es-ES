<properties
   pageTitle="Cómo crear una aplicación que puede iniciar sesión en cualquier usuario de Azure Active Directory | Microsoft Azure"
   description="Paso a paso las instrucciones para crear una aplicación que pueden iniciar sesión en un usuario desde cualquier inquilino de Azure Active Directory, también conocido como una aplicación de varios inquilino."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Cómo iniciar sesión en cualquier usuario de Azure Active Directory (AD) mediante el modelo de aplicación de múltiples arrendatarios
Si ofrece un Software como una aplicación de servicio para muchas organizaciones, puede configurar la aplicación para aceptar inicios de sesión de cualquier inquilino de Azure AD.  En Azure AD se denomina realizar su inquilino de múltiples de aplicación.  Los usuarios en cualquier inquilino de Azure AD podrán iniciar sesión en la aplicación después de dar su consentimiento para utilizar su cuenta con la aplicación.  

Si tiene una aplicación existente que tiene su propio sistema de cuenta o es compatible con otros tipos de inicio de sesión de otros proveedores de nube, agregar Azure AD de inicio de sesión de cualquier inquilino es tan sencillo como registrar la aplicación, agregar inicio de sesión en el código a través OAuth2, OpenID conectar o SAML y colocar un signo en con el botón de Microsoft en su aplicación. Haga clic en el botón para obtener más información sobre la aplicación de la marca.

[! [Inicio de sesión botón] [AAD-sesión]][AAD-App-Branding]


En este artículo se supone que ya está familiarizado con la creación de una aplicación de inquilinos solo para Azure AD.  Si no, cabeza hasta la [página principal de guía de desarrollador] [ AAD-Dev-Guide] y pruebe una de nuestras guías de inicio rápidos.

Hay cuatro pasos sencillos para convertir la aplicación a una aplicación de varios inquilino de Azure AD:

1.  Actualizar el registro de aplicación para que sea múltiples arrendatarios
2.  Actualizar el código para enviar convocatorias a la/Common extremo 
3.  Actualizar el código para controlar varios valores de emisor
4.  Comprender el consentimiento de usuario y la administración y realizar cambios de código adecuado

Vamos a analizar cada paso en detalle. También puede saltar directamente a [esta lista de muestras de múltiples arrendatarios][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Actualizar registro sea múltiples arrendatarios
De forma predeterminada, los registros de aplicación o API de web en Azure AD son solo inquilino.  Puede realizar su registro múltiples arrendatarios buscando el modificador "Aplicación es múltiple inquilino" en la página Configuración de registro de la aplicación en el [portal de clásica Azure] [ AZURE-classic-portal] y si se establece en "Sí".

Nota: Antes de una aplicación, se puede realizar varios inquilino Azure AD requiere la URI de identificador de aplicación de la aplicación que sea único global. El URI de ID de aplicación es una de las formas de que una aplicación se identifica en mensajes de protocolo.  Para una aplicación única inquilinos, es suficiente para el URI de ID de aplicación a ser único dentro de ese inquilino.  Para una aplicación de múltiples arrendatario, deben ser único para que Azure AD puede encontrar la aplicación a través de todos los inquilinos.  Requerir URI de ID de aplicación que tiene un nombre de host que coincida con un dominio comprobado del inquilino de Azure AD exige exclusividad global.  Por ejemplo, si el nombre de su inquilino era contoso.onmicrosoft.com, a continuación, válido URI de ID de aplicación sería `https://contoso.onmicrosoft.com/myapp`.  Si su inquilino tenía un dominio comprobado de `contoso.com`, también sería un URI de identificador de aplicación válido `https://contoso.com/myapp`.  Configuración de una aplicación como múltiples arrendatarios se producirá un error si el URI de ID de aplicación no sigue este patrón.

Los registros de cliente nativo son varios inquilinos de forma predeterminada.  No es necesario realizar ninguna acción para realizar un nativo inquilino múltiple de registro de aplicación de cliente.

## <a name="update-your-code-to-send-requests-to-common"></a>Actualizar el código para enviar convocatorias a/Common
En una aplicación única inquilino, las solicitudes de inicio de sesión se envía al extremo de inicio de sesión del inquilino.   Por ejemplo, para contoso.onmicrosoft.com el extremo sería:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Las solicitudes enviadas al extremo del inquilino pueden iniciar sesión los usuarios (o invitados) en ese inquilino a las aplicaciones de ese inquilino.  Con una aplicación de múltiples arrendatario, la aplicación no sabe adelantado qué inquilino es el usuario, por lo que no puede enviar solicitudes al extremo del inquilino.  En su lugar, las solicitudes se envían a un extremo que multiplexes a través de los inquilinos todo Azure AD:

    https://login.microsoftonline.com/common

Cuando Azure AD recibe una solicitud en la/Common extremo, se inicie la sesión de usuario y consecuencia descubre qué inquilino es el usuario de.  La/extremo comunes funciona con todos los protocolos de autenticación compatibles con Azure AD: conectar OpenID, OAuth 2.0, SAML 2.0 y WS-Federation.

Respuesta a continuación, la aplicación de inicio de sesión contiene un símbolo que representa el usuario.  El valor de emisor del token indica a una aplicación qué inquilino es el usuario de.  Cuando se devuelve una respuesta de la/Common extremo, el valor de emisor del token se corresponde a inquilinos del usuario.  Es importante que tenga en cuenta la/Common extremo no es un inquilino y no es un emisor, es simplemente un multiplexor.  Cuando se utiliza/Common, la lógica de la aplicación para validar tokens debe actualizarse para tenerlo en cuenta. 

Como se mencionó anteriormente, aplicaciones de múltiples arrendatarios también deben proporcionar una experiencia de inicio de sesión coherente para los usuarios, tras la aplicación de Azure AD directrices de marca. Haga clic en el botón para obtener más información sobre la aplicación de la marca.

[! [Inicio de sesión botón] [AAD-sesión]][AAD-App-Branding]

Echemos un vistazo al uso de la/Common extremo y la implementación de código con más detalle.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Actualizar el código para controlar varios valores de emisor
Aplicaciones Web y web API recibirán y validar tokens de Azure AD.  

> [AZURE.NOTE] Mientras las aplicaciones cliente nativo soliciten y reciban tokens de Azure AD, lo hacen para enviarlos a la API, donde validadas.  Aplicaciones nativas no validan tokens y deben considerar a ellos opaco.

Echemos un vistazo a cómo una aplicación valida tokens que recibe de Azure AD.  Una aplicación de inquilinos solo normalmente tendrá un valor de extremo como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

y usarlo para crear una dirección URL de metadatos (en este caso, conectar OpenID) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Para descargar dos partes esenciales de información que se usan para validar tokens: el inquilino de firma de claves y valor emisor.  Cada inquilino de Azure AD tiene un valor único emisor del formulario:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

Cuando el valor GUID es la versión de seguros de cambiar el nombre de los identificadores de inquilinos del inquilino.  Si hace clic en el vínculo de metadatos encima de `contoso.onmicrosoft.com`, puede ver este valor emisor en el documento.

Cuando una aplicación de inquilinos solo valida un token, comprueba la firma del token de las teclas de firma del documento de metadatos y se asegura de que el valor emisor del token coincida con el que se ha encontrado en el documento de metadatos.

Desde la/Common extremo no se corresponde con un inquilino y no es un emisor cuando examine el valor de emisor en los metadatos de/comunes tiene una dirección URL con plantillas en lugar de un valor real:

    https://sts.windows.net/{tenantid}/

Por lo tanto, una aplicación de múltiples arrendatario no puede validar tokens comparando el valor de emisor en los metadatos con la `issuer` valor del token.  Una aplicación de múltiples arrendatario lógica para decidir qué valores del emisor son válidas y qué son no es necesario, según el inquilino parte del ID del valor emisor.  

Por ejemplo, si una aplicación de múltiples arrendatario solo permite iniciar sesión de inquilinos específicos que se hayan registrado para su servicio, a continuación, debe comprobar el valor de emisor o la `tid` el valor del token para asegurarse de que ese inquilino está en su lista de los suscriptores de notificación.  Si una aplicación de múltiples arrendatario sólo se ocupa de personas y no las decisiones de acceso basadas en los inquilinos, a continuación, puede pasar por alto el valor emisor totalmente.

En los ejemplos de varios inquilinos que encontrará en la sección [Relacionada contenido](#related-content) al final de este artículo, validación del emisor está deshabilitado para habilitar los inquilinos de Azure AD iniciar sesión.

Ahora vamos a ver la experiencia del usuario para los usuarios que están iniciando sesión en el inquilinos varias aplicaciones.

## <a name="understanding-user-and-admin-consent"></a>Usuario descripción y consentimiento del administrador
Para que un usuario iniciar sesión en una aplicación en Azure AD, la aplicación debe estar representada en inquilino del usuario.  Esto permite hacer cosas como aplicar directivas únicas cuando los usuarios de su inquilino de iniciar sesión en la aplicación de la organización.  Para una aplicación de inquilinos solo este registro es sencillo; es lo que sucede cuando se registre la aplicación en el [portal de clásica Azure][AZURE-classic-portal].

Para una aplicación de múltiples arrendatario, el registro inicial para la aplicación reside en el inquilino de Azure AD usado por el programador.  Cuando un usuario de un inquilino diferentes inicia sesión en la aplicación por primera vez, Azure AD les pide que consentimiento para los permisos solicitados por la aplicación.  Si acepta, a continuación, se crea una representación de la aplicación denominada *principal del servicio* en el inquilino de usuario y continuar con el inicio de sesión. También se crea una delegación en el directorio que registra el consentimiento del usuario a la aplicación. Vea [objetos de aplicación y servicio Principal] [ AAD-App-SP-Objects] para obtener más información sobre de la aplicación objetos de la aplicación y ServicePrincipal, y cómo se relacionan entre sí.

![Consentimiento a la aplicación de un solo nivel][Consent-Single-Tier] 

Esta experiencia consentimiento se verá afectada por los permisos solicitados por la aplicación.  Azure AD es compatible con dos tipos de permisos, solo aplicación y delegados:

- Un delegado permiso concede a una aplicación la capacidad para que actúe como un usuario iniciado en un subconjunto de las cosas que el usuario puede hacer.  Por ejemplo, una aplicación puede conceder el permiso delegado para leer el firmado en el calendario del usuario.
- Se concede un permiso de aplicación directamente a la identidad de la aplicación.  Por ejemplo, una aplicación puede conceder el permiso solo aplicación para leer la lista de usuarios en un inquilino y podrá hacerlo con independencia de que ha iniciado sesión en la aplicación.

Algunos permisos pueden ser aceptados por un usuario habitual, mientras que otras necesitan consentimiento del Administrador de inquilinos. 

### <a name="admin-consent"></a>Consentimiento del administrador
Permisos de aplicación sólo siempre requieren consentimiento del Administrador de inquilinos.  Si la aplicación solicita un permiso de aplicación y un usuario normal intenta iniciar sesión en la aplicación, la aplicación recibirá un mensaje de error que indica que el usuario no consigue consentimiento.

Ciertos permisos delegados requieren también el consentimiento del Administrador de inquilinos.  Por ejemplo, la capacidad de volver a escribir en Azure AD como el usuario sesión requiere consentimiento del Administrador de inquilinos.  Como los permisos de aplicación solo si un usuario normal intenta iniciar sesión una aplicación que solicita un permiso delegado que requiere consentimiento del administrador, la aplicación recibirá un error.  Si no requiere un permiso de consentimiento de administración determinado por el programador que publicó el recurso y se pueden encontrar en la documentación para el recurso.  Vínculos a temas que describen los permisos disponibles para la API de Azure AD Graph y la API de Microsoft Graph están en la sección [Contenido relacionado](#related-content) de este artículo.

Si su aplicación usa los permisos que requieren autorización de administrador, debe tener un gesto de la aplicación como un botón o vínculo donde el administrador puede iniciar la acción.  La convocatoria de la aplicación que se envía para esta acción es una solicitud de autorización de OAuth2/OpenID conectar habitual, pero que también incluye la `prompt=admin_consent` parámetro de cadena de consulta.  Una vez que el administrador ha aceptado y se crea la entidad de seguridad de servicio en el inquilino del cliente, las solicitudes de inicio de sesión subsiguientes no necesita la `prompt=admin_consent` parámetro.   Dado que el administrador haya decidido que los permisos solicitados son aceptables, ningún otro usuario en el inquilino le pedirá su consentimiento a partir de ese punto.

La `prompt=admin_consent` parámetro también puede ser usado por aplicaciones que se solicitan permisos que no requieren autorización de administración, pero quiere proporcionar una experiencia donde el Administrador de inquilinos "registra" de la aplicación una vez y, a continuación, ningún otro usuario le pide permiso para consentimiento desde ese punto en.

Si una aplicación requiere consentimiento del administrador y, a continuación, el administrador inicia sesión en la aplicación, pero la `prompt=admin_consent` parámetro no se envía, el administrador podrá consentimiento correctamente a la aplicación, pero solo se consentimiento para su cuenta de usuario.  Los usuarios normales aún no podrá iniciar sesión y consentimiento a la aplicación.  Esto es útil si desea asignar al administrador de inquilinos la capacidad para explorar la aplicación antes de permitir que a otros usuarios de access.

Un administrador de inquilinos puede deshabilitar la posibilidad de que los usuarios normales consentimiento para las aplicaciones.  Si esta capacidad está deshabilitada, siempre es necesario para la aplicación se pueden configurar en el inquilino consentimiento del administrador.  Si desea probar la aplicación con consentimiento de usuario habitual deshabilitado, encontrará el modificador de configuración en el inquilino de Azure AD sección de configuración del [portal clásica Azure][AZURE-classic-portal].

> [AZURE.NOTE] Algunas aplicaciones desea una experiencia donde los usuarios normales pueden consentimiento inicialmente, y más adelante, la aplicación puede implicar los permisos de administrador y solicite que requieren autorización de administrador.  No hay ninguna manera de hacerlo con el registro de una sola aplicación en Azure AD hoy.  La próxima extremo de Azure AD v2 permitirá aplicaciones para solicitar permisos en tiempo de ejecución en lugar de en tiempo de registro, que le permitirá este escenario.  Para obtener más información, consulte la [Guía del programador de modelo de aplicación de Azure AD v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplicaciones de consentimiento y de varios niveles
La aplicación puede tener varios niveles, cada uno representado por su propio registro en Azure AD.  Por ejemplo, una aplicación nativa que llama a una API de web o una aplicación web que llama a un sitio web API.  En ambos casos, el cliente (aplicación nativa o web app) solicita permisos para llamar a los recursos (web API).  Para que el cliente dado su consentimiento correctamente en el inquilino del cliente, todos los recursos a las solicitudes de permisos ya deben existir en el inquilino del cliente.  Si no se cumple esta condición, Azure AD devolverán un error que se debe agregar el recurso en primer lugar.

Esto puede ser un problema si su aplicación lógica consta de dos o más registros aplicación, por ejemplo, un cliente independiente y recursos.  ¿Cómo obtengo el recurso en el inquilino del cliente primera?  Azure AD cubre este caso habilitando cliente y recursos para ser aceptado en un solo paso, donde el usuario ve la suma total de los permisos solicitados por el cliente y los recursos en la página consentimiento.  Para habilitar este comportamiento, el registro de aplicación del recurso debe incluir el identificador de aplicación del cliente como un `knownClientApplications` en su manifiesto de aplicación.  Por ejemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Esta propiedad puede actualizarse a través del recurso [manifiesto de la aplicación][AAD-App-Manifest]y se muestra en un cliente nativo de varios niveles que llama ejemplo web API en la sección [Relacionada contenido](#related-content) al final de este artículo. El diagrama siguiente proporciona una descripción general de consentimiento para una aplicación de varios niveles:

![Consentimiento a la aplicación cliente conocidos de varios niveles][Consent-Multi-Tier-Known-Client] 

Un caso similar ocurre si se registran los diferentes niveles de una aplicación en diferentes inquilinos.  Por ejemplo, considere el caso de crear una aplicación de cliente nativo que llama a la API de Office 365 Exchange Online.  Para desarrollar a aplicaciones y versiones posterior para la aplicación nativa se ejecute en el inquilino del cliente nativo, la principal del servicio Exchange Online debe estar presente.  En este caso el cliente debe comprar Exchange Online para el servicio principal que se creen en su inquilino.  En el caso de una API creada por una organización que no sea de Microsoft, el desarrollador de la API debe proporcionar una manera de sus clientes al consentimiento su aplicación en un inquilino del cliente, por ejemplo, una página web que unidades consentimiento mediante los mecanismos descritos en este artículo.  Después de crea la entidad de seguridad de servicio en el inquilino, la aplicación nativa puede obtener tokens para la API.

El diagrama siguiente proporciona información general de consentimiento para una aplicación de varios niveles registrada en inquilinos diferentes:

![Consentimiento a la aplicación de varias parte de varios niveles][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revocar consentimiento
Los usuarios y administradores pueden revocar consentimiento a la aplicación en cualquier momento:

- Los usuarios revocar el acceso a las aplicaciones individuales quitándolos de sus [Aplicaciones del Panel de acceso] [ AAD-Access-Panel] lista.
- Los administradores revocar el acceso a aplicaciones quitándolos de Azure AD utilizando la sección Administración de Azure AD del [portal clásica Azure][AZURE-classic-portal].

Si un administrador consiente a una aplicación para todos los usuarios en un inquilino, los usuarios no pueden revocar el acceso de la forma individual.  Solo el administrador puede revocar el acceso y solo para toda la aplicación.

### <a name="consent-and-protocol-support"></a>Consentimiento y soporte técnico de protocolo
Consentimiento es compatible con Azure AD a través de la OAuth, OpenID conectarse, WS-Federation y protocolos SAML.  No se admiten los protocolos SAML y WS-Federation la `prompt=admin_consent` parámetro, por lo que solo es posible a través de OAuth y conectar OpenID consentimiento del administrador.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicaciones de múltiples arrendatarios y almacenamiento en caché Tokens de acceso
Aplicaciones de múltiples arrendatarios también pueden obtener tokens de acceso para llamar a las API que están protegidas por Azure AD.  Un error común al uso de la biblioteca de autenticación de Active Directory (ADAL) con una aplicación de múltiples arrendatario es inicialmente solicitar un símbolo para un usuario con/Common, recibe una respuesta y, a continuación, solicitar un token subsiguientes para ese mismo usuario también usa/Common.  Dado que la respuesta de Azure AD procede de un inquilino no/comunes, ADAL almacena el token como desde el inquilino. La siguiente llamada a/Common para obtener un token de acceso para el usuario pierde la entrada de caché y el usuario se le pide que vuelva a iniciar sesión.  Para evitar la pérdida de la memoria caché, asegúrese de que las llamadas siguientes para un usuario ya iniciada en realizados en el extremo del inquilino.

## <a name="related-content"></a>Contenido relacionado

- [Ejemplos de aplicaciones de múltiples arrendatarios][AAD-Samples-MT]
- [Directrices de personalización de marca para aplicaciones][AAD-App-Branding]
- [Guía del desarrollador de Azure AD][AAD-Dev-Guide]
- [Objetos de la aplicación y servicio Principal][AAD-App-SP-Objects]
- [Integración de aplicaciones con Azure Active Directory][AAD-Integrating-Apps]
- [Información general sobre el marco de trabajo de consentimiento][AAD-Consent-Overview]
- [Ámbitos de permiso de la API de Microsoft Graph][MSFT-Graph-AAD]
- [Gráfico de Azure AD API ámbitos de permiso][AAD-Graph-Perm-Scopes]

Utilice la sección de comentarios de Disqus siguiente para proporcionar comentarios y ayudar a perfeccionar y dar forma a nuestro contenido.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














