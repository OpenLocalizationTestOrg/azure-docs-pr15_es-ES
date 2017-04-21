<properties
   pageTitle="Creación de soluciones de administración en conjunto de aplicaciones de administración de operaciones (OMS) | Microsoft Azure"
   description="Soluciones de administración de amplían la funcionalidad de conjunto de aplicaciones de administración de operaciones (OMS) proporcionando escenarios de administración empaquetado de los clientes pueden agregar a su área de trabajo OMS.  En este artículo se proporciona información detallada sobre cómo crear soluciones de administración para usarlo en su entorno u ofrecerse a sus clientes."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Creación de soluciones de administración de operaciones Management Suite (OMS) (vista preliminar)

>[AZURE.NOTE]Esta es una documentación preliminar para crear soluciones de administración de OMS que actualmente están en la vista previa. Cualquier esquema que se describe a continuación está sujeto a cambios.  

Soluciones de administración de amplían la funcionalidad de conjunto de aplicaciones de administración de operaciones (OMS) proporcionando escenarios de administración empaquetado de los clientes pueden agregar a su área de trabajo OMS.  Este artículo ofrece detalles sobre cómo crear sus propias soluciones de administración que puede usar en su propio entorno o que esté disponible para los clientes a través de la Comunidad.

## <a name="planning-your-management-solution"></a>Planear la solución de administración
Soluciones de administración de OMS incluyen varios recursos auxiliares un escenario de administración determinado.  Al planear la solución, debe centrarse en el caso de que está tratando de lograr y todos los recursos necesarios para dar soporte.  Cada solución debe ser independiente y definir cada recurso que necesita, incluso si uno o más recursos también se definen en otras soluciones.  Cuando se instala una solución de administración, se crea cada recurso a menos que ya existe y, a continuación, puede definir qué ocurre con recursos cuando se quita una solución.  

Por ejemplo, una solución de administración puede incluir un [runbook de automatización de Azure](../automation/automation-intro.md) que recopila los datos en el repositorio de análisis de registro con una [programación](../automation/automation-schedules.md) y una [vista](../log-analytics/log-analytics-view-designer.md) que proporciona varias visualizaciones de los datos recopilados.  El mismo plan podría ser usado por otra solución.  Como autor de la solución de administración, podría definir todos los recursos de tres pero especificar que la vista y runbook deben ser eliminados automáticamente cuando se quita la solución.    También podría definir la programación pero especificar que debe permanecer en su lugar si se ha quitado la solución en caso de que estaba en uso por la otra solución.

## <a name="management-solution-files"></a>Archivos de la solución de administración
Soluciones de administración se implementan como [plantillas de administración de recursos](../resource-manager-template-walkthrough.md).  La tarea principal para aprender cómo crear soluciones de administración es aprendizaje cómo [autor de una plantilla](../resource-group-authoring-templates.md).  Este artículo proporciona detalles únicos de plantillas que se utilizan para que soluciones y cómo definir los recursos de solución típica.

La estructura básica de un archivo de solución de administración es igual a una [Plantilla de administrador de recursos](resource-group-authoring-templates.md#template-format) , que es el siguiente.  Cada una de las siguientes secciones describe los elementos de nivel superior y y su contenido en una solución.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parámetros

[Parámetros](../resource-group-authoring-templates.md#parameters) son valores que requieren del usuario al instalar la solución de administración.  Hay parámetros estándares que tienen todas las soluciones y puede agregar parámetros adicionales según sea necesario para la solución en particular.  ¿Cómo los usuarios proporcionar valores de parámetro al instalar la solución dependerá el parámetro concreto y cómo se instala la solución.


Cuando un usuario instala la solución de administración a través de [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) o [plantillas de Azure tutorial](operations-management-suite-solutions.md#finding-and-installing-solutions) le pide que seleccione un [área de trabajo OMS y cuenta con la automatización](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Se utilizan para rellenar los valores de cada uno de los parámetros estándar.  El usuario no le pedirá que proporcione directamente los valores de los parámetros estándar, pero le pide que proporcione valores para los parámetros adicionales.

Cuando el usuario instale la solución [otro método](operations-management-suite-solutions.md#finding-and-installing-solutions), proporcione un valor para todos los parámetros estándares y todos los parámetros adicionales.

A continuación se muestra un parámetro de ejemplo.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

La siguiente tabla describe los atributos de un parámetro.

| Atributo | Descripción |
|:--|:--|
| tipo        | Tipo de datos para el parámetro. El control de entrada que se muestran para el usuario depende del tipo de datos.<br><br>BOOL - cuadro de lista desplegable<br>cadena - cuadro de texto<br>int - cuadro de texto<br>SecureString - campo contraseña<br> |
| categoría    | Categoría opcional para el parámetro.  Parámetros de la misma categoría se agrupan. |
| control     | Funciones adicionales para los parámetros de cadena.<br><br>se muestra DateTime - control de fecha y hora.<br>GUID - valor Guid se genera automáticamente y no se muestra el parámetro. |
| Descripción | Descripción opcional para el parámetro.  Se muestran en un globo de información junto al parámetro. |


### <a name="standard-parameters"></a>Parámetros estándar
La siguiente tabla enumeran los parámetros estándar para todas las soluciones de administración.  Se rellena para el usuario en lugar de pedir cuando se instala la solución desde las plantillas de tutorial rápido o Azure Marketplace.  El usuario debe proporcionar los valores para ellos si se instala la solución con otro método.

>[AZURE.NOTE]La interfaz de usuario en las plantillas de tutorial rápido y Azure Marketplace espera los nombres de parámetro en la tabla.  Si utiliza los nombres de parámetro diferente, a continuación, se le solicita el usuario para ellos y no se rellenarán automáticamente.


| Parámetro | Tipo | Descripción |
|:--|:--|:--|
| nombre de la cuenta       | cadena |  Nombre de la cuenta de automatización Azure. |
| pricingTier       | cadena | Nivel de precios de área de trabajo de análisis de registro y cuenta de automatización de Azure. |
| regionId          | cadena | Región de la cuenta de automatización de Azure. |
| nombre de solución      | cadena | Nombre de la solución. |
| workspaceName     | cadena | Nombre del área de trabajo de análisis de registro. |
| workspaceRegionId | cadena | Región del área de trabajo de análisis de registro. |





### <a name="sample"></a>Ejemplo
A continuación se muestra una entidad de parámetro de ejemplo para una solución.  Esto incluye todos los parámetros estándar y dos parámetros adicionales de la misma categoría.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Hacer referencia a valores de parámetro en otros elementos de la solución con la sintaxis **parámetros ('nombre de parámetro')**.  Por ejemplo, para tener acceso al nombre de área de trabajo, debería usar **parameters('workspaceName')** 

## <a name="variables"></a>Variables

El elemento de **Variables** incluye valores que usará el resto de la solución de administración.  Estos valores no se exponen en el usuario que instale la solución.  Van a proporcionar al autor con una única ubicación donde pueden administrar los valores que se pueden usar varias veces en toda la solución. Debe colocar los valores específicos para la solución de variables en lugar de codificar ellos en el elemento de **recursos** .  Esto hace más legible el código y le permite cambiar fácilmente estos valores en versiones posteriores.

A continuación se muestra un ejemplo de un elemento de **variables** con parámetros típicos usados en soluciones.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Hacer referencia a valores de variables a través de la solución con la sintaxis **variables ('nombre de variable')**.  Por ejemplo, para obtener acceso a la variable de nombre de solución, debería usar **variables('solutionName')** 


## <a name="resources"></a>Recursos

El elemento de **recursos** define los diferentes incluidos en la solución de administración de recursos.  Se trata de la parte más grande y compleja de la plantilla.  Recursos se definen con la estructura siguiente.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dependencias
Los elementos de **dependsOn** especifica una [dependencia](../resource-group-define-dependencies.md) en otro recurso.  Cuando se instala la solución, no se crea un recurso hasta que se han creado todas sus dependencias.  Por ejemplo, la solución podría [iniciar un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) cuando se instala con un [recurso de trabajo](operations-management-suite-solutions-resources-automation.md#automation-jobs).  El recurso de trabajo sería dependiente del recurso runbook para asegurarse de que se ha creado runbook antes de crea el trabajo.

### <a name="oms-workspace-and-automation-account"></a>Área de trabajo OMS y cuenta con la automatización
Soluciones de administración requieren un [área de trabajo OMS](../log-analytics/log-analytics-manage-access.md) para contener vistas y una [cuenta de automatización](../automation/automation-security-overview.md#automation-account-overview) que contenga runbooks y recursos relacionados.  Estos deben estar disponibles para que los recursos en la solución se crean y no se deben definir en la propia solución.  El usuario va a [especificar un área de trabajo y una cuenta](operations-management-suite-solutions.md#oms-workspace-and-automation-account) cuando implementa la solución, pero como el autor debe tener en cuenta los siguientes puntos.


## <a name="solution-resource"></a>Recursos de solución
Cada solución requiere una entrada de recursos en el elemento de **recursos** que define la propia solución.  Esto tendrá un tipo de **Microsoft.OperationsManagement/solutions**.  A continuación se muestra un ejemplo de un recurso de solución.  Los diferentes elementos se describen en las secciones siguientes.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nombre de la solución
El nombre de la solución debe ser único en su suscripción de Azure. El valor recomendado para utilizar es la siguiente.  Utiliza una variable denominada **nombre de solución** para el nombre base y el parámetro **workspaceName** para asegurarse de que el nombre es único.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Esto se resuelve en un nombre similar al siguiente.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Dependencias
Recursos de solución deben tener una [dependencia](../resource-group-define-dependencies.md) en todos los recursos en la solución ya que deben existir antes de que la solución puede crearse.  Para ello, agregando una entrada para cada recurso en el elemento **dependsOn** .


### <a name="properties"></a>Propiedades
Recursos de solución tiene las propiedades de la tabla siguiente.  Esto incluye los recursos de referencia y contenidos en la solución que define cómo se administra el recurso después de instala la solución.  Cada recurso en la solución debe aparecer en la **referencedResources** o la propiedad **containedResources** .

| (Propiedad) | Descripción |
|:--|:--|
| workspaceResourceId | Id. del área de trabajo OMS en el formulario * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nombre del área de trabajo\>*. |
| referencedResources   | Lista de recursos de la solución que no se quitan cuando se quita la solución. |
| containedResources   | Lista de recursos de la solución que se debe quitar cuando se quita la solución. |

El ejemplo anterior es una solución con un runbook, programación y la vista.  La programación y runbook son *que se hace referencia* en el elemento de **Propiedades** para que no se quitan cuando se quita la solución.  La vista es el *contenido* para que se elimina cuando se elimina la solución.


### <a name="plan"></a>Plan
La entidad de **plan** de recursos de solución tiene las propiedades de la tabla siguiente. 

| (Propiedad) | Descripción |
|:--|:--|
| nombre          | Nombre de la solución. |
| Versión       | Versión de la solución según lo determinado por el autor. |
| producto       | Cadena única para identificar la solución. |
| Publisher     | Editor de la solución. |


## <a name="other-resources"></a>Otros recursos
Puede obtener los detalles y ejemplos de recursos que sean comunes a soluciones de administración en los artículos siguientes.

- [Vistas y paneles](operations-management-suite-solutions-resources-views.md)
- [Recursos de automatización](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Probar una solución de administración
Antes de implementar la solución de administración, se recomienda probar mediante [AzureRmResourceGroupDeployment de prueba](../resource-group-template-deploy.md#deploy-with-powershell).  Esto valide el archivo de solución y ayuda para que identificar los problemas antes de implementarlo.



## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre los detalles de [plantillas de administrador de recursos de Azure de creación](../resource-group-authoring-templates.md).
- Buscar [Plantillas de Azure tutorial rápido](https://azure.microsoft.com/documentation/templates) para obtener ejemplos de plantillas de administrador de recursos diferentes.
- Ver los detalles para [Agregar vistas de una solución de administración](operations-management-suite-solutions-resources-views.md).
- Ver los detalles para [Agregar una solución de administración de recursos de automatización](operations-management-suite-solutions-resources-automation.md).
 