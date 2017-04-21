<properties 
   pageTitle="Solucionar problemas de grupos de seguridad de red - PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar problemas de los grupos de seguridad de red en el modelo de implementación de administrador de recursos de Azure con PowerShell de Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Solucionar problemas de los grupos de seguridad de red con PowerShell de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Si configura grupos de seguridad de red (NSGs) en la máquina virtual (VM) y está experimentando problemas de conectividad VM, este artículo proporciona una descripción general de las capacidades de diagnóstico para NSGs para ayudar a solucionar aún más.

NSGs le permiten controlar los tipos de tráfico que fluyen dentro y fuera de sus máquinas virtuales (VM). NSGs se puede aplicar a subredes en una red Virtual de Azure (VNet), interfaces de red (NIC) o ambas. Las reglas efectivo aplicadas a una NIC son una agregación de las reglas que existen en el NSGs aplicados a una NIC y la subred a que está conectado. Reglas en estos NSGs a veces pueden entran en conflicto con otros y afectar a la conectividad de red de la VM.  

Puede ver todas las reglas de seguridad eficaz desde su NSGs, tal como se aplica en NIC de la máquina virtual. Este artículo le muestra cómo solucionar problemas de conectividad VM con estas reglas en el modelo de implementación de administrador de recursos de Azure. Si no está familiarizado con los conceptos de VNet y GSN, lea los artículos de información general [de red Virtual](virtual-networks-overview.md) y [los grupos de seguridad de red](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Uso de reglas de seguridad eficaces para solucionar problemas de flujo de tráfico VM

El siguiente escenario es un ejemplo de un problema de conexión comunes:

Una máquina virtual denominada *VM1* forma parte de una subred denominada *subred1* dentro de un VNet denominado *WestUS VNet1*. Se produce un error en un intento de conectar con la máquina virtual con RDP sobre el puerto TCP 3389. NSGs se aplican a la NIC *VM1 NIC1* y la subred *subred1*. El tráfico al puerto TCP 3389 está permitido en la GSN asociado a la interfaz de red *VM1 NIC1*, pero TCP ping a correctamente de puerto 3389 de VM1.

Mientras este ejemplo usa el puerto TCP 3389, los siguientes pasos pueden usarse para determinar errores de conexión entrante y saliente sobre cualquier puerto.

## <a name="detailed-troubleshooting-steps"></a>Detalla los pasos de solución de problemas
Complete los pasos siguientes para solucionar problemas de una máquina virtual NSGs:

1. Iniciar una sesión PowerShell Azure e iniciar sesión en Azure. Si no está familiarizado con el uso de PowerShell de Azure, lea el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

2. Escriba el comando siguiente para devolver todas las reglas de GSN aplicadas a una NIC denominada *VM1 NIC1* en el grupo de recursos *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Si no conoce el nombre de un NIC, escriba el comando siguiente para recuperar los nombres de todas las NIC en un grupo de recursos: 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    El texto siguiente es un ejemplo de la salida de reglas eficaces devuelve para *VM1 NIC1* NIC:

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Tenga en cuenta la siguiente información en el resultado:

    - Hay dos secciones **NetworkSecurityGroup** : uno está asociado a una subred (*subred1*) y uno está asociado a una NIC (*VM1 NIC1*). En este ejemplo, se ha aplicado un GSN a cada uno.
    - **Asociación** muestra el recurso (subred o NIC) un determinado GSN está asociado. Si el recurso GSN se mueve o elimina la asociación inmediatamente antes de ejecutar este comando, tendrá que esperar unos segundos para que el cambio para reflejar en el comando. 
    - Los nombres de regla que están precedidos por *defaultSecurityRules*: GSN un cuando se crea, se crean varias reglas de seguridad predeterminados dentro de ella. No se puede quitar las reglas de forma predeterminada, pero se pueden sobrescribir con reglas mayor prioridad.
     Lea el artículo [Introducción a GSN](virtual-networks-nsg.md#default-rules) para obtener más información acerca de las reglas de seguridad de GSN predeterminado.
    - **ExpandedAddressPrefix** se expande los prefijos de dirección GSN predeterminada etiquetas. Etiquetas representan varios prefijos de direcciones. Expansión de las etiquetas puede ser útil al solucionar problemas de conectividad de máquina virtual de prefijos de dirección específica. Por ejemplo, con VNET interconexión, etiqueta VIRTUAL_NETWORK se expande para mostrar prefijos de VNet peered en el resultado anterior.

        >[AZURE.NOTE] El comando solo muestra eficaces reglas si un GSN está asociado con una subred, una NIC o ambos. Una máquina virtual puede tener varias NIC con diferentes NSGs aplicados. Para solucionar el problema, ejecute el comando para cada NIC.
        
3. Para facilitar el filtrado de un número mayor de reglas GSN, escriba los comandos siguientes para solucionar aún más: 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Aplicar un filtro para el tráfico RDP (puerto TCP 3389) a la vista de cuadrícula, tal como se muestra en la siguiente imagen:

    ![Lista de reglas](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Como puede ver en la vista de cuadrícula, hay conceder y denegar reglas para RDP. El resultado del paso 2 muestra que la regla de *DenyRDP* se encuentra en la GSN aplicado a la subred. Las reglas de entrada, NSGs aplicados a la subred se procesan en primer lugar. Si se encuentra una coincidencia, no se procesa la GSN aplicado a la interfaz de red. En este caso, la regla de *DenyRDP* desde la subred bloquea RDP VM (**VM1**).

    >[AZURE.NOTE] Una máquina virtual puede tener varias NIC adjuntas. Cada uno puede estar conectado a una subred diferente. Puesto que se ejecutan los comandos en los pasos anteriores con una NIC, es importante para asegurarse de que especifique la NIC tiene el error de conectividad. Si no está seguro, siempre puede ejecutar los comandos en cada NIC conectado a la máquina virtual.

5. A RDP en VM1, cambie la regla de *Denegar RDP (3389)* para *Permitir RDP(3389)* en **Subred1 GSN** GSN. Confirme que el puerto TCP 3389 está abierto al abrir una conexión RDP con la máquina virtual o con la herramienta de PsPing. Puede obtener más información sobre PsPing lea la [página de descarga de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)

    Puede o quitar reglas de un GSN con la información en el resultado del comando siguiente:

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Consideraciones

Solución de problemas de conectividad, tenga en cuenta los siguientes puntos:

- Reglas de GSN predeterminadas bloqueará acceso entrante desde internet y permitir únicamente VNet tráfico entrante. Deben agregar explícitamente reglas para permitir el acceso entrante desde Internet, según sea necesario.
- Si no hay ninguna regla de seguridad GSN causando la conectividad de red de la VM no se realice correctamente, el problema puede ser debido a:
    - Software de firewall que se ejecuta en el sistema operativo de la VM
    - Rutas configuradas para equipos virtuales o tráfico local. Tráfico de Internet puede redirigirá a local mediante túnel forzado. Una conexión RDP/SSH desde Internet a su máquina virtual no funcionen con esta configuración, dependiendo de cómo el hardware de red local controla este tráfico. Lea el artículo de [Solución de problemas de rutas](virtual-network-routes-troubleshoot-powershell.md) para aprender a diagnosticar los problemas de ruta que pueden impedir la el flujo del tráfico dentro y fuera de la máquina virtual. 
- Si ha peered VNets, de forma predeterminada, la etiqueta VIRTUAL_NETWORK se expande automáticamente para incluir prefijos para VNets peered. Puede ver estos prefijos en la lista de **ExpandedAddressPrefix** para solucionar problemas relacionados con la conectividad de interconexión VNet. 
- Reglas de seguridad eficaz solo se muestran si hay un GSN asociado a la VM NIC y o subred. 
- Si no hay ningún NSGs asociadas a la NIC o subred y tiene una dirección IP pública asignada a su máquina virtual, todos los puertos estará abiertos para acceso entrante y saliente. Si la máquina virtual tiene una dirección IP pública, aplicar NSGs a la subred o NIC es muy recomendable.  
