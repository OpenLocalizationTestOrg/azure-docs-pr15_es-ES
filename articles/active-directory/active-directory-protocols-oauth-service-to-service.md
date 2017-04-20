<properties
    pageTitle="Auth de servicio a Azure AD con OAuth2.0 | Microsoft Azure"
    description="Este artículo describe cómo usar mensajes HTTP para implementar la autenticación de servicio para el servicio con el flujo de conceder OAuth2.0 credenciales de cliente."
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

# <a name="service-to-service-calls-using-client-credentials"></a>Servicio de llamadas de servicio con las credenciales del cliente

El flujo de OAuth 2.0 cliente credenciales conceder permite a un servicio web (un *cliente confidencial*) para usar sus propias credenciales para autenticar al llamar a otro servicio web, en lugar de suplantación de un usuario. En este escenario, el cliente normalmente es un servicio web de nivel medio, un servicio demonio o sitio web.

## <a name="client-credentials-grant-flow-diagram"></a>Diagrama de flujo de concesión de las credenciales del cliente

En el diagrama siguiente se explica cómo las credenciales del cliente concesión funciona de flujo en Azure Active Directory (AD Azure).

![Flujo de concesión de credenciales de cliente OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. La aplicación cliente se autentica en el extremo de emisión de token de Azure AD y solicita un token de acceso.
2. El extremo de emisión de token de Azure AD emite el token de acceso.
3. El token de acceso se utiliza para autenticar al recurso protegido.
4. Datos de los recursos protegidos se devuelven a la aplicación web.

## <a name="register-the-services-in-azure-ad"></a>Registrar los servicios en Azure AD

Registrar el servicio de llamadas y el servicio de recepción de Azure Active Directory (AD Azure). Para obtener instrucciones detalladas, vea [Agregar, actualizar y quitar una aplicación](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Solicitar un Token de acceso

Para solicitar un token de acceso, utilice un HTTP POST a específico del inquilino extremo de Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Solicitud de token de acceso de servicio de servicio

Una solicitud de token de acceso al servicio contiene los siguientes parámetros.

| Parámetro | | Descripción |
|-----------|------|------------|
| response_type | Obligatorio | Especifica el tipo de respuesta solicitada. En un flujo de concesión de credenciales de cliente, el valor debe ser **client_credentials**.|
| client_id | Obligatorio | Especifica el identificador de cliente de Azure AD del servicio web llamada. Para buscar el ID de cliente de la aplicación llamada, en el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el directorio y, a continuación, haga clic en la aplicación y, a continuación, haga clic en **Configurar**.|
| client_secret | Obligatorio |  Escriba una clave registrada para el servicio web de llamada en Azure AD. Para crear una clave en el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el directorio, haga clic en la aplicación y, a continuación, haga clic en **Configurar**. |
| recursos | Obligatorio | Escriba el URI de identificador de aplicación del servicio web recepción. Para buscar el URI de identificador de aplicación, en el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el directorio y, a continuación, haga clic en la aplicación y, a continuación, haga clic en **Configurar**. |

## <a name="example"></a>Ejemplo

La siguiente HTTP POST solicita un token de acceso para el servicio web de https://service.contoso.com/. La `client_id` identifica el servicio web que solicita el token de acceso.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Respuesta de Token de acceso de servicio de servicio

Una respuesta de éxito contiene una respuesta de JSON OAuth 2.0 con los siguientes parámetros.

| Parámetro   | Descripción |
|-------------|-------------|
|access_token |El token de acceso solicitado. El servicio web llamada puede utilizar este token para autenticar al servicio web recepción. |
|access_type  | Indica el valor de tipo de token. El único tipo que admite Azure AD es **portador**. Para obtener más información sobre los tokens portador, consulte la [marco de autorización de OAuth 2.0: uso de Token portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | ¿Cuánto tiempo el token de acceso es válido (en segundos).|
|expires_on   |El tiempo cuando expira el token de acceso. El número de segundos que representa la fecha de 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. Este valor se utiliza para determinar la duración de tokens en caché. |
|recursos     | URI de identificador de aplicación del servicio web recepción. |

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una respuesta de éxito a una solicitud de un token de acceso a un servicio web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Vea también

* [OAuth 2.0 en Azure AD](active-directory-protocols-oauth-code.md)
