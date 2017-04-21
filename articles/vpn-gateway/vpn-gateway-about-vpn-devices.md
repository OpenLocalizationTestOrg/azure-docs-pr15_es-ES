<properties 
   pageTitle="Acerca de los dispositivos de VPN para conexiones de puerta de enlace VPN de sitio a sitio para redes virtuales de Azure | Microsoft Azure"
   description="Este artículo trata sobre dispositivos VPN y los parámetros de IPsec para conexiones de puerta de enlace de VPN S2S y contiene vínculos a ejemplos y las instrucciones de configuración."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Acerca de los dispositivos VPN para conexiones de puerta de enlace VPN de sitio a otro

Un dispositivo VPN es necesario para configurar una conexión VPN de sitio a sitio (S2S). Conexiones de sitio a sitio pueden utilizarse para crear una solución híbrida o siempre que desee una conexión segura entre su red local y la red virtual. Este artículo trata sobre los dispositivos VPN compatibles y parámetros de configuración. 

>[AZURE.NOTE] Al configurar una conexión de sitio a otro, una dirección IP IPv4 de público se requiere para su dispositivo VPN.                                                                                                                                                                               

Si el dispositivo no aparece en la tabla de [dispositivos VPN valida](#devicetable) , vea la sección de [dispositivos VPN no validada](#additionaldevices) de este artículo. Es posible que el dispositivo aún funcionen con Azure. Para compatibilidad con dispositivos VPN, póngase en contacto con el fabricante del dispositivo.

**Elementos que tenga en cuenta al ver las tablas:**

- Se ha producido un cambio de terminología de enrutamiento estático o dinámico. Es probable que producirán ambos términos. No hay ningún cambio de funcionalidad, solo los nombres están cambiando.
    - Enrutamiento estático = PolicyBased
    - Enrutamiento dinámico = RouteBased
- Especificaciones de la puerta de enlace VPN de alto rendimiento y la puerta de enlace VPN RouteBased son iguales a menos que se indique lo contrario. Por ejemplo, los dispositivos VPN validados que son compatibles con puertas de enlace VPN RouteBased también son compatibles con la puerta de enlace de Azure alto rendimiento VPN. 


## <a name="devicetable"></a>Dispositivos VPN validados 

Validar un conjunto de dispositivos VPN estándar en colaboración con proveedores de dispositivos. Todos los dispositivos de las familias de dispositivo contenidas en la siguiente lista deben trabajar con puertas de enlace VPN de Azure. Consulte [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md) para comprobar el tipo de puerta de enlace que tiene que crear para la solución que desea configurar. 

Para ayudarle a configurar el dispositivo VPN, consulte los vínculos que corresponden a la familia de dispositivo adecuado. Para compatibilidad con dispositivos VPN, póngase en contacto con el fabricante del dispositivo.



| **Proveedor**                      | **Familia de dispositivo**                                        | **Versión mínima de sistema operativo**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | Cobrar serie VPN enrutadores                                    | 2.9.2                                              | Próximamente                                                                                                                                                                                                                                          | No es compatible                                                                                                                                                                                               |
| Barracuda redes, Inc.        | Serie de Barracuda NextGen Firewall F             | PolicyBased: 5.4.3, RouteBased: 6.2.0  | [Instrucciones de configuración](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Instrucciones de configuración](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda redes, Inc.        |  Serie de Barracuda NextGen Firewall X                 | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | No es compatible                                                                                                                                                                                               |
| Brocado                         | VRouter Vyatta 5400                                      | Enrutador virtual 6.6R3 GA                            | [Instrucciones de configuración](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | No es compatible                                                                                                                                                                                               |
| Punto de comprobación                     | Puerta de enlace de seguridad                                         | R75.40, R75.40VS                                     | [Instrucciones de configuración](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Instrucciones de configuración](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | No es compatible                                                                                                                                                                                               |
| Cisco                           | ASR                                                      | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased)                | [Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased *)               | [Ejemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Ejemplos de Cisco *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | MPX NetScaler, SDX, VPX      |10.1 y anteriores                                           | [Instrucciones de integración](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | No es compatible                                                                                                                                                                                               |
| Dell SonicWALL                  | Serie TZ, NSA, serie SuperMassive, clase E NSA serie | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instrucciones - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instrucciones - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instrucciones - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instrucciones - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Serie de BIG-IP                                 |           12.0                                            | [Instrucciones de configuración](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Instrucciones de configuración](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Instrucciones de configuración](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Instrucciones de configuración](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Japón iniciativa de Internet (IIJ) | Serie SEIL                                              | SEIL / X 4.60, 4.60 SEIL/B1, SEIL/x86 3,20            | [Instrucciones de configuración](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | No es compatible                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased)            | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | Serie J                                                 | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased)          | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (PolicyBased y RouteBased)                  | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (PolicyBased y RouteBased)                  | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Ejemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Servicio de acceso remoto y enrutamiento                        | Windows Server 2012                                | No es compatible                                                                                                                                                                                                                                       | [Ejemplos de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Abrir AG de sistemas | Puerta de enlace de seguridad de Control de objetivos | N/A. | [Guía de instalación](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guía de instalación](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Próximamente)                                                                                                                                                                                                                                        | No es compatible                                                                                                                                                                                               |
| Redes de Palo Alto              | Todos los dispositivos que usa OS panorámica     | PANORÁMICA-OS 6.1.5 o posterior (PolicyBased), panorámica-OS 7.0.5 o posterior (RouteBased)       | [Instrucciones de configuración]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Instrucciones de configuración](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Todos los                                                      | Fireware XTM v11.x                                 | [Instrucciones de configuración](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | No es compatible                                                                                                                                                                                               |

(*) Enrutadores ISR 7200 serie solo admiten PolicyBased VPN.

## <a name="additionaldevices"></a>Dispositivos VPN no validada

Si no ve el dispositivo que aparecen en la tabla de dispositivos VPN valida, todavía puede trabajar con una conexión de sitio a otro. Compruebe que el dispositivo VPN cumple los requisitos mínimos descritos en la sección requisitos de puerta de enlace del artículo [Acerca de puertas de enlace VPN](vpn-gateway-about-vpngateways.md#gateway-requirements) . Dispositivos que cumplen los requisitos mínimos también deberían funcionar bien con puertas de enlace VPN. Póngase en contacto con el fabricante del dispositivo para obtener instrucciones de configuración y soporte técnico adicionales.


## <a name="editing-device-configuration-samples"></a>Ejemplos de configuración de dispositivo de edición

Después de descargar el de ejemplo de configuración de dispositivo de la VPN, debe reemplazar algunos de los valores para reflejar la configuración de su entorno. 

**Para editar una muestra:**

1. Abra el Bloc de notas de ejemplo. 
1. Buscar y reemplazar todas las cadenas de <*texto*> con los valores que pertenecen a su entorno. Asegúrese de incluir < y >. Cuando se especifica un nombre, el nombre que seleccione debe ser único. Si un comando no funciona, consulte la documentación del fabricante del dispositivo.

| **Texto de muestra**                | **Cambiar a**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | El nombre elegido para este objeto. Ejemplo: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | El nombre elegido para este objeto. Ejemplo: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | El nombre elegido para este objeto. Ejemplo: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | El nombre elegido para este objeto. Ejemplo: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | El nombre elegido para este objeto. Ejemplo: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Especifique el rango. Ejemplo: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Especificar la máscara de subred. Ejemplo: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Especifique el intervalo de local. Ejemplo: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Especifique la máscara de subred local. Ejemplo: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Esta información específica de la red virtual y se encuentra en el Portal de administración como **dirección IP de puerta de enlace**. |
| &lt;SP_PresharedKey&gt;                | Esta información específica de la red virtual y se encuentra en el Portal de administración como administrar clave.          |



## <a name="ipsec-parameters"></a>Parámetros de IPsec

>[AZURE.NOTE] Aunque los valores que aparecen en la tabla siguiente son compatibles con la puerta de enlace de VPN de Azure, actualmente no hay ninguna manera de especificar o seleccionar una combinación específica de la puerta de enlace de VPN de Azure. Debe especificar las restricciones del dispositivo VPN local. Además, debe fijar MSS en 1350.

### <a name="ike-phase-1-setup"></a>Configuración de IKE Fase 1

| **(Propiedad)**                                       | **PolicyBased** | **Puerta de enlace RouteBased y estándar o VPN de alto rendimiento** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versión de IKE                                        | IKEv1                          | IKEv2                                                            |
| Grupo Diffie-Hellman                               | Grupo 2 (1024 bits)             | Grupo 2 (1024 bits)                                               |
| Método de autenticación                              | Clave previamente compartida                 | Clave previamente compartida                                                   |
| Algoritmos de cifrado                              | AES256 AES128 3DES             | 3DES AES256                                                      |
| Algoritmo de hash                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Fase 1 seguridad asociación duración (hora) | 28.800 segundos                 | 10,800 segundos                                                   |


### <a name="ike-phase-2-setup"></a>Configuración de IKE Fase 2

| **(Propiedad)**                                                             | **PolicyBased**                 | **Puerta de enlace RouteBased y estándar o VPN de alto rendimiento**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versión de IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algoritmo de hash                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Duración de asociación de seguridad de la fase 2 (hora)                        | 3.600 segundos                                  | 3.600 segundos                                                                  |
| Duración de asociación de seguridad de la fase 2 (rendimiento)                  | KB 102,400,000                                 | -                                                                  |
| Cifrado de IPsec SA & autenticación ofrece (en orden de preferencia) | 1. 2 DE ESP AES256. ESP-AES128 3. ESP-3DES 4. N/A. | Ver la *asociación de seguridad de puerta de enlace RouteBased IPsec (SA) ofrece* (abajo) |
| Confidencialidad directa perfecta (PFS)                                            | No                                             | Sin (*)|
| Detección de punto inactiva                                                      | No compatible                                  | Compatibles                                                          |

(*) Azure puerta de enlace como contestador IKE puede aceptar PFS DH Group 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Ofertas de asociación de seguridad de puerta de enlace RouteBased IPsec

La siguiente tabla enumera IPsec SA cifrado y autenticación ofrece. Ofertas se muestran el orden de preferencia que se presenta o se acepta la oferta.

| **Cifrado IPsec SA y ofertas de autenticación** | **Azure puerta de enlace como iniciador**                               | **Azure puerta de enlace como contestador**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | AH SHA1 con ESP AES_128 con HMAC nulo                      |
| 5                                                 | AH SHA1 con ESP AES_256 con HMAC nulo                      | AH SHA1 con ESP 3_DES con HMAC nulo                        |
| 6                                                 | AH SHA1 con ESP AES_128 con HMAC nulo                      | AH MD5 con ESP 3_DES con null HMAC, sin duración propuesto |
| 7                                                 | AH SHA1 con ESP 3_DES con HMAC nulo                        | AH SHA1 con ESP 3_DES SHA1, sin duración                    |
| 8                                                 | AH MD5 con ESP 3_DES con null HMAC, sin duración propuesto | AH MD5 con ESP 3_DES MD5, sin duración                     |
| 9                                                 | AH SHA1 con ESP 3_DES SHA1, sin duración                    | ESP DES MD5                                                  |
| 10                                                | AH MD5 con ESP 3_DES MD5, sin duración                     | ESP DES SHA1, sin duración                                   |
| 11                                                | ESP DES MD5                                                  | AH SHA1 con ESP DES HMAC null, sin duración propuesto        |
| 12                                                | ESP DES SHA1, sin duración                                   | AH MD5 con ESP DES HMAC null, sin duración propuesto        |
| 13                                                | AH SHA1 con ESP DES HMAC null, sin duración propuesto        | AH SHA1 con ESP DES SHA1, sin duración                      |
| 14                                                | AH MD5 con ESP DES HMAC null, sin duración propuesto        | AH MD5 con ESP DES MD5, sin duración                       |
| 15                                                | AH SHA1 con ESP DES SHA1, sin duración                      | ESP SHA, sin duración                                        |
| 16                                                | AH MD5 con ESP DES MD5, sin duración                       | ESP MD5, sin duración                                        |
| 17                                                | -                                                            | AH SHA, sin duración                                         |
| 18                                                | -                                                            | AH MD5, sin duración                                        |


- Puede especificar el cifrado IPsec ESP nulo con puertas de enlace RouteBased y VPN de alto rendimiento. Cifrado basado en null no ofrece una protección a los datos al transmitirlos y sólo se puede utilizar al máximo rendimiento y mínima latencia es necesaria.  Los clientes puede utilizarlo en escenarios de comunicación de VNet a VNet o cifrado se aplica en otra parte de la solución.

- Conectividad entre local a través de Internet, use la configuración predeterminada de la puerta de enlace de Azure VPN con cifrado y algoritmos hash aparecen en las tablas encima para garantizar la seguridad de la comunicación crítica.





