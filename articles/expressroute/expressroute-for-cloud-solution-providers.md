<properties
   pageTitle="Azure ExpressRoute para proveedores de soluciones de nube | Microsoft Azure"
   description="En este artículo se proporciona información para los proveedores de servicios de nube que desee incorporar los servicios de Azure y ExpressRoute en sus ofertas."
   documentationCenter="na"
   services="expressroute"
   authors="richcar"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="richcar"/>

# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute para proveedores de soluciones de nube (CSP)

Microsoft proporciona servicios de Hyper-escala para distribuidores tradicionales y distribuidores (CSP) poder disponer rápidamente de nuevos servicios y soluciones para sus clientes sin necesidad de invertir en desarrollar estos nuevos servicios. Para permitir que el proveedor de soluciones de nube (CSP) la capacidad de administrar directamente estos nuevos servicios, Microsoft ofrece programas y API que permiten el proveedor de administración de recursos de Microsoft Azure en nombre de sus clientes. Uno de los recursos es ExpressRoute. ExpressRoute permite el proveedor que conecte los recursos de cliente existentes a servicios de Azure. ExpressRoute es un vínculo de comunicaciones privadas de alta velocidad a servicios en Azure. 

ExpresRoute consta de un par de circuitos de alta disponibilidad que se han adjuntado a un solo cliente suscripciones y no pueden compartirse entre varios clientes. Debe finalizar cada circuito en un enrutador diferente para mantener la alta disponibilidad.

>[AZURE.NOTE] Hay ancho de banda y conexión mayúsculas en ExpressRoute lo que significa que grandes y complejas implementaciones requieren varios circuitos ExpressRoute para un único cliente.

Microsoft Azure proporciona un número creciente de los servicios que puede ofrecer a sus clientes.  Para tomar mejores ventajas de los servicios requieren el uso conexiones ExpressRoute para proporcionar alta velocidad baja acceso de latencia en el entorno de Microsoft Azure.

## <a name="microsoft-azure-management"></a>Administración de Microsoft Azure
Microsoft proporciona proveedores con la API para administrar las suscripciones de cliente Azure al permitir la integración de programación con sus propios sistemas de gestión de servicio. Las capacidades de administración compatibles pueden encontrarse [aquí](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Administración de recursos de Microsoft Azure
Según el contrato que tiene con su cliente determina cómo se administrará la suscripción. El CSP puede administrar directamente la creación y el mantenimiento de recursos o el cliente puede mantener el control de la suscripción de Microsoft Azure y crear los recursos de Azure como necesiten. Si su cliente administra la creación de recursos en su suscripción de Microsoft Azure se usan uno de dos modelos: modelo "Conectar a través" o "Directa a" modelo. Estos modelos se describen en detalle en las secciones siguientes.  

### <a name="connect-through-model"></a>Conectar a través de modelo

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

En el modelo mediante conectar, el CSP crea una conexión directa entre el centro de datos y la suscripción del cliente Azure. La conexión directa se realiza mediante ExpressRoute, conectarse a su red con Azure. A continuación, el cliente se conecta a su red. Este escenario requiere que el cliente pasa a través de la red CSP para tener acceso a servicios de Azure. 

Si su cliente tiene otras suscripciones Azure no administrados por la que, debería usar Internet o a su propia conexión privada para conectarse a dichos servicios que se aprovisione en la suscripción no CSP. 

Para administrar los servicios de Azure de CSP, se supone que el CSP tiene un almacén de identidad de cliente previamente establecidas que debería replicarse en Azure Active Directory para la administración de su suscripción de CSP a través de Administrate-On-Behalf-Of (AOBO). Controladores claves para este escenario incluyen donde un determinado socio o el proveedor de servicios tiene una relación establecida con el cliente, el cliente consumo de servicios de proveedor actualmente o el partner deseo proporcionar una combinación de proveedor hospeda y alojado de Azure soluciones para proporcionar flexibilidad y cliente dirección desafíos que no puede satisfacer CSP únicamente. Este modelo se muestra en **la figura**, a continuación.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Conectar a modelo

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-to.png)

En el modelo para conectar, el proveedor de servicios crea una conexión directa entre los centros de datos de sus clientes y la suscripción de Azure CSP aprovisionado con ExpressRoute sobre el cliente (cliente) red.

>[AZURE.NOTE] Para ExpressRoute el cliente necesarias para crear y mantener el circuito ExpressRoute.  

Este escenario de conectividad requiere que el cliente se conecta directamente a través de una red de cliente para tener acceso a la suscripción de Azure CSP administrados, usando una conexión de red directa que creó, posee y administra total o parcialmente por el cliente. Para estos clientes se supone que el proveedor no tiene actualmente establecido un almacén de identidades de cliente y el proveedor ¿ayudará al cliente replicar su identificación de la tienda en Azure Active Directory para la administración de su suscripción a través de AOBO. Controladores claves para este escenario incluyen donde un determinado socio o el proveedor de servicios tiene una relación establecida con el cliente, el cliente consumo de servicios de proveedor actualmente o el partner tiene deseo proporcionar servicios a los que se basan únicamente en hospedado Azure soluciones sin necesidad de centro de datos de proveedor existente o una infraestructura.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Elegir entre estas dos opciones se basan en las necesidades de su cliente y sus necesidades actual para proporcionar servicios de Azure. Controlan los detalles de estos modelos y el acceso basado en roles asociados, redes y patrones de diseño de identidad se tratan en detalles en los siguientes vínculos:
-   **Control de acceso en función de función (RBAC)** – RBAC se basa en Azure Active Directory.  Para obtener más información sobre RBAC Azure vea [aquí](../active-directory/role-based-access-control-configure.md).
-   **Redes** : trata sobre los distintos temas de las redes de Microsoft Azure.
-   **Azure Active Directory (AAD)** -AAD proporciona la administración de identidades de Microsoft Azure y 3er SaaS aplicaciones. Para obtener más información acerca de, consulte Azure AD [aquí](https://azure.microsoft.com/documentation/services/active-directory/).  


## <a name="network-speeds"></a>Velocidad de red
ExpressRoute es compatible con la velocidad de red de 50 Mb/s a 10Gb/s. Esto permite a los clientes comprar la cantidad de ancho de banda necesario para su entorno único.

>[AZURE.NOTE] Se puede aumentar el ancho de banda de red según sea necesario sin interrumpir las comunicaciones, pero para reducir la red velocidad requiere rompe hacia abajo el circuito y recrear a la menor velocidad de red.  

ExpressRoute admite la conexión de varios vNets a un único circuito ExpressRoute para una mejor utilización de las conexiones de alta velocidad. Un solo circuito ExpressRoute puede compartirse entre varias suscripciones Azure propiedad del mismo cliente.

## <a name="configuring-expressroute"></a>Configurar ExpressRoute
ExpressRoute puede estar configurado para admitir tres tipos de tráfico ([enrutamiento de dominios](#ExpressRoute-routing-domains)) sobre un único circuito ExpressRoute. Este tráfico se divide en Microsoft interconexión, interconexión pública de Azure e interconexión privada. Puede elegir uno o todos los tipos de tráfico se envíe a través de un solo circuito ExpressRoute o utilizar varios circuitos ExpressRoute dependiendo del tamaño de la ExpressRoute circuito y aislamiento necesarios para su cliente. La posición de seguridad de su cliente no permita tráfico público y privadas para recorrer sobre el mismo circuito.

### <a name="connect-through-model"></a>Conectar a través de modelo
En una configuración de conexión del que será responsable de todos los fundamentos de red para conectarse a los recursos del centro de datos de los clientes a las suscripciones hospedadas en Azure. Cada uno de los clientes que desee usar las capacidades de Azure tendrá su propia conexión ExpressRoute, que será administrada por el que. El que usa los mismos métodos que cliente usaría para adquirir el circuito ExpressRoute. El seguirá los mismos pasos descritos en el artículo [ExpressRoute flujos de trabajo](./expressroute-workflows.md) para el aprovisionamiento de circuito y Estados de circuito. El, a continuación, configurará las rutas de protocolo de puerta de enlace de borde (BGP) para controlar el tráfico que fluye entre la red local y vNet Azure.

### <a name="connect-to-model"></a>Conectar a modelo
En una configuración para conectar su cliente ya tiene una conexión existente a Azure o iniciará una conexión al proveedor de servicios de internet, vincular ExpressRoute desde el centro de datos del cliente directamente a Azure, en lugar de su centro de datos. Para comenzar el proceso de aprovisionamiento, su cliente siga los pasos descrito en el modelo mediante conectar. Una vez que se ha establecido el circuito cliente tendrá que configurar los enrutadores local para tener acceso a la red y el vNets de Azure.

Puede ayudar a configurar la conexión y la configuración de las rutas para permitir que los recursos en su datacenter(s) para comunicarse con los recursos de cliente en el centro de datos o con los recursos alojados en Azure.

## <a name="expressroute-routing-domains"></a>Dominios de enrutamiento ExpressRoute
ExpressRoute ofrece tres dominios enrutamiento: público, privado e interconexión de Microsoft. Cada uno de los dominios de enrutamiento estén configurados con enrutadores idénticos en configuración de activo / activo para alta disponibilidad. Para obtener más detalles sobre ExpressRoute buscar dominios enrutamiento [aquí](./expressroute-circuit-peerings.md).

Puede definir los filtros de rutas personalizadas para permitir que solo los vías que desea permitir o necesita. Para obtener más información o para ver cómo hacer estos cambios consulte el artículo: [crear y modificar el enrutamiento de un circuito de ExpressRoute con PowerShell](./expressroute-howto-routing-classic.md) para obtener más detalles acerca de los filtros de enrutamiento.

>[AZURE.NOTE] Para Microsoft e interconexión pública conectividad aunque debe ser una dirección IP pública que posee el cliente o CSP y debe cumplir todas las reglas definidas. Para obtener más información, consulte la página de [Requisitos previos de ExpressRoute](expressroute-prerequisites.md) .  

## <a name="routing"></a>Enrutamiento
ExpressRoute se conecta a las redes de Azure a través de la puerta de enlace de red Virtual de Azure. Las puertas de enlace de red proporcionan enrutamiento de redes virtuales Azure.

Crear redes virtuales de Azure, también crea una tabla de enrutamiento predeterminada para el vNet dirigir el tráfico de las subredes de la vNet. Si la tabla de ruta predeterminada no es suficiente para la solución personalizada rutas pueden crearse para enrutar el tráfico saliente para dispositivos personalizados o para bloquear redirige a determinadas subredes o redes externas.

### <a name="default-routing"></a>Ruta predeterminada
La tabla de rutas predeterminada incluye las rutas siguientes:

- Enrutamiento dentro de una subred
- Subred a subred dentro de la red virtual
- En Internet
- Red virtual de red para virtual mediante la puerta de enlace VPN
- Red virtual de red para en local mediante una puerta de enlace VPN o ExpressRoute

![texto alternativo](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Enrutamiento definidas por el usuario (UDR)
Rutas definidas por el usuario permiten el control de tráfico saliente de la subred asignada a otras subredes en la red virtual o sobre una de las otras puertas de enlace predefinidos (ExpressRoute; internet o VPN). La tabla de enrutamiento de sistema de forma predeterminada puede reemplazarse con una tabla de enrutamiento definidas por el usuario que reemplaza la tabla de enrutamiento predeterminada con rutas personalizadas. Con el enrutamiento definidas por el usuario, los clientes pueden crear rutas específicas para dispositivos como firewalls o dispositivos de detección de intrusiones o bloquear el acceso a subredes específicas de la subred que aloja la ruta definida por el usuario. Para obtener información general sobre las rutas de definidas por el usuario busque [aquí](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Seguridad
Dependiendo de qué modelo está en uso, para conectar o conectarse a su cliente define las directivas de seguridad de su vNet o proporciona la seguridad de requisitos de la directiva a la CSP para definir a sus vNets. Pueden definirse los criterios de seguridad siguientes:

1.  **Aislamiento de cliente** : plataforma la Azure proporciona aislamiento de cliente almacenando el ID de cliente y vNet información en una base de datos seguro, que se utiliza para encapsulados tráfico de cada cliente en un túnel GRE.
2.  Reglas de **Grupo de seguridad de red (GSN)** son para definir el tráfico permitido dentro y fuera de las subredes de vNets en Azure. De forma predeterminada, la GSN contienen reglas de bloqueo para bloquear el tráfico de Internet a la vNet y permitir reglas para el tráfico dentro de un vNet. Para obtener más información acerca de los grupos de seguridad de red busque [aquí](https://azure.microsoft.com/blog/network-security-groups/).
3.  **Forzar túnel** : se trata de una opción para redirigir el tráfico de internet enlazado original en Azure se redirige a través de la conexión de ExpressRoute para el centro de datos local en. Para obtener más información acerca del túnel forzado [aquí](./expressroute-routing.md#advertising-default-routes).  

4.  **Cifrado** , aunque los circuitos ExpressRoute son exclusivos para un cliente específico, es posible que podría ser infringe el proveedor de red, lo que permite un intruso examinar el tráfico de paquetes. Para solucionar este potencial, un cliente o CSP puede cifrar el tráfico a través de la conexión al definir las directivas de modo de túnel IPSec para todo el tráfico que fluye entre los recursos locales y Azure recursos (consulte el modo de túnel IPSec opcional de 1 de cliente en la figura 5: seguridad ExpressRoute, más arriba). La segunda opción sería usar un dispositivo firewall en cada punto final de circuito de ExpressRoute. Esto requerirá parte 3ª adicional del firewall máquinas virtuales y equipos esté instalado en ambos extremos para cifrar el tráfico a través del circuito ExpressRoute.


![texto alternativo](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Pasos siguientes
El servicio de proveedor de soluciones de nube proporciona una manera de aumentar su valor a sus clientes sin necesidad de compras de infraestructura y capacidad de cara, manteniendo su posición como el proveedor de servicios de subcontratación principal. Integración perfecta con Microsoft Azure puede realizarse a través de la API de CSP, lo que le permite integrar la administración de Microsoft Azure marcos existentes de administración.  

Puede encontrar información adicional en los siguientes vínculos:

[Programa de proveedor de soluciones de nube de Microsoft](https://partner.microsoft.com/en-US/Solutions/cloud-reseller-overview).  
[Preparar transact como un proveedor de soluciones de la nube](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Recursos de proveedor de soluciones de nube de Microsoft](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).
