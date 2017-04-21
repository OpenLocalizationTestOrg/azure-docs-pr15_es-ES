<properties
   pageTitle="Resistencia para la recuperación de pérdida de una orientación técnica de región Azure | Microsoft Azure"
   description="Artículo de entender y diseñar aplicaciones tolerancia a errores de errores flexibles, altamente disponible, así como planificación de recuperación"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Orientación técnica de Azure resistencia: recuperación de una interrupción del servicio de toda la región

Azure se divide física y lógicamente en unidades denominadas regiones. Una región consta de los centros de datos de una o más cerca. En el momento de escribir este documento, Azure tiene 24 regiones del mundo.

En circunstancias especiales, es posible que las instalaciones en toda una región pueden convertirse en accesibles, por ejemplo debido a errores de red. O bien, las instalaciones de perder por completo, por ejemplo debido a un desastre natural. Esta sección explica las capacidades de Azure para crear aplicaciones que se distribución entre regiones. Esta distribución ayuda a reducir la posibilidad de que un error en una región podría afectar a otras áreas.

##<a name="cloud-services"></a>Servicios de nube

###<a name="resource-management"></a>Administración de recursos

Puede distribuir instancias de cálculo entre regiones mediante la creación de un servicio de nube independiente en cada región de destino y, a continuación, publicar el paquete de implementación para cada servicio de nube. Sin embargo, tenga en cuenta que la distribución de tráfico entre servicios de nube de diferentes regiones debe implementado por el desarrollador de la aplicación o con un servicio de administración de tráfico.

Determinar el número de instancias de la función reserva para implementar con anticipación para recuperación es un aspecto importante de planificación de capacidad. Tener una implementación completa secundaria garantiza que capacidad ya está disponible cuando es necesario; Sin embargo, esta eficaz duplica el costo. Un modelo común es tener una implementación pequeña secundaria lo bastante grande para ejecutar servicios críticos. Esta implementación secundaria pequeña es una buena idea, ambos reservar capacidad y para probar la configuración del entorno secundario.

>[AZURE.NOTE]La cuota de suscripción no es una garantía de capacidad. La cuota es simplemente un límite de crédito. Para garantizar la capacidad, el número requerido de funciones debe definirse en el modelo de servicio y, a continuación, se deben implementar los roles.

###<a name="load-balancing"></a>Equilibrio de carga

Para equilibrar la carga de tráfico a través de regiones requiere una solución de administración de tráfico. Azure proporciona [Administrador de tráfico de Azure](https://azure.microsoft.com/services/traffic-manager/). También puede aprovechar las ventajas de los servicios de terceros que proporcionan las capacidades de administración de tráfico similar.

###<a name="strategies"></a>Estrategias

Muchas estrategias alternativas están disponibles para implementar el cálculo distribuido en todas las regiones. Estos deben adaptarse a los requisitos empresariales específicos y circunstancias de la aplicación. En un nivel alto, los enfoques pueden dividirse en las siguientes categorías:

  * __Implementar en desastres__: en este método, la aplicación se redistribuirá desde cero en el momento de desastres. Esto es adecuado para aplicaciones no críticas que no requieren un tiempo de recuperación garantiza.

  * __Libre caliente (activo/pasivo)__: se crea un servicio hospedado secundario en una región alternativo y funciones se implementan para garantizar la capacidad de mínima; Sin embargo, los roles no reciben tráfico de producción. Este enfoque es útil para las aplicaciones que no han sido diseñadas para distribuir el tráfico entre regiones.

  * __Libre caliente (activo)__: la aplicación está diseñada para recibir la carga de producción en varias áreas. Se puede configurar los servicios de nube de cada región para mayor capacidad que necesaria para fines de recuperación de desastres. Como alternativa, pueden ajustar el tamaño de los servicios de nube out según sea necesario en el momento de un desastre y migración tras error. Este enfoque requiere una inversión considerable en diseño de la aplicación, pero tiene ventajas muy significativas. Incluyen tiempo de baja y garantiza la recuperación, continua pruebas de todas las ubicaciones de recuperación y el uso eficaz de capacidad.

Para obtener una descripción completa de diseño distribuido está fuera del ámbito de este documento. Para obtener más información, consulte [recuperación y alta disponibilidad para las aplicaciones de Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Máquinas virtuales de Windows

Recuperación de infraestructura como un máquinas virtuales de servicio (IaaS) (VM) es similar a la plataforma como servicio (PaaS) calcular recuperación en muchos aspectos. Hay diferencias importantes, sin embargo, puesto que la una VM IaaS consta de la máquina virtual y en el disco de máquina virtual.

  * __Usar la copia de seguridad de Azure crear copias de seguridad de región cruzada que sean coherentes de aplicación__.
  [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) permite a los clientes crear copias de seguridad coherentes en varios discos VM y admitir la replicación de copias de seguridad en todas las regiones. Puede hacerlo eligiendo geo replicar la cámara de copia de seguridad en el momento de creación. Tenga en cuenta que la replicación de la copia de seguridad de cámara debe estar configurado en el momento de creación. No se puede establecer más adelante. Si se pierde una región, Microsoft hará que las copias de seguridad disponibles para los clientes. Los clientes podrán restaurar a cualquiera de sus puntos de restauración configurado.

  * __Separar el disco de datos desde el disco de sistema operativo__. Un factor importante para máquinas virtuales de IaaS es que no se puede cambiar el disco de sistema operativo sin volver a crear la máquina virtual. No es un problema si su estrategia de recuperación es volver a implementar después desastre. Sin embargo, podría ser un problema si va a usar el enfoque caliente reserva para reservar capacidad. Para implementar correctamente, debe tener el disco de sistema operativo correcto implementado a las ubicaciones principales y secundarias, y los datos deben estar almacenados en una unidad independiente. Si es posible, utilice una configuración de sistema operativo estándar que se puede proporcionar en ambas ubicaciones. Después de una migración tras error, a continuación, debe asociar la unidad de datos a sus máquinas virtuales de IaaS existente en el controlador de dominio secundario. Utilizar AzCopy para copiar instantáneas de los discos de datos en un sitio remoto.

  * __Tenga en cuenta posibles problemas de coherencia después de la una migración geo de varios discos de VM__. Discos de VM se implementan como blobs de Azure almacenamiento y tener las mismas características de replicación geo. A menos que se utiliza la [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) , hay ninguna garantía de coherencia en discos, porque geo replicación es asincrónica y aplica de forma independiente. Discos de VM individuales se garantiza que un bloqueo coherente estado después de un error de geo, pero no coherente en discos. Esto podría provocar problemas en algunos casos (por ejemplo, en el caso de bandas de disco).

##<a name="storage"></a>Almacenamiento de información

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Recuperación usando Geo redundantes almacenamiento de blobs, tablas, cola y almacenamiento en disco VM

En Azure, blobs, tablas, colas y VM discos son replicar geo todos de forma predeterminada. Esto se conoce como almacenamiento Geo redundantes (GRS). GRS aplica almacenamiento de datos a un centro de datos pareja cientos de distancia dentro de una región geográfica específica. GRS está diseñado para proporcionar duración adicional en caso de que hay un desastre en el centro de datos importantes. Controles de Microsoft cuando se produce error y migración tras error se limita a desastres principales en los que la ubicación principal original se considera no recuperable en un período de tiempo razonable. En algunos casos, puede ser varios días. Datos suelen es duplicar en unos minutos, aunque el intervalo de sincronización no está todavía cubierto por un contrato de nivel de servicio.

En el caso de un failover geo, no habrá cambia cómo se accede a la cuenta (la tecla de dirección URL y la cuenta no cambiará). La cuenta de almacenamiento, sin embargo, estará en una región distinta después de la migración. Esto podría afectar a las aplicaciones que requieren regional afinidad con su cuenta de almacenamiento. Incluso para los servicios y aplicaciones que no requieren una cuenta de almacenamiento en el mismo centro de datos, la latencia de centro de datos cruzados y los cargos de ancho de banda podrían atractivos motivos para mover temporalmente el tráfico a la región de migración tras error. Esto puede incluir en una estrategia de recuperación general.

Además de migración tras error automática proporcionada por GRS, Azure ha introducido un servicio que proporciona acceso de lectura a la copia de los datos en la ubicación de almacenamiento secundario. Esto se denomina almacenamiento Geo redundantes de acceso de lectura (RA GRS).

Para obtener más información sobre el almacenamiento GRS y GRS RA, vea [replicación de almacenamiento de Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Asignaciones de región geográfica replicación:

Es importante saber donde los datos están geo-replicar, para saber dónde desplegar las otras instancias de los datos que requieren regional afinidad con el almacenamiento. La siguiente tabla muestra los pares de ubicación primario y secundario:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>Precios geo replicación:

Replicación geo se incluye en el precio actual de almacenamiento de Azure. Esto se denomina almacenamiento Geo redundantes (GRS). Si no desea que los datos replicada geo puede deshabilitar la replicación de geo para su cuenta. Se denomina almacenamiento redundante localmente y se cargará a un precio con descuento con respecto al GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Determinar si se ha producido un error de geo

Si se produce un error de geo, se registrará en el [Panel de estado del servicio de Azure](https://azure.microsoft.com/status/). Las aplicaciones pueden implementar un medio automatizado de detectar esto, sin embargo, mediante la supervisión de la región geográfica para su cuenta de almacenamiento. Esto puede usarse para desencadenar otras operaciones de recuperación, como la activación de recursos de cálculo en la región de geo que mover su almacenamiento. Puede realizar una consulta para este desde la administración del servicio API, mediante el uso de [Propiedades de la cuenta de almacenamiento obtener](https://msdn.microsoft.com/library/ee460802.aspx). Las propiedades relevantes son:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Discos de VM y migración tras error de geo

Como se describe en la sección en discos de VM, no hay ninguna garantía de coherencia de los datos en discos VM después de un error. Para garantizar la exactitud de las copias de seguridad, un producto de copia de seguridad, como el Administrador de protección de datos se debe utilizar para realizar copias de seguridad y restaurar los datos de aplicación.

##<a name="database"></a>Base de datos

###<a name="sql-database"></a>Base de datos SQL

Base de datos de SQL Azure proporciona dos tipos de recuperación: Geo restaurar y la replicación de Geo Active.

####<a name="geo-restore"></a>Restaurar geo

[Restaurar geo](../sql-database/sql-database-recovery-using-backups.md#geo-restore) también está disponible con bases de datos básico, estándar y Premium. Proporciona la opción de recuperación de forma predeterminada cuando la base de datos no está disponible debido a un incidente de la región donde se encuentra la base de datos. Similar al punto en el tiempo restaurar, restaurar Geo se basa en copias de seguridad de base de datos en el almacenamiento de Azure geo redundantes. Restaura de la copia de seguridad replicar geo y por tanto es y son resistente a las interrupciones de almacenamiento en el área principal. Para obtener más detalles, consulte [restaurar una base de datos de SQL Azure o una migración tras error a un secundario](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Replicación de Geo Active

[Replicación de Geo Active](../sql-database/sql-database-geo-replication-overview.md) está disponible para todos los niveles de base de datos. Está diseñado para las aplicaciones que tienen requisitos de recuperación más rigurosa que puede ofrecer Geo restaurar. Replicación de Geo Active puede crear un máximo de cuatro secundarios legibles en servidores de diferentes regiones. Puede iniciar la migración tras error a cualquiera de los secundarios. Además, la replicación de Geo Active puede usarse para admitir los escenarios de actualización o reubicación de aplicación, así como el equilibrio de carga para las cargas de trabajo de solo lectura. Para obtener información detallada, vea [Configurar la replicación de Geo](../sql-database/sql-database-geo-replication-portal.md) y conmutar [a la base de datos secundario](../sql-database/sql-database-geo-replication-failover-portal.md). Para obtener más información sobre cómo implementar aplicaciones y actualización de aplicaciones sin el tiempo de inactividad del diseño y, consulte [Active Geo replicación de base de datos de SQL mediante el diseño de una aplicación para la recuperación de la nube](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [actualizaciones graduales de administración de aplicaciones de nube de base de datos activa Geo replicación de SQL](../sql-database/sql-database-manage-application-rolling-upgrade.md) .

###<a name="sql-server-on-virtual-machines"></a>SQL Server en máquinas virtuales

Una gran variedad de opciones están disponibles para la recuperación y alta disponibilidad para SQL Server 2012 (y posteriores) se ejecuta en Azure máquinas virtuales de Windows. Para obtener más información, vea [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Otros servicios de la plataforma Windows Azure

Al intentar ejecutar el servicio de nube en varias áreas de Azure, debe tener en cuenta las implicaciones para cada una de las dependencias. En las secciones siguientes, las directrices específicos del servicio se supone que debe utilizar el mismo servicio de Azure en un centro de datos de Azure alternativo. Esto implica la configuración y las tareas de replicación de datos.

>[AZURE.NOTE]En algunos casos, estos pasos pueden ayudar a mitigar una interrupción del servicio específico en lugar de un evento de centro de datos completo. Desde la perspectiva de la aplicación, podría ser una interrupción específicos del servicio tal y como limitar y requeriría migrar temporalmente el servicio a un área de Azure alternativo.

###<a name="service-bus"></a>Bus de servicio

Bus de servicios de Azure usa un único espacio de nombres no abarca áreas de Azure. El primer requisito es configurar los espacios de nombres de bus de servicio necesaria en la región alternativa. Sin embargo, también hay consideraciones para la duración de los mensajes en cola. Existen varias estrategias para replicar mensajes en todas las regiones de Azure. Los detalles de estas estrategias de replicación y otras estrategias, vea [procedimientos recomendados para aplicaciones aislantes contra interrupciones de Bus de servicio y problemas](../service-bus-messaging/service-bus-outages-disasters.md). Para otras consideraciones de disponibilidad, vea [Bus de servicio (disponibilidad)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>Aplicación de servicio

Para migrar una aplicación de servicio de la aplicación de Azure, como aplicaciones Web o aplicaciones de Mobile a una región de Azure secundaria, debe tener una copia de seguridad del sitio Web disponible para la publicación. Si la interrupción no consisten en el centro de datos de Azure todo, es posible usar FTP para descargar una copia de seguridad reciente del contenido del sitio. A continuación, cree una nueva aplicación en la región alternativa, a menos que lo haya hecho previamente para reservar capacidad. Publicar el sitio en la nueva región y a continuación, realice los cambios de configuración necesarios. Dichos cambios pueden incluir las cadenas de conexión de base de datos o la configuración regional específico. Si es necesario, agregue el certificado del sitio SSL y cambie el registro CNAME de DNS para que el nombre de dominio personalizado apunta a la dirección URL nueva de Azure Web App.

###<a name="hdinsight"></a>HDInsight

Se almacenan los datos asociados a HDInsight de forma predeterminada en el almacenamiento de blobs de Windows Azure. HDInsight requiere que un clúster de Hadoop MapReduce trabajos de procesamiento debe encontrarse conjunta en la misma región como la cuenta de almacenamiento que contiene los datos que se está analizados. Si utiliza la característica de replicación geo disponible para el almacenamiento de Azure, puede acceder a los datos en la región secundario donde los datos se hayan duplicado si por algún motivo el área principal ya no está disponible. Puede crear un nuevo clúster de Hadoop en la región donde los datos replicados y continuar el procesamiento. Para otras consideraciones de disponibilidad, vea [HDInsight (disponibilidad)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Informes de SQL

En este momento, la recuperación de la pérdida de un área de Azure requiere varias instancias de SQL Reporting en diferentes regiones de Azure. Estas instancias SQL Reporting deben tener acceso a los mismos datos y que los datos deben tener su propia recuperación planear un desastre. También puede mantener externos copias de seguridad del archivo RDL para cada informe.

###<a name="media-services"></a>Media Services

Azure Media Services tiene un enfoque de recuperación diferentes para la codificación y transmisión. Normalmente, transmisión por secuencias es más importante durante una interrupción regional. Para preparar, debe tener una cuenta de Media Services en dos regiones de Azure diferentes. El contenido codificado debe estar ubicado en ambas regiones. Durante una falla, puede redirigir el tráfico de transmisiones a la región alternativa. Puede realizar la codificación de cualquier región de Azure. Si la codificación es urgentes, por ejemplo durante el proceso de evento en directo, debe estar preparado para enviar trabajos a un centro de datos alternativo durante los errores.

###<a name="virtual-network"></a>Red virtual

Archivos de configuración constituyen la forma más rápida de configurar una red virtual en un área de Azure alternativo. Después de configurar la red virtual en la región de Azure principal, [exporte la configuración de red virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) de la red actual a un archivo de configuración de red. En el caso de una interrupción en el área principal, [restaurar la red virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) del archivo de configuración almacenado. Configurar otros servicios en la nube, máquinas virtuales o configuración entre local para que funcione con la nueva red virtual.

##<a name="checklists-for-disaster-recovery"></a>Listas de comprobación para la recuperación

##<a name="cloud-services-checklist"></a>Lista de comprobación de servicios de nube

  1. Revise la sección de servicios de nube de este documento.
  2. Crear una estrategia de recuperación entre región.
  3. Comprender las compensaciones en reservar capacidad en regiones alternativas.
  4. Usar herramientas de enrutamiento de tráfico, como el administrador del tráfico de Azure.

##<a name="virtual-machines-checklist"></a>Lista de comprobación de máquinas virtuales

  1. Revise la sección de máquinas virtuales de este documento.
  2. Usar [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) para crear copias de seguridad coherentes en todas las regiones.

##<a name="storage-checklist"></a>Lista de comprobación de almacenamiento

  1. Revise la sección de almacenamiento de este documento.
  2. Deshabilitar la replicación geo de recursos de almacenamiento.
  3. Comprender la región alternativa para la replicación de geo en caso de migración tras error.
  4. Crear estrategias de copia de seguridad personalizadas para estrategias controlada por el usuario de migración tras error.

##<a name="sql-database-checklist"></a>Lista de comprobación de base de datos SQL

  1. Revise la sección de la base de datos SQL de este documento.
  2. Use [Geo restaurar](../sql-database/sql-database-recovery-using-backups.md#geo-restore) o [Replicación Geo](../sql-database/sql-database-geo-replication-overview.md) según corresponda.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server en máquinas virtuales lista de comprobación

  1. Revise el servidor de SQL Server en máquinas virtuales de sección de este documento.
  2. Usar grupos de disponibilidad AlwaysOn entre región o reflejo de base de datos.
  3. También puede utilizar copia de seguridad y restaurar el almacenamiento de blobs.

##<a name="service-bus-checklist"></a>Lista de comprobación de Bus de servicio

  1. Revise la sección de Bus de servicio de este documento.
  2. Configurar un espacio de nombres de Bus de servicio en una región alternativo.
  3. Considere la posibilidad de estrategias de duplicación personalizada para mensajes en todas las regiones.

##<a name="app-service-checklist"></a>Lista de comprobación de servicio de aplicaciones

  1. Revise la sección servicio de aplicación de este documento.
  2. Mantener las copias de seguridad del sitio fuera de la región principal.
  3. Si la interrupción es parcial, intentar recuperar el sitio actual con FTP.
  4. Plan para implementar el sitio en un sitio nuevo o existente en una región alternativo.
  5. Planear los cambios de configuración de aplicación y registros CNAME de DNS.

##<a name="hdinsight-checklist"></a>Lista de comprobación de HDInsight

  1. Revise la sección HDInsight de este documento.
  2. Crear un nuevo clúster de Hadoop en la región de datos duplicados.

##<a name="sql-reporting-checklist"></a>Lista de comprobación de informes de SQL

  1. Revise la sección informes de SQL de este documento.
  2. Mantener una instancia de SQL Reporting alternativa en una región diferente.
  3. Mantener un plan independiente para replicar el destino en esa región.

##<a name="media-services-checklist"></a>Lista de comprobación de Media Services

  1. Revise la sección Media Services de este documento.
  2. Crear una cuenta de Media Services en una región alternativa.
  3. Codificar el mismo contenido en ambas regiones para admitir streaming migración tras error.
  4. Enviar trabajos de codificación a una región alternativo en caso de una interrupción del servicio.

##<a name="virtual-network-checklist"></a>Lista de comprobación de red virtual

  1. Revise la sección de red Virtual de este documento.
  2. Usar exporta configuración de una red virtual para volver a crearlo en otra región.

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie centrada en [orientación técnica de la resistencia de Azure](./resiliency-technical-guidance.md). El siguiente artículo de esta serie se centra en la [recuperación de un centro de datos local en Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
