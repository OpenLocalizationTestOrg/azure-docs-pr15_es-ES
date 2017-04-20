<properties
    pageTitle="Metadatos de federación de Azure AD | Microsoft Azure"
    description="En este artículo se describe el documento de metadatos de federación publica Azure Active Directory para los servicios que aceptan tokens de Azure Active Directory."
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


# <a name="federation-metadata"></a>Metadatos de federación

Azure Active Directory (AD Azure) publica un documento de metadatos de federación para los servicios que ha configurado para aceptar los tokens de seguridad que emite Azure AD. En la [versión 1.2 de lenguaje de federación de servicios Web (WS-Federation)](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que extiende [metadatos para la versión 2.0 de lenguaje de marcado de aserción de seguridad (SAML) de OASIS](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)se describe el formato de documento de metadatos de federación.

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Específicos del inquilino y extremos de metadatos del inquilino independiente

Azure AD publica extremos específicas del inquilino e independientes de inquilinos.

Extremos específicos del inquilino están diseñados para un inquilino determinado. Los metadatos de federación específicas del inquilino incluyen información acerca del inquilino, incluida la información de emisor y extremo específicas del inquilino. Aplicaciones que restringir el acceso a un único inquilino utilizar extremos específicos del inquilino.

Extremos independiente del inquilino proporcionan información común para todos los Azure AD inquilinos. Esta información se aplica a los inquilinos hospedados en *://Login.microsoftonline.com)* y se comparte entre los inquilinos. Independiente del inquilino extremos se recomiendan para las aplicaciones de múltiples arrendatarios, ya que no están asociados con cualquier particular inquilinos.

## <a name="federation-metadata-endpoints"></a>Extremos de metadatos de federación

Azure AD publica metadatos de federación en `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **extremos específicos de inquilinos**, la `TenantDomainName` puede ser uno de los siguientes tipos:

- Un nombre de dominio registrado de un anuncio de Azure de inquilinos, como por ejemplo: `contoso.onmicrosoft.com`.
- La inmutable inquilinos identificador del dominio, como por ejemplo `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **extremos independiente del inquilino**, la `TenantDomainName` es `common`. Este documento enumera los elementos de metadatos de federación que sean comunes a todos los inquilinos de Azure AD hospedadas en://Login.microsoftonline.com).

Por ejemplo, podría ser un extremo específicas del inquilino `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. El extremo del inquilino independiente es [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Puede ver el documento de metadatos de federación escribiendo esta dirección URL en un explorador.

## <a name="contents-of-federation-metadata"></a>Contenido de metadatos de federación

La siguiente sección proporciona información necesaria para los servicios que utilizan los símbolos de Azure AD.

### <a name="entity-id"></a>Id. de entidad

La `EntityDescriptor` elemento contiene un `EntityID` atributo. El valor de la `EntityID` atributo representa el emisor, es decir, el token servicio de seguridad (STS) que emite el token. Es importante validar al emisor cuando reciba un símbolo.

Los metadatos siguientes muestran un ejemplo específico del inquilino `EntityDescriptor` elemento con un `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Puede reemplazar el identificador del inquilino en el extremo del inquilino independiente con su ID de inquilinos para crear una específica de inquilinos `EntityID` valor. El valor resultante será el mismo que el emisor de tokens. La estrategia permite a una aplicación de varios inquilino validar al emisor de un inquilino determinado.

Los metadatos siguientes muestran un ejemplo independiente del inquilino `EntityID` elemento. Tenga en cuenta que el `{tenant}` es un literal, no un marcador de posición.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de firma de tokens

Cuando un servicio recibe un símbolo que haya emitido un inquilino de Azure AD, se debe validar la firma del token con una clave de firma que se publica en el documento de metadatos de federación. Los metadatos de federación incluyen la parte pública de los certificados que usan los inquilinos de firma de tokens. Los bytes sin formato del certificado aparecen en la `KeyDescriptor` elemento. El certificado de firma de token sea válido para firmar únicamente cuando el valor de la `use` atributo es `signing`.

Un documento de metadatos de federación publicado por Azure AD puede tener varias claves de firma, como cuando está preparando Azure AD para actualizar el certificado de firma. Cuando un documento de metadatos de federación incluye más de un certificado, un servicio que se está validando los tokens debe admitir todos los certificados en el documento.

Los siguientes metadatos muestran un ejemplo `KeyDescriptor` elemento con una clave de firma.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

La `KeyDescriptor` elemento aparece en dos lugares en el documento de metadatos de federación. en la sección específica de la federación de WS y la sección SAML específica. Los certificados publicados en ambas secciones será el mismo.

En la sección específica de la federación de WS, un lector de metadatos de federación de WS sería los certificados de un `RoleDescriptor` elemento con el `SecurityTokenServiceType` tipo.

Los siguientes metadatos muestran un ejemplo `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

En la sección SAML específica, un lector de metadatos de federación de WS sería los certificados de un `IDPSSODescriptor` elemento.

Los siguientes metadatos muestran un ejemplo `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
No existen diferencias en el formato de certificados específicas del inquilino e independientes de inquilinos.

### <a name="ws-federation-endpoint-url"></a>Dirección URL del extremo de WS-Federation

Los metadatos de federación incluyen la dirección URL que utiliza Azure AD para el inicio de sesión único y cierre de sesión de protocolo WS-Federation único. Este extremo aparece en la `PassiveRequestorEndpoint` elemento.

Los siguientes metadatos muestran un ejemplo `PassiveRequestorEndpoint` elemento para un extremo del inquilino específicos.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
El extremo del inquilino independiente, la dirección URL de la federación de WS aparece en el extremo WS-Federation, como se muestra en el ejemplo siguiente.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Dirección URL del extremo de protocolo SAML

Los metadatos de federación incluyen la dirección URL que Azure AD se usa para el inicio de sesión único y solo cerrar sesión en protocolo SAML 2.0. Estos extremos aparecen en la `IDPSSODescriptor` elemento.

Las direcciones URL de inicio de sesión y cierre de sesión aparecen en la `SingleSignOnService` y `SingleLogoutService` elementos.

Los siguientes metadatos muestran un ejemplo `PassiveResistorEndpoint` para un extremo del inquilino específicos.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Del mismo modo los extremos de los extremos de protocolo SAML 2.0 comunes se publican en los metadatos de federación independiente del inquilino, como se muestra en el ejemplo siguiente.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
