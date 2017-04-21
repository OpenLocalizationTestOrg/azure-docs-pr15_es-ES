<properties
   pageTitle="Recursos de automatización en soluciones OMS | Microsoft Azure"
   description="Soluciones de OMS normalmente incluirá runbooks en automatización de Azure automatizar procesos como recopilar y procesar datos de supervisión.  En este artículo se describe cómo incluir runbooks y sus recursos relacionados en una solución."
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
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Recursos de automatización en soluciones OMS (vista preliminar)

>[AZURE.NOTE]Esta es una documentación preliminar para crear soluciones de administración de OMS que actualmente están en la vista previa. Cualquier esquema que se describe a continuación está sujeto a cambios.   

[Soluciones de administración de OMS](operations-management-suite-solutions.md) normalmente incluirá runbooks en automatización de Azure automatizar procesos como recopilar y procesar datos de supervisión.  Además de runbooks, cuentas de automatización incluye activos como variables y programaciones que admite la runbooks utilizado en la solución.  En este artículo se describe cómo incluir runbooks y sus recursos relacionados en una solución.

>[AZURE.NOTE]Los ejemplos de este artículo utilizan parámetros y las variables que son necesarios o comunes para soluciones de administración y se describen en la [creación de soluciones de administración en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con el procedimiento para [crear una solución de administración](operations-management-suite-solutions-creating.md) y la estructura de un archivo de solución.

## <a name="samples"></a>Ejemplos
Puede obtener plantillas de administrador de recursos de ejemplo para los recursos de automatización de las [plantillas de tutorial en GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Cuenta de automatización
Todos los recursos en la automatización de Azure se encuentran en una [cuenta de automatización](../automation/automation-security-overview.md#automation-account-overview).  Como se describe en [el área de trabajo OMS y cuenta con la automatización](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) la cuenta de automatización no se incluye en la solución de administración, pero debe existir antes de instalar la solución.  Si no está disponible, se producirá un error en la instalación de la solución.

El nombre de su cuenta de automatización es el nombre de cada recurso de automatización.  Esto se realiza en la solución con el parámetro de **nombre de la cuenta** , como se muestra en el siguiente ejemplo de un recurso runbook.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Recursos de [automatización de Azure runbook](../automation/automation-runbook-types.md) tienen un tipo de **Microsoft.Automation/automationAccounts/runbooks** y tienen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| runbookType | Especifica los tipos de runbook. <br><br> Secuencia de comandos: secuencia de comandos de PowerShell <br>PowerShell - flujo de trabajo de PowerShell <br> GraphPowerShell - runbook de secuencia de comandos de PowerShell gráfica <br> GraphPowerShellWorkflow - runbook de flujo de trabajo de PowerShell gráfica   |
| logProgress | Especifica si se deben generar [los registros de progreso](../automation/automation-runbook-output-and-messages.md) para runbook. |
| logVerbose  | Especifica si se deben generar [registros detallados](../automation/automation-runbook-output-and-messages.md) para runbook. |
| Descripción | Descripción opcional de runbook. |
| publishContentLink | Especifica el contenido del runbook. <br><br>URI - Uri al contenido del runbook.  Se trata de un archivo. ps1 para runbooks PowerShell y secuencias de comandos y un archivo exportado runbook gráfica para un runbook de gráfico.  <br> versión: versión del runbook para su propio seguimiento. |

Un ejemplo de un recurso runbook está por debajo.  En este caso, recupera runbook de la [Galería de PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Iniciar un runbook
Existen dos métodos para iniciar un runbook en una solución de administración.

- Para iniciar runbook cuando se instala la solución, cree un [recurso de trabajo](#automation-jobs) como se describe a continuación.
- Para iniciar runbook en una programación, cree un [recurso de programación](#schedules) tal como se describe a continuación. 


## <a name="automation-jobs"></a>Tareas de automatización
Para iniciar un runbook cuando se instala la solución de administración, cree un recurso de **trabajo** .  Recursos de trabajo tienen un tipo de **Microsoft.Automation/automationAccounts/jobs** y las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| runbook    | Entidad de **nombre** con el nombre del runbook.  |
| parámetros | Entidad para cada parámetro requerido por runbook. |


El trabajo incluye el nombre del runbook y los valores de parámetro que se envíen a runbook.  El trabajo debe [dependen de](operations-management-suite-solutions-creating.md#resources) que se inicia desde runbook runbook debe crearse antes de la tarea.  También se pueden crear dependencias en el resto de trabajos de runbooks que deben completarse antes de la actual.

El nombre de un recurso de trabajo debe contener un GUID que normalmente se asigna un parámetro.  Puede obtener más información acerca de los parámetros GUID de la [creación de soluciones en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-solutions-creating.md#parameters).  

A continuación se muestra un ejemplo de un recurso de trabajo que se inicia un runbook cuando se instala la solución de administración.  Uno debe llevar a cabo otras runbooks antes de esto uno inicia, por lo que tiene dependencias en las tareas para ese runbook.  Los detalles de la tarea se proporcionan a través de los parámetros y variables en lugar de que se especifica directamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificados
[Certificados de automatización de Azure](../automation/automation-certificates.md) tiene un tipo de **Microsoft.Automation/automationAccounts/certificates** y las propiedades en la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| base64Value   | Valor de base 64 para el certificado. |
| huella digital    | Huella digital del certificado. |

Un ejemplo de un recurso de certificado está por debajo.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenciales
[Credenciales de automatización de Azure](../automation/automation-credentials.md) tiene un tipo de **Microsoft.Automation/automationAccounts/credentials** y las propiedades en la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| nombre de usuario   | Nombre de usuario de la credencial. |
| contraseña   | Contraseña de la credencial. |

Un ejemplo de un recurso de credenciales está por debajo.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Programaciones
[Automatización de Azure programaciones](../automation/automation-schedules.md) tiene un tipo de **Microsoft.Automation/automationAccounts/schedules** y tienen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Descripción | Descripción opcional de la programación. |
| hora de inicio   | Especifica la hora de inicio de una programación como un objeto DateTime. Se puede proporcionar una cadena si se puede convertir en un valor de DateTime válido. |
| isEnabled   | Especifica si se habilita la programación. |
| intervalo    | El tipo de intervalo para la programación.<br><br>día<br>hora |
| frecuencia   | Frecuencia con la que se debe activar la programación en número de días u horas. |

Un ejemplo de un recurso de programación está por debajo.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variables
[Variables de automatización de Azure](../automation/automation-variables.md) tiene un tipo de **Microsoft.Automation/automationAccounts/variables** y las propiedades en la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Descripción | Descripción opcional de la variable. |
| isEncrypted | Especifica si se debe cifrar la variable. |
| tipo        | Tipo de datos de la variable. |
| valor       | Valor de la variable. |

Un ejemplo de un recurso variable está por debajo.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Módulos
La solución de administración no es necesario definir [módulos globales](../automation/automation-integration-modules.md) utilizados por sus runbooks porque siempre estará disponibles.  Debe incluir un recurso para cualquier otro módulo usado por sus runbooks y runbook debe dependen del recurso de módulo para asegurarse de que se crea antes runbook.

[Módulos de integración](../automation/automation-integration-modules.md) tienen un tipo de **Microsoft.Automation/automationAccounts/modules** y las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| contentLink | Especifica el contenido del módulo. <br><br>URI - Uri al contenido del runbook.  Se trata de un archivo. ps1 para runbooks PowerShell y secuencias de comandos y un archivo exportado runbook gráfica para un runbook de gráfico.  <br> versión: versión del runbook para su propio seguimiento. |

Un ejemplo de un recurso del módulo está por debajo.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Actualizar los módulos
Si actualiza una solución de administración que incluye un runbook que usa una programación y la nueva versión de la solución tiene un nuevo módulo que utiliza ese runbook, runbook puede usar la versión anterior del módulo.  Debe incluir la runbooks siguientes en la solución y crear una tarea para ejecutarlos antes de cualquier otro runbooks.  Esto se asegurará de que todos los módulos se actualizan como necesaria antes de cargar el runbooks.

- [Actualización ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) se asegurará de que todos los módulos utilizados por runbooks en la solución son la versión más reciente.  
- [Administración de MS ReRegisterAutomationSchedule](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) se vuelve a registrar todos los recursos de programación para asegurarse de que la runbooks vinculados a dichas con uso los últimos módulos.


A continuación se muestra un ejemplo de los elementos de una solución para la actualización del módulo de soporte técnico.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Pasos siguientes

- [Agregar una vista a la solución](operations-management-suite-solutions-resources-views.md) para visualizar los datos recopilados.