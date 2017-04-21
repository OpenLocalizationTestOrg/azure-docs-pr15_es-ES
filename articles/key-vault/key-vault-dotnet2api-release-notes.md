<properties
   pageTitle="Notas de la versión de depósito .NET 2.x API de claves | Microsoft Azure"
   description="Los desarrolladores de .NET usar esta API para código para depósito de clave de Azure"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Depósito clave Azure .NET 2.0 - Guía de migración y notas

Las notas y las instrucciones siguientes son para los desarrolladores que trabajan con Azure clave depósito .NET / biblioteca C#. El cambio de la 1.0 versión a la versión 2.0, un número de actualizaciones se han realizado que le requieren trabajo de migración en el código para beneficiarse de las mejoras funcionales y las adiciones como soporte certificados de depósito de clave de características.

Proporciona soporte técnico de certificados de depósito de clave para la administración de su x509 certificados y los comportamientos siguientes:  

-   Permite que el propietario de un certificado crear un certificado a través de un proceso de creación de clave depósito o la importación de un certificado existente. Esto incluye ambos autofirmado y entidad emisora de certificados genera certificados.

- Permite el propietario de un certificado de depósito clave implementar el almacenamiento seguro y la administración de X509 certificados sin interacción con material de clave privada.  

-   Permite que el propietario de un certificado crear una directiva que dirige depósito clave para administrar el ciclo de vida de un certificado.  

-   Permite a los propietarios de certificado proporcionar información de contacto de notificación sobre los eventos de ciclo de vida de caducidad y renovación de certificado.  

-   Es compatible con la renovación automática con emisores seleccionados - partner clave depósito X509 proveedores de certificados y entidades emisoras de certificados.
    - Nota: proveedores/autoridades no asociado también se permiten pero no se admite la característica de renovación automática.


## <a name="net-support"></a>Compatibilidad con .NET
- **.NET 4.0** no es compatible con la versión 2.0 de .NET Azure clave depósito / biblioteca C#
- **Núcleo de .NET** es compatible con la versión 2.0 de .NET Azure clave depósito / biblioteca C#

## <a name="namespaces"></a>Espacios de nombres
- El espacio de nombres para los **modelos** se cambia de **Microsoft.Azure.KeyVault** a **Microsoft.Azure.KeyVault.Models**.
- Se quita el espacio de nombres **Microsoft.Azure.KeyVault.Internal** .
- El espacio de nombres de las dependencias de Azure SDK cambian de **Hyak.Common** y **Hyak.Common.Internals** a **Microsoft.Rest** y **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Cambios de tipo
- *Secreto* cambia a *SecretBundle*
- Cambiar el *diccionario* a *IDictionary*
- *Lista<T>, string []* cambia a *IList<T> *
- *NextList* cambia a *NextPageLink*


## <a name="return-types"></a>Tipos de valor devuelto
- **Lista de claves** y **SecretList** devolverá *IPage<T> * en lugar de *ListKeysResponseMessage*
- La generada **BackupKeyAsync** devolverá *BackupKeyResult* que contiene el *valor* (blob de copia de seguridad). Antes de que se ajuste al método y devolver solo el valor.

## <a name="exceptions"></a>Excepciones
- *KeyVaultClientException* cambia a *KeyVaultErrorException*
- Error de servicio se cambia de excepción *. Error* a excepción de *. Body.Error.Message*.
- Elimina información adicional desde el mensaje de error de **[JsonExtensionData]**.

## <a name="constructors"></a>Constructores
- En lugar de aceptar un *HttpClient* como un argumento de constructor, el constructor solo acepta *HttpClientHandler* o *[] DelegatingHandler*.



## <a name="downloaded-packages"></a>Paquetes descargados  
Cuando un cliente procesa una dependencia en depósito de clave siguiente se descargaron
#### <a name="previous-package-list"></a>Lista de paquete anterior
- versión del paquete id="Hyak.Common" = "1.0.2" targetFramework = "net45"
- versión del paquete id="Microsoft.Azure.Common" = "2.0.4" targetFramework = "net45"
- versión del paquete id="Microsoft.Azure.Common.Dependencies" = "1.0.0" targetFramework = "net45"
- versión del paquete id="Microsoft.Azure.KeyVault" = "1.0.0" targetFramework = "net45"
- versión del paquete id="Microsoft.Bcl" = "1.1.9" targetFramework = "net45"
- versión del paquete id="Microsoft.Bcl.Async" = "1.0.168" targetFramework = "net45"
- versión del paquete id="Microsoft.Bcl.Build" = "1.0.14" targetFramework = "net45"
- versión del paquete id="Microsoft.Net.Http" = "2.2.22" targetFramework = "net45"

#### <a name="current-package-list"></a>Lista de paquete actual
- versión del paquete id="Microsoft.Azure.KeyVault" = "2.0.0-preview" targetFramework = "net45"
- versión del paquete id="Microsoft.Rest.ClientRuntime" = "2.2.0" targetFramework = "net45"
- versión del paquete id="Microsoft.Rest.ClientRuntime.Azure" = "3.2.0" targetFramework = "net45"


## <a name="class-changes"></a>Cambios de clase

- Se ha quitado la clase **UnixEpoch**
- Se cambia el nombre de clase de **Base64UrlConverter** a **Base64UrlJsonConverter**

## <a name="other-changes"></a>Otros cambios

- Soporte técnico para la configuración de directiva de intentos de operación KV en errores transitorias se ha agregado a esta versión de la API.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- De las operaciones que devuelven un *depósito*, el tipo de valor devuelto es una clase que contenía una propiedad de cámara. El tipo devuelto es ahora *depósito*.
- *PermissionsToKeys* y *PermissionsToSecrets* ahora son *Permissions.Keys* y *Permissions.Secrets*
- Algunos de los cambios de tipos de valor devuelto se aplican al control-plano también.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- El paquete se divide **Microsoft.Azure.KeyVault.Extensions** y **Microsoft.Azure.KeyVault.Cryptography** para las operaciones de cifrado.
