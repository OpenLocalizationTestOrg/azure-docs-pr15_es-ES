<properties
   pageTitle="Empezar a crear una Internet opuestas equilibrador de carga en modo clásico con PowerShell | Microsoft Azure"
   description="Aprenda a crear un Internet opuestas equilibrador de carga en modo clásico con PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Empezar a crear un opuestas equilibrador de carga (clásico) de PowerShell de Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un Internet opuestas equilibrador de carga con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Configurar el equilibrador de carga con PowerShell

Para configurar un equilibrador de carga con powershell, siga los pasos siguientes:

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../../articles/powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.


2. Después de crear una máquina virtual, puede usar los cmdlets de PowerShell para agregar un equilibrador de carga a una máquina virtual dentro del mismo servicio de nube.

En el ejemplo siguiente se agrega que un equilibrador de carga establece denominada "matriz de servidores Web" nube "mytestcloud" (o servicio myctestcloud.cloudapp.net), agregar los extremos del equilibrador de carga a equipos virtuales llamados "web1" y "web2". El equilibrador de carga recibe tráfico de red en el puerto 80 y equilibra la carga entre las máquinas virtuales definidas por el extremo local (en este caso puerto 80) utilizando TCP.


### <a name="step-1"></a>Paso 1
Crear un extremo de equilibrio de carga de la primera VM "web1"

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Paso 2

Crear otro extremo para la segunda máquina virtual "web2" con el mismo nombre del conjunto de equilibrador de carga

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Quitar una máquina virtual de un equilibrador de carga

Puede usar AzureEndpoint quitar para quitar un punto final de la máquina virtual de equilibrador de carga

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes

También puede [empezar a crear un equilibrador de carga interno](load-balancer-get-started-ilb-classic-ps.md) y configurar qué tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento de tráfico especific red de equilibrador de carga.

Si la aplicación se necesita mantener las conexiones activas para servidores detrás de un equilibrador de carga, puede saber más sobre [configuración de tiempo de espera TCP inactivas equilibradores de carga](load-balancer-tcp-idle-timeout.md). Le servirá para obtener información sobre el comportamiento de la conexión inactiva cuando se usa equilibrador de carga de Azure.

