<properties
    pageTitle="Administración de Control de acceso basado en roles con la API de REST"
    description="Administración de control de acceso basado en roles con la API de REST"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>Administración de Control de acceso basado en roles con la API de REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
- [API DE REST](role-based-access-control-manage-access-rest.md)

Control de acceso basado en roles (RBAC) en el Portal de Azure y las API de administrador de recursos de Azure le ayuda a administrar el acceso a su suscripción y recursos en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o identidades de servicio de Active Directory asignándoles algunas de las funciones en un ámbito particular.

## <a name="list-all-role-assignments"></a>Lista de todas las asignaciones de roles

Enumera todas las asignaciones de roles del ámbito especificado y subscopes.

Para asignaciones de roles de la lista, debe tener acceso a `Microsoft.Authorization/roleAssignments/read` operación en el ámbito. Todas las funciones integradas se le ha concedido acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Utilice el método **GET** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito que desee para las asignaciones de roles de la lista. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{versión api}* por 2015-07-01.

3. Reemplazar el *{filtro de}* a la condición que desea aplicar para filtrar la lista de asignación de roles:

  - Lista de las asignaciones de roles para solo el ámbito especificado, sin incluir las asignaciones de roles en subscopes:`atScope()`    
  - Las asignaciones de roles para un usuario específico, un grupo o una aplicación de la lista:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - Asignaciones de roles para un usuario específico, los heredado de grupos incluidos de la lista |`assignedTo('{objectId of user}')`

### <a name="response"></a>Respuesta

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Obtener información acerca de una asignación de roles

Obtiene información sobre una asignación de roles único especificada por el identificador de asignación de roles.

Para obtener información sobre una asignación de roles, debe tener acceso a `Microsoft.Authorization/roleAssignments/read` operación. Todas las funciones integradas se le ha concedido acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Utilice el método **GET** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito que desee para las asignaciones de roles de la lista. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de asignación de rol}* con el identificador GUID de la asignación de roles.

3. Reemplace *{versión api}* por 2015-07-01.

### <a name="response"></a>Respuesta

Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Crear una asignación de roles

Crear una asignación de roles en el ámbito especificado para la identidad especificada concesión de la función especificada.

Para crear una asignación de roles, debe tener acceso a `Microsoft.Authorization/roleAssignments/write` operación. De las funciones integradas, solo *propietario* y *Administrador de acceso de usuarios* tienen acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Use el método **poner** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito en el que desea crear las asignaciones de roles. Cuando se crea una asignación de roles en un ámbito de elemento primario, todos los ámbitos secundarios heredan la misma asignación de roles. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1   
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de asignación de rol}* con un nuevo GUID, que se convierte en el identificador GUID de la nueva asignación de roles.

3. Reemplace *{versión api}* por 2015-07-01.

En el cuerpo de la solicitud, proporcionarán los valores en el siguiente formato:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nombre del elemento     | Obligatorio | Tipo   | Descripción |
|------------------|----------|--------|-------------|
| roleDefinitionId | Sí      | Cadena | El identificador de la función. El formato del identificador es:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | Sí      | Cadena | Id. de objeto de la entidad de seguridad de Azure AD (usuario, grupo o principal de servicio) que tiene asignado el rol. |

### <a name="response"></a>Respuesta

Código de estado: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Eliminar una asignación de roles

Eliminar una asignación de roles en el ámbito especificado.

Para eliminar una asignación de roles, debe tener acceso a la `Microsoft.Authorization/roleAssignments/delete` operación. De las funciones integradas, solo *propietario* y *Administrador de acceso de usuarios* tienen acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Use el método **Eliminar** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito en el que desea crear las asignaciones de roles. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de asignación de rol}* con el identificador de asignación de rol GUID.

3. Reemplace *{versión api}* por 2015-07-01.

### <a name="response"></a>Respuesta

Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Todas las funciones de la lista

Enumera todas las funciones que están disponibles para la asignación en el ámbito especificado.

En la lista de funciones, debe tener acceso a `Microsoft.Authorization/roleDefinitions/read` operación en el ámbito. Todas las funciones integradas se le ha concedido acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Utilice el método **GET** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito que desee enumerar las funciones. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{versión api}* por 2015-07-01.

3. Reemplazar el *{filtro de}* a la condición que desea aplicar para filtrar la lista de funciones:

  - Lista de funciones disponibles para la asignación en el ámbito especificado y cualquier de sus ámbitos secundarios:`atScopeAndBelow()`
  - Buscar una función con nombre para mostrar exacta: `roleName%20eq%20'{role-display-name}'`. Use el formulario de la dirección URL codificada del nombre para mostrar exacto de la función. Por ejemplo,`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Respuesta

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Obtener información acerca de una función

Obtiene información sobre un solo rol especificado por el identificador de definición de rol. Para obtener información sobre una única función utilizando su nombre para mostrar, consulte [la lista de todas las funciones](role-based-access-control-manage-access-rest.md#list-all-roles).

Para obtener información acerca de una función, debe tener acceso a `Microsoft.Authorization/roleDefinitions/read` operación. Todas las funciones integradas se le ha concedido acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Utilice el método **GET** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito que desee para las asignaciones de roles de la lista. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de definición de rol}* con el identificador GUID de la definición de función.

3. Reemplace *{versión api}* por 2015-07-01.

### <a name="response"></a>Respuesta

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Crear una función personalizada
Crear una función personalizada.

Para crear una función personalizada, debe tener acceso a `Microsoft.Authorization/roleDefinitions/write` operación en todos los `AssignableScopes`. De las funciones integradas, solo *propietario* y *Administrador de acceso de usuarios* tienen acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Use el método **poner** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por la primera *AssignableScope* de la función personalizada. Los siguientes ejemplos muestra cómo especificar el ámbito para los distintos niveles.

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de definición de rol}* con un nuevo GUID, que se convierte en el identificador GUID de la nueva función personalizado.

3. Reemplace *{versión api}* por 2015-07-01.

En el cuerpo de la solicitud, proporcionarán los valores en el siguiente formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nombre del elemento | Obligatorio | Tipo | Descripción |
|--------------|----------|------|-------------|
| nombre         | Sí | Cadena   | Identificador GUID de la función personalizada.    |
| properties.roleName               | Sí | Cadena   | Nombre para mostrar de la función personalizada. Caracteres de tamaño máximo de 128.                        |
| Properties.Description            | No  | Cadena   | Descripción de la función personalizada. Caracteres de tamaño máximo de 1024.                                               |
| Properties.Type                   | Sí | Cadena   | Establecer a "CustomRole."                                         |
| Properties.Permissions.Actions    | Sí | String] | Matriz de cadenas de la acción que especifica las operaciones que se le ha concedido el rol personalizado.             |
| properties.permissions.notActions | No  | String] | Matriz de cadenas de la acción que especifica las operaciones excluir de las operaciones que se le ha concedido el rol personalizado. |
| properties.assignableScopes       | Sí | String] | Matriz de ámbitos en que se puede utilizar la función personalizada.   |

### <a name="response"></a>Respuesta

Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Actualizar una función personalizada

Modificar una función personalizada.

Para modificar una función personalizada, debe tener acceso a `Microsoft.Authorization/roleDefinitions/write` operación en todos los `AssignableScopes`. De las funciones integradas, solo *propietario* y *Administrador de acceso de usuarios* tienen acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Use el método **poner** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por la primera *AssignableScope* de la función personalizada. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de definición de rol}* con el identificador GUID de la función personalizada.

3. Reemplace *{versión api}* por 2015-07-01.

En el cuerpo de la solicitud, proporcionarán los valores en el siguiente formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nombre del elemento | Obligatorio | Tipo | Descripción |
|--------------|----------|------|-------------|
| nombre         | Sí      | Cadena | Identificador GUID de la función personalizada. |
| properties.roleName | Sí | Cadena | Nombre para mostrar de la función personalizada actualizada. |
| Properties.Description | No | Cadena | Descripción de la función personalizada actualizada. |
| Properties.Type | Sí | Cadena | Establecer a "CustomRole." |
| Properties.Permissions.Actions | Sí | String] | Matriz de cadenas de la acción que especifica las operaciones a la que la función personalizada actualizado concede acceso. |
| properties.permissions.notActions | No | String] | Matriz de cadenas de la acción que especifica las operaciones excluir de las operaciones que se concede el rol personalizado actualizado. |
| properties.assignableScopes | Sí | String] | Matriz de ámbitos en que se puede utilizar la función personalizada actualizado. |

### <a name="response"></a>Respuesta

Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Eliminar una función personalizada

Eliminar una función personalizada.

Para eliminar una función personalizada, debe tener acceso a `Microsoft.Authorization/roleDefinitions/delete` operación en todos los `AssignableScopes`. De las funciones integradas, solo *propietario* y *Administrador de acceso de usuarios* tienen acceso a esta operación. Para obtener más información sobre las asignaciones de roles y el acceso de administración de recursos de Azure, consulte [Control de acceso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitar

Use el método **Eliminar** con el siguiente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de la dirección URI, realice las siguientes sustituciones para personalizar su solicitud:

1. Reemplace *{ámbito}* por el ámbito en el que desea eliminar la definición de función. Los siguientes ejemplos muestran cómo especificar el ámbito para los distintos niveles:

  - Suscripción: /subscriptions/ {Id. de suscripción}  
  - Grupo de recursos: /subscriptions/ {Id. de suscripción} / resourceGroups/myresourcegroup1  
  - Recursos: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Reemplace *{identificador de definición de rol}* con el identificador de definición de rol GUID de la función personalizada.

3. Reemplace *{versión api}* por 2015-07-01.

### <a name="response"></a>Respuesta

Código de estado: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
