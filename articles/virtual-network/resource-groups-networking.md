<properties
   pageTitle="Información general del proveedor de recursos de red | Microsoft Azure"
   description="Obtenga más información sobre el nuevo proveedor de recursos de red en el Administrador de recursos de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Proveedor de recursos de red
Una necesidad de respaldo de éxito del negocio de hoy, es la capacidad para crear y administrar aplicaciones de red de gran escala en una forma ágil, flexible, segura y predecible. Azure Resource Manager (ARM) le permite crear aplicaciones, como un conjunto único de recursos en grupos de recursos. Estos recursos se administran a través de varios proveedores de recursos en el BRAZO.

Administrador de recursos de Azure se basa en proveedores de recursos diferente para proporcionar acceso a los recursos. Hay tres proveedores de recursos principal: red, almacenamiento y cálculo. Este documento trata las características y ventajas del proveedor del recurso de red, incluidos:

- **Metadatos** , puede agregar información a los recursos con etiquetas. Estas etiquetas pueden utilizarse para realizar un seguimiento de uso de los recursos en todas las suscripciones y grupos de recursos.
- **Mayor control de su red** - acoplados de recursos de red y puede controlar ellos de forma más detallada. Esto significa que tiene una mayor flexibilidad en la administración de los recursos de red.
- **Configuración rápida** - porque no se corresponden estrictamente recursos de red, puede crear y organizar los recursos de la red en paralelo. Esto ha reducido considerablemente el tiempo de configuración.
- **Control de acceso en función de funciones** - RBAC proporciona roles predeterminados, con el ámbito de seguridad específico, además de permitir la creación de funciones personalizadas para la administración de seguridad.
- **Más fácil implementación y administración** , es más fácil implementar y administrar aplicaciones, ya que puede crear una pila de toda la aplicación como un conjunto único de recursos en un grupo de recursos. Y más fácil de implementar, ya que puede implementar simplemente proporcionando una carga JSON de plantilla.
- **Personalización rápida** : puede usar plantillas de estilo descriptiva para permitir la personalización predecible y rápida de implementaciones.
- **Personalización de repetición** : puede usar plantillas de estilo descriptiva para permitir la personalización predecible y rápida de implementaciones.
- **Interfaces de administración** - puede usar cualquiera de las siguientes interfaces para administrar los recursos:
    - RESTO según API
    - PowerShell
    - .NET SDK
    - Node.JS SDK
    - Java SDK
    - CLI de Azure
    - Portal de vista previa
    - Idioma de la plantilla ARM

## <a name="network-resources"></a>Recursos de red
Ahora pueden administrar recursos de la red de forma independiente, en lugar de tener todas ellas administrados a través de un recurso de cálculo único (una máquina virtual). Así se garantiza que un alto grado de flexibilidad y ágiles redactar una infraestructura compleja y gran escala en un grupo de recursos.

Vista conceptual de una implementación de ejemplo relacionadas con una aplicación de varios niveles se muestra a continuación. Cada recurso que ve, como NIC, las direcciones IP públicas y VM, se puede administrar por separado.

![Modelo de recursos de red](./media/resource-groups-networking/Figure2.png)

Todos los recursos contiene un conjunto común de propiedades y establezca sus propiedades individuales. Las propiedades comunes son:

|(Propiedad)|Descripción|Valores de ejemplo|
|---|---|---|
|**nombre**|Nombre de recurso único. Cada tipo de recurso tiene sus propio restricciones de nomenclatura.|PIP01, VM01, NIC01|
|**ubicación**|Azure región donde reside el recurso|westus, eastus|
|**Id.**|Identificación de URI según única|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Puede comprobar las propiedades individuales de los recursos en las secciones siguientes.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de administración
Puede administrar los recursos de red Azure con interfaces diferentes. En este documento nos centraremos en cable de esas interfaces: API de REST y plantillas.

### <a name="rest-api"></a>API DE REST
Como se mencionó anteriormente, se pueden administrar recursos de la red a través de una gran variedad de interfaces, incluidas las API de REST, .NET SDK, Node.JS SDK, SDK de Java, PowerShell, CLI, Portal de Azure y plantillas.

La API de Rest se ajustan a la especificación de protocolo HTTP 1.1. La estructura URI general de la API se muestra a continuación:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Y los parámetros de llaves representar los elementos siguientes:

- **Id. de suscripción** : el identificador de suscripción de Azure.
- **nombres de proveedor de recursos** - espacio de nombres para el proveedor que utiliza. El valor para el proveedor de recursos de red es *Microsoft.Network*.
- **nombre de la región** : el nombre de la región de Azure

Se admiten los siguientes métodos HTTP al realizar llamadas a la API de REST:

- **Colocar** - utilizados para crear un recurso de un tipo determinado, modificar una propiedad de recursos o cambiar una asociación entre los recursos.
- **GET** - usada para recuperar información de un recurso de preparación.
- **Eliminar** : para eliminar un recurso existente.

La solicitud y la respuesta se ajustan a un formato de carga JSON. Para obtener más detalles, vea [Las API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>Idioma de la plantilla ARM
Además de administrar recursos imperativamente (a través de las API o SDK), también puede usar un estilo de programación descriptiva para crear y administrar recursos de red mediante el lenguaje de la plantilla de ARM.

Una representación de ejemplo de una plantilla se proporciona a continuación:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

La plantilla es principalmente una descripción de JSON de los recursos y los valores de instancia insertados a través de parámetros. El ejemplo siguiente se puede usar para crear una red virtual con 2 subredes.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Tiene la opción de proporcionar los valores de parámetro manualmente cuando se usa una plantilla o puede usar un archivo de parámetros. El ejemplo siguiente muestra un conjunto de valores de parámetro para utilizar con la plantilla anterior posibles:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Son las principales ventajas del uso de plantillas:

- Puede crear una infraestructura compleja de un grupo de recursos en un estilo descriptiva. ARM controla la orquestación de creación de los recursos, incluida la administración de dependencia.
- La infraestructura puede crear de forma reproducible entre distintas regiones y dentro de una región cambiando simplemente parámetros.
- El estilo descriptiva conduce al más corto plazo de creación de las plantillas y distribuir la infraestructura.

Para plantillas de ejemplo, vea [Tutorial rápido Azure plantillas](https://github.com/Azure/azure-quickstart-templates).

Para obtener más información sobre el idioma de la plantilla de ARM, consulte [Lenguaje de plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md).

La plantilla de ejemplo anterior usa la red virtual y los recursos de la subred. Hay otros recursos de red que puede utilizar como se indica a continuación:

### <a name="using-a-template"></a>Con una plantilla

Puede implementar servicios en Azure desde una plantilla con PowerShell, AzureCLI, o bien realizando un clic para implementar desde GitHub. Para implementar los servicios de una plantilla en GitHub, ejecute los siguientes pasos:

1. Abra el archivo template3 desde GitHub. Por ejemplo, abra [red Virtual con dos subredes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Haga clic en **implementar a Azure**y, a continuación, iniciar sesión en el portal de Azure con sus credenciales.
3. Compruebe la plantilla y, a continuación, haga clic en **Guardar**.
4. Haga clic en **Editar parámetros** y seleccione una ubicación, como *Oeste de Estados Unidos*, para los vnet y subredes.
5. Si es necesario, cambie los parámetros **ADDRESSPREFIX** y **SUBNETPREFIX** y, a continuación, haga clic en **Aceptar**.
6. Haga clic en **Seleccionar un grupo de recursos** y, a continuación, haga clic en el grupo de recursos que desea agregar la vnet y subredes a. Como alternativa, puede crear un nuevo grupo de recursos haciendo clic en **o crear nuevos**.
3. Haga clic en **crear**. Observe el mosaico que muestra la **implementación de la plantilla de aprovisionamiento**. Una vez realizada la implementación, verá una pantalla similar a la siguiente.

![Ejemplo de implementación de plantilla](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Pasos siguientes

[Lenguaje de la plantilla de administrador de recursos de Azure](../resource-group-authoring-templates.md)

[Redes de Azure: plantillas que se usan normalmente](https://github.com/Azure/azure-quickstart-templates)

[Calcular el proveedor de recursos](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Información general de administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md)
