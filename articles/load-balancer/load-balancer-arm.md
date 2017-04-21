<properties
   pageTitle="Compatibilidad de Azure del Administrador de recursos con equilibrador de carga | Microsoft Azure "
   description="Usar powershell para equilibrador de carga con el Administrador de recursos de Azure. Uso de plantillas para equilibrador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Uso de Azure compatibilidad con el Administrador de recursos con equilibrador de carga de Azure

Administrador de recursos de Azure es el marco de administración preferido para los servicios de Azure. Azure equilibrador de carga se pueden administrar mediante herramientas y API basadas en el Administrador de recursos de Azure.

## <a name="concepts"></a>Conceptos

Con el Administrador de recursos, equilibrador de carga de Azure contiene los siguientes recursos secundarios:

- Configuración de IP front-end: un equilibrador de carga puede incluir uno o más front-end direcciones IP, también conocidas como una direcciones IP virtual (VIP). Estas direcciones IP servir como entrada para el tráfico.

- Grupo de direcciones de back-end – son direcciones IP asociadas con la tarjeta de interfaz de red de máquina virtual (NIC) a la que se distribuirá la carga.

- Reglas de equilibrio de carga: una propiedad regla asigna un determinado front-end IP y puerto a un conjunto de direcciones IP de back-end y combinación de puerto. Un equilibrador de carga única puede tener varias reglas de equilibrio de carga. Cada regla es una combinación de un front-end IP y puerto y back-end IP y puerto asociado a máquinas virtuales.

- Sondeos: sondeos le permiten realizar un seguimiento de la salud de instancias de máquina virtual. Si se produce un error en un sondeo de mantenimiento, la instancia de VM se pueden sacar giro automáticamente.

- Reglas de entrada NAT: reglas NAT que define el tráfico entrante que pasan a través de la parte frontal fin IP y distribuyen a la dirección IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Tutorial rápido plantillas

Administrador de recursos de Azure le permite aprovisionar las aplicaciones usando una plantilla descriptiva. En una plantilla, puede implementar varios servicios junto con sus dependencias. Use la misma plantilla para implementar varias veces su aplicación durante todas las etapas del ciclo de vida de aplicación.

Plantillas pueden incluir definiciones de máquinas virtuales, redes virtuales, conjuntos de disponibilidad, Interfaces de red (NIC), cuentas de almacenamiento, equilibradores de carga, los grupos de seguridad de red y direcciones IP públicas. Con las plantillas puede crear todo lo que necesita para una aplicación compleja. El archivo de plantilla se puede incorporar en sistema de administración de contenido para el control de versiones y colaboración.

[Más información sobre las plantillas](http://go.microsoft.com/fwlink/?LinkId=544798)

[Más información acerca de los recursos de red](../virtual-network/resource-groups-networking.md)

Plantillas de tutorial rápido con equilibrador de carga de Azure almacenadas en un [repositorio de GitHub](https://github.com/Azure/azure-quickstart-templates) un conjunto de plantillas de comunidad generada de host.

Ejemplos de plantillas:

- [Máquinas virtuales de 2 en un equilibrador de carga y reglas de equilibrio de carga](http://go.microsoft.com/fwlink/?LinkId=544799)
- [Máquinas virtuales de 2 en un VNET con las reglas de un equilibrador de carga interno y equilibrador de carga](http://go.microsoft.com/fwlink/?LinkId=544800)
- [Máquinas virtuales de 2 en un equilibrador de carga y configurar reglas NAT en la Libras](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurar equilibrador de carga de Azure con PowerShell o CLI

Empezar a usar cmdlets, herramientas de línea de comandos y las API de REST de administrador de recursos de Azure

- [Cmdlets de redes de Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) puede utilizarse para crear un equilibrador de carga.
- [Cómo crear un equilibrador de carga con el Administrador de recursos de Azure](load-balancer-get-started-ilb-arm-ps.md)
- [Usar la CLI Azure con administración de recursos de Azure](../xplat-cli-azure-resource-manager.md)
- [API REST de equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>Pasos siguientes

También puede [empezar a crear un opuestas equilibrador de carga de Internet](load-balancer-get-started-internet-arm-ps.md) y configurar qué tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento de tráfico de red de equilibrador carga específica.

Obtenga información sobre cómo administrar la [configuración de tiempo de espera TCP inactivas equilibradores de carga](load-balancer-tcp-idle-timeout.md). Esto es importante cuando la aplicación se necesita mantener las conexiones activas para servidores detrás de un equilibrador de carga.
