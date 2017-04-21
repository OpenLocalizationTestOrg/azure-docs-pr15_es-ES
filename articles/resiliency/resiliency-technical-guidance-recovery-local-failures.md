<properties
   pageTitle="Orientación técnica: recuperación de errores locales en Azure | Microsoft Azure"
   description="Artículo en Descripción y diseñar flexibles, altamente disponible, tolerancia aplicaciones, así como planificación de recuperación centrado en errores locales dentro de Azure."
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

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Orientación técnica de Azure resistencia: recuperación de errores locales en Azure

Hay dos amenazas principales a la disponibilidad de aplicaciones:

* El error de dispositivos, como unidades y servidores
* El agotamiento de los recursos críticos, como cálculo bajo condiciones de carga de recursos asignadas

Azure proporciona una combinación de administración de recursos, elasticidad, equilibrio de carga y particiones para habilitar alta disponibilidad en estas circunstancias. Algunas de estas características se realizan automáticamente para todos los servicios de Azure. Sin embargo, en algunos casos, el desarrollador de la aplicación deberá realizar ciertas tareas adicionales para beneficiarse de ellas.

##<a name="cloud-services"></a>Servicios de nube

Servicios de nube de Azure consta de colecciones de una o varias funciones web o de trabajo. Una o varias instancias de una función pueden ejecutar simultáneamente. La configuración determina el número de instancias. Instancias de la función se supervisa y se administran mediante un componente denominado el controlador de tela. El controlador de tela detecta y responde automáticamente los errores de hardware y software.

Cada instancia de la función se ejecuta en su propia máquina virtual (VM) y se comunica con su controlador de tela a través de un agente de invitado. El agente de invitado recopila métricas de recursos y nodo, incluido el uso VM, estado, registros, uso de recursos, excepciones y las condiciones de error. El controlador de tela consulta al agente de invitado a intervalos configurables y reinicia la máquina virtual si el agente de invitado deja de responder. En caso de error de hardware, el controlador de tela asociado mueve todas las instancias de la función afectado a un nuevo nodo de hardware y vuelve a configurar la red para enrutar el tráfico allí.

Para beneficiarse de estas características, los desarrolladores deben asegurarse de que todos los roles de servicio evitan almacenar el estado de las instancias de la función. En su lugar, deben tener acceso a todos los datos persistentes de almacenamiento resistente, como el almacenamiento de Azure o base de datos de SQL Azure. Esto permite que las funciones controlar las solicitudes. También significa que las instancias de función pueden ir hacia abajo en cualquier momento sin necesidad de crear incoherencias en el estado transitorio o persistente del servicio.

El requisito para almacenar el estado externamente a las funciones tiene varias implicaciones. Por ejemplo, que implica que todos los cambios relacionados con una tabla de almacenamiento de Azure se deben cambiar si es posible en una sola transacción de grupo de la entidad. Por supuesto, no es siempre posible hacer que todos los cambios en una sola transacción. Debe prestar atención especial para garantizar que errores de instancia de función no causar problemas cuando interrumpen operaciones de ejecución larga que abarcan dos o más actualizaciones de estado persistente del servicio. Si otro rol intenta volver a intentar esta operación, debe anticipar y controlar el caso de que el trabajo se ha completado parcialmente.

Por ejemplo, considere la posibilidad de un servicio que divide los datos a través de varias stores. Si una función de trabajo deja de funcionar mientras se reubicación de un fragmentar, la reubicación de las partes podría no finalizar. O bien, se pueden repetir la reubicación desde el principio mediante una función de trabajo diferentes, posiblemente causando datos huérfanos o daños en los datos. Para evitar problemas, operaciones de ejecución larga deben ser uno o ambos de los procedimientos siguientes:

 * *Idempotente*: puede repetir sin efectos secundarios. Para ser idempotente, una operación de larga duración debe tener el mismo efecto sin importar cuántas veces se ejecuta, incluso cuando se vuelvan a interrumpir durante la ejecución.
 * *Reinicio incrementalmente*: puede continuar desde el punto de error más reciente. Para ser incrementalmente reinicio, una operación de larga duración debe constar de una secuencia de pequeñas atómicas operaciones. También debe registrar su progreso en resistente almacenamiento, para que cada invocación posterior recoge donde se detiene su predecesora.

Por último, todas las operaciones de ejecución larga deben invocar varias veces hasta que realice correctamente. Por ejemplo, una operación de aprovisionamiento posible que se coloca en una cola de Azure y quita de la cola por una función de trabajador solo cuando complete la operación. Recolección podría ser necesaria para limpiar los datos que vuelvan a interrumpir operaciones crear.

###<a name="elasticity"></a>Elasticidad

El número inicial de instancias que se ejecutan para cada rol se determina en la configuración de la función. Los administradores deben configurar inicialmente cada rol para ejecutarse con dos o más instancias en función de la carga esperada. Pero puede escalar fácilmente las instancias de función hacia arriba o hacia abajo como uso tramas cambian. Puede hacerlo manualmente en el portal de Azure, o puede automatizar el proceso mediante el uso de Windows PowerShell, las API de administración de servicio o las herramientas de terceros. Para obtener más información, vea [cómo Autoescala una aplicación](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Partición

El controlador de Azure tela usa dos tipos de particiones:

* Una *actualización de dominio* se usa para actualizar instancias de rol del servicio de grupos. Azure implementa instancias del servicio en varios dominios de actualización. Una actualización en contexto, el controlador de tela obtiene hacia abajo todas las instancias de una actualización de dominio, actualiza y, a continuación, reinicia antes de pasar a la siguiente actualización del dominio. Este método impide que todo el servicio no está disponible durante el proceso de actualización.
* Un *dominio de error* define posibles puntos de error de hardware o de red. Para cualquier función que tiene más de una instancia, el controlador de tela garantiza que las instancias se distribuyen entre varios dominios de error, para evitar que los errores de hardware aislado interrumpir el servicio. Dominios de error rigen exposición todos los errores del servidor y clúster.

El [contrato de nivel de servicio (SLA) Azure](https://azure.microsoft.com/support/legal/sla/) garantiza que cuando dos o más instancias de rol web se implementan en diferentes errores y actualización los dominios, tendrá conectividad externa al menos 99,95% del tiempo. A diferencia de dominios de actualización, no hay ninguna manera de controlar el número de dominios de error. Azure automáticamente asigna los dominios de error y distribuye las instancias de función en ellos. Al menos primero dos instancias de todas las funciones se ubican en diferentes errores y actualización los dominios para asegurarse de que ninguna función con al menos dos instancias cumplan el SLA. Se representa en el diagrama siguiente.

![Vista simplificada de aislamiento de dominio de errores](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Equilibrio de carga

Todo el tráfico a un rol de web entrante atraviesa un equilibrador de carga sin estado, que distribuye las solicitudes de cliente entre las instancias de la función. Las instancias de una función individual no tiene direcciones IP públicas y no están directamente accesible desde Internet. Funciones Web no tienen estadas para que las solicitudes de cliente se pueden enrutar a cualquier instancia de rol. Cada 15 segundos, se produce un evento de [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) . Puede utilizar para indicar si está lista para recibir el tráfico de la función o si está ocupado y debería tomarse fuera de la rotación de equilibrador de carga.

##<a name="virtual-machines"></a>Máquinas virtuales de Windows

Máquinas virtuales de Windows Azure es distinto de la plataforma como servicio (PaaS) calcular roles en varios aspectos en relación con alta disponibilidad. En algunos casos, debe realizar trabajo adicional para garantizar la alta disponibilidad.

###<a name="disk-durability"></a>Duración de disco

A diferencia de las instancias de función PaaS datos almacenados en unidades de máquina virtual están persistentes incluso cuando se traslada la máquina virtual. Máquinas virtuales de Windows Azure usar discos de VM que existen como BLOB en el almacenamiento de Azure. Debido a las características de disponibilidad del almacenamiento de Azure, los datos almacenados en unidades de la máquina virtual también están altamente disponibles.

Observe que la unidad D (en máquinas virtuales de Windows) es la excepción a esta regla. Unidad D es almacenamiento realmente físico en el servidor de bastidor que hospeda la máquina virtual y sus datos se perderán si la máquina virtual es recicla. Unidad D está destinada solo almacenamiento temporal. En Linux, Azure "normalmente" (pero no siempre) expone el disco temporal local como dispositivo de bloques de /dev/sdb. A menudo es montaje por el agente de Azure Linux como /mnt/resource o/mnt puntos de montaje (configurables a través de /etc/waagent.conf).

###<a name="partitioning"></a>Partición

Azure forma nativa comprende los niveles de una aplicación PaaS (función web y rol de trabajo) y, por tanto, puede correctamente distribuirlo en errores y actualización de dominios. Por el contrario, se deben definir manualmente los niveles en una infraestructura como una aplicación de servicio (IaaS) a través de conjuntos de disponibilidad. Conjuntos de disponibilidad son necesarios para un SLA en IaaS.

![Conjuntos de disponibilidad para máquinas virtuales de Windows Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

En el diagrama anterior, el nivel de Internet Information Services (IIS) (que funciona como un nivel de aplicación web) y el nivel SQL (que funciona como un nivel de datos) se asignan a conjuntos de disponibilidad diferentes. Esto asegura que todas las instancias de cada nivel tienen redundancia de hardware al distribuir máquinas virtuales entre los dominios de error y que todo niveles no se tomen hacia abajo durante una actualización.

###<a name="load-balancing"></a>Equilibrio de carga

Si las máquinas virtuales deben tener tráfico distribuye entre ellos, debe agrupar las máquinas virtuales en un equilibrio de carga y de aplicación a través de un extremo TCP o UDP específico. Para obtener más información, consulte [máquinas virtuales de equilibrio de carga](../virtual-machines/virtual-machines-linux-load-balance.md). Si las máquinas virtuales reciban entrada desde otro origen (por ejemplo, mecanismo de cola), no es necesario un equilibrador de carga. El equilibrador de carga utiliza una comprobación de estado básico para determinar si se debe enviar tráfico al nodo. También es posible crear sus propios sondeos para implementar métricas de mantenimiento específicos de la aplicación que determinan si la máquina virtual debe recibir el tráfico.

##<a name="storage"></a>Almacenamiento de información

Almacenamiento de Azure es el servicio de datos resistentes de línea base para Azure. Proporciona blobs, tabla, cola y almacenamiento de disco de máquina virtual. Utiliza una combinación de replicación y administración de recursos para proporcionar alta disponibilidad en un único centro de datos. La disponibilidad de almacenamiento de Azure SLA garantiza que al menos 99,9 por ciento del tiempo:

* Solicitudes correctamente con formato para agregar, actualizar, lea y eliminar datos correctamente y correctamente procesará.
* Cuentas de almacenamiento tendrá conectividad con la puerta de enlace de Internet.

###<a name="replication"></a>Replicación

Almacenamiento de Azure facilita la duración de datos al mantener varias copias de todos los datos de unidades diferentes en subsistemas de almacenamiento físico completamente independientes dentro de la región. Datos se duplica sincrónicamente y todas las copias se confirman antes de que se confirma la escritura. Almacenamiento de Azure es encarecidamente coherente, lo que significa que lee se garantiza para reflejar la escritura más reciente. Además, las copias de los datos se analizan continuamente para detectar y reparar bit rot, una amenaza a la integridad de los datos almacenados a menudo se pasa por alto.

Prestación de servicios de replicación simplemente mediante el almacenamiento de Azure. El desarrollador de servicio no es necesario realizar trabajo adicional para recuperar de un error local.

###<a name="resource-management"></a>Administración de recursos

Cuentas de almacenamiento creadas después de mayo de 2014, puede aumentar para hasta 500 TB (el máximo anterior era 200 TB). Si se requiere un espacio adicional, se deben diseñar aplicaciones para utilizar varias cuentas de almacenamiento.

###<a name="virtual-machine-disks"></a>Discos de máquina virtual

Disco de la máquina virtual se almacena como un blob de página en el almacenamiento de Azure, póngale las mismas propiedades de duración y escalabilidad como almacenamiento de blobs. Este diseño pone los datos en el disco de la máquina virtual persistente, incluso si se produce un error en el servidor que ejecuta la máquina virtual y se debe reiniciar la máquina virtual en otro servidor.

##<a name="database"></a>Base de datos

###<a name="sql-database"></a>Base de datos SQL

Base de datos de SQL Azure proporciona la base de datos como un servicio. Permite que las aplicaciones aprovisionar rápidamente, insertar datos y bases de datos relacionales de consulta. Proporciona muchas de las características de SQL Server y la funcionalidad de familiares mientras condensar la carga de hardware, configuración, revisiones y resistencia.

>[AZURE.NOTE] Base de datos de SQL Azure no proporciona paridad característica uno a uno con SQL Server. Que está diseñada para satisfacer un conjunto diferente de requisitos que ha adaptado de forma exclusiva para las aplicaciones de nube (escala elástico, base de datos como un servicio para reducir los costos de mantenimiento y así sucesivamente). Para obtener más información, vea [Elija una opción de SQL Server de nube: base de datos de SQL Azure (PaaS) o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Replicación

Base de datos de SQL Azure proporciona integrada resistencia a errores de nivel de nodo. Escribe todos en una base de datos se duplican automáticamente en dos o más nodos de fondo a través de una técnica de confirmación de quórum. (Principal y secundario al menos una deben confirmar que la actividad se escribe en el registro de transacciones antes de la transacción se considera correcta y devuelve.) En el caso de error en un nodo, la base de datos pasa automáticamente a una de las réplicas secundarias. Hace que una interrupción de la conexión transitorias para las aplicaciones cliente. Por este motivo, todos los clientes de la base de datos de SQL Azure deben implementar algún tipo de control de conexión transitorias. Para obtener más información, vea [Reintentar orientación específica del servicio](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Administración de recursos

Cada base de datos, cuando cree, está configurado con un límite de tamaño superior. El tamaño máximo disponible actualmente es 1 TB (tamaño límites varían en función de su nivel de servicio, vea [niveles de servicio y los niveles de rendimiento de bases de datos de SQL Azure](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Cuando una base de datos alcanza el límite de tamaño superior, rechaza comandos adicionales de INSERCIÓN o actualización. (Consultas y eliminación de datos es posible).

Dentro de una base de datos de la base de datos de SQL Azure usa a una estructura para administrar los recursos. Sin embargo, en lugar de un controlador de tela utiliza una topología de anillo para detectar errores. Cada réplica en un clúster tiene dos vecinos y es responsable de detectar cuando se desplazan hacia abajo. Cuando una réplica deja de funcionar, las que la rodean desencadena un agente de la nueva configuración para volver a crearlo en otro equipo. Limitación de motor sirve para asegurarse de que un servidor lógico no usar demasiados recursos en un equipo o supere los límites de la máquina física.

###<a name="elasticity"></a>Elasticidad

Si la aplicación requiere más que el límite de la base de datos de 1 TB, debe implementar un enfoque de escalado. Escalar con la base de datos de SQL Azure dividiendo manualmente, también conocido como sharding, los datos a través de varias bases de datos SQL. Este enfoque de escalado proporciona la oportunidad de lograr crecimiento casi lineal costo con escala. Crecimiento elástico o capacidad a petición puede crecer con costes incrementales según sea necesario porque se cargarán bases de datos en función del tamaño real promedio usado por día, que no se basa en el tamaño máximo posible.

##<a name="sql-server-on-virtual-machines"></a>SQL Server en máquinas virtuales

Al instalar SQL Server (versión 2014 o posterior) en Azure máquinas virtuales de Windows, puede aprovechar las ventajas de las características de disponibilidad tradicional de SQL Server. Estas características incluyen grupos de disponibilidad AlwaysOn y reflejo de base de datos. Tenga en cuenta que máquinas virtuales de Azure, almacenamiento y redes características de operaciones diferentes de una infraestructura de TI no virtualizado en local. Una implementación correcta de una alta disponibilidad/recuperación solución (HA/DR) de SQL Server en Azure requiere que estas diferencias y diseñar su solución para adaptarse a ellos.

###<a name="high-availability-nodes-in-an-availability-set"></a>Nodos de alta disponibilidad en un conjunto de disponibilidad

Al implementar una solución de alta disponibilidad en Azure, puede usar la disponibilidad establecer en Azure para colocar los nodos de alta disponibilidad en dominios de error independiente y actualizar los dominios. Para que quede claro, el conjunto de disponibilidad es un concepto de Azure. Es un procedimiento recomendado que debe seguir para asegurarse de que las bases de datos son en realidad altamente disponibles, tanto si está utilizando grupos de disponibilidad AlwaysOn, reflejo de base de datos o algo más. Si no seguir este procedimiento recomendado, es posible que false suponiendo que el sistema es altamente disponible. Pero en realidad, los nodos pueden todos errores simultáneamente porque se producen colocar en el mismo dominio de errores en la región de Azure.

Esta recomendación no es aplicable como con el envío de registro. Como una característica de recuperación de desastres, debe asegurarse de que los servidores se están ejecutando en áreas independientes de Azure. Por definición, estas regiones son dominios de error independiente.

Para que Azure Cloud Services VM implementado a través del portal clásico estén en el mismo conjunto de disponibilidad, se debe implementar en el mismo servicio de nube. Máquinas virtuales implementadas a través del Administrador de recursos del Azure (el portal actual) no tienen esta limitación. Para portal clásica había implementado máquinas virtuales en servicio de nube de Azure, solo los nodos en el mismo servicio de nube pueden participar en el mismo conjunto de disponibilidad. Además, las máquinas virtuales de servicios de nube debe estar en la misma red virtual para garantizar que mantiene su direcciones IP, incluso después de resolución de problemas de servicio. Esto evita las interrupciones de actualización DNS.

###<a name="azure-only-high-availability-solutions"></a>Solo Azure: soluciones de alta disponibilidad

Puede tener una solución de alta disponibilidad para las bases de datos de SQL Server en Azure usando grupos de disponibilidad AlwaysOn o reflejo de base de datos.

El diagrama siguiente muestra la arquitectura de grupos de disponibilidad AlwaysOn ejecuta en Azure máquinas virtuales de Windows. Este diagrama se realizó desde el artículo sobre este tema, [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Grupos de disponibilidad AlwaysOn en Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

Automáticamente también pueden aprovisionar un grupos de disponibilidad AlwaysOn implementación-to-end en máquinas virtuales de Azure con la plantilla de AlwaysOn en el portal de Azure. Para obtener más información, vea [SQL Server AlwaysOn oferta en Galería de Portal de Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

En el diagrama siguiente muestra el uso de la base de datos reflejada en Azure máquinas virtuales de Windows. También se realizó desde el tema en profundidad [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Reflejo de base de datos en Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] Ambas arquitecturas requieren un controlador de dominio. Sin embargo, con la que coinciden con la base de datos, es posible usar certificados de servidor para eliminar la necesidad de un controlador de dominio.

##<a name="other-azure-platform-services"></a>Otros servicios de la plataforma Windows Azure

Aplicaciones que se basan en Azure beneficiarse de las capacidades de la plataforma para recuperar de errores locales. En algunos casos, puede realizar acciones específicas para aumentar la disponibilidad de su situación específica.

###<a name="service-bus"></a>Bus de servicio

Para reducir una interrupción temporal de Bus de servicio de Azure, considere la posibilidad de creación de una cola de cliente resistente. Temporalmente utiliza un mecanismo de almacenamiento alternativo, local para almacenar los mensajes que no se pueden agregar a la cola de Bus de servicio. La aplicación puede decidir cómo tratar los mensajes almacenados temporalmente después de que se restaura el servicio. Para obtener más información, vea [procedimientos recomendados para mejorar el rendimiento con Bus de servicio habían negociada mensajería](../service-bus-messaging/service-bus-performance-improvements.md) y [Bus de servicio (recuperación)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

###<a name="hdinsight"></a>HDInsight

Los datos que está asociada a HDInsight de Azure se almacenan en el almacenamiento de blobs de Windows Azure de forma predeterminada. Almacenamiento de Azure especifica las propiedades de alta disponibilidad y la duración de almacenamiento de blobs. El procesamiento de múltiples nodos que está asociada a Hadoop MapReduce trabajos se produce en una transitorias Hadoop distribuido archivo sistema (HDFS) que se aprovisiona cuando lo necesite HDInsight. Resultados de un trabajo MapReduce también se almacenan en el almacenamiento de blobs de Windows Azure, de forma predeterminada para que los datos procesados es resistentes y sigue altamente disponibles después de que el clúster Hadoop quedará con aprovisionamiento anulado. Para obtener más información, vea [HDInsight (recuperación)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Listas de comprobación de errores locales

###<a name="cloud-services"></a>Servicios de nube

  1. Revise la sección de servicios de nube de este documento.
  2. Configurar al menos dos instancias de cada rol.
  3. Conservar el estado de almacenamiento resistente, no se encuentran en las instancias de función.
  4. Controlar correctamente el evento de StatusCheck.
  5. Ajustar cambios relacionados en transacciones cuando sea posible.
  6. Compruebe que las tareas de la función de trabajo son idempotentes y reinicio.
  7. Continúe invocar operaciones hasta que realice correctamente.
  8. Considere la posibilidad de estrategias de ajuste automático.

###<a name="virtual-machines"></a>Máquinas virtuales de Windows

  1. Revise la sección de máquinas virtuales de este documento.
  2. No utilice la unidad D para el almacenamiento persistente.
  3. Agrupar máquinas en un nivel de servicio en un conjunto de disponibilidad.
  4. Configurar el equilibrio de carga y sondeos opcionales.

###<a name="storage"></a>Almacenamiento de información

  1. Revise la sección de almacenamiento de este documento.
  2. Use varias cuentas de almacenamiento cuando las cuotas supera los datos o el ancho de banda.

###<a name="sql-database"></a>Base de datos SQL

  1. Revise la sección de la base de datos SQL de este documento.
  2. Implementar una directiva de reintento para controlar los errores transitorios.
  3. Utilizar particiones/sharding como una estrategia de escalado.

###<a name="sql-server-on-virtual-machines"></a>SQL Server en máquinas virtuales

  1. Revise el servidor de SQL Server en máquinas virtuales de sección de este documento.
  2. Seguir las recomendaciones anteriores para máquinas virtuales.
  3. Usar características de alta disponibilidad de SQL Server, como AlwaysOn.

###<a name="service-bus"></a>Bus de servicio

  1. Revise la sección de Bus de servicio de este documento.
  2. Considerar la creación de una cola de cliente resistente como una copia de seguridad.

###<a name="hdinsight"></a>HDInsight

  1. Revise la sección HDInsight de este documento.
  2. No disponibilidad adicional es necesario para errores locales.

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie centrada en [orientación técnica de la resistencia de Azure](./resiliency-technical-guidance.md). El siguiente artículo de esta serie es la [recuperación de una interrupción del servicio de toda la región](./resiliency-technical-guidance-recovery-loss-azure-region.md).
