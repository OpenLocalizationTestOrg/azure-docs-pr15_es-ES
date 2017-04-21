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

# <a name="router-configuration-samples-to-setup-and-manage-nat"></a>Ejemplos de configuración de enrutador para configurar y administrar NAT

Esta página proporciona ejemplos de configuración de NAT para ASA de Cisco y Juniper SRX enrutadores de serie. Estos están pensados para ser ejemplos indicativo y no deben usarse como está. Puede trabajar con su proveedor para dar con configuraciones adecuadas para su red. 

>[AZURE.IMPORTANT] Ejemplos de esta página se pretenden sean puramente para obtener instrucciones. Debe trabajar con el equipo de ventas y técnico de su proveedor y su equipo de red para dar con configuraciones adecuadas para satisfacer sus necesidades. Microsoft no admite problemas relacionados con las configuraciones que se muestran en esta página. Debe ponerse en contacto con el proveedor del dispositivo para los problemas de compatibilidad.

Ejemplos de configuración de enrutador siguientes se aplican a Azure público y Microsoft peerings. No debe configurar NAT para interconexión privada Azure. Revisar [requisitos de NAT ExpressRoute](expressroute-nat.md) y [peerings ExpressRoute](expressroute-circuit-peerings.md) para obtener más detalles.

**Nota:** DEBE usar grupos de IP de NAT independientes para la conectividad a internet y ExpressRoute. Usar el mismo conjunto de IP de NAT a través de internet y ExpressRoute producirá asimétricos enrutamiento y pérdida de conectividad.

## <a name="cisco-asa-firewalls"></a>Firewalls ASA de Cisco

### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configuración de ELENA para el tráfico de red del cliente a Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configuración de ELENA tráfico de Microsoft para la red de clientes

#### <a name="interfaces-and-direction"></a>Interfaces y dirección:
    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

#### <a name="configuration"></a>Configuración:
Grupo NAT:

    object network outbound-PAT
        host <NAT-IP>

Servidor de destino:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Grupo de objetos de las direcciones IP de cliente

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>
    
    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Comandos NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Enrutadores de Juniper SRX serie 

### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. crear redundantes interfaces Ethernet para el clúster

    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. crear dos zonas de seguridad

 - Zona de confianza para la red interna y la zona de definir como no fiable para red externa opuestas enrutadores de borde
 - Asignar interfaces apropiadas para las zonas
 - Permitir que los servicios en las interfaces


    seguridad {zonas de confianza de zona de seguridad de {{entrante-tráfico del host {-servicios del sistema {ping;                  } protocolos {bgp;                  interfaces}} {reth0.100;              {}} zona de seguridad definir como no fiable {entrante-tráfico del host {-servicios del sistema {ping;                  } protocolos {bgp;                  interfaces}} {reth1.100;              }          }      }  }


### <a name="3-create-security-policies-between-zones"></a>3. crear directivas de seguridad entre las zonas
 
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. configurar directivas NAT
 - Cree dos grupos NAT. Uno se utilizará para el tráfico saliente de Microsoft y otro de NAT de Microsoft al cliente.
 - Crear reglas para NAT el tráfico respectivo

        security {
            nat {
                source {
                    pool SNAT-To-ExpressRoute {
                        routing-instance {
                            External-ExpressRoute;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    pool SNAT-From-ExpressRoute {
                        routing-instance {
                            Internal;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    rule-set Outbound_NAT {
                        from routing-instance Internal;
                        to routing-instance External-ExpressRoute;
                        rule SNAT-Out {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-To-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                    rule-set Inbound-NAT {
                        from routing-instance External-ExpressRoute;
                        to routing-instance Internal;
                        rule SNAT-In {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-From-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }


### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. configurar BGP para anunciar prefijos selectivos en cada dirección

Consulte los ejemplos en la página de [ejemplos de configuración de enrutamiento](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. crear directivas

    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Pasos siguientes

Consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener más detalles.
