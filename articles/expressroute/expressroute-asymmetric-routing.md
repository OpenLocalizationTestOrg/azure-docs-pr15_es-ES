<properties
   pageTitle="Enrutamiento asimétricos | Microsoft Azure"
   description="En este artículo le guiará por los problemas que puede enfrentarse un cliente con el enrutamiento asimétricos en una red que tiene varios vínculos a un destino."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="osamazia"/>

# <a name="asymmetric-routing-with-multiple-network-paths"></a>Enrutamiento asimétricos con varias rutas de red

En este artículo se explica cómo reenviar y devolver el tráfico de red puede tomar rutas diferentes cuando hay varias rutas disponibles entre el origen de red y de destino.

Es importante comprender dos conceptos para comprender el enrutamiento asimétricos. Uno es el efecto de varias rutas de red. La otra es cómo dispositivos, como un firewall, mantener el estado. Estos tipos de dispositivos se denominan dispositivos con estado. Una combinación de estos dos factores crea escenarios de red que el tráfico se rechaza mediante un dispositivo con estado porque el dispositivo con estado no detectado que se ha originado tráfico con el mismo dispositivo.

## <a name="multiple-network-paths"></a>Varias rutas de red

Cuando una red empresarial tiene solo un vínculo a Internet a través de su proveedor de servicios de Internet, todo el tráfico a y desde Internet desplaza a la misma ruta de acceso. A menudo, las empresas adquirir varios circuitos como rutas redundantes, para mejorar el tiempo de actividad de red. En este caso, es posible que el tráfico que pasa fuera de la red a Internet, vaya a través de un vínculo y a continuación, el tráfico devuelto atraviesan un vínculo diferente. Esto se conoce como enrutamiento asimétricos. En el enrutamiento asimétricos, inversa tráfico de red toma una ruta diferente del flujo de original.

![Con varias rutas de red](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Aunque se produce principalmente en Internet, enrutamiento asimétricos también se aplica a otras combinaciones de varias rutas. Por ejemplo, se aplica tanto a una ruta de Internet y una privada que vaya al mismo destino y, a varias rutas privados que vaya al mismo destino.

Cada enrutador a lo largo de la forma de origen a destino, calcula la ruta mejor para llegar a un destino. Determinación del enrutador de mejor posible ruta de acceso se basa en dos factores principales:

-   Enrutamiento entre redes externas se basa en un protocolo de enrutamiento, protocolo de puerta de enlace de borde (BGP). BGP toma anuncios de vecinos y los ejecuta a través de una serie de pasos para determinar la mejor ruta al destino previsto. Almacena la mejor ruta en su tabla de enrutamiento.
-   La longitud de una máscara de subred asociada a una ruta afecta rutas. Si un enrutador recibe varios anuncios para la misma dirección IP, pero con diferentes máscaras de subred, el enrutador prefiere el anuncio con una máscara de subred más larga porque se considera una ruta más específica.

## <a name="stateful-devices"></a>Dispositivos con estado

Busque el encabezado IP de un paquete de enrutadores fines de enrutamiento. Algunos dispositivos buscar aún más profundas dentro del paquete. Normalmente, estos dispositivos mire capa (4) (Transmission Control Protocol, o TCP; o User Datagram Protocol o UDP), o incluso Layer7 encabezados (capa de aplicación). Estos tipos de dispositivos son dispositivos de seguridad o dispositivos de optimización de ancho de banda. 

Un firewall es un ejemplo común de un dispositivo con estado. Un firewall permite o impide que un paquete pase a través de sus interfaces en función de varios campos como protocolo, puerto TCP/UDP y encabezados de dirección URL. Este nivel de la inspección de paquetes coloca a un visible procesamiento de carga en el dispositivo. Para mejorar el rendimiento, el firewall inspecciona el primer paquete del flujo. Si permite el paquete continuar, mantiene la información de flujo en su tabla de estado. Todos los paquetes posteriores relacionadas con este flujo permitidos basándose en la determinación inicial. El firewall podría recibir un paquete que forma parte de un flujo de existente. Si el firewall tiene información de estado anterior, el firewall descarta el paquete.

## <a name="asymmetric-routing-with-expressroute"></a>Asimétricos enrutamiento de ExpressRoute

Cuando se conecte a Microsoft a través de Azure ExpressRoute, los cambios de red similar a esta:

-   Tiene varios vínculos a Microsoft. Un vínculo es su conexión a Internet y la otra es a través de ExpressRoute. Parte del tráfico Microsoft que vaya a través de Internet pero que vuelva a través de ExpressRoute (o viceversa).
-   Recibirá las direcciones IP más específicas a través de ExpressRoute. Por lo tanto, para el tráfico de su red a Microsoft para los servicios ofrecidos a través de ExpressRoute, enrutadores siempre prefieren ExpressRoute.

Para conocer el efecto que tienen estos dos cambios en una red, veamos algunos escenarios. Por ejemplo, tiene un solo circuito a Internet y consumir todos los servicios de Microsoft a través de Internet. El tráfico de su red a Microsoft y al revés atraviesa el mismo vínculo de Internet y se pasa a través del firewall. El firewall registra el flujo mientras ve el primer paquete y se permiten paquetes devueltos porque existe el flujo de la tabla de estado.

![Asimétricos enrutamiento de ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


A continuación, activar ExpressRoute y consumir servicios ofrecidos por Microsoft sobre ExpressRoute. Todos los otros servicios de Microsoft se consumen por Internet. Implementar un firewall independiente en el borde que está conectado a ExpressRoute. Microsoft anuncia prefijos más específicos a la red sobre ExpressRoute para servicios específicos. La infraestructura de enrutamiento elige ExpressRoute como la ruta preferida para dichos prefijos. Si no va a anunciar sus direcciones IP públicas a Microsoft sobre ExpressRoute, Microsoft se comunica con las direcciones IP públicas a través de Internet. Reenvíe el tráfico de su red a Microsoft usa ExpressRoute y tráfico inverso de Microsoft usa Internet. Cuando el servidor de seguridad en el borde ve un paquete de respuesta para un flujo de que no se encuentra en la tabla de estado, disminuye el tráfico devuelto.

Si decide usar el mismo conjunto de traducción (NAT) de direcciones de red para ExpressRoute y para Internet, verá problemas similares con los clientes en su red en direcciones IP. Las solicitudes de servicios como Windows Update vayan a través de Internet porque no se anunciaron direcciones IP para estos servicios a través de ExpressRoute. Sin embargo, el tráfico devuelto se suministra a través de ExpressRoute. Si recibe una dirección IP con la misma máscara de subred de Microsoft desde Internet y ExpressRoute, prefiere ExpressRoute por Internet. Si un firewall u otro dispositivo con estado que se encuentra en el borde de la red y opuestas ExpressRoute no tiene previa información sobre el flujo, quita los paquetes que pertenecen a ese flujo.

## <a name="asymmetric-routing-solutions"></a>Soluciones de enrutamiento asimétricas

Tiene dos opciones principales para resolver el problema de enrutamiento asimétricos. Una es a través de enrutamiento y la otra es mediante NAT basada en origen (SNAT).

### <a name="routing"></a>Enrutamiento

Asegúrese de que las direcciones IP públicas se anuncian a vínculos adecuados wide area network (WAN). Por ejemplo, si desea usar Internet para el tráfico de autenticación y ExpressRoute para el tráfico de correo, no debe anunciar las direcciones IP de los servicios de federación de Active Directory (AD FS) públicas sobre ExpressRoute. Asimismo, asegúrese de no exponen en local servidor AD FS a direcciones IP que el enrutador recibe sobre ExpressRoute. Rutas recibidas a través de ExpressRoute son más específicas para que hagan ExpressRoute la ruta preferida para el tráfico de autenticación a Microsoft. Hace que el enrutamiento asimétricos.

Si desea usar ExpressRoute para la autenticación, asegúrese de va a anunciar las direcciones IP de AD FS públicas sobre ExpressRoute sin NAT. De esta forma, el tráfico que procede de Microsoft y se dirige a la implementación local servidor AD FS trata sobre ExpressRoute. Devolver el tráfico de cliente a Microsoft usa ExpressRoute porque es la ruta preferida por Internet.

### <a name="source-based-nat"></a>NAT basada en origen

Otra manera de resolver problemas de enrutamiento asimétricos es mediante SNAT. Por ejemplo, no se anuncia la dirección IP pública de un servidor de Protocolo Simple de transferencia de correo (SMTP) local sobre ExpressRoute porque piensa utilizar Internet para este tipo de comunicación. Una solicitud que se origina con Microsoft y, a continuación, pasa al servidor SMTP local pasa por Internet. Se SNAT la solicitud entrante a una dirección IP interna. Inverso tráfico desde el servidor SMTP pasa por el firewall perimetral (que usa para NAT) en lugar de a través de ExpressRoute. El tráfico devuelto vuelve a través de Internet.


![Configuración de red NAT basada en origen](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detección de enrutamiento asimétrica

Traceroute es la mejor manera de asegurarse de que el tráfico de red atraviesa la ruta esperada. Si desea que el tráfico del servidor SMTP local a Microsoft para tomar la ruta de acceso de Internet, es la traceroute esperado desde el servidor SMTP Office 365. El resultado se valida que el tráfico de hecho sale de la red hacia Internet y no hacia ExpressRoute.
