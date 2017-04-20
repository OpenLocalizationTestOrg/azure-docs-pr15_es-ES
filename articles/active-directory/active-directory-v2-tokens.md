<properties
    pageTitle="Referencia token de Azure AD versión 2.0 | Microsoft Azure"
    description="Los tipos de tokens y reclamaciones emitidos por el extremo de la versión 2.0"
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

# <a name="v20-token-reference"></a>referencia de token de versión 2.0

El punto final de la versión 2.0 emite varios tipos de tokens de seguridad en el procesamiento de cada [flujo de autenticación](active-directory-v2-flows.md). Este documento describe el formato, las características de seguridad y contenido de cada tipo de token.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Tipos de símbolos

El punto final de la versión 2.0 es compatible con el [Protocolo de autorización de OAuth 2.0](active-directory-v2-protocols.md), que utiliza access_tokens y refresh_tokens.  También es compatible con autenticación e inicio de sesión a través de [OpenID conectarse](active-directory-v2-protocols.md#openid-connect-sign-in-flow), que presenta a un tercer tipo de token, el id_token.  Cada uno de estos tokens se representa como "token portador".

Un token portador es un token de seguridad ligero que permite el acceso de "portador" a un recurso protegido. En este sentido, "portador" es cualquier parte que puede presentar el token. Aunque una fiesta primero debe autenticar con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para proteger el token de transmisión y el almacenamiento, puede interceptar y utilizar una de las partes no deseadas. Mientras algunos tokens de seguridad tienen un mecanismo integrado para evitar que personas no autorizadas usarlos, tokens portador no tiene este mecanismo y deben transporte en un canal seguro como la seguridad de capa de transporte (HTTPS). Si se transmite una token portador, un hombre en el medio ataque puede ser usada por un usuario malintencionado para adquirir el token y usarlo para un acceso no autorizado a un recurso protegido. Se aplican los mismos principios de seguridad al almacenar o almacenamiento en caché tokens portador para su uso posterior. Siempre asegúrese de que la aplicación se transmite y almacena los símbolos de portador de forma segura. Para más consideraciones de seguridad en tokens portador, vea [RFC 6750 sección 5](http://tools.ietf.org/html/rfc6750).

Muchos de los símbolos de punto final de la versión 2.0 se implementan como Json Web Tokens o JWTs.  Una JWT es una forma compacta y seguridad de la dirección URL de la transferencia de información entre dos partes.  La información contenida en JWTs se denominan "reclamaciones" o aserciones de la información sobre el portador y el asunto del token.  Las notificaciones de JWTs son los objetos JSON codificado y para la transmisión en serie.  Puesto que la JWTs emitidos por el extremo de la versión 2.0 se ha iniciado sesión, pero no cifrados, puede inspeccionar fácilmente el contenido de una JWT con fines de depuración. Para obtener más información sobre JWTs, puede hacer referencia a la [especificación de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens son una forma de token de seguridad de inicio de sesión que su aplicación recibe al realizar la autenticación con [OpenID conectarse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Que se representan como [JWTs](#types-of-tokens)y contienen notificaciones que puede usar para firmar el usuario en la aplicación.  Puede usar las notificaciones en un id_token como le convenga: normalmente se usan para mostrar la información de cuenta o decisiones de control de acceso en una aplicación.  El punto final de la versión 2.0 sólo emite un tipo de id_token, que tiene un conjunto coherente de reclamaciones independientemente del tipo de usuario que ha iniciado sesión.  Es decir que el formato y contenido de la id_tokens será el mismo para ambos usuarios Account Microsoft personales y cuentas de trabajo o escuela.

Id_tokens se ha iniciado sesión, pero no cifrados en este momento.  Cuando la aplicación recibe una id_token, debe [validar la firma](#validating-tokens) para probar la autenticidad del token y validar unos notificaciones del token para demostrar su validez.  Las notificaciones validadas por una aplicación varían en función de los requisitos de escenario, pero hay algunas [validaciones comunes de notificación de](#validating-tokens) que su aplicación debe realizar en todas las situaciones.

Información detallada sobre las notificaciones en id_tokens se proporciona a continuación, así como un id_token de ejemplo.  Tenga en cuenta que las notificaciones en id_tokens no se devuelven en un orden determinado.  Además, notificaciones nuevos se pueden introducir en id_tokens en cualquier punto en el tiempo: la aplicación no debe romper mientras se introducen notificaciones nuevos.  La siguiente lista incluye las notificaciones de que su aplicación confiable puede interpretar en el momento de escribir este artículo.  Si es necesario, puede encontrarse incluso más detalle en la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Ejemplo id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Como práctica, intente inspeccionar las notificaciones de id_token de ejemplo pegándola en [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Notificaciones de id_tokens
| Nombre | Reclamar | Valor de ejemplo | Descripción |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audiencia | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identifica al destinatario del token.  En id_tokens, la audiencia es el identificador de aplicación de la aplicación, como asignada a su aplicación en el portal de registro de la aplicación.  La aplicación debería validar este valor y rechazar el token si no coincide. |
| Emisor | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identifica el servicio de token de seguridad (STS) que construye y devuelve el token, así como el inquilino de Azure AD en el que el usuario fue autenticado.  La aplicación debería validar la reclamación de emisor para asegurarse de que el token suministrado desde el extremo de la versión 2.0.  También se debe utilizar la parte guid de la notificación para restringir el conjunto de inquilinos permitidos para iniciar sesión en la aplicación.  Guid que indica que el usuario es un usuario de consumidores de cuenta de Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Emitido en | `iat` | `1452285331` | La hora en que fue emitido el token, representado en tiempo de tiempo. |
| Fecha de caducidad | `exp` | `1452289231` | La hora en que el token es válido, se representan en tiempo de tiempo.  La aplicación debería utilizar esta notificación para comprobar la validez de la duración del token.  |
| No antes | `nbf` | `1452285331` |  La hora en que el token es válido, se representan en tiempo de tiempo. Normalmente es idéntica a la hora de emisión.  La aplicación debería utilizar esta notificación para comprobar la validez de la duración del token.  |
| Versión | `ver` | `2.0` | La versión de la id_token, según se define en Azure AD.  El extremo de la versión 2.0, el valor será `2.0`. |
| Id. de inquilinos | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Guid que representa el Azure AD inquilinos que es el usuario.  Para las cuentas de trabajo y escuela, el guid será el ID de inquilinos inmutable de la organización a la que pertenece el usuario.  Para las cuentas personales, el valor será `9188040d-6c67-4c5b-b112-36a304b66dad`.  La `profile` ámbito es necesaria para recibir esta notificación. |
| Código Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | El hash de código está incluido en id_tokens solo cuando haya emitido la id_token junto con un código de autorización de OAuth 2.0.  Puede usar para validar la autenticidad de un código de autorización.  Vea la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html) para obtener más detalles sobre cómo realizar esta validación. |
| Hash de Token de acceso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | El hash de token de acceso se incluye en id_tokens solo cuando haya emitido la id_token junto con un símbolo de acceso OAuth 2.0.  Puede usar para validar la autenticidad de un token de acceso.  Vea la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html) para obtener más detalles sobre cómo realizar esta validación. |
| Valor de seguridad | `nonce` | `12345` | El valor de seguridad es una estrategia para mitigar ataques de reproducción de token.  La aplicación puede especificar un valor de seguridad en una solicitud de autorización mediante la `nonce` parámetro de consulta.  El valor que se proporciona en la solicitud se va a emitir en la id_token `nonce` reclamación, sin modificar.  Esto permite la aplicación para comprobar el valor con el valor que especifica en la solicitud, que se asocia la sesión de la aplicación a un determinado id_token.  La aplicación debe realizar esta validación durante el proceso de validación de id_token. |
| Nombre | `name` | `Babe Ruth` | La notificación de nombre proporciona un valor legible humano que identifica al asunto del token. Este valor se garantiza que no sean únicos, es modificable y está diseñado para usarse solo con fines de visualización.  La `profile` ámbito es necesaria para recibir esta notificación. |
| Correo electrónico | `email` | `thegreatbambino@nyy.onmicrosoft.com` | La dirección de correo electrónico asociada a la cuenta de usuario, si existe.  Su valor es modificable y puede cambiar de un usuario a lo largo del tiempo.  La `email` ámbito es necesaria para recibir esta notificación. |
| Nombre de usuario preferido | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | El nombre de usuario principal que se usa para representar al usuario del extremo de la versión 2.0.  Podría ser una dirección de correo electrónico, número de teléfono o un nombre de usuario genérico sin un formato especificado.  Su valor es modificable y puede cambiar de un usuario a lo largo del tiempo.  La `profile` ámbito es necesaria para recibir esta notificación. |
| Asunto | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Entidad de seguridad acerca de que el token declara información, como el usuario de una aplicación. Este valor no varía y no se pueden reasignar o volver a utilizar, se puede usar para realizar comprobaciones de autorización de forma segura, como cuando se utiliza el token de acceso a un recurso. Dado que el asunto es siempre presente en los símbolos de los problemas de Azure AD, recomendamos usar este valor en un sistema de autorización de propósito general. |
| Id. de objeto | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | El identificador de la cuenta profesional o educativa en el sistema de Azure AD del objeto.  Esta notificación no será emitida para las cuentas personales de Microsoft.  La `profile` ámbito es necesaria para recibir esta notificación. |


## <a name="access-tokens"></a>Símbolos de acceso

Símbolos de acceso emitidos por el extremo de la versión 2.0 sólo están por Microsoft Services en este momento.  Las aplicaciones no es necesario llevar a cabo cualquier validación o comprobación de token de acceso para cualquiera de los escenarios compatibles actualmente.  Puede tratar tokens de acceso como completamente opaco: son simplemente cadenas que su aplicación puede pasar a Microsoft en las solicitudes HTTP.

En el futuro, el extremo de la versión 2.0 presentará la capacidad de la aplicación recibir tokens de acceso de otros clientes.  En ese momento, esta información se actualizará con la información que necesita la aplicación para realizar la validación de token de acceso y otras tareas similares.

Cuando se solicita un token de acceso desde el extremo de la versión 2.0, el extremo de la versión 2.0 también devuelve algunos metadatos sobre el token de acceso para el consumo de la aplicación.  Esta información incluye la hora de expiración del token de acceso y los ámbitos para los que es válida.  Esto permite a su aplicación para realizar inteligente almacenamiento en caché de tokens de acceso sin tener que analizar abrir el token de acceso propiamente dicho.

## <a name="refresh-tokens"></a>Actualizar tokens

Actualizar los tokens son símbolos de seguridad que puede utilizar la aplicación para adquirir nuevo acceso tokens en un flujo de OAuth 2.0.  Permite a su aplicación obtener acceso a largo plazo a los recursos en nombre de un usuario sin necesidad de interacción por el usuario.

Actualización tokens son varios recursos.  Es decir que un símbolo de actualización recibido durante una solicitud de token para un recurso puede canjear por tokens de acceso a un recurso completamente diferente.

Para recibir una actualización en una respuesta de token, la aplicación debe solicitar y conceder el `offline_acesss` ámbito.   Para obtener más información sobre la `offline_access` ámbito, consulte el [artículo de consentimiento y ámbitos aquí](active-directory-v2-scopes.md).

Actualizar tokens y siempre estén, completamente opaco a su aplicación.  Emitido por el extremo de la versión 2.0 de Azure AD y puede solo se inspeccionen e interpreta el extremo de la versión 2.0.  Son larga, pero no se debe escribir su aplicación para que un símbolo de actualización durará durante un período de tiempo de espera.  Tokens de actualización pueden ser no válidas en cualquier momento para una variedad de motivos.  Es la única manera de la aplicación saber si un símbolo de actualización es válido intentar canjear realizando una solicitud de token en el extremo de la versión 2.0.

Cuando se canjea un token de actualización para un nuevo token de acceso (y si la aplicación había concedida el `offline_access` ámbito), recibirá un nuevo token de actualización en la respuesta del token.  Debe guardar el token de actualización recién emitido reemplaza la utilizados en la solicitud.  Esto se garantiza que los tokens actualización siguen siendo válidos tanto como sea posible.

## <a name="validating-tokens"></a>Validar tokens

En este momento, la validación solo token que sus aplicaciones es necesario realizar está validando id_tokens.  Para validar un id_token, la aplicación debería validar firma del id_token y las notificaciones en la id_token.

<!-- TODO: Link -->
Le proporcionamos bibliotecas y ejemplos de código que muestran cómo controlar fácilmente token validación: la información siguiente simplemente se proporciona para aquellos que deseen comprender el proceso subyacente.  También hay varias bibliotecas de Abrir origen parte 3ª de validación de JWT - hay al menos una opción para casi cualquier plataforma & idioma por allí.

#### <a name="validating-the-signature"></a>Validar la firma
Un JWT contiene tres segmentos, separadas por el `.` caracteres.  El primer segmento se conoce como el **encabezado**, el segundo como el **cuerpo**y la tercera como la **firma**.  El segmento de la firma puede usarse para validar la autenticidad de los id_token para que se puede confiar en la aplicación.

Id_Tokens están firmados con algoritmos de cifrado asimétricos estándar del sector, como RSA 256. El encabezado de la id_token contiene información sobre el método de cifrado y de clave utilizado para firmar el token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

La `alg` reclamación indica el algoritmo que se usó para firmar el token, mientras el `kid` reclamación indica la clave pública determinada que se usó para firmar el token.

En cualquier momento determinado, el extremo de la versión 2.0 puede firmar una id_token con cualquiera de un conjunto determinado de pares de clave pública y privada.  El punto final de la versión 2.0 gira el conjunto de claves de forma periódica, posibles para que su aplicación debe escribirse manejar esos cambios claves automáticamente.  Una frecuencia razonable para comprobar las actualizaciones a las claves públicas usadas por el extremo de la versión 2.0 es de 24 horas.

Para adquirir los datos de clave firma necesarios para validar la firma usando se encuentra en el documento de metadatos OpenID conectarse:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Pruebe esta dirección URL en un explorador.

Este documento de metadatos es un objeto JSON que contiene varios elementos útiles de información, como la ubicación de los distintos extremos necesarios para realizar la autenticación OpenID conectarse.  

También incluye un `jwks_uri`, que proporciona la ubicación del conjunto de claves públicas utilizado para firmar tokens.  El documento JSON se encuentra en la `jwks_uri` contiene toda la información de clave pública en uso en ese momento determinado.  Puede usar la aplicación de la `kid` reclamar en el encabezado JWT para seleccionar qué clave pública de este documento se ha utilizado para iniciar un símbolo concreto.  A continuación, puede realizar validación de firma con la clave pública correcta y el algoritmo indicado.

Realizar la validación de firma está fuera del ámbito de este documento, hay muchas bibliotecas de código abierto para ayudarle a hacerlo si es necesario.

#### <a name="validating-the-claims"></a>Validar las notificaciones
Cuando la aplicación recibe una id_token al inicio de sesión de usuario, también debe realizar algunas comprobaciones con las notificaciones de la id_token.  Incluir, pero no están limitados a:

- La notificación de la **audiencia** - para comprobar que la id_token estaba destinada a la aplicación.
- Las notificaciones **No antes** y **Fecha de caducidad** - para comprobar que la id_token no ha expirado.
- La reclamación **emisor** - comprobar que el token de hecho fue emitido a su aplicación por el extremo de la versión 2.0.
- El **valor de seguridad** - como una reducción de ataque token de reproducción.
- y mucho más...

Para obtener una lista completa de validaciones reclamación que debe realizar la aplicación, consulte la [especificación OpenID conectar](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalles de los valores esperados para estas notificaciones se incluyen encima de la [sección id_token](#id_tokens).


## <a name="token-lifetimes"></a>Duración de token

Las duraciones de tokens siguientes se proporcionan puramente para su nivel de comprensión, que pueden ayudar a desarrollar y depurar aplicaciones.  No se deben escribir las aplicaciones que va a pasar cualquiera de estas duraciones permanezca constante: pueden y cambiará en cualquier momento.

| Token | Duración | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (cuentas de trabajo o escuela) | 1 hora | Id_Tokens son normalmente válidos para una hora.  La aplicación web puede utilizar esta misma duración en mantener su propia sesión con el usuario (recomendado) o elija una duración de la sesión completamente diferente.  Si su aplicación debe obtener un nuevo id_token, simplemente debe realizar una solicitud de inicio de sesión nueva a la versión 2.0 autorizar extremo.  Si el usuario tiene una sesión de explorador válido con el extremo de la versión 2.0, no deberá escribir sus credenciales de nuevo. |
| Id_Tokens (cuentas personales) | 24 horas | Son normalmente válidos Id_Tokens cuentas personales de 24 horas.  La aplicación web puede utilizar esta misma duración en mantener su propia sesión con el usuario (recomendado) o elija una duración de la sesión completamente diferente.  Si su aplicación debe obtener un nuevo id_token, simplemente debe realizar una solicitud de inicio de sesión nueva a la versión 2.0 autorizar extremo.  Si el usuario tiene una sesión de explorador válido con el extremo de la versión 2.0, no deberá escribir sus credenciales de nuevo. |
| Símbolos de acceso (cuentas de trabajo o escuela) | 1 hora | Como parte de los metadatos de token indicados en respuestas de tokens. |
| Símbolos de acceso (cuentas personales) | 1 hora | Como parte de los metadatos de token indicados en respuestas de tokens.  Se puede configurar Access_tokens emitido en nombre de las cuentas personales para una duración diferente, pero una hora normalmente es el caso. |
| Actualizar Tokens (cuenta trabajo o escuela) | Hasta 14 días | Un símbolo de actualización solo es válido para un máximo de 14 días.  Sin embargo, el token de actualización puede ser válido en cualquier momento por varios motivos, por lo que debe continuar su aplicación probar y usar un símbolo de actualización hasta que se produce un error o hasta que la aplicación lo sustituye por un nuevo token de actualización.  Un símbolo de actualización también será válido si ha sido 90 días desde que el usuario ha escrito sus credenciales. |
| Actualizar Tokens (cuentas personales) | Hasta 1 año | Un símbolo de actualización solo es válido para un máximo de 1 año.  Sin embargo, el token de actualización puede ser válido en cualquier momento por varios motivos, por lo que debe continuar su aplicación probar y utilice un símbolo de actualización hasta que se produce un error. |
| Códigos de autorización (cuentas de trabajo o escuela) | 10 minutos | Códigos de autorización son de corta duración propósito y deben canjear inmediatamente por access_tokens y refresh_tokens cuando se reciben. |
| Códigos de autorización (cuentas personales) | 5 minutos | Códigos de autorización son de corta duración propósito y deben canjear inmediatamente por access_tokens y refresh_tokens cuando se reciben.  Códigos de autorización emitidos en nombre de las cuentas personales también son un solo uso. |
