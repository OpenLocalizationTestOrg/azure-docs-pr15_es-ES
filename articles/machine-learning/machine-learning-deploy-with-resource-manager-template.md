<properties
    pageTitle="Implementar el área de trabajo con Azure plantilla de administrador de recursos de aprendizaje | Microsoft Azure"
    description="Cómo implementar un área de trabajo para el aprendizaje Azure con el Administrador de recursos de Azure plantilla"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Implementar el área de trabajo con Azure Administrador de recursos de aprendizaje

## <a name="introduction"></a>Introducción
Con un administrador de recursos de Azure plantilla de implementación ahorra tiempo asignando una manera scalable implementar componentes interconectados con una validación y vuelva a intentar mecanismo. Para configurar áreas de trabajo de aprendizaje de máquina de Azure, por ejemplo, debe configurar una cuenta de almacenamiento de Azure y, a continuación, implementar el área de trabajo. Imagine hacerlo manualmente para cientos de áreas de trabajo. Una alternativa más fácil es utilizar una plantilla de administrador de recursos de Azure para implementar un área de trabajo de aprendizaje de máquina de Azure y todas sus dependencias. En este artículo le guiará en el proceso paso a paso. Para ver una excelente introducción del Administrador de recursos de Azure, consulte [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Paso a paso: crear un área de trabajo de aprendizaje de equipo
Se crea un grupo de recursos de Azure e implementar una nueva cuenta de almacenamiento de Azure y una nueva área de aprendizaje de máquina de Azure usando una plantilla de administrador de recursos. Una vez completada la implementación, se imprimirá información importante sobre las áreas de trabajo que se crearon (la clave principal, el workspaceID y la dirección URL para el área de trabajo).

### <a name="create-an-azure-resource-manager-template"></a>Crear una plantilla de administrador de recursos de Azure
Un área de trabajo de aprendizaje de equipo requiere una cuenta de almacenamiento de Azure para almacenar el conjunto de datos vinculado a ella.
La siguiente plantilla usa el nombre del grupo de recursos para generar el nombre de la cuenta de almacenamiento y el nombre del área de trabajo.  También usa el nombre de la cuenta de almacenamiento como una propiedad al crear el área de trabajo.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Guardar esta plantilla como archivo de mlworkspace.json en c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementar el grupo de recursos basado en la plantilla
* Abrir PowerShell
* Instalar módulos para el Administrador de recursos de Azure y administración de servicios de Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estos pasos descargar e instalación los módulos necesarios para completar los pasos restantes. Solo debe hacerse una vez en el entorno de dónde se ejecute los comandos de PowerShell.   

* Autenticar a Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Este paso es necesario repetir para cada sesión. Una vez autenticada, debe mostrarse la información de la suscripción.

![Cuenta de Azure][1]

Ahora que tenemos acceso a Azure, podemos crear el grupo de recursos.

* Crear un grupo de recursos

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Compruebe que se ha creado correctamente el grupo de recursos. **ProvisioningState** debe ser "correcta".
El nombre del grupo de recursos se usa la plantilla para generar el nombre de la cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe estar entre 3 y 24 caracteres de longitud y utilizar solo letras minúsculas y números.

![Grupo de recursos][2]

* Con la implementación de grupo de recursos, implementar una nueva área de trabajo de aprendizaje de equipo.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Una vez completada la implementación, resulta sencillo a las propiedades de acceso del área de trabajo que implementa. Por ejemplo, puede tener acceso el Token de clave principal.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Otra forma de recuperar tokens del área de trabajo existente es usar el comando invocar AzureRmResourceAction. Por ejemplo, puede mostrar los símbolos de primario y secundarios de todas las áreas de trabajo.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Después de que se aprovisiona el área de trabajo, también puede automatizar muchas de las tareas de Azure máquina aprendizaje Studio uso del [Módulo de PowerShell para el aprendizaje Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Pasos siguientes 
* Más información sobre las [Plantillas de administrador de recursos de Azure edición](../resource-group-authoring-templates.md). 
* Eche un vistazo en el [Repositorio de plantillas de Azure tutorial rápido](https://github.com/Azure/azure-quickstart-templates). 
* Vea este vídeo sobre [El Administrador de recursos de Azure](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
