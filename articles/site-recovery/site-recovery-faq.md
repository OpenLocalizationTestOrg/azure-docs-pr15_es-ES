<properties
    pageTitle="Recuperación de Azure sitio: Preguntas más frecuentes sobre | Microsoft Azure"
    description="Este artículo explica las preguntas más frecuentes acerca de la recuperación de sitio de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Recuperación de sitio Azure: Preguntas más frecuentes (P+F)

Este artículo incluye las preguntas más frecuentes acerca de la recuperación de sitio de Azure. Si tiene preguntas después de leer este artículo, publique en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>General

### <a name="what-does-site-recovery-do"></a>¿Qué hace la recuperación de sitio?

Recuperación de sitio contribuye a su estrategia de recuperación (BCDR) empresarial continuidad y desastres, al coordinar y automatizar la replicación de máquinas virtuales de local y servidores físicos a Azure o a un centro de datos secundario. [Más información](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>¿Qué puede proteger recuperación de sitio?

- **Máquinas virtuales Hyper-V**: recuperación de sitios puede proteger cualquier carga de trabajo que se ejecuta en una máquina virtual Hyper-V.
- **Servidores físicos**: recuperación de sitios puede proteger físicos servidores ejecutando Windows o Linux.
- **Máquinas virtuales de VMware**: recuperación de sitios puede proteger cualquier carga de trabajo que se ejecuta en una VM VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>¿Sitio recuperación admite el modelo de administrador de recursos de Azure?

Además de recuperación de sitio en el portal de clásico Azure, recuperación de sitio está disponible en el portal de Azure con soporte técnico para el Administrador de recursos. Para recuperación de sitio en el Azure mayoría de los escenarios de implementación portal ofrece una experiencia simplificada implementación y pueden replicar máquinas virtuales y servidores físicos en almacenamiento clásico o almacenamiento de administrador de recursos. Estas son las implementaciones compatibles:

- [Replicar máquinas virtuales de VMware o servidores físicos en Azure en el portal de Azure](site-recovery-vmware-to-azure.md)
- [Replicar máquinas virtuales de Hyper-V en nubes de VMM en Azure en el portal de Azure](site-recovery-vmm-to-azure.md)
- [Replicar VM de Hyper-V (sin VMM) a Azure en el portal de Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicar máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario en el portal de Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>¿Qué tengo en Hyper-V para organizar la replicación con recuperación de sitio?

Servidor de host de Hyper-V de lo que necesita depende del escenario de implementación. Consulte los requisitos previos de Hyper-V en:

- [Replicación de máquinas virtuales de Hyper-V (sin VMM) en Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicación de máquinas virtuales de Hyper-V (con VMM) en Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicación de máquinas virtuales de Hyper-V en un centro de datos secundario](site-recovery-vmm-to-vmm.md#before-you-start)

- Si va a duplicar en un centro de datos secundario, lea acerca de [compatibles con sistemas operativos para máquinas virtuales de Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si va a duplicar en Azure, sitio de recuperación es compatible con todos los sistemas operativos que son [compatibles con Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>¿Puedo proteger máquinas virtuales cuando se ejecuta Hyper-V en un sistema operativo cliente?

No, máquinas virtuales deben estar ubicadas en un servidor de host de Hyper-V que se ejecuta en un equipo con Windows server admitido. Si necesita proteger el equipo cliente se podía replicar como un equipo físico a [Azure](site-recovery-vmware-to-azure.md) o un [Centro de datos secundario](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>¿Qué cargas de trabajo puedo proteger con la recuperación de sitio?

Puede usar recuperación de sitio para proteger la mayoría de las cargas ejecutando en un servidor físico o de VM compatible. Recuperación de sitio proporciona soporte para replicación compatible con aplicaciones, para que se pueden recuperar aplicaciones a un estado inteligente. Se integra con aplicaciones de Microsoft, como SharePoint, Exchange, Dynamics, SQL Server y Active Directory y trabaja estrechamente con proveedores importantes, incluyendo Oracle, SAP, IBM y sombrero de color rojo. [Más información](site-recovery-workload.md) sobre protección de carga de trabajo.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>¿Hosts Hyper-V necesita estar en nubes de VMM?

Si desea replicar en un centro de datos secundario, máquinas virtuales de Hyper-V deben estar en Hyper-V aloja servidores que se encuentran en una nube VMM. Si desea replicar en Azure, puede replicar VM en servidores de host de Hyper-V con o sin nubes de VMM. [Obtenga más información](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>¿Puedo implementar recuperación de sitios con VMM si solo tengo un servidor VMM?

Sí. O bien puede replicar VM en servidores de Hyper-V en la nube VMM en Azure o puede replicar entre nubes de VMM en el mismo servidor. Para local a la replicación local, le recomendamos que tiene un servidor VMM en los sitios primarios y secundarios.  [Obtener más información](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>¿Qué servidores físicos puedo proteger?

Puede replicar ejecutan Windows y Linux a Azure o a un sitio secundario. [Obtenga información sobre los](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) requisitos del sistema operativo.  Si está replicar servidores físicos en Azure o en un sitio secundario se aplican los requisitos del mismos.

Tenga en cuenta que los servidores físicos se ejecutarán como máquinas virtuales en Azure si su servidor local deja de funcionar. Actualmente no es compatible la recuperación a un servidor físico local, pero puede fallar a una máquina virtual que se ejecuta en Hyper-V o sus.


### <a name="what-vmware-vms-can-i-protect"></a>¿Qué máquinas virtuales de VMware puedo proteger?

Para proteger máquinas virtuales de VMware tendrá un hipervisor vSphere y máquinas virtuales que ejecutan las herramientas de VMware. También se recomienda que tiene un servidor de vCenter VMware para administrar los hipervisores. [Obtenga más](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) información acerca de los requisitos exactos para replicar servidores VMware y máquinas virtuales en Azure o en un sitio secundario.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>¿Puedo administrar recuperación para mi sucursales con la recuperación de sitio?

Sí. Al usar recuperación de sitio para coordinar la replicación y migración tras error en las sucursales, recibirá una orquestación unificado y la vista de todas las cargas de trabajo de office rama en una ubicación central. Puede ejecutar migraciones tras error y administrar la recuperación de todas las ramas de la sede, sin necesidad de visitar la ramas fácilmente.

## <a name="security"></a>Seguridad

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>¿Se datos replicación se envía al servicio de recuperación de sitio?

No, el sitio recuperación no interceptar los datos duplicados y no tiene ninguna información sobre lo que se está ejecutando en sus máquinas virtuales o servidores físicos.
Datos de replicación se intercambian entre hosts de Hyper-V local, hipervisores de VMware, o servidores físicos y almacenamiento de Azure o el sitio secundario. Recuperación de sitio no tiene capacidad para interceptar los datos. Solo los metadatos necesarios para organizar la replicación y migración tras error se envían al servicio de recuperación de sitios.

Recuperación de sitio es ISO 27001:2013, 27018, HIPAA, DPA certificados y va a evaluar SOC2 y FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por motivos de cumplimiento, incluso los metadatos local deben permanecer en la misma región geográfica. ¿Recuperación de sitios puede ayudar a?

Sí. Cuando se crea un depósito de recuperación de sitios en una región, garantizamos que todos los metadatos que se necesita habilitar y organizar la replicación y migración tras error permanece dentro de esa región está geográficos límite.

### <a name="does-site-recovery-encrypt-replication"></a>¿Sitio recuperación cifrar replicación?

Para máquinas virtuales y servidores físicos, se admite la replicación entre sitios local cifrado en tránsito. Para máquinas virtuales y servidores físicos replicar en Azure, el cifrado en tránsito y cifrado resto (en Azure) son compatibles.


## <a name="replication"></a>Replicación


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>¿Hay requisitos previos para replicar máquinas virtuales en Azure?

Máquinas virtuales de Windows que desea replicar en Azure deben cumplir [los requisitos de Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>¿Puedo replicar máquinas virtuales de Hyper-V generación 2 a Azure?

Sí. Recuperación de sitio se convierte de generación 2 a la generación 1 durante la migración tras error. En la recuperación se convierte la máquina a la generación 2. [Obtenga más información](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Si replicar en Azure ¿cómo puedo pagar máquinas virtuales de Azure?

Durante la replicación normal, se duplica datos con el almacenamiento de Azure geo redundantes y no necesita pagar los gastos de máquina virtual de Azure IaaS, lo que proporciona una ventaja significativa. Cuando se ejecuta un error en Azure, recuperación del sitio crea automáticamente máquinas virtuales de Azure IaaS y después de que se le facturará para los recursos de cálculo consumir en Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>¿Hay un SDK que se pueden utilizar para automatizar el flujo de trabajo de ASR?

Sí. Puede automatizar los flujos de trabajo de recuperación de sitios con la API de Rest, PowerShell o el SDK de Azure. Actualmente se admiten escenarios para implementar la recuperación de sitios con PowerShell:

- [Replicar VM de Hyper-V en VMM nubes clásico de PowerShell de Azure](site-recovery-deploy-with-powershell.md)
- [Replicar VM de Hyper-V en VMM nubes para el Administrador de recursos de PowerShell de Azure](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Replicar VM de Hyper-V sin VMM clásico de PowerShell de Azure](site-recovery-hyper-v-site-to-azure-classic.md)
- [Replicar VM de Hyper-V sin VMM Administrador de recursos de PowerShell de Azure](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Si replicar en Azure ¿qué tipo de cuenta de almacenamiento que necesito?

- **Azure portal clásica**: si está implementar recuperación de sitio en el portal de clásico Azure, tendrá una [cuenta de almacenamiento geo redundantes estándar](../storage/storage-redundancy.md#geo-redundant-storage). Almacenamiento de Premium no es compatible actualmente. La cuenta debe estar en la misma región como depósito de recuperación del sitio.

- **Portal de Azure**: si está implementando recuperación de sitio en el portal de Azure, tendrá una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos son resistentes si se produce una interrupción regional, o si no se puede recuperar el área principal. La cuenta debe estar en la misma región como depósito de servicios de recuperación. Almacenamiento de Premium es compatible únicamente si está replicar máquinas virtuales de VMware o servidores físicos.

### <a name="how-often-can-i-replicate-data"></a>¿Con qué frecuencia se pueden replicar los datos?

- **Hyper-V:** Máquinas virtuales de Hyper-V se pueden replicar cada 30 segundos, 5 minutos o 15 minutos. Si ha configurado la replicación de SAN replicación es sincrónica.
- **VMware y servidores físicos:** La frecuencia de replicación no es relevante aquí. La replicación es continua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>¿Puedo ampliar la replicación desde el sitio de recuperación existente a otro sitio de tercer?

No se admite la replicación ampliada o encadenada. Solicitar esta característica en [el foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>¿Puedo hacer una réplica sin conexión la primera vez que replicar en Azure?

Esto no es compatible. Solicitar esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>¿Puedo excluir discos específicos de la replicación?

Esto es compatible cuando haya [replicar máquinas virtuales de VMware y servidores físicos](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) en Azure, con el portal de Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>¿Puedo replicar máquinas virtuales con discos dinámicos?

Se admiten discos dinámicos al replicar máquinas virtuales Hyper-V. También son compatibles al replicar máquinas virtuales de VMware y equipos físicos en Azure. El disco de sistema operativo debe ser un disco básico.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>¿Puedo agregar un nuevo equipo a un grupo de replicación existente?

Agregar nuevos equipos a los grupos de replicación existentes es compatible. Para ello, seleccione el grupo de replicación (desde el módulo de 'Elementos replicada') y el menú contextual de seleccionar y haga clic en el grupo de replicación y seleccione la opción adecuada.

![Agregar a grupo de replicación](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>¿Puedo limitar el ancho de banda asignado para el tráfico de replicación de Hyper-V?

Sí. Puede obtener más información acerca del límite de ancho de banda en los artículos de la implementación:

- [Capacidad de planificación de la replicación de máquinas virtuales de VMware y servidores físicos](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Capacidad de planificación para replicar máquinas virtuales de Hyper-V en nubes de VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Capacidad de planificación para replicar máquinas virtuales de Hyper-V sin VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Migración tras error


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Si estoy error sobre a Azure, ¿cómo puedo acceder a los equipos virtuales Azure después de la migración?

Puede obtener acceso a las máquinas virtuales de Azure mediante una conexión de Internet segura, sobre una VPN de sitio a otro o sobre Azure ExpressRoute. Debe preparar una serie de cosas para poder conectarse. Más información en:

- [Conectarse a máquinas virtuales de Azure después de la migración de máquinas virtuales de VMware o servidores físicos](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Conectarse a máquinas virtuales de Azure después de la migración de máquinas virtuales de Hyper-V en nubes de VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Conectarse a máquinas virtuales de Azure después de la migración de máquinas virtuales de Hyper-V sin VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>¿Si conmutar a Azure cómo Azure Asegúrese de que mis datos están flexibles?

Azure está diseñado para resistencia. Recuperación de sitio ya está diseñado para la migración tras error a un centro de datos Azure secundario, el acuerdo de Azure si es necesario. Si esto sucede, nos Asegúrese de que los metadatos y depósitos permanezcan dentro de la misma región geográfica que eligió para su cámara.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Si estoy replicar entre dos centros de datos ¿qué ocurre si mi centro de datos principal experimenta una interrupción inesperada?

Puede desencadenar una migración tras error planificada desde el sitio secundario. Recuperación de sitios no tenga conectividad desde el sitio principal para realizar la migración tras error.

### <a name="is-failover-automatic"></a>¿Es migración tras error automática?

Migración tras error no automático. Iniciar migraciones tras error con un solo clic en el portal, o puede usar el [Sitio recuperación de PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) para desencadenar un error. Si es una acción sencilla en el portal de recuperación del sitio.

Para automatizar podría usar Orchestrator local o Operations Manager para detectar un error de la máquina virtual y, a continuación, desencadenar la migración tras error mediante el SDK.

- [Obtenga más información](site-recovery-create-recovery-plans.md) acerca de los planes de recuperación.
- [Obtenga más información](site-recovery-failover.md) sobre la migración tras error.
- [Obtenga más información](site-recovery-failback-azure-to-vmware.md) sobre errores atrás máquinas virtuales de VMware y servidores físicos


## <a name="service-providers"></a>Proveedores de servicios


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Soy un proveedor de servicios. ¿Funciona para modelos de infraestructura compartida y dedicada de recuperación del sitio?

Sí, sitio recuperación admite ambos modelos de infraestructura dedicada y compartido.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>¿Para un proveedor de servicios, es la identidad de mi inquilino compartido con el servicio de recuperación de sitio?

No. Inquilino identidad permanece anónima. Los inquilinos no es necesario acceder al portal de recuperación del sitio. Solo el administrador del proveedor de servicio interactúa con el portal.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>¿Datos de la aplicación de inquilinos nunca irán a Azure?

Al replicar entre sitios de propiedad del proveedor de servicio, los datos de la aplicación nunca se van a Azure. Datos se cifran en tránsito y replicada directamente entre los sitios de proveedor de servicio.

Si va a duplicar en Azure, se envían los datos de aplicación para el almacenamiento de Azure pero no para el servicio de recuperación de sitios. Datos cifrados en tránsito y permanezcan cifrados en Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>¿Mi inquilinos recibirán una factura para los servicios de Azure?

No. Relación de facturación de Azure es directamente con el proveedor de servicios. Proveedores de servicios son responsables de generar facturas específicas para los inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Si estoy duplicar en Azure, ¿es necesario ejecutar máquinas virtuales de Azure en todo momento?

No, los datos se duplica en una cuenta de almacenamiento de Azure en la suscripción. Al realizar una migración de prueba tras error (ejercicio de DR) o una migración tras error real, recuperación del sitio crea automáticamente máquinas virtuales de Windows en su suscripción.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>¿Se asegura de aislamiento de nivel de inquilino al replicar en Azure?

Sí.

### <a name="what-platforms-do-you-currently-support"></a>¿Qué plataformas admite actualmente?

Se admite módulo Azure, el sistema de plataforma de nube y System Center basa implementaciones (2012 y superiores). [Más información](https://technet.microsoft.com/library/dn850370.aspx) acerca de la integración del módulo Azure y recuperación del sitio.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>¿Admiten único Azure e implementaciones de servidor VMM único?

Sí, puede replicar máquinas virtuales Hyper-V en Azure o replicar entre sitios de proveedor de servicio.  Tenga en cuenta que si replicar entre sitios de proveedor de servicio, integración runbook Azure no está disponible.


## <a name="next-steps"></a>Pasos siguientes

- Lea la [información general de recuperación del sitio](site-recovery-overview.md)
- Obtenga más información sobre la [arquitectura de recuperación de sitio](site-recovery-components.md)  
