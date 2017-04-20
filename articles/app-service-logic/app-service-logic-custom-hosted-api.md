<properties
    pageTitle="Llamar a una API personalizada en aplicaciones de lógica"
    description="Con la API personalizada hospedada en la aplicación de servicio con aplicaciones de lógica"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>Con la API personalizada hospedada en la aplicación de servicio con aplicaciones de lógica

Aunque las aplicaciones de lógica tiene un amplio conjunto de conectores de 40 para una gran variedad de servicios, desea llamar en su propia API personalizada que puede ejecutar su propio código. Una de las formas más sencilla y escalabilidad para hospedar su propio web personalizados de la API es usar la aplicación de servicio. En este artículo se explica cómo llamar a cualquier API alojado en una aplicación, aplicación web o una aplicación móvil de API del servicio de aplicación web.

Para obtener información sobre la creación de API como un desencadenador o acción dentro de aplicaciones de lógica, consulte [este artículo](app-service-logic-create-api-app.md).

## <a name="deploy-your-web-app"></a>Implemente la aplicación Web

En primer lugar, deberá implementar la API como una aplicación Web en la aplicación de servicio. Las instrucciones aquí cubren implementación básica: [crear una aplicación web de ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Aunque puede llamar a cualquier API desde una aplicación de lógica, para obtener la mejor experiencia, se recomienda agregar metadatos Swagger integrar fácilmente con acciones de aplicaciones de lógica.  Puede encontrar más detalles sobre [cómo agregar swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Configuración de la API

Para el Diseñador de aplicaciones de lógica analizar su Swagger, es importante que CORS de habilitar y configurar las propiedades de APIDefinition de la aplicación web.  Esto es muy fácil de configurar en el Portal de Azure.  Abra el módulo de configuración de la aplicación Web y en la sección API, establezca la API 'definición' a la dirección URL del archivo swagger.json (suele ser https://{name}.azurewebsites.net/swagger/docs/v1) y agregue una directiva CORS para simplemente ' *' para permitir las solicitudes de las aplicaciones de lógica diseñador.

## <a name="calling-into-the-api"></a>Llamar a la API

Cuando se encuentra en el portal de aplicaciones de lógica, si ha establecido CORS y las propiedades de la definición de la API debería poder fácilmente agregar acciones personalizadas API dentro del flujo.  En el diseñador puede seleccionar para examinar los sitios Web de suscripción para obtener una lista de los sitios Web con una dirección URL de swagger definida.  También puede usar el HTTP + Swagger acción puede apuntar a un swagger y acciones disponibles y entradas de lista.  Por último, siempre puede crear una solicitud de uso de la acción HTTP para llamar a cualquier API, incluso los que no tienen o exponer a un documento swagger.

Si desea proteger su API, hay varias maneras diferentes de hacerlo:

1. Ningún cambio de código necesario - Azure Active Directory puede usarse para proteger su API sin necesidad de los cambios de código o la redistribución.
1. Exigir la autenticación básica, AAD Auth o autenticación de certificado en el código de la API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Llamadas a la API sin un cambio de código de seguridad

En esta sección, debe crear dos aplicaciones de Azure Active Directory: uno para la aplicación de lógica y otro para la aplicación Web.  Deberá autenticar llamadas a su aplicación Web con la identidad de servicio (identificador de cliente y secreto) asociada con la aplicación de AAD para la aplicación de la lógica. Por último, se incluyen la aplicación ID en la definición de aplicación de la lógica.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configurar una identidad de aplicación de la aplicación de lógica

Esto es lo que la aplicación de la lógica que se utiliza para autenticarse en active directory. Solo *necesita* hacer esto una vez para el directorio. Por ejemplo, puede elegir usar la misma identidad para todas las aplicaciones de lógica, aunque también se pueden crear identidades únicas por aplicación lógica si lo desea. Puede hacerlo en la interfaz de usuario o usar PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Crear la identidad de la aplicación con el portal de clásico de Azure

1. Vaya a [Active directory en el portal de clásico Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) y seleccione el directorio que usa para la aplicación Web
2. Haga clic en la ficha **aplicaciones**
3. Haga clic en **Agregar** en la barra de comandos en la parte inferior de la página
4. Asigne un nombre a utilizar, haga clic en la flecha siguiente a su identidad
5. Poner en una cadena única con formato de un dominio en los cuadros de texto y a continuación, haga clic en la marca de verificación
6. Haga clic en la ficha **Configurar** para esta aplicación
7. Copiar el **Identificador de cliente**, se utilizará en la aplicación de lógica
8. En la sección de **teclas** , haga clic en **Seleccionar duración** y elija año 1 o 2 años
9. Haga clic en el botón **Guardar** en la parte inferior de la pantalla (que deba esperar unos pocos segundos)
10. Ahora, asegúrese de copiar la clave en el cuadro. También se utilizará en la aplicación de lógica

#### <a name="create-the-application-identity-using-powershell"></a>Crear la identidad de la aplicación con PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Asegúrese de copiar el **Identificador del inquilino**, el **Identificador de la aplicación** y la contraseña que utilizó

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Parte 2: Proteger su aplicación Web con una identidad de aplicación AAD

Si ya se ha implementado la aplicación Web solo se puede habilitar en el portal. En caso contrario, puede hacer que habilitar la autorización en parte de la implementación del Administrador de recursos de Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Habilitar la autorización en el Portal de Azure

1. Vaya a la aplicación Web y haga clic en la **configuración** de la barra de comandos.
2. Haga clic en **Autorización/autenticación**.
3. **Activar.**

En este momento, se crea automáticamente una aplicación para usted. Necesita ID de cliente de la aplicación para el elemento 3, así que deberá:

1. Vaya a [Active directory en el portal de clásico Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) y seleccione el directorio.
2. Busque la aplicación en el cuadro de búsqueda
3. Haga clic en ella en la lista
4. Haga clic en la ficha **Configurar**
5. Debería ver el **Identificador del cliente**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Implementación de la aplicación Web mediante una plantilla de ARM

En primer lugar, debe crear una aplicación para la aplicación Web. Debe ser diferente de la aplicación que se usa para la aplicación de la lógica. Inicie siguiendo los pasos anteriores en la parte 1, pero ahora para el uso de la **página principal** y **IdentifierUris** la**dirección URL** de https:// real de la aplicación Web.

>[AZURE.NOTE]Cuando se crea la aplicación para la aplicación Web, debe utilizar el [enfoque de portal clásico Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), como el cmdlet de PowerShell no configura los permisos necesarios para iniciar la sesión de los usuarios en un sitio Web.

Una vez que haya el cliente de identificador e inquilino, se incluyen las siguientes como un recurso de sub de la aplicación Web en la plantilla de implementación:

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Para ejecutar una implementación automáticamente que implementa una aplicación Web en blanco y la aplicación de lógica junto que usan AAD, haga clic en el botón siguiente:

[![Implementar en Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para la plantilla completa, consulte [llamadas de la aplicación de lógica en una API personalizado hospedado en la aplicación de servicio y protegido por AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Parte 3: Rellenar la sección autorización en la aplicación de lógica

En la sección de **autorización** de la acción de **HTTP** :`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descripción |
|---------|-------------|
| tipo | Tipo de autenticación. Autenticación de ActiveDirectoryOAuth, el valor es ActiveDirectoryOAuth. |
| inquilino | El identificador del inquilino usado para identificar al inquilino AD. |
| audiencia | Obligatorio. El recurso. que se está conectando. |
| clientID | El identificador de cliente de la aplicación de Azure AD. |
| secreto | Obligatorio. Secreto del cliente que solicita el token. |

La plantilla anterior ya tiene esta configuración, pero si va a crear la aplicación de la lógica de directamente, deberá incluir la sección autorización completa.

## <a name="securing-your-api-in-code"></a>Proteger la API en código

### <a name="certificate-auth"></a>Autenticación de certificado

Puede usar los certificados de cliente para validar las solicitudes entrantes a la aplicación Web. Vea [Cómo configurar TLS mutuo autenticación aplicación Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) de cómo configurar el código.

En la sección *autorización* debe proporcionar: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descripción |
|---------|-------------|
| tipo | Obligatorio. Tipo de autenticación. De certificados SSL de cliente, el valor debe ser ClientCertificate. |
| PFX | Obligatorio. Contenido codificado en base 64 del archivo PFX. |
| contraseña | Obligatorio. Contraseña para tener acceso al archivo PFX. |

### <a name="basic-auth"></a>Autenticación básica

Puede usar la autenticación básica (por ejemplo, nombre de usuario y contraseña) para validar las solicitudes entrantes. Autenticación básica es un patrón comunes y puede hacerlo en cualquier lenguaje creado en la aplicación.

En la sección *autorización* , debe proporcionar: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descripción |
|---------|-------------|
| tipo | Obligatorio. Tipo de autenticación. Para la autenticación básica, el valor debe ser Basic. |
| nombre de usuario | Obligatorio. Nombre de usuario para autenticar. |
| contraseña | Obligatorio. Contraseña para la autenticación. |

### <a name="handle-aad-auth-in-code"></a>Controlador de AAD auth en código

De forma predeterminada, la autenticación de Azure Active Directory habilitar en el Portal no autorización específica. Por ejemplo, no bloquea la API para un usuario específico o una aplicación, pero a un inquilino determinado.

Si desea restringir la API solo la lógica aplicación, por ejemplo, en el código, puede extraer el encabezado que contiene el JWT y comprobar que la persona que llama es, rechazar solicitudes que no coinciden.

Ir más allá, si desea implementar completamente en su propio código y no aprovechar la característica de Portal, puede leer este artículo: [Uso de Active Directory para la autenticación en el servicio de aplicación de Azure](../app-service-web/web-sites-authentication-authorization.md).

Necesitará seguir los pasos anteriores para crear una identidad de aplicación de la aplicación de lógica y usarlo para llamar a la API.
