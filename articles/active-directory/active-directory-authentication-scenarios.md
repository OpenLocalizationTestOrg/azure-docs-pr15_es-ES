
<properties
   pageTitle="Escenarios de autenticación para Azure AD | Microsoft Azure"
   description="Información general sobre los cinco escenarios más comunes de autenticación para Azure Active Directory (AAD)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Escenarios de autenticación para Azure AD

Azure Active Directory (AD Azure) simplifica la autenticación para desarrolladores proporcionando identidad como origen de servicio, con el soporte técnico para los protocolos estándar como OAuth 2.0 y conectar OpenID, así como abrir bibliotecas de distintas plataformas para ayudarle a comenzar a codificar rápidamente. Este documento le ayudará a entender el admite escenarios Azure AD diversos y le mostrará cómo empezar a trabajar. Se divide en las secciones siguientes:

- [Conceptos básicos de autenticación en Azure AD](#basics-of-authentication-in-azure-ad)

- [Notificaciones de Tokens de seguridad de Azure AD](#claims-in-azure-ad-security-tokens)

- [Conceptos básicos de registrar una aplicación en Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Tipos de aplicaciones y escenarios](#application-types-and-scenarios)

  - [Explorador Web para la aplicación Web](#web-browser-to-web-application)

  - [Aplicación de la página (SPA)](#single-page-application-spa)

  - [Aplicación nativa para Web API](#native-application-to-web-api)

  - [Aplicación Web en Web API](#web-application-to-web-api)

  - [Daemon o aplicación de servidor Web API](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Conceptos básicos de autenticación en Azure AD

Si no está familiarizado con los conceptos básicos de autenticación en Azure AD, lea esta sección. En caso contrario, desea omitir hacia abajo hasta los [tipos de aplicaciones y escenarios](#application-types-and-scenarios).

Veamos el escenario más básico donde se requiere la identidad: necesita autenticarse en una aplicación web de un usuario en un explorador web. Este escenario se describe con más detalle en la sección de [Explorador Web para la aplicación Web](#web-browser-to-web-application) , pero es un punto de partida útil para ilustrar las capacidades de Azure AD y conceptualizar cómo funciona el escenario. Tenga en cuenta el siguiente diagrama para este escenario:

![Información general de inicio de sesión de aplicación web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Con el diagrama anterior en cuenta, aquí es lo que necesita saber sobre sus distintos componentes:

- Azure AD es el proveedor de identidades responsable de comprobar la identidad de usuarios y las aplicaciones que existen en el directorio de la organización y finalmente emitir el token de seguridad tras la autenticación de los usuarios y aplicaciones.


- Una aplicación que desea delegar autenticación de Azure AD debe estar registrada en Azure AD, que se registra e identifica de forma única la aplicación en el directorio.


- Los desarrolladores pueden utilizar las bibliotecas de autenticación Abrir origen Azure AD para facilitar la autenticación controlando los detalles de protocolo para usted. Para obtener más información, vea [Bibliotecas de autenticación de Azure Active Directory](active-directory-authentication-libraries.md) .


• Una vez que un usuario se ha autenticado, la aplicación debe validar el token de seguridad del usuario para asegurarse de que la autenticación fue correcta para las partes previstas. Los desarrolladores pueden utilizar las bibliotecas de autenticación proporcionado para controlar la validación de cualquier token de Azure AD, incluyendo JSON Web Tokens (JWT) o SAML 2.0. Si desea realizar la validación manualmente, consulte la documentación del [Controlador de Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Azure AD usa cifrado de clave pública para firmar tokens y compruebe que son válidos. Vea [Importante información sobre la firma clave conversión en Azure AD](active-directory-signing-key-rollover.md) para obtener más información sobre la lógica necesaria debe tener en su aplicación para garantizar que siempre se actualiza con las teclas de más recientes.


• El flujo de las convocatorias y respuestas para el proceso de autenticación es determinado por el protocolo de autenticación que se utilizó como OAuth 2.0, conectar OpenID, WS-Federation o SAML 2.0. Estos protocolos se tratan con más detalle en el tema de [Protocolos de autenticación de Azure Active Directory](active-directory-authentication-protocols.md) y en las secciones siguientes.

> [AZURE.NOTE] Azure AD es compatible con la versión 2.0 de OAuth y los estándares OpenID conectar amplio uso de tokens portador, incluidos tokens portador representados como JWTs. Un token portador es un token de seguridad ligero que permite el acceso de "portador" a un recurso protegido. En este sentido, "portador" es cualquier parte que puede presentar el token. Aunque una fiesta primero debe autenticar con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para proteger el token de transmisión y el almacenamiento, puede interceptar y utilizar una de las partes no deseadas. Mientras algunos tokens de seguridad tienen un mecanismo integrado para evitar que personas no autorizadas usarlos, tokens portador no tiene este mecanismo y deben transporte en un canal seguro como la seguridad de capa de transporte (HTTPS). Si se transmite una token portador, un hombre en el medio ataque puede ser usada por un usuario malintencionado para adquirir el token y usarlo para un acceso no autorizado a un recurso protegido. Se aplican los mismos principios de seguridad al almacenar o almacenamiento en caché tokens portador para su uso posterior. Siempre asegúrese de que la aplicación se transmite y almacena los símbolos de portador de forma segura. Para más consideraciones de seguridad en tokens portador, vea [RFC 6750 sección 5](http://tools.ietf.org/html/rfc6750).


Ahora que ya tiene una descripción general de los conceptos básicos, lea las secciones siguientes para comprender cómo funciona el aprovisionamiento de Azure AD y es compatible con los escenarios comunes de Azure AD.


## <a name="claims-in-azure-ad-security-tokens"></a>Notificaciones de Tokens de seguridad de Azure AD

Token de seguridad emitido por Azure AD contiene reclamaciones o aserciones de información sobre el asunto que se ha autenticado. Estas notificaciones se pueden utilizar la aplicación para las distintas tareas. Por ejemplo, puede usarse para validar el token, identificar el inquilino de directorio del asunto, mostrar información de usuario, determinar la autorización del asunto y así sucesivamente. Las notificaciones presentes en cualquier token de seguridad determinado dependen de que el tipo de token, el tipo de credenciales que se utilizan para autenticar al usuario y la configuración de la aplicación. Se proporciona una breve descripción de cada tipo de Reclamación emitido por Azure AD en la tabla siguiente. Para obtener más información, consulte [Token compatibles y los tipos de notificación](active-directory-token-and-claims.md).


| Reclamar | Descripción |
|-------|-------------|
| Identificador de la aplicación | Identifica la aplicación que esté usando el token.
| Audiencia | Identifica el recurso destinatario que está destinado el token. |
| Referencia de clase de contexto de autenticación de aplicación | Indica cómo el cliente (pública cliente autenticado frente confidencial cliente). |
| Instantánea de autenticación | Registra la fecha y hora cuando se ha producido la autenticación. |
| Método de autenticación | Indica cómo se autentica el asunto del token (contraseña, certificado, etcetera). |
| Nombre | Proporciona el nombre de usuario como conjunto de Azure AD. |
| Grupos | Contiene el usuario es miembro de los grupos de objetos identificadores de Azure AD. |
| Proveedor de identidades | El proveedor de identidades que se han autenticado al asunto del token de registros. |
| Emitido en | Registra el tiempo en el que fue emitido el token usan a menudo para la actualización de token. |
| Emisor | Identifica al STS que emite el token, así como el inquilino de Azure AD. |
| Apellido | Proporciona el apellido del usuario como conjunto de Azure AD. |
| Nombre | Proporciona un valor legible humano que identifica al asunto del token. |
| Identificador de objeto | Contiene un identificador único inmutable del asunto en Azure AD. |
| Roles | Contiene nombres descriptivos Azure AD funciones de aplicación que se ha concedido el usuario. |
| Ámbito | Indica los permisos a la aplicación cliente. |
| Asunto | Indica la entidad de seguridad acerca de que el token declara información. |
| Id. de inquilinos | Contiene un identificador único inmutable del inquilino de directorio que emitir el token. |
| Duración del token | Define el intervalo de tiempo en el que es válido un símbolo. |
| Nombre Principal de usuario | Contiene el nombre de usuario principal del asunto. |
| Versión | Contiene el número de versión del símbolo. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Conceptos básicos de registrar una aplicación en Azure AD

Cualquier aplicación que subcontrata autenticación de Azure AD debe registrarse en un directorio. Este paso implica que indica Azure AD sobre la aplicación, incluida la dirección URL de dónde se encuentra, la dirección URL para enviar respuestas después de la autenticación, el URI para identificar la aplicación y mucho más. Esta información es necesaria para algunas razones principales:

- Azure AD necesita coordenadas para comunicarse con la aplicación al control de inicio de sesión o intercambiar tokens. Estas son las siguientes:

  - URI de identificador de la aplicación: El identificador de una aplicación. Este valor se envía a Azure AD durante la autenticación para indicar qué aplicación el llamador desea un símbolo. Además, este valor se incluye en el token para que la aplicación sepa fue el destino previsto.


  - Responder a dirección URL y la redirección URI: en el caso de una API de web o una aplicación web, la dirección URL de respuesta es la ubicación a la que Azure AD enviará la respuesta de autenticación, incluyendo un símbolo si la autenticación se realizó correctamente. En el caso de una aplicación nativa, redirección URI es un identificador único que Azure AD redirigirá al agente de usuario en una solicitud de OAuth 2.0.


  - Id. de cliente: El identificador de una aplicación, que se genera cuando se registra la aplicación de Azure AD. Cuando se solicita un código de autorización o token, el identificador de cliente y la clave se envían a Azure AD durante la autenticación.


  - Tecla: La clave que se envía junto con un identificador de cliente cuando se autentica a Azure AD para llamar a un sitio web API.


- Azure AD debe asegurarse de que la aplicación tiene los permisos necesarios para tener acceso a los datos de directorio, otras aplicaciones de su organización etc.

Aprovisionamiento se convierte en más precisa cuando entienda que existen dos categorías de aplicaciones que pueden desarrollar e integradas con Azure AD:

- Solicitud de inquilinos única: una aplicación de inquilinos solo está pensada para su uso en una organización. Éstas son normalmente de línea de negocio (LoB) aplicaciones escritas por un desarrollador de la empresa. Una aplicación de inquilinos único solo necesita tener acceso a los usuarios en un directorio y por tanto, solo necesita estén configurados en un directorio. Estas aplicaciones suelen registrarse por un programador de la organización.


- Aplicación de múltiples arrendatario: una aplicación de múltiples arrendatario está pensada para su uso en muchas organizaciones, no solo una organización. Éstas son normalmente software como-servicio (SaaS) aplicaciones escritas un proveedor de software independiente (ISV). Aplicaciones de múltiples arrendatarios necesitan estén configurados en cada directorio donde se van a utilizar, lo que requiere consentimiento de usuario o el administrador para registrar ellos. Este proceso de consentimiento se inicia cuando una aplicación se ha registrado en el directorio y se da acceso a la API de gráfico o tal vez otro web API. Cuando un usuario o administrador de otra organización inscribe a usar la aplicación, se presentan con un cuadro de diálogo que muestra los permisos que requiere la aplicación. El usuario o el administrador, a continuación, puede consentimiento a la aplicación, que le proporciona acceso a la aplicación a los datos establecidos y, por último registra la aplicación en su directorio. Para obtener más información, vea [información general sobre el marco consentimiento](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Algunas consideraciones adicionales surgen al desarrollar una aplicación de múltiples arrendatario en lugar de una aplicación de inquilinos único. Por ejemplo, si va a realizar la aplicación disponible para los usuarios en varios directorios, necesita un mecanismo para determinar qué inquilino se encuentren. Una aplicación de inquilinos solo solo debe buscar en su propio directorio para un usuario, mientras que una aplicación de varios inquilino debe identificar un usuario específico de todos los directorios de Azure AD. Para realizar esta tarea, Azure AD proporciona un extremo de autenticación comunes donde cualquier aplicación de múltiples arrendatario puede dirigir solicitudes inicio de sesión, en lugar de un extremo del inquilino específicos. Este extremo es https://login.microsoftonline.com/common para todos los directorios de Azure AD, mientras que un extremo del inquilino específica podría ser https://login.microsoftonline.com/contoso.onmicrosoft.com. El extremo comunes es especialmente importante tener en cuenta al desarrollar la aplicación, ya que tendrá la lógica necesaria para tratar a varios inquilinos durante el inicio de sesión, cierre de sesión, validación y el token.

Si actualmente desarrollar una aplicación de inquilinos único pero desea que esté disponible para muchas organizaciones, puede fácilmente realizar cambios en la aplicación y su configuración en Azure AD para realizar varios inquilinos capaz. Además, Azure AD utiliza la misma clave de firma para todos los tokens en todos los directorios, si va a proporcionar autenticación en una aplicación de múltiples arrendatario o inquilino único.

Cada escenario mencionado en este documento incluye una sección subsitios que describa sus requisitos de aprovisionamiento. Para obtener información más detallada sobre la creación de una aplicación en Azure AD y las diferencias entre las aplicaciones de una única columna y varias inquilinos, consulte [Aplicaciones de integración con Azure Active Directory](active-directory-integrating-applications.md) para obtener más información. Siga leyendo para conocer los escenarios comunes de aplicaciones de Azure AD.

## <a name="application-types-and-scenarios"></a>Tipos de aplicaciones y escenarios

Cada uno de los escenarios descritos en este documento se puede desarrollar con varios idiomas y plataformas. Que se realicen en los ejemplos de código completo que están disponibles en nuestra [Guía de ejemplos de código](active-directory-code-samples.md)o directamente desde el correspondiente [Github repositorios de ejemplo](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Además, si la aplicación necesita una parte específica o segmento de un escenario de llevar a cabo, en la mayoría de los casos que funcionalidad puede agregarse por separado. Por ejemplo, si tiene una aplicación nativa que llama a una API de web, puede agregar fácilmente una aplicación web que llama a la API de web. El siguiente diagrama muestra estos escenarios y los tipos de aplicaciones, y cómo se pueden agregar distintos componentes:

![Tipos de aplicaciones y escenarios](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Estos son los cinco escenarios de aplicación principal compatibles con Azure AD:

- [Explorador Web para la aplicación Web](#web-browser-to-web-application): el usuario debe iniciar sesión en una aplicación web que está protegida por Azure AD.

- [Aplicación de página único (SPA)](#single-page-application-spa): el usuario debe iniciar sesión en una aplicación de página individual que está protegida por Azure AD.

- [Aplicación nativa para API Web](#native-application-to-web-api): una aplicación nativa que se ejecute en un PC, tableta o teléfono debe autenticar a un usuario para obtener recursos desde un sitio web API que está protegida por Azure AD.

- [Aplicación Web a la API de Web](#web-application-to-web-api): una aplicación web necesita obtener recursos de web API protegido por Azure AD.

- [Daemon o aplicación de servidor Web API](#daemon-or-server-application-to-web-api): una aplicación demonio o una aplicación de servidor sin interfaz de usuario de web necesita obtener recursos desde un sitio web API protegido por Azure AD.

### <a name="web-browser-to-web-application"></a>Explorador Web para la aplicación Web

Esta sección describe una aplicación que autentica a un usuario en un explorador web a una aplicación web. En este escenario, la aplicación web dirige el explorador del usuario iniciar sesión en a Azure AD. Azure AD devuelve una respuesta de inicio de sesión a través del explorador del usuario, que contiene las notificaciones sobre el usuario en un token de seguridad. Es compatible con este escenario mediante los protocolos WS-Federation, SAML 2.0 y conectar OpenID de inicio de sesión.


#### <a name="diagram"></a>Diagrama
![Flujo de autenticación para explorador de la aplicación web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Descripción del flujo de protocolo


1. Cuando un usuario visita la aplicación y las necesidades de inicio de sesión, se le redirige a través de una solicitud de inicio de sesión para el extremo de autenticación en Azure AD.


2. El usuario inicia sesión en la página de inicio de sesión.


3. Si la autenticación es correcta, Azure AD crea un símbolo de autenticación y devuelve una respuesta de inicio de sesión a la dirección URL de respuesta de la aplicación que configuró en el Portal de administración de Azure. Para una aplicación de producción, esta dirección URL de respuesta debería HTTPS. El token devuelto incluye notificaciones sobre el usuario y Azure AD que requieren la aplicación para validar el token.


4. La aplicación valida el token mediante una clave de firma pública y la información del emisor en el documento de metadatos de federación para Azure AD. Después de que la aplicación valide el token, Azure AD inicia una nueva sesión con el usuario. Esta sesión permite al usuario obtener acceso a la aplicación hasta que caduque.


#### <a name="code-samples"></a>Ejemplos de código


Vea los ejemplos de código para explorador Web a escenarios de la aplicación Web. Y compruébelos con frecuencia--agregamos nuevas muestras de todo el tiempo. [Explorador web para la aplicación Web](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>Registrar


- Solo inquilino: Si está creando una aplicación para su organización, se debe registrar en el directorio de la organización a través del Portal de administración de Azure.


- Múltiples arrendatarios: Si está creando una aplicación que puede ser usada por usuarios fuera de su organización, esta debe estar registrada en el directorio de la organización, pero también debe estar registrada en directorio de la organización que va a utilizar la aplicación. Para que la aplicación esté disponible en su directorio, puede incluir un proceso de suscripción para los clientes que les permite consentimiento a la aplicación. Cuando suscribe a la aplicación, se presentará con un cuadro de diálogo que muestra los permisos que requiere la aplicación y, a continuación, en la opción de consentimiento. Según los permisos necesarios, un administrador de la otra organización deberá dar su consentimiento. Cuando el usuario o el administrador consiente, la aplicación se registra en su directorio. Para obtener más información, consulte [Aplicaciones de integración con Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiración de token

La sesión del usuario caduca cuando caduca la duración del token emitido por Azure AD. La aplicación puede acortar este período de tiempo, si lo desea, como la firma de los usuarios basándose en un periodo de inactividad. Cuando finaliza la sesión, el usuario se le indicará que vuelva a iniciar sesión.





### <a name="single-page-application-spa"></a>Aplicación de la página (SPA)

Esta sección describe la autenticación para una sola aplicación de la página, que utiliza Azure AD y la autorización implícita OAuth 2.0 conceden para proteger su web API volver Finalizar. Aplicaciones de página único normalmente están estructuradas como una capa de presentación de JavaScript (front-end) que se ejecuta en el explorador y un back-end API Web que se ejecuta en un servidor e implementa lógica de negocios de la aplicación. Para obtener más información acerca de la autorización implícita conceder y le ayudará a decidir si es adecuado para la aplicación, vea la [Descripción del flujo de conceder implícitos OAuth2 de Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

En este escenario, cuando el usuario inicia sesión, JavaScript frente usos finales [Active Directory Authentication Library JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) y la concesión de autorización implícita para obtener un símbolo de ID (id_token) de Azure AD. El token se almacena en caché y el cliente asocia a la solicitud como el token portador al realizar llamadas a su API Web back-end, que está protegida mediante el software de OWIN intermedio. 
#### <a name="diagram"></a>Diagrama

![Solo el diagrama de aplicación de la página](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descripción del flujo de protocolo

1. El usuario se desplaza a la aplicación web.


2. La aplicación devuelve JavaScript front-end (capa de presentación) en el explorador.


3. El usuario inicia sesión, por ejemplo, haga clic en un vínculo de inicio de sesión. El explorador envía un GET al extremo de autorización de Azure AD para solicitar un símbolo de ID. Esta solicitud incluye el cliente de URL ID y responder en los parámetros de consulta.


4. Azure AD valida la dirección URL de respuesta con la URL de respuesta registrada que configuró en el Portal de administración de Azure.


5. El usuario inicia sesión en la página de inicio de sesión.


6. Si la autenticación es correcta, Azure AD crea un token de identificador y devuelve como un fragmento de dirección URL (#) a la dirección URL de respuesta de la aplicación. Para una aplicación de producción, esta dirección URL de respuesta debería HTTPS. El token devuelto incluye notificaciones sobre el usuario y Azure AD que requieren la aplicación para validar el token.


7. El código de cliente de JavaScript que se ejecuta en el explorador extrae el token de la respuesta a usar en la protección de llamadas en la web de la aplicación que API volver Finalizar.


8. El explorador llama web de la aplicación API volver terminan con el token de acceso en el encabezado de autorización.

#### <a name="code-samples"></a>Ejemplos de código


Vea los ejemplos de código para escenarios de aplicación de página único (SPA). No olvide compruébelos con frecuencia--agregamos nuevas muestras de todo el tiempo. [Aplicación de la página (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>Registrar


- Solo inquilino: Si está creando una aplicación para su organización, se debe registrar en el directorio de la organización a través del Portal de administración de Azure.


- Múltiples arrendatarios: Si está creando una aplicación que puede ser usada por usuarios fuera de su organización, esta debe estar registrada en el directorio de la organización, pero también debe estar registrada en directorio de la organización que va a utilizar la aplicación. Para que la aplicación esté disponible en su directorio, puede incluir un proceso de suscripción para los clientes que les permite consentimiento a la aplicación. Cuando suscribe a la aplicación, se presentará con un cuadro de diálogo que muestra los permisos que requiere la aplicación y, a continuación, en la opción de consentimiento. Según los permisos necesarios, un administrador de la otra organización deberá dar su consentimiento. Cuando el usuario o el administrador consiente, la aplicación se registra en su directorio. Para obtener más información, consulte [Aplicaciones de integración con Azure Active Directory](active-directory-integrating-applications.md).

Después de registrar la aplicación, este debe estar configurado para utilizar el protocolo de OAuth 2.0 implícitos conceder. De forma predeterminada, este protocolo está deshabilitado para las aplicaciones. Para habilitar el protocolo OAuth2 implícitos conceder para la aplicación, descargar su manifiesto de aplicación desde el Portal de administración de Azure, establezca el valor de "oauth2AllowImplicitFlow" en true y, a continuación, cargar el reverso manifiesto en el portal. Para obtener instrucciones detalladas, vea [Habilitar OAuth 2.0 implícitos conceder solo aplicaciones de la página](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiración de token

Cuando utiliza ADAL.js para administrar la autenticación con Azure AD, puede aprovechar varias características que facilitan la actualización un token expirado, así como obtener tokens para recursos de la API de web adicionales que pueden llamar por la aplicación. Cuando el usuario se autentica correctamente con Azure AD, se establece una sesión protegida por una cookie para el usuario entre el explorador y Azure AD. Es importante que tenga en cuenta que la sesión existe entre el usuario y Azure AD y no entre el usuario y la aplicación web que se ejecuta en el servidor. Cuando expira un símbolo, ADAL.js usa esta sesión para obtener silenciosamente otro token. Para ello, usando un iFrame oculto para enviar y recibir la solicitud mediante el protocolo OAuth implícitos conceder. ADAL.js también puede utilizar este mismo mecanismo silenciosamente obtener tokens de acceso de Azure AD para otro web recursos de API que las llamadas de aplicación como estos recursos de soporte técnico entre origen de recursos compartidos (CORS), se registran en el directorio del usuario y, a continuación, cualquier consentimiento necesario ha proporcionado por el usuario durante el inicio de sesión.


### <a name="native-application-to-web-api"></a>Aplicación nativa para Web API


Esta sección describe una aplicación nativa que llama a un sitio web API en nombre de un usuario. Este escenario se basa en el tipo de concesión de código de autorización de OAuth 2.0 con un cliente público, como se describe en la sección 4.1 de la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749). La aplicación nativa Obtiene un token de acceso para el usuario mediante el protocolo OAuth 2.0. Este token de acceso se envía a continuación, en la convocatoria para la web API, que autoriza el usuario y devuelve el recurso que desee.

#### <a name="diagram"></a>Diagrama

![Aplicación nativa para Web API diagrama](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Flujo de autenticación de la aplicación nativa para API

#### <a name="description-of-protocol-flow"></a>Descripción del flujo de protocolo


Si está utilizando las bibliotecas de autenticación de AD, la mayoría de los detalles de protocolo que se describe a continuación se administra por usted, como la ventana emergente del explorador, token de almacenamiento en caché y tratamiento de tokens de actualización.

1. Usar un explorador emergente, que la aplicación nativa realiza una solicitud al extremo de autorización de Azure AD. Esta solicitud incluye el identificador de cliente y la redirección URI de la aplicación nativa, como se muestra en el Portal de administración y la URI de ID de aplicación para la web API. Si el usuario ya no se ha iniciado sesión, se le solicitará que vuelva a iniciar sesión


2. Azure AD autentica al usuario. Si es una aplicación de múltiples arrendatario y consentimiento se requiere para usar la aplicación, el usuario deberán consentimiento si aún no lo ha hecho. Después de la concesión de consentimiento y tras la autenticación, Azure AD emite una respuesta de código de autorización URI de redireccionamiento de la aplicación de cliente.


3. Cuando Azure AD envía una respuesta de código de autorización la redirección URI, la aplicación cliente detiene la interacción con el navegador y extrae el código de autorización de la respuesta. Utilizar este código de autorización, la aplicación cliente envía una solicitud al extremo de token de Azure AD que incluya el código de autorización, detalles sobre la aplicación de cliente (ID de cliente y redirección URI) y el recurso deseado (aplicación de identificador URI para la web API).


4. Azure AD validadas el código de autorización e información acerca de la API de web y de aplicaciones de cliente. Tras una validación correcta, Azure AD devuelve dos tokens: un token de acceso JWT y un token de actualización JWT. Además, Azure AD devuelve información básica sobre el usuario, como su ID de nombre y el inquilino de visualización.


5. Sobre HTTPS, la aplicación cliente que utiliza el token de acceso JWT devuelto para agregar la cadena JWT con una designación de "Portador" en el encabezado de autorización de la solicitud a la API de web. La API de web, a continuación, valide el token JWT y si la validación es correcta, devuelve el recurso que desee.


6. Cuando expira el token de acceso, la aplicación cliente recibe un error que indica que el usuario necesita autenticarse de nuevo. Si la aplicación tiene un símbolo de actualización válida, puede usar para adquirir un nuevo token de acceso sin preguntar al usuario que vuelva a iniciar sesión. Si expira el token de actualización, la aplicación necesitará interactiva autenticar al usuario de nuevo.


> [AZURE.NOTE] El token de actualización emitido por Azure AD puede utilizarse para tener acceso a varios recursos. Por ejemplo, si tiene una aplicación de cliente que tiene permiso para llamar a dos web API, el token de actualización puede utilizarse para obtener un acceso token a otra web API también.


#### <a name="code-samples"></a>Ejemplos de código


Vea los ejemplos de código para la aplicación nativa para escenarios de Web API. Y compruébelos con frecuencia--agregamos nuevas muestras de todo el tiempo. [Aplicación nativa para Web API](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>Registrar


- Solo inquilino: Ambos nativo aplicación y la web API deben estar registrados en el mismo directorio de Azure AD. La API de web se puede configurar para exponer un conjunto de permisos, que se usan para limitar el acceso de la aplicación nativa a sus recursos. La aplicación de cliente, a continuación, selecciona los permisos que desee en el menú desplegable de "Permisos a otras aplicaciones" en el Portal de administración de Azure.


- Múltiples arrendatarios: en primer lugar, la aplicación nativa nunca registrado en el programador o directorio de publisher. En segundo término, la aplicación nativa está configurada para indicar los permisos que requiere que sea completamente funcional. Esta lista de los permisos necesarios se muestra en un cuadro de diálogo cuando un usuario o un administrador en el directorio de destino otorga consentimiento a la aplicación, que hace que esté disponible para su organización. Algunas aplicaciones requieren solo permisos de usuario, que pueden aceptar todos los usuarios de la organización. Otras aplicaciones requieren permisos de administrador, que no se acepta un usuario de la organización. Solo el administrador del directorio puede dar consentimiento para las aplicaciones que requieran este nivel de permisos. Cuando el usuario o el administrador consiente, la web API está registrada en su directorio. Para obtener más información, consulte [Aplicaciones de integración con Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiración de token


Cuando la aplicación nativa utiliza su código de autorización para obtener un acceso JWT token, también recibe un símbolo de actualización JWT. Cuando expira el token de acceso, el token de actualización puede utilizarse para volver a autenticar al usuario sin necesidad de volver a iniciar sesión. Este token de actualización, a continuación, se utiliza para autenticar al usuario, que da como resultado un nuevo token de acceso y el token de actualización.





### <a name="web-application-to-web-api"></a>Aplicación Web en Web API


Esta sección describe una aplicación web que necesita para obtener recursos desde un sitio web API. En este escenario, existen dos tipos de identidad que puede usar la aplicación web para autenticar y llamar a la API de web: una identidad de la aplicación, o un usuario delegado.

*Identidad de la aplicación:* Este escenario utiliza concesión de credenciales de cliente de OAuth 2.0 autenticar como la aplicación y tener acceso a la API de web. Cuando se utiliza una identidad de aplicación web API sólo puede detectar que la aplicación web está llamando a ella, como la web API no recibe cualquier información sobre el usuario. Si la aplicación recibe información sobre el usuario, se enviará a través del protocolo de aplicación y no está firmado por Azure AD. La API de web confía en que la aplicación web autenticado el usuario. Por este motivo, este modelo se denomina subsistema de confianza.

*Delegado la identidad de usuario:* Este escenario se puede conseguir de dos formas: conectar OpenID y concesión de código de autorización de OAuth 2.0 con un cliente de carácter confidencial. La aplicación web Obtiene un token de acceso para el usuario, que demuestra a la web API que el usuario autenticado correctamente a la aplicación web y que puede obtener la identidad de un usuario delegado para llamar a la API de web en la aplicación web. Este token de acceso se envía en la convocatoria para la web API, que autoriza el usuario y devuelve el recurso que desee.

#### <a name="diagram"></a>Diagrama

![Aplicación Web a diagrama de Web API](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Descripción del flujo de protocolo

La identidad de la aplicación y los tipos de identidad de usuario delegado se describen en el flujo de abajo. La diferencia clave entre ellas es que la identidad de usuario delegado en primer lugar debe adquirir un código de autorización antes de que el usuario puede iniciar sesión y obtener acceso a la API de web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Concesión de credenciales de identidad de la aplicación cliente de OAuth 2.0

1. Un usuario ha iniciado sesión en Azure AD en la aplicación web (vea el [Explorador Web para la aplicación Web](#web-browser-to-web-application) anterior).


2. La aplicación web necesita adquirir un token de acceso para que se puede autenticar a la web API y recuperar el recurso que desee. Realiza una solicitud al extremo de token de Azure AD, proporcionar la credencial, ID de cliente y aplicación web de la API de URI de ID.


3. Azure AD autentica la aplicación y devuelve un token de acceso JWT que se usa para llamar a la API de web.


4. A través de HTTPS, la aplicación web usa el token de acceso JWT devuelto para agregar la cadena JWT con una designación de "Portador" en el encabezado de autorización de la solicitud a la API de web. La API de web, a continuación, valide el token JWT y si la validación es correcta, devuelve el recurso que desee.

##### <a name="delegated-user-identity-with-openid-connect"></a>Conectar la identidad de usuario delegado con OpenID

1. Un usuario ha iniciado sesión en una aplicación web mediante Azure AD (consulte la sección de [Explorador Web para la aplicación Web](#web-browser-to-web-application) anterior). Si el usuario de la aplicación web no ha aceptado todavía para permitir que la aplicación web llamar a la API de web en su nombre, el usuario tendrá consentimiento. La aplicación mostrará los permisos que requiere y si alguno de estos permisos de administrador, un usuario normal en el directorio no pueda consentimiento. Este proceso consentimiento solo se aplica a inquilinos varias aplicaciones, no solo inquilino, como la aplicación dispone de los permisos necesarios. Cuando el usuario ha iniciado sesión, la aplicación web ha recibido un símbolo de ID con información sobre el usuario, así como un código de autorización.


2. Utilizar el código de autorización emitido por Azure AD, la aplicación web envía una solicitud al extremo de token de Azure AD que incluya el código de autorización, detalles sobre la aplicación de cliente (ID de cliente y redirección URI) y el recurso deseado (aplicación de identificador URI para la web API).


3. Azure AD validadas el código de autorización e información sobre la aplicación web y las API de web. Tras una validación correcta, Azure AD devuelve dos tokens: un token de acceso JWT y un token de actualización JWT.


4. A través de HTTPS, la aplicación web usa el token de acceso JWT devuelto para agregar la cadena JWT con una designación de "Portador" en el encabezado de autorización de la solicitud a la API de web. La API de web, a continuación, valide el token JWT y si la validación es correcta, devuelve el recurso que desee.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidad de usuario delegado con concesión de código de autorización de OAuth 2.0

1. Un usuario ya ha iniciado sesión en una aplicación web, cuyo mecanismo de autenticación es independiente de Azure AD.


2. La aplicación web requiere un código de autorización para adquirir un token de acceso, por lo que emite una solicitud a través del explorador al extremo de autorización de Azure AD, lo que proporciona el identificador de cliente y redirigir URI de la aplicación web tras la autenticación correcta. El usuario inicia sesión en Azure AD.


3. Si el usuario de la aplicación web no ha aceptado todavía para permitir que la aplicación web llamar a la API de web en su nombre, el usuario tendrá consentimiento. La aplicación mostrará los permisos que requiere y si alguno de estos permisos de administrador, un usuario normal en el directorio no pueda consentimiento. Este proceso consentimiento solo se aplica a inquilinos varias aplicaciones, no solo inquilino, como la aplicación dispone de los permisos necesarios.


4. Después de que el usuario ha aceptado, la aplicación web recibe el código de autorización que necesita para adquirir un token de acceso.


5. Utilizar el código de autorización emitido por Azure AD, la aplicación web envía una solicitud al extremo de token de Azure AD que incluya el código de autorización, detalles sobre la aplicación de cliente (ID de cliente y redirección URI) y el recurso deseado (aplicación de identificador URI para la web API).


6. Azure AD validadas el código de autorización e información sobre la aplicación web y las API de web. Tras una validación correcta, Azure AD devuelve dos tokens: un token de acceso JWT y un token de actualización JWT.


7. A través de HTTPS, la aplicación web usa el token de acceso JWT devuelto para agregar la cadena JWT con una designación de "Portador" en el encabezado de autorización de la solicitud a la API de web. La API de web, a continuación, valide el token JWT y si la validación es correcta, devuelve el recurso que desee.

#### <a name="code-samples"></a>Ejemplos de código

Vea los ejemplos de código para la aplicación Web en escenarios de Web API. Y compruébelos con frecuencia--agregamos nuevas muestras de todo el tiempo. [Aplicación Web API](active-directory-code-samples.md#web-application-to-web-api)de Web.


#### <a name="registering"></a>Registrar

- Solo inquilino: Para la identidad de la aplicación y los casos de identidad de usuario delegado, la aplicación web y la API de web deben registrarse en el mismo directorio de Azure AD. La API de web se puede configurar para exponer un conjunto de permisos, que se utilizan para limitar el acceso a sus recursos de la aplicación web. Si se utiliza un tipo de identidad de usuario delegado, necesita la aplicación web seleccionar los permisos que desee en el menú desplegable "Permisos a otras aplicaciones" en el Portal de administración de Azure. Este paso no es necesario si se utiliza el tipo de identidad de la aplicación.


- Múltiples arrendatarios: en primer lugar, la aplicación web está configurada para indicar los permisos que requiere que sea completamente funcional. Esta lista de los permisos necesarios se muestra en un cuadro de diálogo cuando un usuario o un administrador en el directorio de destino otorga consentimiento a la aplicación, que hace que esté disponible para su organización. Algunas aplicaciones requieren solo permisos de usuario, que pueden aceptar todos los usuarios de la organización. Otras aplicaciones requieren permisos de administrador, que no se acepta un usuario de la organización. Solo el administrador del directorio puede dar consentimiento para las aplicaciones que requieran este nivel de permisos. Cuando el usuario o el administrador consiente, la aplicación web y la API de web están registrados en su directorio.

#### <a name="token-expiration"></a>Expiración de token

Cuando la aplicación web usa su código de autorización para obtener un acceso JWT token, también recibe un símbolo de actualización JWT. Cuando expira el token de acceso, el token de actualización puede utilizarse para volver a autenticar al usuario sin necesidad de volver a iniciar sesión. Este token de actualización, a continuación, se utiliza para autenticar al usuario, que da como resultado un nuevo token de acceso y el token de actualización.


### <a name="daemon-or-server-application-to-web-api"></a>Daemon o aplicación de servidor Web API


Esta sección describe una aplicación demonio o servidor que necesita para obtener recursos desde un sitio web API. Hay dos escenarios subcarpetas que se aplican a esta sección: un daemon que necesita llamar a un sitio web API, integrada en el tipo de concesión de credenciales de cliente de OAuth 2.0; y una aplicación de servidor (por ejemplo, un sitio web API) que necesita llamar a un sitio web API, integrada en la especificación de borrador OAuth 2.0 On-Behalf-Of.

Para el escenario cuando una aplicación de daemon necesita llamar a un sitio web API, es importante conocer algunas cosas. En primer lugar, la interacción con el usuario no es posible con una aplicación demonio, que requiere la aplicación para tener su propia identidad. Un ejemplo de una aplicación daemon es un proceso o un servicio de sistema operativo que se ejecuta en segundo plano. Este tipo de aplicación solicita un token de acceso con su identidad de la aplicación y presentar su cliente ID, credenciales (contraseña o el certificado) y la aplicación identificador URI a Azure AD. Después de una autenticación correcta, el daemon recibe un token de acceso de Azure AD, que se utiliza para llamar a la API de web.

Para el escenario cuando una aplicación de servidor necesita llamar a un web API, resulta útil usar un ejemplo. Suponga que un usuario se autentica en una aplicación nativa y esta aplicación nativa necesita llamar a un sitio web API. Azure AD emite un token de acceso JWT para llamar a la API de web. Si la API web necesita llamar a otro nivel inferior web API, puede usar el flujo en nombre de delegar la identidad del usuario y autenticarse en la web API de segundo nivel.

#### <a name="diagram"></a>Diagrama

![Aplicación de servidor a diagrama de Web API o daemon](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descripción del flujo de protocolo

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Concesión de credenciales de identidad de la aplicación cliente de OAuth 2.0

1. En primer lugar, la aplicación de servidor se necesita autenticar con Azure AD por sí mismo, sin necesidad de interacción humana como un cuadro de diálogo de inicio de sesión interactivo. Realiza una solicitud al extremo de token de Azure AD, proporcionar la credencial, ID de cliente y URI de ID de aplicación.


2. Azure AD autentica la aplicación y devuelve un token de acceso JWT que se usa para llamar a la API de web.


3. A través de HTTPS, la aplicación web usa el token de acceso JWT devuelto para agregar la cadena JWT con una designación de "Portador" en el encabezado de autorización de la solicitud a la API de web. La API de web, a continuación, valide el token JWT y si la validación es correcta, devuelve el recurso que desee.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidad de usuario delegada con la especificación de borrador en nombre de OAuth 2.0

El flujo descrito a continuación se supone que un usuario se ha autenticado en otra aplicación (como una aplicación nativa) y su identidad de usuario se ha utilizado para adquirir un token de acceso a la API de web de primer nivel.

1. La aplicación nativa envía el token de acceso a la API de web de primer nivel.


2. La API de primer nivel web envía una solicitud al extremo de token de Azure AD, proporcionar a su cliente ID y credenciales, así como token de acceso del usuario. Además, la solicitud se envía con un on_behalf_of parámetro que indica la web API está solicitando tokens nuevos para llamar a un nivel inferior web API en nombre del usuario original.


3. Azure AD comprueba que la API de primer nivel web tiene permisos de acceso a la API de segundo nivel web y valida la solicitud, que devuelve un token de acceso JWT y un JWT actualizar token a la API de web de primer nivel.


4. Sobre HTTPS, la API web de primer nivel, a continuación, llama a la API de segundo nivel web agregando la cadena de token en el encabezado de autorización de la solicitud. Puede continuar la API de primer nivel web llamar a la API de segundo nivel web como el token de acceso y actualización tokens son válidos.

#### <a name="code-samples"></a>Ejemplos de código

Vea los ejemplos de código para Daemon o aplicación de servidor para escenarios de Web API. Y compruébelos con frecuencia--agregamos nuevas muestras de todo el tiempo. [Servidor o aplicación Daemon Web API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Registrar

- Solo inquilino: Para la identidad de la aplicación y los casos de identidad de usuario delegado, la aplicación de servidor o daemon debe registrarse en el mismo directorio de Azure AD. La API de web se puede configurar para exponer un conjunto de permisos, que se utilizan para limitar el daemon o acceso de servidor a sus recursos. Si se utiliza un tipo de identidad de usuario delegado, la aplicación de servidor necesita seleccionar los permisos que desee en el menú desplegable "Permisos a otras aplicaciones" en el Portal de administración de Azure. Este paso no es necesario si se utiliza el tipo de identidad de la aplicación.


- Múltiples arrendatarios: en primer lugar, la aplicación demonio o servidor está configurada para indicar los permisos que requiere que sea completamente funcional. Esta lista de los permisos necesarios se muestra en un cuadro de diálogo cuando un usuario o un administrador en el directorio de destino otorga consentimiento a la aplicación, que hace que esté disponible para su organización. Algunas aplicaciones requieren solo permisos de usuario, que pueden aceptar todos los usuarios de la organización. Otras aplicaciones requieren permisos de administrador, que no se acepta un usuario de la organización. Solo el administrador del directorio puede dar consentimiento para las aplicaciones que requieran este nivel de permisos. Cuando el usuario o el administrador consiente, de la API de web registran en su directorio.

#### <a name="token-expiration"></a>Expiración de token

Cuando la primera aplicación utiliza su código de autorización para obtener un acceso JWT token, también recibe un símbolo de actualización JWT. Cuando expira el token de acceso, el token de actualización puede utilizarse para volver a autenticar al usuario sin preguntar las credenciales. Este token de actualización, a continuación, se utiliza para autenticar al usuario, que da como resultado un nuevo token de acceso y el token de actualización.

## <a name="see-also"></a>Vea también

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)

[Información importante sobre la sustitución de la clave de firma en Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
