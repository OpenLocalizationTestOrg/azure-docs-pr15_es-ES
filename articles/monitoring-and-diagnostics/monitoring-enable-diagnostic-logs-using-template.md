<properties
    pageTitle="Habilitar automáticamente la configuración de diagnóstico usando una plantilla de administrador de recursos | Microsoft Azure"
    description="Obtenga información sobre cómo usar una plantilla de administrador de recursos para crear una configuración de diagnóstico que le permitirán transmita los registros de diagnóstico para Hubs evento o guardarlos en una cuenta de almacenamiento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Habilitar automáticamente la configuración de diagnóstico durante la creación de recursos con una plantilla de administrador de recursos
En este artículo le mostraremos cómo puede usar una [plantilla de administrador de recursos de Azure](../resource-group-authoring-templates.md) para configurar opciones de diagnóstico en un recurso cuando se crea. Esto le permite iniciar automáticamente la transmisión de los registros de diagnóstico y métrica de evento Hubs, archivarlos en una cuenta de almacenamiento o enviarlos al análisis de registro cuando se crea un recurso.

El método para habilitar los registros de diagnóstico usando una plantilla de administrador de recursos depende del tipo de recurso.

- Recursos de **Cálculo no** (por ejemplo, grupos de seguridad de red, lógica de aplicaciones, automatización) usar [configuración de diagnóstico descritas en este artículo](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Calcular** Recursos (TORUNDA/LAD basada) use el [archivo de configuración de TORUNDA/LAD descrita en este artículo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

En este artículo se describe cómo configurar con cualquiera de los métodos de diagnóstico.

Los pasos básicos son los siguientes:

1. Crear una plantilla como un archivo JSON que describe cómo crear el recurso y habilitar diagnósticos.
2. [Implementar la plantilla mediante el método de implementación](../resource-group-template-deploy.md).

A continuación le ofrecemos un ejemplo de archivo JSON plantilla que necesita para generar para no calcular y recursos de cálculo.

## <a name="non-compute-resource-template"></a>Plantilla de recursos sin cálculo
Para los recursos de cálculo no, debe hacer dos cosas:

1. Agregar parámetros a la blobs de parámetros para el nombre de la cuenta de almacenamiento, el identificador de regla de bus de servicio o el Id. de área de trabajo de OMS registro análisis (habilitar archivado de registros de diagnóstico en una cuenta de almacenamiento, la transmisión de registros de eventos Hubs o enviar registros a análisis de registro).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. En la matriz de recursos del recurso para el que desea habilitar registros de diagnóstico, agregue un recurso de tipo `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

El blob de propiedades para la configuración de diagnóstico sigue [el formato que se describe en este artículo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Este es un ejemplo completo que crea un grupo de seguridad de la red y se convierte en transmisión Hubs de evento y almacenamiento en una cuenta de almacenamiento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Calcular la plantilla de recursos
Para habilitar diagnósticos en un recurso de cálculo, por ejemplo un clúster de máquina Virtual o tela de servicio, debe:

1. Agregar la extensión de diagnósticos de Azure a la definición de recursos de la máquina virtual.
2. Especifique un concentrador de cuenta o evento de almacenamiento como un parámetro.
3. Agregue el contenido del archivo XML de WADCfg en la propiedad XMLCfg, escape todos los caracteres XML correctamente.

> [AZURE.WARNING] Este último paso puede ser difícil obtener derecha. [Consulte este artículo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) para obtener un ejemplo que se divide el esquema de configuración de diagnósticos en variables de escape y el formato correcto.

Es el proceso completo, incluidos los ejemplos, se describe [en este documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Pasos siguientes
- [Obtenga más información acerca de los registros de diagnóstico de Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Transmita Azure registros de diagnóstico para Hubs de evento](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
