<properties
   pageTitle="Con aserción de cliente para obtener acceso a tokens de Azure AD | Microsoft Azure"
   description="Cómo utilizar aserción de cliente para obtener acceso a tokens de Azure AD."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="using-client-assertion-to-get-access-tokens-from-azure-ad"></a>Usar aserción de cliente para obtener acceso tokens de Azure AD

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

## <a name="background"></a>Fondo

Cuando se usa flujo de código de autorización o híbrido flujo OpenID conectarse, el cliente de intercambios un código de autorización para un token de acceso. Durante este paso, el cliente debe autenticarse en el servidor.

![Secreto de cliente](media/guidance-multitenant-identity/client-secret.png)

Una forma para autenticar al cliente es usando un secreto de cliente. Eso cómo las [Encuestas Tailspin] [ Surveys] aplicación está configurada de manera predeterminada.

Aquí tiene una solicitud de ejemplo desde el cliente para el IDP, solicitar un token de acceso. Nota la `client_secret` parámetro.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

El secreto es una cadena, para que tenga que asegurarse de no perder el valor. Lo mejor es mantener el secreto de cliente fuera del control de código fuente. Cuando se implementa en Azure, almacenar el secreto en una [configuración de aplicación][configure-web-app].

Sin embargo, cualquier persona con acceso a la suscripción de Azure puede ver la configuración de la aplicación. Además, es siempre conlleva para proteger la información confidencial en control de código fuente (por ejemplo, en las secuencias de comandos de implementación), compártalos por correo electrónico y así sucesivamente.

Para obtener seguridad adicional, puede usar [aserción de cliente] en lugar de un secreto de cliente. Con aserción de cliente, el cliente utiliza un certificado X.509 para demostrar que la solicitud de token suministrado desde el cliente. El certificado de cliente está instalado en el servidor web. En general, será más fácil restringir el acceso al certificado de para asegurarse de que nadie accidentalmente revela un secreto de cliente. Para obtener más información sobre la configuración de certificados en una aplicación web, vea [Usar certificados en las aplicaciones de sitios Web de Azure][using-certs-in-websites]

Esto es una solicitud de token con aserción de cliente:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Observe que la `client_secret` parámetro ya no se utiliza. En su lugar, la `client_assertion` parámetro contiene un símbolo JWT que se ha iniciado sesión con el certificado de cliente. La `client_assertion_type` parámetro especifica el tipo de aserción &mdash; en este caso, token JWT. El servidor valida el token JWT. Si el token JWT no es válido, la solicitud de token devuelve un error.

> [AZURE.NOTE] Certificados X.509 no son la única forma de aserción del cliente; se centrará en él aquí porque es compatible con Azure AD.

## <a name="using-client-assertion-in-the-surveys-application"></a>Uso de aserción de cliente en la aplicación de encuestas

Esta sección muestra cómo configurar la aplicación Tailspin encuestas usar aserción de cliente. En estos pasos, se generará un certificado autofirmado que es adecuado para el desarrollo, pero no para producción.

1. Ejecutar la secuencia de comandos de PowerShell [/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] como sigue:

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Para el `Subject` parámetro, escriba un nombre, como "surveysapp". La secuencia de comandos genera un certificado autofirmado y lo almacena en el almacén de certificados "usuario actual/Personal".

2. El resultado de la secuencia de comandos es un fragmento JSON. Agregue lo siguiente a la información de la aplicación de la aplicación web, como sigue:

    1. Inicie sesión en el [portal de administración de Azure] [ azure-management-portal] y vaya al directorio de Azure AD.

    2. Haga clic en **aplicaciones**.

    3. Seleccione la aplicación de encuestas.

    4.  Haga clic en **Administrar manifiestos** y seleccione **Descargar manifiestos**.

    5.  Abra el archivo JSON manifiesto en un editor de texto. Pegar el resultado de la secuencia de comandos en el `keyCredentials` propiedad. Debe ser similar al siguiente:

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.  Guardar los cambios en el archivo JSON.

    7.  Volver al portal. Haga clic en **Administrar manifiestos** > **Cargar manifiestos** y cargar el archivo JSON.

3. Ejecute el comando siguiente para obtener la huella digital del certificado.

    ```
    certutil -store -user my [subject]
    ```

    donde `[subject]` es el valor especificado para el asunto en la secuencia de comandos de PowerShell. Aparece la huella digital en "certificado"hash (SHA1). Quitar los espacios entre los números en hexadecimal.

4. Actualizar la información confidencial de la aplicación. En el Explorador de soluciones, haga clic en el proyecto Tailspin.Surveys.Web y seleccione **Administrar información confidencial de usuario**. Agregar una entrada para "Asimétrico" en "AzureAd", como se muestra a continuación:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    Debe establecer `ValidationRequired` en false, porque el certificado no firmado por una autoridad de entidad emisora de certificados raíz. En producción, use un certificado firmado por una autoridad CA y `ValidationRequired` en true.

    También se elimina la entrada de `ClientSecret`, porque no es necesario con aserción de cliente.

5. En Startup.cs, busque el código que registra la `ICredentialService`. Elimine la línea que usa `CertificateCredentialService`y un comentario la línea que usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

En tiempo de ejecución, la aplicación web lee el certificado del almacén de certificados. El certificado debe estar instalado en el mismo equipo que la aplicación web.

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [Usar depósito de Azure clave para proteger la información confidencial de la aplicación][key vault]


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[aserción de cliente]: https://tools.ietf.org/html/rfc7521
[key vault]: guidance-multitenant-identity-keyvault.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[parte de una serie]: guidance-multitenant-identity.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
