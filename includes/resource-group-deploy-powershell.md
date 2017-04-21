## <a name="how-to-deploy-with-powershell"></a>Cómo implementar con PowerShell

1. Inicie sesión en su cuenta de Azure.

          Add-AzureAccount

   Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Si tiene varias suscripciones, proporcione el identificador de la suscripción que desea usar para su implementación. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Cambiar el módulo de administrador de recursos de Azure.

          Switch-AzureMode AzureResourceManager

4. Si no tiene un grupo de recursos existente, cree un nuevo grupo de recursos. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Se devuelve un resumen del nuevo grupo de recursos.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Para crear una nueva implementación para el grupo de recursos, ejecute el comando **AzureResourceGroupDeployment de nuevo** y proporcione los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre de su grupo de recursos, la ruta de acceso o la dirección URL a la plantilla que creó y otros parámetros necesarias para su situación. 
   
   Tiene las opciones siguientes para proporcionar los valores de parámetro: 
   
   - Usar parámetros en línea.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Utilizar un objeto de parámetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Con un archivo de parámetros.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Cuando se ha implementado el grupo de recursos, verá un resumen de la implementación.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Para obtener información sobre los errores de implementación.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Para obtener información detallada sobre los errores de implementación.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
