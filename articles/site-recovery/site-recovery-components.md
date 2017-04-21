<properties
    pageTitle="¿Cómo funciona la recuperación de sitio? | Microsoft Azure"
    description="Este artículo proporciona una descripción general de la arquitectura de recuperación de sitio"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>¿Cómo funciona la recuperación de sitios de Azure?

Lea este artículo para conocer la arquitectura subyacente del servicio de recuperación de sitios de Azure y los componentes que hacen que trabajan. 

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia BCDR que determina cómo mantenerse ejecución y está disponible durante el tiempo de inactividad planificado y aplicaciones, cargas de trabajo y datos y recuperación tan pronto como sea posible a las condiciones de trabajo normal. La estrategia BCDR debe mantener datos empresariales seguros y recuperables y garantizar que las cargas de trabajo permanecen continuamente disponibles cuando se produce desastre. 

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR por coordinar la replicación de servidores físicos de local y máquinas virtuales en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en su ubicación primaria, conmutar a la ubicación secundaria para mantener las aplicaciones y cargas de trabajo disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales. Más información en [¿Qué es el sitio recuperación?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Recuperación de sitio en el portal de Azure

Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos: el modelo de administrador de recursos de Azure y el modelo de administración de servicios clásico. Azure también tiene dos portales – el [portal clásica Azure](https://manage.windowsazure.com/) que admita el modelo de implementación clásico y el [portal de Azure](https://portal.azure.com) con soporte para ambos modelos de implementación.

Recuperación de sitio está disponible en el portal de clásico y el portal de Azure. En el portal de clásico Azure puede admitir recuperación de sitios con el modelo de administración de servicios clásico. En el portal de Azure puede admitir el modelo clásico o implementaciones de modelo de recursos. [Obtenga más información](site-recovery-overview.md#site-recovery-in-the-azure-portal) acerca de la implementación con el portal de Azure.

La información en este artículo se aplica a implementaciones portal clásicas y Azure. Diferencias se indican donde sea aplicable.

## <a name="deployment-scenarios"></a>Escenarios de implementación

Puede implementar recuperación de sitio para coordinar la replicación en un número de casos:

- **Máquinas virtuales de VMware replicar**: pueden replicar máquinas virtuales de VMware local a Azure o a un centro de datos secundario.
- - **Replicar equipos físicos**: puede replicar equipos físicos que ejecuten Windows o Linux a Azure o a un centro de datos secundario. El proceso para replicar equipos físicos es prácticamente el mismo que el proceso para replicar máquinas virtuales de VMware
- **Replicar máquinas virtuales de Hyper-V (sin VMM)**: puede replicar máquinas virtuales de Hyper-V no administradas por VMM en Azure.
- **Máquinas virtuales de Hyper-V replicar administrado en nubes de System Center VMM**: puede replicar máquinas virtuales local Hyper-V que ejecutan en servidores de host de Hyper-V en nubes VMM a Azure o a un centro de datos secundario. Puede replicar utilizando estándar réplica Hyper-V o replicación de SAN.
- **Migrar VM**: puede usar el sitio recuperación para [migrar máquinas virtuales de Azure IaaS](site-recovery-migrate-azure-to-azure.md) entre regiones, o para [migrar instancias de Windows AWS](site-recovery-migrate-aws-to-azure.md) a máquinas virtuales de IaaS de Azure. Actualmente, sólo admite la migración que significa puede conmutar estas máquinas virtuales pero no puede conmutar por ellos.

Recuperación de sitios puede replicar la mayoría de aplicaciones utiliza estas máquinas virtuales y servidores físicos. Puede obtener un completo resumen de las aplicaciones admitidas en [Qué cargas de trabajo puede proteger recuperación de sitio de Azure?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Replicar en Azure: VMware máquinas virtuales o servidores físicos de Windows o Linux.

Hay dos maneras de replicar VM de VMware con la recuperación de sitios.

- **Con el portal de Azure**-cuando se implementa la recuperación de sitio en el portal de Azure puede conmutar máquinas virtuales al almacenamiento de administrador de servicio clásico o para el Administrador de recursos. Replicación de máquinas virtuales de VMware en el portal de Azure ofrece varias ventajas, incluida la capacidad de replicar a estándar o el Administrador de recursos de almacenamiento de Azure. [Más información](site-recovery-vmware-to-azure.md).
- **Con el portal clásico**-puede implementar recuperación de sitio en el portal de clásico con una experiencia mejorada. Esto se debe utilizar para las implementaciones nuevas en el portal de clásico. En esta implementación solo puede fallar sobre máquinas virtuales al almacenamiento clásica en Azure y no al almacenamiento de administrador de recursos. [Más información](site-recovery-vmware-to-azure-classic.md). También es una [experiencia heredado](site-recovery-vmware-to-azure-classic-legacy.md) para configurar la replicación de VMware en el portal de clásico. Esto no debe usarse para implementaciones nuevas.  Si ya ha implementado utilizando la experiencia heredados [Obtenga más información sobre la migración](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) a la implementación mejorada.



Los requisitos de arquitectura para implementar recuperación de sitios para replicar servidores VMware máquinas virtuales o físicas en el portal de Azure o en el portal de clásico Azure (mejorado) son similares, con un par de diferencias:

- Si implementa en el portal de Azure puede replicar al almacenamiento basado en Administrador de recursos y utilizar redes de administrador de recursos para conectar las máquinas virtuales de Azure después de la migración.
- Cuando se implementa en el portal de Azure ambos LRS y almacenamiento GRS es compatible. En el portal de clásico GRS es necesario.
- El proceso de implementación es más fácil de usar en el portal de Azure y simplificada.


Aquí es lo que necesita:

- **Cuenta de Azure**: tendrá una cuenta de Microsoft Azure.
- **Almacenamiento de Azure**: tendrá una cuenta de almacenamiento de Azure para almacenar datos duplicados. Puede usar una cuenta clásica o una cuenta de almacenamiento de administrador de recursos. La cuenta puede ser LRS o GRS cuando se implementa en el portal de Azure. Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM está terminadas cuando se produce error. 
- **Red de Azure**: tendrá una red virtual Azure que se conectará máquinas virtuales de Azure cuando se crean migración tras error. En el portal de Azure pueden ser redes creadas en el modelo de administrador de servicio clásico o en el modelo de administrador de recursos.
- **Servidor de configuración local**: tendrá un equipo de Windows Server 2012 R2 local que se ejecuta el servidor de configuración y otros componentes de recuperación del sitio. Si va a replicar máquinas virtuales de VMware debe ser una VM VMware altamente disponibles. Si desea replicar servidores físicos la máquina puede ser física. Estos componentes de recuperación de sitio se instalarán en el equipo:
    - **Servidor de configuración**: coordenadas comunicación entre su entorno local y Azure y administra la replicación de datos y la recuperación.
    - **Servidor de procesos**: actúa como una puerta de enlace de replicación. Recibe datos de replicación de equipos de origen protegida, optimiza con almacenamiento en caché, compresión y cifrado y envía los datos al almacenamiento de Azure. También controla la instalación de inserción del servicio de movilidad para equipos protegidas y realiza la detección automática de máquinas virtuales de VMware. A medida que crece la implementación puede agregar servidores adicionales proceso dedicado independiente para controlar el mayor volumen de tráfico de replicación.
    - **Servidor de destino maestro**: controla los datos de replicación durante la recuperación de Azure. 
- **Máquinas virtuales de VMware o servidores físicos para replicar**: cada equipo que desea replicar en Azure tendrán instalado el componente de servicio de movilidad. Este servicio captura la escritura de datos en el equipo y los reenvía al servidor de procesos. Este componente puede instalarse manualmente, puede se inserta ni instala automáticamente el servidor de proceso cuando se habilita la replicación de un equipo.
- **servidor de hosts/vCenter vSPhere**: necesitará uno o más servidores de host de vSphere con máquinas virtuales de VMware. Se recomienda implementar un servidor de vCenter para administrar esos hosts.
- **Recuperación**: Esto es lo necesita:
    - **No se admite la recuperación a física**: Esto significa que si conmutar servidores físicos en Azure y luego quiere conmutar, debe fallar a una VM VMware. No se producirá un error a un servidor físico. Necesitará una máquina virtual de Azure errores volver a y, si no implementar el servidor de configuración como una VM VMware deberá configurar un servidor de destino de patrón independiente como una VM VMware. Esto es necesario porque el servidor de destino maestra interactúa y adjunta al almacenamiento de VMware para restaurar los discos a una VM VMware.
    - - **Servidor de procesos temporal de Azure**: si desea un error de Azure después de la migración debe configurar una máquina virtual de Azure está configurado como un servidor de procesos, para controlar la replicación de Azure. Puede eliminar esta VM cuando termine de recuperación.
    - **Conexión VPN**: para recuperación necesitará una conexión VPN (o Azure ExpressRoute) configure desde la red de Azure en el sitio local.
    - **Local independiente dominar el servidor de destino**: el servidor de destino principal local controla la recuperación. El servidor de destino principal está instalado en el servidor de administración de forma predeterminada, pero si está experimentando problemas volver más grandes volúmenes de tráfico debe configurar un servidor de destino principal local independiente para ello.

**Arquitectura general**

![Mejorado](./media/site-recovery-components/arch-enhanced.png)

**Componentes de implementación**

![Mejorado](./media/site-recovery-components/arch-enhanced2.png)

**Recuperación**

![Recuperación mejorada](./media/site-recovery-components/enhanced-failback.png)


- [Más información](site-recovery-vmware-to-azure.md#azure-prerequisites) sobre los requisitos de implementación de portal de Azure.
- [Más información](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre los requisitos de implementación mejoradas en el portal de clásico.
- [Más información](site-recovery-failback-azure-to-vmware.md) acerca de la recuperación en el portal de Auzre.
- [Más información](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) acerca de la recuperación en el portal de clásico.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Replicar en Azure: máquinas virtuales de Hyper-V no administradas por VMM

Puede reproducir máquinas virtuales de Hyper-V no se administra System Center VMM a Azure con la recuperación de un sitio como sigue:

- **Con el portal de Azure**-cuando se implementa la recuperación de sitio en el portal de Azure puede conmutar máquinas virtuales al almacenamiento clásico o para el Administrador de recursos. [Más información](site-recovery-hyper-v-site-to-azure.md).
- **Con el portal clásico**-puede implementar recuperación de sitio en el portal de clásico. En esta implementación solo puede fallar sobre máquinas virtuales al almacenamiento clásica en Azure y no al almacenamiento de administrador de recursos. [Más información](site-recovery-hyper-v-site-to-azure-classic.md).

La arquitectura de ambas implementaciones es similar, salvo que:

- Si implementa en el portal de Azure puede replicar al almacenamiento de administrador de recursos y utilizar redes de administrador de recursos para conectar las máquinas virtuales de Azure después de la migración.
- El proceso de implementación es más fácil de usar en el portal de Azure y simplificada.

Aquí es lo que necesita:

- **Cuenta de Azure**: tendrá una cuenta de Microsoft Azure.
- **Almacenamiento de Azure**: tendrá una cuenta de almacenamiento de Azure para almacenar datos duplicados. En el portal de Azure puede usar una cuenta clásica o una cuenta de almacenamiento de administrador de recursos. En el portal clásico puede usar una cuenta de clásica. Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM se crean cuando se produce error.
- **Red de Azure**: tendrá una red de Azure máquinas virtuales de Azure se conectará cuando se crean después de la migración. 
- **Host Hyper-v**: necesitará uno o varios servidores de host de Windows Server 2012 R2 Hyper-V. Durante la implementación de recuperación de sitio deberá instalar el proveedor de servicios de recuperación de sitio de Azure y el agente de servicios de recuperación de Microsoft Azure en el host.
- **Máquinas virtuales de Hyper-V**: deberá máquinas virtuales de uno o más en el servidor de host de Hyper-V. Proveedor de recuperación de sitio Azure y el agente de servicios de recuperación de Azure en el host de Hyper-V durante la implementación de recuperación de sitios. El proveedor de coordenadas y organiza la replicación con el servicio de recuperación de sitios a través de internet. El agente controla los datos de replicación de datos sobre HTTPS 443. Comunicaciones desde el proveedor de servicios y el agente son seguros y cifrados. También se cifran los datos duplicados en el almacenamiento de Azure.

**Arquitectura general**

![Sitio de Hyper-V para Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [Más información](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sobre los requisitos de implementación de portal de Azure.
- [Más información](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sobre los requisitos de implementación de portal clásico.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Replicar en Azure: máquinas virtuales de Hyper-V administradas por VMM

Puede replicar máquinas virtuales de Hyper-V en nubes de VMM en Azure con la recuperación de un sitio como sigue:

- **Con el portal de Azure**-cuando se implementa la recuperación de sitio en el portal de Azure puede conmutar máquinas virtuales al almacenamiento clásico o para el Administrador de recursos. [Más información](site-recovery-vmm-to-azure.md).
- **Con el portal clásico**-puede implementar recuperación de sitio en el portal de clásico. En esta implementación solo puede fallar sobre máquinas virtuales al almacenamiento clásica en Azure y no al almacenamiento de administrador de recursos. [Más información](site-recovery-vmm-to-azure-classic.md).

La arquitectura de ambas implementaciones es similar, salvo que:

- Si implementa en el portal de Azure puede replicar al almacenamiento basado en Administrador de recursos y utilizar redes de administrador de recursos para conectar las máquinas virtuales de Azure después de la migración.
- El proceso de implementación es más fácil de usar en el portal de Azure y simplificada.


Aquí es lo que necesita:

- **Cuenta de Azure**: tendrá una cuenta de Microsoft Azure.
- **Almacenamiento de Azure**: tendrá una cuenta de almacenamiento de Azure para almacenar datos duplicados. En el portal de Azure puede usar una cuenta clásica o una cuenta de almacenamiento de administrador de recursos. En el portal clásico puede usar una cuenta de clásica. Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM se crean cuando se produce error.
- **Red de Azure**: deberá configurar asignación de red para que las máquinas virtuales de Azure estén conectadas a redes apropiadas cuando se crean después de la migración. 
- **Servidor VMM**: deberá necesita uno o más servidores VMM local ejecuta en System Center 2012 R2 y una configuración con uno o más nubes privadas. Si implementa en la Azure portal deberá lógicos y configuración redes de máquina virtual para que pueda configurar asignación de red. En el portal de clásico esto es opcional.  Una red de la máquina virtual debe vincularse a una red lógica que está asociada a la nube.
- **Host Hyper-v**: necesitará uno o varios servidores de host de Windows Server 2012 R2 Hyper-V en la nube VMM.
- **Máquinas virtuales de Hyper-V**: deberá máquinas virtuales de uno o más en el servidor de host de Hyper-V.

**Arquitectura general**

![VMM en Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [Más información](site-recovery-vmm-to-azure.md#azure-requirements) sobre los requisitos de implementación de portal de Azure.
- [Más información](site-recovery-vmm-to-azure-classic.md#before-you-start) sobre los requisitos de implementación de portal clásico.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replicar en un sitio secundario: VMware máquinas virtuales o servidores físicos 

Para replicar máquinas virtuales de VMware o servidores físicos en un sitio secundario como descarga InMage Scout que se incluye en la suscripción de recuperación de sitios de Azure. Puede descargarse desde el portal de Azure o desde el portal de clásico de Azure. 

Configure los servidores de componentes en cada sitio (configuración, proceso, destino principal) e instalar al agente unificada en equipos que desea duplicar. Después de la replicación inicial el agente en cada equipo envía los cambios de replicación delta al servidor de procesos. El servidor optimiza los datos y transfiere al servidor de destino principal en el sitio secundario. El servidor de configuración administra el proceso de replicación.

Aquí es lo que necesita:

**Cuenta de Azure**: implementar este escenario con InMage Scout. Para obtenerlo necesitará una suscripción de Azure. Después de crear un depósito de recuperación del sitio se descargue a InMage Scout e instalación las actualizaciones más recientes para configurar la implementación.
**Servidor de procesos (sitio principal)**: configurar el componente de servidor de proceso en el sitio principal para controlar el almacenamiento en caché, la compresión y la optimización de datos. También controla la instalación de inserción del agente unificada a los equipos que desea proteger. 
**VMware ESX/ESXi y vCenter server (sitio principal)**: si está proteger máquinas virtuales de VMware tendrá un hipervisor VMware EXS/ESXi y, opcionalmente, un servidor de vCenter VMware para administrar hipervisores.
- **Servidores de máquinas virtuales o físicas (sitio principal)**: máquinas virtuales de VMware o Windows/Linux servidores físicos que desea proteger will tienen instalado el agente unificada. El agente unificada también se instala en los equipos que actúe como el servidor de destino principal. El agente actúa como un proveedor de comunicación entre todos los componentes. 
- - **Servidor de configuración (sitio secundario)**: el servidor de configuración es el primer componente instalar y está instalado en el sitio secundario para administrar, configurar y supervisar la implementación, mediante el sitio Web de administración o la consola de vContinuum. Hay un servidor de configuración solo en una implementación y debe estar instalado en un equipo que ejecuta Windows Server 2012 R2.
- **servidor de vContinuum (sitio secundario)**: se instala en la misma ubicación (sitio secundario) como el servidor de configuración. Proporciona una consola de administración y supervisión de su entorno protegido. En una instalación predeterminada, el servidor vContinuum es el primer servidor de destino principal y ha instalado el agente unificada.
- **Servidor de destino (sitio secundario) maestro**: el servidor de destino principal contiene datos duplicados. Recibe datos desde el servidor de procesos, crea una máquina de réplica en el sitio secundario y contiene los puntos de retención de datos. El número de servidores de destino principal que necesita depende del número de equipos que va a proteger. Si desea un error al sitio principal tendrá un servidor de destino principal hay demasiado. 

**Arquitectura general**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Replicar en un sitio secundario: máquinas virtuales de Hyper-V administradas por VMM


Puede reproducir máquinas virtuales de Hyper-V administradas por System Center VMM a un centro de datos secundario con la recuperación de un sitio como sigue:

- **Con el portal de Azure**-cuando se implementa la recuperación de sitio en el portal de Azure. [Más información](site-recovery-hyper-v-site-to-azure.md).
- **Con el portal clásico**-puede implementar recuperación de sitio en el portal de clásico. [Más información](site-recovery-hyper-v-site-to-azure-classic.md).

La arquitectura de ambas implementaciones es similar, salvo que:

- Si implementa en el portal de Azure debe configurar la asignación de red. Esto es opcional en el portal de clásico.
- El proceso de implementación es más fácil de usar en el portal de Azure y simplificada.
- - Si implementa en la Azure portal clásica [asignación de almacenamiento](site-recovery-storage-mapping.md) está disponible.

Aquí es lo que necesita:

- **Cuenta de Azure**: tendrá una cuenta de Microsoft Azure.
- **Servidor VMM**: se recomienda un servidor VMM en el sitio primario y en el sitio secundario, cada una con al menos una nube privada de VMM. El servidor debe ejecutar al menos System Center 2012 SP1 con las actualizaciones más recientes y conectado a internet. Nubes deben tener el perfil de capacidad de Hyper-V establecer. Deberá instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM. El proveedor de coordenadas y organiza la replicación con el servicio de recuperación de sitios a través de internet. Comunicaciones entre el proveedor y Azure son seguros y cifrados.
- **Hyper-V server**: servidores de host de Hyper-V deben estar ubicados en las nubes VMM primaria y secundarias. El host de servidores deben ejecutar al menos Windows Server 2012 con las últimas actualizaciones instaladas y conectado a internet. Datos es replicar entre los servidores de host de Hyper-V primario y secundarios en la LAN o VPN utilizando la autenticación Kerberos o certificado.  
- **Máquinas protegida**: el servidor de host de origen Hyper-V debe tener al menos una máquina virtual que desea proteger.

**Arquitectura general**

![Local a local](./media/site-recovery-components/arch-onprem-onprem.png)


- [Más información](site-recovery-vmm-to-vmm.md#azure-prerequisites) sobre los requisitos de implementación en el portal de Azure.
- - [Más información](site-recovery-vmm-to-vmm-classic.md#before-you-start) sobre los requisitos de implementación en el portal de clásico de Azure.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Replicar en un sitio secundario con replicación de SAN: máquinas virtuales de Hyper-V administradas por VMM

Puede replicar máquinas virtuales de Hyper-V administradas en nubes VMM en un sitio secundario con replicación de SAN con el portal de clásico de Azure. Este escenario no es compatible actualmente en el nuevo portal de Azure. 

En este escenario durante la implementación de recuperación de sitio deberá instalar el proveedor de servicios de recuperación de Azure sitio en servidores VMM. El proveedor de coordenadas y organiza la replicación con el servicio de recuperación de sitios a través de internet. Datos es replicar entre las matrices de almacenamiento primario y secundario mediante la replicación de SAN sincrónica.

Aquí es lo que necesita:

**Cuenta de Azure**: necesitará una suscripción de Azure
- **Matriz SAN**: [admite matriz SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) administrados por el servidor VMM principal. SAN comparte una infraestructura de red con otra matriz de SAN en el sitio secundario.
- **Servidor VMM**: se recomienda un servidor VMM en el sitio primario y en el sitio secundario, cada una con al menos una nube privada de VMM. El servidor debe ejecutar al menos System Center 2012 SP1 con las actualizaciones más recientes y conectado a internet. Nubes deben tener el perfil de capacidad de Hyper-V establecer.
- **Hyper-V server**: servidores de host de Hyper-V ubicados en las nubes VMM primaria y secundarias. El host de servidores deben ejecutar al menos Windows Server 2012 con las últimas actualizaciones instaladas y conectado a internet.
- **Máquinas protegida**: el servidor de host de origen Hyper-V debe tener al menos una máquina virtual que desea proteger.

**Arquitectura de replicación de SAN**

![Replicación de SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Más información](site-recovery-vmm-san.md#before-you-start) sobre los requisitos de implementación.
### <a name="on-premises"></a>Local



## <a name="hyper-v-protection-lifecycle"></a>Ciclo de vida de protección de Hyper-V

Este flujo de trabajo muestra el proceso para proteger, replicar y experimentando problemas máquinas virtuales Hyper-V. 

1. **Habilitar protección**: configurar la cámara de recuperación del sitio, configurar la replicación de una nube VMM o un sitio de Hyper-V y habilitar la protección de máquinas virtuales. Un trabajo de **Habilitar protección** se inicia y puede supervisar en la ficha **trabajos** . El trabajo comprueba que el equipo cumple con los requisitos previos y, a continuación, invoca el método de [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que establece la replicación de Azure con la configuración que ha configurado. El trabajo de **Habilitar protección** también invoca el método de [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar una réplica de VM completa.
2. **Replicación inicial**: se toma una instantánea de máquina virtual y discos duro virtuales están replicada uno por uno hasta que está todas copiados a Azure o al centro de datos secundario. El tiempo necesario para completar esta depende del tamaño de la memoria virtual, ancho de banda y el método de replicación inicial. Si se producen cambios de disco mientras la replicación inicial está en curso el seguimiento de replicación de réplica de Hyper-V realiza un seguimiento de los cambios como registros de replicación de Hyper-V (.hrl) que se encuentran en la misma carpeta que los discos. Cada disco tiene un archivo de .hrl asociado que se enviarán a almacenamiento secundario. Tenga en cuenta que los archivos de registro e instantánea consumen recursos de disco mientras la replicación inicial está en curso. Cuando finalice la replicación inicial se elimina la instantánea de la VM y se sincronizan y se combinan los cambios de disco delta en el registro.
3. **Protección de Finalize**: cuando termine de replicación inicial el trabajo de **protección Finalize** configura red y otras opciones de configuración posteriores a la replicación para que la máquina virtual está protegida. Si va a duplicar en Azure debe ajustar la configuración de la máquina virtual para que esté listo para la migración tras error. En este momento puede ejecutar un error de prueba para comprobar que todo funciona según lo esperado.
4. **Replicación**: después de la replicación inicial comienza sincronización delta, según la configuración de replicación. 
    - **Error de replicación**: si falla la replicación delta y una réplica completa sería costosa en términos de ancho de banda o una hora, se produce la sincronización. Por ejemplo, si los archivos de .hrl llegar a 50% del tamaño del disco, a continuación, la máquina virtual se marcará para la sincronización. Sincronización minimiza la cantidad de datos enviados por calculando los totales de las máquinas virtuales de origen y destino y enviar solo el delta. Cuando termine de sincronización se reanude la replicación delta. De forma predeterminada sincronización está programada para que se ejecute automáticamente fuera de las horas de office, pero puede volver a sincronizar una máquina virtual manualmente.
    - **Error de réplica**: si se produce un error de replicación hay un reintento integrado. Si es un error no recuperable, como un error de autenticación o autorización, o un equipo de réplica está en un estado no válido, no se intentará ningún reintento. Si se produce un error recuperable como un error de red o memoria de espacio de disco, un reintento ocurre con mayor intervalos entre reintentos (1, 2, 4, 8, 10 y, a continuación, cada 30 minutos).
4. **Failovers planificada o no planificada**: puede ejecutar failovers planificados o según sea necesario. Si ejecutar un failover planificado a un origen de máquinas virtuales se cerró para no garantizar pérdida de datos. Después de crearán máquinas virtuales de réplica se coloquen en una confirmación de estado pendiente. Necesita confirmar para completar la migración tras error, a menos que esté replicar con SAN en cuyo caso confirmación automática. Después de configurar y ejecutar el sitio primario puede ocurrir recuperación. Si ha replicada en Azure inversa replicación es automática. En caso contrario, empezar inversa replicación manualmente.
 

![flujo de trabajo](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Pasos siguientes

[Prepararse para su implementación](site-recovery-best-practices.md)
