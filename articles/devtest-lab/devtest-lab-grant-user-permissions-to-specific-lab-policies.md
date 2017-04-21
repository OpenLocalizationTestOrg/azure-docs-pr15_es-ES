<properties
    pageTitle="Conceder permisos de usuario a las directivas de laboratorio concreta | Microsoft Azure"
    description="Obtenga información sobre cómo conceder permisos de usuario a las directivas de laboratorio específico en prácticas DevTest según las necesidades de cada usuario"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permisos de usuario a las directivas de laboratorio específico

## <a name="overview"></a>Información general

En este artículo muestra cómo usar PowerShell para conceder permisos de usuario a una directiva de laboratorio determinado. De este modo, pueden aplicar permisos en función de las necesidades de cada usuario. Por ejemplo, que desea conceder a un usuario concreto la capacidad de cambiar la configuración de la directiva de máquina virtual, pero no a las directivas de costo.

## <a name="policies-as-resources"></a>Directivas de recursos

Como se describe en el artículo de [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md) , RBAC permite la administración de acceso específico de recursos para Azure. RBAC puede separar obligaciones dentro de su equipo DevOps y conceder solo la cantidad de acceso a los usuarios que necesitan para realizar su trabajo.

En prácticas de DevTest, una directiva es un tipo de recurso que permite la acción RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada directiva de práctica es un recurso en el tipo de recurso de directiva y puede asignarse como ámbito a un rol de RBAC.

Por ejemplo, para conceder usuarios lectura y escritura permiso para la directiva de **Tamaños de VM permitidos** , podría crear una función personalizada que funciona con la **Microsoft.DevTestLab/labs/policySets/policies/** * acción y luego asignar los usuarios adecuados a este rol personalizado en el ámbito de * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para obtener más información sobre funciones personalizadas en RBAC, vea el [control de acceso de funciones personalizadas](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Crear un rol personalizado laboratorio con PowerShell
Para empezar, debe leer el artículo siguiente, que explica cómo instalar y configurar los cmdlets de PowerShell de Azure: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Una vez que haya configurado los cmdlets de PowerShell de Azure, puede realizar las siguientes tareas:

- Lista de todas las operaciones/acciones para un proveedor de recursos
- Acciones de lista en una función en particular:
- Crear una función personalizada

El siguiente script de PowerShell muestra ejemplos de cómo realizar estas tareas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Asignar permisos a un usuario para una directiva específica con funciones personalizadas
Una vez que haya definido sus funciones personalizadas, puede asignarlos a los usuarios. Para asignar un rol personalizado a un usuario, primero debe obtener el **Id. de objeto** que representa ese usuario. Para ello, use el cmdlet **Get-AzureRmADUser** .

En el ejemplo siguiente, el **Id. de objeto** del usuario *SomeUser* es 05DEFF7B 0AC3 4ABF B74D 6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Una vez que tenga el **Id. de objeto** para el usuario y un nombre de función personalizado, puede asignar ese rol al usuario con el cmdlet **AzureRmRoleAssignment nuevo** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

En el ejemplo anterior, se utiliza la directiva de **AllowedVmSizesInLab** . Puede utilizar cualquiera de las siguientes directivas:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez haya concedido permisos de usuario a las directivas de laboratorio específico, estos son algunos pasos siguientes para tener en cuenta:

- [Proteger el acceso a un laboratorio](devtest-lab-add-devtest-user.md).

- [Establecer directivas de práctica](devtest-lab-set-lab-policy.md).

- [Crear una plantilla de laboratorio](devtest-lab-create-template.md).

- [Crear artefactos personalizados para sus máquinas virtuales](devtest-lab-artifact-author.md).

- [Agregar una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md).
