<properties 
    pageTitle="Geo distribuido escala con entornos de servicio de aplicaciones" 
    description="Obtenga información sobre cómo ajustar horizontalmente aplicaciones mediante distribución geo con el Administrador de tráfico y entornos de aplicación de servicio." 
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
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Geo distribuido escala con entornos de servicio de aplicaciones

## <a name="overview"></a>Información general ##
Escenarios de aplicación que requieren muy alta escala pueden superar la capacidad de recursos de cálculo disponible en una sola implementación de una aplicación.  Aplicaciones de voto, eventos deportivos y entretenimiento televisadas eventos son ejemplos de escenarios que requieren muy alta escala. Pueden se cumplen los requisitos de alta escala horizontalmente escalado aplicaciones, con varias implementaciones de aplicación que se realizan dentro de una única región, así como a través de zonas para controlar los requisitos de carga máxima.

Los entornos de servicio de aplicación son una plataforma ideal para escalada horizontal.  Una vez una aplicación entorno de servicio se ha seleccionado la configuración que pueden admitir una tasa de solicitud conocidos, los desarrolladores pueden implementar adicionales entornos de servicio de aplicación de forma "plantilla genérica" para lograr una capacidad de carga máximo deseado.

Por ejemplo, supongamos una aplicación que se ejecuta en una configuración de entorno de aplicación de servicio se ha probado para controlar las solicitudes de 20 KB por segundo.  Si la capacidad de carga máximo deseado es 100 KB RPS, entornos de servicio de la aplicación de cinco (5) puede crean y se configuran para asegurarse de que la aplicación puede controlar la carga proyectada máxima.

Puesto que los clientes normalmente acceso aplicaciones mediante un dominio personalizado (o de cortesía), los desarrolladores necesitan una manera de distribuir peticiones de aplicación en todas las instancias del entorno de servicio de aplicación.  Es una excelente manera de hacerlo resolver el dominio personalizado con un [perfil de administrador de tráfico de Azure][AzureTrafficManagerProfile].  El perfil de administrador de tráfico se puede configurar para que apunte a todos los entornos de servicio de aplicación individuales.  Administrador de tráfico controlará automáticamente clientes distribuir a través de todos los entornos de servicio de la aplicación en función de la configuración en el perfil de administrador de tráfico de equilibrio de carga.  Este método funciona independientemente de si todos los entornos de aplicación de servicio de implementado en una sola región Azure implementados en todo el mundo en diferentes regiones de Azure.

Además, puesto que los clientes tener acceso a aplicaciones a través del dominio de cortesía, los clientes desconocen el número de la aplicación de servicio entornos ejecuta una aplicación.  Como resultado los desarrolladores pueden rápida y fácilmente agregar y quitar, entornos de servicio de aplicación en función de la carga de tráfico observados.

El siguiente diagrama conceptual muestra una aplicación de escalar horizontalmente en tres entornos de servicio de aplicación dentro de una única región.

![Arquitectura conceptual][ConceptualArchitecture] 

El resto de este tema lo guiará por los pasos necesarios para configurar una topología distribuida para la aplicación de ejemplo con varios entornos de servicio de aplicación.

## <a name="planning-the-topology"></a>Planear la topología ##
Antes de crear un espacio de aplicación distribuida, resulta útil para tener algunas partes información con antelación.

- **Dominio personalizado para la aplicación:**  ¿Qué es el nombre de dominio personalizado que los clientes usarán para tener acceso a la aplicación?  Para la aplicación de muestra el nombre de dominio personalizado es *www.scalableasedemo.com*
- **Dominio tráfico Manager:**  Un nombre de dominio se debe elegir al crear un [perfil de administrador de tráfico de Azure][AzureTrafficManagerProfile].  Este nombre se combinarán con el sufijo *trafficmanager.net* para registrar una entrada de dominio es administrada por el Administrador de tráfico.  Para la aplicación de ejemplo, el nombre elegido es *scalable demostración de ase*.  Como resultado, el nombre de dominio completo es administrado por el Administrador de tráfico es *scalable ase-demo.trafficmanager.net*.
- **Estrategia de ajuste de escala de la superficie de la aplicación:**  ¿Se distribuirá el tamaño de la aplicación en distintos entornos de servicio de aplicación en una sola región?  ¿Varias regiones?  ¿Una mezcla y correspondencia de ambos métodos?  La decisión debe basarse en las expectativas del procedencia de tráfico de cliente, así como cómo puede escalar el resto de la infraestructura de back-end de apoyo de una aplicación.  Por ejemplo, con una aplicación independiente de 100%, una aplicación se puede masivamente escalar mediante una combinación de varios entornos de servicio de aplicación por región Azure, multiplicada por la aplicación de servicio entornos implementado en diferentes regiones de Azure.  Con 15 + públicas Azure regiones disponibles para elegir, los clientes consiguen pueden crear un tamaño de la aplicación de gran escala de todo el mundo.  Para la aplicación de ejemplo que usa para este artículo, se crearon tres entornos de servicio de aplicación en una sola región de Azure (sur Central de EE).
- **Convención de nomenclatura para los entornos de aplicación de servicio:**  Cada entorno de servicio de aplicación requiere un nombre único.  Más allá de uno o dos entornos de servicio de aplicación es útil tener una convención de nomenclatura para ayudarle a identificar cada entorno de servicio de aplicación.  Para la aplicación de ejemplo se utiliza una convención de nomenclatura simple.  Los nombres de los tres entornos de servicio de aplicación son *fe1ase*, *fe2ase*y *fe3ase*.
- **Convención de nomenclatura para las aplicaciones:**  Puesto que se implementará varias instancias de la aplicación, se necesita un nombre para cada instancia de la aplicación implementada.  Una característica de poco conocido pero muy práctica de entornos de aplicación de servicio es que se puede utilizar el mismo nombre de aplicación en distintos entornos de servicio de aplicación.  Dado que cada entorno de servicio de aplicación tiene un sufijo de dominio único, pueden elegir los desarrolladores volver a utilizar el mismo nombre de aplicación en cada entorno.  Por ejemplo, un desarrollador puede tener aplicaciones denominadas como sigue: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etcetera.  Aunque la aplicación de ejemplo de cada instancia de la aplicación también tiene un nombre único.  Los nombres de instancia de aplicación utilizados son *webfrontend1*, *webfrontend2*y *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>Configurar el perfil de administrador de tráfico ##
Una vez que se implementan varias instancias de una aplicación en varios entornos de servicio de aplicación, se pueden registrar las instancias de aplicación individuales con el Administrador de tráfico.  Para la aplicación de ejemplo un administrador de tráfico perfil es necesario para *scalable demo.trafficmanager.net de ase* que puede redirigir a los clientes a cualquiera de las instancias de aplicación implementada siguientes:

- **webfrontend1.fe1ase.p.azurewebsites.net:**  Una instancia de la aplicación de ejemplo implementada en el primer entorno de servicio de aplicación.
- **webfrontend2.fe2ase.p.azurewebsites.net:**  Una instancia de la aplicación de ejemplo implementada en el segundo entorno de servicio de aplicación.
- **webfrontend3.fe3ase.p.azurewebsites.net:**  Una instancia de la aplicación de ejemplo implementada en el entorno de servicios de aplicación de terceros.

Es la manera más sencilla de registrar varios extremos de servicio de la aplicación de Azure ejecutándose en la **misma** región Azure, con la Powershell [admite tráfico administrador de recursos de Azure][ARMTrafficManager].  

El primer paso es crear un perfil de administrador de tráfico de Azure.  El código siguiente muestra cómo se creó el perfil para la aplicación de ejemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observe cómo se estableció el parámetro *RelativeDnsName* a *scalable demostración de ase*.  Se trata de cómo se crea y asociados a un perfil de administrador de tráfico de nombre de dominio *scalable ase-demo.trafficmanager.net* .

El parámetro *TrafficRoutingMethod* define la directiva de administrador de tráfico usará para determinar cómo distribuir la carga de cliente a través de todos los extremos disponibles de equilibrio de carga.  En este ejemplo la *Weighted* método se ha elegido.  Esto se producirá que se extienden a través de todos los extremos de la aplicación registrada basándose en las ponderaciones relativas asociadas a cada extremo de solicitudes de cliente. 

Con el perfil que se creó, cada instancia de la aplicación se agrega al perfil como un extremo de Azure nativo.  El código siguiente obtiene una referencia a cada aplicación web de front-end y, a continuación, agrega cada aplicación como un extremo de administrador de tráfico mediante el parámetro *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Observe que existe una llamada a *Agregar AzureTrafficManagerEndpointConfig* para cada instancia de aplicación individuales.  El parámetro *TargetResourceId* en cada comando Powershell hace referencia a una de las tres instancias de aplicación implementada.  El perfil de administrador de tráfico extender la carga a todos los tres extremos registrados en el perfil.

Todos los tres extremos utilizan el mismo valor (10) para el parámetro de *peso* .  Resultado en Administrador de tráfico difundir solicitudes de cliente en todas las instancias de aplicación tres relativamente uniformemente. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Selecciona dominio personalizado de la aplicación en el dominio del Administrador de tráfico ##
Es el último paso es necesario para que señalen el dominio personalizado de la aplicación en el dominio del Administrador de tráfico.  Para la aplicación de ejemplo, esto significa que señala *www.scalableasedemo.com* *demo.trafficmanager.net scalable de ase*.  Este paso debe completarse con el registrador que administra el dominio personalizado.  

Usar herramientas de administración del registrador de su dominio, un CNAME registros deben crearse que señala el dominio personalizado en el dominio del Administrador de tráfico.  La imagen siguiente muestra un ejemplo del aspecto de esta configuración CNAME:

![CNAME de dominio personalizado][CNAMEforCustomDomain] 

Aunque no se trata en este tema, recuerde que debe tener el dominio personalizado registrado con ella también cada instancia de aplicación individuales.  En caso contrario, si hace una solicitud a una instancia de la aplicación y la aplicación no tiene el dominio personalizado registrado con la aplicación, la solicitud se producirá un error.  

En este ejemplo, el dominio personalizado es *www.scalableasedemo.com*, y cada instancia de aplicación tiene el dominio personalizado asociado.

![Dominio personalizado][CustomDomain] 

Para un resumen de registrar un dominio personalizado con aplicaciones de servicio de la aplicación de Azure, consulte el siguiente artículo sobre [cómo registrar dominios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Probando la topología distribuido ##
El resultado final de la configuración del Administrador de tráfico y DNS es que las solicitudes de *www.scalableasedemo.com* fluirá a través de la siguiente secuencia:

1. Un explorador o dispositivo realizará una búsqueda DNS para *www.scalableasedemo.com*
2. La entrada CNAME en el registrador de dominios hace que la búsqueda DNS se redirige al administrador de tráfico de Azure.
3. Se realiza una búsqueda DNS para *demo.trafficmanager.net scalable de ase* frente a uno de los servidores DNS del Administrador de tráfico de Azure.
4. En función de la directiva (el parámetro de *TrafficRoutingMethod* usado una versión anterior al crear el perfil de administrador de tráfico) de equilibrio de carga, Administrador de tráfico se seleccione uno de los extremos configurados y devolver el FQDN del extremo en el explorador o el dispositivo.
5.  Dado que el FQDN del extremo es la dirección Url de una instancia de aplicación ejecutando en un entorno de servicio de aplicación, el explorador o el dispositivo le pedirá que un servidor DNS de Microsoft Azure para resolver el FQDN en una dirección IP. 
6. El explorador o el dispositivo enviará la solicitud HTTP/S a la dirección IP.  
7. La solicitud llegarán a una de las instancias de aplicación que se ejecuta en uno de los entornos de aplicación de servicio.

La imagen de consola siguiente muestra una consulta DNS de dominio personalizado de la aplicación de ejemplo resolver correctamente a una instancia de aplicación que se ejecuta en uno de los tres entornos de servicio de aplicación de muestra (en este caso, el segundo de los tres entornos de servicio de aplicación):

![Búsqueda DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Información y vínculos adicionales ##
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Documentación en de Powershell que se [admiten tráfico administrador de recursos de Azure][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
