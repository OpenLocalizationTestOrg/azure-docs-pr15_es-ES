<properties
    pageTitle="Información general de máquinas virtuales de Windows | Microsoft Azure"
    description="Obtenga información sobre cómo crear y administrar máquinas virtuales de Windows en Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Información general sobre máquinas virtuales de Windows en Azure

Máquinas virtuales de Windows Azure (VM) es uno de los distintos tipos de [a petición, scalable recursos informáticos](../app-service-web/choose-web-site-cloud-service-vm.md) que ofrece Azure. Normalmente, elige una máquina virtual cuando necesita tener más control sobre el entorno de las otras opciones de la oferta. En este artículo se proporciona información sobre lo debe tener en cuenta antes de crear una máquina virtual, cómo crear y cómo se administran.

Una máquina virtual de Azure proporciona la flexibilidad de la virtualización sin necesidad de comprar y mantener el hardware físico que lo ejecuta. Sin embargo, todavía necesita mantener la máquina virtual al realizar tareas, como la configuración, revisiones e instalar el software que se ejecuta en él.

Máquinas virtuales de Windows Azure puede usarse de diversas maneras. Algunos ejemplos son:

- **Desarrollo y pruebas** : Azure VM ofrecen una rápida y fácil a crear un equipo con configuraciones específicas necesarias para código y probar una aplicación.
- **Aplicaciones en la nube** – porque fluctuación de petición de la aplicación, puede ser conveniente económico para ejecutarlo en una máquina virtual en Azure. Pagar para máquinas virtuales adicionales cuando los necesite y salir de ellos cuando no.
- **Centro de datos extendido** – máquinas virtuales de una red virtual Azure se puede conectar fácilmente a la red de su organización.

El número de máquinas virtuales que utiliza la aplicación puede escalar a lo que se requieren para satisfacer sus necesidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>¿Qué debo pensar antes de crear una máquina virtual?

Siempre hay una gran variedad de [Consideraciones de diseño](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) cuando se crea una infraestructura de aplicación en Azure. Estos aspectos de una máquina virtual están importantes pensar antes de empezar:

- Los nombres de los recursos de la aplicación
- La ubicación donde se almacenan los recursos
- El tamaño de la máquina virtual
- El número máximo de máquinas virtuales que se pueden crear
- El sistema operativo que se ejecuta la máquina virtual
- La configuración de la máquina virtual después de que se inicia
- Los recursos relacionados que necesita de la máquina virtual

### <a name="naming"></a>Los nombres

Una máquina virtual tiene un [nombre](virtual-machines-windows-infrastructure-naming-guidelines.md) asignado y el nombre de un equipo configurado como parte del sistema operativo. El nombre de una máquina virtual puede tener un máximo de 15 caracteres.

Si usa Azure para crear el disco de sistema operativo, el nombre del equipo y el nombre de la máquina virtual son los mismos. Si puede [cargar y usar su propia imagen](virtual-machines-windows-upload-image.md) que contiene un sistema operativo configurado previamente y usarlo para crear una máquina virtual, los nombres pueden ser diferentes. Se recomienda que, cuando se carga un archivo de imagen, hacer el nombre del equipo en el sistema operativo y el nombre de la máquina virtual de la misma.

### <a name="locations"></a>Ubicaciones

Todos los recursos creados en Azure se distribuyen entre varias [regiones geográficas](https://azure.microsoft.com/regions/) todo el mundo. Normalmente, la región se denomina **ubicación** cuando se crea una máquina virtual. Para una máquina virtual, la ubicación especifica se almacenan en el disco duro virtual.

Esta tabla muestra algunas de las maneras en que puede obtener una lista de ubicaciones disponibles.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | Seleccione una ubicación de la lista cuando se crea una máquina virtual. |
| Azure PowerShell | Use el comando [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| API DE REST | Utilice la operación de [ubicaciones de la lista](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>Tamaño de memoria virtual

El [tamaño](virtual-machines-windows-sizes.md) de la máquina virtual que use depende de la carga de trabajo que desea ejecutar. El tamaño que, a continuación, elija determina factores como el procesamiento de capacidad de energía, memoria y almacenamiento. Azure ofrece una amplia variedad de tamaños de muchos tipos de usos de soporte técnico.

Azure cargos un [precio por hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) basada en el sistema operativo y el tamaño de la máquina virtual. Horas parcial, Azure cargos solo para los minutos usados. Almacenamiento es el precio y cobrará por separado.

### <a name="vm-limits"></a>Límites VM

Su suscripción ha predeterminados de [los límites de cuota](../azure-subscription-service-limits.md) de que pueden afectar a la implementación de muchas máquinas virtuales de su proyecto. El límite actual por suscripción es 20 máquinas virtuales por región. Límites pueden ser generados por una incidencia de soporte técnico que solicita un aumento de archivos.

### <a name="operating-system-disks-and-images"></a>Imágenes y discos de sistema operativo

Máquinas virtuales utilizan [discos duro virtuales (VHD)](virtual-machines-windows-about-disks-vhds.md) para almacenar sus datos y el sistema operativo (SO). VHD también se usan para las imágenes que se puede elegir entre instalar un sistema operativo. 

Azure proporciona muchas [imágenes del catálogo de soluciones](https://azure.microsoft.com/marketplace/virtual-machines/) para usarlos con varias versiones y tipos de sistemas operativos Windows Server. Imágenes del catálogo de soluciones se identifican mediante publisher de imagen, oferta, sku y versión (normalmente versión especificada más reciente). 

Esta tabla muestra algunas formas en que puede encontrar la información de una imagen.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | Los valores se especifican automáticamente automáticamente cuando se selecciona una imagen para usar. |
| Azure PowerShell | [AzureRMVMImagePublisher Get](https://msdn.microsoft.com/library/mt603484.aspx) -"ubicación"<BR>[AzureRMVMImageOffer Get](https://msdn.microsoft.com/library/mt603824.aspx) -"ubicación"-"publisherName" de Publisher<BR>[AzureRMVMImageSku Get](https://msdn.microsoft.com/library/mt619458.aspx) -"ubicación"-Publisher "publisherName"-ofrecen "offerName" |
| API REST | [Editores de imagen de lista](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Imagen de la lista ofrece](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Lista SKU de imagen](https://msdn.microsoft.com/library/mt743701.aspx) |

Puede elegir [cargar](virtual-machines-windows-upload-image.md) y usar su propia imagen y cuando lo hace, no se utilizan el nombre de publisher, oferta y sku.

### <a name="extensions"></a>Extensiones

VM [extensiones](virtual-machines-windows-extensions-features.md) proporcionar las capacidades adicionales de máquina virtual mediante la configuración de implementación de publicación y tareas automatizadas.

Estas tareas comunes se pueden utilizar las extensiones:

- **Ejecutar scripts personalizados** : la [Extensión de Script personalizado](virtual-machines-windows-extensions-customscript.md) le ayuda a configurar las cargas de trabajo en la máquina virtual ejecutando el script cuando se aprovisiona la máquina virtual.
- **Implementar y administrar las configuraciones** : la [extensión de configuración de estado deseado de PowerShell (DSC)](virtual-machines-windows-extensions-dsc-overview.md) le ayuda a configurar DSC en una máquina virtual para administrar las configuraciones y entornos.
- **Recopilar datos de diagnóstico** : la [Extensión de diagnósticos de Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) le ayuda a configurar la máquina virtual para recopilar datos de diagnóstico que se pueden usar para supervisar el estado de la aplicación.

### <a name="related-resources"></a>Recursos relacionados

Los recursos en esta tabla se usan en la máquina virtual y necesitan existir o crearse cuando se crea la máquina virtual.

| Recursos | Obligatorio | Descripción |
| -------- | -------- | ----------- |
| [Grupo de recursos](../azure-resource-manager/resource-group-overview.md) | Sí | La máquina virtual debe estar contenida en un grupo de recursos. |
| [Cuenta de almacenamiento](../storage/storage-create-storage-account.md) | Sí | La máquina virtual necesita la cuenta de almacenamiento para almacenar su disco duro virtual. |
| [Red virtual](../virtual-network/virtual-networks-overview.md) | Sí | La máquina virtual debe ser un miembro de una red virtual. |
| [Dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | No | La máquina virtual puede tener una dirección IP pública asignada remotamente accediendo a ellos. |
| [Interfaz de red](../virtual-network/virtual-network-network-interface-overview.md) | Sí | La máquina virtual necesita la interfaz de red para comunicarse en la red. |
| [Discos de datos](virtual-machines-windows-attach-disk-portal.md) | No | La máquina virtual puede incluir discos de datos para expandir la capacidad de almacenamiento. |

## <a name="how-do-i-create-my-first-vm"></a>¿Cómo creo Mi primera VM?

Hay varias opciones para la creación de la máquina virtual. La opción que realice depende del entorno en que se encuentra. 

Esta tabla se proporciona información para ayudarle a crear la máquina virtual.

| Método | Artículo |
| ------ | ------- |
| Portal de Azure | [Crear una máquina virtual ejecuta Windows con el portal](virtual-machines-windows-hero-tutorial.md) |
| Plantillas | [Crear una máquina virtual de Windows con una plantilla de administrador de recursos](virtual-machines-windows-ps-template.md) |
| Azure PowerShell | [Crear una máquina virtual de Windows con PowerShell](virtual-machines-windows-ps-create.md) |
| SDK de cliente | [Implementar recursos de Azure mediante C#](virtual-machines-windows-csharp.md) |
| API REST | [Crear o actualizar una máquina virtual](https://msdn.microsoft.com/library/mt163591.aspx) |

Esperamos nunca sucede, pero en ocasiones algo va mal. Si esta situación sucede, mire la información de [los problemas de implementación de administrador de recursos de solución de problemas con la creación de una máquina virtual Windows Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>¿Cómo administrar la máquina virtual que he creado?

Máquinas virtuales pueden administrarse con un portal basado en explorador, herramientas de línea de comandos con el soporte técnico para secuencias de comandos o directamente a través de la API. Algunas tareas de administración habituales que puede realizar obtención información acerca de una máquina virtual, inicio de sesión en una máquina virtual, administración de la disponibilidad y realizar copias de seguridad.

### <a name="get-information-about-a-vm"></a>Obtener información sobre una máquina virtual

Esta tabla muestra algunas de las maneras en que puede obtener información sobre una máquina virtual.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | En el menú de concentrador, haga clic en **máquinas virtuales de Windows** y, a continuación, seleccione la máquina virtual de la lista. En el módulo de la máquina virtual, tendrá acceso a información general, valores y supervisar métricas. |
| Azure PowerShell | Para obtener información sobre cómo usar PowerShell para administrar máquinas virtuales, vea [administrar máquinas virtuales de Azure con el Administrador de recursos y PowerShell](virtual-machines-windows-ps-manage.md). |
| API DE REST | Para obtener información sobre una máquina virtual, use la operación de [VM obtener información](https://msdn.microsoft.com/library/mt163682.aspx) . |
| SDK de cliente | Para obtener información sobre cómo usar C# para administrar máquinas virtuales, vea [administrar máquinas virtuales de Azure con el Administrador de recursos de Azure y C#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Inicie sesión en la máquina virtual

Utilice el botón conectar en el portal de Azure para [iniciar una sesión de escritorio remoto (RDP)](virtual-machines-windows-connect-logon.md). Cosas a veces pueden fallar cuando intenta usar una conexión remota. Si se produce esta situación para usted, consulte la información de Ayuda de [conexiones de solución de problemas de escritorio remoto a una máquina virtual Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Administrar la disponibilidad

Es importante comprender cómo [garantizar la alta disponibilidad](virtual-machines-windows-manage-availability.md) de la aplicación. Esta configuración implica la creación de varias máquinas virtuales para asegurarse de que se está ejecutando al menos uno.

Para su implementación optar a nuestro contrato de nivel de servicio de VM 99.95, debe implementar máquinas virtuales de dos o más con la carga de trabajo dentro de un [conjunto de disponibilidad](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Esta configuración garantiza que sus máquinas virtuales se distribución entre varios dominios de error y se implementan en hosts con ventanas de mantenimiento diferente. Completa [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica la disponibilidad de garantía de Azure como un todo.
 
### <a name="back-up-the-vm"></a>Realizar copias de seguridad de la máquina virtual
 
Un [depósito de servicios de recuperación](../backup/backup-introduction-to-azure-backup.md) se usa para proteger los datos y activos en servicios de copia de seguridad de Azure y Azure sitio de recuperación. Puede usar un depósito de servicios de recuperación a [implementar y administrar las copias de seguridad para máquinas virtuales de implementada por el Administrador de recursos con PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Pasos siguientes

- Si su intención es trabajar con máquinas virtuales de Linux, mire [Azure y Linux](virtual-machines-linux-azure-overview.md).
- Más información sobre las directrices de la configuración de la infraestructura en el [Tutorial de infraestructura de Azure de ejemplo](virtual-machines-windows-infrastructure-example.md).
- Asegúrese de que seguir las [Prácticas recomendadas para ejecutar una máquina virtual de Windows en Azure](virtual-machines-windows-guidance-compute-single-vm.md).


