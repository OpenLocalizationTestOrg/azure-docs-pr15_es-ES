<properties 
    pageTitle="Bloquear los recursos con el Administrador de recursos | Microsoft Azure" 
    description="Evitar que los usuarios actualizar o eliminar determinados recursos al aplicar una restricción a todos los usuarios y roles." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Recursos de bloqueo con el Administrador de recursos de Azure

Como administrador, debe bloquear una suscripción, grupo de recursos o recursos para evitar que otros usuarios de su organización, eliminar o modificar recursos críticos accidentalmente. Puede establecer el nivel de bloqueo **CanNotDelete** o **sólo lectura**. 

- **CanNotDelete** significa que los usuarios autorizados aún pueden leer y modificar un recurso, pero no puede eliminarlo. 
- **Sólo lectura** significa que pueden leer los usuarios autorizados de un recurso, pero no puede eliminarlo ni realizar acciones en él. Los permisos en el recurso están restringido a la función **lector** . 

Aplicar **sólo lectura** puede provocar resultados inesperados porque algunas operaciones que parezcan leen operaciones realmente requieren acciones adicionales. Por ejemplo, colocando un bloqueo de **sólo lectura** en una cuenta de almacenamiento impide que todos los usuarios enumerando las claves. La lista de operación de claves se administra mediante una solicitud de publicación porque las claves devueltas están disponibles para operaciones de escritura. Otro ejemplo, colocando un bloqueo de **sólo lectura** en un recurso de servicio de aplicación impide que Visual Studio Server Explorer mostrar archivos para el recurso, porque esa interacción requiere acceso de escritura.

A diferencia de control de acceso basado en roles, utilice bloqueos de administración para aplicar una restricción a través de todos los usuarios y roles. Para obtener información sobre cómo establecer permisos para usuarios y roles, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

Al aplicar un bloqueo en un ámbito de elemento primario, todos los recursos secundarios heredan el mismo bloqueo. Incluso los recursos que agregará más adelante heredan el bloqueo del elemento primario. El bloqueo de la herencia más restrictiva tiene prioridad.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>¿Quién puede crear o eliminar bloqueos de su organización

Para crear o eliminar bloqueos de administración, debe tener acceso a **Microsoft.Authorization/\* ** o **Microsoft.Authorization/locks/\* ** acciones. De las funciones integradas, solo el **propietario** y el **Administrador de acceso de usuario** se le concede esas acciones.

## <a name="creating-a-lock-through-the-portal"></a>Crear un bloqueo a través del portal

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Crear un bloqueo en una plantilla

En el ejemplo siguiente se muestra una plantilla que crea un bloqueo en una cuenta de almacenamiento. La cuenta de almacenamiento en la que desea aplicar el bloqueo se proporciona como un parámetro. El nombre del bloqueo se crea concatenando el nombre del recurso con **/Microsoft.Authorization/** y el nombre del bloqueo, en este caso **myLock**.

El tipo proporcionado es específico del tipo de recurso. Para el almacenamiento, establezca el tipo de "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Crear un bloqueo con la API de REST

Puede bloquear recursos implementados con la [API de REST de bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx). La API de REST le permite crear y eliminar bloqueos y recuperar información acerca de los bloqueos existentes.

Para crear un bloqueo, ejecute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

El ámbito puede ser una suscripción, el grupo de recursos o el recurso. El nombre de bloqueo es lo que desea llamar el bloqueo. Versión de la api, use **2015-01-01**.

En la convocatoria, incluya un objeto JSON que especifica las propiedades para el bloqueo.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Para obtener ejemplos, vea [API de REST de bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Crear un bloqueo con PowerShell de Azure

Puede bloquear recursos implementados con PowerShell Azure usando el **Nuevo AzureRmResourceLock** tal como se muestra en el ejemplo siguiente.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

PowerShell Azure proporciona otros comandos para trabajar bloqueos, como **Establecer AzureRmResourceLock** para actualizar un bloqueo y **AzureRmResourceLock quitar** para eliminar un bloqueo.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo trabajar con bloqueos de recursos, vea [Bloquear abajo sus recursos de Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para obtener información acerca de la organización lógicamente los recursos, vea [mediante etiquetas para organizar los recursos](resource-group-using-tags.md)
- Para cambiar un recurso se encuentra en qué grupo de recursos, vea [mover los recursos al nuevo grupo de recursos](resource-group-move-resources.md)
- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para obtener más información, vea [Utilizar una directiva para administrar los recursos y controlar el acceso](resource-manager-policy.md).
