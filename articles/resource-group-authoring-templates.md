<properties
   pageTitle="Creación de plantillas de administrador de recursos de Azure | Microsoft Azure"
   description="Crear plantillas de administrador de recursos de Azure mediante descriptiva sintaxis JSON para implementar aplicaciones de Azure."
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
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Creación de plantillas de administrador de recursos de Azure

En este tema se describe la estructura de una plantilla de administrador de recursos de Azure. Presenta las distintas secciones de una plantilla y las propiedades que están disponibles en esas secciones. La plantilla se compone de JSON y expresiones que puede usar para generar valores para su implementación. 

Para ver la plantilla para los recursos que ya ha implementado, consulte [exportar una plantilla de Azure Administrador de recursos de los recursos existentes](resource-manager-export-template.md). Para obtener instrucciones sobre cómo crear una plantilla, consulte el [Tutorial de la plantilla de administrador de recursos](resource-manager-template-walkthrough.md). Para obtener recomendaciones sobre la creación de plantillas, vea [procedimientos recomendados para crear plantillas de administrador de recursos de Azure](resource-manager-template-best-practices.md).

Un buen editor JSON puede simplificar la tarea de creación de plantillas. Para obtener información sobre cómo usar Visual Studio con las plantillas, vea [crear e implementar grupos de recursos de Azure a través de Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Para obtener información sobre el uso de código VS, vea [trabajar con plantillas de administrador de recursos de Azure en código de Visual Studio](resource-manager-vs-code.md).

Limitar el tamaño de la plantilla de 1 MB y cada archivo parámetro a 64 KB. El límite de 1 MB se aplica el estado final de la plantilla después de que se ha ampliado con definiciones de recursos iterativo y los valores de variables y parámetros. 

## <a name="template-format"></a>Formato de plantilla

En su estructura más sencillo, una plantilla contiene los elementos siguientes:

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nombre del elemento   | Obligatorio | Descripción
| :------------: | :------: | :----------
| $schema        |   Sí    | Ubicación del archivo de esquema JSON que describe la versión de idioma de la plantilla. Utilice la URL que aparece en el ejemplo anterior.
| contentVersion |   Sí    | Versión de la plantilla (por ejemplo, 1.0.0.0). Puede proporcionar cualquier valor para este elemento. Al implementar recursos con la plantilla, este valor se puede utilizar para asegurarse de que se utiliza la plantilla.
| parámetros     |   No     | Valores que se proporcionan cuando se ejecuta la implementación para personalizar la implementación de recursos.
| variables      |   No     | Valores que se usan como JSON fragmentos en la plantilla para simplificar expresiones de idioma de la plantilla.
| recursos      |   Sí    | Tipos de recursos que se implementa o se actualicen en un grupo de recursos.
| resultados        |   No     | Valores que se devuelven después de la implementación.

Nos examine las secciones de la plantilla con mayor detalle más adelante en este tema. Por ahora, revisaremos algunas de la sintaxis que componen la plantilla.

## <a name="expressions-and-functions"></a>Expresiones y funciones

La sintaxis de la plantilla básica es JSON. Sin embargo, expresiones y funciones amplían el JSON que está disponible en la plantilla. Con expresiones, crear valores que no sean valores literales estrictos. Se incluyen las expresiones entre corchetes [y] y se evaluará cuando se implementa la plantilla. Expresiones pueden aparecer en cualquier lugar de un valor de cadena JSON y siempre devuelven otro valor JSON. Si necesita usar una cadena literal que comienza con un corchete [, debe usar dos corchetes [[.

Normalmente, se utilizar las expresiones con funciones para realizar operaciones para configurar la implementación. Al igual que en JavaScript, llamadas de función tiene el formato **functionName(arg1,arg2,arg3)**. Hacer referencia a propiedades utilizando los operadores punto y [índice].

En el ejemplo siguiente se muestra cómo usar varias funciones al construir valores:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Para obtener la lista completa de las funciones de plantilla, vea [funciones de plantilla de administrador de recursos de Azure](resource-group-template-functions.md). 


## <a name="parameters"></a>Parámetros

En la sección de parámetros de la plantilla, puede especificar qué valores puede introducir al implementar los recursos. Estos valores de parámetro que pueda personalizar la implementación proporcionando valores que están diseñados para un entorno determinado (por ejemplo, desarrollo, prueba y de producción). No tiene que proporcionar parámetros en la plantilla, pero sin parámetros su plantilla siempre debería implementar los mismos recursos con los mismos nombres, ubicaciones y propiedades.

Puede usar estos valores de parámetro a lo largo de la plantilla para establecer los valores de los recursos implementados. Solo los parámetros declarados en la sección parámetros pueden utilizarse en otras secciones de la plantilla.

Definir parámetros con la estructura siguiente:

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Nombre del elemento   | Obligatorio | Descripción
| :------------: | :------: | :----------
| parameterName  |   Sí    | Nombre del parámetro. Debe ser un identificador de JavaScript válido.
| tipo           |   Sí    | Tipo del valor del parámetro. Vea la siguiente lista de tipos permitidos.
| defaultValue   |   No     | Valor predeterminado para el parámetro, si no se proporciona ningún valor para el parámetro.
| allowedValues  |   No     | Matriz de valores permitidos para el parámetro para asegurarse de que se proporciona el valor de la derecha.
| minValue       |   No     | El valor mínimo de parámetros de tipo entero, este valor es ambos inclusive.
| maxValue       |   No     | El valor máximo de parámetros de tipo entero, este valor es ambos inclusive.
| Mínima      |   No     | La longitud mínima de cadena, secureString y parámetros de tipo de matriz, este valor es ambos inclusive.
| maxLength      |   No     | La longitud máxima de cadena, secureString y parámetros de tipo de matriz, este valor es ambos inclusive.
| Descripción    |   No     | Descripción del parámetro, que se muestra a los usuarios de la plantilla a través de la interfaz de portal plantilla personalizada.

Los valores y los tipos permitidos son:

- **cadena**
- **secureString**
- **int**
- **BOOL**
- **objeto** 
- **secureObject**
- **matriz**

Para especificar un parámetro como opcional, proporcione defaultValue (puede ser una cadena vacía). 

Si especifica un nombre de parámetro que coincida con uno de los parámetros en el comando para implementar la plantilla, le pide que proporcione un valor para un parámetro con el sufijo **FromTemplate**. Por ejemplo, si incluye un parámetro denominado **ResourceGroupName** en la plantilla que es realizar el mismo que el parámetro **ResourceGroupName** en el [Nuevo AzureRmResourceGroupDeployment] [ deployment2cmdlet] cmdlet, se le pide que proporcione un valor para **ResourceGroupNameFromTemplate**. En general, debe evitar esta confusión por no nombrar parámetros con el mismo nombre que los parámetros que se utilizan para operaciones de implementación.

>[AZURE.NOTE] Todas las contraseñas, claves y otra información confidencial debe utilizar el tipo de **secureString** . No se pueden leer los parámetros de plantilla con el tipo de secureString después de la implementación de recursos. 

En el ejemplo siguiente se muestra cómo definir parámetros:

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Para saber cómo los valores de parámetro de entrada durante la implementación, vea [implementar una aplicación con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variables

En la sección de variables, construir valores que se pueden utilizar en toda la plantilla. Normalmente, las variables se basan en valores proporcionados de los parámetros. No es necesario definir las variables, pero a menudo simplifican la plantilla reduciendo expresiones complejas.

Definir variables con la estructura siguiente:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

En el ejemplo siguiente se muestra cómo definir una variable que se construye de dos valores de parámetro:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

En el ejemplo siguiente se muestra una variable que es un tipo JSON complejo y variables que se construyen de variables relacionadas:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Recursos

En la sección recursos, se definen los recursos que se implementa o se actualicen. Esta sección puede obtener complicada porque debe comprender los tipos de que implementación para proporcionar los valores correctos. 

Definir los recursos con la estructura siguiente:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nombre del elemento             | Obligatorio | Descripción
| :----------------------: | :------: | :----------
| apiVersion               |   Sí    | Versión de la API de REST de usar para crear el recurso.
| tipo                     |   Sí    | Tipo de recurso. Este valor es una combinación de espacio de nombres del proveedor de recursos y el tipo de recurso (por ejemplo, **Microsoft.Storage/storageAccounts**).
| nombre                     |   Sí    | Nombre del recurso. El nombre debe cumplir las restricciones de componente URI definidas en RFC3986. Además, servicios de Azure que expone el nombre del recurso para fuera de las partes validar el nombre para asegurarse de que no es un intento de suplantación de identidad de otro. Consulte [comprobar el nombre del recurso](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| ubicación                 |   Varía  | Admite geo ubicaciones del recurso proporcionado. Puede seleccionar cualquiera de las ubicaciones disponibles, pero normalmente tiene sentido elegir uno que sea similar a los usuarios. Normalmente, también tiene sentido colocar recursos que interactúan entre sí en la misma región. La mayoría de los tipos de recursos requieren una ubicación, pero algunos tipos (por ejemplo, una asignación de roles), no se requiere una ubicación.
| etiquetas                     |   No     | Etiquetas que están asociadas al recurso.
| comentarios                 |   No     | Las notas para documentar los recursos en la plantilla
| dependsOn                |   No     | Recursos que depende del recurso que se ha definido. Las dependencias entre los recursos se evalúan y se implementan los recursos en su orden dependiente. Cuando no hay recursos dependientes entre sí, que se implementen en paralelo. El valor puede ser una lista de valores separados por comas de un recurso nombres o identificadores únicos de recursos.
| propiedades               |   No     | Opciones de configuración específicas de recursos. Los valores de las propiedades están la misma que los valores que proporciona en el cuerpo de la solicitud para que la operación de la API de REST (método SUPERPONER) crear el recurso. Para obtener vínculos a la documentación del esquema de recurso o la API de REST, consulte [proveedores de administrador de recursos, regiones, versiones API y esquemas](resource-manager-supported-services.md).
| copiar                     |   No     | Si hay más de una instancia, el número de recursos que se va a crear. Para obtener más información, vea [crear varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md). |
| recursos                |   No     | Recursos secundarios que dependen del recurso que se ha definido. Puede proporcionar los tipos de recursos que se permiten en el esquema de recurso de primario. El nombre completo del tipo de recurso secundario incluye el tipo de recurso primario, como **Microsoft.Web/sites/extensions**. Dependencia en el recurso primario no implica; debe definir explícitamente esa dependencia. 

Saber qué valores debe para especificar para **apiVersion**, **tipo**y la **ubicación** no es obvio. Afortunadamente, puede determinar estos valores a través de Azure PowerShell o CLI de Azure.

Para obtener todos los proveedores de recursos con **PowerShell**, use:

    Get-AzureRmResourceProvider -ListAvailable

En la lista devuelta, busque los proveedores de recursos que le interesa. Para obtener los tipos de recursos para un proveedor de recursos (por ejemplo, almacenamiento), use:

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

Para obtener las versiones de la API para un tipo de recurso (por ejemplo cuentas de almacenamiento), use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

Para obtener las ubicaciones compatibles para un tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

Para obtener todos los proveedores de recursos con **Azure CLI**, use:

    azure provider list

En la lista devuelta, busque los proveedores de recursos que le interesa. Para obtener los tipos de recursos para un proveedor de recursos (por ejemplo, almacenamiento), use:

    azure provider show Microsoft.Storage

Para obtener ubicaciones compatibles y las versiones API, use:

    azure provider show Microsoft.Storage --details --json

Para obtener más información acerca de los proveedores de recursos, consulte [proveedores de administrador de recursos, regiones, versiones API y esquemas](resource-manager-supported-services.md).

La sección de recursos contiene una matriz de los recursos para implementar. Dentro de cada recurso, también puede definir una matriz de recursos secundarios. Por lo tanto, la sección de recursos podría tener una estructura como:

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


En el ejemplo siguiente se muestra un recurso de **Microsoft.Web/serverfarms** y **Microsoft.Web/sites** con un recurso de **extensiones** secundario. Observe que el sitio está marcado como dependiente de la granja de servidores desde la granja de servidores debe existir antes de que se puede implementar el sitio. Observe también que el recurso de **extensiones** es un elemento secundario del sitio.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Resultados

En la sección de resultados, especifique los valores que se devuelven de implementación. Por ejemplo, podría devolver la URI para obtener acceso a un recurso implementado.

En el ejemplo siguiente se muestra la estructura de una definición de salida:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nombre del elemento   | Obligatorio | Descripción
| :------------: | :------: | :----------
| outputName     |   Sí    | Nombre del valor de salida. Debe ser un identificador de JavaScript válido.
| tipo           |   Sí    | Tipo de valor de salida. Los valores de salida admiten los mismos tipos de parámetros de entrada de plantilla.
| valor          |   Sí    | Expresión de idioma de la plantilla que se evalúa y devuelto como valor de salida.


En el ejemplo siguiente se muestra un valor que se devuelve en la sección de resultados.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Para obtener más información sobre cómo trabajar con los resultados, ver el [estado de uso compartido en plantillas de administrador de recursos de Azure](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Pasos siguientes
- Para ver plantillas completadas para muchos tipos distintos de soluciones, consulte las [Plantillas de Azure tutorial rápido](https://azure.microsoft.com/documentation/templates/).
- Para obtener más información sobre las funciones que puede utilizar desde dentro de una plantilla, vea [Funciones de plantilla de administrador de recursos de Azure](resource-group-template-functions.md).
- Para combinar varias plantillas durante la implementación, consulte [uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).
- Debe utilizar recursos que hay dentro de otro grupo de recursos. Este escenario es habitual cuando se trabaja con cuentas de almacenamiento o redes virtuales que comparten varios grupos de recursos. Para obtener más información, vea la [función resourceId](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
