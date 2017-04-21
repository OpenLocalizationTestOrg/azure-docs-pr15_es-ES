<properties
    pageTitle="Migrar máquinas virtuales de Windows de servicios Web de Amazon a Azure con la recuperación de sitio | Microsoft Azure"
    description="En este artículo se describe cómo migrar máquinas virtuales de Windows ejecutando en servicios de Web de Amazon (AWA) a Azure mediante la recuperación de sitio de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuales de Windows en servicios de Web de Amazon (AWS) a Azure con la recuperación de sitio de Azure

## <a name="overview"></a>Información general

Bienvenido a Azure sitio recuperación. Use este artículo para migrar instancias de Windows que se ejecutan en AWS a Azure con la recuperación de sitio. Antes de empezar, tenga en cuenta que:

- Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: Administrador de recursos de Azure y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación. Los pasos básicos para la migración son iguales si está configurando recuperación de sitio en el Administrador de recursos o en clásico. Sin embargo, las instrucciones de la interfaz de usuario y las capturas de pantalla en este artículo son relevantes para el portal de Azure.
- **Actualmente sólo puede migrar desde AWS en Azure. Puede conmutar máquinas virtuales de AWS a Azure, pero no puede conmutar ellos nuevamente. No hay ninguna replicación en curso.**
- Las instrucciones de migración en este artículo se basan en las instrucciones para duplicar una máquina física en Azure. Incluye vínculos a los pasos en [máquinas virtuales de VMware replicar o servidores físicos en Azure](site-recovery-vmware-to-azure.md), que describe cómo replicar un servidor físico en el portal de Azure.
- Si está configurando la recuperación de sitio en el portal de clásico, siga las instrucciones detalladas en [este artículo](site-recovery-vmware-to-azure-classic.md). **Ya no se debe usar** las instrucciones de este [artículo heredado](site-recovery-vmware-to-azure-classic-legacy.md).

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## <a name="prerequisites"></a>Requisitos previos

Esto es lo necesita para esta implementación

- **Servidor de configuración**: una VM local que ejecutan Windows Server 2012 R2 que actúa como el servidor de configuración. Instalar los otros componentes de recuperación de sitios (incluido el servidor de proceso y destino maestro) en este VM demasiado. Más en la [arquitectura de escenario](site-recovery-vmware-to-azure.md#scenario-architecture) y [los requisitos previos de configuración server](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Instancias de VM EC2**: las instancias de Windows que desea migrar.

## <a name="deployment-steps"></a>Pasos de implementación

Esta sección describe los pasos de implementación en el nuevo portal de Azure. Si necesita estos pasos de implementación de recuperación de sitio en el portal de clásico, consulte [este artículo](site-recovery-vmware-to-azure-classic.md).

1. [Crear un depósito](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implementar un servidor de configuración](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Cuando se ha implementado el servidor de configuración, compruebe que se puede comunicar con las máquinas virtuales que desea migrar.
4. [Establecer las opciones de replicación](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Crear una directiva de replicación y asignar al servidor de configuración.
5. [Instalar el servicio de movilidad](site-recovery-vmware-to-azure.md#step-6-replication-application). Cada máquina virtual que desea proteger tiene instalado el servicio de movilidad. Este servicio envía los datos al servidor de procesos. Puede instalar manualmente el servicio de movilidad o inserta e instala automáticamente el servidor de proceso cuando se habilita la protección de la máquina virtual. Reglas de Firewall en instancias EC2 que desee migrar deben estar configuradas para permitir la instalación de inserción de este servicio. El grupo de seguridad para instancias EC2 debería tener las siguientes reglas:

    ![reglas de Firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Habilitar la replicación](site-recovery-vmware-to-azure.md#enable-replication). Habilitar la replicación de las máquinas virtuales que desea migrar. Puede detectar las instancias de EC2 con las direcciones IP privadas, que se puede acceder desde la consola EC2.
7. [Ejecutar una migración tras error no planeado](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial, puede ejecutar una migración tras error planificado de AWS a Azure para cada máquina virtual. Si lo desea, puede crear un plan de recuperación y ejecutar un failover no planificado, para migrar múltiples máquinas virtuales de AWS a Azure. [Más información](site-recovery-create-recovery-plans.md) acerca de los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre otras situaciones de replicación en [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)
