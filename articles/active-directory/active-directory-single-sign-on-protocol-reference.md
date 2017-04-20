<properties
    pageTitle="Inicio de sesión único de Azure en SAML protocolo | Microsoft Azure"
    description="En este artículo se describe el protocolo de inicio de sesión único en SAML de Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="single-sign-on-saml-protocol"></a>Protocolo de SAML de inicio de sesión único

Este artículo tratan las solicitudes de autenticación de SAML 2.0 y las respuestas que es compatible con Azure Active Directory (AD Azure) para el inicio de sesión único.

El diagrama de protocolo a continuación describe la secuencia de inicio de sesión único. El servicio de nube (el proveedor de servicios) utiliza un enlace de redirección HTTP para pasar un `AuthnRequest` elemento (solicitud de autenticación) a Azure AD (el proveedor de identidades). Azure AD, a continuación, utiliza HTTP post enlace para publicar un `Response` elemento del servicio de nube.

![Flujo de trabajo de inicio de sesión único](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Para solicitar una autenticación de usuario, servicios de nube enviar una `AuthnRequest` elemento a Azure AD. Un ejemplo SAML 2.0 `AuthnRequest` puede tener un aspecto similar a este:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| ID. | Obligatorio | Azure AD utiliza este atributo para rellenar la `InResponseTo` atributo de la respuesta devuelta. Identificador no debe comenzar con un número, por lo que es una estrategia común anteponga una cadena como "id" para la representación de cadena de GUID. Por ejemplo, `id6c1c178c166d486687be4aaf5e482730` es un identificador válido. |
| Versión | Obligatorio | Debe ser **2.0**.|
| IssueInstant | Obligatorio | Se trata de una cadena de fecha y hora con un valor de UTC y el [formato de ida y vuelta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD espera un valor de fecha y hora de este tipo, pero no se evalúan o use el valor. |
| AssertionConsumerServiceUrl | opcional | Si se proporciona, debe coincidir con la `RedirectUri` del servicio de nube de Azure AD. |
| ForceAuthn | opcional | Si se proporciona, debe ser false. Cualquier otro valor, produce un error.|
| IsPassive | opcional | Si se proporciona, debe ser false. Cualquier otro valor, produce un error. |  

Todos los demás `AuthnRequest` atributos como consentimiento, destino, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex y ProviderName **pasan por alto**.

Azure AD también pasa por alto el `Conditions` elemento en `AuthnRequest`.

### <a name="issuer"></a>Emisor

La `Issuer` elemento en una `AuthnRequest` debe coincidir exactamente con uno de los **ServicePrincipalNames** en el servicio de nube de Azure AD. Normalmente, esto se establece al **URI de ID de aplicación** que se especifica durante el registro de aplicación.

Un extracto SAML muestra que contiene la `Issuer` elemento es similar a esta:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Este elemento solicita un formato de Id. de nombre determinado en la respuesta y es opcional en `AuthnRequest` elementos enviados a Azure AD.

Un ejemplo `NameIdPolicy` elemento es similar a esta:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Si `NameIDPolicy` es siempre puede incluir su opcional `Format` atributo. La `Format` atributo sólo puede tener uno de los siguientes valores; cualquier otro valor producirá un error.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory emite la reclamación NameID como un identificador en pares.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory emite la reclamación NameID en formato de dirección de correo electrónico.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Este valor permite Azure Active Directory para seleccionar el formato de la notificación. Azure Active Directory emite el NameID como un identificador en pares.

No incluya la `SPNameQualifer` atributo. Azure AD pasa por alto el `AllowCreate` atributo.

### <a name="requestauthncontext"></a>RequestAuthnContext

La `RequestedAuthnContext` elemento especifica los métodos de autenticación que desee. Es opcional en `AuthnRequest` elementos enviados a Azure AD. Azure AD admite solo uno `AuthnContextClassRef` valor: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Especificar el ámbito

La `Scoping` elemento, que incluye una lista de proveedores de identidades, es opcional en `AuthnRequest` elementos enviados a Azure AD.

Si se proporciona, no incluya la `ProxyCount` atributo, `IDPListOption` o `RequesterID` elemento, como no son compatibles.

### <a name="signature"></a>Firma

No incluya un `Signature` elemento en `AuthnRequest` elementos, como Azure AD no admite firmado las peticiones de autenticación.

### <a name="subject"></a>Asunto

Azure AD pasa por alto el `Subject` elemento de `AuthnRequest` elementos.

## <a name="response"></a>Respuesta

Cuando se completa correctamente un inicio de sesión solicitado, Azure AD envía una respuesta al servicio de nube. Una respuesta de ejemplo en un intento de inicio de sesión correcto es similar a esta:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Respuesta

La `Response` elemento incluye el resultado de la solicitud de autorización. Conjuntos de Azure AD el `ID`, `Version` y `IssueInstant` valores en los `Response` elemento. También establece los siguientes atributos:

- `Destination`: Al inicio de sesión se completa correctamente, esto se establece en el `RedirectUri` del proveedor de servicios (servicio de nube).
- `InResponseTo`: Se establece en el `ID` atributo de la `AuthnRequest` elemento que inició la respuesta.

### <a name="issuer"></a>Emisor

Conjuntos de Azure AD la `Issuer` elemento `https://login.microsoftonline.com/<TenantIDGUID>/` donde <TenantIDGUID> es el identificador del inquilino del inquilino de Azure AD.

Por ejemplo, una respuesta de ejemplo con elemento emisor puede tener un aspecto similar a este:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado

La `Status` elemento concede el éxito o error de inicio de sesión. Incluye la `StatusCode` elemento, que contiene un código o un conjunto de códigos anidados que representan el estado de la solicitud. También incluye la `StatusMessage` elemento, que contiene los mensajes de error personalizados que se generan durante el proceso de inicio de sesión.

<!-- TODO: Add a authentication protocol error reference -->

La siguiente es una respuesta SAML a un intento de inicio de sesión incorrecto.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Aserción

Además del `ID`, `IssueInstant` y `Version`, Azure AD conjuntos de los elementos siguientes en la `Assertion` elemento de la respuesta.

#### <a name="issuer"></a>Emisor

Esto se establece en `https://sts.windows.net/<TenantIDGUID>/`donde <TenantIDGUID> es el identificador del inquilino del inquilino de Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Firma

Azure AD firma la aserción en respuesta a un inicio de sesión correctamente. La `Signature` elemento contiene una firma digital que puede usar el servicio de nube para autenticar el origen de para comprobar la integridad de la aserción.

Para generar la firma digital, Azure AD utiliza la clave de firma en el `IDPSSODescriptor` elemento de su documento de metadatos.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Asunto

Especifica la entidad de seguridad es el asunto de las instrucciones de la aserción. Contiene un `NameID` elemento, que representa el usuario autenticado. La `NameID` valor es un identificador destino que se dirige únicamente al proveedor de servicios que es la audiencia del token. Es persistente: se puede revocar, pero nunca se reasigna. También es opaco, ya que no muestra nada acerca del usuario y no se puede utilizar como un identificador de consultas de atributo.

La `Method` atributo de la `SubjectConfirmation` elemento siempre se establece en `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condiciones

Este elemento especifica las condiciones que definen el uso aceptable de aserciones SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

La `NotBefore` y `NotOnOrAfter` atributos especifican el intervalo durante el cual es válida la aserción.

- El valor de la `NotBefore` atributo sea igual o ligeramente (menos de un segundo) posterior al valor de `IssueInstant` atributo de la `Assertion` elemento. Azure AD no tiene en cuenta para cualquier diferencia de tiempo entre sí y el servicio de nube (proveedor de servicios) y no agrega cualquier búfer en este momento.
- El valor de la `NotOnOrAfter` atributo es 70 minutos más tarde que el valor de la `NotBefore` atributo.

#### <a name="audience"></a>Audiencia

Esta página contiene un URI que identifica un público objetivo. Azure AD establece el valor de este elemento en el valor de `Issuer` elemento de la `AuthnRequest` que inició la sesión. Para evaluar la `Audience` valor, use el valor de la `App ID URI` que se ha especificado durante el registro de aplicación.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Al igual que el `Issuer` valor, el `Audience` valor debe coincidir exactamente con uno de los nombres principales de servicio que representa el servicio de nube de Azure AD. Sin embargo, si el valor de la `Issuer` elemento no es un valor de URI del `Audience` valor en la respuesta es la `Issuer` valor con el prefijo `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Esta página contiene notificaciones sobre el asunto o el usuario. En el siguiente extracto contiene una muestra `AttributeStatement` elemento. Los puntos suspensivos indican que el elemento puede incluir varios atributos y valores de atributo.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **Notificación de nombre** : el valor de la `Name` atributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) es el nombre de usuario principal del usuario autenticado, como `testuser@managedtenant.com`.
- **Notificación de ObjectIdentifier** : el valor de la `ObjectIdentifier` atributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) es el `ObjectId` del objeto de directorio que representa el usuario autenticado en Azure AD. `ObjectId`es inmutable, globalmente único y volver a utilizar seguro identificador del usuario autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Este elemento declara que el asunto de aserción autenticado por un medio determinado en un momento determinado.

- La `AuthnInstant` atributo especifica la hora en que el usuario autenticado con Azure AD.
- La `AuthnContext` elemento especifica el contexto de autenticación utilizado para autenticar al usuario.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
