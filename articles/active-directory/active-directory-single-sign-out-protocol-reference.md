<properties
    pageTitle="Inicio de sesión único de Azure fuera SAML protocolo | Microsoft Azure"
    description="Este artículo describe el protocolo de SAML Sign-Out único de Azure Active Directory"
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


# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de cierre de sesión único

Perfil de cierre de sesión único de explorador web de Azure Active Directory (AD Azure) es compatible con SAML 2.0. Para cerrar sesión único para que funcione correctamente, Azure AD debe registrar su dirección URL de metadatos durante el registro de aplicación. Azure AD Obtiene la dirección URL de cierre de sesión y la clave de firma del servicio de nube de los metadatos. Azure AD utiliza la clave de firma para comprobar la firma de la LogoutRequest entrante y utiliza la LogoutURL para redirigir a los usuarios una vez cerrados sesión.

Si el servicio de nube no admite un extremo de metadatos, una vez que se registra la aplicación, el desarrollador debe ponerse en contacto con soporte técnico de Microsoft para proporcionar la dirección URL de cierre de sesión y la clave de firma.

Este diagrama muestra el flujo de trabajo del proceso de cierre de sesión único de Azure AD.

![Inicio de sesión único el flujo de trabajo](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

El servicio de nube le enviará un `LogoutRequest` mensaje a Azure AD para indicar que ha finalizado una sesión. El siguiente fragmento muestra un ejemplo `LogoutRequest` elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

La `LogoutRequest` elemento enviado a Azure AD requiere los siguientes atributos:

- `ID`: Identifica la solicitud de cierre de sesión. El valor de `ID` no debe comenzar con un número. La práctica típica es anexar **identificador** a la representación de cadena de GUID.

- `Version`: Establecer el valor de este elemento en **2.0**. Este valor es necesario.

- `IssueInstant`: Se trata de un `DateTime` de cadena con un valor de tiempo Universal coordinado (UTC) y el [formato de ida y vuelta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD espera un valor de este tipo, pero no la exige.

- El `Consent`, `Destination`, `NotOnOrAfter` y `Reason` atributos se omiten si se incluyen en un `LogoutRequest` elemento.

### <a name="issuer"></a>Emisor

La `Issuer` elemento en una `LogoutRequest` debe coincidir exactamente con uno de los **ServicePrincipalNames** en el servicio de nube de Azure AD. Normalmente, esto se establece al **URI de ID de aplicación** que se especifica durante el registro de aplicación.

### <a name="nameid"></a>NameID

El valor de la `NameID` elemento debe coincidir con la `NameID` del usuario al que se cierra la sesión.
## <a name="logoutresponse"></a>LogoutResponse

Envía de Azure AD un `LogoutResponse` en respuesta a un `LogoutRequest` elemento. El siguiente fragmento muestra un ejemplo `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Conjuntos de Azure AD el `ID`, `Version` y `IssueInstant` valores en los `LogoutResponse` elemento. También se establece la `InResponseTo` elemento con el valor de la `ID` atributos de la `LogoutRequest` que produce la respuesta.

### <a name="issuer"></a>Emisor

Azure AD establece este valor en `https://login.microsoftonline.com/<TenantIdGUID>/` donde <TenantIdGUID> es el identificador del inquilino del inquilino de Azure AD.

Para evaluar el valor de la `Issuer` elemento, use el valor del **Identificador URI de aplicación** proporcionó durante el registro de aplicación.

### <a name="status"></a>Estado

Usos de Azure AD la `StatusCode` elemento de la `Status` elemento para indicar el éxito o error de cierre de sesión. Cuando se produce un error en el intento de cierre de sesión, la `StatusCode` elemento también puede contener los mensajes de error personalizados.
