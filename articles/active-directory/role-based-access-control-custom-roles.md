<properties
    pageTitle="Funciones personalizadas en Azure RBAC | Microsoft Azure"
    description="Obtenga información sobre cómo definir funciones personalizadas con Control de acceso de Azure Role-Based para la administración de identidades más precisa en la suscripción de Azure."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="kgremban"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/25/2016"
    ms.author="kgremban"/>


# <a name="custom-roles-in-azure-rbac"></a>Funciones personalizadas en Azure RBAC


Crear una función personalizada en el Control de acceso de Azure Role-Based (RBAC) si ninguna de las funciones integradas satisface sus necesidades de acceso específico. Funciones personalizadas pueden crearse con [PowerShell de Azure](role-based-access-control-manage-access-powershell.md), [interfaz de línea de comandos de Azure](role-based-access-control-manage-access-azure-cli.md) y la [API de REST](role-based-access-control-manage-access-rest.md). Funciones integradas, como funciones personalizadas pueden asignarse a usuarios, grupos y aplicaciones en la suscripción, el grupo de recursos y ámbitos de recursos. Funciones personalizadas se almacenan en un inquilino de Azure AD y se pueden compartir entre todas las suscripciones que usen ese inquilino como el directorio de Azure AD para la suscripción.

A continuación se muestra un ejemplo de una función personalizada para la supervisión y reiniciar máquinas virtuales de Windows:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Acciones
La propiedad de **acciones** de una función personalizada especifica las operaciones de Azure al que la función concede acceso. Es una colección de cadenas de operación que identifiquen a operaciones de proveedores de recursos de Azure. Cadenas de operación que contienen caracteres comodín (\*) conceder acceso a todas las operaciones que coinciden con la cadena de operación. Por ejemplo:

-   `*/read`concede acceso Leer operaciones para todos los tipos de recursos de todos los proveedores de recursos de Azure.
-   `Microsoft.Network/*/read`concede acceso Leer operaciones para todos los tipos de recursos en el proveedor de recursos de Microsoft.Network de Azure.
-   `Microsoft.Compute/virtualMachines/*`concede acceso a todas las operaciones de máquinas virtuales y su elemento secundario tipos de recursos.
-   `Microsoft.Web/sites/restart/Action`concede acceso a sitios Web de reiniciar.

Usar `Get-AzureRmProviderOperation` (en PowerShell) o `azure provider operations show` (en Azure CLI) para operaciones de la lista de proveedores de recursos de Azure. También puede usar estos comandos para comprobar que una cadena de operación es válida y para expandir las cadenas de la operación de caracteres comodín.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Captura de pantalla de PowerShell: Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | Operación de FT, OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Presentación de Azure CLI captura de pantalla - azure proveedor operaciones "Microsoft.Compute/virtualMachines/\*/Action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Use la propiedad **NotActions** si el conjunto de operaciones que se desean permitir que se define más fácilmente, excluyendo operaciones restringidas. El acceso de una función personalizada se calcula restando las operaciones **NotActions** de las operaciones de **acciones** .

> [AZURE.NOTE] Si un usuario tiene asignado una función que excluya una operación en **NotActions**y a continuación, se asigna una segunda función que conceda acceso a la misma operación, se permitirá al usuario para realizar esta operación. **NotActions** no es una regla de denegar: es simplemente una forma adecuada para crear un conjunto de operaciones permitidas cuando es necesario excluir determinadas operaciones.

## <a name="assignablescopes"></a>AssignableScopes
La propiedad **AssignableScopes** de la función personalizada especifica los ámbitos (suscripciones, grupos de recursos o recursos) en el que está disponible para asignación de funciones personalizado. Puede disponer de la función personalizada de asignación en solo las suscripciones o grupos de recursos que lo requieren y no de otros correos experiencia del usuario para el resto de las suscripciones o los grupos de recursos.

Ejemplos de ámbitos puede asignables válidos:

-   "/ suscripciones/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ suscripciones/e91d47c4-76f3-4271-a796-21b4ecfe3624" - hace que la función disponibles para la asignación de dos suscripciones.
-   "/ suscripciones/c276fc76-9cd4-44c9-99a7-4fd71546436e" - hace que la función disponibles para la asignación de una sola suscripción.
-  "/ suscripciones/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups o red" - disponga de la función de asignación solo en el grupo de recursos de red.

> [AZURE.NOTE] Debe usar al menos una suscripción, grupo de recursos o identificador de recurso.

## <a name="custom-roles-access-control"></a>Control de acceso a funciones personalizadas
La propiedad **AssignableScopes** de la función personalizada también controla quién puede ver, modificar y eliminar la función.

- ¿Quién puede crear un rol personalizado?
    Los propietarios (y administradores de acceso de usuario) de suscripciones, grupos de recursos y recursos pueden crear funciones personalizadas para su uso en estos ámbitos.
    La creación de la función de usuario debe llevar a cabo `Microsoft.Authorization/roleDefinition/write` operación en el **AssignableScopes** de la función.

- ¿Quién puede modificar un rol personalizado?
    Los propietarios (y administradores de acceso de usuario) de suscripciones, grupos de recursos y recursos pueden modificar los roles personalizados en estos ámbitos. Los usuarios tendrán que llevar a cabo la `Microsoft.Authorization/roleDefinition/write` operación en el **AssignableScopes** de una función personalizada.

- ¿Quién puede ver funciones personalizadas?
    Todas las funciones integradas en Azure RBAC permiten la visualización de las funciones que están disponibles para la asignación. Usuarios que pueden realizar la `Microsoft.Authorization/roleDefinition/read` operación en un ámbito puede ver las funciones RBAC que están disponibles para la asignación en ese ámbito.

## <a name="see-also"></a>Vea también
- [Control de acceso en función de roles](role-based-access-control-configure.md): Introducción a RBAC en el portal de Azure.
- Obtenga información sobre cómo administrar el acceso con:
    - [PowerShell](role-based-access-control-manage-access-powershell.md)
    - [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
    - [API DE REST](role-based-access-control-manage-access-rest.md)
- [Funciones integradas](role-based-access-built-in-roles.md): obtener detalles acerca de las funciones que vienen en RBAC.
