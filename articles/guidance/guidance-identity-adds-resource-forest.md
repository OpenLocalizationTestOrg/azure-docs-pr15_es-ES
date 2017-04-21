<properties
   pageTitle="Referencia de arquitectura Azure - IaaS: Creación de un bosque de recursos de Active Directory en Azure | Microsoft Azure"
   description="Cómo crear un dominio de Active Directory de confianza en Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Crear un bosque de recursos de servicios de directorio de Active Directory (suma) en Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe cómo crear un dominio de Active Directory en Azure independiente, pero es de confianza, dominios del bosque local.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta arquitectura de referencia utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

Una organización que se ejecute en local de Active Directory (AD) puede tener un bosque que incluya varios dominios. Por ejemplo, podría crear dominios individuales para diversos departamentos o suborganizations o dominios nuevos se han agregado como resultado de la adquisición o fusión de otras organizaciones. Puede usar dominios para proporcionar aislamiento entre las áreas funcionales que se deben mantener independientes, posiblemente por motivos de seguridad, pero puede compartir información entre dominios mediante el establecimiento de relaciones de confianza.

Una organización que utiliza dominios independientes puede sacar partido de Azure cambiando uno o varios de estos dominios en la nube. Como alternativa, una organización que desee conservar todos los recursos de nube lógicamente distintas de esos local mantenidos y almacenar información sobre los recursos de la nube en su propio directorio, en un dominio que también se almacenan en la nube.

Puede implementar Active Directory en Azure de varias maneras diferentes, como se describe en los artículos de [Ampliación de Active Directory para Azure] [ extending-ad-to-azure] e [Implementación de Azure Active Directory][implementing-aad]. Este documento se centra en un escenario específico: creación de un dominio en la nube que es distinta de los dominios que llevan a cabo en local, pero que puede tener una relación de confianza con los dominios locales. 

Casos de uso común para esta arquitectura incluyen:

- Mantenimiento de separación de seguridad para objetos e identidades mantenidas en la nube.

- Migrar los dominios individuales desde local en la nube.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura (*haga clic para acercar*). Para obtener más información sobre los elementos atenuados, lea [implementar una arquitectura de red seguro híbrido en Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar una arquitectura de red seguro híbrido con acceso a Internet en Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Red local.** La red local contiene su propio bosque de AD y dominios.

- **Servidores de AD.** Estos son los controladores de dominio implementar servicios de directorio (AD DS) que se ejecuta como máquinas virtuales en la nube. Estos servidores hospedan un bosque que contiene uno o más dominios, independientes de esos local encuentra.

- **Relación de confianza unidireccional.** El ejemplo en el diagrama muestra una confianza unidireccional desde el dominio en la nube para el dominio local. Esta relación permite a los usuarios locales acceso a los recursos del dominio en la nube, pero no al revés. Se trata de un caso común. Sin embargo, puede crear una confianza bidireccional si los usuarios de la nube también necesitan tener acceso a los recursos locales.

- **Subred de Active Directory.** Los servidores de AD DS se hospedan en una subred independiente. Reglas de GSN protegen los servidores de AD DS y pueden proporcionar un firewall contra el tráfico de forma inesperada.

- **Subred del nivel de Web**, **subred de nivel empresarial**y **subred de nivel de datos**. Estas subredes hospedar los servidores y componentes que se ejecutan aplicaciones en la nube. Para obtener más información, vea [Ejecutar máquinas virtuales para una arquitectura de N niveles en Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Los recursos y máquinas virtuales de esta subred se encuentran en el dominio de la nube.

- **Puerta de enlace de azure**. La puerta de enlace Azure proporciona una conexión entre la red local y VNet de Azure. Puede ser una [conexión VPN] [ azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Para obtener más información, vea [implementar una arquitectura de red seguro híbrido en Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recomendaciones

Esta sección proporciona una lista de recomendaciones basadas en los componentes básicos necesarios para implementar la arquitectura básica. Estas recomendaciones cubren:

- Configuración de suma, y

- Configuración de la relación de confianza.

Es posible que tenga requisitos adicionales o diferentes de los que se describen aquí. Puede usar los elementos de esta sección como punto de partida para considerar cómo personalizar la arquitectura de su propio sistema.

### <a name="adds-recommendations"></a>Agrega recomendaciones

Para obtener recomendaciones específicas de implementación de Active Directory en la nube, consulte el documento [Ampliar Active Directory a Azure][extending-ad-to-azure]. El artículo [directrices para implementar Active Directory de Windows Server en Azure máquinas virtuales de Windows] [ ad-azure-guidelines] contiene información detallada adicional.

### <a name="trust-recommendations"></a>Recomendaciones de confianza

Los dominios locales se encuentran dentro de un bosque distinto de los dominios en la nube. Para habilitar la autenticación de usuarios locales en la nube, los dominios en la nube deben confiar en el dominio de inicio de sesión del bosque local. Del mismo modo, si la nube proporciona un dominio de inicio de sesión para usuarios externos, puede ser necesario para el bosque local debe confiar en el dominio de la nube.

Puede establecer relaciones de confianza en el nivel de bosque mediante la [creación de confianzas de bosque][creating-forest-trusts], o en el nivel de dominio mediante la [creación de confianzas externas][creating-external-trusts]. Una nivel de confianza de bosque crea una relación entre todos los dominios en dos bosques. Una nivel de confianza del dominio externo sólo crea una relación entre dos dominios especificados. Solo debe crear confianzas de nivel de dominio externo entre dominios en diferentes bosques.

Confianzas pueden ser unidireccionales (unidireccional) o bidireccional (bidireccional):

- Una confianza unidireccional permite a los usuarios en un dominio o bosque (conocido como *entrante* del dominio o bosque) para tener acceso a los recursos que llevan a cabo en otra ( *salientes* de dominio o bosque). 

- Confianza bidireccional permite a los usuarios de dominio o bosque tener acceso a los recursos contenidos en el otro.

La siguiente tabla resume las configuraciones de confianza para algunos escenarios simples:

| Escenario | Confiar en local | Confianza de nube |
|----------|-------------------|-------------|
| Los usuarios necesitan tener acceso a los recursos en la nube en local, pero no viceversa | Unidireccional | Unidireccional, salientes |
| Los usuarios en la nube requieren acceso a los recursos que se encuentran en local, pero no viceversa | Unidireccional, salientes | Unidireccional |
| Los usuarios en la nube y local requiere acceso a los recursos en la nube y local | Bidireccional, entrantes y salientes | Bidireccional, entrantes y salientes |

## <a name="security-considerations"></a>Consideraciones de seguridad

Nivel bosques son transitivos. Si establece una nivel de confianza de bosque entre un bosques local y una en la nube, esta confianza se extiende a otros nuevos dominios creados en cualquiera de los bosques. Si utiliza dominios para proporcionar separación por motivos de seguridad, considere la posibilidad de crear relaciones de confianza en el nivel de dominio solo. Confianzas de nivel de dominio son intransitivas.

Para consideraciones de seguridad de AD específicas, vea la sección de *Consideraciones de seguridad* de [Ampliación de Active Directory para Azure][extending-ad-to-azure].

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Implementar al menos dos controladores de dominio para cada dominio. Esto permite la replicación automática entre servidores. Crear una disponibilidad para las VM que actúan como servidores de AD tratamiento de cada dominio. Asegúrese de que hay al menos dos servidores en el conjunto. 

Además, considere la posibilidad de designar uno o varios de los servidores de cada dominio como [patrones de operaciones en espera] [ standby-operations-masters] en caso de que se produce un error en la conectividad con un servidor que actúa como una función FSMO.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

AD es automáticamente scalable controladores de dominio que forman parte del mismo dominio. Las solicitudes se distribuyen entre todos los controladores de un dominio. Puede agregar otro controlador de dominio y sincroniza automáticamente con el dominio. No configure un equilibrador de carga independiente para dirigir el tráfico a los controladores del dominio. Asegúrese de que todos los controladores de dominio tienen recursos de memoria y almacenamiento suficientes para controlar la base de datos de dominio. Dar el mismo tamaño de todas las máquinas virtuales de controlador de dominio.

## <a name="management-and-monitoring-considerations"></a>Administración y supervisión consideraciones

Para obtener información sobre la administración y supervisión consideraciones, consulte las secciones equivalentes de [Ampliación de Active Directory para Azure][extending-ad-to-azure]. 

Para obtener más información, vea [Supervisión de Active Directory][monitoring_ad]. Puede instalar herramientas como [Microsoft Systems Center] [ microsoft_systems_center] en un servidor de supervisión de la subred de administración para ayudar a realizar estas tareas. 

## <a name="solution-components"></a>Componentes de la solución

Una secuencia de comandos de solución de ejemplo, [Implementar ReferenceArchitecture.ps1][solution-script], está disponible que puede usar para implementar la arquitectura que sigue las recomendaciones descritas en este artículo. Esta secuencia de comandos utiliza plantillas de administrador de recursos de Azure. Las plantillas están disponibles como un conjunto de bloques de creación fundamentales, cada una de las cuales realiza una acción específica, como crear un VNet o configurar un GSN. El propósito de la secuencia de comandos es organizar la implementación de la plantilla.

Las plantillas se con parámetros con los parámetros que llevan a cabo en archivos independientes de JSON. Puede modificar los parámetros de estos archivos para configurar la implementación para satisfacer sus propios requisitos. No es necesario modificar las plantillas de sí mismos. No debe cambiar los esquemas de los objetos en los archivos de parámetros.

Al editar las plantillas, crear objetos que siguen las convenciones de nomenclatura descritas en [Recomendado convenciones de nomenclatura para los recursos de Azure][naming-conventions].

La solución de ejemplo crea y configura un entorno en la nube que implementa un dominio denominado *treyresearch.com*. Este entorno consta de la puerta de enlace VPN de subred y servidores, DMZ, nivel de web, nivel empresarial y componentes de nivel de acceso de datos, de suma y nivel de administración. La solución de ejemplo también incluye una configuración opcional para crear un entorno local simulado con su propio dominio, *contoso.com*. La solución incluye secuencias de comandos que establecer una relación de confianza a través de estos dominios que permite a los usuarios locales tener acceso a objetos en el dominio *treyresearch.com* en la nube.

Las secciones siguientes describen los elementos de las instalaciones y configuraciones de la nube.

### <a name="on-premises-components"></a>Componentes de local

>[AZURE.NOTE] Estos componentes no son el enfoque principal de la arquitectura que se describe en este documento y se proporcionan simplemente para dar a una oportunidad de probar el entorno de nube de forma segura, en lugar de usar un entorno de producción real. Por este motivo, esta sección solo resumen los archivos de parámetro de clave. Puede modificar la configuración, como las direcciones IP o los tamaños de las máquinas virtuales, pero es aconsejable dejar muchos de los demás parámetros sin cambios.

Este entorno consta de un bosque de AD del dominio *contoso.com* . El dominio contiene dos servidores agrega con direcciones IP 192.168.0.4 y 192.168.0.5. Estos dos servidores también ejecutan el servicio DNS. La cuenta de administrador local en ambas máquinas virtuales se denomina `testuser` con contraseña `AweS0me@PW`. Además, se establece la configuración de una puerta de enlace VPN para conectarse a la VNet en la nube. Puede modificar la configuración mediante la edición de los siguientes archivos JSON ubicados en los [**parámetros o los**] [ on-premises-folder] carpeta:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Este archivo define el espacio de direcciones de red para el entorno local.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Este archivo contiene la configuración para las VM local agrega servicios de hospedaje. De forma predeterminada, se crean dos máquinas virtuales de *estándar-DS3-v2* .

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** y ** [connection.parameters.json][on-premises-connection-parameters]**. Estos archivos contienen la configuración de la conexión VPN a la puerta de enlace VPN de Azure en la nube, incluida la clave compartida que se utilizará para proteger el tráfico atraviesa la puerta de enlace.

Los archivos en la carpeta restantes contienen la información de configuración utilizada para crear el dominio local (*contoso.com*) con esta infraestructura. Usarlos para instalar agrega, configuración de DNS y crear el bosque local.

La solución también usa la siguiente secuencia de comandos denominado [entrante trust.ps1][incoming-trust], que se ejecuta en un equipo en el dominio local:

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Esta secuencia de comandos agrega las direcciones IP de los servidores de AD DS en la nube (consulte la sección siguiente) para el servicio DNS local y, a continuación, se utiliza el [netdom] [ netdom] comando para crear una relación de confianza unidireccional entrante del dominio en la nube (*treyresearch.com*).

### <a name="cloud-components"></a>Componentes de nube

Estos componentes forman el núcleo de esta arquitectura. Que la infraestructura del dominio de *treyresearch.com* de instalación y crean las relaciones de confianza con el dominio de *contoso.com* local. Los [**parámetros o azure**] [ azure-folder] carpeta contiene los archivos de parámetro siguiente para configurar estos componentes:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Este archivo define la estructura de la VNet para las máquinas virtuales y otros componentes en la nube. Incluye valores, como el nombre, el espacio de direcciones, subredes y las direcciones de los servidores DNS necesarios. Las direcciones de DNS en esta referencia de ejemplo muestra las direcciones IP de los servidores DNS locales y también en el servidor DNS de Azure predeterminado. Modificar estas direcciones para hacer referencia a su propia configuración DNS si no usa el entorno local de ejemplo:
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Este archivo configura las VM ejecuta agrega en la nube. La configuración consta de dos máquinas virtuales. Cambiar el nombre de usuario administrador y la contraseña en el `virtualMachineSettings` sección y, opcionalmente, puede modificar el tamaño de máquina virtual para que coincidan con los requisitos del dominio:

    Para obtener más información, vea [Ampliación de Active Directory para Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [Agregar-agrega-dominio-controller.parameters.json][add-adds-domain-controller-parameters]**. Este archivo contiene la configuración para crear el dominio *treyresearch.com* que ocupan los servidores agrega. Utiliza extensiones personalizadas que establecen el dominio y agregar los servidores agrega a él. A menos que cree servidores agrega adicionales (en cuyo caso debe agregarlas a la `vms` matriz), cambiar sus nombres predeterminada o si desea crear un dominio con un nombre diferente, no es necesario modificar este archivo.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Este archivo contiene los valores que se usan para crear la puerta de enlace VPN de Azure en la nube que se usa para conectarse a la red local. Debe modificar la `sharedKey` valor en el `connectionsSettings` sección que coincida con el dispositivo VPN local. Para obtener más información, vea [implementar una arquitectura de red híbrida con Azure y VPN local][hybrid-azure-on-prem-vpn].

- ** [dmz private.parameters.json] [ dmz-private-parameters] ** y ** [dmz public.parameters.json ] [ dmz-public-parameters] **. Estos archivos configuran entrante (público) salientes (privadas) laterales y de las máquinas virtuales que forman parte de la DMZ, proteger los servidores de la nube. Para obtener más información sobre estos elementos y su configuración, vea [implementar una DMZ entre Azure e Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [equilibrador de carga web.parameters.json][loadBalancer-web-parameters]**, ** [equilibrador de carga biz.parameters.json][loadBalancer-biz-parameters]**, y ** [equilibrador de carga data.parameters.json][loadBalancer-data-parameters]**. Estos archivos parámetros contienen las especificaciones de la máquina virtual de los niveles de acceso a web, business y datos y configurar equilibradores de carga para cada nivel. Estas son las VM que hospedan las aplicaciones web y bases de datos y a continuación, realizan las cargas de trabajo de la empresa para la organización. Las máquinas virtuales en el nivel de web se agregan al dominio en la nube mediante los valores especificados en el ** [web-vm-dominio-join.parameters.json] [ web-vm-domain-join-parameters] ** archivo.

    Cada archivo contiene dos conjuntos de parámetros de configuración. La `virtualMachineSettings` sección define las máquinas virtuales que hospeda el servicio ADFS en la nube. De forma predeterminada, la secuencia de comandos crea dos de estas máquinas virtuales en el mismo conjunto de disponibilidad. Los siguientes fragmentos, muestran las partes relevantes del archivo *web.parameters.json del equilibrador de carga* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    Puede modificar el tamaño y el número de máquinas virtuales en cada nivel según sus necesidades.

    La `loadBalancerSettings` sección proporciona la descripción del equilibrador de carga para estas máquinas virtuales. El equilibrador de carga pasa tráfico que aparece en los puertos 80 (HTTP) y 443 (HTTPS) a uno o de las máquinas virtuales. 

    >[AZURE.NOTE] La regla para el puerto 80 usa una conexión de TCP en lugar de HTTP. Esto es porque la instalación de IIS en el nivel de web está configurada para admitir sólo la autenticación de Windows. Autenticación anónima está deshabilitada. Intentar de *ping* puerto 80 en una conexión HTTP produce un error 401 (no autorizado), mientras que mediante una conexión TCP solo detecta si el puerto está activo:

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Este archivo contiene la configuración de la casilla salto y administración máquinas virtuales. Solo es posible tener inicio de sesión y acceso administrativo a las VM en la web, business y niveles de datos desde el cuadro saltar. De forma predeterminada, la secuencia de comandos crea una sola *Standard_DS1_v2* VM, pero se puede modificar este archivo para crear máquinas virtuales más grandes o más si la carga de trabajo de administración es probable que sea significativa.

La configuración también usa el [trust.ps1 saliente] [ outgoing-trust] secuencia de comandos que se muestra a continuación para crear una confianza de salida unidireccional con el dominio *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Esta secuencia de comandos es similar a la secuencia de comandos de *trust.ps1 entrante* que se ha descrito anteriormente. Agrega las direcciones IP de las instalaciones en servidores de AD DS al servicio DNS local y, a continuación, se utiliza el [netdom] [ netdom] comando para crear la relación de confianza saliente.

## <a name="solution-deployment"></a>Implementación de soluciones

La solución supone los siguientes requisitos previos:

- Tiene una suscripción existente de Azure en la que puede crear grupos de recursos.

- Haya descargado e instalado la versión más reciente de Powershell de Azure. Consulte [aquí] [ azure-powershell-download] para obtener instrucciones.

Para ejecutar la secuencia de comandos que se implementa la solución:

1. Mover a carpeta apropiada en el equipo local y cree las subcarpetas siguientes:

    - Secuencias de comandos

    - Las secuencias de comandos y parámetros

    - Parámetros de secuencias de comandos y línea

    - Parámetros de secuencias de comandos o Azure

2. Descargar la [Implementación ReferenceArchitecture.ps1] [ solution-script] archivo a la carpeta de secuencias de comandos

3. Descargar el contenido de los [parámetros o los] [ on-premises-folder] a la carpeta de secuencias de comandos y parámetros/local:

4. Descargar el contenido de los [parámetros o azure] [ azure-folder] a la carpeta de secuencias de comandos y parámetros/Azure.

5. Editar el archivo de implementación ReferenceArchitecture.ps1 en la carpeta de secuencias de comandos y cambie las siguientes líneas para especificar los grupos de recursos que se deben creados o utilizados para mantener los recursos creados por la secuencia de comandos:

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Editar los archivos de parámetros en las carpetas de secuencias de comandos y parámetros/Azure y secuencias de comandos, parámetros o local. Actualizar las referencias de grupo de recursos en estos archivos para que coincida con los nombres de los grupos de recursos asignados a las variables en el archivo de implementación ReferenceArchitecture.ps1. La siguiente tabla muestra qué archivos parámetro hacen referencia a qué grupo de recursos. Los grupos de recursos *ra-adfs-carga de trabajo-c*, *ra-adfs-seguridad-c*, *ra-adfs-agrega-c*, *ra-adfs-adfs-c*y *ra-adfs-proxy-c* solo se usan en la secuencia de comandos de PowerShell y no aparecen en los archivos de parámetros.

  	|Grupo de recursos|Archivos de parámetro|
  	|--------------|--------------|
  	|RA-adtrust-local-c|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adtrust-red-c|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-Public.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*dos veces*)

    Además, establecer la configuración de la local y la nube componentes, como se describe en los [Componentes de la solución] [ solution-components] sección.

7. Abra una ventana de PowerShell de Azure, mover a la carpeta de secuencias de comandos y ejecute el siguiente comando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Reemplazar `<subscription id>` con su identificador de suscripción de Azure.

    Para `<location>`, especifique un área de Azure, como por ejemplo `eastus` o `westus`.

    La `<mode>` parámetro puede tener uno de los siguientes valores:

    - `Onpremise`, para crear el entorno local simulada.

    - `Infrastructure`, para crear la infraestructura de VNet y abandonarán el cuadro en la nube.

    - `CreateVpn`, para generar la puerta de enlace de red virtual Azure y conectarse a la red local.

    - `AzureADDS`, para crear las máquinas virtuales que actúe como agrega servidores, implementar Active Directory en estas máquinas virtuales y crear el dominio en la nube.

    - `WebTier`, lo que crea la web máquinas virtuales de nivel y equilibrador de carga.

    - `Prepare`, que realiza todas las tareas anteriores. **Esta es la opción recomendada si está creando una implementación completamente nueva y no tiene una infraestructura local existente.**

    - `Workload`Para crear el nivel de datos empresariales y máquinas virtuales y equilibradores de carga. Estas máquinas virtuales no se incluye como parte de la `Prepare` opción.

    >[AZURE.NOTE] Si utiliza la `Prepare` opción, la secuencia de comandos tarda varias horas en completarse.

8.  Si está utilizando la configuración local de ejemplo:

    1. Conectar con el cuadro saltar (*ra-adtrust-administración-vm1* en el grupo de recursos de *ra-adtrust-seguridad-c* ). Inicie sesión como *usuario de prueba* con contraseña *AweS0me@PW*.

    2.  En el cuadro saltar, abra una sesión RDP en la máquina virtual primera en el dominio *contoso.com* (el dominio local). Este VM tiene la dirección IP 192.168.0.4. El nombre de usuario es *contoso\testuser* con contraseña *AweS0me@PW*.

    3. Descargar el [correo entrante trust.ps1] [ incoming-trust] secuencia de comandos y ejecútela para crear la confianza de entrada desde el dominio *treyresearch.com* .

9. Si está utilizando su propia infraestructura local:

    1. Descargar el [correo entrante trust.ps1] [ incoming-trust] secuencia de comandos.

    2. Editar la secuencia de comandos y reemplace el valor de la `$TrustedDomainName` variable con el nombre de su propio dominio.

    3. Ejecute la secuencia de comandos.

10. Desde el cuadro saltar, conectarse a la primera VM en el dominio de *treyresearch.com* (el dominio en la nube). Este VM tiene la dirección IP 10.0.4.4. El nombre de usuario es *treyresearch\testuser* con contraseña *AweS0me@PW*.

11. Descargar el [trust.ps1 saliente] [ outgoing-trust] secuencia de comandos y ejecútela para crear la confianza de entrada desde el dominio *treyresearch.com* . Si está utilizando sus propios equipos locales, editar el script en primer lugar. Establecer el `$TrustedDomainName` variable el nombre del dominio local y especifique las direcciones IP de los servidores de AD DS para este dominio en el `$TrustedDomainDnsIpAddresses` variable.

12. En un equipo local, realice los pasos descritos en el artículo [sobre cómo comprobar una confianza] [ verify-a-trust] para determinar si se ha configurado correctamente la relación de confianza entre los dominios *contoso.com* y *treyresearch.com* . Debe esperar unos minutos después de completar los pasos anteriores antes de que se puedan validar la confianza.

Los pasos opcionales restantes muestran cómo determinar si la confianza del dominio funciona como se esperaba. Estos pasos requieren que tenga acceso a un equipo de desarrollo con Visual Studio instalado.

1.  En la ventana de Azure PowerShell, ejecute el comando siguiente para crear el nivel de web si no configuró anteriormente (mediante el uso de la `Prepare` opción):

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Este comando crea el nivel de web y agrega las máquinas virtuales al dominio *treyresearch.com* .

2. Con Visual Studio en el equipo de desarrollo, cree una aplicación Web ASP.NET denominada *TreyResearchWebApp*. Use .NET Framework 4.5.2.

3. Seleccione la plantilla *MVC* y cambie la autenticación para *La autenticación de Windows*. No crear un servicio de aplicación en la nube.

3. Crear y ejecutar la aplicación para probar la autenticación. Compruebe que el nombre de usuario de Windows actual aparece en la barra de menús en la parte superior de la página, hacia la derecha.

4. Cierre el Explorador de Internet.

5. En la ventana *Explorador de soluciones* , haga clic en el proyecto TreyResearchWebApp, haga clic en *Publicar*.

6. En la ventana de la *Publicación Web* , haga clic en *personalizado*. Crear un perfil personalizado denominado *TreyResearchWebApp*.

7. En la página de *conexión* , establezca el *método de publicación* en el *Sistema de archivos* y especifique una carpeta denominada *TreyResearchWebApp*, ubicado en una ubicación adecuada en el equipo de desarrollo.

8. En la página *configuración* , establezca la *configuración* en la *versión*.

9. En la página de *vista previa* , haga clic en *Publicar*.

10. Conectarse a cada VM en el nivel de web por separado (mediante el cuadro saltar) y realice las siguientes tareas. Las direcciones IP de la web nivel VM son 10.0.1.4 y 10.0.1.5. El nombre de usuario para ambos máquinas virtuales es *treyresearch\testuser* con contraseña *AweS0me@PW*:

    1. Copie la carpeta *TreyResearchWebApp* y su contenido desde el equipo de desarrollo a la carpeta *C:\inetpub* .

    2. Con la consola del Administrador de Internet Information Services (IIS), vaya al *sitio Web de Sitios\Nombre* en el equipo.

    3. En el panel de *acciones* , haga clic en *Configuración básica*y cambie la ruta de acceso física del sitio web para *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. En el panel de *Vista de características* , haga doble clic en *autenticación*. Compruebe que ha activado la *Autenticación de Windows* y se deshabilita la *Autenticación anónima* .

11. desde un equipo local, abra Internet Explorer y vaya al sitio web en http://10.0.1.254 (esta es la dirección del equilibrador de carga de nivel de web).

12. En el cuadro de diálogo *Seguridad de Windows* , escriba las credenciales de un usuario en el dominio local. Especifique un nombre de usuario que no existe en el dominio *treyresearch* . Si está utilizando el entorno local simulada primero de crear un usuario en el dominio de *contoso* y especifique las credenciales de este usuario.

13. Cuando aparezca la página de inicio, compruebe que el dominio correcto y el nombre de usuario aparecen en la barra de menús en la parte superior de la página, hacia la derecha.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las prácticas recomendadas para [extender su dominio local agrega Azure][adds-extend-domain]

- Obtenga información sobre los procedimientos recomendados para [crear una infraestructura ADFS] [ adfs] en Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Arquitectura de red híbrido con distintos dominios de AD segura"