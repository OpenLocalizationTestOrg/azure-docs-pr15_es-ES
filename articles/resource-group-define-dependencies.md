<properties
   pageTitle="Dependencias en las plantillas de administrador de recursos | Microsoft Azure"
   description="Describe cómo establecer un recurso como depende del otro recurso durante la implementación para asegurarse de recursos en el orden correcto."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Definir las dependencias en las plantillas de administrador de recursos de Azure

Para un recurso determinado, puede haber otros recursos que deben existir antes de que se implementa el recurso. Por ejemplo, un servidor SQL server debe existir antes de implementar una base de datos SQL. Definir esta relación marcando un recurso como dependiente del otro recurso. Normalmente, defina una dependencia con el elemento **dependsOn** , pero también se pueden definir a través de la función de **referencia** . 

Administrador de recursos evalúa las dependencias entre los recursos y las instala en su orden dependiente. Cuando no hay recursos dependientes entre sí, el Administrador de recursos los implementa en paralelo.

## <a name="dependson"></a>dependsOn

Dentro de la plantilla, el elemento dependsOn le permite definir un recurso como dependiente en uno o más recursos. Su valor puede ser una lista de valores separados por comas de nombres de los recursos. 

En el ejemplo siguiente se muestra un conjunto de escala de la máquina virtual que depende de un equilibrador de carga, una red virtual y un bucle que crea varias cuentas de almacenamiento. Estos otros recursos no se muestran en el ejemplo siguiente, pero tienen que existen en otra ubicación en la plantilla.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Para definir una dependencia entre un recurso y los recursos que se crean a través de un bucle de copia, establezca el elemento de dependsOn en nombre del bucle. Para obtener un ejemplo, vea [crear varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

Mientras que se puede inclinada utilizar dependsOn para asignar relaciones entre los recursos, es importante entender por qué está haciendo debido a puede afectar al rendimiento de la implementación. Por ejemplo, para documentar cómo están interconectados recursos, dependsOn no es el enfoque correcto. No se puede consultar los recursos que se han definido en el elemento de dependsOn después de la implementación. Usando dependsOn, potencialmente afectar el tiempo de implementación porque el Administrador de recursos no se implementa en paralelo dos recursos que tienen una dependencia. Para documentar las relaciones entre los recursos, use en su lugar la [vinculación de recursos](resource-group-link-resources.md).

## <a name="child-resources"></a>Recursos secundarios

La propiedad de recursos permite especificar recursos secundarios relacionados con el recurso que se ha definido. Recursos secundarios sólo pueden ser definidos cinco niveles. Es importante que tenga en cuenta que no se crea una dependencia implícita entre un recurso secundario y el recurso principal. Si necesita el recurso secundario implementar después del recurso primario, debe indicar explícitamente esa dependencia con la propiedad dependsOn. 

Cada recurso primario acepta sólo determinados tipos de recursos como recursos secundarios. Los tipos de recursos aceptados se especifican en el [esquema de la plantilla](https://github.com/Azure/azure-resource-manager-schemas) del recurso principal. El nombre del tipo de recurso secundario incluye el nombre del tipo de recurso primario, como **Microsoft.Web/sites/config** y **Microsoft.Web/sites/extensions** son ambos recursos secundario de la **Microsoft.Web/sites**.

En el ejemplo siguiente se muestra un SQL server y la base de datos SQL. Observe que se ha definido una dependencia explícita entre la base de datos SQL y SQL server, aunque la base de datos es un elemento secundario del servidor.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>función de referencia

La [función referencia](resource-group-template-functions.md#reference) permite una expresión para obtener su valor de otro nombre de JSON y pares de valor y recursos de tiempo de ejecución. Expresiones de referencia implícitamente declaran que un recurso depende de otro. 

    reference('resourceName').propertyPath

Puede usar este elemento o el elemento dependsOn para especificar las dependencias, pero no es necesario utilizar ambos para el mismo recurso dependiente. Siempre que sea posible, use una referencia implícita para evitar agregar una dependencia innecesaria de forma accidental.

Para obtener más información, vea la [función de referencia](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo crear plantillas de administrador de recursos de Azure, consulte [plantillas de edición](resource-group-authoring-templates.md). 
- Para obtener una lista de las funciones disponibles en una plantilla, vea [funciones de plantilla](resource-group-template-functions.md).

