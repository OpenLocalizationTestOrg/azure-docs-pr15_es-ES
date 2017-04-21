<properties
    pageTitle="Directrices de soluciones de almacenamiento | Microsoft Azure"
    description="Obtenga información sobre las directrices de diseño e implementación claves para implementar soluciones de almacenamiento de servicios de infraestructura de Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="storage-infrastructure-guidelines"></a>Directrices de infraestructura de almacenamiento

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

En este artículo se centra en comprender las necesidades de almacenamiento y consideraciones de diseño para lograr un rendimiento óptimo máquina virtual ().


## <a name="implementation-guidelines-for-storage"></a>Directrices de implementación de almacenamiento

Decisiones:

- ¿Debe usar almacenamiento estándar o Premium para su carga de trabajo?
- ¿Necesita discos con bandas para crear los discos de más de 1023 GB?
- ¿Necesita discos con bandas para lograr un rendimiento óptimo i/OS para su carga de trabajo?
- ¿Qué conjunto de cuentas de almacenamiento necesita hospedar su carga de trabajo de TI o infraestructura?

Tareas:

- Revise las solicitudes de i/OS de las aplicaciones que va a implementar y planear el número adecuado y el tipo de cuentas de almacenamiento.
- Crear el conjunto de cuentas de almacenamiento utilizando la convención de nomenclatura. Puede usar PowerShell Azure o el portal.


## <a name="storage"></a>Almacenamiento de información

Almacenamiento de Azure es una parte esencial de implementar y administrar aplicaciones y máquinas virtuales (VM). Almacenamiento de Azure proporciona servicios para almacenar mensajes, los datos no estructurados y datos de archivos, y también es parte de la infraestructura de máquinas virtuales de soporte.

Hay dos tipos de cuentas de almacenamiento de máquinas virtuales de soporte:

- Ofrecer de cuentas de almacenamiento estándar acceso al almacenamiento de blobs (usado para almacenar los discos de Azure VM), almacenamiento de tablas, almacenamiento en la cola y almacenamiento de archivos.
- Cuentas de [almacenamiento Premium](../storage/storage-premium-storage.md) ofrecen soporte de disco de alto rendimiento, baja latencia para i/OS intensivas cargas de trabajo, como servidores de SQL Server en un clúster de AlwaysOn. Almacenamiento de Premium admite actualmente solo los discos de VM de Azure.

Azure crea máquinas virtuales con un disco de sistema operativo, un disco temporal y cero o más discos de datos opcional. El disco de sistema operativo y los discos de datos son blobs de Azure página, mientras que el disco temporal se almacena localmente en el nodo donde reside el equipo. Tenga cuidado al diseñar aplicaciones para usar solo este disco temporal para los datos no persistente como la máquina virtual se puede migrar entre hosts durante un evento de mantenimiento. Se perderán los datos almacenados en el disco temporal.

El entorno de almacenamiento de Azure subyacente para asegurarse de que los datos permanecerán protegidos frente a errores de hardware o mantenimiento planificados proporciona alta disponibilidad y la duración. Al diseñar su entorno de almacenamiento de Azure, puede elegir replicar almacenamiento VM:

- localmente dentro de un determinado centro de datos de Azure
- entre los centros de datos de Azure dentro de una región determinada
- entre los centros de datos de Azure a través de diferentes regiones

Obtenga [más información sobre las opciones de replicación de alta disponibilidad](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Discos de sistema operativo y los datos tienen un tamaño máximo de 1023 gigabytes (GB). El tamaño máximo de un blob es 1.024 GB y que debe contener los metadatos (pie de página) del archivo de disco duro virtual (un GB es 1024<sup>3</sup> bytes). Puede usar espacios de almacenamiento en Windows Server 2012 para sobrepasar este límite al agrupar juntos discos de datos para presentar volúmenes lógicos más de 1023GB a su máquina virtual.

Existen algunos límites de escalabilidad al diseñar las implementaciones de almacenamiento de Azure: consulte [límites de suscripción y el servicio de Microsoft Azure, cuotas y restricciones](azure-subscription-service-limits.md#storage-limits) para obtener más detalles. Vea también [destinos de rendimiento y escalabilidad de almacenamiento de Azure](../storage/storage-scalability-targets.md).

Para el almacenamiento de la aplicación, puede almacenar los datos de objeto no estructurados, como documentos, imágenes, las copias de seguridad, datos de configuración, registros, etcetera. uso de almacenamiento de blobs. En lugar de la aplicación escribir en un disco virtual conectado a la máquina virtual, puede escribir la aplicación directamente al almacenamiento de blobs de Windows Azure. Almacenamiento de blobs también proporciona la opción de [niveles de almacenamiento de acceso rápido y fríos](../storage/storage-blob-storage-tiers.md) según sus necesidades de disponibilidad y las restricciones de costos.


## <a name="striped-disks"></a>Discos con bandas
Además de lo que le permite crear discos de más de 1023 GB, en muchos casos, usando bandas para discos de datos mejora el rendimiento al permitir varios BLOB al fondo el almacenamiento de un único volumen. Con bandas, las i/OS necesarios para escribir y leer datos desde un disco lógico continúa en paralelo.

Azure impone límites en el número de discos de datos y el ancho de banda disponible, dependiendo del tamaño de la máquina virtual. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-windows-sizes.md).

Si está utilizando discos con bandas para discos de datos de Azure, tenga en cuenta lo siguiente:

- Discos de datos siempre deben ser el tamaño máximo (1023 GB).
- Adjuntar los discos de datos máximo permitidos para el tamaño de la máquina virtual.
- Use espacios de almacenamiento.
- Evitar el uso de opciones de caché de datos de Azure disco (directiva de caché = ninguno).

Para obtener más información, vea [espacios de almacenamiento - diseñar rendimiento](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).


## <a name="multiple-storage-accounts"></a>Varias cuentas de almacenamiento

Al diseñar su entorno de almacenamiento de Azure, puede utilizar varias cuentas de almacenamiento como el número de máquinas virtuales implementar aumenta. Esto le permite distribuir a las i/OS a través de la infraestructura de almacenamiento de Azure subyacente para mantener un rendimiento óptimo de las máquinas virtuales y aplicaciones. Diseñar las aplicaciones que está implementando, considere los requisitos de i/OS que cada VM cuenta y saldo de esas máquinas virtuales entre cuentas de almacenamiento de Azure. Intente evitar agrupar todas las alta i/OS requieren máquinas virtuales en uno o dos cuentas de almacenamiento.

Para obtener más información acerca de las capacidades de i/OS de las diferentes opciones de almacenamiento de Azure y algunos recomendamos máximos, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 