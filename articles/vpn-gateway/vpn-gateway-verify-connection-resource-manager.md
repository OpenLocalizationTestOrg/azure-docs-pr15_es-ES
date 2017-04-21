<properties
   pageTitle="Comprobar una conexión de puerta de enlace | Microsoft Azure"
   description="Este artículo le muestra cómo comprobar una conexión de puerta de enlace en el modelo de implementación de administrador de recursos"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Comprobar una conexión de puerta de enlace

Puede comprobar la conexión de la puerta de enlace de varias maneras diferentes. En este artículo le mostrará cómo comprobar el estado de una conexión de la puerta de enlace de administrador de recursos con el portal de Azure y utilizando PowerShell.


## <a name="verify-using-powershell"></a>Comprobar con PowerShell

Debe instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener información sobre cómo instalar los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Para obtener más información sobre cómo usar los cmdlets del Administrador de recursos, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Paso 1: Iniciar sesión en su cuenta de Azure

1. Abra la consola de PowerShell con privilegios elevados y conectarse a su cuenta.

        Login-AzureRmAccount

2. Compruebe las suscripciones para la cuenta.

        Get-AzureRmSubscription 

3. Especifique la suscripción que desea usar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Paso 2: Compruebe su conexión


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Comprobar con el portal de Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Pasos siguientes

- Puede agregar máquinas virtuales con sus redes virtuales. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.

