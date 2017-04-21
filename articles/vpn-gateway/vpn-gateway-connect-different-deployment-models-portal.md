<properties 
   pageTitle="Cómo conectar redes virtuales clásicas a redes virtuales del Administrador de recursos en el portal de | Microsoft Azure"
   description="Obtenga información sobre cómo crear una conexión VPN entre clásico VNets y VNets Administrador de recursos con el portal y puerta de enlace VPN"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Conectar redes virtuales desde diferentes modelos de implementación en el portal

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure tiene dos modelos de administración: clásico y el Administrador de recursos (RM). Si ha estado utilizando Azure algún tiempo, probablemente tenga máquinas virtuales de Azure y roles de instancia ejecuta en una VNet clásico. Sus máquinas virtuales más recientes y las instancias de función pueden estar ejecutándose en un VNet creado en el Administrador de recursos. En este artículo le guiará a través de conexión clásico VNets a VNets Administrador de recursos para permitir que los recursos que se encuentra en los modelos de implementación independiente para comunicarse con otros a través de una conexión de puerta de enlace. 

Puede crear una conexión entre VNets que están en diferentes suscripciones y de diferentes regiones. También puede conectar VNets que ya tiene conexiones a redes local, como la puerta de enlace que se han configurado con es dinámico o de la ruta. Para obtener más información acerca de las conexiones de VNet a VNet, consulte las [preguntas más frecuentes de VNet a VNet](#faq) al final de este artículo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para las conexiones de VNet a VNet

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Actualización de la tabla siguiente como nuevos artículos y herramientas adicionales no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde la tabla.<br><br>

**VNet a VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet interconexión

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Antes de comenzar

Los siguientes pasos le guíe durante la configuración necesaria para configurar una puerta de enlace dinámico o de ruta para cada VNet y crear una conexión VPN entre las puertas de enlace. Esta configuración no admite estáticos o basado en la directiva de puertas de enlace. 

En este artículo, usamos el portal clásico, el portal de Azure y PowerShell. Actualmente, no es posible crear esta configuración con solo el portal de Azure.

### <a name="prerequisites"></a>Requisitos previos

 - Ambos VNets ya se han creado.
 - Los intervalos de direcciones para la VNets no se superponen entre sí o se superponen con cualquiera de los intervalos para otras conexiones que las puertas de enlace que esté conectados a.
 - Ha instalado las últimas cmdlets de PowerShell (1.0.2 o posterior). Para obtener más información, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Asegúrese de que instalar la administración de servicio (SM) y los cmdlets del Administrador de recursos (RM). 

### <a name="values"></a>Configuración de ejemplo

Puede usar la configuración de ejemplo como referencia.

**Configuración de VNet clásico**

Nombre de VNet = ClassicVNet <br>
Ubicación = US occidental <br>
Espacios de direcciones de red virtual = 10.0.0.0/24 <br>
Subred 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nombre de red local = RMVNetLocal <br>

**Configuración del Administrador de recursos VNet**

Nombre de VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espacios de direcciones IP de red virtual = 192.168.0.0/16 <br>
Subred 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Ubicación = US oriental <br>
Nombre de puerta de enlace de red virtual = RMGateway <br>
Nombre de puerta de enlace público IP = gwpip <br>
Tipo de puerta de enlace = VPN <br>
Tipo de VPN = basado en la ruta <br>
Puerta de enlace de red local = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Sección 1: Configurar las opciones de VNet clásicos

En esta sección, creamos la red local y la puerta de enlace para su VNet clásico. Las instrucciones de esta sección usar el portal clásico. En la actualidad, el portal de Azure no ofrece todas las opciones que pertenecen a un VNet clásico.

### <a name="part-1---create-a-new-local-network"></a>Parte 1: crear una nueva red local

Abra el [portal clásica](https://manage.windowsazure.com) e inicie sesión con su cuenta de Azure.

1. En la esquina inferior izquierda de la pantalla, haga clic en **nuevo** > **Servicios de red** > **Red Virtual** > **Agregar de red local**.

2. En la ventana **especificar los detalles de su red local** , escriba un nombre para el VNet RM que desea conectarse. En el cuadro **dirección IP del dispositivo VPN (opcional)** , escriba cualquier dirección IP pública válida. Esto es simplemente un marcador de posición temporal. Cambiar la dirección IP más adelante. En la esquina inferior derecha de la ventana, haga clic en el botón de flecha.
 
3. En la página **especificar el espacio de direcciones** , en el cuadro de texto **IP inicial** , escriba el prefijo de red y bloque CIDR para la VNet Administrador de recursos que desea conectarse. Esta configuración se utiliza para especificar el espacio de direcciones para redirigir a los VNet RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Parte 2: asociar la red local para su VNet

1. Haga clic en **Redes virtuales** en la parte superior de la página para cambiar a la pantalla de redes virtuales y luego haga clic para seleccionar su VNet clásico. En la página para su VNet, haga clic en **Configurar** para desplazarse a la página de configuración.

2. En la sección de conexión de **conectividad de sitio a sitio** , seleccione la casilla de verificación **conectarse a la red local** . A continuación, seleccione la red local que ha creado. Si tiene varias redes locales que ha creado, asegúrese de seleccionar el que ha creado para representar la VNet Administrador de recursos en la lista desplegable.

3. Haga clic en **Guardar** en la parte inferior de la página.

### <a name="part-3---create-the-gateway"></a>Parte 3: crear la puerta de enlace

1. Después de guardar la configuración, haga clic en el **panel** en la parte superior de la página para cambiar a la página de panel. En la parte inferior de la página de panel, haga clic en **Crear la puerta de enlace**, haga clic en **Enrutamiento dinámico**. Haga clic en **Sí** para empezar a crear la puerta de enlace. Una puerta de enlace enrutamiento dinámico se requiere para esta configuración.

2. Espere a que la puerta de enlace que debe crearse. A veces puede necesitar 45 minutos o más en completarse.

### <a name="ip"></a>Parte 4: ver la dirección IP pública de puerta de enlace

Después de la puerta de enlace se ha creado, puede ver la dirección IP de puerta de enlace en la página de **panel** . Esta es la dirección IP pública de la puerta de enlace. Escriba o copie la dirección IP pública. Se usa en pasos posteriores cuando se crea la red local para la configuración del Administrador de recursos VNet.


## <a name="creatermgw"></a>Sección 2: Configurar las opciones del Administrador de recursos VNet

En esta sección, creamos la puerta de enlace de red virtual y la red local para su VNet Administrador de recursos. No inicie los siguientes pasos hasta después de recuperar la dirección IP pública de puerta de enlace de VNet clásico.

Las capturas de pantalla se proporcionan ejemplos. Asegúrese de reemplazar los valores con su propio. Si va a crear esta configuración como ejercicio, consulte estos [valores](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Parte 1: crear una subred de puerta de enlace

Antes de conectar su red virtual a una puerta de enlace, debe crear la subred de puerta de enlace de la red virtual al que desea conectarse. Crear una subred de puerta de enlace con el recuento CIDR de /28 o mayor (/ 27/26, etcetera.)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Desde un explorador, vaya al [portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2: crear una puerta de enlace de red virtual


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Parte 3: crear una puerta de enlace de red local

Normalmente, la 'puerta de enlace de red local' hace referencia a su ubicación local. Indica a Azure a la dirección IP que se va a redirigir a la ubicación y la dirección IP pública del dispositivo para esta ubicación. Sin embargo, en este caso, se refiere a la dirección IP pública asociada a su VNet clásica y la puerta de enlace de red virtual y el intervalo de direcciones.

Asigne un nombre por el que puede hacer referencia a él Azure a la puerta de enlace de red local. Puede crear la puerta de enlace de red local mientras se crea la puerta de enlace de red virtual. Para esta configuración, use la dirección IP pública que se ha asignado a la puerta de enlace VNet clásico en la [sección anterior](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>Parte 4: copie la dirección IP pública

Una vez que ha terminado de crear la puerta de enlace de red virtual, copie la dirección IP pública que está asociada con la puerta de enlace. Se usa cuando configure la configuración de la red local para su VNet clásico. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Sección 3: Modificar la red local para la VNet clásico

Abra el [portal clásico](https://manage.windowsazure.com).

2. En el portal clásico, desplácese hacia abajo en el lado izquierdo y haga clic en **redes**. En la página de **redes** , haga clic en **Redes locales** en la parte superior de la página. 

3. Haga clic en la red local que ha configurado en la parte 1. En la parte inferior de la página, haga clic en **Editar**.

4. En la página **especificar los detalles de su red local** , reemplace la dirección IP del marcador de posición con la dirección IP pública de la puerta de enlace de administrador de recursos que creó en la sección anterior. Haga clic en la flecha para desplazarse a la siguiente sección. Compruebe que el **Espacio de direcciones** es correcta y, a continuación, haga clic en la marca de verificación para aceptar los cambios.

## <a name="connect"></a>Sección 4: Crear la conexión

En esta sección, creamos la conexión entre el VNets. Los pasos de este requieren PowerShell. No puede crear esta conexión en cualquiera de los portales. Asegúrese de que haya descargado e instalado la clásica (SM) y los cmdlets de PowerShell de administrador de recursos (RM).


1. Inicie sesión en su cuenta de Azure en la consola de PowerShell. El siguiente cmdlet le solicitará las credenciales de inicio de sesión para su cuenta de Azure. Después de iniciar sesión, la configuración de cuenta se descarga para que estén disponibles para Azure PowerShell.

        Login-AzureRmAccount 

    Obtener una lista de las suscripciones de Azure si tiene más de una suscripción.

        Get-AzureRmSubscription

    Especifique la suscripción que desea usar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Agregue su cuenta de Azure para usar los cmdlets de PowerShell clásicos. Para ello, puede usar el siguiente comando:

        Add-AzureAccount

3. Establecer la clave compartida, ejecute el siguiente ejemplo. En este ejemplo, `-VNetName` es el nombre de la VNet clásica y `-LocalNetworkSiteName` es el nombre especificado para la red local cuando ha configurado en el portal de clásico. La `-SharedKey` es un valor que se puede generar y especifique. El valor que especifique aquí debe ser el mismo valor que especifique en el paso siguiente al crear la conexión.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Para crear la conexión VPN, ejecute los comandos siguientes:
    
    **Establecer las variables**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Crear la conexión**<br> Tenga en cuenta que el `-ConnectionType` es 'IPsec', no 'Vnet2Vnet'. En este ejemplo, `-Name` es el nombre que desea llamar a la conexión. En el ejemplo siguiente se crea una conexión con el nombre '*rm a clásica conexión*'.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Compruebe su conexión

Puede comprobar la conexión con el portal clásico, el portal de Azure o PowerShell. Puede usar los siguientes pasos para comprobar la conexión. Reemplace los valores con su propio.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>Preguntas más frecuentes de VNet a VNet

Ver los detalles de preguntas más frecuentes para obtener información adicional acerca de las conexiones de VNet a VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


