<properties 
   pageTitle="Guía de solución de problemas de ExpressRoute - obtener ARP tablas | Microsoft Azure"
   description="Esta página proporciona instrucciones sobre cómo obtener al ARP tablas para un circuito ExpressRoute"
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

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Guía de solución de problemas de ExpressRoute - tablas ARP introducción del modelo de implementación de administrador de recursos

> [AZURE.SELECTOR]
[PowerShell - Administrador de recursos](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - clásica](expressroute-troubleshooting-arp-classic.md)

En este artículo le guiará por los pasos para obtener información sobre las tablas de ARP para su circuito ExpressRoute. 

>[AZURE.IMPORTANT] Este documento está pensado para ayudarle a diagnosticar y solucionar problemas de simples. No está pensado para ser un reemplazo de soporte técnico de Microsoft. Si no puede resolver el problema siguiendo los pasos descritos a continuación, deberá abrir una incidencia de soporte técnico con el [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Resolución de protocolo () ARP y tablas de direcciones
Protocolo de resolución de direcciones (ARP) es un protocolo de nivel 2 definido en [RFC 826](https://tools.ietf.org/html/rfc826). ARP se utiliza para asignar la dirección de Ethernet (dirección MAC) con una dirección ip.

La tabla ARP proporciona una asignación de las direcciones ipv4 y MAC para una determinada interconexión. La tabla ARP de una interconexión de circuito ExpressRoute proporciona la información siguiente para cada interfaz (primaria y secundaria)

1. Asignación de dirección ip de la interfaz de la enrutador local a la dirección de MAC
2. Asignación de ExpressRoute enrutador interfaz la dirección ip a la dirección de MAC
3. Edad de la asignación

Tablas de ARP le ayudarán a validar la configuración de nivel 2 y solución de problemas básicos de nivel 2 problemas de conectividad. 

Tabla de ARP de ejemplo: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


En la sección siguiente proporciona información sobre cómo puede ver las tablas de ARP ver por los enrutadores de borde de ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Requisitos previos para aprender a usar tablas ARP

Asegurarse de que tiene los siguientes antes de más de progreso

 - Circuito de ExpressRoute válido configurado con al menos una interconexión. El circuito debe estar configurado totalmente por el proveedor de servicios de conectividad. Usted (o su proveedor de servicios de conectividad) debe haber configurado al menos uno de los peerings (público privado, Azure Azure y Microsoft) en este circuito.
 - Intervalos de direcciones IP que se utiliza para configurar la peerings (público privado, Azure Azure y Microsoft). Revise los ejemplos de asignación de direcciones ip en la [página de requisitos del enrutamiento de ExpressRoute](expressroute-routing.md) para obtener una descripción de cómo se asignan direcciones ip para interfaces en el lado y en el lado de ExpressRoute. Puede obtener información sobre la configuración de interconexión revisando la [página de configuración de interconexión ExpressRoute](expressroute-howto-routing-arm.md).
 - Información de su equipo de red del proveedor de servicios de conectividad en las direcciones MAC de interfaces se utiliza con estas direcciones IP.
 - Debe tener el último módulo de PowerShell para Azure (versión 1.50 o más reciente).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obtener al ARP tablas para su circuito ExpressRoute
Esta sección proporciona instrucciones sobre cómo puede ver las tablas de ARP por interconexión con PowerShell. Usted o su proveedor de conectividad debe haber configurado la interconexión antes de pasar aún más. Cada circuito tiene dos rutas de acceso (primario y secundarios). Puede comprobar la tabla ARP de cada ruta de forma independiente.

### <a name="arp-tables-for-azure-private-peering"></a>Tablas de ARP para interconexión privada de Azure
El siguiente cmdlet proporciona a ARP tablas para interconexión privada de Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

A continuación se muestran resultados de ejemplo para una de las rutas

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tablas de ARP para interconexión pública de Azure
El siguiente cmdlet proporciona a ARP tablas para interconexión pública de Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


A continuación se muestran resultados de ejemplo para una de las rutas

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tablas de ARP para Microsoft interconexión
El siguiente cmdlet proporciona a ARP tablas para Microsoft interconexión

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


A continuación se muestran resultados de ejemplo para una de las rutas

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Cómo usar esta información
La tabla ARP de una interconexión puede usarse para determinar validar conectividad y configuración de nivel 2. Esta sección proporciona información general sobre el aspecto que tendrán las tablas de ARP en escenarios diferentes.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabla de ARP cuando un circuito está en estado de funcionamiento (estado esperado)

 - La tabla ARP tendrá una entrada para el lado local con una dirección IP y la dirección de MAC y una entrada similar para la parte de Microsoft. 
 - El último octeto de la dirección ip local siempre será un número impar.
 - El último octeto de la dirección ip de Microsoft siempre será un número par.
 - La misma dirección MAC aparecerán en el lado de Microsoft para todos los 3 peerings (primario / secundarios). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabla ARP cuando local / lado del proveedor de servicios de conectividad tiene problemas

 - Solo una entrada aparecerá en la tabla ARP. Esto le mostrará la asignación entre la dirección MAC y la dirección IP se utiliza en el lado de Microsoft. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Abrir una solicitud de soporte con su proveedor de conectividad depurar estos problemas. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabla de ARP al lado de Microsoft tiene problemas

 - No se muestra una tabla ARP para interconexión si hay problemas en el lado de Microsoft. 
 -  Abrir una incidencia de soporte técnico con [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especificar que tiene un problema con la conectividad de nivel 2. 

## <a name="next-steps"></a>Pasos siguientes

 - Validar las configuraciones de nivel 3 para su circuito ExpressRoute
     - Obtener resumen para determinar el estado de las sesiones BGP ruta 
     - Obtener la tabla de ruta para determinar qué prefijos se anuncian a través de ExpressRoute
 - Validar la transferencia de datos revisando bytes dentro / fuera
 - Abrir una incidencia de soporte técnico con [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si todavía experimenta problemas.
