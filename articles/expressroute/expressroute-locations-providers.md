<properties
   pageTitle="Ubicaciones de ExpressRoute | Microsoft Azure"
   description="Este artículo proporciona una descripción detallada de ubicaciones donde se ofrecen servicios y cómo conectarse a áreas de Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute asociados y ubicaciones interconexión

Las tablas de este artículo proporcionan información sobre proveedores de conectividad ExpressRoute, ExpressRoute geográfico, servicios de nube de Microsoft compatibles a través de ExpressRoute y ExpressRoute integradores (SIs).

## <a name="partners"></a>Proveedores de conectividad de ExpressRoute

ExpressRoute es compatible en todas las regiones Azure y ubicaciones. El mapa siguiente proporciona una lista de regiones Azure ExpressRoute ubicaciones. Consulte ExpressRoute ubicaciones a las que Microsoft mismo nivel con varios proveedores de servicios.

![Mapa de ubicación][0]

Si está conectado al menos una ubicación de ExpressRoute dentro de la región geopolíticas, tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolíticas. La tabla siguiente proporciona un mapa de regiones Azure ExpressRoute ubicaciones dentro de una región geopolíticas.

|**Región geopolíticas**|**Áreas de Azure**|**ExpressRoute ubicaciones**|
|---|---|---|
|**América del Norte**|US oriental, occidental de Estados Unidos, US oriental 2, Central de Estados Unidos, sur Central de EE., Norte Central de EE., Canadá Central, Canadá oriental|Atlanta, Chicago, Dallas, Las Vegas, Los Ángeles, Nueva York, Seattle, Silicon Valley, Washington DC, Montreal +, Quebec ciudad +, Toronto|
|**América del sur**|Sur de Brasil|Sao Paulo|
|**Europa**|Europa del Norte, Europa occidental, Reino Unido oeste, Reino Unido sur|Amsterdam, Dublin, Londres, Newport(Wales) +, París|
|**Asia**|Asia oriental, sudeste asiático|Hong Kong, Singapur|
|**Japón**|Japón oeste Japón oriental|Osaka, Tokio|
|**Australia**|Australia sureste, Australia Oriental|Melbourne, Sídney|
|**India**|India oeste, India Central, India sur|Chennai, Mumbai|



La tabla siguiente proporciona información sobre las regiones y límites geopolíticos de nubes nacionales.

|**Región geopolíticas**|**Áreas de Azure**|**ExpressRoute ubicaciones**|
|---|---|---|---|
|**Nube de gobierno de Estados Unidos**|Estados Unidos Gov Iowa, US Gov Virginia|Chicago, Dallas, Nueva York, Washington DC|
|**China**|China Norte, China oriental|Pekín, Shanghai|
|**Alemania**|Alemania Central, Alemania oriental|Berlín, Frankfurt|


Conectividad en todas las regiones geopolíticas no es compatible en el SKU ExpressRoute estándar. Debe habilitar el complemento de premium ExpressRoute permitir la conectividad global. No se admite la conectividad a entornos de nube nacionales. Puede trabajar con su proveedor de servicios de conectividad si surge esta necesidad.


## <a name="connectivity-provider-locations"></a>Ubicaciones del proveedor de servicios de conectividad

> [AZURE.SELECTOR]
[Ubicaciones por proveedor](expressroute-locations.md#connectivity-provider-locations)
[proveedores de ubicación](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Azure de producción
| **Ubicación**  | **Proveedores de servicios** |
|---------------|-----------------------|
| **Amsterdam** | Redes Aryaka, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - conectar en la nube, Interxion, Verizon de comunicaciones, naranja, Tata comunicaciones, TeleCity Group, Telenor, de nivel 3 |
| **Atlanta** | Equinix |
| **Chennai** | Comunicaciones de TATA |
| **Chicago** | AT & T NetBond, Comcast, Equinix, comunicaciones de nivel 3, grupo de Zayo |
| **Dallas** | AT & T NetBond, Cologix, Equinix, nivel 3 comunicaciones, Megaport |
| **Dublin** | Colt, Telecity grupo |
| **Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, naranja, PCCW Global limitada, comunicaciones de Tata, Verizon |
| **Londres** | AT & T NetBond, British Telecom, Colt, Equinix, InterCloud, soluciones de Internet - conectar en la nube, Interxion, Jisc, nivel 3 comunicaciones, Montaña, NTT comunicaciones, naranja, Tata comunicaciones, Telecity Group, Telenor, Verizon, Vodafone |
| **Las Vegas** | Nivel 3 comunicaciones +, Megaport
| **Los Ángeles** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Nueva York** | Equinix, Megaport, Zayo Group |
| **Newport(Wales) +** | Datos de generación siguiente + |
| **Montreal** | Cologix + |
| **Mumbai** | Comunicaciones de TATA |
| **Osaka** | NTT Equinix, Internet iniciativa Japón Inc. - IIJ, comunicaciones, Softbank |
| **París** | Interxion, Equinix + |
| **Sao Paulo** | Equinix, telefónica |
| **Seattle** | Megaport Equinix, comunicaciones de nivel 3, |
| **Silicon Valley** | Redes Aryaka, AT & T NetBond, British Telecom, CenturyLink +, Comcast, Equinix, nivel 3, naranja, Tata comunicaciones, Verizon, Zayo grupo de comunicaciones |
| **Singapur** | Redes Aryaka, AT & T NetBond, British Telecom, Equinix, InterCloud, Megaport, naranja, SingTel, comunicaciones de Tata, Verizon |
| **Sídney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, naranja, Telstra Corporation, Verizon |
| **Tokio** | Aryaka redes, British Telecom, Colt, Equinix, Internet iniciativa Japón Inc. - IIJ, NTT comunicaciones, Softbank, Verizon |
| **Toronto** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Redes Aryaka, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, nivel 3, Megaport, naranja, Tata comunicaciones, Verizon, Zayo grupo de comunicaciones |

 **+**denota próximamente

### <a name="national-cloud-environments"></a>Entornos de nube nacional

#### <a name="us-government-cloud"></a>Nube de gobierno de Estados Unidos

| **Ubicación**  |**Proveedores de servicios** |
|---------------|--------------------|
| **Chicago** | AT & T NetBond, Equinix, nivel 3 comunicaciones, Verizon |
| **Dallas** |  Equinix, Verizon + |
| **Nueva York** | Verizon Equinix, comunicaciones de nivel 3 +, |
| **Washington DC** | AT & T NetBond, Equinix, nivel 3 comunicaciones, Verizon |

#### <a name="china"></a>China

| **Ubicación**  | **Proveedores de servicios** |
|---------------|-----------------------|
| **Beijing** | China Telecom |
| **Shanghai** |  China Telecom |
Para obtener más información, vea [ExpressRoute en China](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Alemania

| **Ubicación**  | **Proveedores de servicios** |
|---------------|-----------------------|
| **Berlín** | Colt + e cubierta |
| **Frankfurt** | Colt, Equinix, Interxion |

## <a name="nonpartners"></a>Conectividad a través de los proveedores de servicios no aparece

Si su proveedor de servicios de conectividad no se enumera en las secciones anteriores, todavía puede crear una conexión.

- Póngase en contacto con su proveedor de servicios de conectividad para ver si están conectados a cualquiera de los cambios en la tabla anterior. Puede comprobar los vínculos siguientes para obtener más información sobre los servicios ofrecidos por proveedores de exchange. Varios proveedores de conectividad ya están conectados a intercambios Ethernet.

    - [Nube de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Tiene a su proveedor de servicios de conectividad ampliar su red a la ubicación que prefiera interconexión.
    - Asegúrese de que su proveedor de servicios de conectividad extiende la conectividad de una manera altamente disponible para que no hay ningún punto de error.
- Solicitar un circuito de ExpressRoute con el exchange como su proveedor de servicios de conectividad para conectarse a Microsoft.
    - Siga los pasos de [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

|**Ubicación**|**Exchange**|**Proveedores de conectividad**|
|-------------|------------|-------------------------|
| **Nueva York** | Equinix | Lightower |
| **Seattle** | Equinix | Comunicaciones de Alaska |
| **Silicon Valley** | Equinix | Comunicaciones de XO |
| **Singapur** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute integradores

Habilitar la conectividad privada según sus necesidades puede ser difícil, basándose en la escala de la red. Puede trabajar con cualquiera de los integradores enumerados en la tabla siguiente para ayudarle con integrado para ExpressRoute.

|**Continente**|**Integradores**|
|-------------|---------------------|
| **Asia** | Avanade Inc., OneAs1a|
| **Europa** | Avanade Inc., Dotnet soluciones|
| **NOS** | Avanade Inc., Equinix Professional Services, Perficient, liderazgo de proyecto|

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
- Asegúrese de que se cumplen todos los requisitos previos. Vea [requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"
