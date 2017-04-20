<properties
    pageTitle="El extremo de la versión 2.0 de Azure AD | Microsoft Azure"
    description="Una comparación entre la Azure AD original y los extremos de la versión 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="whats-different-about-the-v20-endpoint"></a>¿Qué es la diferencia del extremo de la versión 2.0?

Si está familiarizado con Azure Active Directory o han integrado aplicaciones con Azure AD en el pasado, es posible que algunas diferencias en el extremo de la versión 2.0 que no cabría esperar.  Este documento indica las diferencias para su comprensión.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).


## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Cuentas de Microsoft y Azure AD
el punto final de la versión 2.0 permiten a los desarrolladores escribir aplicaciones que aceptan inicio de sesión de cuentas de Microsoft Accounts y Azure AD, con un extremo auth único.  Esto le da la capacidad para escribir su aplicación completamente cuenta independiente; Puede que no conoce el tipo de cuenta que el usuario inicia sesión en con.  Por supuesto, *puede* hacer que su aplicación conocer el tipo de cuenta que se usa en una sesión determinada, pero no es necesario.

Por ejemplo, si la aplicación llama a [Microsoft Graph](https://graph.microsoft.io), algunas funciones adicionales y datos estará disponibles para los usuarios de la empresa, como sus sitios de SharePoint o los datos del directorio.  Pero, para varias acciones, como [leer el correo de un usuario](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), el código se puede escribir la misma para las cuentas de Microsoft Accounts y Azure AD.  

Integración de la aplicación con Microsoft Accounts y Azure AD ahora es un proceso simple.  Puede usar un único conjunto de puntos finales de una única biblioteca y un registro de aplicación única para obtener acceso a los consumidores y enterprise del mundo.  Para obtener más información sobre el punto final de la versión 2.0, consulte [la información general](active-directory-appmodel-v2-overview.md).


## <a name="new-app-registration-portal"></a>Nuevo portal de registro de aplicación
solo se puede registrar el punto final de la versión 2.0 en una nueva ubicación: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este es el portal de dónde puede obtener un identificador de la aplicación, personalizar el aspecto de la página de inicio de sesión de la aplicación y mucho más.  Todo lo que necesita para acceder al portal es una cuenta de Microsoft con tecnología - cuenta personal o profesional o educativa.  

Seguimos agregar más funcionalidad a este Portal de registro de la aplicación con el tiempo.  El objetivo es que este portal será la nueva ubicación donde puede ir a administrar nada y todo lo que tiene que hacer con las aplicaciones de Microsoft.


## <a name="one-app-id-for-all-platforms"></a>Un identificador de aplicación para todas las plataformas
En el servicio de Azure Active Directory original, puede haber registrado varias aplicaciones diferentes para un solo proyecto.  Había que usar registros de la aplicación independiente para los clientes nativos y aplicaciones web:

![Registro de aplicación anterior interfaz de usuario](../media/active-directory-v2-flows/old_app_registration.PNG)

Por ejemplo, si ha creado un sitio Web y una aplicación de iOS, había que registre por separado, con dos diferentes ID de aplicación.  Si tiene un sitio Web y back-end web api, podría ha registrado cada uno como una aplicación independiente de Azure AD.  Si tiene una aplicación de iOS y una aplicación Android, también podría ha registrado dos aplicaciones diferentes.  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Ahora, todo lo que necesita es un registro de aplicación única y un identificador de aplicación única para cada uno de los proyectos.  Puede agregar varios "plataformas" a cada proyecto y proporcionar los datos adecuados para cada plataforma que agrega.  Por supuesto, puede crear tantas aplicaciones como desee según sus necesidades, pero para la mayoría de los casos, solo un identificador de la aplicación debería ser necesario.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Nuestro objetivo es este llevar a una experiencia de desarrollo y administración de aplicación más simplificado y crear una vista más consolidada de un solo proyecto que podría estar trabajando en.


## <a name="scopes-not-resources"></a>Ámbitos, no los recursos
En el servicio de Azure AD original, puede comportarse de una aplicación como un **recurso**o un destinatario de símbolos.  Un recurso puede definir un número de **ámbitos** o **oAuth2Permissions** que comprende, permitiendo aplicaciones solicitar tokens a ese recurso para un conjunto determinado de ámbitos de cliente.  Tenga en cuenta la API de Azure AD gráfico como un ejemplo de un recurso:

- Identificador de recursos, o `AppID URI`:`https://graph.windows.net/`
- Ámbitos, o `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etcetera.  

Todo esto sucede en el extremo de la versión 2.0.  Aún puede comportarse de una aplicación como recurso, definir ámbitos e identificarse por un URI.  Aplicaciones cliente aún pueden solicitar acceso a los ámbitos.  Sin embargo, ha cambiado la forma en que un cliente solicita los permisos.  En el pasado, autorizar un 2.0 OAuth solicitud de Azure AD posible que haya tenía el siguiente aspecto:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

Cuando el parámetro de **recurso** indica qué recursos de la aplicación cliente solicita autorización.  Azure AD calcula los permisos necesarios para que la aplicación en función de la configuración estática en el Portal de Azure y emite tokens según corresponda.  Ahora, la misma versión OAuth 2.0 autorizar solicitud parece que:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

donde el parámetro de **ámbito** indica qué recursos y permisos de la aplicación está solicitando autorización. El recurso deseado aún es muy presente en la solicitud, simplemente esté incluido en cada uno de los valores de parámetro de ámbito.  Usar el parámetro de ámbito de esta manera permite que sea compatible con la especificación de OAuth 2.0 el extremo de la versión 2.0 y alinea más de cerca con prácticas comunes de la industria.  También permite aplicaciones realizar [consentimiento incremental](#incremental-and-dynamic-consent), que se describe en la siguiente sección.

## <a name="incremental-and-dynamic-consent"></a>Consentimiento incremental y dinámica
Aplicaciones registrado en Azure AD disponible de forma general es necesario para especificar los permisos de OAuth 2.0 necesarios en el Portal de Azure, durante la creación de la aplicación de servicio:

![Interfaz de usuario de registro de permisos](../media/active-directory-v2-flows/app_reg_permissions.PNG)

Los permisos de una aplicación obligatoria estaba configuran **estática**.  Aunque esta configuración de la aplicación que existe en el Portal de Azure permitidos y mantiene el código bueno y simple, presenta algunos problemas para desarrolladores:

- Una aplicación tenía que conoce la totalidad de los permisos que podría necesitar durante la creación de la aplicación.  Agregar permisos a lo largo del tiempo era un proceso difícil.
- Una aplicación tenía que conoce la totalidad de los recursos nunca obtendrá acceso a antes de tiempo.  Era difícil crear aplicaciones que pueden tener acceso a un número arbitrario de recursos.
- Una aplicación tenía que solicitar todos los permisos que nunca tendría al iniciar primera sesión del usuario.  En algunos casos esto llevado a una lista muy larga de permisos, que no se recomienda los usuarios finales de aprobación de acceso de la aplicación en el inicio de sesión inicial.

Con el extremo de la versión 2.0, puede especificar los permisos que su aplicación necesita **dinámicamente**, en tiempo de ejecución durante el uso normal de la aplicación.  Para ello, puede especificar los ámbitos de su aplicación necesita en cualquier momento determinado en el tiempo incluirlos en el `scope` parámetros de una solicitud de autorización:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

El permiso de solicitudes anteriores para la aplicación leer los datos del directorio de un usuario de Azure AD, así como escribir datos en su directorio.  Si el usuario ha aceptado los permisos en el pasado para esta aplicación en particular, simplemente se introduzca sus credenciales y haber iniciado sesión en la aplicación.  Si el usuario no ha aceptado a cualquiera de estos permisos, el extremo de la versión 2.0 pídale al usuario su consentimiento para los permisos.  Para obtener más información, puede leer hasta en [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md).

Permitir una aplicación para solicitar permisos dinámicamente a través de la `scope` parámetro proporciona un control completo sobre la experiencia del usuario.  Si lo desea, puede elegir a frontload su consentimiento experiencia y pregunte para todos los permisos en una solicitud de autorización inicial.  O bien, si su aplicación requiere una gran cantidad de permisos, puede reunir incrementalmente, los permisos del usuario que intenten usar determinadas características de la aplicación a lo largo del tiempo.

## <a name="well-known-scopes"></a>Ámbitos conocidos

#### <a name="offline-access"></a>Acceso sin conexión
el punto final de la versión 2.0 puede requerir el uso de un nuevo permiso conocido para aplicaciones - la `offline_access` ámbito.  Todas las aplicaciones, debe solicitar este permiso si necesitan tener acceso a los recursos en el nombre de un usuario durante un período prolongado de tiempo, incluso cuando el usuario que no esté activa utilizando la aplicación.  La `offline_access` ámbito aparecerá al usuario en los cuadros de diálogo de consentimiento como "Obtener acceso a los datos sin conexión", que el usuario debe Aceptar.  Solicitar la `offline_access` permisos permitirán la aplicación web de recibir refresh_tokens OAuth 2.0 desde el extremo de la versión 2.0.  Refresh_tokens son larga y largos períodos de access se pueden cambiar de nuevo access_tokens OAuth 2.0.  

Si su aplicación no se solicita la `offline_access` ámbito, no recibirá refresh_tokens.  Esto significa que cuando canjear un authorization_code en el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), sólo recibirá vuelva un access_token desde el `/token` punto final.  Esa access_token permanecerá válida durante un breve período de tiempo (normalmente una hora), pero caducará al final.  Volver a partir de ese momento, la aplicación necesitará redirigir al usuario a la `/authorize` extremo para recuperar una nueva authorization_code.  Durante este redireccionamiento, el usuario puede o no posible que tenga que escribir sus credenciales de nuevo o volver a consentimiento a permisos, según el tipo de aplicación.

Para obtener más información sobre OAuth 2.0, refresh_tokens y access_tokens, consulte la [referencia de protocolo de versión 2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile--email"></a>OpenID, perfil y correo electrónico

En el servicio de Azure Active Directory original, el más OpenID conectar inicio de sesión de flujo básico proporciona un amplio abanico de información sobre el usuario en el id_token resultante.  Nombre del usuario, preferido de nombre de usuario, dirección de correo electrónico, el identificador de objeto y más, pueden incluir las notificaciones en un id_token.

Ahora estamos restringir la información que el `openid` permite el acceso de la aplicación a un ámbito.  El ámbito 'openid' solo permitir la aplicación de sesión del usuario en y recibir un identificador específico de la aplicación para el usuario.  Si desea obtener información de identificación personal (PII) sobre el usuario en la aplicación, la aplicación necesitará solicitar permisos adicionales del usuario.  Presentamos dos nuevos ámbitos: la `email` y `profile` ámbitos – que permita hacerlo.

La `email` ámbito es muy sencillo: permite el acceso de la aplicación a la dirección de correo electrónico principal del usuario a través de la `email` reclamar en la id_token.  La `profile` Id. de objeto de ámbito permite el acceso de la aplicación a todos los demás información básica sobre el usuario: su nombre, el nombre de usuario preferido y así sucesivamente.

Esto le permite a su aplicación de forma mínima revelación de código: solo puede preguntar al usuario para el conjunto de información que la aplicación se necesita para hacer su trabajo.  Para obtener más información sobre estos ámbitos, consulte [la referencia de ámbito de la versión 2.0](active-directory-v2-scopes.md). 

## <a name="token-claims"></a>Notificaciones de tokens

Las notificaciones de tokens emitidos por el extremo de la versión 2.0 no es idénticas a tokens emitidos por lo general, disponible extremos de Azure AD - aplicaciones migrar al nuevo servicio no deben suponer una reclamación determinado se existe en id_tokens o access_tokens.   Tokens emitidos por el extremo de la versión 2.0 son compatibles con las especificaciones OAuth 2.0 y conectar OpenID, pero puede seguir semántica diferente a generalmente disponibles servicio de Azure AD.

Para obtener información sobre las notificaciones específicas emitidas en tokens versión 2.0, vea la [referencia de token de versión 2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Limitaciones
Hay algunas restricciones para tener en cuenta al usar el punto de la versión 2.0.  Consulte el [documento de las limitaciones de versión 2.0](active-directory-v2-limitations.md) para ver si cualquiera de estas restricciones se aplican a su caso particular.
