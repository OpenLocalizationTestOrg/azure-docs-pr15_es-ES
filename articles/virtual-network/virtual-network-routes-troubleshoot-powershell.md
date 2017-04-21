<properties 
   pageTitle="Solucionar problemas de rutas - PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar problemas de rutas en el modelo de implementación de administrador de recursos de Azure con PowerShell de Azure."
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

# <a name="troubleshoot-routes-using-azure-powershell"></a>Solucionar problemas de rutas con PowerShell de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Si experimenta problemas de conectividad de red a o desde la máquina Virtual de Azure (VM), las rutas pueden afectar a los flujos de tráfico de máquina virtual. En este artículo se proporciona información general sobre las capacidades de diagnóstico para rutas para ayudar a solucionar aún más.

Tablas de rutas están asociadas a subredes y son eficaces en todas las interfaces de red (NIC) en esa subred. Los siguientes tipos de rutas se pueden aplicar a cada interfaz de red:

- **Rutas de sistema:** De forma predeterminada, cada subred creado en una red Virtual de Azure (VNet) tiene tablas ruta del sistema que permiten tráfico VNet local, el tráfico a través de puertas de enlace VPN local y el tráfico de Internet. También existen rutas de sistema de VNets peered.
- **BGP rutas:** Se propaga a interfaces de red a través de conexiones de VPN de sitio a otro o ExpressRoute. Más información acerca del enrutamiento de BGP lea los artículos [BGP con puertas de enlace VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) y [Descripción general de ExpressRoute](../expressroute/expressroute-introduction.md) .
- **Rutas definidas por el usuario (UDR):** Si está utilizando los dispositivos de red virtual o están túnel forzado tráfico a una red local a través de una VPN de sitio a sitio, es posible que tenga rutas definidas por el usuario (UDRs) asociadas con la tabla de ruta de subred. Si no está familiarizado con UDRs, lea el artículo de [rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined-routes) .

Con las diversas rutas que pueden aplicarse a una interfaz de red, puede ser difícil determinar qué rutas agregadas son eficaces. Para ayudar a solucionar problemas de conectividad de red de máquina virtual, puede ver todas las rutas eficaces para una interfaz de red en el modelo de implementación de administrador de recursos de Azure.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Usar rutas eficaces para solucionar problemas de flujo de tráfico VM

En este artículo, se utiliza el siguiente escenario como ejemplo para ilustrar cómo solucionar problemas de las rutas eficaces para una interfaz de red:

Una máquina virtual (*VM1*) conectado a la VNet (*VNet1*, prefijo: 10.9.0.0/16) no puede conectarse a un VM(VM3) en un VNet recién peered (*VNet3*, 10.10.0.0/16 prefijo). Hay ninguna UDRs o BGP enruta aplicado a la interfaz de red VM1 NIC1 conectado a la máquina virtual, se aplican solo rutas de sistema.

En este artículo se explica cómo determinar la causa del error de conexión, utilizando la capacidad de rutas eficaces en modelo de implementación de administración de recursos de Azure.
Mientras que el ejemplo usa solo rutas de sistema, pueden utilizarse los mismos pasos para determinar errores de conexión entrante y saliente sobre cualquier tipo de ruta.

>[AZURE.NOTE] Si su máquina virtual tiene más de un NIC conectado, consulte rutas efectivo para cada una de las NIC para diagnosticar problemas de conectividad de red a y desde una máquina virtual.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Vista de rutas eficaces para una máquina virtual

Para ver las rutas agregadas que se aplican a una máquina virtual, realice los pasos siguientes:

### <a name="view-effective-routes-for-a-network-interface"></a>Vista de rutas eficaces para una interfaz de red

Para ver las rutas agregadas que se aplican a una interfaz de red, realice los pasos siguientes:

1.  Iniciar una sesión PowerShell Azure e iniciar sesión en Azure. Si no está familiarizado con PowerShell de Azure, lea el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

2.  El siguiente comando devuelve todas las rutas aplicadas a una interfaz de red denominada *VM1 NIC1* en el grupo de recursos *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Si no conoce el nombre de una interfaz de red, escriba el comando siguiente para recuperar los nombres de todas las interfaces de red en un group.* de recursos

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    El resultado para cada ruta aplicado a la subred a que la NIC está conectada aspecto el resultado siguiente:

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Tenga en cuenta lo siguiente en el resultado:
    - **Nombre**: nombre de la ruta eficaz puede estar vacía, a menos que se especifica explícitamente para rutas definidas por el usuario. 
    - **Estado**: indica el estado de la ruta eficaces. Los valores posibles son "Activos" o "No válido"
    - **AddressPrefixes**: especifica el prefijo de dirección de la ruta eficaces en la notación CIDR. 
    - **nextHopType**: indica el siguiente salto para la ruta dada. Valores posibles son *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*o *Null*. Un valor *null* para **nextHopType** en un UDR puede indicar una ruta no válida. Por ejemplo, si **nextHopType** es *VirtualAppliance* y la red virtual dispositivo VM no está en un estado de configurado o en ejecución. Si **nextHopType** es *VPNGateway* y no hay ninguna puerta de enlace se aprovisione o en ejecución en el VNet determinado, la ruta puede ser válida.
    - **NextHopIpAddress**: especifica la dirección IP del siguiente salto de la ruta eficaces.
    
    El siguiente comando devuelve las rutas de más fácil de ver la tabla:

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    El resultado siguiente es parte de los resultados recibidos para el escenario descrito anteriormente:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. No hay ninguna ruta que aparece la *WestUS VNet3* VNet (prefijo 10.10.0.0/16)* * desde *WestUS VNet1* (prefijo 10.9.0.0/16) en el resultado del paso anterior. Como se muestra en la siguiente imagen, el vínculo de interconexión VNet con la VNet *WestUS VNet3* se encuentra en el estado *desconectado* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    El vínculo bidireccional de la interconexión es errónea, lo que explica por qué VM1 no se puede conectar a VM3 en la VNet *WestUS VNet3* . Un vínculo de interconexión bidireccional VNet de nuevo la instalación de *WestUS VNet1* y *WestUS VNet3* VNets. El resultado devuelto después de establece correctamente el vínculo de interconexión VNet sigue:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Una vez que determine el problema, puede agregar, quitar, o cambiar las rutas y distribuir tablas. Escriba el comando siguiente para ver una lista de los comandos usados para hacerlo:

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Consideraciones

Consideraciones a tener en cuenta al revisar la lista de rutas devuelven:

- El enrutamiento se basa en más larga prefijo coincidir (LPM) entre UDRs, rutas BGP y del sistema. Si hay más de una ruta con la misma coincidencia LPM, se selecciona una ruta en función de su origen en el siguiente orden:
    - Ruta definidas por el usuario
    - Ruta BGP
    - Ruta de sistema (predeterminado)

    Con rutas efectiva, solo puede ver efectiva rutas de coincidencia LPM basada en todas las rutas disponibles. Al mostrar cómo las rutas realmente se evalúan para una determinada NIC, esto facilita mucho rutas específicas que pueden afectar a la conectividad de la máquina virtual de solución de problemas.

- Si tiene UDRs y envíe el tráfico a un dispositivo virtual de red (NVA), con *VirtualAppliance* como **nextHopType**, asegúrese de que el reenvío de IP está habilitado en el NVA recibe el tráfico o se descartan paquetes. 
- Si está habilitada la forzado túnel, todo el tráfico saliente de Internet se se enrutan a local. RDP/SSH desde Internet a su máquina virtual puede no funcionar con esta configuración, dependiendo de cómo la local controla este tráfico. 
  Puede habilitar túnel forzado:
    - Si utiliza VPN de sitio a otro, estableciendo una ruta definida por el usuario (UDR) con nextHopType como puerta de enlace VPN
    - Si se anuncia una ruta predeterminada sobre BGP
- Para que el tráfico de interconexión de VNet para que funcione correctamente, debe existir una ruta del sistema con **nextHopType** *VNetPeering* para el rango de prefijo de VNet peered. Si no existe tal una ruta y el vínculo de interconexión VNet es Aceptar:
    - Espere unos segundos y vuelva a intentar si se trata de un vínculo de interconexión recién establecido. En ocasiones se tarda más en propagarse redirige a todas las interfaces de red en una subred.
    - Reglas del grupo de seguridad de la red (GSN) que pueden influir en los flujos de tráfico. Para obtener más información, vea el artículo de [Solución de problemas de grupos de seguridad de red](virtual-network-nsg-troubleshoot-powershell.md) .
