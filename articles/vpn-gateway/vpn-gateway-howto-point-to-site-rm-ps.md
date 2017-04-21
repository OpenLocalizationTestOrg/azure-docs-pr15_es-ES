<properties 
   pageTitle="Configurar una conexión de puerta de enlace VPN de sitio a punto a una red virtual utiliza el modelo de implementación de administrador de recursos | Microsoft Azure"
   description="Conectar forma segura a su red Virtual de Azure mediante la creación de una conexión VPN de sitio a punto de la puerta de enlace."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar una conexión punto a sitio para un VNet con PowerShell

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clásico: Portal de Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configuración de un punto de sitio (P2S) le permite crear una conexión segura desde un equipo cliente individuales a una red virtual. Una conexión de P2S es útil cuando desea conectarse a su VNet desde una ubicación remota, como desde casa o de una conferencia, o solo tiene unos pocos clientes que necesitan conectarse a una red virtual. 

Conexiones de punto a sitio no requieren un dispositivo VPN o una dirección IP de público para que funcione. Establecer una conexión VPN iniciando la conexión desde el equipo cliente. Para obtener más información acerca de las conexiones de punto a sitios, consulte las [P+F de puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y la [planificación y diseño](vpn-gateway-plan-design.md). 

En este artículo le guiará a través de la creación de un VNet con una conexión punto a sitio en el modelo de implementación de administrador de recursos con PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementación y métodos para las conexiones P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos de implementación disponibles para las configuraciones de P2S y dos modelos de implementación. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flujo de trabajo básico 

![Punto-al-sitio-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "sitio de punto")

En este escenario, creará una red virtual con una conexión punto a sitio. Las instrucciones también le ayudará a generar certificados, que son necesarios para esta configuración. Una conexión de P2S se compone de los siguientes elementos: A VNet con una puerta de enlace VPN, un archivo .cer de certificado de raíz (clave pública), un certificado de cliente y la configuración de la VPN en el cliente. 

Usamos los siguientes valores para esta configuración. Las variables se establece en la sección [1](#declare) de este artículo. Puede siga los pasos indicados como un tutorial y use los valores sin cambiarlas o cambiarlos para reflejar su entorno. 

### <a name="example"></a>Valores de ejemplo

- **Nombre: VNet1**
- **Espacio de direcciones: 192.168.0.0/16** y **10.254.0.0/16**<br>En este ejemplo, usamos más de un espacio de direcciones para ilustrar que esta configuración funcionará con varios espacios de direcciones. Sin embargo, múltiples espacios de dirección no son necesarios para esta configuración.
- **Nombre de subred: front-end**
    - **Intervalo de direcciones de subred: 192.168.1.0/24**
- **Nombre de subred: back-end**
    - **Intervalo de direcciones de subred: 10.254.1.0/24**
- **Nombre de subred: GatewaySubnet**<br>El nombre de subred *GatewaySubnet* es obligatorio para la puerta de enlace VPN trabajar.
    - **Intervalo de direcciones de subred: 192.168.200.0/24** 
- **Grupo de direcciones de cliente VPN: 172.16.201.0/24**<br>Clientes VPN que se conecten a la VNet usando una conexión punto a sitio recibir una dirección IP del grupo de direcciones del cliente VPN.
- **Suscripción:** Si tiene más de una suscripción, compruebe que está usando correcto.
- **Grupo de recursos: TestRG**
- **Ubicación: Estados Unidos oriental**
- **Servidor DNS: dirección IP** del servidor DNS que desea usar para la resolución de nombres.
- **Nombre de GW: Vnet1GW**
- **Nombre IP público: VNet1GWPIP**
- **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de comenzar

- Compruebe que tiene una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Cuando trabaje con PowerShell para esta configuración, asegúrese de que se está ejecutando como administrador. 

## <a name="declare"></a>Parte 1: registro en y establecer variables

En esta sección, inicie sesión y declarar los valores que se utiliza para esta configuración. Los valores declarados se usan en las secuencias de comandos de ejemplo. Cambiar los valores para reflejar su entorno. O bien, puede usar los valores declarados y siga los pasos como ejercicio.

1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. Este cmdlet le solicitará las credenciales de inicio de sesión para su cuenta de Azure. Después de iniciar sesión, descarga la configuración de cuenta para que estén disponibles para Azure PowerShell.

        Login-AzureRmAccount 

2. Obtener una lista de las suscripciones de Azure.

        Get-AzureRmSubscription

3. Especifique la suscripción que desea usar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Declarar las variables que desea usar. Use el siguiente ejemplo, la sustitución de los valores de sus propios cuando sea necesario.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Parte 2: configurar un VNet 

1. Crear un grupo de recursos.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Crear las configuraciones de subred para la red virtual, nombre *GatewaySubnet*, *front-end*y *back-end*. Estos prefijos deben ser parte del espacio de direcciones VNet declarados.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Crear la red virtual. El servidor DNS especificado debe ser un servidor DNS que puede resolver los nombres de los recursos que se está conectando a. En este ejemplo, hemos usado una dirección IP pública. Asegúrese de usar sus propios valores.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Especifique las variables de la red virtual que creó.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Solicitar una dirección IP pública asignada dinámicamente. Esta dirección IP es necesaria para la puerta de enlace para que funcione correctamente. Más adelante conecte la puerta de enlace a la configuración de IP de puerta de enlace.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>Parte 3: certificados

Azure utilizan certificados para autenticar a clientes VPN de sitio a punto VPN. Exportar datos de certificado público (no la clave privada) como Base 64 codificado X.509 .cer archivo desde un certificado de raíz generado por una solución de certificado de empresa o un certificado autofirmado raíz. A continuación, importar los datos del certificado público desde el certificado raíz a Azure. Además, debe generar un certificado de cliente desde el certificado raíz para clientes. Cada cliente que quiere conectarse a la red virtual mediante una conexión de P2S debe tener un certificado de cliente generado desde el certificado raíz.

### <a name="cer"></a>1. obtener el archivo .cer del certificado raíz

Debe obtener los datos de certificado pública para el certificado raíz que desea usar.

- Si está utilizando un sistema de certificados de empresa, obtenga el archivo .cer del certificado raíz que desea usar. 

- Si no está utilizando una solución de certificado de empresa, debe generar un certificado autofirmado raíz. Para conocer los pasos de Windows 10, puede hacer referencia a [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md).


1. Para obtener un certificado de un archivo .cer, abra **certmgr.msc** y busque el certificado raíz. Haga clic en el certificado autofirmado raíz, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**. Se abre el **Asistente para exportación de certificados**.

2. En el asistente, haga clic en **siguiente**, seleccione **No, no exportar la clave privada**y, a continuación, haga clic en **siguiente**.

3. En la página de **Formato de archivo de exportación** , seleccione **Base 64 codificado X.509 (. CER).** A continuación, haga clic en **siguiente**. 

4. En el **archivo de exportación**, **busque** la ubicación a la que desea exportar el certificado. Para **nombre de archivo**, el nombre del archivo de certificado. A continuación, haga clic en **siguiente**.

5. Haga clic en **Finalizar** para exportar el certificado.

### <a name="generate"></a>2. generar el certificado de cliente

A continuación, generar los certificados de cliente. Se puede generar un certificado único para cada cliente que se conectará, o puede usar el mismo certificado en varios clientes. La ventaja de generar los certificados de cliente único es la capacidad para revocar un certificado si es necesario. En caso contrario, si todos los usuarios usa el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, debe generar e instale nuevos certificados para todos los clientes que usan el certificado para autenticar. Los certificados de cliente se instalan en cada equipo cliente más adelante en este ejercicio.

- Si está utilizando una solución de certificado de empresa, generar un certificado de cliente con el formato de valor de nombre común 'name@yourdomain.com', en lugar de formato de la NetBIOS 'Dominio\nombre de usuario'. 

- Si está utilizando un certificado autofirmado, vea [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

### <a name="exportclientcert"></a>3. exporte el certificado de cliente

Se requiere para la autenticación un certificado de cliente. Después de generar el certificado de cliente, exportarlo. Exportar el certificado de cliente se instalará más adelante en cada equipo cliente.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic en el certificado de cliente que desea exportar, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Se trata de un archivo *.pfx* . Asegúrese de recordar la contraseña (clave) que ha configurado para este certificado o grabar.

### <a name="upload"></a>4. cargar el archivo .cer del certificado raíz

Declarar la variable para el nombre del certificado, reemplace el valor por su propio:

        $P2SRootCertName = "Mycertificatename.cer"

Agregar los datos de certificado pública para el certificado raíz a Azure. Puede cargar archivos de hasta 20 certificados raíz. No cargar la clave privada para el certificado raíz en Azure. Una vez que se carga el archivo .cer, Azure usa para autenticar a los clientes que se conectan a la red virtual. 

Reemplace la ruta de acceso de archivo con sus propios y, a continuación, ejecute los cmdlets.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Parte 4: crear la puerta de enlace VPN

Configurar y crear la puerta de enlace de red virtual para su VNet. La *-GatewayType* debe ser **Vpn** y la *-VpnType* debe ser **RouteBased**. Esto puede tardar hasta 45 minutos en completarse.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Parte 5: descargar el paquete de configuración de cliente VPN

Clientes que se conectan a Azure con P2S deben tener un certificado de cliente y un paquete de configuración de cliente VPN instalado. Paquetes de configuración de cliente VPN están disponibles para los clientes de Windows. El paquete de cliente VPN contiene información para configurar el software de cliente VPN que está integrado en Windows y es específico de la VPN que desea conectarse. El paquete no instala software adicional. Consulte las [Preguntas más frecuentes de puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obtener más información.

1. Después de la puerta de enlace se ha creado, puede descargar el paquete de configuración de cliente. En este ejemplo se descarga el paquete de los clientes de 64 bits. Si desea descargar al cliente de 32 bits, reemplace 'Amd64' con 'x86'. También puede descargar al cliente VPN mediante el portal de Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. El cmdlet de PowerShell devuelve un vínculo de dirección URL. Este es un ejemplo de lo que el aspecto de dirección URL devuelto como:

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copie y pegue el vínculo que se devuelve a un explorador web para descargar el paquete. A continuación, instale el paquete en el equipo cliente. Si recibe un mensaje SmartScreen emergente, haga clic en **más información**, **de todos modos ejecutar** para instalar el paquete.

4. En el equipo cliente, vaya a **Configuración de la red** y haga clic en **VPN**. Verá la conexión que aparece. Se muestran el nombre de la red virtual que se conecta a y un aspecto similar a este ejemplo: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Cliente VPN")


## <a name="clientcertificate"></a>Parte 6 - instalar el certificado de cliente

Cada equipo cliente debe tener un certificado de cliente para autenticar. Cuando instala el certificado de cliente, necesitará la contraseña que se creó cuando se exporta el certificado de cliente.

1. Copie el archivo .pfx en el equipo cliente.
2. Haga doble clic en el archivo .pfx para instalarlo. No modifique la ubicación de instalación.

## <a name="connect"></a>Parte 7: conectar con Azure

1. Para conectarse a su VNet, en el equipo cliente, vaya a las conexiones VPN y busque la conexión VPN que ha creado. Se denomina el mismo nombre que su red virtual. Haga clic en **Conectar**. Puede aparecer un mensaje emergente que hace referencia a utilizando el certificado. Si esto sucede, haga clic en **continuar** para usar privilegios elevados. 

2. En la página de estado de **conexión** , haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla **Seleccionar certificado** , compruebe que el certificado de cliente que muestra es el que desea usar para conectarse. Si no es así, use la flecha de lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

    ![Conexión de cliente VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Conexión de cliente VPN")

3. Ahora debe establecerse la conexión.

    ![Establecer la conexión] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Establecer la conexión")

## <a name="verify"></a>Parte 8: comprobar la conexión

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecutar *ipconfig/all*.

2. Ver los resultados. Observe que la dirección IP que ha recibido es una de las direcciones en el grupo de direcciones de cliente VPN de sitio a punto especificado en la configuración. Los resultados deben ser algo parecido a este:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>Para agregar o quitar un certificado raíz de confianza

Los certificados se utilizan para autenticar a clientes VPN de sitio a punto VPN. Los siguientes pasos le guiarán a través de agregar y quitar certificados raíz. Cuando agrega un archivo X.509 codificado base 64 (.cer) en Azure, está indicando a Azure que confíe en el certificado raíz que representa el archivo. 

Puede agregar o quitar certificados raíz de confianza con PowerShell o en el portal de Azure. Si desea hacer esto con el portal de Azure, vaya a su **puerta de enlace de red virtual > Configuración > configuración del sitio de punto > certificados de raíz**. Los siguientes pasos le guíe durante estas tareas utilizando PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Agregar un certificado raíz de confianza

Puede agregar hasta 20 archivos de .cer de certificados raíz de confianza a Azure. Siga los pasos siguientes para agregar un certificado raíz.

1. Crear y preparar el nuevo certificado raíz que va a agregar a Azure. Exportar la clave pública como Base 64 codificado X.509 (. CER) y abrirlo con un editor de texto. Copiar solo la sección que se muestra a continuación. 
 
    Copie los valores, como se muestra en el ejemplo siguiente:

    ![certificado] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificado")
    
2. Especifique el nombre del certificado y la información clave como una variable. Reemplazar la información con su propio, como se muestra en el siguiente ejemplo:

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Agregar el nuevo certificado raíz. Sólo puede agregar un certificado en un momento.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Puede comprobar que el nuevo certificado se agregó correctamente mediante el siguiente cmdlet.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Para quitar un certificado raíz de confianza

Puede quitar el certificado raíz de confianza de Azure. Al quitar un certificado de confianza, los certificados de cliente que generaron el certificado raíz ya no podrá conectarse a Azure a través del sitio de punto. Si desea que los clientes se conecten, que necesitan instalar un nuevo certificado de cliente que se genera a partir de un certificado de confianza en Azure.

1. Para quitar un certificado raíz de confianza, modifique el ejemplo siguiente:

    Declare las variables.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Quitar el certificado.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Utilice el siguiente cmdlet para comprobar que el certificado se quitó correctamente. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Para administrar la lista de certificados de cliente revocados

Puede revocar certificados de cliente. La lista de revocación de certificados le permite denegar selectivamente conectividad punto a un sitio basado en certificados de cliente individuales. Si quita un .cer de certificados raíz de Azure, revoca el acceso para todos los certificados de cliente generados de firmado por el certificado raíz revocados. Si desea revocar un certificado de cliente en particular, no en la raíz, puede hacerlo. De este modo los certificados generados desde el certificado raíz seguirá ser válidos.

Lo más habitual es usar el certificado raíz para administrar el acceso a los niveles de equipo o la organización, con el uso de certificados de cliente revocados poseen control de acceso de usuarios individuales.

### <a name="revoke-a-client-certificate"></a>Revocar un certificado de cliente

1. Obtener la huella digital del certificado de cliente para revocar.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Agregar la huella digital a la lista de huella digital revocado.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Compruebe que la huella digital se agregó a la lista de revocación de certificados. Debe agregar la huella digital de una a la vez.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Restablecer un certificado de cliente

Puede restablecer un certificado de cliente eliminando la huella digital de la lista de certificados de cliente revocados.

1.  Quitar la huella digital de la lista de huella digital de certificado de cliente revocados.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Compruebe si la huella digital se quita de la lista revocada.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Pasos siguientes

Puede agregar una máquina virtual a su red virtual. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.