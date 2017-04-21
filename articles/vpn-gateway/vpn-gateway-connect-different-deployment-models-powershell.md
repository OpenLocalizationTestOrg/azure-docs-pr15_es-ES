<properties 
   pageTitle="Cómo conectar clásicas redes virtuales con el Administrador de recursos de redes virtuales con PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo crear una conexión VPN entre clásico VNets y VNets Administrador de recursos con la puerta de enlace VPN y PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Conectar redes virtuales desde diferentes modelos de implementación con PowerShell

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure tiene dos modelos de administración: clásico y el Administrador de recursos (RM). Si ha estado utilizando Azure algún tiempo, probablemente tenga máquinas virtuales de Azure y roles de instancia ejecuta en una VNet clásico. Sus máquinas virtuales más recientes y las instancias de función pueden estar ejecutándose en un VNet creado en el Administrador de recursos. En este artículo le guiará a través de conexión clásico VNets a VNets Administrador de recursos para permitir que los recursos que se encuentra en los modelos de implementación independiente para comunicarse con otros a través de una conexión de puerta de enlace.

Puede crear una conexión entre VNets que están en diferentes suscripciones y de diferentes regiones. También puede conectar VNets que ya tiene conexiones a redes local, como la puerta de enlace que se han configurado con es dinámico o de la ruta. Para obtener más información acerca de las conexiones de VNet a VNet, consulte las [preguntas más frecuentes de VNet a VNet](#faq) al final de este artículo.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Modelos de implementación y métodos para conectar VNets en diferentes modelos de implementación

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Actualización de la tabla siguiente como nuevos artículos y herramientas adicionales no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde la tabla.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet interconexión

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Antes de comenzar

Los siguientes pasos le guíe durante la configuración necesaria para configurar una puerta de enlace dinámico o de ruta para cada VNet y crear una conexión VPN entre las puertas de enlace. Esta configuración no admite estáticos o basado en la directiva de puertas de enlace.

### <a name="prerequisites"></a>Requisitos previos

 - Ambos VNets ya se han creado.
 - Los intervalos de direcciones para la VNets no se superponen entre sí o se superponen con cualquiera de los intervalos para otras conexiones que las puertas de enlace que esté conectados a.
 - Ha instalado las últimas cmdlets de PowerShell (1.0.2 o posterior). Para obtener más información, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Asegúrese de que instalar la administración de servicio (SM) y los cmdlets del Administrador de recursos (RM). 

### <a name="exampleref"></a>Configuración de ejemplo

Puede usar la configuración de ejemplo como referencia.

**Configuración de VNet clásico**

Nombre de VNet = ClassicVNet <br>
Ubicación = US occidental <br>
Espacios de direcciones de red virtual = 10.0.0.0/24 <br>
Subred 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nombre de red local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Configuración del Administrador de recursos VNet**

Nombre de VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espacios de direcciones IP de red virtual = 192.168.0.0/16 <br>
Subred 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Ubicación = US oriental <br>
Nombre de puerta de enlace público IP = gwpip <br>
Puerta de enlace de red local = ClassicVNetLocal <br>
Nombre de puerta de enlace de red virtual = RMGateway <br>
Configuración de direcciones IP de puerta de enlace = gwipconfig


## <a name="createsmgw"></a>Sección 1: configurar el VNet clásico

### <a name="part-1---download-your-network-configuration-file"></a>Parte 1: descargar el archivo de configuración de red

1. Inicie sesión en su cuenta de Azure en la consola de PowerShell con derechos elevados. El siguiente cmdlet le solicitará las credenciales de inicio de sesión para su cuenta de Azure. Después de iniciar sesión, descarga la configuración de cuenta para que estén disponibles para Azure PowerShell. Usar los cmdlets de PowerShell SM para completar esta parte de la configuración.

        Add-AzureAccount

2. Exportar el archivo de configuración de red Azure, ejecute el siguiente comando. Puede cambiar la ubicación del archivo que desea exportar a una ubicación diferente, si es necesario. Se modifique el archivo y, a continuación, importarla a Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Abra el archivo .xml que ha descargado para editarlo. Para obtener un ejemplo del archivo de configuración de red, vea el [Esquema de configuración de red](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Parte 2: Compruebe la subred de puerta de enlace

En el elemento **VirtualNetworkSites** , agregue una subred de puerta de enlace a su VNet si ya no se ha creado uno. Cuando se trabaja con el archivo de configuración de red, la subred de puerta de enlace debe tener el nombre "GatewaySubnet" o Azure no reconoce y usarla como una subred de puerta de enlace.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Ejemplo:**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Parte 3: agregar el sitio de la red local

Agregar el sitio de red local representa el VNet RM al que desea conectarse. Debe agregar un elemento **LocalNetworkSites** el archivo si ya no existe. En este momento en la configuración, el VPNGatewayAddress puede ser cualquier dirección IP pública válida porque no hemos aún creamos la puerta de enlace para el Administrador de recursos de VNet. Una vez que se crea la puerta de enlace, nos reemplazar esta dirección IP de marcador de posición con la dirección IP pública correcta que se ha asignado a la puerta de enlace de RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>Parte 4: asociar el VNet con el sitio de la red local

En esta sección, se especifique el sitio de red local que desea conectar el VNet a. En este caso, es la VNet Administrador de recursos que hace referencia a una versión anterior. Asegúrese de que los nombres coinciden. Este paso no crea una puerta de enlace. Especifica la red local que se conectará la puerta de enlace.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Parte 5: guardar el archivo y cargar

Guarde el archivo y luego importar en Azure, ejecute el siguiente comando. Asegúrese de que cambiar la ruta de acceso del archivo según sea necesario para su entorno.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Verá algo parecido a este resultado que muestra que la importación se realizó correctamente.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Parte 6: crear la puerta de enlace 

Puede crear la puerta de enlace VNet con el portal clásico o usando PowerShell.

Utilice el ejemplo siguiente para crear una puerta de enlace enrutamiento dinámico:

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

Puede comprobar el estado de la puerta de enlace mediante la `Get-AzureVNetGateway` cmdlet.


## <a name="creatermgw"></a>Sección 2: Configurar la puerta de enlace VNet RM

Para crear una puerta de enlace VPN para la VNet RM, siga las instrucciones siguientes. No inicie los pasos hasta después de recuperar la dirección IP pública de puerta de enlace de VNet clásico. 

1. **Inicie sesión en su cuenta de Azure** en la consola de PowerShell. El siguiente cmdlet le solicitará las credenciales de inicio de sesión para su cuenta de Azure. Después de iniciar sesión, la configuración de cuenta se descarga para que estén disponibles para Azure PowerShell.

        Login-AzureRmAccount 

    Obtener una lista de las suscripciones de Azure si tiene más de una suscripción.

        Get-AzureRmSubscription

    Especifique la suscripción que desea usar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Crear una puerta de enlace de red local**. En una red virtual, la puerta de enlace de red local normalmente se refiere a su ubicación local. En este caso, la puerta de enlace de red local se refiere a su VNet clásico. Asígnele un nombre que puede hacer referencia a él Azure y también especificar el prefijo de espacio de dirección. Azure usa el prefijo de dirección IP que especifique para identificar qué tráfico para enviar a su ubicación local. Si necesita ajustar la información aquí más adelante, antes de crear la puerta de enlace, puede modificar los valores y vuelva a ejecutar la muestra.<br><br>
    - `-Name`es el nombre que desea asignar para hacer referencia a la puerta de enlace de red local.<br>
    - `-AddressPrefix`es el espacio de direcciones para su VNet clásico.<br>
    - `-GatewayIpAddress`es la dirección IP pública de puerta de enlace de VNet clásico. Asegúrese de cambiar en el ejemplo siguiente para reflejar la dirección IP correcta.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Solicitar una dirección IP pública** que se asignarán a la puerta de enlace de red virtual para el Administrador de recursos de VNet. No puede especificar la dirección IP que desee usar. La dirección IP dinámicamente asignada a la puerta de enlace de red virtual. Sin embargo, esto significa que cambiará la dirección IP. Sólo los cambios de direcciones IP de red virtual puerta de enlace es cuando se elimina y vuelve a crear la puerta de enlace. No cambiará en cambiar el tamaño, restablecer o en otras internas o las actualizaciones de mantenimiento de la puerta de enlace.<br>En este paso, también se establece una variable que se utiliza en un paso posterior.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Compruebe que su red virtual tiene una subred de puerta de enlace**. Si no existe ninguna subred de puerta de enlace, agregue uno. Asegúrese de que la subred de puerta de enlace se denomina *GatewaySubnet*.

5. **Recuperar la subred** que se utiliza para la puerta de enlace, ejecute el siguiente comando. En este paso, también se establece una variable que se utiliza en el siguiente paso.
    - `-Name`es el nombre de su VNet Administrador de recursos.
    - `-ResourceGroupName`es el grupo de recursos que está asociada la VNet. La subred de puerta de enlace ya debe existir para este VNet y debe tener un nombre *GatewaySubnet* funcione correctamente.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Crear la configuración de direccionamiento de IP de puerta de enlace**. La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.<br>En este paso, el `-SubnetId` y `-PublicIpAddressId` parámetros se deben pasar la propiedad id de la subred y objetos de direcciones IP, respectivamente. No puede usar una cadena simple. Estas variables se establecen en el paso solicitar una dirección IP pública y el paso para recuperar la subred.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Crear la puerta de enlace de red virtual del Administrador de recursos** , ejecute el siguiente comando. La `-VpnType` debe ser *RouteBased*. Este proceso puede demorarse 45 minutos o más para completar esta operación.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Copie la dirección IP pública** una vez la puerta de enlace VPN se ha creado. Se usa cuando configure la configuración de la red local para su VNet clásico. Puede usar el cmdlet siguiente para recuperar la dirección IP pública. La dirección IP pública aparece en el retorno como *dirección IP*.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Sección 3: Modificar la red local para la VNet clásico

Puede realizar este paso ya sea por exportar el archivo de configuración de red, editarlo y, a continuación, guardar e importar el archivo volver a Azure. O bien, puede modificar esta configuración en el portal de clásico. 

### <a name="to-modify-in-the-portal"></a>Para modificar en el portal

1. Abra el [portal clásico](https://manage.windowsazure.com).

2. En el portal clásico, desplácese hacia abajo en el lado izquierdo y haga clic en **redes**. En la página de **redes** , haga clic en **Redes locales** en la parte superior de la página. 

3. En la página **Redes locales** , haga clic en la red local que configurado en la parte 1 y, a continuación, vaya a la parte inferior de la página y haga clic en **Editar**.

4. En la página **especificar los detalles de su red local** , reemplace la dirección IP del marcador de posición con la dirección IP pública de la puerta de enlace de administrador de recursos que creó en la sección anterior. Haga clic en la flecha para desplazarse a la siguiente sección. Compruebe que el **Espacio de direcciones** es correcta y, a continuación, haga clic en la marca de verificación para aceptar los cambios.

### <a name="to-modify-using-the-network-configuration-file"></a>Modificar mediante el archivo de configuración de red

1. Exportar el archivo de configuración de red.
2. En un editor de texto, modifique la dirección IP de puerta de enlace VPN.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Guardar los cambios e importar el archivo modificado a Azure.


## <a name="connect"></a>Sección 4: Crear una conexión entre las puertas de enlace

Crear una conexión entre las puertas de enlace requiere PowerShell. Debe agregar su cuenta de Azure para usar los cmdlets de PowerShell clásicos. Para ello, puede usar el siguiente cmdlet: 
        
    Add-AzureAccount

1. **Establecer la clave compartida** , ejecute el siguiente comando. En este ejemplo, `-VNetName` es el nombre de la VNet clásica y `-LocalNetworkSiteName` es el nombre especificado para la red local cuando ha configurado en el portal de clásico. La `-SharedKey` es un valor que se puede generar y especifique. El valor que especifique aquí debe ser el mismo valor que especifique en el paso siguiente al crear la conexión. El retorno de este ejemplo debe mostrar **Estado: correcta**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Crear la conexión VPN, ejecute los comandos siguientes.

    **Establecer las variables**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Crear la conexión**<br> Tenga en cuenta que el `-ConnectionType` es IPsec, no Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Puede ver su conexión en cada portal o mediante el `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Preguntas más frecuentes de VNet a VNet

Ver los detalles de preguntas más frecuentes para obtener información adicional acerca de las conexiones de VNet a VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


