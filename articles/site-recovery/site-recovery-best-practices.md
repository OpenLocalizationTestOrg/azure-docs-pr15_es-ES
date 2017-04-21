<properties
    pageTitle="Prepararse para la implementación de recuperación del sitio | Microsoft Azure"
    description="Este artículo describe cómo obtener listo para implementar la replicación con la recuperación de sitios de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Prepararse para la implementación de recuperación de sitios de Azure

Lea este artículo para ver una introducción de alto nivel de los requisitos de implementación para cada escenario de replicación compatibles con el servicio de recuperación de sitios de Azure. Después de leer los requisitos generales para cada escenario, puede vincular a los detalles de implementación específicos para cada implementación.

Después de leer este artículo publicar comentarios o preguntas en la parte inferior del artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia BCDR que determina cómo mantenerse ejecución y está disponible durante el tiempo de inactividad planificado y aplicaciones, cargas de trabajo y datos y recuperación tan pronto como sea posible a las condiciones de trabajo normal. La estrategia BCDR debe mantener datos empresariales seguros y recuperables y garantizar que las cargas de trabajo permanecen continuamente disponibles cuando se produce desastre. 

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR por coordinar la replicación de servidores físicos de local y máquinas virtuales en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en su ubicación primaria, conmutar a la ubicación secundaria para mantener las aplicaciones y cargas de trabajo disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales. Más información en [¿Qué es el sitio recuperación?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Seleccione el modelo de implementación

Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos: el modelo de administrador de recursos de Azure y el modelo de administración de servicios clásico. Azure también tiene dos portales – el [portal clásica Azure](https://manage.windowsazure.com/) que admita el modelo de implementación clásico y el [portal de Azure](https://ms.portal.azure.com/) con soporte para ambos modelos de implementación.

Recuperación de sitio está disponible en el portal de clásico y el portal de Azure. En el portal de clásico Azure puede admitir recuperación de sitios con el modelo de administración de servicios clásico. En el portal de Azure puede admitir el modelo clásico o implementaciones del Administrador de recursos. [Obtenga más información](site-recovery-overview.md#site-recovery-in-the-azure-portal) acerca de la implementación con el portal de Azure.

Cuando escoge una nota de modelo de implementación que:

- Se recomienda que usar el [portal de Azure](https://portal.azure.com) y el modelo de administrador de recursos siempre que sea posible.
- Recuperación de sitio proporciona una más fácil y más intuitiva Introducción experiencia en el portal de Azure.
- El portal de Azure pueden replicar máquinas clásica y almacenamiento del Administrador de recursos en Azure. Además, puede usar LRS o GRS cuentas de almacenamiento en el portal de Azure.
- El portal de Azure combina los servicios de copia de seguridad y recuperación de sitio en un único depósito de servicios de recuperación para que puedan configurar y administrar servicios BCDR desde una única ubicación.
- Los usuarios con suscripciones a Azure aprovisionados con el programa de proveedor de soluciones de nube (CSP) ahora pueden administrar operaciones de recuperación de sitio en el portal de Azure.
- Replicar máquinas virtuales de VMware o equipos físicos en Azure en el portal de Azure proporciona varias características nuevas como soporte para el almacenamiento de premium y la capacidad para excluir discos específicos de la replicación.


## <a name="select-your-deployment-scenario"></a>Seleccione el escenario de implementación

**Implementación** | **Detalles** | **Portal de Azure** | **Portal clásica** | **PowerShell**
---|---|---|---|---
**Máquinas virtuales de VMware en Azure** | Replicar VM de VMware con el almacenamiento de Azure | En la Azure puede conmutar portal máquinas virtuales a estándar o de almacenamiento de administrador de recursos<br/><br/> Implementación en el [portal de Azure](site-recovery-vmware-to-azure.md) ofrece una experiencia simplificada de implementación y un número de ventajas de la característica. | En el portal de clásico Azure puede implementar con el [modelo mejorado](site-recovery-vmware-to-azure-classic.md) y conmutar a almacenamiento de Azure clásico.<br/><br/> También es un modelo de heredados no debe usarse para las implementaciones de nuevas. |  PowerShell actualmente no es compatible.
**Máquinas virtuales de Hyper-V en Azure** | Máquinas virtuales de Hyper-V para el almacenamiento de Azure. Pueden ser máquinas virtuales en hosts Hyper-V administrados en nubes de System Center VMM o sin VMM. | En la Azure portal máquinas virtuales puede conmutar a estándar o de almacenamiento de administrador de recursos.<br/><br/> El portal de Azure ofrece una experiencia de implementación optimizada. [Más información](site-recovery-vmm-to-azure.md) acerca de cómo replicar máquinas virtuales de Hyper-V en nubes de VMM. [Más información](site-recovery-hyper-v-site-to-azure.md) acerca de cómo replicar máquinas virtuales de Hyper-V (sin VMM).| En el portal de Azure clásico puede conmutar máquinas virtuales con el almacenamiento de Azure clásica | Implementación de PowerShell es compatible.
**Servidores físicos en Azure** | Replicar servidores Windows o Linux físicos para el almacenamiento de Azure | En la Azure servidores de portal pueden conmutar a estándar o de almacenamiento de administrador de recursos.<br/><br/> Implementación en el [portal de Azure](site-recovery-vmware-to-azure.md) ofrece una experiencia simplificada de implementación y un número de ventajas de la característica. | En el portal de clásico Azure puede implementar con el [modelo mejorado](site-recovery-vmware-to-azure-classic.md) y conmutar a almacenamiento de Azure clásico.<br/><br/> También es un modelo de heredados no debe usarse para las implementaciones de nuevas. | Implementación de PowerShell actualmente no es compatible.
**Servidores de máquinas virtuales de VMware o físico al sitio secundario* | Replicar máquinas virtuales de VMware o servidores Windows o Linux físicos en un sitio secundario. [Obtener más información y descargar](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) la Guía del usuario InMage Scout. | No está disponible en el portal de Azure | En el portal de clásico deberá descargar a InMage Scout desde un depósito de recuperación del sitio. | Implementación de PowerShell actualmente no es compatible
**Máquinas virtuales de Hyper-V para un sitio secundario** | Replicar VM de Hyper-V en nubes VMM una nube secundario | En el [portal de Azure](site-recovery-vmm-to-vmm.md) pueden replicar máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario con Hyper-V réplica solo. SAN no es compatible actualmente. | En el portal de clásico Azure pueden replicar máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario con [Hyper-V réplica](site-recovery-vmm-to-vmm-classic.md) o [replicación de SAN](site-recovery-vmm-san.md) | Se admite la implementación de PowerShell



## <a name="check-what-you-need-for-deployment"></a>Comprobar lo que necesita para su implementación

### <a name="replicate-to-azure"></a>Replicar en Azure

**Requisito** | **Detalles** 
---|---
**Cuenta de Azure** | Tendrá una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**Almacenamiento de Azure** | Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM se crean cuando se produce error. Para replicar en Azure, tendrá una [cuenta de almacenamiento de Azure](../storage/storage-introduction.md).<br/><br/>Si está implementando recuperación de sitio en el portal de clásico tendrá una o varias [cuentas de almacenamiento GRS estándar](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Si implementa en el portal de Azure puede usar almacenamiento GRS o LRS.<br/><br/> Si va a replicar máquinas virtuales de VMware o servidores físicos en el almacenamiento de Azure premium portal es compatible. Tenga en cuenta que si usa una cuenta de almacenamiento premium también tendrá una cuenta de almacenamiento estándar para almacenar registros de replicación que capturan los cambios en curso a datos locales. [Almacenamiento de Premium](../storage/storage-premium-storage.md) normalmente se usa para máquinas virtuales que necesitan un rendimiento de IO alto y baja latencia para cargas de trabajo intensivo IO de host.<br/><br/> Si desea usar una cuenta de premium para almacenar datos duplicados, también deberá una cuenta de almacenamiento estándar para almacenar registros de replicación que capturan los cambios en curso a datos locales.
**Red de Azure** | Para replicar en Azure, necesitará una red de Azure máquinas virtuales de Azure se conectará cuando se crean después de la migración.<br/><br/> Si implementa en el portal de clásico usará una red clásica. Si está implementando en el portal de Azure, puede usar un clásico o red del Administrador de recursos.<br/><br/> La red debe estar en la misma región como la cámara.
**Asignación de red (VMM Azure)** | Si va a duplicar desde VMM en Azure, [asignación de red](site-recovery-network-mapping.md) garantiza que máquinas virtuales de Azure están conectadas a redes correctas después de la migración.<br/><br/> Para configurar la asignación de red necesita configurar redes VM en el portal VMM.
**Local** | **Máquinas virtuales de VMware**: necesitará un equipo local ejecutar componentes de recuperación del sitio, VMware vSphere hosts/vCenter server y máquinas virtuales que desea duplicar. [Obtenga más información](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Servidores físicos**: si está replicar servidores físicos tendrá un máquinas local ejecutar componentes de recuperación de sitios y servidores físicos que desea duplicar. [Obtenga más información](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Si desea [conmutar](site-recovery-failback-azure-to-vmware.md) después de la migración a Azure tendrá una infraestructura VMware hacerlo.<br/><br/> **Máquinas virtuales de Hyper-V**: si desea replicar VM Hyper-V en nubes VMM tendrá un servidor VMM y se encuentran hosts Hyper-V en qué máquinas virtuales que desea proteger. [Obtenga más información](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Si desea replicar VM Hyper-V sin VMM deberá hosts Hyper-V en el que se encuentran máquinas virtuales. [Obtenga más información](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Máquinas protegidos** | Máquinas protegidas que se replican en Azure deben cumplir con los [requisitos previos de Azure](#azure-virtual-machine-requirements) se describe a continuación.


### <a name="replicate-to-a-secondary-site"></a>Replicar en un sitio secundario

**Requisito** | **Detalles** 
---|---
**Cuenta de Azure** | Tendrá una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**Local** | **Máquinas virtuales de VMware**: en el sitio primario tendrá un servidor de procesos de almacenamiento en caché, compresión y cifrado de los datos de replicación antes de enviarlo al sitio secundario. En el sitio secundario deberá instalar un servidor de configuración para administrar y supervisar la implementación y un servidor de destino principal. También se recomienda un servidor vContinuum para facilitar la administración. Además, necesita uno o más VMware vSphere hosts y, opcionalmente, un servidor de vCenter. [Obtener más información](site-recovery-vmware-to-vmware.md)<br/><br/> **Máquinas virtuales de Hyper-V en nubes de VMM**: tendrá que configurar los servidores VMM y hosts de Hyper-V que contiene máquinas virtuales que desea duplicar. [Obtenga más información](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Asignación de red (VMM VMM)** | Si va a duplicar desde VMM en VMM, [asignación de red](site-recovery-network-mapping.md) garantiza que máquinas virtuales de réplica estén conectadas a redes apropiadas después de la migración y óptima se colocan en hosts Hyper-V. Para configurar la asignación de red necesita configurar redes VM en los servidores VMM.
**Asignación de almacenamiento** | Si va a duplicar desde VMM en VMM, opcionalmente, puede configurar [asignación de almacenamiento](site-recovery-storage-mapping.md) para especificar el destino de almacenamiento para máquinas virtuales duplicadas. Asignación de almacenamiento se puede configurar para la replicación de réplica de Hyper-V y SAN.<br/><br/> Asignación de almacenamiento no es compatible actualmente en el portal de Azure.


## <a name="verify-url-access"></a>Comprobar el acceso de URL

Asegúrese de que estas direcciones URL están accesibles desde servidores.

**DIRECCIÓN URL** | **VMM a VMM** | **VMM en Azure** | **Hyper-V para Azure** | **VMware en Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Permitir | Permitir | Permitir | Permitir
*. backup.windowsazure.com | No es necesario | Permitir | Permitir | Permitir
*. hypervrecoverymanager.windowsazure.com | Permitir | Permitir | Permitir | Permitir
*. store.core.windows.net | Permitir | Permitir | Permitir | Permitir
*. blob.core.windows.net | No es necesario | Permitir | Permitir | Permitir
https://www.msftncsi.com/ncsi.txt | Permitir | Permitir | Permitir | Permitir
https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | No es necesario | No es necesario | No es necesario | Permitir

## <a name="azure-virtual-machine-requirements"></a>Requisitos de Azure máquina virtual

Puede implementar la recuperación de sitios para replicar máquinas virtuales y servidores físicos con cualquier sistema operativo compatible con Azure. Esto incluye la mayoría de las versiones de Windows y Linux. Debe asegurarse de que máquinas virtuales que desea proteger cumplir con los requisitos de Azure en local.


**Característica** | **Requisitos** | **Detalles**
---|---|---
Host Hyper-v. | Debe ejecutar Windows Server 2012 R2 | Comprobación de requisitos previos se producirá un error si el sistema operativo no compatible
Hipervisor de VMware  | Sistema operativo compatible | [Comprobar los requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operativo invitado | Hyper-V para replicación Azure: recuperación de sitio es compatible con todos los sistemas operativos que son [compatibles con Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidor físico y VMware: comprobar los [requisitos previos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) de Windows y Linux | Comprobación de requisitos previos se producirá un error si no es compatible.
Arquitectura de sistema operativo de invitado | 64 bits | Comprobación de requisitos previos se producirá un error si no compatibles
Tamaño del disco de sistema operativo |  1023 GB | Comprobación de requisitos previos se producirá un error si no compatibles
Número de discos de sistema operativo | 1 | Comprobación de requisitos previos se producirá un error si no es compatible.
Número de discos de datos | 16 o menos (el valor máximo es una función del tamaño de la máquina virtual se ha creado. 16 = XL) | Comprobación de requisitos previos se producirá un error si no compatibles
Tamaño del disco duro virtual de disco de datos | Hasta 1023 GB | Comprobación de requisitos previos se producirá un error si no compatibles
Adaptadores de red | Se admiten varios adaptadores |
Dirección IP estática | Compatibles | Si la máquina virtual principal está usando una dirección IP estática puede especificar la dirección IP estática de la máquina virtual que se creará en Azure. Tenga en cuenta que la dirección IP estática para una máquina virtual de linux ejecutando en Hyper-v no es compatible.
disco iSCSI | No compatible | Comprobación de requisitos previos se producirá un error si no compatibles
Disco duro de virtual compartido | No compatible | Comprobación de requisitos previos se producirá un error si no compatibles
Disco FC | No compatible | Comprobación de requisitos previos se producirá un error si no compatibles
Formato de disco duro| DISCO DURO VIRTUAL <br/><br/> VHDX | Aunque VHDX no es compatible actualmente en Azure, sitio recuperación convierte automáticamente VHDX en disco duro virtual al conmutar a Azure. Cuando no volver a local las máquinas virtuales seguir usando el formato VHDX.
BitLocker | No compatible | BitLocker debe estar deshabilitado antes de proteger una máquina virtual.
Nombre de la máquina virtual| Entre 1 y 63 caracteres. Restringido a letras, números y guiones. Debe iniciar y terminar con una letra o número | Actualizar el valor de las propiedades de la máquina virtual en el sitio de recuperación
Tipo de máquina virtual | <p>Generación 1</p> <p>Generación 2: Windows</p> |  Máquina virtual de generación 2 con el tipo de disco de sistema operativo de disco básico que incluye 1 o 2 volúmenes de datos con formato de disco como VHDX que es menor que 300GB es compatible. Máquinas virtuales de Linux generación 2 no son compatibles. [Obtenga más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Optimizar la implementación

Utilice las siguientes sugerencias para ayudarle a optimizar y escalar su implementación.

- **Tamaño de volumen de sistema operativo**: cuando se duplica una máquina virtual a Azure el volumen del sistema operativo debe ser menor que 1 TB. Si tiene más volúmenes más manualmente puede moverlos a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si va a duplicar en Azure puede tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Un modo eficaz, puede replicar y conmutar una máquina virtual de ~ 32 TB.
- **Límites del plan de recuperación**: recuperación de sitios puede escalar a miles de máquinas virtuales de Windows. Planes de recuperación están diseñados como un modelo para las aplicaciones que debe conmutar entre sí para que se limita el número de equipos en un plan de recuperación a 50.
- **Límites de servicio Azure**: Azure cada suscripción incluye un conjunto de límites predeterminados en núcleos, etcetera de servicios de nube. Se recomienda que ejecute un error de prueba para validar la disponibilidad de recursos en la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planificación de capacidad**: Lea acerca de [planificación de la capacidad](site-recovery-capacity-planner.md) de recuperación de sitios.
- **Ancho de banda de replicación**: si es breve en ancho de banda de replicación tenga en cuenta que:
    - **ExpressRoute**: sitio recuperación funciona con Azure ExpressRoute y WAN optimizadores como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
    - **Tráfico de replicación**: usos de recuperación de sitio realiza una inteligente usando solo los bloques de datos y no el disco duro virtual todo la replicación inicial. Solo los cambios se duplican durante la replicación en curso.
    - **El tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [QoS de Windows](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto.  Además, si está duplicar en Azure recuperación de sitios con el agente de copia de seguridad de Azure puede configurar el límite para que el agente. [Obtenga más información](https://support.microsoft.com/kb/3056159).
- **RTO**: medir el objetivo de tiempo de recuperación (RTO) puede esperar con la recuperación de sitios, le recomendamos ejecutar un error de prueba y ver los trabajos de recuperación de sitio para analizar cuánto tiempo se tarda en completar las operaciones. Si está experimentando problemas a Azure, para el mejor RTO se recomienda que automatizar todas las acciones manuales mediante la integración con Azure planes de automatización y la recuperación.
- **RPO**: recuperación de sitio es compatible con un objetivo de punto de recuperación cerca sincrónico (RPO) al replicar en Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.


##<a name="service-urls"></a>Direcciones URL de servicios
Asegúrese de que estas direcciones URL están accesibles desde el servidor


**Direcciones URL** | **VMM a VMM** | **VMM en Azure** | **Sitio de Hyper-V para Azure** | **VMware en Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | Se requiere acceso  | Se requiere acceso  | Se requiere acceso  | Se requiere acceso
 \*. backup.windowsazure.com |  | Se requiere acceso  | Se requiere acceso  | Se requiere acceso
 \*. hypervrecoverymanager.windowsazure.com | Se requiere acceso  | Se requiere acceso  | Se requiere acceso  | Se requiere acceso
 \*. store.core.windows.net | Se requiere acceso  | Se requiere acceso  | Se requiere acceso  | Se requiere acceso
 \*. blob.core.windows.net |  | Se requiere acceso  | Se requiere acceso  | Se requiere acceso
 https://www.msftncsi.com/ncsi.txt | Se requiere acceso  | Se requiere acceso  | Se requiere acceso  | Se requiere acceso
 https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | Se requiere acceso


## <a name="next-steps"></a>Pasos siguientes

Después de aprendizaje y comparar los requisitos de implementación general puede leer los requisitos previos detallados y empezar a implementar cada escenario.

- [Replicar máquinas virtuales de VMware en Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicar servidores físicos en Azure](site-recovery-vmware-to-azure-classic.md)
- [Duplicar servidor Hyper-V en nubes VMM en Azure](site-recovery-vmm-to-azure.md)
- [Replicar máquinas virtuales de Hyper-V (sin VMM) en Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicar VM de Hyper-V para un sitio secundario](site-recovery-vmm-to-vmm.md)
- [Replicar VM de Hyper-V para un sitio secundario con SAN](site-recovery-vmm-san.md)
- [Replicar VM de Hyper-V con un solo servidor VMM](site-recovery-single-vmm.md)
