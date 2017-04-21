<properties
   pageTitle="Orientación técnica: recuperación locales con Azure | Microsoft Azure"
   description="Artículo en Descripción y recuperación de diseño de sistemas de infraestructura de local a Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Orientación técnica de Azure resistencia: recuperación locales con Azure

Azure proporciona un conjunto completo de servicios para habilitar la extensión de un centro de datos local en Azure fines de alta disponibilidad y desastres recuperación:

* __Redes__: con una red privada virtual segura de ampliar su red local en la nube.
* __Calcular__: los clientes que usan Hyper-V local pueden "Levantar y Mayús" máquinas virtuales (VM) existentes en Azure.
* __Almacenamiento__: StorSimple amplía el sistema de archivos al almacenamiento de Azure. El servicio de copia de seguridad de Azure proporciona copia de seguridad para los archivos y las bases de datos SQL con el almacenamiento de Azure.
* __Replicación de base de datos__: SQL Server 2014 (o posterior) disponibilidad grupos, puede implementar alta disponibilidad y recuperación de desastres para los datos locales.

##<a name="networking"></a>Redes

Puede usar una red Virtual Azure para crear una sección lógicamente aislada en Azure y segura conectarse a su centro de datos local o en un equipo de solo cliente mediante una conexión IPsec. Con una red Virtual, puede aprovechar las ventajas de la infraestructura scalable a petición en Azure mientras que permite la conectividad a datos y aplicaciones locales, incluidos los sistemas de ejecución en Windows Server, grandes sistemas y UNIX. Para obtener más información, consulte la [documentación de redes de Azure](../virtual-network/virtual-networks-overview.md) .

##<a name="compute"></a>Calcular

Si está utilizando Hyper-V local, puede "Levantar y Mayús" existentes máquinas virtuales de Azure y ejecuta Windows Server 2012 (o posterior), sin realizar cambios en la máquina virtual o convertir VM formatos de proveedores de servicios. Para obtener más información, consulte [acerca de los discos y VHD para máquinas virtuales de Windows Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Recuperación de sitio de Azure

Si desea recuperación como un servicio (DRaaS), Azure proporciona [Recuperación de sitio de Azure](https://azure.microsoft.com/services/site-recovery/). Recuperación de sitio de Azure ofrece protección completa para servidores de VMware, Hyper-V y físicas. Con la recuperación de sitio de Azure, puede usar otro servidor local o Azure como su sitio de recuperación. Para obtener más información sobre la recuperación de sitio de Azure, consulte la [documentación de recuperación de sitio de Azure](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Almacenamiento de información

Hay varias opciones para el uso de Azure como un sitio de copia de seguridad de datos locales.

###<a name="storsimple"></a>StorSimple

StorSimple segura y transparente integra almacenamiento de nube para las aplicaciones locales. También ofrece un único dispositivo que ofrece local en niveles de alto rendimiento y almacenamiento de la nube, archivado live, protección de datos basados en la nube y de recuperación. Para obtener más información, consulte la [página de producto de StorSimple](https://azure.microsoft.com/services/storsimple/).

###<a name="azure-backup"></a>Copia de seguridad de Azure

Copia de seguridad de Azure permite realizar copias de seguridad de la nube mediante herramientas familiares de copia de seguridad de Windows Server 2012 (o posteriores), Windows Server 2012 Essentials (o posterior) y el Administrador de protección de datos de System Center 2012 (o posterior). Estas herramientas proporcionan un flujo de trabajo para la administración de copia de seguridad es independiente de la ubicación de almacenamiento de las copias de seguridad, si un disco local o en el almacenamiento de Azure. Después de que se realizan copias de seguridad de los datos en la nube, los usuarios autorizados pueden recuperar fácilmente las copias de seguridad a cualquier servidor.

Con copias de seguridad incrementales, solo los cambios en archivos se transfieren a la nube. Esto le permite usar espacio de almacenamiento eficaz, reducir el consumo de ancho de banda y compatible con la recuperación de punto en el tiempo de varias versiones de los datos. También puede usar características adicionales, como las directivas de retención de datos, la compresión de datos y limitación de transferencias de datos. Uso de Azure como la ubicación de copia de seguridad tiene la ventaja evidente que las copias de seguridad automáticamente son "fuera del sitio". Esto elimina los requisitos adicionales para proteger y medios de copia de seguridad en el sitio.

Para obtener más información, vea [¿Qué es la copia de seguridad de Azure?](../backup/backup-introduction-to-azure-backup.md) y [Azure Configurar copia de seguridad de datos DPM](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Base de datos

Puede tener una solución de recuperación de desastres para las bases de datos de SQL Server en un entorno de TI híbrido con grupos de disponibilidad AlwaysOn, reflejo de base de datos, envío de registros y copia de seguridad y restaurar con el almacenamiento de blobs de Windows Azure. Todas estas soluciones usan SQL Server se ejecuta en Azure máquinas virtuales de Windows.

Grupos de disponibilidad AlwaysOn puede usarse en un entorno de TI híbrido donde réplicas de base de datos existen dos local y en la nube. Esto se muestra en el diagrama siguiente.

![Grupos de disponibilidad de SQL Server AlwaysOn en una arquitectura de nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

También puede abarcar base de datos que coinciden con los servidores local y la nube en una instalación basada en el certificado. El siguiente diagrama muestra este concepto.

![Reflejo de base de datos de SQL Server en una arquitectura de nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

Envío de registro puede utilizarse para sincronizar una base de datos local con una base de datos de SQL Server en una máquina virtual Azure.

![Registro de SQL Server en una arquitectura de nube híbrida de envío](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Por último, puede crear una copia de una base de datos local directamente almacenamiento de blobs de Windows Azure.

![Cuando se realiza una copia de seguridad de SQL Server con el almacenamiento de blobs de Windows Azure en una arquitectura de nube híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Para obtener más información, consulte [alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Windows Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) y [copia de seguridad y restauración de SQL Server en máquinas virtuales de Windows Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Listas de comprobación para la recuperación local en Microsoft Azure

###<a name="networking"></a>Redes

  1. Revise la sección de redes de este documento.
  2. Usar una red Virtual para conectar de forma segura en local en la nube.

###<a name="compute"></a>Calcular

  1. Revise la sección de cálculo de este documento.
  2. Reubicar máquinas virtuales entre Hyper-V y Azure.

###<a name="storage"></a>Almacenamiento de información

  1. Revise la sección de almacenamiento de este documento.
  2. Aproveche las ventajas de servicios de StorSimple para el uso de almacenamiento de la nube.
  3. Usar el servicio de copia de seguridad de Azure.

###<a name="database"></a>Base de datos

  1. Revise la sección de la base de datos de este documento.
  2. Considere la posibilidad de usar SQL Server en máquinas virtuales de Azure como la copia de seguridad.
  3. Configurar grupos de disponibilidad AlwaysOn.
  4. Configurar el reflejo de base de datos basados en certificados.
  5. Use el envío de registro.
  6. Copia de seguridad de las bases de datos local con el almacenamiento de blobs de Windows Azure.

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie centrada en [orientación técnica de la resistencia de Azure](./resiliency-technical-guidance.md). El siguiente artículo de esta serie es la [recuperación de daños en los datos o el borrado accidental](./resiliency-technical-guidance-recovery-data-corruption.md).