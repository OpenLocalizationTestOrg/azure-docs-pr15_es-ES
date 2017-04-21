<properties
    pageTitle="Información general sobre IPv6 para equilibrador de carga de Azure | Microsoft Azure"
    description="Descripción de la compatibilidad con IPv6 equilibrador de carga de Azure y máquinas virtuales de equilibrio de carga."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, equilibrador de carga de azure, pila dual, ip pública, ipv6 nativa, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Información general sobre IPv6 para equilibrador de carga de Azure

Con una dirección IPv6 se pueden implementar equilibradores de carga a través de Internet. Además de conectividad de IPv4, esto permite las siguientes capacidades:

* Llevar a cabo conectividad IPv6 nativa entre los clientes de Internet pública y máquinas virtuales de Azure (VM) a través del equilibrador de carga.
* Llevar a cabo conectividad IPv6 nativa saliente entre máquinas virtuales y públicos clientes habilitados para IPv6 de Internet.

La imagen siguiente muestra la funcionalidad de IPv6 para equilibrador de carga de Azure.

![Equilibrador de carga de Azure con IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Una vez implementado, un cliente IPv4 o IPv6 habilitado Internet puedan comunicarse con direcciones IPv4 o IPv6 pública (o nombres de host) del equilibrador de carga a través de Internet de Azure. El equilibrador de carga enruta los paquetes de IPv6 a las direcciones IPv6 privadas de las máquinas virtuales con traducción de direcciones de red (NAT). El cliente de Internet IPv6 no puede comunicarse directamente con la dirección IPv6 de las máquinas virtuales.

## <a name="features"></a>Características

Proporciona la compatibilidad de IPv6 para máquinas virtuales implementado mediante el Administrador de recursos de Azure:

1. Equilibrio de carga de servicios de IPv6 para clientes de IPv6 en Internet
2. Extremos IPv4 y IPv6 nativa en máquinas virtuales ("dual apilada")
3. Entrantes y saliente iniciado conexiones IPv6 nativas
4. Protocolos compatibles como TCP, UDP y HTTP (S) habilitar una gama completa de arquitecturas de servicio

## <a name="benefits"></a>Ventajas

Esta funcionalidad permite las siguientes ventajas clave:

* Cumplir las normas gubernamentales que requieren que las nuevas aplicaciones tengan acceso a los clientes sólo IPv6
* Habilitar mobile e Internet de desarrolladores cosas (IOT) usar apiladas doble (IPv4 + IPv6) Azure máquinas virtuales de Windows para el crecimiento mobile & IOT mercados de direcciones

## <a name="details-and-limitations"></a>Detalles y limitaciones

Detalles

* El servicio DNS de Azure contiene los registros de DNS A IPv4 y IPv6 AAAA y responde con ambos registros para el equilibrador de carga. El cliente elige qué dirección (IPv4 o IPv6) para comunicarse con.
* Cuando una máquina virtual inicia una conexión a un dispositivo conectado a Internet IPv6 público, dirección IPv6 de origen de la máquina virtual es dirección de red traducido (NAT) a la dirección IPv6 pública del equilibrador de carga.
* Máquinas virtuales con el sistema operativo Linux deben estar configuradas para recibir una dirección IP IPv6 a través de DHCP. Muchas de las imágenes de Linux en la Galería de Azure ya están configuradas para admitir IPv6 sin modificación. Para obtener más información, vea [Configurar DHCPv6 para máquinas virtuales de Linux](load-balancer-ipv6-for-linux.md)
* Si elige usar un sondeo de mantenimiento con el equilibrador de carga, crear un sondeo IPv4 y usarlo con extremos IPv4 e IPv6. Si el servicio en la máquina virtual se desconecta, extrae extremos IPv4 e IPv6 de giro.

Limitaciones

* No puede agregar reglas de equilibrio de carga de IPv6 en el portal de Azure. Las reglas pueden crearse sólo a través de la plantilla, CLI, PowerShell.
* No puede actualizar las máquinas virtuales existentes para usar direcciones IPv6. Debe implementar nuevas máquinas virtuales.
* Una dirección IPv6 solo se puede asignar a una única interfaz de red en cada máquina virtual.
* Las direcciones IPv6 públicas no se pueden asignar a una máquina virtual. Solo se puede asignar a un equilibrador de carga.
* No puede configurar la búsqueda DNS inversa para las direcciones IPv6 públicas.
* Las máquinas virtuales con las direcciones IPv6 no pueden ser miembros de un servicio de nube de Azure. Que se pueden conectar a una red Virtual de Azure (VNet) y comunican con ellos a través de sus direcciones IPv4.
* Direcciones IPv6 privado se pueden implementar en máquinas virtuales individuales en un grupo de recursos, pero no se puede implementar en un grupo de recursos a través de conjuntos de escala.
* Máquinas virtuales de Azure no pueden conectarse con IPv6 con otras máquinas virtuales, otros servicios de Azure o los dispositivos locales. Sólo puede comunicarse con el equilibrador de carga de Azure sobre IPv6. Sin embargo, que puedan comunicarse con estos recursos de usando IPv4.
* Protección de grupo de seguridad de la red (GSN) para IPv4 es compatible con implementaciones de pila doble (IPv4 + IPv6). NSGs no se aplican a los extremos de IPv6.
* El extremo de IPv6 en la máquina virtual no se expone directamente a internet. Es detrás de un equilibrador de carga. Solo los puertos especificados en las reglas de equilibrador de carga sean accesibles sobre IPv6.
* Cambiar el parámetro IdleTimeout para IPv6 es **no es compatible actualmente**. El valor predeterminado es cuatro minutos.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo implementar un equilibrador de carga con IPv6.

* [Disponibilidad de IPv6 por región](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implementar un equilibrador de carga con IPv6 usando una plantilla](load-balancer-ipv6-internet-template.md)
* [Implementar un equilibrador de carga con IPv6 con PowerShell de Azure](load-balancer-ipv6-internet-ps.md)
* [Implementar un equilibrador de carga con IPv6 mediante CLI de Azure](load-balancer-ipv6-internet-cli.md)
