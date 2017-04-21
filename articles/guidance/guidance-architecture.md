
<properties
   pageTitle="Guía de Azure | patrones y prácticas | Microsoft Azure"
   description="Arquitecturas de referencia de Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Arquitecturas de referencia de Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Este contenido está en desarrollo activo. Es útil en la actualidad, por lo que estamos lo que esté disponible para la vista previa. Valor de sus comentarios._

Nuestras arquitecturas de referencia están organizadas por escenario, con varias arquitecturas relacionadas agrupadas.
Cada arquitectura individual ofrece prácticas recomendadas y los pasos indicados, así como un componente ejecutable que incluye las recomendaciones.
Muchas de las arquitecturas son progresivos; compilar a partir de arquitecturas anteriores que tienen menos requisitos.

## <a name="designing-your-infrastructure-for-resiliency"></a>Diseño de la infraestructura de la resistencia

Esta serie comienza con las prácticas recomendadas para la configuración de VM óptima y culmina en una implementación de múltiples región con migración tras error.

- [Quedando una máquina virtual de Windows Azure](guidance-compute-single-vm.md)
- [Ejecuta una máquina virtual de Linux en Azure](guidance-compute-single-vm-linux.md)
- [Ejecución de múltiples máquinas virtuales de disponibilidad y escalabilidad](guidance-compute-multi-vm.md)
- [Ejecución de máquinas virtuales de Windows para una arquitectura de capas](guidance-compute-n-tier-vm.md)
- [Ejecución de máquinas virtuales de Linux para una arquitectura de capas](guidance-compute-n-tier-vm-linux.md)
- [Ejecución de máquinas virtuales de Windows en varias áreas para alta disponibilidad](guidance-compute-multiple-datacenters.md)
- [Ejecución de máquinas virtuales de Linux en varias áreas para alta disponibilidad](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>Conectarse a su red local a Azure

Esta serie comienza con la demostración las formas de conectarse a la red existente de Azure. A continuación, se expande para cubrir los requisitos de disponibilidad y seguridad.

- [Implementar una arquitectura de red híbrido con Azure y VPN local](guidance-hybrid-network-vpn.md)
- [Implementar una arquitectura de red híbrido con Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [Implementar una arquitectura de red híbrida altamente disponible](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Proteger su red híbrida

Esta serie cubre prácticas sobre la creación de DMZ en Azure para conexiones seguras procedentes de su centro de datos local e Internet.

- [Implementación de una DMZ entre Azure y su centro de datos local](guidance-iaas-ra-secure-vnet-hybrid.md)
- [Implementación de una DMZ entre Azure e Internet](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Proporcionar servicios de identidad

Esta serie inicia que muestra cómo utilizar Azure Active Directory para proporcionar autenticación de usuario en Azure. A continuación, se expande para cubrir escenarios complejos ampliación de la infraestructura de suma a Azure y mediante ADFS para la delegación.

- [Implementación de Azure Active Directory](./guidance-identity-aad.md)
- [Extensión de servicios de directorio de Active Directory (suma) en Azure](./guidance-identity-adds-extend-domain.md)
- [Crear un bosque de recursos de servicios de directorio de Active Directory (suma) en Azure](./guidance-identity-adds-resource-forest.md)
- [Implementación de servicios de federación de Active Directory (ADFS) en Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Arquitectura de la aplicación web scalable mediante PaaS de Azure

Esta serie cubre recomendaciones para crear aplicaciones web scalable y altamente disponibles. 

- [Aplicación web básica](guidance-web-apps-basic.md)
- [Mejorar la escalabilidad en una aplicación web](guidance-web-apps-scalability.md)
- [Aplicación Web con alta disponibilidad](guidance-web-apps-multi-region.md)
