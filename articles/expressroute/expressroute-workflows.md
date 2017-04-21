<properties
   pageTitle="Flujos de trabajo para configurar un circuito ExpressRoute | Microsoft Azure"
   description="Esta página le guiará por los flujos de trabajo para configurar peerings y circuito ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Flujos de trabajo de ExpressRoute para el aprovisionamiento de circuito y Estados de circuito

Esta página le guiará a través del servicio de aprovisionamiento y enrutamiento de flujos de trabajo de configuración en un nivel alto.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

La siguiente ilustración y pasos correspondientes mostrar las tareas que debe seguir para tener un circuito ExpressRoute aprovisionado-to-end. 

1. Usar PowerShell para configurar un circuito ExpressRoute. Siga las instrucciones en el artículo de [circuitos crear ExpressRoute](expressroute-howto-circuit-classic.md) para obtener más detalles.

2. Conectividad de orden del proveedor de servicios. Este proceso varía. Para obtener más detalles sobre cómo ordenar conectividad, póngase en contacto con su proveedor de servicios de conectividad.

3. Asegúrese de que el circuito se haya aprovisionado correctamente comprobando el circuito ExpressRoute el aprovisionamiento de estado a través de PowerShell. 

4. Configurar dominios de enrutamiento. Si su proveedor de servicios de conectividad administra Layer 3 por usted, configurarán enrutamiento para su circuito. Si su proveedor de servicios de conectividad sólo ofrece servicios de nivel 2, debe configurar el enrutamiento por instrucciones descritas en las páginas de [enrutamiento requisitos](expressroute-routing.md) y [configuración de enrutamiento](expressroute-howto-routing-classic.md) .

    -  Habilitar interconexión privada Azure, debe habilitar esta interconexión para conectarse a máquinas virtuales / implementados en redes virtuales de servicios de nube.
    -  Habilitar interconexión pública de Azure, debe habilitar interconexión pública Azure si desea conectarse a servicios de Azure alojados en las direcciones IP públicas. Esto es necesario tener acceso a recursos de Azure si ha elegido habilitar el enrutamiento predeterminado para interconexión privada Azure.
    -  Habilitar Microsoft interconexión - debe habilitar acceso a Office 365 y servicios en línea de CRM. 
    
    >[AZURE.IMPORTANT] Debe asegurarse de que se utiliza a un proxy independiente / borde para conectarse a Microsoft a la que se use para Internet. Usando el mismo borde para ExpressRoute e Internet se producen asimétricos enrutamiento y provocar interrupciones de la conexión para su red.

    ![](./media/expressroute-workflows/routing-workflow.png)


5. Vinculación de redes virtuales a circuitos ExpressRoute - puede vincular redes virtuales a su circuito ExpressRoute. Siga las instrucciones [para vincular VNets](expressroute-howto-linkvnet-arm.md) a su circuito. Estos VNets pueden ser en la misma suscripción Azure como el circuito ExpressRoute o puede estar en una suscripción diferente.


## <a name="expressroute-circuit-provisioning-states"></a>Circuito de ExpressRoute Estados de aprovisionamiento

Cada circuito ExpressRoute tiene dos estados:

- Estado de aprovisionamiento de proveedor de servicio
- Estado

Estado representa el estado de aprovisionamiento de Microsoft. Esta propiedad se establece en habilitado, cuando se crea un circuito Expressroute

El estado de aprovisionamiento de proveedor de servicios de conectividad representa el estado en el lado de su proveedor de conectividad. Puede ser *NotProvisioned*, *aprovisionamiento*o *Provisioned*. Circuito de ExpressRoute debe estar en estado de Provisioned para que pueda usarlo.

### <a name="possible-states-of-an-expressroute-circuit"></a>Posibles estados de un circuito ExpressRoute

Esta sección se enumeran los posibles estados de un circuito ExpressRoute.

#### <a name="at-creation-time"></a>En tiempo de creación

Verá el circuito ExpressRoute en el estado siguiente tan pronto como se ejecute el cmdlet de PowerShell para crear el circuito ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


#### <a name="when-connectivity-provider-is-in-the-process-of-provisioning-the-circuit"></a>Cuando es el proveedor de servicios de conectividad en el proceso de aprovisionamiento el circuito

Verá el circuito ExpressRoute en el estado siguiente tan pronto como pasar la clave de servicio para el proveedor de servicios de conectividad y se haya iniciado por el proceso de aprovisionamiento.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


#### <a name="when-connectivity-provider-has-completed-the-provisioning-process"></a>Cuando el proveedor de servicios de conectividad finalizado el proceso de aprovisionamiento

Verá el circuito ExpressRoute en el estado siguiente tan pronto como el proveedor de servicios de conectividad finalizado el proceso de aprovisionamiento.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Aprovisionado y habilitado es que el único estado el circuito puede ser en para que pueda usarlo. Si está utilizando un proveedor de servicios de nivel 2, puede configurar el enrutamiento para su circuito solo cuando se encuentra en este estado.

#### <a name="when-connectivity-provider-is-deprovisioning-the-circuit"></a>Cuando el proveedor de servicios de conectividad es baja el circuito

Si se solicita el proveedor de servicios para activar el circuito ExpressRoute, verá el circuito establecer el estado siguiente después de que el proveedor de servicios ha finalizado el proceso de baja del usuario.


    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Puede volver a habilitarlo si es necesario, o bien, ejecute los cmdlets de PowerShell para eliminar el circuito.  

>[AZURE.IMPORTANT] Si ejecuta el cmdlet de PowerShell para eliminar el circuito al hacer el aprovisionamiento de la ServiceProviderProvisioningState o Provisioned la operación se producirá un error. Trabaje con su proveedor de conectividad para activar el circuito ExpressRoute en primer lugar y, a continuación, elimine el circuito. Microsoft seguirá bill el circuito hasta que se ejecute el cmdlet de PowerShell para eliminar el circuito.


## <a name="routing-session-configuration-state"></a>Estado de configuración de sesión de enrutamiento

El estado de aprovisionamiento de BGP le permite saber si se ha habilitado la sesión BGP en el borde de Microsoft. El estado debe estar habilitado para que pueda usar la interconexión.

Es importante comprobar el estado de la sesión BGP especialmente para interconexión de Microsoft. Además de BGP estado de aprovisionamiento, hay otro estado denominado *anunciados estado prefijos público*. El estado de anunciados prefijos público debe estar en estado *configurada* , tanto para la sesión BGP prepararse y el enrutamiento para trabajar to-end. 

Si el estado de prefijo público anunciados se establece en un estado de *validación es necesario* , la sesión BGP no está habilitada, como los prefijos anunciados no coincide con el número de AS en cualquiera de los registros de enrutamiento. 

>[AZURE.IMPORTANT] Si el estado de prefijos público anunciados se encuentra en estado de *validación manual* , debe abrir una incidencia de soporte técnico con [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y pruebas de que usted es propietario de las direcciones IP anunciadas junto con el número de sistema autónomo asociado.


## <a name="next-steps"></a>Pasos siguientes

- Configurar la conexión de ExpressRoute.

    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
    - [Configurar el enrutamiento](expressroute-howto-routing-arm.md)
    - [Vincular un VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
