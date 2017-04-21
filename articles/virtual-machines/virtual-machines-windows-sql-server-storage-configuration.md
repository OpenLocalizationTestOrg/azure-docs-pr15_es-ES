<properties
    pageTitle="Configuración de almacenamiento para máquinas virtuales de SQL Server | Microsoft Azure"
    description="Este tema describe cómo Azure configura almacenamiento para máquinas virtuales de SQL Server durante el aprovisionamiento (modelo de implementación de administrador de recursos). También se explica cómo configurar el almacenamiento para su las máquinas virtuales de SQL Server existente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Configuración de almacenamiento para máquinas virtuales de SQL Server

Al configurar una imagen de la máquina virtual de SQL Server en Azure, el Portal de ayuda a automatizar la configuración de almacenamiento. Esto incluye adjuntar almacenamiento VM, permitiendo acceder a SQL Server que almacenamiento y configurarlo para optimizar los requisitos de rendimiento específicos.

En este tema se explica cómo Azure configura almacenamiento para su las máquinas virtuales de SQL Server durante el aprovisionamiento y para máquinas virtuales existentes. Esta configuración se basa en las [mejores prácticas de performance](virtual-machines-windows-sql-performance.md) para máquinas virtuales de Azure que ejecuta SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

## <a name="prerequisites"></a>Requisitos previos
Para usar las opciones de configuración de almacenamiento automatizado, su máquina virtual requiere las siguientes características:

- Contar con una [imagen de la Galería de SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Utiliza el [modelo de implementación de administrador de recursos](../resource-manager-deployment-model.md).
- Usa [almacenamiento Premium](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Nuevas máquinas virtuales
Las secciones siguientes describen cómo configurar almacenamiento de nuevas máquinas virtuales de SQL Server.

### <a name="azure-portal"></a>Portal de Azure
Al hacer el aprovisionamiento de una máquina virtual de Azure con una imagen de la Galería de SQL Server, puede configurar automáticamente el almacenamiento de su nueva máquina virtual. Especifique el tamaño de almacenamiento, los límites de rendimiento y tipo de carga de trabajo. La siguiente captura de pantalla muestra el módulo de configuración de almacenamiento usado durante la VM SQL aprovisionamiento.

![Configuración de almacenamiento SQL Server VM durante el aprovisionamiento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

En función de las opciones, Azure realiza las siguientes tareas de configuración de almacenamiento después de crear la máquina virtual:

- Crea y adjunta discos de datos de almacenamiento de premium en la máquina virtual.
- Configura los discos de datos para tener acceso a SQL Server.
- Configura los discos de datos en un grupo de almacenamiento basado en el tamaño y rendimiento (IOPS y rendimiento) requisitos especificados.
- Asocia el bloque de almacenamiento a una unidad de nueva en la máquina virtual.
- Optimiza esta nueva unidad basada en el tipo de carga de trabajo especificado (almacenamiento de datos, transaccional procesamiento o General).

Para obtener más información sobre cómo Azure configura almacenamiento, consulte la [sección de configuración de almacenamiento](#storage-configuration). Para obtener un tutorial completo de cómo crear una VM de SQL Server en el Portal de Azure, consulte [el tutorial de aprovisionamiento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Administrar plantillas de recursos
Si usa las siguientes plantillas de administrador de recursos, dos discos de premium datos adjuntos de forma predeterminada, sin ninguna configuración de grupo de almacenamiento. Sin embargo, puede personalizar estas plantillas para cambiar el número de premium discos de datos que se han adjuntado a la máquina virtual.

- [Crear VM con copia de seguridad automática](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Crear VM con revisiones automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Crear VM con integración de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Máquinas virtuales existentes
Para SQL Server las máquinas virtuales existentes, puede modificar algunas opciones de almacenamiento en el portal de Azure. Seleccione la máquina virtual, vaya al área de configuración y, a continuación, seleccione Configuración de SQL Server. El módulo de configuración de SQL Server, muestra el uso actual del almacenamiento de la máquina virtual. En este gráfico, se muestran todas las unidades que existen en la máquina virtual. Para cada unidad, el espacio de almacenamiento de información se muestra en cuatro secciones:

- Datos SQL
- Registro de SQL
- Sí (almacenamiento no sean de SQL)
- Disponible

![Configurar el almacenamiento de SQL Server VM existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Para configurar el almacenamiento para agregar una nueva unidad o extender una unidad existente, haga clic en el vínculo Editar encima del gráfico.

Las opciones de configuración que se observan varía dependiendo de si han utilizado esta característica antes. Al utilizar por primera vez, puede especificar los requisitos de almacenamiento para una nueva unidad. Si ha usado anteriormente esta característica para crear una unidad, puede elegir ampliar el almacenamiento de la unidad.

### <a name="use-for-the-first-time"></a>Usar por primera vez
Si es la primera vez que usa esta característica, puede especificar los límites de tamaño y rendimiento de almacenamiento para una nueva unidad. Este proceso es similar a lo que ve en tiempo de aprovisionamiento. La diferencia principal es que no tiene permiso para especificar el tipo de carga de trabajo. Esta restricción evita interrumpir cualquier configuración de SQL Server existente en la máquina virtual.

![Configurar los controles deslizantes de almacenamiento SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure crea una unidad nueva en función de sus especificaciones. En este escenario, Azure realiza las siguientes tareas de configuración de almacenamiento:

- Crea y adjunta discos de datos de almacenamiento de premium en la máquina virtual.
- Configura los discos de datos para tener acceso a SQL Server.
- Configura los discos de datos en un grupo de almacenamiento basado en el tamaño y rendimiento (IOPS y rendimiento) requisitos especificados.
- Asocia el bloque de almacenamiento a una unidad de nueva en la máquina virtual.

Para obtener más información sobre cómo Azure configura almacenamiento, consulte la [sección de configuración de almacenamiento](#storage-configuration).

### <a name="add-a-new-drive"></a>Agregar una nueva unidad
Si ya ha configurado almacenamiento en la VM de SQL Server, expandir almacenamiento muestra dos opciones nuevas. La primera opción consiste en Agregar una nueva unidad, lo que puede aumentar el nivel de rendimiento de la máquina virtual.

![Agregar una unidad nueva a una VM de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Sin embargo, después de agregar la unidad, debe realizar una configuración manual adicional para conseguir el aumento del rendimiento.

### <a name="extend-the-drive"></a>Ampliar la unidad
La otra opción de almacenamiento de expansión es ampliar la unidad existente. Esta opción aumenta el almacenamiento disponible para la unidad de disco, pero no mejora el rendimiento. Con los grupos de almacenamiento, no se puede modificar el número de columnas después de crear el grupo de almacenamiento. El número de columnas determina el número de escritura en paralelo, puede seccionados en los discos de datos. Por lo tanto, los discos de datos agregados no pueden aumentar el rendimiento. Solo pueden proporcionar más espacio de almacenamiento para los datos que se escriben. Esta limitación también significa que, cuando se amplía la unidad, el número de columnas determina el número mínimo de discos de datos que puede agregar. Si crea una agrupación de almacenamiento con discos de cuatro datos, el número de columnas es también cuatro. Cualquier momento que ampliar el almacenamiento, debe agregar discos de datos por lo menos cuatro.

![Extender una unidad para una VM de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuración de almacenamiento
Esta sección proporciona una referencia para los cambios de configuración de almacenamiento Azure se realiza automáticamente durante la configuración en el Portal de Azure o VM SQL aprovisionamiento.

- Si ha seleccionado menos de dos TB de almacenamiento para la máquina virtual, Azure no crear un grupo de almacenamiento.
- Si ha seleccionado al menos dos TB de almacenamiento para la máquina virtual, Azure configura un grupo de almacenamiento. La siguiente sección de este tema proporciona los detalles de la configuración del grupo de almacenamiento.
- Configuración del almacenamiento automático siempre usa [almacenamiento premium](../storage/storage-premium-storage.md) P30 discos de datos. Por lo tanto, no hay una asignación 1:1 entre el número de Terabytes seleccionados y el número de discos de datos conectados a su máquina virtual.

Para obtener información sobre precios, consulte la página de [precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage) en la pestaña de **Almacenamiento en disco** .

### <a name="creation-of-the-storage-pool"></a>Creación de la agrupación de almacenamiento
Azure usa la siguiente configuración para crear el grupo de almacenamiento en máquinas virtuales de SQL Server.

| Configuración | Valor |
|-----|-----|
| Tamaño de las bandas  | 256 KB (almacenamiento de datos); 64 KB (transacciones) |
| Tamaños de disco | 1 TB |
| Caché | Lectura |
| Tamaño de la asignación | Tamaño de la unidad de asignación de 64 KB NTFS |
| Inicialización instantánea de archivos | Habilitado |
| Bloquear páginas en memoria | Habilitado |
| Recuperación | Recuperación simple (sin resistencia) |
| Número de columnas | Número de discos de datos<sup>1</sup> |
| Ubicación de TempDB | Almacenados en discos de datos<sup>2</sup> |

<sup>1</sup> después de crea el grupo de almacenamiento, no se puede modificar el número de columnas en el grupo de almacenamiento.

<sup>2</sup> esta configuración solo se aplica a la primera unidad que crear con la característica de configuración de almacenamiento.

## <a name="workload-optimization-settings"></a>Configuración de optimización de carga de trabajo
La siguiente tabla describe las opciones de tipo de carga de trabajo de tres disponibles y sus correspondientes optimizaciones:

| Tipo de carga de trabajo | Descripción | Optimizaciones |
|-----|-----|-----|
| **General** | Configuración predeterminada que es compatible con la mayoría de las cargas de trabajo | Ninguno |
| **Procesamiento de transacciones** | Optimiza el almacenamiento de cargas de trabajo OLTP tradicional de la base de datos | Indicador de traza 1117<br/>Marca de traza 1118 |
| **Almacenamiento de datos** | Optimiza el almacenamiento de las cargas de trabajo de análisis y generación de informes | Indicador de traza 610<br/>Indicador de traza 1117 |

>[AZURE.NOTE] Solo puede especificar el tipo de carga de trabajo cuando se aprovisiona una máquina virtual SQL seleccionando en el paso de la configuración de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
Para otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).
