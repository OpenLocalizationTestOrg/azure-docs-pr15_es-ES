<properties
   pageTitle="Almacenamiento en caché tokens de acceso en una aplicación multiempresa | Microsoft Azure"
   description="Almacenamiento en caché tokens de acceso que se utiliza para llamar a un servidor Web API"
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


# <a name="caching-access-tokens-in-a-multitenant-application"></a>Almacenamiento en caché tokens de access en una aplicación multiempresa

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

Es relativamente caro obtener un acceso OAuth token, ya que requiere una solicitud HTTP al extremo del token. Por lo tanto, es conveniente tokens de caché siempre que sea posible. La [Biblioteca de autenticación de Azure AD] [ ADAL] (ADAL) almacena automáticamente en caché tokens obtenidas de Azure AD, incluidos tokens de actualización.

ADAL proporciona una implementación de caché del símbolo de forma predeterminada. Sin embargo, este token caché está destinada a aplicaciones cliente nativo y _no_ adecuado para las aplicaciones web:

-   Es una instancia estática y no seguros para subprocesos.
-   No ajustar a un gran número de usuarios, ya que vaya tokens de todos los usuarios en el mismo diccionario.
-   No se pueden compartir entre servidores web en una granja de servidores.

En su lugar, debe implementar una caché de token personalizada que se deriva de la ADAL `TokenCache` clase pero es adecuado para un entorno de servidor y proporciona el nivel de aislamiento entre tokens para diferentes usuarios conveniente.

La `TokenCache` clase almacena un diccionario de tokens, indizada por emisor, recursos, el identificador de cliente y usuario. Una caché de token personalizada debe escribir este diccionario en un almacén de respaldo, como una caché Redis.

En la aplicación Tailspin encuestas, la `DistributedTokenCache` clase implementa la caché del símbolo. Esta implementación usa la [IDistributedCache] [ distributed-cache] abstracción de ASP.NET Core 1.0. De este modo, cualquier `IDistributedCache` implementación se puede utilizar como un almacén de respaldo.

-   De forma predeterminada, la aplicación de encuestas utiliza una caché Redis.
-   Para un servidor web de instancia única, puede usar la ASP.NET Core 1.0 [en la memoria caché][in-memory-cache]. (Esta es también una buena opción para ejecutar la aplicación localmente durante el desarrollo.)

> [AZURE.NOTE] Actualmente no se admite la caché Redis para .NET principales.

`DistributedTokenCache`almacena los datos de la memoria caché como pares de clave y valor en el almacén de respaldo. La clave es el identificador de usuario más el ID de cliente, por lo que el almacén de respaldo contiene los datos de caché independiente para cada combinación única de usuario o cliente.

![Caché de tokens](media/guidance-multitenant-identity/token-cache.png)

El almacén de respaldo se divide por el usuario. Para cada solicitud HTTP, los tokens para ese usuario se lea desde el almacén de respaldo y se cargan en el `TokenCache` diccionario. Si Redis se usa como el almacén de respaldo, cada instancia del servidor en una granja de servidores lee y escribe en la caché de la misma, y este enfoque escala a muchos usuarios.

## <a name="encrypting-cached-tokens"></a>Cifrar tokens en caché

Tokens son datos confidenciales, ya que permita el acceso a los recursos de un usuario. (Además, a diferencia de la contraseña de un usuario, no puede simplemente almacenar un hash del token.) Por lo tanto, es fundamental evitar tokens se vean comprometidos. La caché con Redis está protegida por contraseña, pero si alguien obtiene la contraseña, puede obtener todos los identificadores de acceso en caché. Por este motivo, el `DistributedTokenCache` todo lo que escribe en el almacén de respaldo cifra. El cifrado se realiza mediante la [protección de datos] de ASP.NET Core 1.0[ data-protection] API.

> [AZURE.NOTE] Si implementa a sitios Web de Azure, las claves de cifrado se realizan copias de seguridad con el almacenamiento de red y sincronizadas entre todos los equipos (vea [Administración de claves][key-management]). De forma predeterminada, las claves no se cifran cuando se ejecuta en los sitios Web de Azure, pero se puede [Habilitar el cifrado mediante un certificado X.509][x509-cert-encryption].


## <a name="distributedtokencache-implementation"></a>Implementación de DistributedTokenCache

La [DistributedTokenCache] [ DistributedTokenCache] clase deriva de la ADAL [TokenCache] [ tokencache-class] clase.

En el constructor, el `DistributedTokenCache` clase crea una clave para el usuario actual y carga de la caché del almacén de copia de seguridad:

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

La clave se crea concatenando el identificador de usuario y el identificador de cliente Ambas proceden de reclamaciones que se encuentra en el usuario `ClaimsPrincipal`:

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

Para cargar los datos de caché, lea el número de serie blob desde el almacén de respaldo y llamada `TokenCache.Deserialize` para convertir el blob en caché los datos.

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

Siempre que ADAL obtener acceso a la memoria caché, se aplica un `AfterAccess` evento. Si ha cambiado los datos de la caché, el `HasStateChanged` propiedad es true. En ese caso, actualizar el almacén de respaldo para reflejar el cambio y, a continuación, establezca `HasStateChanged` en false.

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache envía dos otros eventos:

- `BeforeWrite`. Llama inmediatamente antes de que escribe ADAL en la caché. Puede usar para implementar una estrategia de simultaneidad
- `BeforeAccess`. Llama inmediatamente antes de ADAL lee de la memoria caché. Aquí puede volver a cargar la memoria caché para obtener la última versión.

En este caso, hemos decidido no controla estos dos eventos.

- Para simultaneidad, escriba última wins. Aceptar, que es porque tokens se almacenan por separado para cada usuario + cliente, por lo que un conflicto sólo ocurre si el mismo usuario tenía dos sesiones simultáneas de inicio de sesión.
- Para leer, se cargue la caché en cada solicitud. Las solicitudes se vida corta. Si se modifica la caché en ese momento, la siguiente solicitud seleccionará el nuevo valor.

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [Federating con AD FS de un cliente para aplicaciones multiempresa en Azure][adfs]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[adfs]: guidance-multitenant-identity-adfs.md
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[parte de una serie]: guidance-multitenant-identity.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
