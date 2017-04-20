<properties
    pageTitle="Limitaciones de extremo de versión 2.0 y restricciones | Microsoft Azure"
    description="Una lista de las limitaciones y restricciones con el extremo de la versión 2.0 de Azure AD."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>¿Debo usar el extremo de la versión 2.0?

Al crear aplicaciones que se integran con Azure Active Directory, debe decidir si los protocolos de punto final y la autenticación de la versión 2.0 que satisfaga sus necesidades.  El extremo de Azure AD original está siendo totalmente compatibles y en algunos casos, es más características de la versión 2.0.  Sin embargo, la versión 2.0 extremo [presenta ventajas muy significativas](active-directory-v2-compare.md) para los programadores que puede convencerle para usar el nuevo modelo de programación.

En este momento, nuestra recomendación en uso del extremo de la versión 2.0 es la siguiente:

- Si desea admitir cuentas personales de Microsoft en su aplicación, debe usar el extremo de la versión 2.0.  Pero asegúrese de que conoce las limitaciones mencionadas en este artículo, especialmente las referentes específicamente para trabajar y cuentas de la escuela.
- Si la aplicación sólo requiere trabajo auxiliar y cuentas de la escuela, debe usar [los extremos de Azure AD originales](active-directory-developers-guide.md).

Con el tiempo, el extremo de la versión 2.0 crecerá para eliminar las restricciones que se muestran aquí para que nunca debe utilizar el extremo de la versión 2.0.  Mientras tanto, este artículo está pensado para ayudarle a determinar si el punto final de la versión 2.0 es para usted.  Seguimos actualizar este artículo a lo largo del tiempo para reflejar el estado actual del extremo de la versión 2.0, así que compruebe en Atrás para volver a evaluar los requisitos con las funciones de la versión 2.0.

Si tiene una aplicación existente con Azure AD que no utilice el extremo de la versión 2.0, no hay tener que empezar desde cero.  En el futuro, se va a proporcionar una manera para poder habilitar las aplicaciones de Azure AD existentes para usar con el extremo de la versión 2.0.

## <a name="restrictions-on-apps"></a>Restricciones de aplicaciones
Los siguientes tipos de aplicaciones no son compatibles actualmente con el extremo de la versión 2.0.  Para obtener una descripción de los tipos de aplicaciones compatibles, consulte [este artículo](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>API de Web independiente
Con el extremo de la versión 2.0, tiene la capacidad para [crear una API de Web que está protegida mediante OAuth 2.0](active-directory-v2-flows.md#web-apis).  Sin embargo, dicha API Web solo podrán recibir tokens desde una aplicación que comparte el mismo identificador de aplicación.  No se admite la creación de un sitio web API que se accede desde un cliente con un Id de aplicación.  Que el cliente no podrá solicitar ni obtener permisos a su sitio web API.

Para ver cómo crear un Web API que acepte tokens desde un cliente con el mismo Id de aplicación, vea los ejemplos de Web API de extremo de versión 2.0 en la [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

##### <a name="web-api-on-behalf-of-flow"></a>API de Web en nombre de flujo
Muchos arquitecturas incluyen una API de Web que necesita llamar otra API Web descendentes, ambos protegidos por el extremo de la versión 2.0.  Este escenario es común en clientes nativos que tienen API Web back-end, que a su vez llama a un servicio en línea de Microsoft o de otro web personalizadas API que admita Azure AD.

Se admite este escenario mediante la concesión de credenciales de portador de OAuth 2.0 Jwt, también conocida como el flujo de On-Behalf-Of.  Sin embargo, el flujo en nombre de otra persona no es compatible actualmente del extremo de la versión 2.0.  Para ver cómo funciona este flujo de Azure AD disponible de forma general del servicio, consulte el [ejemplo en nombre de código en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restricciones en los registros de aplicación
En este momento, todas las aplicaciones que desean integrarlo en el extremo de la versión 2.0 deben crear un nuevo registro de aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Cualquier Azure AD existente o aplicaciones de Microsoft Account no será compatibles con el extremo de la versión 2.0, ni tampoco aplicaciones registradas en cualquier portal además el nuevo Portal de registro de aplicación.  Tenemos previsto proporciona una manera de habilitar aplicaciones existentes para usar como una aplicación de la versión 2.0. Sin embargo, en este momento hay ninguna ruta de migración para una aplicación al extremo de la versión 2.0.

Del mismo modo, aplicaciones registradas en el nuevo Portal de registro de aplicación no funcionará en el extremo de autenticación de Azure AD original.  Sin embargo, puede utilizar aplicaciones creadas en el Portal de registro de la aplicación para integrar correctamente con el extremo de autenticación de cuentas de Microsoft, `https://login.live.com`.

Además, los registros de aplicación creados en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tienen lo siguiente:

- No se admite la propiedad de la **página principal** , también conocida como la **dirección URL de inicio de sesión** .  Sin una página principal, estas aplicaciones no se mostrará en el panel mis aplicaciones de Office.
- Información confidencial de aplicación sólo dos permitidos por cada aplicación Id en este momento.
- Un registro de la aplicación solo puede verse y administrado por una cuenta de un desarrollador.  No puede compartirlo entre varios desarrolladores.
- Hay varias restricciones en el formato de redirect_uri permitidos.  Vea la siguiente sección para obtener más detalles.

## <a name="restrictions-on-redirect-uris"></a>Restricciones de URI de redirección
Aplicaciones que se han registrado en el nuevo Portal de registro de la aplicación están actualmente restringidas a un conjunto de valores de redirect_uri limitado.  El redirect_uri para aplicaciones web y servicios deben comenzar con la combinación de `https`, y todos los valores de redirect_uri deben compartir un único dominio DNS.  Por ejemplo, no es posible registrar una aplicación web que tiene redirect_uris:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

El sistema de registro compara el nombre completo de DNS de la redirect_uri existente con el nombre DNS de la redirect_uri que va a agregar. La solicitud para agregar el nombre DNS se producirá un error si se cumple alguna de las condiciones siguientes:  

- Si el nombre DNS completo de la nueva redirect_uri no coincide con el nombre DNS de la redirect_uri existente
- Si el nombre DNS completo de la nueva redirect_uri no es un subdominio de la redirect_uri existente

Por ejemplo, si la aplicación tiene actualmente redirect_uri:

`https://login.contoso.com`

A continuación, es posible agregar:

`https://login.contoso.com/new`

que coincide exactamente con el nombre DNS, o:

`https://new.login.contoso.com`

¿Cuál es un subdominio DNS de login.contoso.com.  Si desea tener una aplicación que tiene east.contoso.com de inicio de sesión y west.contoso.com de inicio de sesión como redirect_uris, a continuación, debe agregar el siguiente redirect_uris en orden:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Se pueden agregar los dos últimos porque son subdominios de la primera redirect_uri, contoso.com. Esta limitación se quitará en una versión próxima.

Para obtener información sobre cómo registrar una aplicación en el nuevo Portal de registro de aplicación, consulte [este artículo](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Restricciones de servicios y las API de
El punto final de la versión 2.0 actualmente admite iniciar sesión en cualquier aplicación registrado en el nuevo Portal de registro de aplicación, siempre que se encuentra en la lista de [autenticación compatibles con flujos](active-directory-v2-flows.md)de.  Sin embargo, estas aplicaciones solo podrá adquirir tokens de acceso OAuth 2.0 para un conjunto muy limitado de recursos.  El punto final de la versión 2.0 el tema access_tokens para:

- La aplicación que solicita el token.  Una aplicación para adquirir un access_token para sí mismo, si la aplicación lógica se compone de varios componentes diferentes o niveles.  Para ver este escenario en acción, consulte los tutoriales de [Introducción](active-directory-appmodel-v2-overview.md#getting-started) .
- El correo de Outlook, calendario y las API de REST de contactos, que se encuentran en https://outlook.office.com.  Para obtener información sobre cómo escribir una aplicación que tiene acceso a estas API, consulte estos tutoriales de [Introducción a Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- Las API de Microsoft Graph.  Para obtener información acerca de Microsoft Graph y todos los datos que están disponibles, visite [https://graph.microsoft.io](https://graph.microsoft.io).

Otros servicios de no se admiten en este momento.  Más Microsoft Online services se agregará en el futuro, así como soporte para su propio personalizado API Web integrado y servicios.

## <a name="restrictions-on-libraries--sdks"></a>Restricciones en las bibliotecas y SDK
Soporte técnico de la biblioteca del extremo de la versión 2.0 es bastante limitado en este momento.  Si desea usar el extremo de la versión 2.0 en una aplicación de producción, tiene las siguientes opciones:

- Si está creando una aplicación web, puede usar de forma segura nuestro software intermedio de servidor disponible de forma general para realizar el inicio de sesión y token de validación.  Incluyen el software intermedio OWIN abrir identificador conectar para ASP.NET y nuestro complemento NodeJS Passport.  Ejemplos de código con nuestro software intermedio están disponibles en la sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started) .
- Para otras plataformas y aplicaciones nativas y móviles, también puede integrar con el extremo de la versión 2.0 por directamente enviar y recibir mensajes de protocolo en el código de la aplicación.  La versión 2.0 OpenID conectarse y OAuth protocolos [explícitamente documentado](active-directory-v2-protocols.md) para ayudarle a realizar la integración de este tipo.
- Por último, puede usar las bibliotecas de abrir conectar ID y OAuth Abrir origen integrar con el extremo de la versión 2.0.  El protocolo de la versión 2.0 debe ser compatible con muchas bibliotecas de protocolo Abrir origen sin cambios importantes.  La disponibilidad de estas bibliotecas varía según la plataforma y de idioma y, a continuación, el sitio Web [Conectar de identificador abierto](http://openid.net/connect/) y [OAuth 2.0](http://oauth.net/2/) mantener una lista de implementaciones populares. Ver [las bibliotecas de autenticación y la versión 2.0 de Azure Active Directory (AD)](active-directory-v2-libraries.md) para obtener más detalles y la lista de bibliotecas de cliente de código abierto y ejemplos en los que se ha probado con el extremo de la versión 2.0.

También hemos publicado una vista previa inicial de la [Biblioteca de autenticación de Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) para .NET solo.  Pueden probar esta biblioteca en aplicaciones cliente y servidor de .NET pero, como una biblioteca de vista previa que no ha se provistos GA calidad admite.

## <a name="restrictions-on-protocols"></a>Restricciones de protocolos
El punto final de la versión 2.0 sólo admite OAuth 2.0 y abrir conectar de ID.  Sin embargo, no todas las características y las capacidades de cada protocolo se han incorporado en el extremo de la versión 2.0, incluidos:

- El OpenID conectar `end_session_endpoint`, lo que permite una aplicación finalizar la sesión del usuario con el extremo de la versión 2.0.
- id_tokens emitido por el extremo de la versión 2.0 sólo contienen un identificador en pares para el usuario.  Esto significa que dos aplicaciones diferentes recibirá identificadores diferentes para el mismo usuario.  Tenga en cuenta que consultando Microsoft Graph `/me` extremo, podrá obtener un identificador correlatable para el usuario que se pueden usar en todas las aplicaciones.
- id_tokens emitido por el extremo de la versión 2.0 no contienen un `email` reclamar para el usuario en este momento, incluso si adquirir permiso del usuario para ver su correo electrónico.
- El extremo de la información de usuario de OpenID conectarse. No se ha implementado en el extremo de la versión 2.0 el extremo de la información de usuario en este momento.  No obstante, todos los datos de perfil de usuario que recibiría potencialmente en este extremo está disponible desde Microsoft Graph `/me` punto final.
- Función y notificaciones de grupo.  En este momento, el extremo de la versión 2.0 no admite reclamaciones emisores de función o grupo en id_tokens.

Para entender mejor el ámbito de la funcionalidad de protocolo admitida en el extremo de la versión 2.0, lea nuestro [OpenID conectar & OAuth 2.0 protocolo referencia](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Restricciones para las cuentas de trabajo y escuela
Existen algunas características específicas de los usuarios de la organización o empresariales de Microsoft que no son compatibles con el extremo de la versión 2.0.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Acceso condicional basado en el dispositivo, aplicaciones móviles y nativas y Microsoft Graph
El extremo de la versión 2.0 aún no admite la autenticación del dispositivo para aplicaciones móviles y nativas, como nativas aplicaciones que se ejecuta en iOS o Android.  Esto podría bloquear su aplicación nativa de llamar a Microsoft Graph para ciertas organizaciones.  Dispositivo se requiere autenticación cuando un administrador establece una directiva de acceso condicional basado en el dispositivo en una aplicación.  Del extremo de la versión 2.0, el escenario más probable para acceso condicional basado en el dispositivo es un administrador de configuración de una directiva de recursos de Microsoft Graph, como la API de Outlook.  Si un administrador establece esta directiva y su aplicación nativa solicita un token de Microsoft Graph, la solicitud finalmente producirá porque aún no se admite la autenticación del dispositivo.  Aplicaciones Web que solicitar tokens a Microsoft Graph, son compatibles cuando se configuran directivas de dispositivo.  En la web se realiza autenticación de dispositivo del escenario de aplicación a través del explorador web del usuario.

Como desarrollador, es muy probable que no tienen ningún control sobre cuándo se establecen en los recursos de Microsoft Graph, o incluso tener en cuenta cuando sucede.  Si está creando una aplicación para usuarios de trabajo y escuela, debe usar [el extremo de Azure AD original](active-directory-developers-guide.md) hasta el extremo de la versión 2.0 es compatible con la autenticación del dispositivo.  Para obtener más información sobre el acceso condicional basado en el dispositivo, consulte [este artículo](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticación de Windows integrada para los inquilinos federados
Si ha usado ADAL (y, por tanto, en el extremo de Azure AD original) en las aplicaciones de Windows, puede que haya desconectado ventaja de lo que se conoce como la concesión de aserción SAML.  Esta concesión permite a los usuarios federado Azure AD inquilinos silenciosamente autenticar con su instancia de Active Directory local sin tener que escribir sus credenciales.  La concesión de aserción SAML no es compatible actualmente en el extremo de la versión 2.0.
