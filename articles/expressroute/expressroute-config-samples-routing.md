<properties
   pageTitle="Ejemplos de configuración de enrutador de cliente ExpressRoute | Microsoft Azure"
   description="Esta página proporciona ejemplos de configuración de enrutador para enrutadores Cisco y Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Ejemplos de configuración de enrutador para configurar y administrar el enrutamiento

Esta página proporciona la interfaz y ejemplos de configuración de enrutamiento para Cisco IOS-XE y Juniper MX enrutadores de serie. Estos están pensados para ser ejemplos indicativo y no deben usarse como está. Puede trabajar con su proveedor para dar con configuraciones adecuadas para su red. 

>[AZURE.IMPORTANT] Ejemplos de esta página se pretenden sean puramente para obtener instrucciones. Debe trabajar con el equipo de ventas y técnico de su proveedor y su equipo de red para dar con configuraciones adecuadas para satisfacer sus necesidades. Microsoft no admite problemas relacionados con las configuraciones que se muestran en esta página. Debe ponerse en contacto con el proveedor del dispositivo para los problemas de compatibilidad.

Ejemplos de configuración de enrutador siguientes se aplican a todos los peerings. Revisar [requisitos de enrutamiento de ExpressRoute](expressroute-routing.md) y [peerings ExpressRoute](expressroute-circuit-peerings.md) para obtener más detalles acerca del enrutamiento.

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE según enrutadores

Los ejemplos de esta sección se aplican a cualquier enrutador ejecuta la familia OS XE IOS.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. configurar interfaces e interfaces secundarias

Necesitará una interfaz sub por interconexión en cada enrutador que conectarse a Microsoft. Una interfaz sub puede identificarse con un par apilado de identificadores de VLAN y una dirección IP o con un ID.

#### <a name="dot1q-interface-definition"></a>Definición de la interfaz de Dot1Q

Este ejemplo proporciona la definición de la interfaz subcarpetas para una interfaz subcarpetas con un identificador de VLAN. El ID de VLAN es único por interconexión. El último octeto de la dirección IPv4 siempre será un número impar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>Definición de la interfaz de QinQ

Este ejemplo proporciona la definición de interfaz subcarpetas para una interfaz con un ID de VLAN dos subsitios. El identificador de VLAN externa (s-etiqueta), si utiliza es el mismo en todas las peerings. El identificador de VLAN interna (etiqueta c) es único por interconexión. El último octeto de la dirección IPv4 siempre será un número impar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. configurar sesiones eBGP

Debe configurar una sesión BGP con Microsoft para cada interconexión. En el siguiente ejemplo le permite configurar una sesión BGP con Microsoft. Si la dirección IPv4 que utiliza la interfaz de sub era a.b.c.d, la dirección IP del vecino BGP (Microsoft) será a.b.c.d+1. El último octeto de la dirección de IPv4 del vecino BGP siempre será un número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. configurar prefijos a anunciará sobre la sesión BGP

Puede configurar el enrutador para anunciar prefijos selectos a Microsoft. Puede hacerlo con el siguiente ejemplo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. asigna la ruta de

Puede usar mapas de ruta y listas de prefijo a prefijos de filtro que se propaga en la red. Puede usar el siguiente ejemplo para llevar a cabo la tarea. Asegurarse de que tiene el programa de instalación de listas de prefijo adecuado.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Enrutadores de serie de Juniper MX 

Los ejemplos de esta sección se aplican a cualquier enrutador de serie Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. configurar interfaces e interfaces secundarias

#### <a name="dot1q-interface-definition"></a>Definición de la interfaz de Dot1Q

Este ejemplo proporciona la definición de la interfaz subcarpetas para una interfaz subcarpetas con un identificador de VLAN. El ID de VLAN es único por interconexión. El último octeto de la dirección IPv4 siempre será un número impar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>Definición de la interfaz de QinQ

Este ejemplo proporciona la definición de interfaz subcarpetas para una interfaz con un ID de VLAN dos subsitios. El identificador de VLAN externa (s-etiqueta), si utiliza es el mismo en todas las peerings. El identificador de VLAN interna (etiqueta c) es único por interconexión. El último octeto de la dirección IPv4 siempre será un número impar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. configurar sesiones eBGP

Debe configurar una sesión BGP con Microsoft para cada interconexión. En el siguiente ejemplo le permite configurar una sesión BGP con Microsoft. Si la dirección IPv4 que utiliza la interfaz de sub era a.b.c.d, la dirección IP del vecino BGP (Microsoft) será a.b.c.d+1. El último octeto de la dirección de IPv4 del vecino BGP siempre será un número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. configurar prefijos a anunciará sobre la sesión BGP

Puede configurar el enrutador para anunciar prefijos selectos a Microsoft. Puede hacerlo con el siguiente ejemplo.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. asigna la ruta de

Puede usar mapas de ruta y listas de prefijo a prefijos de filtro que se propaga en la red. Puede usar el siguiente ejemplo para llevar a cabo la tarea. Asegurarse de que tiene el programa de instalación de listas de prefijo adecuado.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Pasos siguientes

Consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener más detalles.
