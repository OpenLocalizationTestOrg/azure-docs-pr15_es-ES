<properties
   pageTitle="Uso de SAP en máquinas virtuales de Windows | Microsoft Azure"
   description="Borrar sobre el uso de SAP en máquinas virtuales de Windows (VM) de Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Uso de SAP en máquinas virtuales de Windows en Azure

La informática es un término utilizado que está adquiriendo más importancia dentro de la industria de TI de pequeñas empresas hasta las organizaciones multinacionales y grandes en nube. Microsoft Azure es la plataforma de servicios de nube de Microsoft que ofrece una amplia gama de nuevas posibilidades. Ahora, los clientes pueden aprovisionar rápidamente y aprovisionar anule la aplicaciones como servicios de nube, por lo que no están limitados a las restricciones de presupuesto o técnicas. En lugar de invertir tiempo y presupuesto en infraestructura de hardware, las empresas pueden centrarse en la aplicación, los procesos empresariales y sus ventajas para los clientes y usuarios.

Con máquinas virtuales de Microsoft Azure, Microsoft ofrece una infraestructura completa como una plataforma de servicio (IaaS). Las aplicaciones SAP NetWeaver según se admiten en Azure máquinas virtuales de Windows (IaaS). Los artículos siguientes describen cómo planear e implementar aplicaciones SAP NetWeaver basado en máquinas virtuales de Windows en Azure. También puede implementar aplicaciones SAP NetWeaver basado en [máquinas virtuales de Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>NetWeaver SAP en Azure, HA

Título: SAP NetWeaver en Azure - clústeres SAP ASC o SCS instancias con Windows Server clúster en Azure con SIOS DataKeeper

Resumen: ' este documento describe cómo usar SIOS DataKeeper para configurar una configuración de SAP ASC o SCS altamente disponible en Azure. SAP protege su punto único de componentes de error como SAP ASC o SCS o servicios de replicación de cola con las configuraciones de Windows Server clúster que requieren discos compartidos. Estos componentes SAP son fundamentales para la funcionalidad de un sistema SAP. Por lo tanto, funcionalidades de alta disponibilidad necesita situarse en su lugar para asegurarse de que los componentes pueden admitir un error de un servidor o una máquina virtual como listo con las configuraciones de clúster de Windows para entornos de Hyper-V y vacío. A partir de agosto del de 2015 Azure en sí mismo no puede proporcionar discos compartidos que serían necesarios para las ventanas según configuraciones altamente disponibles necesarias para estos componentes SAP críticos. No obstante con la Ayuda del producto DataKeeper por SIOS, las configuraciones de Windows Server clúster según sea necesario para SAP ASC o SCS pueden generarse en la plataforma de IaaS de Azure. Este artículo describe con un enfoque paso a paso para instalar una configuración de Windows Server clúster con disco compartido proporcionado por Datakeeper SIOS en Azure. Papel explicará detalles en las configuraciones en el lado de Azure, Windows y SAP que hacen que la configuración de alta disponibilidad trabajar de manera óptima. El documento sirve de complemento de la documentación de instalación de SAP y notas de SAP que representan los recursos principales para las instalaciones y las implementaciones de software SAP en dada plataformas.

Actualizado: Agosto del de 2015

[Descargue a esta guía ahora](http://go.microsoft.com/fwlink/?LinkId=613056)
