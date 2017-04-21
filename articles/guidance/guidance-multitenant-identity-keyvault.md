<properties
   pageTitle="Usar depósito de clave para proteger la información confidencial de la aplicación | Microsoft Azure"
   description="Cómo utilizar el servicio de depósito de clave para almacenar información confidencial de la aplicación"
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
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# <a name="using-azure-key-vault-to-protect-application-secrets"></a>Usar Azure clave depósito para proteger la información confidencial de la aplicación

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

## <a name="overview"></a>Información general

Es común para que la configuración de la aplicación que es confidenciales y debe estar protegido, como por ejemplo:

- Cadenas de conexión de base de datos
- Contraseñas
- Claves de cifrado

Como práctica recomendada de seguridad, nunca se debe almacenar estos información confidencial en control de código fuente. Es muy fácil perder &mdash; aunque el repositorio de código fuente es privado. Y no se prácticamente mantener información confidencial del general público. En proyectos grandes, es posible que desee restringir que los programadores y operadores pueden tener acceso a la información confidencial de producción. (Configuración para entornos de desarrollo o prueba es diferentes).

Una opción más segura es almacenar estos información confidencial en [Depósito de clave de Azure][KeyVault]. Depósito de clave es un servicio hospedada en la nube para administrar las claves de cifrado y otra información confidencial. Este artículo le muestra cómo usar clave depósito para almacenar los valores de configuración de aplicación.

En las [Encuestas Tailspin] [ Surveys] aplicación, la siguiente configuración estará secreta:

- La cadena de conexión de base de datos.
- La cadena de conexión Redis.
- El secreto de cliente para la aplicación web.

Para almacenar información confidencial de configuración en depósito de clave, la aplicación de encuestas implementa un proveedor de configuración personalizada, que se conecta al [sistema de configuración]de ASP.NET Core 1.0[configuration]. El proveedor personalizado lee la configuración de la cámara de clave en el inicio.

Opciones de configuración de carga de la aplicación de encuestas desde las siguientes ubicaciones:

- El archivo appsettings.json
- La [información confidencial de usuario almacena] [ user-secrets] (entorno de desarrollo; para realizar pruebas)
- El entorno de hospedaje (configuración de la aplicación en las aplicaciones web de Azure)
- Depósito clave

Cada uno de estos reemplaza lo anterior, para cualquier configuración almacenados en depósito de clave tiene prioridad.

> [AZURE.NOTE] De forma predeterminada, el proveedor de configuración de cámara clave está deshabilitado. No es necesario para ejecutar la aplicación localmente. ¿Habilitarlo en una implementación de producción.

> El proveedor de clave depósito no es compatible actualmente para .NET principales, ya que requiere la [Microsoft.Azure.KeyVault] [ Microsoft.Azure.KeyVault] paquete.

En el inicio, la aplicación lee configuración de cada proveedor de configuración registrados y utiliza para rellenar un objeto opciones inflexible. (Para obtener más información, consulte [uso de las opciones y objetos de configuración de][options].)

## <a name="implementation"></a>Implementación

La [KeyVaultConfigurationProvider] [ KeyVaultConfigurationProvider] clase es un proveedor de configuración que se conecta al [sistema de configuración]de ASP.NET Core 1.0[configuration].

Usar el `KeyVaultConfigurationProvider`, llame a la `AddKeyVaultSecrets` método de extensión en la clase de inicio:

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Observe que `KeyVaultConfigurationProvider` requiere algunas opciones de configuración que deben estar almacenados en uno de los otros orígenes de configuración.

Cuando se inicie la aplicación, `KeyVaultConfigurationProvider` enumera toda la información confidencial en depósito de clave. Para cada secreto busca una etiqueta denominada 'ConfigKey'. El valor de la etiqueta es el nombre de la configuración.

> [AZURE.NOTE] [Etiquetas] [ key-tags] están almacenados con una clave de metadatos opcionales. Las etiquetas se usan aquí porque los nombres de clave no pueden contener caracteres de dos puntos (:).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Vea [KeyVaultConfigurationProvider.cs].

## <a name="setting-up-key-vault-in-the-surveys-app"></a>Configuración de cámara de clave en la aplicación de encuestas

Requisitos previos:

- Instalar los [Cmdlets del Administrador de recursos Azure][azure-rm-cmdlets].
- Configurar la aplicación de encuestas, como se describe en [ejecución de la aplicación de encuestas][readme].

Pasos de alto nivel:

1. Configurar el usuario administrador en el inquilino.
2. Configurar un certificado de cliente.
3. Crear un depósito clave.
4. Agregar opciones de configuración para su cámara clave.
5. Quite el código que permite depósito clave.
6. Actualizar la información confidencial de usuario de la aplicación.

### <a name="set-up-an-admin-user"></a>Configurar el usuario administrador

> [AZURE.NOTE] Para crear un depósito clave, debe utilizar una cuenta que puede administrar su suscripción de Azure. Además, cualquier aplicación que autorizar leer de la cámara clave debe registrado en el mismo inquilino como esa cuenta.

En este paso, asegurará que puede crear un depósito clave mientras ha iniciado sesión como un usuario desde el inquilino dónde está registrada la aplicación de encuestas.

En primer lugar, cambie el directorio asociado a su suscripción de Azure.

1. Inicie sesión en el [portal de administración de Azure][azure-management-portal]

2. Haga clic en **configuración**.

    ![Configuración](media/guidance-multitenant-identity/settings.png)

3. Seleccione la suscripción de Azure.

4. Haga clic en **Editar directorio** en la parte inferior del portal.

    ![Configuración](media/guidance-multitenant-identity/edit-directory.png)

5. En "Cambiar el directorio asociado", seleccione el inquilino de Azure AD donde se registra la aplicación de encuestas,

    ![Configuración](media/guidance-multitenant-identity/edit-directory2.png)

6. Haga clic en el botón de flecha y complete el cuadro de diálogo.

Crear un usuario de administración con el inquilino de Azure AD dónde está registrada la aplicación de encuestas.

1. Inicie sesión en el [portal de administración de Azure][azure-management-portal].

2. Seleccione al inquilino de Azure AD dónde está registrada su aplicación.

3. Haga clic en **usuarios** > **Agregar usuario**.

4. En el cuadro de diálogo **Agregar usuario** , asignar al usuario a la función de administrador Global.

Agregar al usuario de administración como administrador de colaboración para la suscripción de Azure.

1. Inicie sesión en el [portal de administración de Azure][azure-management-portal].

2. Haga clic en **configuración** y seleccione la suscripción de Azure.

3. Haga clic en **administradores**

4. Haga clic en **Agregar** en la parte inferior del portal.

5. Escriba el correo electrónico del usuario al administrador que creó anteriormente.

6. Active la casilla de verificación de la suscripción.

7. Haga clic en el botón de marca de verificación para completar el cuadro de diálogo.

![Agregar un administrador de compañeros](media/guidance-multitenant-identity/co-admin.png)


### <a name="set-up-a-client-certificate"></a>Configurar un certificado de cliente

1. Ejecutar la secuencia de comandos de PowerShell [/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] como sigue:
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
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

3. Agregar el mismo fragmento JSON a la información de la aplicación de la web API (Surveys.WebAPI).

4. Ejecute el comando siguiente para obtener la huella digital del certificado.
    ```
    certutil -store -user my [subject]
    ```
    donde `[subject]` es el valor especificado para el asunto en la secuencia de comandos de PowerShell. Aparece la huella digital en "certificado"hash (SHA1). Quitar los espacios entre los números en hexadecimal.

Use la huella digital más adelante.

### <a name="create-a-key-vault"></a>Crear un depósito clave

1. Ejecutar la secuencia de comandos de PowerShell [/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] como sigue:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Cuando se le solicita las credenciales, inicie sesión en como el usuario de Azure AD que creó anteriormente. La secuencia de comandos crea un nuevo grupo de recursos y un nuevo depósito clave dentro de ese grupo de recursos.

    Nota: para-parámetro de ubicación, puede usar el siguiente comando PowerShell para obtener una lista de regiones válidas:

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Ejecute SetupKeyVault.ps de nuevo, con los parámetros siguientes:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    donde

    - nombre de clave depósito = el nombre que le asignó el depósito clave en el paso anterior.
    - Identificador de cliente de la aplicación Web de = el identificador de cliente para la aplicación web de encuestas.
    - Identificador de cliente de la api de Web = el identificador de cliente de la aplicación Surveys.WebAPI.

    Ejemplo:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Puede obtener el cliente de identificadores desde el [portal de administración de Azure][azure-management-portal]. Seleccione al inquilino de Azure AD, seleccione la aplicación y haga clic en **Configurar**.

    Esta secuencia de comandos autoriza la aplicación web y la API de web para recuperar información confidencial de la cámara clave. Consulte [Introducción a Azure clave depósito] [ authorize-app] para obtener más información.

### <a name="add-configuration-settings-to-your-key-vault"></a>Agregar opciones de configuración para su cámara clave

1. Ejecutar SetupKeyVault.ps como sigue:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    donde

    - nombre de clave depósito = el nombre que le asignó el depósito clave en el paso anterior.
    - Redis nombre DNS = nombre de DNS de su instancia de caché Redis.
    - Tecla de acceso de Redis = la tecla de acceso de la instancia de caché Redis.

    Este comando agrega un secreto a su cámara clave. El secreto es un par de nombre y valor más de una etiqueta:

    -   El nombre de clave no se utiliza la aplicación, pero debe ser único dentro de la cámara de clave.
    -   El valor es el valor de la opción de configuración, en este caso, la cadena de conexión Redis.
    -   la etiqueta "ConfigKey" contiene el nombre de la clave de configuración.

2. En este momento, es una buena idea comprobar si correctamente almacenan la información confidencial en depósito de clave. Ejecute el siguiente comando PowerShell:

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    El resultado debe mostrar el valor secreto más algunos metadatos:

    ![Resultados de PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Ejecute SetupKeyVault.ps nuevo para agregar la cadena de conexión de base de datos:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    donde `<<DB connection string>>` es el valor de la cadena de conexión de base de datos.

    Para realizar pruebas con la base de datos local, copie la cadena de conexión del archivo Tailspin.Surveys.Web/appsettings.json. Si lo hace, asegúrese de cambiar la doble barra diagonal inversa ('\\\\') en una barra invertida. La doble barra diagonal inversa es un carácter de escape en el archivo JSON.

    Ejemplo:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### <a name="uncomment-the-code-that-enables-key-vault"></a>Quite el código que permite depósito de clave

1. Abra la solución Tailspin.Surveys.

2. En [Tailspin.Surveys.Web/Startup.cs][web-startup], busque el siguiente bloque de código y quitar los comentarios.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. En [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup], busque el siguiente bloque de código y quitar los comentarios.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. En [Tailspin.Surveys.Web/Startup.cs][web-startup], busque el código que registra la `ICredentialService`. Elimine la línea que usa `CertificateCredentialService`y un comentario la línea que usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Este cambio permite a la aplicación web usar [aserción cliente] [ client-assertion] para obtener el token de acceso OAuth. Con aserción de cliente, no es necesario un secreto de cliente de OAuth. Como alternativa, puede almacenar el secreto de cliente en depósito de clave. Sin embargo, depósito clave y aserción cliente que ambos utilizan a un cliente de certificados, por lo que si habilita depósito clave, es una buena práctica para habilitar también aserción de cliente.

### <a name="update-the-user-secrets"></a>Actualizar la información confidencial de usuario

En el Explorador de soluciones, haga clic en el proyecto Tailspin.Surveys.Web y seleccione **Administrar información confidencial de usuario**. En el archivo secrets.json, eliminar el JSON existente y pegue la información siguiente:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Reemplazar las entradas de corchetes [] con los valores correctos.

- `AzureAd:ClientId`: El identificador de cliente de la aplicación de encuestas.
- `AzureAd:WebApiResourceId`: El identificador de aplicación URI que especificó al crear la aplicación Surveys.WebAPI en Azure AD.
- `Asymmetric:CertificateThumbprint`: La huella digital del certificado que obtuvo anteriormente, cuando creó el certificado de cliente.
- `KeyVault:Name`: El nombre de la cámara clave.

> [AZURE.NOTE] `Asymmetric:ValidationRequired`es false porque el certificado que ha creado previamente no firmado por una entidad de certificación (CA) de raíz. En producción, use un certificado firmado por una entidad emisora de certificados raíz y `ValidationRequired` en true.

Guarde el archivo actualizado secrets.json.

A continuación, en el Explorador de soluciones, haga clic en el proyecto Tailspin.Surveys.WebApi y seleccione **Administrar información confidencial de usuario**. Eliminar el JSON existente y pegue la información siguiente:

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Reemplazar las entradas de corchetes [] y guarde el archivo secrets.json.

> [AZURE.NOTE] Para la API de web, asegúrese de usar el identificador de cliente de la aplicación Surveys.WebAPI, no a la aplicación de encuestas.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[parte de una serie]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
