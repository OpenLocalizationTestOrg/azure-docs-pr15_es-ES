## <a name="how-to-deploy-with-azure-cli"></a>Cómo implementar con CLI de Azure

1. Inicie sesión en su cuenta de Azure.

        azure login

  Después de proporcionar sus credenciales, el comando devuelve el resultado de su inicio de sesión.

        ...
        info:    login command OK

2. Si tiene varias suscripciones, proporcione el identificador de la suscripción que desea usar para su implementación.

        azure account set <YourSubscriptionNameOrId>

3. Cambiar a módulo de administrador de recursos de Azure

        azure config mode arm

   Recibirá confirmación del nuevo modo.

        info:     New mode is arm

4. Si no tiene un grupo de recursos existente, cree un nuevo grupo de recursos. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución.

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

5. Para crear una nueva implementación para el grupo de recursos, ejecute el comando siguiente y proporcione los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre de su grupo de recursos, la ruta de acceso o la dirección URL a la plantilla que creó y otros parámetros necesarias para su situación.

   Tiene las opciones siguientes para proporcionar los valores de parámetro:

   - Usar parámetros en línea y una plantilla local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Usar parámetros en línea y un vínculo a una plantilla.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Usar un archivo de parámetros.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Cuando se ha implementado el grupo de recursos, verá un resumen de la implementación.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Para obtener información acerca de la implementación más reciente.

         azure group log show -l ExampleResourceGroup

7. Para obtener información detallada sobre los errores de implementación.

         azure group log show -l -v ExampleResourceGroup
