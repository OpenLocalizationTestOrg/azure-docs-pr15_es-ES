<properties
    pageTitle="Introducción a Azure pila clave depósito | Microsoft Azure"
    description="Introducción al uso de la cámara de clave de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Introducción a depósito de clave

Esta sección describe los pasos para crear un depósito, administrar claves e información confidencial, así como para autorizar usuarios o aplicaciones invocar operaciones de la cámara en pila de Azure. Los siguientes pasos se suponen existe una suscripción de inquilino y servicio de KeyVault está registrado en la suscripción. Todos los comandos de ejemplo se basan en los cmdlets KeyVault disponibles como parte del SDK de PowerShell de Azure.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>Habilitar la suscripción de inquilinos para operaciones de cámara 

Para que pueda emitir operaciones con cualquier cámara, debe asegurarse de que la suscripción está habilitada para operaciones de cámara. Para confirmar emita el siguiente comando PowerShell:

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

El resultado del comando anterior debe informar "Registrado" para el estado "Registro" de cada fila.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Si no es así, debe llamar al comando siguiente para registrar el servicio de KeyVault dentro de su suscripción:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

Y el siguiente es el resultado del comando:
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Si obtiene el error: "*la suscripción no está registrada con Azure clave depósito*" al invocar KeyVault cmdlets, confirme que ha habilitado el proveedor de recursos de KeyVault por instrucciones anteriores.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Crear un contenedor consolidado (un depósito) en la pila de Azure para almacenar y administrar la información confidencial y claves de cifrado

Para crear un depósito, un inquilino debe crear un grupo de recursos. Los siguientes comandos de PowerShell crean un grupo de recursos y, a continuación, un depósito en ese grupo de recursos. En el ejemplo también incluye la salida típica de ese cmdlet.

### <a name="creating-a-resource-group"></a>Crear un grupo de recursos:

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Resultado:

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Crear un depósito:


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Resultado:

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
El resultado de este cmdlet muestra propiedades del depósito clave que acaba de crear. Las dos propiedades más importantes son:

-   **Nombre de depósito**: en el ejemplo, esta es **vault010**. Use este nombre para otros cmdlets depósito de clave.

-   **URI del depósito**: en el ejemplo, esta es https://vault010.vault.azurestack.local. Las aplicaciones que utilizan la cámara a través de su API de REST deben utilizar este URI.

Su cuenta de Azure ahora está autorizado para realizar operaciones en este depósito clave. Todavía, nadie más está.


## <a name="operating-on-keys-and-secrets"></a>Funcionamiento de claves y la información confidencial

Después de crear un depósito, siga los pasos siguientes para crear, administrar las claves y la información confidencial:

### <a name="creating-a-key"></a>Crear una clave

Para crear una tecla, use el **Complemento AzureKeyVaultKey** por el ejemplo siguiente. Tras creación satisfactoria de clave, el cmdlet mostrará los detalles recién creados.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

A continuación se muestra el resultado del cmdlet *AzureKeyVaultKey agregar* :

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
Ahora puede hacer referencia a esta clave que creó o cargado en depósito de clave de Azure, utilizando su URI. Utilice **las teclas/https://vault010.vault.azurestack.local:443/keyVaultKeyName001** para obtener siempre la versión actual; y use **https://vault010.vault.azurestack.local:443/teclas/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** para obtener esta versión específica.

### <a name="retrieving-a-key"></a>Recuperar una clave

Use la **AzureKeyVaultKey Get** para recuperar una clave y sus detalles por el ejemplo siguiente:

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

A continuación se muestra el resultado de Get-AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Establecer un secreto

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Salida

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Recuperar un secreto

    Get-AzureKeyVaultSecret -VaultName vault010

Salida

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

Ahora, su cámara clave y clave o secreto está listo para usar las aplicaciones.
Debe autorizar aplicaciones usarlas.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar la aplicación para utilizar la clave o el secreto 

Para autorizar la aplicación para tener acceso a la clave o el secreto de la caja fuerte, use el cmdlet Set -**AzureRmKeyVaultAccessPolicy** .

Por ejemplo, si el nombre de la cámara es *ContosoKeyVault* y la aplicación que desea autorizar tiene un *Identificador de cliente* de *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*y desea autorizar la aplicación para descifrar e inicie sesión con las teclas en la caja fuerte, ejecute lo siguiente:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si desea autorizar esa misma aplicación leer información confidencial en su cámara, ejecute lo siguiente:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Pasos siguientes
[Implementar una máquina virtual con una contraseña de la cámara de clave](azure-stack-kv-deploy-vm-with-secret.md)

[Implementar una máquina virtual con un certificado de depósito de clave](azure-stack-kv-push-secret-into-vm.md)