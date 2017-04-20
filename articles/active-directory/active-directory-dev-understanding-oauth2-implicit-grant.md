<properties
   pageTitle="Descripción de la OAuth2 implícitos conceder el flujo de Azure Active Directory | Microsoft Azure"
   description="Obtener más información acerca de la implementación de Azure Active Directory de la OAuth2 implícitos concesión flujo, y si es adecuado para la aplicación."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Descripción del flujo de conceder implícitos OAuth2 en Azure Active Directory (AD)

La concesión implícita OAuth2 es famoso para ser la concesión con la lista más larga de problemas de seguridad en la especificación de OAuth2. Y todavía, que es el enfoque implementado JS ADAL y lo que recomendamos al escribir aplicaciones SPA. ¿Qué pasa? Se trata todas las de compensaciones: y resulta, la concesión implícita es la mejor solución que puede llevar a cabo para las aplicaciones que consumen una API de Web a través de JavaScript desde un explorador.

## <a name="what-is-the-oauth2-implicit-grant"></a>¿Qué es la concesión implícita OAuth2?

El [código de autorización de OAuth2 conceder](https://tools.ietf.org/html/rfc6749#section-1.3.1) típico es la concesión de autorización que usa dos extremos independientes. Se usa el extremo de autorización para la fase de interacción de usuario, que da como resultado un código de autorización. El extremo del token, a continuación, se utiliza el cliente para intercambiar el código de un token de acceso y a menudo también un símbolo de actualización. Aplicaciones Web son necesarias para presentar sus propias credenciales de aplicación al extremo token, para que el servidor de autorización puede autenticar al cliente.

El [OAuth2 implícito conceder](https://tools.ietf.org/html/rfc6749#section-1.3.2) es un valor de tipo variant que permite a un cliente obtener un token de acceso (y id_token, en el caso de [Conectar OpenId](http://openid.net/specs/openid-connect-core-1_0.html)) directamente desde el extremo de autorización, sin ponerse en contacto con el extremo de token ni autenticación de la aplicación cliente. Esta variante se ha diseñado específicamente para JavaScript basado en aplicaciones que se ejecutan en un explorador Web: en la especificación de OAuth2 original, se devuelven los tokens en un fragmento de URI. Hace el tokens bits esté disponible en el código de JavaScript en el cliente, pero garantiza que no se incluirán en redireccionamientos hacia el servidor. Devolver tokens a través del navegador redirige directamente desde el extremo de autorización. También tiene la ventaja de eliminar los requisitos para cruzada llamadas de origen, que son necesarios si la aplicación de JavaScript es necesario ponerse en contacto con el extremo de token.

Una característica importante de la concesión implícita OAuth2 es el hecho de que dicha fluye tokens actualización nunca devuelto al cliente. Como se verá en la siguiente sección, que no es necesario realmente y de hecho sería un problema de seguridad.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Escenarios adecuados para la concesión implícita OAuth2

Como la especificación de OAuth2 propio declara, la concesión implícita se han resultado para habilitar aplicaciones de agente de usuario, es decir, aplicaciones de JavaScript que se ejecuta dentro de un explorador. La característica que define dichas aplicaciones es que el código JavaScript se usa para obtener acceso a recursos de servidor (normalmente, Web API) y para actualizar la aplicación UX según corresponda. Piense en aplicaciones como Gmail o Outlook Web Access: cuando seleccione un mensaje de la Bandeja de entrada, sólo el panel de visualización de mensaje cambia para mostrar la nueva selección, mientras que el resto de la página permanece sin modificar. Esto es a diferencia de las tradicionales aplicaciones Web basadas en redirigir y, donde cada interacción de usuario de resultados en una devolución de datos de página completa y una representación de página completa de la respuesta del servidor de nuevo.

Aplicaciones que llevar el enfoque de JavaScript basado en su extremo se denominan único de página de aplicaciones o SPAs: la idea es que esas aplicaciones solo servir una página inicial de HTML y JavaScript asociado, con todas las interacciones subsiguientes controladas por llamadas a la API de Web realizan mediante JavaScript. Sin embargo, enfoques de implementación híbrida, donde la aplicación es principalmente basados en devolución de datos, pero realiza ocasionales llamadas JS, no son poco comunes: la discusión sobre el uso del flujo implícita también es relevante para aquellos.

Aplicaciones basadas en Redirect normalmente seguro sus solicitudes a través de las cookies, sin embargo, que enfoque no funciona así para las aplicaciones de JavaScript. Las cookies sólo funcionan con el dominio que se han generado, mientras que las llamadas de JavaScript se podrían dirigidas a otros dominios. De hecho, que con frecuencia será el caso: piense en aplicaciones invocar API de Azure API de Microsoft Graph, API de Office, todos los que se encuentran fuera del dominio desde donde se sirve la aplicación. Una tendencia creciente para las aplicaciones de JavaScript es que no haya ningún back-end, confiar 100% en parte 3ª Web API implementar su función de negocio.

Actualmente, el método preferido de protección de llamadas a API de Web es usar el enfoque token portador de OAuth2, donde cada llamada se acompaña de un token de acceso de OAuth2. La API de Web examina el token de acceso entrante y, si se encuentra en él los ámbitos es necesarios, conceda acceso a la operación. El flujo implícito proporciona cómodamente para aplicaciones de JavaScript para obtener tokens de acceso para una API de Web ofrece numerosas ventajas con respecto a las cookies:

- Pueden obtener tokens confiable sin necesidad de entre llamadas de origen: registro obligatorio de la redirección URI a los que están devueltos tokens garantiza que no se moverán tokens
- Aplicaciones de JavaScript pueden obtener tantas tokens de acceso que necesitan para las API Web de destino: con ninguna restricción en dominios
- Características de HTML5 como sesión o almacenamiento local conceder el control total sobre el almacenamiento en caché token y administración de la duración, mientras que la administración de cookies es opaca a la aplicación
- Símbolos de acceso no susceptibles a ataques de falsificación (CSRF) de solicitud entre sitios

El flujo de conceder implícitos no emite tokens de actualización, principalmente por motivos de seguridad. Un símbolo de actualización no ámbito como concretamente tokens de acceso, otorgar power mucho más inflicting, por tanto, mucho más daños en caso de que se filtran. En el flujo de implícito tokens se entregarán en la dirección URL, por lo tanto, el riesgo de interceptación es muy superior de la concesión de autorización de código.

Sin embargo, tenga en cuenta que una aplicación de JavaScript tiene otro mecanismo a su disposición para renovar tokens de acceso sin preguntar al usuario las credenciales de varias veces. La aplicación puede usar un iframe oculto para realizar nuevas solicitudes de tokens contra el extremo de autorización de Azure AD: siempre y cuando el explorador aún tiene una sesión activa (leer: tiene una cookie de sesión) para el dominio de Azure AD, la solicitud de autenticación puede producirse correctamente sin necesidad de interacción con el usuario. 

En este modelo concede a la aplicación de JavaScript la capacidad de forma independiente renovar tokens de acceso e incluso adquirir nuevos para una nueva API (siempre que el usuario ha dado su consentimiento para ellos. Esto evita la carga agregada de adquirir, mantener y proteger una muestra de alto valor, como un símbolo de actualización. Muestra lo que permite la renovación silenciosa, la cookie Azure AD, se administra fuera de la aplicación. Otra ventaja de este enfoque es que un usuario puede cerrar sesión de Azure AD, con cualquiera de las aplicaciones que ha iniciado sesión en Azure AD, ejecute en cualquiera de las pestañas de explorador. El resultado es la eliminación de la cookie Azure AD y la aplicación de JavaScript pierde automáticamente la capacidad de renovar tokens para el usuario firmado fuera.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>¿Es la concesión implícita adecuado para mi aplicación?

La concesión implícita presenta más riesgos que otros concede. También se documentación las áreas que debe atender a (Véase, por ejemplo, [El uso incorrecto de Access Token para representar propietario del recurso en flujo implícitos] [ OAuth2-Spec-Implicit-Misuse] y [Consideraciones de seguridad y el modelo de amenazas OAuth 2.0][OAuth2-Threat-Model-And-Security-Implications]). Sin embargo, el perfil de riesgo mayor es en gran medida debido a que está diseñada para permitir que las aplicaciones que se ejecuten código activo, servida por un recurso remoto en un explorador. Si planea una arquitectura SPA no tiene componentes back-end o intentó invocar una API de Web a través de JavaScript, se recomienda usar el flujo implícito para adquisición de token.

Si su aplicación es un cliente nativo, el flujo implícito no muy apropiado. Ausencia de la cookie en el contexto de un cliente nativo de Azure AD priva a la aplicación de los medios de mantener una sesión de larga duración. Lo que significa que la aplicación varias veces solicitará al usuario cuando se obtiene el token de acceso para los nuevos recursos.

Si va a desarrollar una aplicación Web que incluye un back-end y consumo API de su código de back-end, el flujo implícito también no es una buena opción. Otros concede dar power mucho más. Por ejemplo, la concesión de credenciales de cliente de OAuth2 proporciona la capacidad de obtener tokens que reflejen los permisos asignados a la aplicación, en lugar de la delegación de usuario. Esto significa que el cliente tiene la capacidad para mantener puede obtener acceso a los recursos incluso cuando un usuario no está ocupado de forma activa en una sesión y así sucesivamente. No sólo eso, pero tales dar garantías de alta seguridad. Por ejemplo, access tokens nunca tránsito a través del explorador de usuario, no se guardan en el historial del explorador del riesgo y así sucesivamente. La aplicación cliente también puede realizar autenticación segura cuando se solicita un token.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una lista completa de recursos para programadores, incluida la información de referencia para los protocolos y la autorización de OAuth2 conceder flujos soporte de Azure AD, consulte el [Azure AD del desarrollador de guía][AAD-Developers-Guide]
- Vea [cómo integrar una aplicación de Azure AD]  [ ACOM-How-To-Integrate] para profundidad adicional en el proceso de integración de aplicación.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

