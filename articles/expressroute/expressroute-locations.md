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

| **Proveedor de servicios**  |**Microsoft Azure** | **Office 365 y CRM Online** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Compatibles | Compatibles | Melbourne, Sídney |
| **[Redes Aryaka]( http://www.aryaka.com/)** | Compatibles | Compatibles | Amsterdam, Silicon Valley, Singapur, Tokio, Washington DC |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Compatibles | Compatibles | Amsterdam, Chicago, Dallas, Londres, Silicon Valley, Singapur, Sídney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Compatibles | Compatibles | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sídney, Tokio, Washington DC |
|**CenturyLink** | Próximamente | Próximamente| Silicon Valley |
|**China Telecom Global** | Compatibles | No compatible | Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Compatibles | Próximamente | Toronto Dallas, Montreal + |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Compatibles | Compatibles | Amsterdam, Dublin, Londres, Tokio |
| **Comcast** | Compatibles | Compatibles | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Compatibles | Compatibles | Los Ángeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Compatibles | Compatibles | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Ángeles, Melbourne, Nueva York, Osaka, París +, Sao Paulo, Seattle, Silicon Valley, Singapur, Sídney, Tokio, Toronto, Washington DC |
| **euNetworks** |  Compatibles | Compatibles | Amsterdam |
| **GÉANT** | Compatibles | Compatibles | Amsterdam |
| **[Internet iniciativa Japón Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Compatibles | Compatibles | Osaka, Tokio |
| **[InterCloud]( https://www.intercloud.com/)** | Compatibles | Compatibles | Amsterdam, Londres, Singapur, Washington DC |
| **Conectar soluciones de Internet - nube** | Compatibles | Compatibles | Amsterdam, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Compatibles | Compatibles | Amsterdam, Londres, París |
| **Jisc** | Compatibles | Compatibles | Londres | 
| **[Comunicaciones de nivel 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Compatibles | Compatibles | Amsterdam, Chicago, Dallas, Las Vegas +, Londres, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Compatibles | Compatibles | Dallas, Hong Kong, Las Vegas, Los Ángeles, Melbourne, Nueva York, Seattle, Singapur, Sídney, Washington DC |
| **MONTAÑA** | Compatibles | Compatibles | Londres |
| **Datos de siguiente generación** | Próximamente | Próximamente | Newport(Wales) + |
| **NEXTDC** | Compatibles | Compatibles | Melbourne, Sídney |
| **Comunicaciones de NTT** | Compatibles | Compatibles | Londres, Los Ángeles, Osaka, Tokio |
| **[Naranja]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Compatibles | Compatibles | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sídney, Washington DC |
| **PCCW Global limitada** | Compatibles | Compatibles | Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Compatibles | Compatibles | Singapur |
| **Softbank** | Compatibles | Compatibles | Osaka, Tokio | 
| **[Comunicaciones de TATA](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Compatibles | Compatibles | Amsterdam, Chennai, Hong Kong, Londres, Mumbai, Silicon Valley, Singapur, Washington DC |
| **[Grupo de TeleCity]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Compatibles | Compatibles | Amsterdam, Dublin, Londres |
| **Telefónica** | Compatibles | Compatibles | Sao Paulo |
| **Telenor** | Compatibles | Compatibles | Amsterdam, Londres |
| **[Teltra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Compatibles | Compatibles | Melbourne, Sídney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Compatibles | Compatibles | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sídney, Tokio, Washington DC |
| **Vodafone** | Compatibles | No compatible | Londres | 
| **[Grupo de Zayo]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Compatibles | Compatibles | Chicago, Los Ángeles, Nueva York, Silicon Valley, Toronto, Washington DC |

 **+**denota próximamente

### <a name="national-cloud-environments"></a>Entornos de nube nacional

#### <a name="us-government-cloud"></a>Nube de gobierno de Estados Unidos

| **Proveedor de servicios**  |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Compatibles | Compatibles | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Compatibles | Compatibles | Chicago, Dallas, Nueva York, Washington DC |
| **[Comunicaciones de nivel 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Compatibles | Compatibles | Chicago, Nueva York +, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Compatibles | Compatibles | Chicago, Dallas +, Nueva York, Washington DC |

#### <a name="china"></a>China

| **Proveedor de servicios**  |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Compatibles | No compatible | Pekín, Shanghai|
Para obtener más información, vea [ExpressRoute en China](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Alemania

| **Proveedor de servicios**  |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Compatibles | No compatible | Berlín +, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Compatibles | No compatible | Frankfurt|
| **cubierta de e** | Compatibles | No compatible | Berlín|
| **Interxion** | Compatibles | No compatible | Frankfurt|

## <a name="nonpartners"></a>Conectividad a través de los proveedores de servicios no aparece

Si su proveedor de servicios de conectividad no se enumera en las secciones anteriores, todavía puede crear una conexión.

- Póngase en contacto con su proveedor de servicios de conectividad para ver si están conectados a cualquiera de los cambios en la tabla anterior. Puede comprobar los vínculos siguientes para obtener más información sobre los servicios ofrecidos por proveedores de exchange. Varios proveedores de conectividad ya están conectados a intercambios Ethernet.

    - [Nube de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Tiene a su proveedor de servicios de conectividad ampliar su red a la ubicación que prefiera interconexión.
    - Asegúrese de que su proveedor de servicios de conectividad extiende la conectividad de una manera altamente disponible para que no hay ningún punto de error.
- Solicitar un circuito de ExpressRoute con el exchange como su proveedor de servicios de conectividad para conectarse a Microsoft.
    - Siga los pasos de [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

|**Proveedor de servicios de conectividad**|**Exchange**|**Ubicaciones**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapur|
|**Comunicaciones de Alaska**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|Nueva York, Washington DC|
|**[Comunicaciones de XO](http://www.xo.com/)**|Equinix|Silicon Valley|


## <a name="expressroute-system-integrators"></a>ExpressRoute integradores

Habilitar la conectividad privada según sus necesidades puede ser difícil, basándose en la escala de la red. Puede trabajar con cualquiera de los integradores enumerados en la tabla siguiente para ayudarle con integrado para ExpressRoute.

|**Integración de sistema**|**Continente**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asia, Europe, Estados Unidos |
|**[Soluciones de dotnet](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Servicios profesionales Equinix](http://www.equinix.com/services/consulting/)**|NOS|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | NOS |
|**[Liderazgo del proyecto](http://www.projectleadership.net/azure)** | NOS |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
- Asegúrese de que se cumplen todos los requisitos previos. Vea [requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"
