<properties
   pageTitle="Soluciones de lote y la informática en la nube | Microsoft Azure"
   description="Obtenga más información sobre el lote y escenarios de alto rendimiento informática (HPC y calcular grande) y opciones de solución de Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/27/2016"
   ms.author="danlep"/>

# <a name="batch-and-hpc-solutions-in-the-azure-cloud"></a>Soluciones de lote y la informática en la nube de Azure

Azure ofrece soluciones de nube eficaz, scalable por lotes y la informática de alto rendimiento (HPC) - también denominadas *Calcular grande*. Conozca aquí calcular grandes cargas de trabajo y servicios de Azure compatible o saltar directamente a [escenarios de soluciones](#scenarios) más adelante en este artículo. Este artículo es principalmente para responsables técnicos, los administradores de TI y proveedores de software independientes, pero otros profesionales de TI y los desarrolladores pueden usarlo para familiarizarse con estas soluciones.

Las organizaciones tiene problemas de los equipos a gran escala: ingeniería diseño y análisis, representación de imagen, modelos complejos, simulaciones Montecarlo, cálculos de riesgos financieros y otros usuarios. Azure ayuda a las organizaciones a resolver estos problemas con los recursos, la escala y la programación que necesitan. Con Azure, las organizaciones pueden:

* Crear soluciones híbrido, extender un clúster HPC local para descargar cargas en la nube

* Ejecutar cargas de trabajo y herramientas de clúster HPC completamente en Azure

* Usar servicios de Azure administrados y scalable como [lote](https://azure.microsoft.com/documentation/services/batch/) para ejecutar cargas de trabajo de descarga intensiva sin tener que implementar y administrar la infraestructura de cálculo

Aunque fuera del ámbito de este artículo, Azure también proporciona a los desarrolladores y asociados de un conjunto completo de capacidades, opciones de arquitectura y herramientas de desarrollo para crear flujos de trabajo calcular grande a gran escala, personalizados. Y un entorno de partner creciente es listo para ayudarle a tomar las cargas de trabajo calcular grande productivo en la nube de Azure.


## <a name="batch-and-hpc-applications"></a>Aplicaciones de lote y la informática

A diferencia de web aplicaciones y muchas aplicaciones de línea de negocio, lote y aplicaciones HPC tienen un principio definido y un final, y pueden ejecutar en una programación o a petición, en ocasiones durante horas o más. La mayoría se dividen en dos categorías principales: *esencia paralelo* (a veces denominado "una paralelo", porque los problemas que resuelven adecuadas para que se ejecuten en paralelo en varios equipos o procesadores) y *acopla estrechamente*. Para obtener más información acerca de estos tipos de aplicación, vea la tabla siguiente. Algunas soluciones Azure enfoques funcionen mejor para un tipo u otro.

>[AZURE.NOTE] En las soluciones de lote y la informática, una instancia de ejecución de una aplicación se denomina normalmente un *trabajo*y cada trabajo obtener divide *las tareas*. Y los recursos de clúster de cálculo para la aplicación a menudo se denominan *nodos de cálculo*.

Tipo | Características | Ejemplos
------------- | ----------- | ---------------
**Esencia en paralelo**<br/><br/>![Esencia en paralelo][parallel] |• Equipos individuales ejecutar lógica de la aplicación de forma independiente<br/><br/> • Agregar equipos permite a la aplicación escalar y reducir el tiempo de cálculo<br/><br/>• Aplicación consta de ejecutables independiente o se divide en un grupo de servicios invocada un cliente (una arquitectura de servicio o inicio de autoridad, aplicación) |• Modelado de riesgos financieros<br/><br/>• La representación de imagen y procesamiento de imágenes<br/><br/>• Media codificación y transcodificación<br/><br/>• Montecarlo simulaciones<br/><br/>• Pruebas de software
**Acopla estrechamente**<br/><br/>![Acopla estrechamente][coupled] |• Aplicación requiere nodos de cálculo para interactuar o intercambiar resultados intermedios<br/><br/>• Nodos de cálculo pueden comunicarse con el mensaje de interfaz de paso (MPI), un protocolo de comunicaciones comunes para la informática paralela<br/><br/>• La aplicación distingue ancho de banda y latencia de red<br/><br/>• Rendimiento de la aplicación se puede mejorar mediante el uso de tecnologías de red de alta velocidad como InfiniBand y remoto acceso directo a memoria (RDMA) |Modelado de depósito de combustible y gas •<br/><br/>• Diseño de ingeniería y análisis, como dinámica de fluidos<br/><br/>• Simulaciones físico como bloqueos del automóvil y reacciones nucleares<br/><br/>Previsión meteorológica de •

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Consideraciones para ejecutar el lote y aplicaciones de HPC en la nube

Puede migrar fácilmente muchas aplicaciones están diseñadas para ejecutarse en clústeres HPC local para Azure, o para un entorno híbrido (entre local). Sin embargo, puede haber algunas limitaciones o consideraciones, incluidos:


* **Disponibilidad de recursos de la nube** : según el tipo de recursos de cálculo de nube que utiliza, es posible que no pueda dependen de disponibilidad de la máquina continua mientras se ejecuta un trabajo. Progreso y el estado de control comprueban que señala estén técnicas comunes para controlar los posibles errores transitorias y más necesarias al utilizar recursos de la nube.


* **Acceso a datos** : técnicas de acceso a datos disponible normalmente en clústeres de empresa, como NFS, pueden requerir una configuración especial en la nube. O bien, es posible que deba adoptar prácticas de acceso de datos diferente y las tramas de la nube.

* **Movimiento de datos** - para las aplicaciones que procesan grandes cantidades de datos, estrategias son necesarios para mover los datos en el almacenamiento de la nube y para calcular los recursos. Es posible que tenga alta velocidad local entre redes como [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). También puede legales, normativos o limitaciones de la directiva para almacenar y acceder a esos datos.


* **Licencias** : póngase en contacto con el proveedor de cualquier aplicación comercial de restricciones de licencia o de otras para ejecutar en la nube. No todos los proveedores ofrecen licencias de pago. Debe planear un servidor de licencias en la nube para la solución o conectarse a un servidor de licencias local.


### <a name="big-compute-or-big-data"></a>¿Cálculo grande o datos grandes?

La línea divisoria entre las aplicaciones Big Data y calcular grande no siempre está clara y algunas aplicaciones pueden tener características de ambos. Ambos implican ejecutan cálculos a gran escala, normalmente en clústeres de equipos. Pero la solución enfoques y herramientas de soporte técnico pueden ser distintas.

• **Calcular grande** tiende a implican aplicaciones que se basan en la capacidad de CPU y memoria, como simulaciones de ingeniería, riesgo financiero modelado y representación digital. La infraestructura para una solución calcular grande puede incluir equipos con procesadores multinúcleo especializados para realizar cálculos sin formato y especializada y de alta velocidad hardware de red para conectar los equipos.

• **Big Data** resuelva los problemas de análisis de datos que implican grandes cantidades de datos que no se puede administrar un sistema de administración de base de datos o equipo solo. Ejemplos de grandes volúmenes de registros web u otros datos de inteligencia empresarial. Big Data tiende a dependen más capacidad de disco y rendimiento de i/OS que en la potencia de CPU. También hay herramientas especializadas de datos grande como Apache Hadoop para administrar el clúster y partición de los datos. (Para obtener información sobre otras soluciones de Hadoop de Azure y Azure HDInsight, consulte [Hadoop](https://azure.microsoft.com/solutions/hadoop/)).

## <a name="compute-management-and-job-scheduling"></a>Calcular la programación de trabajos y administración

Ejecutar aplicaciones de lote y la informática a menudo incluye un *Administrador de clúster* y un *Planificador de tareas* para ayudar a administrar recursos de clúster de cálculo y asignarlos a las aplicaciones que se ejecutan los trabajos. Estas funciones pueden realizarse mediante herramientas independientes, o una herramienta integrada o un servicio.

* **Administrador de clúster** : disposiciones, libera y administra calcular recursos (o nodos de cálculo). Un administrador de clúster puede automatizar la instalación de imágenes del sistema operativo y aplicaciones en los nodos de cálculo, ajustar el tamaño de los recursos de cálculo según las peticiones y supervisar el rendimiento de los nodos.

* **Planificador** - especifica los recursos (como procesadores o memoria) una aplicación necesidades y las condiciones cuando se ejecuta. Planificador de tareas mantiene una cola de trabajos y asigna los recursos en función de asignada una prioridad u otras características.

Pueden migrar clústeres y herramientas para clústeres basadas en Windows y Linux de la programación de trabajos bien a Azure. Por ejemplo, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), solución de clúster de cálculo gratuita de Microsoft para cargas de trabajo de Windows y Linux HPC, ofrece varias opciones para ejecutar en Azure. También puede crear clústeres de Linux para ejecutar herramientas de código abierto como par y SLURM. También puede ofrecer soluciones de cuadrícula comercial a Azure, como [TIBCO DataSynapse GridServer](http://www.tibco.com/company/news/releases/2016/tibco-to-accelerate-cloud-adoption-of-banking-and-capital-markets-customers-via-microsoft-collaboration), [IBM plataforma Symphony](http://www-01.ibm.com/support/docview.wss?uid=isg3T1023592)y [Motor de cuadrícula Univa](http://www.univa.com/products/grid-engine).

Tal como se muestra en las secciones siguientes, también puede aprovechar las ventajas de los servicios de Azure para administrar recursos de cálculo y programar herramientas de administración de clúster tradicional de trabajos sin (o además de).


## <a name="scenarios"></a>Escenarios

Hay tres escenarios comunes para ejecutar un tamaño grande para calcular las cargas de trabajo en Azure mediante las soluciones existentes de clúster HPC, servicios de Azure o una combinación de ambos. Consideraciones claves para elegir cada escenario se muestran, pero no la exhaustivas. Más acerca de los servicios de Azure disponibles que puede usar en su solución es más adelante en el artículo.

  | Escenario | ¿Por qué elegir él?
------------- | ----------- | ---------------
**Ráfaga un clúster HPC de Azure**<br/><br/>[! [Clúster ráfaga] [burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Aprende más:<br/>• [Ráfaga a instancias de trabajo Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurar un híbrido calcular clúster con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Ráfaga lote Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/>|• Combinar el [Paquete de HPC de Microsoft](https://technet.microsoft.com/library/cc514029) u otro clúster local con recursos adicionales de Azure en una solución híbrida.<br/><br/>• Ampliar las cargas de trabajo calcular grande para que se ejecute en plataforma como servicio (PaaS) instancias de máquina virtual (actualmente sólo Windows Server).<br/><br/>• Obtener acceso a un almacén de datos o servidor de licencias local mediante una red virtual Azure opcional|• Tiene un clúster HPC existente y necesita más recursos <br/><br/>• Que no desea comprar y administre la infraestructura adicional de clúster HPC<br/><br/>• Tiene períodos de recursos asignadas a petición transitorias o proyectos especiales
**Crear un clúster HPC completamente en Azure**<br/><br/>[! [Clúster de IaaS] [iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Aprende más:<br/>• [Soluciones de clúster HPC en Azure](./big-compute-resources.md)<br/><br/>|• Implementar rápida y coherente las aplicaciones y herramientas de clúster en la infraestructura de Windows o Linux estándar o personalizada como un máquinas virtuales de servicio (IaaS).<br/><br/>• Ejecutar varias cargas de trabajo calcular grande mediante la solución de su elección de programación de trabajos.<br/><br/>• Use servicios adicionales de Azure incluidos redes y almacenamiento para crear soluciones completas en la nube. |• Que no desea comprar y administre la infraestructura adicional de clúster Linux o Windows HPC<br/><br/>• Tiene períodos de recursos asignadas a petición transitorias o proyectos especiales<br/><br/>• Necesita una adicional clúster una hora pero no desea invertir en equipos y espacio para implementarlo<br/><br/>• Desea descargar la aplicación de descarga intensiva para que se ejecute como un servicio por completo en la nube
**Escalar una aplicación paralela a Azure**<br/><br/>[! [Lote azure] [batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Aprende más:<br/>• [Conceptos básicos del lote de Azure](./batch-technical-overview.md)<br/><br/>• [Introducción a la biblioteca de Azure lote para .NET](./batch-dotnet-get-started.md)|• Desarrollar con [Azure lote](https://azure.microsoft.com/documentation/services/batch/) escalar varias cargas de trabajo de cálculo grande para que se ejecute en grupos de máquinas virtuales de Windows o Linux.<br/><br/>• Use un servicio de la plataforma Windows Azure para administrar la implementación y ajuste automático de máquinas virtuales, programación de trabajos, recuperación, movimiento de datos, administración de dependencia e implementación de aplicaciones.|• Que no desea administrar calcular recursos o un programador de tareas en su lugar, desea centrarse en ejecución de las aplicaciones<br/><br/>• Desea descargar la aplicación de descarga intensiva para que se ejecute como un servicio en la nube<br/><br/>• Desea cambiar automáticamente la escala de los recursos de cálculo para que coincida con la carga de trabajo de cálculo


## <a name="azure-services-for-big-compute"></a>Servicios de Azure para calcular grande

Aquí encontrará más información sobre el cálculo, datos, redes y servicios relacionados que puede combinar para calcular un tamaño grande para soluciones y flujos de trabajo. Para obtener información detallada sobre servicios de Azure, consulte la [documentación](https://azure.microsoft.com/documentation/)de servicios de Azure. Los [escenarios](#scenarios) más adelante en este artículo se muestran solo algunas formas de utilizar estos servicios.

>[AZURE.NOTE] Azure con regularidad presenta nuevos servicios que pueden ser útiles para su situación. Si tiene preguntas, póngase en contacto con un [asociado de Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) o correo electrónico *bigcompute@microsoft.com*.

### <a name="compute-services"></a>Servicios de proceso

Servicios de Azure cálculo son el componente principal de una solución calcular grande y las ventajas de oferta de servicios de cálculo diferentes para diferentes escenarios. En un nivel básico, estos servicios ofrecen diferentes modos de aplicaciones que se ejecutan en instancias de cálculo basado en máquina virtual Azure proporciona mediante la tecnología de Windows Server Hyper-V. Pueden ejecutar estas instancias estándar o personalizados herramientas y sistemas operativos de Windows y Linux. Azure ofrece una opción de [tamaños de instancia](../virtual-machines/virtual-machines-windows-sizes.md) con diferentes configuraciones de núcleos de CPU, memoria, capacidad de disco y otras características. Según sus necesidades, puede ajustar el tamaño de las instancias de miles de núcleos y luego escalar hacia abajo cuando tenga menos recursos.

>[AZURE.NOTE] Aprovechar las ventajas de las instancias de descarga intensiva Azure para mejorar el rendimiento y escalabilidad de cargas de trabajo HPC incluidos aplicaciones MPI paralelas que requieren una red de aplicación de alto rendimiento y baja latencia. Consulte [acerca de máquinas virtuales de una serie de serie de H y descarga intensiva](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).  

Servicio | Descripción
------------- | -----------
**[Máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Proporcionar calcular infraestructura como servicio (IaaS) con la tecnología de Microsoft Hyper-V<br/><br/>• Habilitar flexible aprovisionar y administrar equipos de nube persistente de imágenes de Windows Server o Linux estándar de [Azure Marketplace](https://azure.microsoft.com/marketplace/), o las imágenes y los discos de datos proporcionados<br/><br/>• Se puede implementar y administrar como [Conjuntos de escala de VM](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) para generar servicios a gran escala de idénticos máquinas virtuales de Windows, con ajuste automático para aumentar o reducir la capacidad de automáticamente<br/><br/>• Ejecutar local calcular herramientas de clúster y aplicaciones por completo en la nube<br/><br/>
**[Servicios de nube](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Puede ejecutar aplicaciones calcular grande en trabajo instancias de rol, que son máquinas virtuales que ejecute una versión de Windows Server y se administran por completo de Azure<br/><br/>• Permiten a aplicaciones scalable, confiables con baja carga administrativa, en la que se ejecuta en una plataforma como un modelo de servicio (PaaS)<br/><br/>• Pueden requerir herramientas adicionales o desarrollo integrar con soluciones de clúster HPC local
**[Proceso por lotes](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Se ejecuta cargas de trabajo paralelo y lote a gran escala en un servicio totalmente gestionado<br/><br/>• Proporciona la programación de trabajos y ajuste automático de un grupo de máquinas virtuales administrado<br/><br/>• Permite a los desarrolladores compilar y ejecutar aplicaciones como servicio o nube habilitar aplicaciones existentes<br/>

### <a name="storage-services"></a>Servicios de almacenamiento

Una solución calcular grande normalmente funciona en un conjunto de datos de entrada y genere los datos para los resultados. Algunos de los servicios de almacenamiento de Azure utilizados en calcular grande soluciones son:

* [Blob, tabla y el almacenamiento de cola](https://azure.microsoft.com/documentation/services/storage/) - administrar grandes cantidades de datos no estructurados, datos NoSQL y mensajes de flujo de trabajo y comunicaciones, respectivamente. Por ejemplo, puede usar el almacenamiento de blobs de grandes conjuntos de datos técnicos o de las imágenes de entrada o archivos multimedia de los procesos de aplicación. Puede usar colas para la comunicación asincrónica en una solución. Vea [Introducción al almacenamiento de Azure](../storage/storage-introduction.md).

* [Almacenamiento de archivos de azure](https://azure.microsoft.com/services/storage/files/) : recursos compartidos archivos comunes y los datos de Azure mediante el protocolo SMB estándar, que es necesario para algunas soluciones de clúster HPC.

* [Almacén de datos de lago](https://azure.microsoft.com/services/data-lake-store/) - proporciona un sistema de archivos distribuido Apache Hadoop ampliados para la nube, útil para el lote, en tiempo real y análisis interactivo.

### <a name="data-and-analysis-services"></a>Servicios de datos y análisis

Algunos escenarios calcular grande implican flujos de datos a gran escala o generan datos que necesita más procesamiento o análisis. Azure ofrece varios servicios y análisis de datos, incluidos:

* [Generador de datos](https://azure.microsoft.com/documentation/services/data-factory/) - flujos de trabajo basados en datos de versiones (canalizaciones) que unirse, agregado y transformar datos locales, basada en la nube y almacena datos de Internet.

* [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/) - proporciona las características clave de un sistema de administración de la base de datos relacional de Microsoft SQL Server en un servicio administrado.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - implementa y disposiciones clústeres de Windows Server o basados en Linux Apache Hadoop en la nube para administrar, analizar e informar de datos grandes.

* [Aprendizaje](https://azure.microsoft.com/documentation/services/machine-learning/) : le ayuda a crear, probar, operar y administrar soluciones analíticas predictivas en un servicio totalmente gestionado.

### <a name="additional-services"></a>Servicios adicionales

La solución calcular grande que tenga otros servicios de Azure para conectarse a recursos locales o en otros entornos. Algunos ejemplos:

* [Una red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) - crea una sección lógicamente aislada en Azure conectarse Azure recursos entre sí o a su centro de datos local. Con una red virtual entre local, aplicaciones calcular grande pueden tener acceso a datos locales, servicios de Active Directory y servidores de licencias

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - crea una conexión privada entre los centros de datos de Microsoft y la infraestructura de local o en un entorno de la ubicación. ExpressRoute proporciona mayor seguridad, más confiabilidad, velocidades y latencias menores que típico conexiones a través de Internet.

* [Bus de servicio](https://azure.microsoft.com/documentation/services/service-bus/) : proporciona varios mecanismos para aplicaciones para comunicarse o intercambiar datos, si se encuentran en Azure, en otra plataforma de nube o en un centro de datos.

## <a name="next-steps"></a>Pasos siguientes

* Vea [Los recursos técnicos para el lote y la informática](big-compute-resources.md) para encontrar orientación técnica para generar la solución.

* Explique las opciones de Azure con sus socios incluidos UberCloud e informática de ciclo.

* Obtenga información sobre calcular grande de Azure soluciones emitidas por [Torre Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)y [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Para los últimos anuncios, consulte [Microsoft HPC y el blog del equipo de lote](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png
