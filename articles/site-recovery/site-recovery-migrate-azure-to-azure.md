<properties
    pageTitle="Migrar máquinas virtuales de Azure IaaS de una región Azure a otro sitio de recuperación | Microsoft Azure"
    description="Use Azure sitio recuperación para migrar máquinas virtuales de Azure IaaS de una región Azure a otro."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuales de Azure IaaS entre regiones Azure con la recuperación de sitio de Azure

## <a name="overview"></a>Información general

Bienvenido a Azure sitio recuperación! Utilice este artículo si desea migrar máquinas virtuales de Azure entre regiones de Azure. Antes de empezar, tenga en cuenta que:

- Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: Administrador de recursos de Azure y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación. Los pasos básicos para la migración son iguales si está configurando recuperación de sitio en el Administrador de recursos o en clásico. Sin embargo, las instrucciones de la interfaz de usuario y las capturas de pantalla en este artículo son relevantes para el portal de Azure.
- **Actualmente sólo puede migrar desde una región a otra. Puede conmutar máquinas virtuales de una región Azure a otro, pero no puede conmutar ellos nuevamente.**
- Las instrucciones de migración en este artículo se basan en las instrucciones para duplicar una máquina física en Azure. Incluye vínculos a los pasos en [máquinas virtuales de VMware replicar o servidores físicos en Azure](site-recovery-vmware-to-azure.md), que describe cómo replicar un servidor físico en el portal de Azure.
- Si está configurando la recuperación de sitio en el portal de clásico, siga las instrucciones detalladas en [este artículo](site-recovery-vmware-to-azure-classic.md). **Ya no se debe usar** las instrucciones de este [artículo heredado](site-recovery-vmware-to-azure-classic-legacy.md).

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Requisitos previos

Aquí es lo que necesita para esta implementación:

- **Servidor de configuración**: una VM local que ejecutan Windows Server 2012 R2 que actúa como el servidor de configuración. Instalar los otros componentes de recuperación de sitios (incluido el servidor de proceso y destino maestro) en este VM demasiado. Más en la [arquitectura de escenario](site-recovery-vmware-to-azure.md#scenario-architecture) y [los requisitos previos de configuración server](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Máquinas virtuales de IaaS**: máquinas virtuales de la que desea migrar. Migrar estas máquinas virtuales por trata como equipos físicos.

## <a name="deployment-steps"></a>Pasos de implementación

Esta sección describe los pasos de implementación en el nuevo portal de Azure. Si necesita estos pasos de implementación de recuperación de sitio en el portal de clásico, consulte [este artículo](site-recovery-vmware-to-azure-classic.md).

1. [Crear un depósito](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implementar un servidor de configuración](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Una vez que haya implementado el servidor de configuración, compruebe que se puede comunicar con las máquinas virtuales que desea migrar.
4. [Establecer las opciones de replicación](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Crear una directiva de replicación y asignar al servidor de configuración.
5. [Instalar el servicio de movilidad](site-recovery-vmware-to-azure.md#step-6-replication-application). Cada máquina virtual que desea proteger tiene instalado el servicio de movilidad. Este servicio envía los datos al servidor de procesos. Puede instalar manualmente el servicio de movilidad o inserta e instala automáticamente el servidor de proceso cuando se habilita la protección de la máquina virtual. Las reglas de Firewall en las máquinas virtuales que desea migrar deben estar configuradas para permitir la instalación de inserción de este servicio.
6. [Habilitar la replicación](site-recovery-vmware-to-azure.md#enable-replication). Habilitar la replicación de las máquinas virtuales que desea migrar. Puede detectar los equipos virtuales IaaS que desea migrar a Azure usando la dirección IP privada de las máquinas virtuales. Buscar esta dirección en el panel de la máquina virtual de Azure. Al habilitar la replicación, establezca el tipo de máquina para las máquinas virtuales como máquinas físicas.
7. [Ejecutar una migración tras error no planeado](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial, puede ejecutar una migración tras error no planeado de una región Azure a otro. Si lo desea, puede crear un plan de recuperación y ejecutar un failover no planificado, para migrar múltiples máquinas virtuales entre regiones. [Más información](site-recovery-create-recovery-plans.md) acerca de los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre otras situaciones de replicación en [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)
