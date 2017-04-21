<properties
   pageTitle="Implementar recursos con Azure CLI y plantilla | Microsoft Azure"
   description="Usar el Administrador de recursos de Azure y Azure CLI para implementar un recursos Azure. Los recursos se definen en una plantilla de administrador de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementar recursos a las plantillas de administrador de recursos y CLI de Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI de Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API DE REST](resource-group-template-deploy-rest.md)

Este tema explica cómo usar Azure CLI con las plantillas de administrador de recursos para implementar los recursos de Azure.  

> [AZURE.TIP] Para obtener ayuda con un error de depuración durante la implementación, consulte:
>
> - [Operaciones de implementación de vista con Azure CLI](resource-manager-troubleshoot-deployments-cli.md) para obtener información sobre cómo obtener la información que le ayuda a solucionar el error
> - [Solución de problemas comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver los errores comunes de implementación

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de acceso compartido de firma (SA) durante la implementación.

## <a name="quick-steps-to-deployment"></a>Pasos rápidos para su implementación

En este artículo se describe todas las distintas opciones disponibles durante la implementación. Sin embargo, a menudo solo necesita dos comandos simples. Para comenzar rápidamente con la implementación, use los comandos siguientes:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Para obtener más información acerca de las opciones de implementación que podría ser mejor a su situación, continúe leyendo este artículo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Implementar con CLI Azure

Si usted no ha utilizado previamente Azure CLI con el Administrador de recursos, vea [utilizar CLI Azure para Mac, Linux y Windows con la administración de recursos de Azure](xplat-cli-azure-resource-manager.md).

1. Inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve el resultado de su inicio de sesión.

        azure login
  
        ...
        info:    login command OK

2. Si tiene varias suscripciones, proporcione el identificador de la suscripción que desea usar para su implementación.

        azure account set <YourSubscriptionNameOrId>

3. Cambiar a módulo de administrador de recursos de Azure. Recibir confirmación del nuevo modo.

        azure config mode arm
   
        info:     New mode is arm

4. Si no tiene un grupo de recursos existente, cree un grupo de recursos. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Debe proporcionar una ubicación para el grupo de recursos, porque el grupo de recursos almacena metadatos acerca de los recursos. Por motivos de cumplimiento, desea especificar dónde se almacenan metadatos. En general, recomendamos que especifique una ubicación donde se guardarán la mayoría de los recursos. Uso de la misma ubicación, puede simplificar la plantilla.

        azure group create -n ExampleResourceGroup -l "West US"

     Se devuelve un resumen del nuevo grupo de recursos.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Validar la implementación antes de ejecutar, ejecute el comando **validar la plantilla de grupo de azure** . Cuando se prueba la implementación, proporcionar parámetros tal como lo haría al ejecutar la implementación (que se muestra en el siguiente paso).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Para implementar recursos en el grupo de recursos, ejecute el comando siguiente y proporcione los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre de su grupo de recursos, la ruta de acceso o la dirección URL para la plantilla y otros parámetros necesarias para su situación. 
   
     Tiene las tres opciones siguientes para proporcionar los valores de parámetro: 

     1. Usar parámetros en línea y una plantilla local. Cada parámetro tiene el formato: `"ParameterName": { "value": "ParameterValue" }`. En el ejemplo siguiente se muestra los parámetros con caracteres de escape.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Usar parámetros en línea y un vínculo a una plantilla.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Usar un archivo de parámetros. Para obtener información sobre el archivo de plantilla, vea el [archivo de parámetros](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Después de que se han implementado los recursos a través de uno de los tres métodos anteriores, verá un resumen de la implementación.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Para ejecutar una implementación completa, establezca **el modo** en **completado**. Tenga cuidado al usar este modo como accidentalmente puede eliminar los recursos que no están en la plantilla.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Si desea registrar información adicional acerca de la implementación que puede ayudarle a solucionar los errores de implementación, use el parámetro de **configuración de depuración** . Puede especificar que el contenido de la solicitud, el contenido de respuesta o ambos haber iniciado con la operación de implementación.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Implementar la plantilla de almacenamiento con token SA

Puede agregar sus plantillas a una cuenta de almacenamiento y un vínculo a ellos durante la implementación con un símbolo de SA.

> [AZURE.IMPORTANT] Siguiendo los pasos siguientes, el blob que contiene la plantilla es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SA para el blob, el blob es accesible para cualquiera con ese URI. Si otro usuario intercepta el URI, ese usuario es tener acceso a la plantilla. Utilizando un token SA es una buena manera de limitar el acceso a sus plantillas, pero no debe incluir datos confidenciales como contraseñas directamente en la plantilla.

### <a name="add-private-template-to-storage-account"></a>Agregar plantilla privado a la cuenta de almacenamiento

Establecer una cuenta de almacenamiento para las plantillas de los siguientes pasos:

1. Crear un grupo de recursos.

        azure group create -n "ManageGroup" -l "westus"

2. Crear una cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único en Azure, así que especifique su propio nombre de la cuenta.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Establezca las variables de la cuenta de almacenamiento y la clave.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Crear un contenedor. El permiso está establecido en **desactivar** lo que significa que el contenedor sólo es accesible para el propietario.

        azure storage container create --container templates -p Off 
        
4. Agregar la plantilla al contenedor.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Proporcionar token SA durante la implementación

Para implementar una plantilla privada en una cuenta de almacenamiento, recuperar un token SA e incluirlo en el URI de la plantilla.

1. Crear un símbolo de SA con permisos de lectura y una fecha de caducidad para limitar el acceso. Establecer la fecha de caducidad para permitir tiempo suficiente completar la implementación. Recuperar el URI completo de la plantilla, incluido el token de SA.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Implementar la plantilla proporcionando el URI que incluye el token de SA.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Para obtener un ejemplo de un símbolo de SA con las plantillas vinculadas, consulte [uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Pasos siguientes
- Para obtener un ejemplo de implementación de recursos a través de la biblioteca de cliente .NET, vea [recursos de implementar mediante una plantilla y bibliotecas de .NET](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parámetros de plantilla, consulte [creación de plantillas](resource-group-authoring-templates.md#parameters).
- Para obtener instrucciones sobre cómo implementar la solución en distintos entornos, consulte [entornos de desarrollo y prueba en Microsoft Azure](solution-dev-test-environments.md).
- Para obtener información detallada sobre el uso de una referencia KeyVault pasar valores seguros, vea [pasar valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

