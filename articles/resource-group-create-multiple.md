<properties
   pageTitle="Implementar varias instancias de recursos | Microsoft Azure"
   description="Utilizar la operación de copia y las matrices en una plantilla de administrador de recursos de Azure para repetir varias veces al implementar recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Crear varias instancias de recursos en el Administrador de recursos de Azure

En este tema se muestra cómo trabajemos en la plantilla de administrador de recursos de Azure para crear varias instancias de un recurso.

## <a name="copy-copyindex-and-length"></a>copiar, copyIndex y longitud

Dentro de los recursos para crear varias veces, puede definir un objeto de **copia** que especifica el número de veces que repetir. La copia tiene el siguiente formato:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Puede tener acceso el valor actual de iteración con la función **copyIndex()** , como se muestra a continuación, dentro de la función de concatenación.

    [concat('examplecopy-', copyIndex())]

Al crear varios recursos desde una matriz de valores, puede usar la función **longitud** para especificar el recuento. Proporcionar la matriz como parámetro de la función de longitud.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Use el valor de índice en el nombre

Puede usar la copia operación crear varias instancias de un recurso que se denominan de forma exclusiva basada en el índice incremental. Por ejemplo, que desea agregar un número hasta el final de cada nombre de recurso que se implementa. Para implementar los tres sitios de web con el nombre:

- examplecopy 0
- examplecopy-1
- examplecopy-2.

Utilice la siguiente plantilla:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Valor de índice de desplazamiento

Observará en el ejemplo anterior que el valor de índice que va desde cero a 2. Para desviar el valor de índice, puede pasar un valor de la función **copyIndex()** , como **copyIndex(1)**. El número de iteraciones para realizar sigue especificado en el elemento de copia, pero el valor de copyIndex se desplaza por el valor especificado. Por lo tanto, usando la misma plantilla que el ejemplo anterior, pero que especifica **copyIndex(1)** ¿implementar tres sitios de web con el nombre:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Utilizar copia con matriz
   
La operación de copia es especialmente útil cuando se trabaja con matrices porque puede recorrer cada elemento de la matriz. Para implementar los tres sitios de web con el nombre:

- Contoso examplecopy
- examplecopy Fabrikam
- examplecopy Coho

Utilice la siguiente plantilla:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Por supuesto, establezca el número de copias en un valor distinto de la longitud de la matriz. Por ejemplo, podría crear una matriz con muchos valores y, a continuación, pase un valor de parámetro especifica cuántos elementos de la matriz a implementar. En ese caso, Establece el número de copias tal como se muestra en el primer ejemplo. 

## <a name="depending-on-resources-in-a-loop"></a>En función de los recursos en un bucle

Puede especificar que un recurso se implementen después de otro recurso mediante el elemento **dependsOn** . Cuando necesite implementar un recurso que depende de la colección de recursos en un bucle, puede usar proporcionar el nombre del bucle copia en el elemento **dependsOn** . En el ejemplo siguiente se muestra cómo implementar 3 cuentas de almacenamiento antes de implementar la máquina Virtual. No se muestra la definición de máquina Virtual completa. Observe que el elemento de la copia tiene **nombre** establecido en **storagecopy** y el elemento **dependsOn** para las máquinas virtuales también se establece en **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>Bucle en un recurso anidado

No puede usar un bucle de copia de un recurso anidado. Si necesita crear varias instancias de un recurso que se define normalmente como anidado dentro de otro recurso, en su lugar debe crear el recurso como un recurso de nivel superior y definir la relación con el recurso primario a través de las propiedades de **tipo** y el **nombre** .

Por ejemplo, supongamos que normalmente definen un conjunto de datos como un recurso anidado dentro de un generador de datos.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Para crear varias instancias de conjuntos de datos, necesarias para cambiar la plantilla como se muestra a continuación. Observe el tipo de calificado completo y el nombre incluye el nombre del generador de datos.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Crear varias instancias cuando copia no funcionan

Solo puede usar **Copiar** en tipos de recursos, no en propiedades dentro de un tipo de recurso. Puede causar problemas para usted cuando desee crear varias instancias de algo que forma parte de un recurso. Un escenario común es crear varios discos de datos para una máquina Virtual. No puede utilizar **Copiar** con los discos de datos porque **dataDisks** es una propiedad en la máquina Virtual, no su propio tipo de recurso. En su lugar, se crea una matriz con tantos discos de datos como se necesite y pase el número real de discos de datos para crear. En la definición de máquina virtual use la función **tomar** obtener solo el número de elementos que desea que realmente de la matriz.

Un ejemplo completo de este modelo es mostrar en la plantilla para [crear una máquina virtual con una selección dinámica de discos de datos](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

A continuación, se muestran las secciones correspondientes de la plantilla de implementación. Una gran cantidad de la plantilla se ha quitado para resaltar las secciones implicados en la creación dinámica de un número de discos de datos. Observe el parámetro **numDataDisks** que le permite pasar el número de discos para crear. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Pasos siguientes
- Si desea obtener información acerca de las secciones de una plantilla, vea [Crear plantillas de administrador de recursos de Azure](./resource-group-authoring-templates.md).
- Para todas las funciones que puede usar en una plantilla, vea [Funciones de plantilla de administrador de recursos de Azure](./resource-group-template-functions.md).
- Para obtener información sobre cómo implementar la plantilla, vea [implementar una aplicación con la plantilla de administrador de recursos de Azure](resource-group-template-deploy.md).
