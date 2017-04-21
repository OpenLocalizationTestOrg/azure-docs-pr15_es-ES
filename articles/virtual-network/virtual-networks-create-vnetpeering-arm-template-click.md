<properties
   pageTitle="Crear VNet interconexión con plantillas de administrador de recursos | Microsoft Azure"
   description="Obtenga información sobre cómo crear una red virtual interconexión con las plantillas en el Administrador de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Crear VNet interconexión con plantillas de administrador de recursos

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para crear un VNet interconexión con las plantillas de administrador de recursos, siga los pasos siguientes:

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.

    > [AZURE.NOTE] El cmdlet de PowerShell para administrar VNet interconexión se incluye con [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. El texto siguiente muestra la definición de un vínculo de interconexión VNet para VNet1 a VNet2, según el escenario anterior. Copie el siguiente contenido y guardarlo en un archivo llamado VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. La sección siguiente muestra la definición de un vínculo de interconexión VNet para VNet2 a VNet1, según el escenario anterior.  Copie el siguiente contenido y guardarlo en un archivo llamado VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Como se ve en la plantilla anterior, hay algunas propiedades configurables de interconexión VNet:

  	|Opción|Descripción|Predeterminado|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Si es o no el espacio de dirección de un punto VNet incluye como parte de la etiqueta virtual_network.|Sí|
  	|AllowForwardedTraffic|Si el tráfico no origina una VNet peered se aceptan o se descarta.|No|
  	|AllowGatewayTransit|Permite al interlocutor VNet utilizar la puerta de enlace VNet.|No|
  	|UseRemoteGateways|Use la puerta de enlace de su punto VNet. El punto VNet debe tener una puerta de enlace configurado y AllowGatewayTransit seleccionado. No puede usar esta opción si ha configurado una puerta de enlace.|No|

    Cada vínculo de interconexión VNet tiene el conjunto de propiedades anteriores. Por ejemplo, puede establecer AllowVirtualNetworkAccess en True para el vínculo de interconexión VNet VNet1 a VNet2 y establecer en False para el vínculo de interconexión VNet en la otra dirección.


4. Para implementar el archivo de plantilla, puede ejecutar el cmdlet AzureRmResourceGroupDeployment de nuevo para crear o actualizar la implementación. Para obtener más información sobre el uso de plantillas de administrador de recursos, consulte este [artículo](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Reemplace el archivo de plantilla y de nombre de grupo de recursos según corresponda.

    A continuación se muestra un ejemplo según el escenario anterior:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Muestra el resultado:

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Muestra el resultado:

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Una vez finalizada la implementación, puede ejecutar el cmdlet siguiente para ver el estado de interconexión:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Muestra el resultado:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Una vez establecida la interconexión en este escenario, debería poder iniciar las conexiones de cualquier máquina virtual a cualquier máquina virtual en ambos VNets. De forma predeterminada, AllowVirtualNetworkAccess es verdadero y VNet interconexión se aprovisionar las ACL adecuadas para permitir la comunicación entre VNets. Puede aplicar reglas de grupo (GSN) de seguridad de red para bloquear la conectividad entre subredes específicas o máquinas virtuales para tener un control detallado de acceso entre dos redes virtuales.  Para obtener más información de creación de reglas de GSN, consulte este [artículo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para crear un VNet interconexión entre suscripciones, siga los pasos siguientes:

1. Inicie sesión en Azure con privilegios un usuario de la cuenta de suscripción A y ejecute el siguiente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Esto no es necesario, se puede establecer interconexión incluso si los usuarios individualmente elevar interconexión solicitudes para sus respectivos Vnets como que coincida con las solicitudes. Agregar un usuario con privilegios de lo otro VNet como usuarios en el VNet local facilita la instalación.

2. Inicie sesión en Azure con la cuenta con privilegios-del usuario B para suscripción B y ejecute el siguiente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. En el usuario A está inicio de sesión, ejecute este cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Le mostramos cómo se define el archivo JSON.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. En la sesión de inicio de sesión del usuario-B, ejecute el siguiente cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Le mostramos cómo se define el archivo JSON:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Una vez establecida la interconexión en este escenario, debería poder iniciar las conexiones de cualquier máquina virtual a cualquier máquina virtual de ambos VNets entre varias suscripciones.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. En este escenario, puede implementar la plantilla de ejemplo siguiente para establecer la interconexión VNet.  Tendrá que establecer la propiedad AllowForwardedTraffic en True, lo que permite el dispositivo de red virtual en el VNet peered para enviar y recibir tráfico.

    Aquí es la plantilla para crear un VNet interconexión de HubVNet a VNet1. Tenga en cuenta que AllowForwardedTraffic se establece en falso.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Aquí es la plantilla para crear un VNet interconexión de VNet1 a HubVnet. Tenga en cuenta que AllowForwardedTraffic está establecida en true.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Una vez establecida la interconexión, puede hacer referencia a este [artículo](virtual-network-create-udr-arm-ps.md) para definir routes(UDR) definidas por el usuario para redirigir el tráfico de VNet1 a través de un equipo virtual para utilizar su capacidad. Al especificar la dirección de salto siguiente en la ruta, puede establecer a la dirección IP del dispositivo virtual en el punto VNet HubVNet.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para crear una interconexión entre redes virtuales de diferentes modelos de implementación, siga los pasos siguientes:
1. El texto siguiente muestra la definición de un vínculo de interconexión VNet para VNET1 a VNET2 en este escenario. Solo vínculo es necesario para mismo nivel de una red virtual clásica a una red virtual del Administrador de recursos de Azure.

    Asegúrese de poner en su identificador de suscripción para donde se encuentra la red virtual clásica o VNET2 y cambiar MyResouceGroup el nombre de grupo de recursos adecuado.

    {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parámetros": {}, "variables": {}, "recursos": [{"apiVersion": "2016-06-01", "tipo": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings", "nombre": "VNET1/LinkToVNET2", "ubicación": "[() resourceGroup .location]", "propiedades": {"allowVirtualNetworkAccess": true, "allowForwardedTraffic": false, "allowGatewayTransit": false, "useRemoteGateways": false, "remoteVirtualNetwork": {"id": "[resourceId (' Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"}}}]}

2. Para implementar el archivo de plantilla, ejecute el cmdlet siguiente para crear o actualizar la implementación.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Cuando se complete correctamente la implementación, puede ejecutar el siguiente cmdlet para ver el estado de interconexión:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Una vez establecida la interconexión entre un VNet clásica y un administrador de recursos VNet, debería poder iniciar conexiones desde cualquier máquina virtual en VNET1 a cualquier máquina virtual en VNET2 y viceversa.
