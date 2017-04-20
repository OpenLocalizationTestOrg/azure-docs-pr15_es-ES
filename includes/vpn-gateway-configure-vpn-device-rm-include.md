
Para configurar el dispositivo VPN, necesitará la dirección IP pública de la puerta de enlace de red virtual para configurar el dispositivo VPN local. Trabajar con el fabricante del dispositivo para obtener información de configuración específica y configurar el dispositivo. Consulte los [Dispositivos de VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) para obtener más información acerca de los dispositivos VPN que funcionan bien con Azure.

Para buscar la dirección IP pública de la puerta de enlace de red virtual con PowerShell, use el siguiente ejemplo:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

También puede ver la dirección IP pública de la puerta de enlace de red virtual a través del portal de Azure. Desplácese a **puertas de enlace de red Virtual**, a continuación, haga clic en el nombre de la puerta de enlace.