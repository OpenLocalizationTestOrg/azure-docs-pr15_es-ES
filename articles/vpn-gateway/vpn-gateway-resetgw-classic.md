<properties
   pageTitle="Restablecer una puerta de enlace VPN Azure | Microsoft Azure"
   description="En este artículo le guiará a través de restablecimiento de la puerta de enlace de VPN de Azure. El artículo se aplica a puertas de enlace VPN en clásico y los modelos de implementación de administrador de recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Restablecer una puerta de enlace de Azure VPN con PowerShell


En este artículo le guiará a través de restablecimiento de la puerta de enlace VPN Azure usar cmdlets de PowerShell. Estas instrucciones incluyen el modelo de implementación clásico y el modelo de implementación de administrador de recursos.

Restablecer la puerta de enlace VPN de Azure es útil si se pierde la conexión VPN entre local en uno o varios túneles VPN S2S. En esta situación, los dispositivos VPN local funcionen correctamente, pero no son capaces de establecer túneles IPsec con las puertas de enlace VPN de Azure. 

Cada puerta de enlace VPN de Azure se compone de dos instancias VM que se ejecuta en una configuración de reserva activa. Al usar el cmdlet de PowerShell para restablecer la puerta de enlace, reinicia la puerta de enlace y, a continuación, vuelve a aplicar las configuraciones entre local a él. La puerta de enlace mantiene la dirección IP pública que ya tiene. Esto significa que no tendrá que actualizar la configuración del enrutador VPN con una nueva dirección IP pública de puerta de enlace VPN de Azure.  

Una vez que se ejecuta el comando, la instancia actual activa de la puerta de enlace VPN de Azure se reinicia inmediatamente. Habrá una separación breve durante la migración tras error de la instancia activa (que se reinicia), a la instancia en espera. La separación debe ser inferior a un minuto.

Si no se restaura la conexión después del primer reinicio, emita el mismo comando nuevamente para reiniciar la segunda instancia VM (la nueva active puerta de enlace). Si el dos reinicio se solicita consecutiva, habrá un período ligeramente donde se que se reinicia ambas instancias VM (activos y en espera). Esto provocará una interrupción mayor en la conectividad VPN, hasta 2 a 4 minutos para máquinas virtuales completar el reinicio.

Después de dos reiniciar, si todavía experimenta problemas de conectividad entre local, abra una solicitud de soporte desde el portal de Azure.

## <a name="before-you-begin"></a>Antes de empezar

Antes de restablecer la puerta de enlace, compruebe los elementos clave que se muestran a continuación para cada túnel VPN IPsec a sitios (S2S). Desconectar de túneles VPN S2S producirá cualquier incoherencia en los elementos. Comprobar y corregir las configuraciones de su local y puertas de enlace VPN de Azure evita innecesario reiniciar el equipo e interrupciones para las demás conexiones de trabajo en las puertas de enlace.

Compruebe lo siguiente antes de restablecer la puerta de enlace:

- Las direcciones IP de Internet (VIP) de la puerta de enlace VPN de Azure y la puerta de enlace VPN local están configuradas correctamente en el Azure y las directivas VPN local.
- La clave previamente compartida debe ser el mismo en puertas de enlace VPN Azure y local.
- Si aplica configuración IPsec/IKE específica, como el cifrado, algoritmos hash y PFS (confidencialidad directa perfecta), asegúrese de que puertas de enlace VPN la Azure y el local tienen las mismas configuraciones.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Restablecer una puerta de enlace de VPN que utiliza el modelo de implementación de administración de recursos

El cmdlet de PowerShell Administrador de recursos para el restablecimiento de la puerta de enlace es `Reset-AzureRmVirtualNetworkGateway`. En el ejemplo siguiente se restablece la puerta de enlace de Azure VPN, "VNet1GW", en el grupo de recursos "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Restablecer una puerta de enlace de VPN que utiliza el modelo de implementación clásica

El cmdlet de PowerShell para restablecer la puerta de enlace VPN de Azure es `Reset-AzureVNetGateway`. En el ejemplo siguiente se restablece la puerta de enlace VPN de Azure la red virtual denominada "ContosoVNet".
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultado:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Pasos siguientes
    
Vea la [referencia de cmdlet de PowerShell administración del servicio](https://msdn.microsoft.com/library/azure/mt617104.aspx) y la [referencia de los cmdlets de PowerShell Administrador de recursos](http://go.microsoft.com/fwlink/?LinkId=828732) para obtener más información.






