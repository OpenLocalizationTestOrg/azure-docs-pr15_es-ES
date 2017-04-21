<properties
   pageTitle="Requisitos de enrutamiento para ExpressRoute | Microsoft Azure"
   description="Esta página proporciona requisitos detallados para configurar y administrar el enrutamiento de circuitos ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>Requisitos de enrutamiento ExpressRoute  

Para conectarse a servicios de nube de Microsoft con ExpressRoute, debe configurar y administrar el enrutamiento. Algunos proveedores de conectividad ofrecen configurar y administrar el enrutamiento de un servicio administrado. Póngase en contacto con su proveedor de servicios de conectividad para ver si ofrecen este servicio. Si no es así, debe cumplir los siguientes requisitos. 

Consulte el artículo de [circuitos y enrutamiento de dominios](expressroute-circuit-peerings.md) para obtener una descripción de las sesiones de enrutamiento que deben definirse en para facilitar la conectividad.

>[AZURE.NOTE] Microsoft no admite los protocolos de redundancia de enrutador (por ejemplo, HSRP, VRRP) para configuraciones de alta disponibilidad. Se basan en un par de redundante de sesiones BGP por interconexión de alta disponibilidad.

## <a name="ip-addresses-used-for-peerings"></a>Direcciones IP que usa para peerings

Debe reservar algunos bloques de direcciones IP para configurar el enrutamiento entre la red y enrutadores de borde (MSEEs) de empresa de Microsoft. Esta sección proporciona una lista de requisitos y describe las reglas sobre cómo deben adquiridas y utilizar estas direcciones IP.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Direcciones IP que usa para interconexión privada de Azure

Puede usar las direcciones IP privadas o las direcciones IP públicas para configurar la peerings. El intervalo de direcciones se utiliza para configurar las rutas no debe superponerse con intervalos de direcciones que se usan para crear redes virtuales en Azure. 

 - Debe reservar un /29 subred o las dos /30 para interfaces de enrutamiento.
 - Pueden ser las subredes utilizadas para el enrutamiento de direcciones IP privadas o las direcciones IP públicas.
 - Las subredes no deben estar en conflicto con el intervalo reservado por el cliente para su uso en la nube de Microsoft.
 - Si un /29 subred se utiliza, se dividirá en dos /30 subredes. 
     - La primera /30 subred se utilizará para el vínculo principal y el segundo/subred 30 se utilizará para el vínculo secundario.
     - Para cada uno de los /30 subredes, debe usar la primera dirección IP de la /30 subred en el enrutador. Microsoft usará la segunda dirección IP de la /30 subred para establecer una sesión BGP.
     - Debe configurar ambos sesiones BGP para nuestra [disponibilidad SLA](https://azure.microsoft.com/support/legal/sla/) sea válido.  

#### <a name="example-for-private-peering"></a>Ejemplo de interconexión privada

Si decide usar a.b.c.d/29 para configurar la interconexión, se dividirá en dos /30 subredes. En el ejemplo siguiente, veremos cómo se usa la subred a.b.c.d/29. 

a.b.c.d/29 se dividir a.b.c.d/30 y a.b.c.d+4/30 y pasa hacia abajo a Microsoft a través de las API de aprovisionamiento. A.b.c.d+1 que usa como la IP VRF para PE principal y Microsoft va a utilizar a.b.c.d+2 como la VRF IP para el MSEE principal. A.b.c.d+5 que usa como la IP VRF para PE secundario y Microsoft utiliza a.b.c.d+6 como la IP VRF para el MSEE secundario.

Considere la posibilidad de un caso de que seleccione 192.168.100.128/29 configurar interconexión privada. 192.168.100.128/29 incluye direcciones de 192.168.100.128 a 192.168.100.135, entre los que:

- 192.168.100.128/30 se asignará a link1, con el proveedor mediante 192.168.100.129 y Microsoft mediante 192.168.100.130.
- 192.168.100.132/30 se asignará a link2, con el proveedor mediante 192.168.100.133 y Microsoft mediante 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Direcciones IP que usa para Azure público y Microsoft interconexión

Debe usar direcciones IP públicas que es el propietario para configurar las sesiones BGP. Microsoft debe poder Compruebe la propiedad de las direcciones IP con enrutamiento de registros de Internet y registros de enrutamiento de Internet. 

- Debe usar un nombre único/subred 29 o las de dos /30 para configurar el BGP interconexión para cada interconexión por ExpressRoute de circuito (si tiene más de uno). 
- Si un /29 subred se utiliza, se dividirá en dos /30 subredes. 
    - La primera /30 subred se utilizará para el vínculo principal y el segundo/subred 30 se utilizará para el vínculo secundario.
    - Para cada uno de los /30 subredes, debe usar la primera dirección IP de la /30 subred en el enrutador. Microsoft usará la segunda dirección IP de la /30 subred para establecer una sesión BGP.
    - Debe configurar ambos sesiones BGP para nuestra [disponibilidad SLA](https://azure.microsoft.com/support/legal/sla/) sea válido.

## <a name="public-ip-address-requirement"></a>Requisito de dirección IP pública 

### <a name="private-peering"></a>Interconexión privada 

Puede usar las direcciones IPv4 públicas o privadas para interconexión privada. Le proporcionamos aislamiento de llevar a cabo el tráfico de modo superpuesto de direcciones con otros clientes no es posible en caso de interconexión privada. Estas direcciones no se anuncian a Internet. 

### <a name="public-peering"></a>Interconexión pública

La ruta de interconexión pública Azure le permite conectarse a todos los servicios alojados en Azure sobre sus direcciones IP públicas. Estos incluyen servicios enumerados en las [Preguntas más frecuentes de ExpessRoute](expressroute-faqs.md) y cualquier hospedado por proveedores de software independientes de Microsoft Azure. Conectividad con servicios de Microsoft Azure en interconexión pública siempre se inicia desde su red en la red de Microsoft. Debe usar direcciones IP públicas el tráfico destinado a la red de Microsoft.

### <a name="microsoft-peering"></a>Interconexión de Microsoft

La ruta de acceso de interconexión de Microsoft le permite conectarse a servicios de nube de Microsoft que no se admiten a través de la ruta de interconexión pública de Azure. La lista de servicios incluye los servicios de Office 365, como Exchange Online, SharePoint Online, Skype para la empresa y CRM Online. Microsoft admite la conectividad bidireccional en la interconexión de Microsoft. El tráfico destinado a los servicios de nube de Microsoft debe utilizar las direcciones IPv4 públicas válidas antes de unirse a la red de Microsoft.

Asegúrese de que su dirección IP y como número están registrados para usted en uno de los registros que se muestran a continuación.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Direcciones IP públicas anunciadas a Microsoft sobre ExpressRoute no deben ser anuncia a Internet. Esto puede interrumpir la conectividad con otros servicios de Microsoft. Sin embargo, las direcciones IP públicas utilizadas por los servidores de su red comunican con extremos de O365 en Microsoft pueden anunciará sobre ExpressRoute. 

## <a name="dynamic-route-exchange"></a>Exchange ruta dinámica

Enrutamiento de exchange se terminará de protocolo eBGP. Sesiones EBGP se establecen entre el MSEEs y los enrutadores. Autenticación de sesiones BGP no es necesario. Si es necesario, puede configurarse un hash MD5. Consulte el [enrutamiento de configurar](expressroute-howto-routing-classic.md) y [circuito aprovisionamiento circuito Estados y flujos de trabajo](expressroute-workflows.md) para obtener información acerca de cómo configurar las sesiones BGP.

## <a name="autonomous-system-numbers"></a>Números del sistema autónomos

Microsoft usará como 12076 para Azure pública, privada de Azure e interconexión de Microsoft. Nos hemos reservado ASNs desde 65515 a 65520 para uso interno. 16 y 32 bits como números son compatibles.

No existen requisitos alrededor de simetría de transferencia de datos. Las rutas de acceso hacia adelante y volver pueden recorrer los pares de enrutador diferente. Rutas idénticos deben anuncia de ambos lados a través de varios pares de circuito que usted pertenece. Métrica no deben ser idénticos.

## <a name="route-aggregation-and-prefix-limits"></a>Agregación de ruta y los límites de prefijo

Se admiten hasta 4.000 prefijos anunciados nos a través de la interconexión privada Azure. Puede aumentar hasta 10.000 prefijos si se habilita el complemento de premium ExpressRoute. Se aceptan hasta 200 prefijos por sesión BGP para Azure público e interconexión de Microsoft. 

Si el número de prefijos supera el límite, se eliminará la sesión BGP. Se aceptará rutas predeterminadas en el vínculo privado de interconexión solo. Proveedor filtran ruta predeterminada y direcciones IP (RFC 1918) de la Azure público y rutas de interconexión de Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Tránsito y entre región de enrutamiento

ExpressRoute no pueden configurarse como enrutadores de tránsito. Tendrá que dependen de su proveedor de conectividad de servicios de enrutamiento de tránsito.

## <a name="advertising-default-routes"></a>Rutas de publicidad predeterminadas

Rutas predeterminadas solo se permiten en Azure sesiones interconexión privadas. En este caso, se le redirige todo el tráfico de las redes virtuales asociadas a su red. Anunciar rutas predeterminadas en interconexión privada se traducirán en la ruta de acceso de internet de bloqueo de Azure. Debe confiar en el borde corporativo para enrutar el tráfico desde y hacia internet para servicios alojados en Azure. 

 Para habilitar la conectividad con otros servicios de Azure y los servicios de infraestructura, debe asegurarse de que uno de los siguientes elementos está en su lugar:

 - Interconexión pública Azure está habilitada para enrutar el tráfico a extremos públicos
 - Use enrutamiento definidas por el usuario para permitir la conexión a internet para cada subred que requieren conectividad a Internet.
 
>[AZURE.NOTE] Anunciar rutas predeterminadas romperá Windows y otra activación de la licencia de máquina virtual. Siga las instrucciones [aquí](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) para solucionar este problema.

## <a name="support-for-bgp-communities-preview"></a>Soporte técnico para Comunidades BGP (vista preliminar)


Esta sección proporciona información general sobre cómo se utilizará Comunidades BGP con ExpressRoute. Microsoft anuncia rutas en el público y rutas de interconexión de Microsoft con rutas etiquetadas con valores de la Comunidad adecuado. La razón para hacerlo y a continuación, se describen los detalles sobre los valores de la Comunidad. Sin embargo, Microsoft, no cumplirá los valores de la Comunidad etiquetados a rutas anunciadas a Microsoft.

Si se está conectando a Microsoft a través de ExpressRoute en cualquier ubicación interconexión dentro de una región geopolíticas, tendrá acceso a todos los servicios de nube de Microsoft a través de todas las regiones dentro de los límites geopolíticos. 

Por ejemplo, si está conectado a Microsoft en Amsterdam mediante ExpressRoute, tendrá acceso a todos los servicios de nube de Microsoft alojado en Europa del Norte y Europa occidental. 

Consulte la página [ubicaciones interconexión y socios ExpressRoute](expressroute-locations.md) para obtener una lista de las regiones geopolíticas, las áreas de Azure asociadas y ExpressRoute correspondiente interconexión ubicaciones.

Puede comprar más de un circuito ExpressRoute por región geopolíticos. Tener varias conexiones ofrece ventajas muy significativas en alta disponibilidad debido a geo redundancia. En caso de que tenga varios circuitos ExpressRoute, recibirá el mismo conjunto de prefijos anunciados de Microsoft en la interconexión pública y Microsoft interconexión rutas de acceso. Esto significa que tendrá varias rutas de acceso de su red en Microsoft. Esto puede provocar potencialmente decisiones de enrutamiento óptimas realizarse dentro de su red. Como resultado, puede experimentar experiencias de conectividad óptima para los distintos servicios. 

Microsoft etiqueta prefijos anunciados por interconexión pública, y con los valores de comunidad BGP apropiados, que indica la región de Microsoft los prefijos se hospedan en. Puede confiar en los valores de la Comunidad a tomar decisiones de enrutamiento apropiadas para ofrecer [enrutamiento óptimo a los clientes](expressroute-optimize-routing.md).

| **Región geopolíticas** | **Región de Microsoft Azure** | **Valor de la Comunidad BGP** |
|---|---|---|
| **América del Norte** |    |  |
|    | Estados Unidos oriental | 12076:51004 |
|    | Estados Unidos oriental 2 | 12076:51005 |
|    | Estados Unidos occidental | 12076:51006 |
|    | Estados Unidos oeste 2 | 12076:51026 |
|    | Oeste Central de EE. | 12076:51027 |
|    | Norte Central de EE. | 12076:51007 |
|    | Sur Central de EE. | 12076:51008 |
|    | Central de EE. | 12076:51009 |
|    | Canadá Central | 12076:51020 |
|    | Canadá oriental | 12076:51021 |
| **América del sur** |  |  |
|    | Sur de Brasil | 12076:51014 |
| **Europa** |    |  |
|    | Europa del Norte | 12076:51003 |
|    | Europa occidental | 12076:51002 |
| **Asia Pacífico** |    |   |
|    | Asia oriental | 12076:51010 |
|    | Sudeste asiático | 12076:51011 |
| **Japón** |     |   |
|    | Japón oriental | 12076:51012 |
|    | Japón oeste | 12076:51013 |
| **Australia** |    |   | 
|    | Australia Oriental | 12076:51015 |
|    | Australia sureste | 12076:51016 |
| **India** |    |   |
|    | India sur | 12076:51019 |
|    | India oeste | 12076:51018 |
|    | India Central | 12076:51017 |

Todas las rutas anunciadas de Microsoft se etiquetarán con el valor de la Comunidad adecuado. 

>[AZURE.IMPORTANT] Globales se etiquetarán con un valor de la Comunidad adecuada y prefijos se anunciará solo cuando se habilita el complemento de ExpressRoute premium.


Además, Microsoft también etiqueta prefijos basados en el servicio que pertenecen. Esto se aplica solo a la interconexión de Microsoft. La tabla siguiente proporciona una asignación de servicio para el valor de la Comunidad BGP.

| **Servicio** | **Valor de la Comunidad BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype empresarial** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Otros servicios de Office 365** | 12076:5100 |

>[AZURE.NOTE] Microsoft no respeta los valores de la Comunidad BGP que ha configurado en las rutas anunciadas a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Configurar la conexión de ExpressRoute.

    - [Crear un circuito ExpressRoute para el modelo de implementación clásico](expressroute-howto-circuit-classic.md) o [crear y modificar un circuito de ExpressRoute con el Administrador de recursos de Azure](expressroute-howto-circuit-arm.md)
    - [Configurar el enrutamiento para el modelo de implementación clásico](expressroute-howto-routing-classic.md) o [configurar enrutamiento para el modelo de implementación de administrador de recursos](expressroute-howto-routing-arm.md)
    - [Vínculo un VNet clásico a un circuito de ExpressRoute](expressroute-howto-linkvnet-classic.md) o [Un VNet Administrador de recursos a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)


