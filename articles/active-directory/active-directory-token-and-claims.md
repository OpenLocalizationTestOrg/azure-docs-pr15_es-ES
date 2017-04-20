 <properties
   pageTitle="Referencia de Token de Azure AD | Microsoft Azure"
   description="Una guía para comprender y evaluar las notificaciones de los tokens SAML 2.0 y JSON Web Tokens (JWT) emitidos por Azure Active Directory (AAD)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Referencia de token de Azure AD

Azure Active Directory (AD Azure) emite varios tipos de tokens de seguridad en el procesamiento de cada flujo de autenticación. Este documento describe el formato, las características de seguridad y contenido de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de símbolos

Azure AD es compatible con el [Protocolo de autorización de OAuth 2.0](active-directory-protocols-oauth-code.md), que utiliza access_tokens y refresh_tokens.  También es compatible con autenticación e inicio de sesión a través de [OpenID conectarse](active-directory-protocols-openid-connect-code.md), que presenta a un tercer tipo de token, el id_token.  Cada uno de estos tokens se representa como "token portador".

Un token portador es un token de seguridad ligero que permite el acceso de "portador" a un recurso protegido. En este sentido, "portador" es cualquier parte que puede presentar el token. Aunque se requiere autenticación con Azure AD para que reciban un token portador, es necesario para proteger el token, para evitar la interceptación por las partes no deseadas. Porque tokens portador no tiene un mecanismo integrado para evitar que personas no autorizadas usarlos, debe transporte en un canal seguro como la seguridad de capa de transporte (HTTPS). Si se transmite una token portador, un hombre en el medio ataque puede usarse para adquirir el token y obtener acceso no autorizado a un recurso protegido. Se aplican los mismos principios de seguridad al almacenar o almacenamiento en caché tokens portador para su uso posterior. Siempre asegúrese de que la aplicación se transmite y almacena los símbolos de portador de forma segura. Para más consideraciones de seguridad en tokens portador, vea [RFC 6750 sección 5](http://tools.ietf.org/html/rfc6750).

Muchos de los símbolos de Azure AD se implementan como JSON Web Tokens o JWTs.  Una JWT es una forma compacta y seguridad de la dirección URL de la transferencia de información entre dos partes.  La información contenida en JWTs se denominan "reclamaciones" o aserciones de la información sobre el portador y el asunto del token.  Las notificaciones de JWTs son los objetos JSON codificado y para la transmisión en serie.  Puesto que la JWTs emitidos por Azure AD se ha iniciado sesión, pero no cifrados, puede inspeccionar fácilmente el contenido de una JWT con fines de depuración.  Hay varias herramientas disponibles para hacerlo, como [jwt.calebb.net](http://jwt.calebb.net). Para obtener más información sobre JWTs, puede hacer referencia a la [especificación de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens son una forma de token de seguridad de inicio de sesión que su aplicación recibe al realizar la autenticación con [OpenID conectarse](active-directory-protocols-openid-connect-code.md).  Que se representan como [JWTs](#types-of-tokens)y contienen notificaciones que puede usar para firmar el usuario en la aplicación.  Puede usar las notificaciones en un id_token como le convenga: normalmente se usan para mostrar la información de cuenta o decisiones de control de acceso en una aplicación.

Id_tokens se ha iniciado sesión, pero no cifrados en este momento.  Cuando la aplicación recibe una id_token, debe [validar la firma](#validating-tokens) para probar la autenticidad del token y validar unos notificaciones del token para demostrar su validez.  Las notificaciones validadas por una aplicación varían en función de los requisitos de escenario, pero hay algunas [validaciones comunes de notificación de](#validating-tokens) que su aplicación debe realizar en todas las situaciones.

Vea la siguiente sección para obtener más información en id_tokens reclamaciones, así como un id_token de ejemplo.  Tenga en cuenta que las notificaciones en id_tokens no se devuelven en un orden determinado.  Además, notificaciones nuevos se pueden introducir en id_tokens en cualquier punto en el tiempo: la aplicación no debe romper mientras se introducen notificaciones nuevos.  La siguiente lista incluye las notificaciones de que su aplicación confiable puede interpretar en el momento de escribir este artículo.  Si es necesario, puede encontrarse incluso más detalle en la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Ejemplo id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Como práctica, intente inspeccionar las notificaciones de id_token de ejemplo pegándola en [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Notificaciones de id_tokens

| Notificación JWT | Nombre | Descripción |
|-----------|------|-------------|
| `appid`| Identificador de la aplicación | Identifica la aplicación que esté usando el token de acceso a un recurso. La aplicación puede actuar por sí mismo o en nombre de un usuario. Normalmente, el identificador de aplicación representa un objeto de la aplicación, pero también puede representar un objeto principal del servicio en Azure AD. <br><br> **Valor de JWT de ejemplo**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Audiencia | El destinatario del token. La aplicación que recibe el token debe comprobar que el valor de la audiencia sea correcto y rechazar los tokens está pensados para una audiencia diferente. <br><br> **Valor de SAML de ejemplo**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valor de JWT de ejemplo**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Referencia de clase de contexto de autenticación de aplicación | Indica cómo se autentica el cliente. Para un cliente público, el valor es 0. Si se utilizan el identificador de cliente y secreto de cliente, el valor es 1. <br><br> **Valor de JWT de ejemplo**: <br> `"appidacr": "0"`|
| `acr`| Referencia de clase de contexto de autenticación | Indica cómo se autentica el asunto, en lugar de cliente en la notificación de referencia de clase de contexto de autenticación de aplicación. El valor "0" indica que la autenticación de usuario final no cumple los requisitos de ISO/IEC 29115. <br><br> **Valor de JWT de ejemplo**: <br> `"acr": "0"`|
| | Instantánea de autenticación | Registra la fecha y hora cuando se ha producido la autenticación. <br><br> **Valor de SAML de ejemplo**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Método de autenticación | Identifica cómo se autentica el asunto del token. <br><br> **Valor de SAML de ejemplo**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valor de JWT de ejemplo**:`“amr”: ["pwd"]` |
| `given_name`| Nombre | Proporciona la primera o "" nombre de usuario, según esté definido en el objeto de usuario de Azure AD. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"given_name": "Frank"` |
| `groups`| Grupos | Proporciona los identificadores de objeto que representan los miembros del grupo del asunto. Estos valores son únicos (consulte el identificador de objeto) y pueden utilizarse para administrar el acceso, como aplicar autorización para tener acceso a un recurso de forma segura. Los grupos que se incluyen en la notificación de grupos se configuran en una base por aplicación, mediante la propiedad "groupMembershipClaims" del manifiesto de aplicación. Un valor nulo excluir todos los grupos, un valor de "SecurityGroup" incluirá únicamente las pertenencias a grupos de seguridad de Active Directory y un valor de "Todos" necesitarán incluyen grupos de seguridad y listas de distribución de Office 365. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Proveedor de identidades | El proveedor de identidades que se han autenticado al asunto del token de registros. Este valor es idéntico al valor de la notificación de emisor a menos que la cuenta de usuario está en un inquilino diferente que el emisor. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Almacena la hora en que fue emitido el token. A menudo se utiliza para medir la actualización de token. <br><br> **Valor de SAML de ejemplo**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valor de JWT de ejemplo**: <br> `"iat": 1390234181` |
| `iss` | Emisor | Identifica el servicio de token de seguridad (STS) que construye y devuelve el token. En los símbolos que devuelve Azure AD, el emisor es sts.windows.net. El GUID en el valor de la reclamación de emisor es el identificador de inquilinos del directorio de Azure AD. El identificador del inquilino es un identificador inmutable y confiable del directorio. <br><br> **Valor de SAML de ejemplo**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valor de JWT de ejemplo**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Apellido | Proporciona el último nombre, apellidos o nombre de la familia del usuario, según se define en el objeto de usuario de Azure AD. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"family_name": "Miller"` |
| `unique_name`| Nombre | Proporciona un valor legible humano que identifica al asunto del token. Este valor se garantiza que no sean únicas en un inquilino y está diseñado para usarse solo con fines de visualización. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | Identificador de objeto | Contiene un identificador único de un objeto en Azure AD. Este valor es inmutable y no se puede reasignar o volver a utilizar. Use el identificador de objeto para identificar un objeto en las consultas de Azure AD. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Roles | Representa todas las funciones de aplicación que el asunto se ha concedido tanto directa o indirectamente a través de la pertenencia a grupos y puede usarse para exigir el control de acceso basado en roles. Funciones de aplicación se definen en una base por aplicación, mediante la `appRoles` propiedad del manifiesto de aplicación. La `value` propiedad de cada función de la aplicación es el valor que aparece en la notificación de roles. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Ámbito | Indica los permisos de suplantación con la aplicación cliente. El permiso predeterminado es `user_impersonation`. El propietario del recurso protegido puede registrar valores adicionales de Azure AD. <br><br> **Valor de JWT de ejemplo**: <br> `"scp": "user_impersonation"`|
| `sub` |Asunto| Identifica la entidad de seguridad acerca de que el token declara información, como el usuario de una aplicación. Este valor no varía y no se pueden reasignar o volver a utilizar, se puede usar para realizar comprobaciones de autorización de forma segura. Dado que el asunto es siempre presente en los símbolos de los problemas de Azure AD, recomendamos usar este valor en un sistema de autorización de propósito general. <br> `SubjectConfirmation`no es una notificación. Describe cómo se comprueba el asunto del token. `Bearer`indica que el asunto es confirmado por dispongan del token. <br><br> **Valor de SAML de ejemplo**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valor de JWT de ejemplo**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | Id. de inquilinos | Un identificador inmutable, no reutilizable que identifica al inquilino de directorio que emitir el token. Puede usar este valor para tener acceso a los recursos de directorio inquilino específico en una aplicación de varios inquilino. Por ejemplo, puede usar este valor para identificar al inquilino en una llamada a la API de gráfico. <br><br> **Valor de SAML de ejemplo**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valor de JWT de ejemplo**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Duración del token | Define el intervalo de tiempo en el que es válido un símbolo. El servicio que valida el token debe comprobar que la fecha actual es dentro de la duración del token, más, debe rechazar el token. El servicio podría permitir hasta cinco minutos más allá del intervalo de la duración del token para tener en cuenta las diferencias en la hora ("desfase temporal") entre Azure AD y el servicio. <br><br> **Valor de SAML de ejemplo**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valor de JWT de ejemplo**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nombre Principal de usuario | Almacena el nombre de usuario de la identidad de usuario.<br><br> **Valor de JWT de ejemplo**: <br> `"upn": frankm@contoso.com`|
| `ver`| Versión | Almacena el número de versión del símbolo. <br><br> **Valor de JWT de ejemplo**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Símbolos de acceso

Símbolos de acceso están por Microsoft Services solo en este momento.  Las aplicaciones no es necesario llevar a cabo cualquier validación o comprobación de token de acceso para cualquiera de los escenarios compatibles actualmente.  Puede tratar tokens de acceso como completamente opaco: son simplemente cadenas que su aplicación puede pasar a Microsoft en las solicitudes HTTP.

Cuando se solicita un token de acceso, Azure AD devuelve también algunos metadatos sobre el token de acceso para el consumo de la aplicación.  Esta información incluye la hora de expiración del token de acceso y los ámbitos para los que es válida.  Esto permite a su aplicación para realizar inteligente almacenamiento en caché de tokens de acceso sin tener que analizar abrir el token de acceso propiamente dicho.

## <a name="refresh-tokens"></a>Actualizar tokens

Actualizar los tokens son símbolos de seguridad, que puede utilizar la aplicación para adquirir nuevos tokens de access en un flujo de OAuth 2.0.  Permite a su aplicación obtener acceso a largo plazo a los recursos en nombre de un usuario sin necesidad de interacción por el usuario.

Actualización tokens son varios de los recursos, lo que significa que es posible recibidos durante una solicitud de token para un recurso pero canjear por tokens de acceso a un recurso completamente diferente. Para especificar varios recursos, establezca el `resource` parámetro en la solicitud para el recurso destino.

Actualización tokens son totalmente opacos a su aplicación. Son larga, pero no se debe escribir su aplicación para que un símbolo de actualización durará durante un período de tiempo de espera.  Tokens de actualización pueden ser no válidas en cualquier momento para una variedad de motivos.  Es la única manera de la aplicación saber si un símbolo de actualización es válido intentar canjear realizando una solicitud de token al extremo de token de Azure AD.

Cuando canjear un token de actualización para un nuevo token de acceso, recibirá un nuevo token de actualización en la respuesta de token.  Debe guardar el token de actualización recién emitido reemplaza la utilizados en la solicitud.  Esto se garantiza que los tokens actualización siguen siendo válidos tanto como sea posible.

## <a name="validating-tokens"></a>Validar tokens

En este momento, la validación de token solo que la aplicación cliente es necesario realizar está validando id_tokens.  Para validar un id_token, la aplicación debería validar firma del id_token y las notificaciones en la id_token.

Le proporcionamos bibliotecas y ejemplos de código que muestran cómo controlar fácilmente validación token, si desea conocer el proceso subyacente.  También hay varias bibliotecas de terceros Abrir origen disponibles para la validación de JWT, al menos una opción para casi todas las plataformas e idioma. Para obtener más información acerca de las bibliotecas de autenticación de Azure AD y ejemplos de código, vea [bibliotecas de autenticación de Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validar la firma

Un JWT contiene tres segmentos, separadas por el `.` caracteres.  El primer segmento se conoce como el **encabezado**, el segundo como el **cuerpo**y la tercera como la **firma**.  El segmento de la firma puede usarse para validar la autenticidad de los id_token para que se puede confiar en la aplicación.

Id_Tokens están firmados con algoritmos de cifrado asimétricos estándar del sector, como RSA 256. El encabezado de la id_token contiene información sobre el método de cifrado y de clave utilizado para firmar el token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

La `alg` reclamación indica el algoritmo que se usó para firmar el token, mientras el `x5t` reclamación indica la clave pública determinada que se usó para firmar el token.

En cualquier momento determinado, Azure AD puede firmar una id_token con cualquiera de un conjunto determinado de pares de clave pública y privada. Azure AD gira el conjunto de claves de forma periódica, posibles para que su aplicación debe escribirse manejar esos cambios claves automáticamente.  Una frecuencia razonable para comprobar las actualizaciones a las claves públicas utilizadas Azure AD es cada 24 horas.

Para adquirir los datos de clave firma necesarios para validar la firma usando se encuentra en el documento de metadatos OpenID conectarse:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Pruebe esta dirección URL en un explorador.

Este documento de metadatos es un objeto JSON que contiene varios elementos útiles de información, como la ubicación de los distintos extremos necesarios para realizar la autenticación OpenID conectarse.  

También incluye un `jwks_uri`, que proporciona la ubicación del conjunto de claves públicas utilizado para firmar tokens.  El documento JSON se encuentra en la `jwks_uri` contiene toda la información de clave pública en uso en ese momento determinado.  Puede usar la aplicación de la `kid` reclamar en el encabezado JWT para seleccionar qué clave pública de este documento se ha utilizado para iniciar un símbolo concreto.  A continuación, puede realizar validación de firma con la clave pública correcta y el algoritmo indicado.

Realizar la validación de firma está fuera del ámbito de este documento, hay muchas bibliotecas de código abierto para ayudarle a hacerlo si es necesario.

#### <a name="validating-the-claims"></a>Validar las notificaciones

Cuando la aplicación recibe una id_token al inicio de sesión de usuario, también debe realizar algunas comprobaciones con las notificaciones de la id_token.  Incluir, pero no están limitados a:

  - La notificación de la **audiencia** - para comprobar que la id_token estaba destinada a la aplicación.
  - Las notificaciones **No antes** y **Fecha de caducidad** - para comprobar que la id_token no ha expirado.
  - La reclamación **emisor** - comprobar que el token de hecho emitió a su aplicación Azure AD.
  - El **valor de seguridad** - mitigar un ataque de reproducción de token.
  - y mucho más...

Para obtener una lista completa de validaciones reclamación que debe realizar la aplicación, consulte la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalles de los valores esperados para estas notificaciones se incluyen en la sección [id_token sección](#id-tokens) anterior.

## <a name="sample-tokens"></a>Tokens de ejemplo

Esta sección muestra ejemplos de tokens SAML y JWT que devuelve Azure AD. Estos ejemplos le permite ver las notificaciones en contexto.
Token SAML

Este es un ejemplo de un token SAML típico.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Símbolo de JWT - suplantación de usuario

Se trata de una muestra de un típico token de web JSON (JWT) utilizado en un flujo de concesión de código de autorización.
Además de reclamaciones, el token incluye un número de versión en **ver** y **appidacr**, la referencia de clase de contexto de autenticación, que indica cómo se autentica el cliente. Para un cliente público, el valor es 0. Si se ha usado un identificador de cliente o secreto de cliente, el valor es 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Contenido relacionado
- Vea las [operaciones de directiva](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) de Azure AD Graph y la [entidad de directiva](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), para obtener más información acerca de administración de la directiva de duración del token a través de la API de Azure AD Graph.
- Para obtener más información y ejemplos sobre cómo administrar las directivas a través de los cmdlets de PowerShell, incluidos los ejemplos, consulte [duraciones de tokens Configurable en Azure AD](active-directory-configurable-token-lifetimes.md). 
