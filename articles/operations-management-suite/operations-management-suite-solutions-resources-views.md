<properties
   pageTitle="Vistas de soluciones de administración del conjunto de aplicaciones de administración de operaciones (OMS) | Microsoft Azure"
   description="Soluciones de administración en conjunto de aplicaciones de administración de operaciones (OMS) normalmente incluirá una o más vistas para visualizar datos.  En este artículo se describe cómo exportar una vista creada por el Diseñador de vistas e incluir en una solución de administración. "
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
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Vistas de soluciones de administración del conjunto de aplicaciones de administración de operaciones (OMS) (vista preliminar)

>[AZURE.NOTE]Esta es una documentación preliminar para crear soluciones de administración de OMS que actualmente están en la vista previa. Cualquier esquema que se describe a continuación está sujeto a cambios.    

[Soluciones de administración en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-solutions.md) normalmente incluirá una o más vistas para visualizar datos.  En este artículo se describe cómo exportar una vista creada por el [Diseñador de vistas](../log-analytics/log-analytics-view-designer.md) e incluir en una solución de administración.  

>[AZURE.NOTE]Los ejemplos de este artículo utilizan parámetros y las variables que son necesarios o comunes para soluciones de administración y se describen en la [creación de soluciones de administración en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con el procedimiento para [crear una solución de administración](operations-management-suite-solutions-creating.md) y la estructura de un archivo de solución.


## <a name="overview"></a>Información general

Para incluir una vista en una solución de administración, cree un **recurso** para él en el [archivo de solución](operations-management-suite-solutions-creating.md).  El JSON que describa la configuración de la vista detallada es normalmente complejo aunque y no algo que un autor de solución típica podrán crear de forma manual.  Es el método más habitual crear la vista mediante el [Diseñador de vistas](../log-analytics/log-analytics-view-designer.md), exportarlo y, a continuación, agregue su configuración detallada para la solución. 

Los pasos básicos para agregar una vista a una solución son los siguientes.  Cada paso se describe en más detalle en las secciones siguientes.

1. Exportar la vista a un archivo.
2. Crear el recurso de la vista en la solución.
3. Agregue los detalles de la vista.

## <a name="export-the-view-to-a-file"></a>Exportar la vista a un archivo
Siga las instrucciones en el [Diseñador de vistas de análisis de registro](../log-analytics/log-analytics-view-designer.md) para exportar una vista a un archivo.  El archivo exportado estarán en formato JSON con los mismos [elementos que el archivo de solución](operations-management-suite-solutions-creating.md#management-solution-files).  

El elemento de **recursos** del archivo de vista tendrá un recurso con un tipo de **Microsoft.OperationalInsights/workspaces** que representa el área de trabajo OMS.  Este elemento tendrá un subelemento con un tipo de las **vistas** que representa la vista y contiene su configuración detallada.  Se copiar los detalles de este elemento y, a continuación, cópielo en la solución.


## <a name="create-the-view-resource-in-the-solution"></a>Crear el recurso de la vista en la solución
Agregue los siguientes recursos de vista para el elemento de **recursos** de su archivo de solución.  Usa las variables que se describen a continuación que debe agregar.  Tenga en cuenta que las propiedades de **panel** y **OverviewTile** son los marcadores de posición que se sobrescriben con las propiedades correspondientes del archivo de vista exportada.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Agregue las siguientes variables al elemento [variables](operations-management-suite-solutions-creating.md#variables) del archivo de solución y reemplace los valores a los de la solución.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Observe que puede copiar el recurso de toda la vista de su archivo de vista exportada pero necesarias para realizar los cambios siguientes para que funcione en su solución.  

- El **tipo** de recurso de vista debe cambiar de **vistas** a **Microsoft.OperationalInsights/workspaces**.
- La propiedad **nombre** para el recurso de la vista debe modificarse para que incluya el nombre del área de trabajo.
- La dependencia en el área de trabajo debe quitarse desde el recurso de área de trabajo no está definido en la solución.
- Propiedad **DisplayName** debe agregarse a la vista.  **Id**, **nombre**y **DisplayName** deben coincidir con.
- Nombres de parámetro deben cambiarse para que coincida con el conjunto de parámetros necesario.
- Las variables deben definirse en la solución y usadas en las propiedades correspondientes.

## <a name="add-the-view-details"></a>Agregue los detalles de la vista
El recurso de vista en el archivo exportado vista incluirá dos elementos en el elemento de **Propiedades** con el nombre de **panel** y **OverviewTile** que contienen la configuración de la vista detallada.  Copie estos dos elementos y su contenido en el elemento de **Propiedades** del recurso de vista en el archivo de solución. 

## <a name="example"></a>Ejemplo
Por ejemplo, en el ejemplo siguiente muestra un archivo de solución sencilla con una vista.  Puntos suspensivos (...) se muestran para el contenido del **panel** y **OverviewTile** por razones de espacio.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Pasos siguientes

- Obtener detalles completos de la creación de [soluciones de administración](operations-management-suite-solutions-creating.md).
- Incluir [runbooks de automatización en su solución de administración](operations-management-suite-solutions-resources-automation.md).