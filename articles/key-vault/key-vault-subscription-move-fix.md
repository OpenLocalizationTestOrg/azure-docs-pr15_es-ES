<properties
    pageTitle="Cambiar el identificador del inquilino depósito clave después de mover una suscripción | Microsoft Azure"
    description="Obtenga información sobre cómo cambiar el identificador de inquilinos para un depósito clave después de mover una suscripción a un inquilino diferente"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Cambiar un identificador de inquilinos depósito clave después de mover una suscripción
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>P: mi suscripción se ha movido desde inquilino A inquilinos B. ¿Cómo cambiar el identificador de inquilinos para mi cámara clave existente y establecer ACL correctas para principales en inquilino B?

Cuando se crea un nuevo depósito clave en una suscripción, se asocian automáticamente para el identificador del inquilino de Azure Active Directory predeterminada de la suscripción. Todas las entradas de la directiva de acceso también se relacionan con este ID de inquilinos. Cuando se mueve la suscripción Azure de inquilino A inquilino B, los depósitos claves existentes están accesibles las identidades (usuarios y aplicaciones) en el inquilino B. Para solucionar este problema, necesita:

- Cambiar el identificador del inquilino asociado a todos los existentes claves depósitos en esta suscripción a inquilino B.
- Quitar todas las entradas de directiva de acceso existentes.
- Agregar nuevas entradas de directiva de acceso que están asociadas a inquilinos B.

Por ejemplo, si tiene depósito clave 'myvault' en una suscripción que se ha movido del inquilino A B, aquí del inquilino cómo cambiar el ID de inquilino de este depósito clave y quitar antiguo directivas de acceso.

<pre>
$vaultResourceId = (AzureRmKeyVault get - VaultName myvault). ResourceId $vault = AzureRmResource Get-ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() AzureRmResource Set - ResourceId $vaultResourceId-propiedades $vault. Propiedades
</pre>

Debido a este depósito inquilino A antes del desplazamiento, el valor original de **$vault. Properties.TenantId** es A inquilinos, mientras **(Get-AzureRmContext). Tenant.TenantId** se inquilinos B.

Ahora que la cámara está asociado con el ID de inquilinos correcto y se quitan las entradas de directiva de acceso antiguos, establecer nuevo acceso [AzureRmKeyVaultAccessPolicy del conjunto](https://msdn.microsoft.com/library/mt603625.aspx)de las entradas de la directiva.

## <a name="next-steps"></a>Pasos siguientes

Si tiene preguntas sobre depósito de Azure clave, visite los [Foros de depósito de clave de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
