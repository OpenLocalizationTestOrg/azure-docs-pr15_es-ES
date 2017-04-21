<properties
    pageTitle="Crear clústeres Hadoop, HBase, tormenta o motor en Linux en HDInsight | Microsoft Azure"
    description="Aprenda a crear Hadoop, HBase, tormenta o despierten clústeres en Linux para HDInsight con un explorador, la CLI de Azure, Azure PowerShell, resto, o a través de un SDK."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>


# <a name="create-linux-based-hadoop-clusters-in-hdinsight"></a>Crear clústeres basados en Linux Hadoop en HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Un clúster de Hadoop consta de varios equipos virtuales (nodos) que se usan para el procesamiento de tareas en el clúster distribuido. Azure resume los detalles de implementación de instalación y configuración de los nodos individuales, por lo que debe proporcionar información de configuración general. En este artículo, obtenga información sobre estas opciones de configuración.

## <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="cluster-types"></a>Tipos de clúster

Actualmente, HDInsight de Azure proporciona cinco tipos diferentes de clústeres, cada una con un conjunto de componentes para proporcionar determinadas funcionalidades.

| Tipo de clúster | Funcionalidad |
| ------------ | ----------------------------- |
| Hadoop       | Consulta y análisis (procesos)     |
| HBase        | Almacenamiento de datos NoSQL            |
| Tormenta        | Procesamiento de eventos en tiempo real |
| Motor        | Procesamiento en memoria, consultas interactivas, procesamiento de secuencia del lote de micro |
| [Sección interactivo (vista preliminar)](hdinsight-hadoop-use-interactive-hive.md) | En la memoria caché para las consultas de subárbol interactivas y más rápidas|
| Servidor R motor (vista preliminar) | Una gran variedad de estadísticas de datos grande, el modelado de predicción y aprendizaje capacidades |

Cada tipo de clúster tiene su propio número de nodos en el clúster, terminología de nodos en el clúster y tamaño predeterminado de la máquina virtual para cada tipo de nodo. En la siguiente tabla, el número de nodos de cada tipo de nodo es entre paréntesis.

| Tipo| Nodos | Diagrama|
|-----|------|--------|
|Hadoop| Cabeza nodo (2), datos (1 +)|![Nodos de clúster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|Cabeza servidor (2), región (1 +), el nodo de patrón/Zookeeper (3)|![Nodos de clúster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Tormenta|Nimbus nodo (2), el servidor de Supervisor (1 +), Zookeeper (3)|![Nodos de clúster de tormenta HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Motor|Cabeza nodo (2), el nodo de trabajo (1 +), Zookeeper (3) (libre para el tamaño de la máquina virtual de Zookeepers A1)|![Nodos de clúster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|


En la tabla siguiente muestra los tamaños de máquina virtual de forma predeterminada para HDInsight:

- Todas las áreas excepto Brasil sur y Oeste Japón:

  	|Tipo de clúster                     | Hadoop               | HBase                | Tormenta                | Motor                                                                 | Servidor de R |
  	|---------------------------------|----------------------|----------------------|----------------------|-----------------------------------------------------------------------|-----------------------------------------------------------------------|
  	|Predeterminado de cabeza, tamaño de memoria virtual           | D3 v2                | D3 v2                | A3                   | D12 v2                                                                | D12 v2                                                                |
  	|Cabeza: VM tamaños recomendados      | D3 v2, v2 D4, D12 v2 | D3 v2, v2 D4, D12 v2 | A3, A4 Y A5           | V2 D12, v2 D13, D14 v2                                                | V2 D12, v2 D13, D14 v2                                                |
  	|Trabajo: predeterminado tamaño VM         | D3 v2                | D3 v2                | D3 v2                | Windows: D12 v2; Linux: D4 v2                                         | Windows: D12 v2; Linux: D4 v2                                         |
  	|Trabajo: VM tamaños recomendados    | D3 v2, v2 D4, D12 v2 | D3 v2, v2 D4, D12 v2 | D3 v2, v2 D4, D12 v2 | Windows: D12 v2 D13 v2, D14 v2; Linux: D4 v2, v2 D12, v2 D13, D14 v2 | Windows: D12 v2 D13 v2, D14 v2; Linux: D4 v2, v2 D12, v2 D13, D14 v2 |
  	|Zookeeper – predeterminado tamaño VM      |                      | A3                   | A2                   |                                                                       |
  	|Zookeeper – VM tamaños recomendados |                      | A3, A4 Y A5           | A2, A3, A4           |                                                                       |
  	|Borde - Default tamaño VM           |                      |                      |                      |                                                                       | Windows: D12 v2; Linux: D4 v2                                         |
  	|Borde - VM tamaño recomendado       |                      |                      |                      |                                                                       | Windows: D12 v2 D13 v2, D14 v2; Linux: D4 v2, v2 D12, v2 D13, D14 v2 |

- Brasil sur y oeste de Japón sólo (sin v2 tamaños aquí):

  	|Tipo de clúster                     | Hadoop      | HBase       | Tormenta      | Motor                                          |Servidor de R|
  	|---------------------------------|-------------|-------------|------------|------------------------------------------------|--------|
  	|Predeterminado de cabeza, tamaño de memoria virtual           | D3          | D3          | A3         | D12                                            | D12|
  	|Cabeza: VM tamaños recomendados      | D3, D4, D12 | D3, D4, D12 | A3, A4 Y A5 | D14 D12, D13,                                  | D14 D12, D13,|
  	|Trabajo: predeterminado tamaño VM         | D3          | D3          | D3         | Windows: D12; Linux: D4                        | Windows: D12; Linux: D4|
  	|Trabajo: VM tamaños recomendados    | D3, D4, D12 | D3, D4, D12 | D3, D4, D12| Windows: D12, D13, D14; Linux: D4, D12, D13, D14| Windows: D12, D13, D14; Linux: D4, D12, D13, D14|
  	|Zookeeper – predeterminado tamaño VM      |             | A2          | A2         |                                                | |
  	|Zookeeper – VM tamaños recomendados |             | A2, A3, A4  | A2, A3, A4 |                                                | |
  	|Borde: predeterminado tamaños VM          |             |             |            |                                                | Windows: D12; Linux: D4 |
  	|Borde, tamaños VM recomendados      |             |             |            |                                                | Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

Tenga en cuenta que cabeza se conoce como *Nimbus* para el tipo de clúster de tormenta. Trabajador se conoce como *región* para el tipo de clúster HBase y como el tipo de clúster de tormenta *Supervisor* .

> [AZURE.IMPORTANT] Si desea tener más de 32 nodos de trabajo, en la creación de un clúster o ajustando el clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.

Puede agregar otros componentes como matiz o R a estos tipos básicos mediante [Acciones de Script](#customize-clusters-using-script-action).

> [AZURE.IMPORTANT] HDInsight formar una gran variedad de tipos, que corresponden a la carga de trabajo o la tecnología de clúster está optimizado para clústeres. No hay ningún método para crear un clúster que combina varios tipos, como tormenta y HBase en un clúster. 

Si su solución requiere tecnologías que se extienden en varios tipos de clúster HDInsight, debe crear una red Virtual de Azure y crear los tipos de clúster requiere dentro de la red virtual. Esto permite que los clústeres y el código que se implementa en ellos para comunicarse directamente entre sí.

Para obtener más información sobre el uso de una red Virtual de Azure con HDInsight, consulte [Ampliar HDInsight con redes virtuales de Azure](hdinsight-extend-hadoop-virtual-network.md).

Para obtener un ejemplo del uso de dos tipos de clúster dentro de una red Virtual de Azure, vea [analizar datos de sensor con tormenta y HBase](hdinsight-storm-sensor-data-analysis.md).


## <a name="cluster-tiers"></a>Niveles de clúster

HDInsight de Azure proporciona las ofertas de nube de datos grande en dos categorías: estándar y [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). HDInsight Premium incluye R y otros componentes adicionales. HDInsight Premium solo es compatible con HDInsight versión 3.4.

La tabla siguiente muestra el tipo de clúster de HDInsight y la matriz de soporte de HDInsight Premium.

| Tipo de clúster | Estándar | Premium  |
|--------------|---------------|--------------|
| Hadoop       | Sí           | Sí          |
| Motor        | Sí           | Sí          |
| HBase        | Sí           | No           |
| Tormenta        | Sí           | No           |
| Servidor R motor | No | Sí |

Esta tabla se actualizarán como más tipos de clúster se incluyen en HDInsight Premium. La siguiente captura de pantalla muestra la información del portal Azure para elegir los tipos de clúster.

![Configuración de HDInsight premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## <a name="basic-configuration-options"></a>Opciones de configuración básica

Los siguientes son las opciones de configuración básica utilizadas para crear un clúster de HDInsight.

### <a name="cluster-name"></a>Nombre de clúster ###

Nombre de clúster se usa para identificar un clúster. Nombre de clúster debe ser único global y deben cumplir las siguientes directrices de nomenclatura:

- El campo debe ser una cadena que contiene entre 3 y 63 caracteres.
- El campo puede contener sólo letras, números y guiones.

### <a name="cluster-type"></a>Tipo de clúster###

Vea [tipos de clúster](#cluster-types) y [niveles de clúster](#cluster-tiers).

### <a name="operating-system"></a>Sistema operativo ###

Puede crear clústeres HDInsight en uno de los siguientes sistemas operativos dos:

- HDInsight en Linux.  HDInsight proporciona la opción de configuración de clústeres de Linux en Azure. Configurar un clúster de Linux si está familiarizado con Linux o Unix, migrar desde una solución basada en Linux Hadoop existente, o desea facilita la integración con componentes de ecosistema Hadoop generados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
- HDInsight de Windows (centro de datos de Windows Server 2012 R2).

### <a name="hdinsight-version"></a>Versión HDInsight###

Esto se utiliza para determinar la versión de HDInsight necesaria para este clúster. Para obtener más información, vea [versiones de clúster Hadoop y componentes en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

### <a name="subscription-name"></a>Nombre de suscripción###

Cada clúster HDInsight está vinculado a una suscripción de Azure.

### <a name="resource-group-name"></a>Nombre del grupo de recursos ###

Ayuda de [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) a trabajar con los recursos de la aplicación como un grupo, se conoce como un grupo de recursos de Azure. Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una única operación coordinada.

### <a name="credentials"></a>Credenciales###

Con HDInsight clústeres, puede configurar dos cuentas de usuario durante la creación de clúster:

- Usuarios de HTTP. El nombre de usuario predeterminado es *admin* con la configuración básica en el portal de Azure. A veces se denomina "Clúster de usuario".
- Usuario SSH (Linux clústeres). Esto se usa para conectarse al clúster mediante SSH. Puede crear cuentas de usuario adicionales SSH después de crear el clúster siguiendo los pasos de [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-unix.md).

    >[AZURE.NOTE] Para clústeres basados en Windows, puede crear un usuario RDP para conectarse al clúster mediante RDP.

### <a name="data-source"></a>Origen de datos###

El sistema de archivos distribuido Hadoop (HDFS) original usa muchos discos locales en el clúster. HDInsight usa almacenamiento de blobs de Windows Azure almacenamiento de datos. Almacenamiento de blobs de Windows Azure es una solución de almacenamiento sólido y general que se integra con HDInsight. Mediante una interfaz HDFS, el conjunto completo de componentes de HDInsight puede trabajar directamente con los datos estructurados o en el almacenamiento de blobs de Windows. Almacenar los datos en el almacenamiento de blobs le ayuda a eliminar de forma segura los clústeres HDInsight que se usan para el cálculo sin perder los datos de usuario.

Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Windows Azure en la cuenta de almacenamiento de Azure. Algunos procesos de creación requieren la cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs crearse. El contenedor de almacenamiento de blobs de Windows se usa como la ubicación de almacenamiento predeterminada por el clúster. Si lo desea, puede especificar cuentas adicionales de almacenamiento de Azure (almacenamiento vinculado) que sean accesibles por el clúster. El clúster también puede acceder a los contenedores de almacenamiento de blobs que estén configurados con acceso de lectura completa público o público acceso de lectura para blobs solo.  Para obtener más información, vea [Administrar el acceso a los recursos de almacenamiento de Azure](../storage/storage-manage-access-to-resources.md).

![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

>[AZURE.NOTE] Un contenedor de almacenamiento de blobs de Windows proporciona una agrupación de un conjunto de BLOB tal como se muestra en la siguiente imagen.

![Almacenamiento de blobs de Windows Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

No se recomienda usar el contenedor de almacenamiento de blobs de forma predeterminada para almacenar datos empresariales. Eliminar el contenedor de almacenamiento de blobs de forma predeterminada después de cada uso para reducir los costos de almacenamiento es una buena práctica. Observe que el contenedor predeterminado contiene la aplicación y el sistema de registros. Asegúrese de recuperar los registros antes de eliminar el contenedor.

>[AZURE.WARNING] No se admite el uso compartido de un contenedor de almacenamiento de blobs para varios clústeres.

Para obtener más información sobre el uso de almacenamiento de blobs de secundaria, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Además del almacenamiento de blobs de Windows Azure, también puede usar [Lago almacén de datos de Azure](../data-lake-store/data-lake-store-overview.md) como una cuenta de almacenamiento predeterminada para clúster de HBase en HDInsight, como almacenamiento vinculado para todos los tipos de clúster HDInsight cuatro. Para obtener más información, consulte [crear un clúster de HDInsight con el almacén de datos de lago con Azure portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="location-region"></a>Ubicación (región) ###

El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben encontrarse en la misma ubicación de Azure.

![Áreas de Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Para obtener una lista de regiones compatibles, haga clic en la lista desplegable de **región** en [HDInsight de precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### <a name="node-pricing-tiers"></a>Niveles de precios de nodo###

Los clientes de facturación para el uso de los nodos de la duración del ciclo de vida del clúster. Se inicia cuando se crea un clúster de facturación y se detiene cuando se elimina el clúster. Clústeres no se pueden eliminar ni poner en espera.

Tipos de clúster diferentes tienen diferentes tipos de nodo, números de nodos y tamaños de nodo. Por ejemplo, un tipo de clúster Hadoop tiene dos _nodos cabezales_ y un valor predeterminado de cuatro _nodos de datos_, aunque no tiene un tipo de clúster de tormenta dos _nodos nimbus_, tres _nodos de zookeeper_y un valor predeterminado de cuatro _nodos de supervisor_. El costo de HDInsight clústeres se determina por el número de nodos y los tamaños de máquinas virtuales de los nodos. Por ejemplo, si sabe que va a realizar operaciones que necesitan una gran cantidad de memoria, puede que desee para seleccionar un recurso de cálculo con más memoria. Con fines de aprendizaje, se recomienda utilizar un nodo de datos. Para obtener más información sobre precios de HDInsight, consulte [HDInsight de precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

>[AZURE.NOTE] El límite de tamaño de clúster varía entre suscripciones de Azure. Póngase en contacto con el soporte de facturación para aumentar el límite.

>Los nodos de clúster no se cuentan como máquinas virtuales porque las imágenes de máquina virtual utilizadas para los nodos son un detalle de implementación del servicio HDInsight. Las muestras de cálculo usadas por los nodos consumen el número total de núcleos de cálculo disponibles para su suscripción. Puede ver el número de núcleos disponibles y las muestras que se usará el clúster en la sección de resumen del módulo de niveles de precios de nodo al crear un clúster de HDInsight.

Al usar el portal de Azure para configurar el clúster, el tamaño de nodo está disponible mediante el módulo de __Nivel de precios de nodo__ . También puede ver el costo asociado con los tamaños de nodo diferente. La siguiente captura de pantalla muestra las opciones para un clúster de Hadoop base Linux.

![Tamaños de nodo HDInsight VM](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

Las siguientes tablas muestran los tamaños admitidos por clústeres HDInsight y las capacidades que proporcionan.

#### <a name="standard-tier-a-series"></a>Nivel estándar: serie####

En el modelo de implementación clásica, algunos tamaños de VM son ligeramente distintos de PowerShell y CLI.
* Standard_A3 es grande
* Standard_A4 es ExtraLarge

|Tamaño |Núcleos de CPU|Memoria|NIC (Max)|Max. tamaño del disco|Max. discos de datos (1023 GB cada uno)|Max. IOPS (500 por disco)|
|---|---|---|---|---|---|---|
|Standard_A3\Large|4|7 GB|2|Temporal = 285 GB |8|8 x 500|
|Standard_A4\ExtraLarge|8|14 GB|4|Temporal = 605 GB |16|16 x 500|
|Standard_A6|4|28 GB|2|Temporal = 285 GB |8|8 x 500|
|Standard_A7|8|56 GB|4|Temporal = 605 GB |16|16 x 500|


#### <a name="standard-tier-d-series"></a>Nivel estándar: serie D####

|Tamaño |Núcleos de CPU|Memoria|NIC (Max)|Max. tamaño del disco|Max. discos de datos (1023 GB cada uno)|Max. IOPS (500 por disco)|
|---|---|---|---|---|---|---|
|Standard_D3 |4|14 GB|4|Temporal (SSD) = 200 GB |8|8 x 500|
|Standard_D4 |8|28 GB|8|Temporal (SSD) = 400 GB |16|16 x 500|
|Standard_D12 |4|28 GB|4|Temporal (SSD) = 200 GB |8|8 x 500|
|Standard_D13 |8|56 GB|8|Temporal (SSD) = 400 GB |16|16 x 500|
|Standard_D14 |16|112 GB|8|Temporal (SSD) = 800 GB |32|32 x 500|

#### <a name="standard-tier-dv2-series"></a>Nivel estándar: Dv2 serie####

|Tamaño |Núcleos de CPU|Memoria|NIC (Max)|Max. tamaño del disco|Max. discos de datos (1023 GB cada uno)|Max. IOPS (500 por disco)|
|---|---|---|---|---|---|---|
|Standard_D3_v2 |4|14 GB|4|Temporal (SSD) = 200 GB |8|8 x 500|
|Standard_D4_v2 |8|28 GB|8|Temporal (SSD) = 400 GB |16|16 x 500|
|Standard_D12_v2 |4|28 GB|4|Temporal (SSD) = 200 GB |8|8 x 500|
|Standard_D13_v2 |8|56 GB|8|Temporal (SSD) = 400 GB |16|16 x 500|
|Standard_D14_v2 |16|112 GB|8|Temporal (SSD) = 800 GB |32|32 x 500|    

Por motivos de implementación que debería considerar cuando está pensando en usar estos recursos, vea [tamaños para máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md). Para obtener información acerca de precios de varios tamaños, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight).   

> [AZURE.IMPORTANT] Si desea tener más de 32 nodos de trabajo, en la creación de un clúster o ajustando el clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.

Se inicia cuando se crea un clúster de facturación y se detiene cuando se elimina el clúster. Para obtener más información sobre precios, vea [los detalles de precios HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="use-additional-storage"></a>Usar el almacenamiento adicional

En algunos casos, puede agregar almacenamiento adicional al clúster. Por ejemplo, es posible que tenga varias cuentas de Azure almacenamiento de diferentes regiones geográficas o servicios diferentes, pero desea analizarlos todos con HDInsight.

Puede agregar cuentas de almacenamiento cuando se crea un clúster de HDInsight o después de haber creado un clúster.  Consulte [clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md).

Para obtener más información sobre el almacenamiento de blobs secundario, vea [almacenamiento de blobs de Azure usando con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener más información sobre el almacenamiento de lago secundaria de datos, vea [crear HDInsight clústeres con datos lago almacén con Azure portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


## <a name="use-hiveoozie-metastore"></a>Usar subárbol/Oozie metastore

Le recomendamos encarecidamente que use un metastore personalizado si desea conservar las tablas de la sección después de eliminar el clúster HDInsight. Podrá adjuntar esa metastore a otro clúster de HDInsight.

> [AZURE.IMPORTANT] Metastore HDInsight creó para una versión de clúster HDInsight, no pueden compartir diferentes versiones de clúster HDInsight. Para obtener una lista de versiones de HDInsight, consulte [HDInsight compatibles con versiones](hdinsight-component-versioning.md#supported-hdinsight-versions).

El metastore contiene los metadatos de la sección y Oozie, como tablas de sección, particiones, esquemas y columnas. La metastore le ayuda a conservar los metadatos de una sección y Oozie, por lo que no es necesario volver a crear tablas de la sección o trabajos Oozie cuando se crea un nuevo clúster. De forma predeterminada, subárbol utiliza una base de datos de SQL Azure incrustado para almacenar esta información. La base de datos incrustado no conserva los metadatos cuando se elimina el clúster. Al crear tabla subárbol en un clúster de HDInsight con un metastore subárbol configurado, esas tablas se conservarán cuando vuelva a crear el clúster con el mismo metastore de sección.

Configuración de Metastore no está disponible para los tipos de clúster HBase.

> [AZURE.IMPORTANT] Al crear un metastore personalizado, no use un nombre de base de datos que contiene guiones o guiones. Esto puede provocar que el proceso de creación de clúster no se realice correctamente.

## <a name="use-azure-virtual-networks"></a>Usar redes virtuales Azure

Con una [red virtual Azure](https://azure.microsoft.com/documentation/services/virtual-network/), puede crear una red segura persistente que contiene los recursos que necesita para la solución. Con una red virtual, puede:

* Conectarse a recursos de la nube juntos en una red privada (solo de nube).

    ![diagrama de configuración solo nube](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conectar los recursos de la nube a su red local del centro de datos (para sitios o sitio de punto) mediante una red privada virtual (VPN).

| Configuración de sitio a otro | Configuración del sitio de punto |
| -------------------------- | --------------------------- |
| Con la configuración del sitio para el sitio, puede conectar varios recursos desde el centro de datos a la red virtual Azure mediante una VPN de hardware o el enrutamiento y el servicio de acceso remoto.<br />![diagrama de configuración de sitio a otro](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | Con la configuración del sitio de punto, puede conectar un recurso específico a la red virtual Azure mediante un software VPN.<br />![diagrama de la configuración del sitio de punto](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Clústeres basados en Windows requieren v1 red virtual (clásico), mientras que clústeres basados en Linux requieren una red virtual de v2 (Azure jefe de recursos). Si no tiene el tipo correcto de red, no podrán utilizarse al crear el clúster.

Para obtener más información sobre cómo usar HDInsight con una red virtual, incluidos los requisitos de configuración específica de la red virtual, vea [capacidades de extender HDInsight mediante una red virtual Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="customize-clusters-using-hdinsight-cluster-customization-bootstrap"></a>Personalizar clústeres mediante la personalización de clúster HDInsight (inicio)

A veces, desea configurar los siguientes archivos de configuración:

- clusterIdentity.xml
- site.xml principales
- Gateway.Xml
- HBASE env.xml
- site.xml HBASE
- HDFS site.xml
- sección env.xml
- sección site.xml
- sitio de mapred
- site.xml oozie
- oozie env.xml
- site.xml tormenta
- site.xml tez
- site.xml webhcat
- site.xml hilo

Para mantener los cambios a través de la duración de un clúster, puede usar HDInsight personalización de clúster durante el proceso de creación, o bien, puede usar Ambari en clústeres basados en Linux. Para obtener más información, consulte [clústeres de personalizar HDInsight con inicio](hdinsight-hadoop-customize-cluster-bootstrap.md).

>[AZURE.NOTE] Los clústeres basados en Windows no pueden conservar los cambios debido a crear una nueva imagen. Para obtener más información, vea [Función instancia reinicia debido a actualizaciones de sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).  Para mantener los cambios a través de la duración de los clústeres, debe utilizar HDInsight de personalización de clúster durante el proceso de creación.

## <a name="customize-clusters-using-script-action"></a>Personalizar clústeres con la acción de secuencia de comandos

Puede instalar componentes adicionales o personalizar la configuración de clúster mediante secuencias de comandos durante la creación. Estas secuencias de comandos se invocan mediante la **Acción de secuencia de comandos**, que es una opción de configuración que puede usarse desde el portal de Azure, cmdlets de Windows PowerShell de HDInsight o el SDK de .NET HDInsight. Para obtener más información, consulte [clúster de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md).

Algunos componentes de Java nativas, como Mahout y en cascada, se pueden ejecutar en el clúster como archivos JAR (Java Archive). Estos archivos JAR pueden distribuir al almacenamiento de blobs de Windows Azure y enviar HDInsight clústeres mediante mecanismos de envío de trabajo Hadoop. Para obtener más información, vea [Hadoop enviar trabajos mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md).

>[AZURE.NOTE] Si tiene problemas para implementar archivos JAR para clústeres HDInsight o llamar a archivos JAR en clústeres HDInsight, póngase en contacto con el [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

> En cascada no es compatible con HDInsight y no es elegible para Microsoft Support. Para obtener listas de componentes compatibles, vea [Novedades en las versiones de clúster proporcionadas por HDInsight?](hdinsight-component-versioning.md)

## <a name="use-edge-node"></a>Utilice el nodo del borde

 Un nodo del borde vacío es una máquina virtual de Linux con las herramientas cliente instalado y configurado como en la headnodes. Puede usar el nodo del borde para acceder el clúster, probar las aplicaciones de cliente y las aplicaciones cliente de hospedaje. Para obtener más información, vea [nodos de borde vacío de uso en HDInsight](hdinsight-apps-use-edge-node.md).
 
## <a name="cluster-creation-methods"></a>Métodos de creación de clúster

En este artículo, ha aprendido información básica sobre la creación de un clúster de HDInsight basados en Linux. Utilice la siguiente tabla para buscar información específica sobre cómo crear un clúster mediante un método que mejor se adapte a sus necesidades.

| Clústeres creados con | Explorador Web | Línea de comandos | API DE REST | SDK | Linux, Mac OS X o Unix | Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [El portal de Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔     | &nbsp; | &nbsp; | &nbsp; | ✔      | ✔ |
| [Generador de datos de Azure](hdinsight-hadoop-create-linux-clusters-adf.md) | ✔     | ✔  | ✔  |✔  | ✔      | ✔ |
| [CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)         | &nbsp; | ✔     | &nbsp; | &nbsp; | ✔      | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔     | &nbsp; | &nbsp; | ✔ | ✔ |
| [Doblez](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔     | ✔ | &nbsp; | ✔      | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔      | ✔ |
| [Plantillas de administrador de recursos de Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) | &nbsp; | ✔     | &nbsp; | &nbsp; | ✔      | ✔ |
