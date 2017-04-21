<properties
   pageTitle="Guía de solución de problemas de ExpressRoute: obtener ARP tablas | Microsoft Azure"
   description="Esta página contiene instrucciones para conseguir a la ARP tablas para un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>Guía de solución de problemas de ExpressRoute: obtener ARP tablas en el modelo de implementación clásica

> [AZURE.SELECTOR]
[PowerShell - Administrador de recursos](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - clásica](expressroute-troubleshooting-arp-classic.md)

En este artículo le guiará por los pasos para obtener las tablas de protocolo de resolución de direcciones (ARP) para su circuito de Azure ExpressRoute.

>[AZURE.IMPORTANT] Este documento está pensado para ayudarle a diagnosticar y solucionar problemas de simples. No está pensado para ser un reemplazo de soporte técnico de Microsoft. Si no se soluciona el problema mediante el uso de las siguientes directrices, abra una solicitud de soporte con [Ayuda de Microsoft Azure + soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Resolución de protocolo () ARP y tablas de direcciones
ARP es un protocolo de nivel 2 que se define en [RFC 826](https://tools.ietf.org/html/rfc826). ARP se utiliza para asignar una dirección de Ethernet (dirección MAC) a una dirección IP.

Una tabla ARP proporciona una asignación de las direcciones IPv4 y MAC para una determinada interconexión. La tabla ARP de una interconexión de circuito ExpressRoute proporciona la información siguiente para cada interfaz (primaria y secundaria):

1. Asignación de una dirección IP de interfaz de enrutador local a la dirección de MAC
2. Asignación de una dirección IP de ExpressRoute enrutador interfaz a la dirección de MAC
3. La edad de la asignación

Tablas de ARP pueden ayudar a con validación de configuración de nivel 2 y solución de problemas de conectividad de nivel 2 básicas.

A continuación se muestra un ejemplo de una tabla ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


En la sección siguiente proporciona información sobre cómo ver las tablas de ARP que se ven los enrutadores de borde de ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Requisitos previos para usar las tablas de ARP

Asegurarse de que tiene los siguientes antes de continuar:

 - Circuito de ExpressRoute válido que está configurado con al menos una interconexión. El circuito debe estar configurado totalmente por el proveedor de servicios de conectividad. Usted (o su proveedor de servicios de conectividad) debe configurar al menos uno de los peerings (público privado, Azure Azure o Microsoft) en este circuito.

 - Intervalos de direcciones IP que se usan para configurar la peerings (público privado, Azure Azure y Microsoft). Revise los ejemplos de asignación de direcciones IP en la [página de requisitos del enrutamiento de ExpressRoute](expressroute-routing.md) para obtener una descripción de cómo se asignan direcciones IP para interfaces en su aise y en el lado de ExpressRoute. Puede obtener información sobre la configuración de interconexión revisando la [página de configuración de interconexión ExpressRoute](expressroute-howto-routing-classic.md).

 - Información de su proveedor de grupo o conectividad de red acerca de las direcciones MAC de las interfaces que se usan con estas direcciones IP.

 - El último módulo de Windows PowerShell para Azure (versión 1.50 o posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tablas de ARP para su circuito ExpressRoute
Esta sección proporciona instrucciones sobre cómo ver las tablas de ARP para cada tipo de interconexión con PowerShell. Antes de continuar, debe configurar la interconexión usted o su proveedor de servicios de conectividad. Cada circuito tiene dos rutas de acceso (primario y secundarios). Puede comprobar la tabla ARP de cada ruta de forma independiente.

### <a name="arp-tables-for-azure-private-peering"></a>Tablas de ARP para interconexión privada de Azure
El siguiente cmdlet proporciona a ARP tablas interconexión privada Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

A continuación se muestra resultados de ejemplo de una de las rutas de acceso:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tablas de ARP para interconexión pública de Azure:
El siguiente cmdlet proporciona a ARP tablas interconexión pública de Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

A continuación se muestra resultados de ejemplo de una de las rutas de acceso:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A continuación se muestra resultados de ejemplo de una de las rutas de acceso:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tablas de ARP para Microsoft interconexión
El siguiente cmdlet proporciona a ARP tablas interconexión de Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


A continuación se muestra el resultado de ejemplo para una de las rutas:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Cómo usar esta información
La tabla ARP de una interconexión puede usarse para validar la conectividad y la configuración de nivel 2. Esta sección proporciona información general sobre el aspecto de las tablas de ARP en escenarios diferentes.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabla de ARP cuando un circuito está en un estado de funcionamiento (esperado)

 - La tabla ARP tiene una entrada para el lado local con una dirección IP y MAC válida y una entrada similar para la parte de Microsoft.
 - El último octeto de la dirección IP local siempre es un número impar.
 - El último octeto de la dirección IP de Microsoft siempre es un número par.
 - La misma dirección MAC aparece en la parte de Microsoft para todas las tres peerings (principal y secundario).


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabla de ARP cuando se local o cuando el lado de proveedor de servicios de conectividad tiene problemas

 Solo una entrada aparece en la tabla ARP. Muestra la asignación entre la dirección MAC y la dirección IP que se utiliza en el lado de Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Si experimenta un problema similar a este, abra una solicitud de soporte con su proveedor de conectividad para resolverlo.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabla de ARP al lado de Microsoft tiene problemas

 - No se muestra una tabla ARP para interconexión si hay problemas en el lado de Microsoft.
 -  Abrir una solicitud de soporte con [Ayuda de Microsoft Azure + soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especificar que tiene un problema con la conectividad de nivel 2.

## <a name="next-steps"></a>Pasos siguientes

 - Validar las configuraciones de nivel 3 para su circuito ExpressRoute:
     - Obtener una ruta de resumen para determinar el estado de las sesiones BGP.
     - Obtener una tabla de ruta para determinar qué prefijos se anuncian a través de ExpressRoute.
 - Validar la transferencia de datos revisando bytes de entrada y salida.
 - Abrir una solicitud de soporte con [Ayuda de Microsoft Azure + soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si todavía experimenta problemas.
