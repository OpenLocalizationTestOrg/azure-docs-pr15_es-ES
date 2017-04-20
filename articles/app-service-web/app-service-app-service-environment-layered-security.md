<properties 
    pageTitle="Arquitectura de seguridad superpuestas con entornos de servicio de aplicaciones" 
    description="Implementar una arquitectura de seguridad superpuestas con entornos de aplicación de servicio." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementar una arquitectura de seguridad superpuestas con entornos de servicio de aplicaciones

## <a name="overview"></a>Información general ##
 
Debido a que los entornos de aplicación de servicio proporciona un entorno aislado runtime implementado en una red virtual, los desarrolladores pueden crear una arquitectura de seguridad superpuestas proporcionar diferentes niveles de acceso a la red para cada nivel de aplicación física.

Un deseo comunes es ocultar API fondo general acceso a Internet y a continuación, solo permite a las API para llamar a aplicaciones web ascendente.  [Grupos de seguridad (NSGs) de red] [ NetworkSecurityGroups] puede usarse en subredes que contienen los entornos de servicio de aplicación para restringir el acceso del público a aplicaciones API.

El siguiente diagrama muestra una arquitectura de ejemplo con una aplicación de WebAPI según implementada en un entorno de servicio de aplicación.  Tres instancias de aplicación web independientes, implementados en tres entornos de servicio de aplicación independiente, realizan llamadas de back-end a la misma aplicación WebAPI.

![Arquitectura conceptual][ConceptualArchitecture] 

Los signos más verdes indica que el grupo de seguridad de la red de la subred que contiene "apiase" permite llamadas entrantes de las aplicaciones web de nivel superior, como llamadas bien de sí misma.  Sin embargo el mismo grupo de seguridad de red rechaza explícitamente acceso general tráfico entrante desde Internet. 

El resto de este tema lo guiará por los pasos necesarios para configurar el grupo de seguridad de red en la subred que contiene "apiase".

## <a name="determining-the-network-behavior"></a>Determinar el comportamiento de la red ##
Para saber qué reglas de seguridad de red son necesarias, debe determinar qué clientes de red se permitirá alcance el entorno de servicios de aplicación que contiene la aplicación de la API y los clientes que se bloqueará.

Desde los [grupos de seguridad de red (NSGs)] [ NetworkSecurityGroups] se aplican a subredes y entornos de aplicación de servicio se implementa en subredes, las reglas de un GSN aplicarán a **todas las** aplicaciones que se ejecutan en un entorno de servicio de aplicación.  Con la arquitectura de ejemplo para este artículo, una vez que un grupo de seguridad de red se aplica a la subred que contiene "apiase", todas las aplicaciones que se ejecutan en el entorno de aplicación de servicio "apiase" estarán protegidas por el mismo conjunto de reglas de seguridad. 

- **Determinar la dirección IP saliente de las personas que llaman ascendente:**  ¿Qué es la dirección IP o las direcciones de las personas que llaman ascendente?  Estas direcciones tendrán explícitamente podrá tener acceso en la GSN.  Dado que las llamadas entre entornos de aplicación de servicio se consideran llamadas "Internet", esto significa que la dirección IP saliente asignados a cada uno de los tres entornos de servicio de aplicación precede necesidades podrá tener acceso en la GSN para la subred "apiase".   Para obtener más detalles acerca de cómo determinar la dirección IP saliente para aplicaciones que se ejecute en un entorno de servicio de aplicación, consulte la [Arquitectura de red] [ NetworkArchitecture] artículo de visión general.
- **¿Necesita la aplicación de la API de back-end llame a sí mismo?**  Un punto a veces se pasa por alto y sutil es el escenario donde se necesita la aplicación de back-end llame a sí mismo.  Si una aplicación de la API de back-end en un entorno de servicio de la aplicación debe llamar a sí mismo, también se trata como una llamada de "Internet".  En la arquitectura de ejemplo, es necesario permitir el acceso de la dirección IP saliente de la "apiase" entorno de servicio de aplicación también.

## <a name="setting-up-the-network-security-group"></a>Configuración del grupo de seguridad de red ##
Una vez que se conoce el conjunto de direcciones IP salientes, el siguiente paso es crear un grupo de seguridad de la red.  Grupos de seguridad de red pueden crearse en ambos Administrador de recursos basados redes virtuales, así como redes virtuales clásicas.  Los ejemplos siguientes muestran crear y configurar un GSN en una red virtual clásica con Powershell.

Para la arquitectura de ejemplo, los entornos se encuentran en el sur Central de EE, por lo que se crea una vacía GSN en esa región:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

En primer lugar explícita Permitir que se agrega una regla de la infraestructura de administración de Azure tal como se indica en el artículo en [el tráfico de] [ InboundTraffic] para entornos de aplicación de servicio.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
A continuación, se agregan dos reglas para permitir llamadas HTTP y HTTPS desde el entorno de servicios de aplicación precede primera ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Aclarar y repita para el segundo y tercer precede aplicación entornos de servicio ("fe2ase" y "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por último, conceda acceso a la dirección IP saliente del entorno de servicio de aplicación de la API de back-end de modo que puede devolver la llamada en sí mismo.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

No hay otras reglas de seguridad de la red deben configurarse porque cada GSN tiene un conjunto de reglas predeterminadas que bloquear el acceso entrante desde Internet de forma predeterminada.

La lista completa de reglas en el grupo de seguridad de red se muestran a continuación.  Observe cómo la última regla está resaltada, bloquea el acceso entrante de todas las personas que llaman distintos a los que se han concedido acceso explícitamente.

![Configuración de GSN][NSGConfiguration] 

El último paso es aplicar la GSN a la subred que contiene el entorno de aplicación de servicio "apiase".  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Con GSN aplicado a la subred, solo los tres precede aplicación servicio entornos y el entorno de servicios de aplicación que contiene la API back-end, pueden llamar en el entorno de "apiase".


## <a name="additional-links-and-information"></a>Información y vínculos adicionales ##
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Información acerca de los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md). 

Descripción [de las direcciones IP salientes] [ NetworkArchitecture] y entornos de aplicación de servicio.

[Puertos de red] [ InboundTraffic] usados por los entornos de aplicación de servicio.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
