<properties 
   pageTitle="Solucionar problemas de rutas - Portal | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar problemas de rutas en el modelo de implementación de Azure Administrador de recursos con el Portal de Azure."
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

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Solucionar problemas de rutas con el Portal de Azure

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

1. Inicie sesión en el portal de Azure en https://portal.azure.com.
2. Haga clic en **más servicios**, haga clic en **máquinas virtuales de Windows** en la lista que aparece.
3. Seleccione una máquina virtual para solucionar problemas de la lista que aparece y se muestra un módulo VM con opciones.
4. Haga clic en **diagnosticar & solucionar los problemas de** y, a continuación, seleccione un problema común. En este ejemplo, **no puedo conectarme a mi VM de Windows** está seleccionada. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Pasos aparecen en el problema, tal como se muestra en la siguiente imagen: 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Haga clic en *rutas eficaces* en la lista de pasos recomendados.

6. Aparece el módulo de **rutas efectiva** , tal como se muestra en la siguiente imagen:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Si su máquina virtual tiene sólo una NIC, está seleccionado de manera predeterminada. Si tiene más de una NIC, seleccione la NIC para el que desea ver las rutas eficaces.

    >[AZURE.NOTE] Si la máquina virtual asociada a la NIC no está en un estado de ejecución, no se mostrarán rutas eficaces. Solo las rutas eficaces primeros 200 se muestran en el portal. Para obtener la lista completa, haga clic en **Descargar**. Puede filtrar aún más los resultados del archivo .csv descargado.

    Tenga en cuenta lo siguiente en el resultado:
    - **Origen**: indica el tipo de ruta. Se muestran las rutas de sistema como *predeterminada*, UDRs se muestran como rutas de *usuario* y la puerta de enlace (estática o BGP) se muestran como *VPNGateway*.
    - **Estado**: indica el estado de la ruta eficaces. Los valores posibles son *activa* o *no válidos*.
    - **AddressPrefixes**: especifica el prefijo de dirección de la ruta eficaces en la notación CIDR. 
    - **nextHopType**: indica el siguiente salto para la ruta dada. Valores posibles son *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*o *Null*. Un valor *null* para **nextHopType** en un UDR puede indicar una ruta no válida. Por ejemplo, si **nextHopType** es *VirtualAppliance* y la red virtual dispositivo VM no está en un estado de configurado o en ejecución. Si **nextHopType** es *VPNGateway* y no hay ninguna puerta de enlace se aprovisione o en ejecución en el VNet determinado, la ruta puede ser válida.
    
7. No hay ninguna ruta que aparece la VNet *WestUS VNET3* (prefijo 10.10.0.0/16) desde el *WestUS VNet1* (prefijo 10.9.0.0/16) en la imagen en el paso anterior. En la imagen siguiente, el vínculo de interconexión está en el estado *desconectado* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    El vínculo bidireccional de la interconexión es errónea, lo que explica por qué VM1 no se puede conectar a VM3 en la VNet *WestUS VNet3* .

8. La imagen siguiente muestra las rutas después de establecer el vínculo de interconexión bidireccional:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Para más escenarios de solución de problemas de túnel forzado y evaluación de ruta, lea la sección [Consideraciones](virtual-network-routes-troubleshoot-portal.md#Considerations) de este artículo.

### <a name="view-effective-routes-for-a-network-interface"></a>Vista de rutas eficaces para una interfaz de red

Si el flujo del tráfico de red se ve afectado una interfaz de red (NIC), puede ver una lista completa de rutas eficaces directamente en una NIC. Para ver las rutas agregadas que se aplican a una NIC, realice los pasos siguientes:

1. Inicie sesión en el portal de Azure en https://portal.azure.com.
2. Haga clic en **más servicios**, haga clic en **interfaces de red**
3. Buscar en la lista para el nombre de una NIC o selecciónelo en la lista que aparece. En este ejemplo, se selecciona **VM1 NIC1** .
4. Seleccione **efectiva rutas** en el módulo de **interfaz de red** , tal como se muestra en la siguiente imagen:
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    El **ámbito** predeterminado es la interfaz de red seleccionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Rutas eficaces de vista de una tabla de ruta

Cuando se modifica rutas definidas por el usuario (UDRs) en una tabla de ruta, desea revisar el impacto de las rutas de ser agregado en una máquina virtual determinada. Una tabla de ruta se puede asociar con cualquier número de subredes. Ahora puede ver todas las rutas eficaces para todas las NIC que se aplica una tabla de ruta determinada, sin tener que cambiar el contexto de la hoja de ruta determinada tabla.

En este ejemplo, se especifica un UDR (*UDRoute*) en una tabla de la ruta (*UDRouteTable*). Esta ruta envía todo el tráfico de Internet desde *subred1* en la VNet *WestUS VNet1* a través de un dispositivo virtual de red (NVA), en *subred2* de la misma VNet. En la siguiente imagen se muestra la ruta:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Para ver las rutas de una tabla de ruta agregadas, siga los pasos siguientes:

1. Inicie sesión en el portal de Azure en https://portal.azure.com.
2. Haga clic en **más servicios**, haga clic en **tablas de rutas**
3. Buscar en la lista de la tabla de ruta que desea ver las rutas agregadas a y selecciónelo. En este ejemplo, se selecciona **UDRouteTable** . Aparece un módulo de la tabla de ruta seleccionado, tal como se muestra en la siguiente imagen:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Seleccione **Efectiva rutas** en el módulo de la **tabla de rutas** . El **ámbito** se establece en la tabla de ruta seleccionada.
5. Una tabla de rutas se puede aplicar a varias subredes. Seleccione la **subred** que desea revisar en la lista. En este ejemplo, se selecciona **subred1** .
6. Seleccione una **interfaz de red**. Se muestran todas las NIC conectadas a la subred seleccionada. En este ejemplo, se selecciona **VM1 NIC1** .

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Si la NIC no está asociada a una VM en ejecución, no se muestran rutas eficaces.

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
    - Reglas del grupo de seguridad de la red (GSN) que pueden influir en los flujos de tráfico. Para obtener más información, vea el artículo de [Solución de problemas de grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md) .
