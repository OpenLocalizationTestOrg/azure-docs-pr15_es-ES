<properties 
    pageTitle="Cómo controlar el tráfico a un entorno de servicio de aplicaciones" 
    description="Obtenga información sobre cómo configurar reglas de seguridad de red para controlar el tráfico a un entorno de servicio de aplicación." 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Cómo controlar el tráfico a un entorno de servicio de aplicaciones

## <a name="overview"></a>Información general ##
Se puede crear un entorno de servicio de la aplicación en **ambos** una red virtual del Administrador de recursos de Azure, **o** una implementación clásica modelo [red virtual][virtualnetwork].  Una red virtual nueva y nueva subred pueden definirse en el momento en que se crea un entorno de servicio de aplicación.  Como alternativa, puede crearse un entorno de servicio de la aplicación en una red virtual existente y subred existente.  Con un cambio reciente realizado en junio de 2016, ahora se puede implementar ASEs en redes virtuales que utilizan intervalos de direcciones pública o espacios de direcciones de RFC1918 (es decir direcciones privadas).  Para obtener más detalles sobre la creación de un entorno de servicio de aplicación, vea [cómo crear un entorno de servicio de la aplicación][HowToCreateAnAppServiceEnvironment].

Un entorno de servicio de la aplicación siempre debe crearse dentro de una subred porque una subred proporciona un límite de red que se puede utilizar para bloquear el tráfico entrante detrás de los servicios y dispositivos ascendentes tal que solo se acepta el tráfico HTTP y HTTPS de direcciones IP precedentes específicas.

Tráfico de red entrante y saliente en una subred está controlado mediante un [grupo de seguridad de red][NetworkSecurityGroups]. Controlar el tráfico de entrada, se requiere la subred que contiene el entorno de aplicación de servicio de grupo de creación de reglas de seguridad de red en un grupo de seguridad de la red y, a continuación, asignar la seguridad de la red.

Una vez que un grupo de seguridad de red está asignado a una subred, tráfico de aplicaciones en el entorno de servicio de la aplicación es permitidos/bloqueados en función de la permitir y denegar reglas definidas en el grupo de seguridad de la red.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>Puertos de red que se utiliza en un entorno de servicio de aplicaciones ##
Antes de bloquear el tráfico de red con un grupo de seguridad de red, es importante conocer el conjunto de puertos de red obligatorios y opcionales utilizados por un entorno de servicio de aplicación.  Cerrar accidentalmente el tráfico a algunos puertos puede dar lugar a pérdida de funcionalidad en un entorno de servicio de aplicación.

A continuación se muestra una lista de puertos utilizados por un entorno de servicio de aplicación. Todos los puertos son **TCP**, a menos que se indique lo contrario claramente:

- 454: **obligatorio puerto** utilizadas la infraestructura de Azure para administrar y mantener entornos de servicio de aplicación a través de SSL.  Bloquee el tráfico a este puerto.  Este puerto está enlazado siempre a la dirección VIP pública de una ASE.
- 455: **obligatorio puerto** utilizadas la infraestructura de Azure para administrar y mantener entornos de servicio de aplicación a través de SSL.  Bloquee el tráfico a este puerto.  Este puerto está enlazado siempre a la dirección VIP pública de una ASE.
- 80: puerto para el tráfico HTTP entrante a aplicaciones que se ejecutan en planes de servicio de aplicación en un entorno de servicio de aplicación predeterminado.  En un ASE habilitado ILB, este puerto está enlazado a la dirección ILB de la ASE.
- 443: puerto para el tráfico SSL entrante a aplicaciones que se ejecutan en planes de servicio de aplicación en un entorno de servicio de aplicación predeterminado.  En un ASE habilitado ILB, este puerto está enlazado a la dirección ILB de la ASE.
- 21: canal de control FTP.  Este puerto se puede bloquear de forma segura si no se utiliza FTP.  En un ASE habilitado ILB, este puerto puede estar enlazado a la dirección ILB para un ASE.
- 990: canal de control para FTPS.  Este puerto se puede bloquear de forma segura si no se utiliza FTPS.  En un ASE habilitado ILB, este puerto puede estar enlazado a la dirección ILB para un ASE.
- 10020 10001: canales de datos FTP.  Al igual que con el canal de control, estos puertos pueden segura bloquearse si no se utiliza FTP.  En un ASE habilitado ILB, este puerto puede estar enlazado a la dirección ILB del ASE.
- 4016: se usa para depuración remota con Visual Studio 2012.  Este puerto se puede bloquear de forma segura si no se usa la característica.  En un ASE habilitado ILB, este puerto está enlazado a la dirección ILB de la ASE.
- 4018: se usa para depuración remota con Visual Studio de 2013.  Este puerto se puede bloquear de forma segura si no se usa la característica.  En un ASE habilitado ILB, este puerto está enlazado a la dirección ILB de la ASE.
- 4020: se usa para depuración remota con Visual Studio de 2015.  Este puerto se puede bloquear de forma segura si no se usa la característica.  En un ASE habilitado ILB, este puerto está enlazado a la dirección ILB de la ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS y conectividad saliente ##
En un entorno de servicio de aplicación para que funcione correctamente, se requiere acceso de salida a varios extremos. Una lista completa de los extremos externos utilizado un ASE se encuentra en la sección "Requiere conectividad de red" de este artículo de la [Configuración de red para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Entornos de servicio de aplicación requieren una infraestructura DNS válida configurada para la red virtual.  Si por cualquier motivo se cambia la configuración de DNS después de haber creado un entorno de servicio de aplicación, los desarrolladores pueden forzar un entorno de servicio de la aplicación para reanudar la nueva configuración de DNS.  Desencadenar un reinicio de entorno graduales mediante el icono "Reiniciar" ubicada en la parte superior del módulo de administración de entorno de aplicación de servicio en el [portal de Azure] [ NewPortal] hará que el entorno de recoger la nueva configuración de DNS.

También se recomienda que los servidores DNS personalizados en el vnet ser configuración antelación antes de crear un entorno de servicio de aplicación.  Si se cambia la configuración de DNS de la red virtual mientras se crea un entorno de servicio de aplicación, que da al error del proceso de creación de entorno de servicio de aplicación.  En un modo similar, si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace VPN y el servidor DNS es accesible o no está disponible, el proceso de creación del entorno de servicio de aplicación también funcionará.

## <a name="creating-a-network-security-group"></a>Crear un grupo de seguridad de red ##
Para obtener más detalles sobre cómo funcionan los grupos de seguridad de la red, vea la siguiente [información][NetworkSecurityGroups].  Resalta táctil en los detalles de los grupos de seguridad de red, con un enfoque en configurar y aplicar un grupo de seguridad de red en una subred que contiene un entorno de servicio de la aplicación.

**Nota:** Grupos de seguridad de red pueden configurarse gráficamente con el [Portal de Azure](https://portal.azure.com) o a través de PowerShell de Azure.

Grupos de seguridad de red se crea por primera vez como una entidad independiente asociada a una suscripción. Dado que se crean los grupos de seguridad de red en un área de Azure, asegúrese de que el grupo de seguridad de la red se crea en la misma región como el entorno de servicios de aplicación.

La siguiente muestra cómo crear un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Una vez creado un grupo de seguridad de red, una o más reglas de seguridad de red se agregan a él.  Dado que el conjunto de reglas puede cambiar con el tiempo, es aconsejable espaciar el esquema de numeración que se utiliza para las prioridades de la regla para que sea más fácil insertar reglas adicionales a lo largo del tiempo.

El ejemplo siguiente muestra una regla que concede acceso a los puertos de administración es necesario, la infraestructura de Azure para administrar y mantener un entorno de servicio de aplicación.  Tenga en cuenta que todo el tráfico de administración pase a través de SSL y está protegido por los certificados de cliente, por lo que aunque se abren los puertos son accesibles por cualquier entidad distinta de la infraestructura de administración de Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Cuándo bloquear el acceso a los puertos 80 y 443 "Ocultar" un entorno de servicio de aplicación detrás de dispositivos ascendentes o servicios, debe saber la dirección IP en sentido ascendente.  Por ejemplo, si usa un servidor de aplicaciones web (WAFS), el WAFS tendrá su propia dirección (o direcciones IP) que usa cuando el tráfico de proxy a un entorno de servicio de aplicación de nivel inferior.  Debe usar esta dirección IP en el parámetro *SourceAddressPrefix* de una regla de seguridad de la red.

En el ejemplo siguiente, el tráfico entrante de una dirección IP precede específica esté permitido explícitamente.  La dirección *1.2.3.4* se utiliza como un marcador de posición para la dirección IP de un WAFS en sentido ascendente.  Cambie el valor para que coincida con la dirección utilizada por su dispositivo ascendente o el servicio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Si se desea compatibilidad con FTP, las siguientes reglas se pueden utilizar como una plantilla para conceder acceso a los datos y el puerto de control FTP puertos de canal.  Puesto que FTP es un protocolo de estado, es podrán que no pueda enrutar el tráfico FTP a través de un dispositivo de proxy o firewall HTTP/HTTPS tradicional.  En este caso debe establecer el *SourceAddressPrefix* en un valor diferente: por ejemplo el intervalo de direcciones IP de programador o equipos de implementación que FTP se están ejecutando los clientes. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** puede cambiar el intervalo de puertos del canal de datos durante el período de vista previa.)

Si se utiliza la depuración remota con Visual Studio, las reglas siguientes muestran cómo conceder acceso.  Hay una regla independiente para cada versión compatible de Visual Studio, ya que cada versión utiliza un puerto diferente para la depuración remota.  Al igual que con el acceso FTP, tráfico de depuración remoto no fluya correctamente a través de un dispositivo de proxy o WAFS tradicional.  El *SourceAddressPrefix* en su lugar puede establecerse en el intervalo de direcciones IP de equipos de programador ejecuta Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Asignar un grupo de seguridad de la red a una subred ##
Un grupo de seguridad de la red tiene una regla de seguridad de forma predeterminada que rechaza el acceso a todo el tráfico externo.  El resultado de la combinación de las reglas de seguridad de red descritas anteriormente y la regla de seguridad predeterminados bloqueando el tráfico entrante, es que solo el tráfico de dirección de origen intervalos asociados con una acción de *Permitir* podrá enviar tráfico a aplicaciones que se ejecute en un entorno de servicio de aplicación.

Después de un grupo de seguridad de la red se rellena con las reglas de seguridad, debe asignarse a la subred que contiene el entorno de servicios de aplicación.  El comando de asignación hace referencia el nombre de la red virtual donde reside el entorno de servicios de aplicación, así como el nombre de la subred en la que se creó el entorno de servicios de aplicación.  

El ejemplo siguiente muestra un grupo de seguridad de red que se asigna a una red virtual y de subred:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Una vez que se realiza correctamente la asignación de grupo de seguridad de red (la asignación es operaciones de ejecución larga y puede tardar unos minutos en completarse), solo que coinciden con las reglas de *Permitir* el tráfico correctamente llegarán a aplicaciones en el entorno de servicios de aplicación.

Para que sean completas en el ejemplo siguiente se muestra cómo quitar y, por tanto, no asociar el grupo de seguridad de la red de la subred:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Consideraciones especiales para SSL de IP explícito ##
Si una aplicación está configurada con un IP-SSL explícito de direcciones (aplicable a ASEs que solo tienen una pública VIP), en lugar de usar la dirección IP predeterminada del entorno de servicio de aplicación, HTTP y HTTPS flujos de tráfico en la subred sobre un conjunto diferente de puertos distintos puertos 80 y 443.

Encontrará el par individual de puertos utilizados por cada dirección IP SSL de la interfaz de usuario del portal de módulo UX de detalles del entorno de servicio de aplicación.  Seleccionadas "todas las configuraciones"--> "Direcciones IP".  El módulo "Direcciones IP" muestra una tabla de todas las direcciones IP SSL configuradas explícitamente para el entorno de aplicación de servicio, junto con el par de puerto especial que se utiliza para redirigir el tráfico HTTP y HTTPS asociado con cada dirección IP SSL.  Es este par de puerto que debe usarse para los parámetros de DestinationPortRange al configurar reglas en un grupo de seguridad de la red.

Cuando una aplicación en un ASE está configurada para utilizar SSL IP, clientes externos no podrán ver y no es necesario preocuparse por la asignación de par puerto especial.  El tráfico a las aplicaciones fluirá normalmente a la dirección IP SSL configurada.  La conversión de par de puerto especial ocurre automáticamente internamente durante la sección final de enrutar el tráfico a la subred que contiene la ASE. 

## <a name="getting-started"></a>Introducción

Para empezar con entornos de servicio de aplicación, vea [Introducción a entorno de aplicación de servicio][IntroToAppServiceEnvironment]

Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para obtener detalles alrededor de forma segura conectarse a recursos de back-end desde un entorno de aplicación de servicio de aplicaciones, vea [Conectar forma segura a recursos de back-end desde un entorno de servicio de aplicación][SecurelyConnecttoBackend]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
