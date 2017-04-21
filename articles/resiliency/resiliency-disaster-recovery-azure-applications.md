<properties
   pageTitle="Recuperación de aplicaciones de Azure | Microsoft Azure"
   description="Información general técnica e información detallada sobre cómo diseñar aplicaciones para recuperación en Microsoft Azure."
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Recuperación de las aplicaciones integradas en Microsoft Azure

Considerando alta disponibilidad sobre la administración de error temporal, recuperación (DR) es la grave pérdida de funcionalidad de la aplicación. Por ejemplo, considere el escenario donde una región deja de funcionar. En este caso, debe tener un plan para ejecutar la aplicación u obtener acceso a los datos fuera de la región de Azure. Ejecución de este plan implica personas, procesos y aplicaciones auxiliares que permitir que el sistema de la función. Los propietarios de empresa y tecnología que definen el modo de funcionamiento del sistema para un desastre también determinan el nivel de funcionalidad para el servicio durante un desastre. El nivel de funcionalidad puede tardar unos formularios: disponible completamente, parcialmente disponible (funcionalidad de degradado o retrasar el procesamiento), o completamente disponible.

##<a name="azure-disaster-recovery-features"></a>Características de recuperación de desastres de Azure

Al igual que con las consideraciones de disponibilidad, Azure tiene [orientación técnica de la resistencia](./resiliency-technical-guidance.md) diseñada para ser compatible con la recuperación. También es una relación entre algunas de las características de disponibilidad de Azure y recuperación de desastres. Por ejemplo, la administración de roles a través de dominios de error aumenta la disponibilidad de una aplicación. Sin que la administración, un error de hardware no controlada se convierta en un escenario de "desastres". Para la aplicación correcta de las características de disponibilidad y estrategias es una parte importante de corrección desastres la aplicación. Sin embargo, este artículo va más allá de los problemas de disponibilidad general a eventos de desastres más graves (y menos).

##<a name="multiple-datacenter-regions"></a>Varias regiones del centro de datos

Azure mantiene centros de datos en muchas regiones del mundo. Esta infraestructura admite varios escenarios de recuperación de desastres, como proporcionadas por el sistema geo-a la replicación de almacenamiento de Azure regiones secundarias. También significa que puede obtener acceso fácilmente y rentable implementar un servicio de nube a varias ubicaciones del mundo. Compare esto con el costo y la dificultad de la ejecución de sus propios centros de datos en varias áreas. Implementar los datos y servicios a varias áreas le ayuda a proteger su aplicación de interrupciones importantes en una sola región.

##<a name="azure-traffic-manager"></a>Administrador de tráfico de Azure

Cuando se produce un error específico de la región, debe redirigir el tráfico a servicios o implementaciones en otra región. Puede hacer esta ruta manualmente, pero resulta más eficaz utilizar un proceso automatizado. Administrador de tráfico Azure está diseñado para esta tarea. Puede usarlo para administrar automáticamente la migración tras error del tráfico de usuario para otra región en caso de que se produce un error en el área principal. Dado que administración del tráfico es una parte importante de la estrategia global, es importante entender los conceptos básicos del Administrador de tráfico.

En el diagrama siguiente, los usuarios conectarse a una dirección URL que ha especificado para el Administrador de tráfico (__http://myATMURL.trafficmanager.net__) y que resúmenes de las direcciones URL de sitio real (__http://app1URL.cloudapp.net__ y __http://app2URL.cloudapp.net__). En función de cómo configurar los criterios para cuando para enrutar usuarios, los usuarios se enviarán al sitio real correcto cuando exige la directiva. Las opciones de directiva son turnos, el rendimiento o la migración tras error. Por motivos de este artículo, se estará interesados con la opción de la migración tras error.

![Enrutamiento mediante el Administrador de tráfico de Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Para configurar el Administrador de tráfico, proporcionar un nuevo prefijo de tráfico administrador DNS. Esto es el prefijo de dirección URL que deberá proporcionar a los usuarios acceso al servicio. Administrador de tráfico resume ahora un nivel hacia arriba y no en el nivel de la región de equilibrio de carga. El Administrador de tráfico de DNS se asigna a un CNAME para implementaciones que administra.

En el Administrador de tráfico, especifique la prioridad de las distribuciones de usuarios redirigirá a cuando se produzca el error. Administrador de tráfico supervisa los extremos de las implementaciones y notas cuando se produce un error en la implementación principal. En el error, el Administrador de tráfico analiza la lista de prioridades de implementaciones y dirige a los usuarios a la siguiente en la lista.

Aunque el Administrador de tráfico decida dónde ir en un error, puede decidir si su dominio de migración tras error está activo o inactivo mientras no está en modo de migración tras error. Esta funcionalidad tiene nada que ver con el administrador del tráfico de Azure. El Administrador de tráfico detecta un error en el sitio primario y se pone en el sitio de migración tras error. Administrador de tráfico renueva independientemente de si ese sitio está actualmente atender a los usuarios o no.

Para obtener más información sobre el funcionamiento del Administrador de tráfico de Azure, consulte:

 * [Información general del Administrador de tráfico](../traffic-manager/traffic-manager-overview.md)
 * [Configurar método de enrutamiento de migración tras error](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Escenarios de desastres Azure

Las secciones siguientes cubren diferentes tipos de situaciones de desastres. Interrupciones del servicio de toda la región no son la causa sola de errores de toda la aplicación. También pueden provocar errores de diseño o administración deficiente interrupciones. Es importante tener en cuenta las posibles causas de un error durante el diseño y la fases de pruebas de su plan de recuperación. Un buen plan aprovecha características Azure y les aumenta con estrategias específicos de la aplicación. La respuesta elegida dependen de la importancia de la aplicación, el objetivo de punto de recuperación (RPO) y el objetivo de tiempo de recuperación (RTO).

###<a name="application-failure"></a>Error de la aplicación

Administrador de tráfico Azure controla automáticamente errores como resultado del software subyacente de hardware o sistema operativo en la máquina virtual de host. Azure crea una nueva instancia de la función en un servidor en funcionamiento y lo agrega a la rotación de equilibrador de carga. Si el número de instancias de la función es mayor que uno, Azure turnos de procesamiento a las demás instancias de rol ejecución mientras se reemplazan el nodo del error.

Hay errores de aplicación graves que se producen independientemente de los errores de hardware o sistema operativo. La aplicación puede producir un error debido a las excepciones graves causadas por lógica incorrecta o problemas de integridad de datos. Debe incorporar suficiente telemetría en el código para que un sistema de supervisión puede detectar condiciones de error y notificar a un administrador de la aplicación. Un administrador que tenga conocimiento de los procesos de recuperación de desastres puede tomar una decisión invocar a un proceso de migración tras error. Como alternativa, un administrador puede aceptar simplemente una interrupción de disponibilidad para resolver los errores críticos.

###<a name="data-corruption"></a>Daños en los datos

Azure almacena automáticamente los datos de la base de datos de SQL Azure y Azure almacenamiento tres veces forma redundante dentro de los dominios de error diferente en la misma región. Si utiliza la replicación de geo, los datos se almacenan tres veces adicionales en una región diferente. Sin embargo, si los usuarios o la aplicación de daña los datos en la copia primaria, los datos rápidamente aplica a las demás copias. Desgraciadamente, el resultado tres copias de datos dañadas.

Para administrar posibles daños en los datos, tiene dos opciones. En primer lugar, puede administrar una estrategia de copia de seguridad personalizada. Puede almacenar las copias de seguridad en Azure o local, dependiendo de los requisitos de la empresa o las regulaciones. Otra opción es usar la nueva opción de restauración de punto en el tiempo de recuperación de una base de datos SQL. Para obtener más información, vea la sección de [estrategias de datos para la recuperación](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Interrupción de la red

Cuando no pueden acceder partes de la red de Azure, no podrá obtener acceso a la aplicación o los datos. Si una o varias instancias de la función no están disponibles debido a problemas de red, Azure usa las instancias restantes disponibles de la aplicación. Si la aplicación no puede acceder a sus datos debido a una interrupción de red Azure, potencialmente puede ejecutar en modo degradado localmente mediante datos almacenados en caché. Debe diseñar la estrategia de recuperación para ejecutar en modo degradado en la aplicación. Para algunas aplicaciones, no se puede resultar práctico.

Otra opción es almacenar datos en una ubicación alternativa hasta que se restaura la conectividad. Si modo degradado no es una opción, el resto de opciones es el tiempo de inactividad de la aplicación o migración tras error en una región alternativa. El diseño de una aplicación que se ejecuta en modo degradado es tanto una decisión empresarial como una técnica. Esto se describe más adelante en la sección de [degradado de la funcionalidad de la aplicación](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Error de un servicio dependiente

Azure proporciona muchos de los servicios que pueden experimentar tiempos de inactividad periódicos. Considere la posibilidad de [Azure Redis caché](https://azure.microsoft.com/services/cache/) como ejemplo. Este servicio de múltiples arrendatario ofrece capacidades de caché a la aplicación. Es importante tener en cuenta lo que sucede en la aplicación si el servicio dependiente no está disponible. En muchas formas, este escenario es similar al escenario de interrupción de red. Sin embargo, teniendo en cuenta cada servicio por separado da como resultado posibles mejoras en el plan general.

Caché de Azure Redis proporciona almacenamiento en caché para la aplicación desde la implementación de servicio de nube, que ofrece ventajas de recuperación de desastres. En primer lugar, ahora se ejecuta el servicio en las funciones que son locales a la implementación. Por lo tanto, es mejor puede supervisar y administrar el estado de la memoria caché como parte de los procesos de administración global para el servicio de nube. Este tipo de almacenamiento en caché también expone características nuevas. Una de las nuevas características es alta disponibilidad de los datos almacenados en caché. Esto le permite conservar los datos almacenados en caché si se produce un error en un único nodo manteniendo copias duplicadas en otros nodos.

Tenga en cuenta que alta disponibilidad disminuye el rendimiento y aumenta la latencia debido a la actualización de la copia secundaria escribe. También se duplica la cantidad de memoria que se usa para cada elemento, así que planear. Este ejemplo específico muestra que cada servicio dependiente podría tener capacidades que mejoran la resistencia a errores graves y la disponibilidad general.

Con cada servicio dependiente, debe comprender las implicaciones de una interrupción del servicio. En el ejemplo de almacenamiento en caché, es posible tener acceso a los datos directamente desde una base de datos hasta que se restaura la memoria caché. Sería un modo degradado en términos de rendimiento, pero desea proporcionar la funcionalidad completa con respecto a los datos.

###<a name="region-wide-service-disruption"></a>Interrupción del servicio de toda la región

Los errores anteriores han sido principalmente errores que pueden administrarse en la misma región de Azure. Sin embargo, también debe preparar la posibilidad de que hay una interrupción del servicio de toda la región. Si se produce una interrupción del servicio de toda la región, no están disponibles las copias de los datos redundantes localmente. Si ha habilitado la replicación geo, hay tres copias adicionales de los BLOB y tablas en una región distinta. Si Microsoft declara la región perdida, Azure reasigna todas las entradas DNS a la región geográfica replicada.

>[AZURE.NOTE] Tenga en cuenta que no tiene ningún control sobre este proceso y se producirá sólo de interrupción del servicio de toda la región. Por este motivo, debe confiar en otras estrategias de copia de seguridad específicos de la aplicación para obtener el máximo nivel de disponibilidad. Para obtener más información, vea la sección de [estrategias de datos para la recuperación](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Interrupción del servicio de Azure todo

En la planificación de desastres, debe tener en cuenta todo el intervalo de posibles desastres. Una de las interrupciones del servicio más graves sería simultáneamente todas las áreas de Azure. Al igual que con otras interrupciones de servicio, puede decidir que deberá realizar el riesgo de inactividad temporal de ese evento. Interrupciones del servicio generalizado que abarcan regiones deberían ser mucho menos habituales que las interrupciones del servicio aislado que implican servicios dependientes o regiones único.

Sin embargo, para algunas aplicaciones críticas, puede decidir que debe ser un plan de copia de seguridad para este escenario. El plan para este evento puede incluir experimentando problemas a los servicios en una [nube alternativo](#alternative-cloud) o una [solución en la nube y local híbrido](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Funcionalidad de la aplicación degradado

Una aplicación bien diseñada normalmente utiliza una colección de módulos que se comuniquen aunque la implementación de patrones de intercambio de información flexible. Una aplicación de DR descriptivo requiere separación de las tareas en el nivel de módulo. Esto es para evitar la interrupción de un servicio dependiente bloquee toda la aplicación. Por ejemplo, considere una aplicación web de comercio y de la compañía. Los siguientes módulos podrían constituyen la aplicación:

 * __Catálogo de productos__ permite a los usuarios examinar los productos.
 * __Carro de la compra__ permite a los usuarios agregar o quitar productos de carro de la compra.
 * __Estado del pedido__ muestra el estado de envío de los pedidos de usuario.
 * __Envío de pedido__ finaliza la sesión de compra mediante el envío de la orden de pago.
 * __Procesamiento de__ valida el orden de la integridad de datos y realiza una comprobación de disponibilidad de cantidad.

Cuando un dependiente de un módulo de esta aplicación deja de funcionar, ¿cómo el módulo funciona hasta esa parte recupera? Un sistema bien diseñado e implementa límites de aislamiento a través de separación de las tareas en tiempo de diseño y en tiempo de ejecución. Puede clasificar cada error recuperable y no recuperable. Errores no recuperables tardarán hacia abajo el módulo, pero puede mitigar un error recuperable a través de alternativas. Como se describe en la sección de alta disponibilidad, tratamiento de errores y tomar medidas alternativas para ocultar algunos problemas de los usuarios. Durante una interrupción del servicio más grave, la aplicación está completamente disponible. Sin embargo, una tercera opción es continuar el mantenimiento de los usuarios en modo degradado.

Por ejemplo, si la base de datos para hospedar pedidos se desconecta, el módulo de procesamiento pierde su capacidad para procesar las transacciones de ventas. Según la arquitectura, puede ser difícil o imposible de los elementos de procesamiento y envío de orden de la aplicación para continuar. Si la aplicación no está diseñada para controlar este escenario, puede desconectarse toda la aplicación.

Sin embargo, en esta misma situación, es posible que los datos de producto se almacenan en una ubicación diferente. En ese caso, el módulo de catálogo de productos aún puede utilizarse para ver los productos. En modo degradado, la aplicación continúa esté disponible para los usuarios de la funcionalidad disponible como ver el catálogo de productos. Otros elementos de la aplicación, sin embargo, no están disponibles, como las consultas de ordenación o inventario.

Otra variante de modo degradado se centra en el rendimiento en lugar de funciones. Por ejemplo, considere la posibilidad de un escenario donde el catálogo de productos en caché a través de Azure Redis caché. Si el almacenamiento en caché no está disponible, la aplicación puede ir directamente a almacenamiento de servidor para recuperar información del catálogo de productos. Pero este acceso podría ser menor que la versión en caché. Por este motivo, el rendimiento de la aplicación está degradado hasta que el servicio de caché se restaura completamente.

Decidir cuánto de una aplicación seguirá función en modo degradado es una decisión empresarial y toma de decisiones técnica. La aplicación también debe decidir cómo informar a los usuarios de los problemas temporales. En este ejemplo, la aplicación podría permitir la visualización de productos e incluso agréguelo al carro de la compra. Sin embargo, cuando el usuario intenta realizar una compra, la aplicación notifica al usuario que el módulo de ventas está temporalmente inaccesible. No es ideal para el cliente, pero puede evitar que una interrupción del servicio de toda la aplicación.

##<a name="data-strategies-for-disaster-recovery"></a>Estrategias de datos para la recuperación

Controlar los datos correctamente es el área más difícil obtener derecho en cualquier plan de recuperación. Restauración de datos también es la parte del proceso de recuperación normalmente tarda más tiempo. Diferentes opciones en los modos de degradación resultado retos de recuperación de datos de error y coherencia después de un error.

Uno de los factores es necesario restaurar o mantener una copia de los datos de la aplicación. Estos datos que usa para referencia y efectos de transacciones en un sitio secundario. Una configuración de local requiere un proceso de planificación caro y largo para implementar una estrategia de recuperación varias regiones. Para su comodidad, la mayoría de los proveedores de nube, incluyendo Azure, fácilmente permiten la implementación de aplicaciones para varias regiones. Estas regiones se distribuyen geográfica de tal forma que interrupción del servicio de varias regiones debe ser muy poco frecuente. La estrategia de administración de datos en todas las regiones es uno de los factores de colaboradores para el éxito de cualquier plan de recuperación.

Las secciones siguientes describen técnicas de recuperación de desastres relacionados con las copias de seguridad de datos, datos de referencia y datos de transacciones.

###<a name="backup-and-restore"></a>Copia de seguridad y restauración

Copias de seguridad periódicas de datos de aplicación pueden admitir algunos escenarios de recuperación de desastres. Recursos de almacenamiento diferentes requieren distintas técnicas.

Para los niveles de Basic, estándar y Premium base de datos de SQL, puede aprovechar las ventajas de restauración de punto en el tiempo para recuperar la base de datos. Para obtener más información, vea [información general: empresa continuidad y base de datos de recuperación de base de datos SQL de nube](../sql-database/sql-database-business-continuity.md). Otra opción es utilizar la replicación de Geo Active para base de datos de SQL. Esto aplica automáticamente los cambios de base de datos a bases de datos secundarios en la misma región Azure o incluso en una región distinta de Azure. Esto proporciona una alternativa posible que algunas de las técnicas de sincronización de datos más manuales presentadas en este artículo. Para obtener más información, vea [información general: base de datos activa Geo replicación de SQL](../sql-database/sql-database-geo-replication-overview.md).

También puede utilizar un enfoque más manual para copia de seguridad y restauración. Use el comando Copiar de la base de datos para crear una copia de la base de datos. Debe usar este comando para obtener una copia de seguridad con coherencia transacciones. También puede usar el servicio de importación o exportación de la base de datos de SQL Azure. Esto es compatible con bases de datos de exportación a archivos de MOCHILA que se almacenan en el almacenamiento de blobs de Windows Azure.

La redundancia integrada de almacenamiento de Azure crea dos réplicas del archivo de copia de seguridad de la misma región. Sin embargo, la frecuencia de ejecución del proceso de copia de seguridad determina el RPO, que es la cantidad de datos que se pueden perder en escenarios de desastres. Por ejemplo, suponga que realiza una copia de seguridad en la parte superior de la hora y dos minutos antes de la parte superior de la hora se produce un desastre. Perderá 58 minutos de datos que se hicieron después de realiza la última copia de seguridad. Además, para proteger una interrupción del servicio de toda la región, debe copiar los archivos de MOCHILA a una región alternativo. A continuación, tiene la opción de restaurar las copias de seguridad de la región alternativa. Para obtener más detalles, consulte [información general: empresa continuidad y base de datos de recuperación de base de datos SQL de nube](../sql-database/sql-database-business-continuity.md).

Para el almacenamiento de Azure, puede desarrollar sus propios procesos de copia de seguridad personalizada o usar una de muchas herramientas de copia de seguridad de terceros. Tenga en cuenta que la mayoría de los diseños de aplicaciones complejidades adicionales que hacen referencia a recursos de almacenamiento entre sí. Por ejemplo, considere la posibilidad de una base de datos SQL que tiene una columna que se vincula a un blob en el almacenamiento de Azure. Si las copias de seguridad no se producen de forma simultánea, la base de datos que tenga el puntero a un blob que no se copia antes del error. La aplicación o el plan de recuperación debe implementar procesos para controlar esta incoherencia después de una recuperación.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Modelo de datos de referencia para la recuperación

Datos de referencia son de solo lectura que admite la funcionalidad de la aplicación. Normalmente no cambia con frecuencia. Aunque la copia de seguridad y restauración es un método para controlar las interrupciones del servicio de toda la región, el RTO es relativamente largo. Cuando se implementa la aplicación a una región secundaria, algunas estrategias pueden mejorar el RTO para los datos de referencia.

Dado que hacen referencia a cambios en los datos con poca frecuencia, puede mejorar el RTO al mantener una copia permanente de los datos de referencia en la región secundario. Esto elimina el tiempo necesario para restaurar las copias de seguridad en caso de desastre. Para satisfacer los requisitos de recuperación de desastres varias regiones, debe implementar la aplicación y los datos de referencia juntos en varias áreas. Como se mencionó en el [modelo de datos de referencia de alta disponibilidad](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), puede implementar datos de referencia para la función, para almacenamiento externo, o a una combinación de ambos.

El modelo de implementación de datos de referencia dentro de los nodos de cálculo implícitamente cumple los requisitos de recuperación de desastres. Implementación de datos de referencia para la base de datos SQL requiere implementar una copia de los datos de referencia para cada región. La misma estrategia se aplica al almacenamiento de Azure. Debe implementar una copia de los datos de referencia que se almacenan en el almacenamiento de Azure a las regiones primaria y secundarias.

![Publicación de datos de referencia para regiones primaria y secundarias](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Debe implementar sus propias rutinas de copia de seguridad específicos de la aplicación para todos los datos, incluidos los datos de referencia. Replicar geo copias en todas las regiones se utilizan sólo en una interrupción del servicio de toda la región. Para evitar que el tiempo de inactividad ampliado, implementar las partes esenciales de datos de la aplicación en la región secundaria. Para obtener un ejemplo de esta topología, consulte el [modelo activo y pasivo](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Modelo de datos de transacciones para recuperación

Implementación de una estrategia de modo de desastres completamente funcional requiere replicación asincrónica de los datos de transacciones en la región secundario. Las ventanas de tiempo práctica en el que se puede producir la replicación determinará las características RPO de la aplicación. Aún puede recuperar los datos que se ha perdido desde el área principal durante la ventana de replicación. También puede combinar con la región secundaria más tarde.

Los siguientes ejemplos de arquitectura proporcionan algunas ideas sobre diferentes formas de controlar los datos de transacciones en un escenario de migración tras error. Es importante que tenga en cuenta que estos ejemplos no exhaustivos. Por ejemplo, ubicaciones de almacenamiento intermedio como colas podrían reemplazarse con la base de datos de SQL Azure. Las colas a sí mismos posible almacenamiento de Azure o Bus de servicio de Azure colas (vea [colas Azure y Bus de servicio--en comparación y frente](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Destinos de almacenamiento de servidor también pueden variar, como tablas de Azure en lugar de la base de datos de SQL. Además, se pueden insertar roles de trabajo como intermediarios en varios pasos. Lo más importante es no para emular estas arquitecturas exactamente, pero para tener en cuenta diversas alternativas en la recuperación de datos de transacciones y módulos relacionados.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Replicación de datos de transacciones en preparación para la recuperación

Considere la posibilidad de una aplicación que utiliza colas de almacenamiento de Azure para almacenar datos de transacciones. Esto permite funciones de trabajo procesar los datos de transacciones a la base de datos en una arquitectura desacoplada. Es necesario que las transacciones que utilice alguna forma de caché temporal si los roles front-end requieren la consulta inmediata de los datos. Según el nivel de tolerancia de pérdida de datos, puede que desee replicar las colas, la base de datos o todos los recursos de almacenamiento. Con solo replicación de base de datos, si la región principal se interrumpe, aún puede recuperar los datos en las colas cuando vuelva a la región principal.

El siguiente diagrama muestra una arquitectura donde se sincroniza la base de datos en todas las regiones.

![Replicación de datos de transacciones en preparación para la recuperación](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

El reto más grande para implementar esta arquitectura es la estrategia de replicación entre regiones. El servicio de sincronización de datos de SQL Azure permite a este tipo de replicación. Sin embargo, el servicio aún está en vista previa y no se recomienda para entornos de producción. Para obtener más información, vea [información general: empresa continuidad y base de datos de recuperación de base de datos SQL de nube](../sql-database/sql-database-business-continuity.md). Para las aplicaciones de producción, debe invertir en una solución de terceros o crear su propia lógica de replicación en código. Según la arquitectura, la replicación podría ser bidireccional, que también es más complejo.

Una implementación potencial podría ser el uso de la cola intermedia en el ejemplo anterior. La función de trabajo que procesa los datos en el destino de almacenamiento final podría realizar el cambio en el área principal y el secundario región. No son tareas triviales y una guía completa para código de replicación está fuera del ámbito de este artículo. El punto importante es que una gran cantidad de tiempo y pruebas deben centrarse en cómo replicar los datos a la región secundaria. Procesamiento y pruebas adicionales pueden ayudar a asegurarse de que los procesos de migración y recuperación controlen correctamente datos posibles incoherencias o transacciones duplicadas.

>[AZURE.NOTE] La mayoría de este artículo se centra en plataforma como servicio (PaaS). Sin embargo, opciones adicionales de replicación y disponibilidad de aplicaciones híbridas usar máquinas virtuales de Azure. Estas aplicaciones híbridas usan infraestructura como servicio (IaaS) para hospedar SQL Server en máquinas virtuales de Azure. Esto permite enfoques tradicionales de disponibilidad de SQL Server, como registro de envío o grupos de disponibilidad AlwaysOn. Algunas técnicas, como AlwaysOn, trabajar solo entre instancias de SQL Server local y máquinas virtuales de Windows Azure. Para obtener más información, vea [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Modo de aplicación degradado para la captura de transacción

Considere la posibilidad de una segunda arquitectura que funciona en modo degradado. La aplicación en la región secundaria desactiva todas las funciones, como informes de inteligencia empresarial (BI) o purga colas. Acepta solo los tipos más importantes de transacciones flujos de trabajo, según los requisitos empresariales. El sistema captura las transacciones y escribe en colas. Puede posponer el sistema de procesamiento de los datos durante la fase inicial de la interrupción del servicio. Si el sistema en el área principal se reactiva dentro de la ventana de tiempo esperado, los roles de trabajo en el área principal pueden consumir las colas. Este proceso elimina la necesidad de combinar la base de datos. Si la interrupción del servicio principal región va más allá de la ventana tolerable, puede iniciar la aplicación de procesamiento de las colas.

En este escenario, la base de datos secundario contiene datos transacciones incrementales que deben combinarse después de que se vuelva a activar el principal. El siguiente diagrama muestra esta estrategia para almacenar temporalmente datos de transacciones hasta que se restaura el área principal.

![Modo de aplicación degradado para la captura de transacción](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Para obtener más información sobre las técnicas de administración de datos para aplicaciones de Azure flexibles, consulte [a prueba de errores: pautas para arquitecturas de nube flexibles](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Topologías de implementación de recuperación

Debe preparar aplicaciones críticas la posibilidad de una interrupción del servicio de toda la región. Para ello, al incorporar una estrategia de implementación de varias regiones en la planificación de operaciones.

Región de múltiples implementaciones pueden implicar procesos de profesionales de TI para publicar los datos de aplicación y la referencia en la región secundaria después de un desastre. Si la aplicación requiere instantánea migración tras error, el proceso de implementación puede implicar un programa de instalación activo y pasivo o un activos. Este tipo de implementación tiene instancias existentes de la aplicación que se ejecuta en la región alternativa. Una herramienta de enrutamiento como el administrador del tráfico de Azure proporciona servicios de equilibrio de carga en el nivel DNS. Puede detectar interrupciones del servicio y redirigir a los usuarios de diferentes regiones cuando sea necesario.

Parte de una recuperación de Azure correcta es arquitectura de que la recuperación en la solución desde el principio. La nube proporciona opciones adicionales de recuperación de errores durante un desastre que no están disponibles en un proveedor de hospedaje tradicional. Más concretamente, puede rápidamente y dinámicamente asignar recursos a una región distinta. Por lo tanto, no paga mucho para recursos inactivos mientras espera para que un error que se produzca.

Las secciones siguientes cubren diferentes topologías de implementación de recuperación. Normalmente, hay un equilibrio de mayor costo o complejidad para disponibilidad adicional.

###<a name="single-region-deployment"></a>Implementación de región únicos

Implementación de una sola región realmente no es una topología de recuperación de desastres, pero está pensada para contraste con las otras arquitecturas. Región solo implementaciones son comunes para las aplicaciones de Azure. Sin embargo, este tipo de implementación no es grave competir por un plan de recuperación.

El siguiente diagrama muestra una aplicación que se ejecuta en una sola región de Azure. Azure Administrador de tráfico y el uso de dominios de error y actualización aumentan la disponibilidad de la aplicación dentro de la región.

![Implementación de región únicos](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Aquí, es evidente que la base de datos es un punto único de error. Aunque Azure duplica los datos a través de dominios de error diferente a réplicas internas, todo esto se produce en la misma región. La aplicación no puede resistir un error grave. Si la región se desconecta, todos los dominios de errores de avanzar, incluidas todas las instancias de servicio y recursos de almacenamiento.

Para todos excepto las aplicaciones menos críticas, debe diseñar un plan de implementación de las aplicaciones en diferentes regiones. También debe considerar RTO y restricciones considerar usar qué topología de implementación de costo.

Echemos un vistazo ahora a patrones específicos para admitir migración tras error a través de diferentes regiones. Todos estos ejemplos usan dos regiones para describir el proceso.

###<a name="redeployment-to-a-secondary-azure-region"></a>Redistribución a una región de Azure secundaria

En el patrón de redistribución a una región secundaria, solo la región principal tiene aplicaciones y bases de datos. La región secundaria no está configurada para la recuperación automática. Así que cuando se produce un desastre, debe girar todas las partes del servicio en la nueva región. Esto incluye al cargar un servicio de nube a Azure, implementación del servicio de nube, la restauración de los datos y cambiar el DNS para redirigir el tráfico.

Aunque es más asequible de las opciones de varias regiones, tiene las características RTO peores. En este modelo, se almacenan las copias de seguridad de paquete y base de datos de servicio en modo local o en la instancia de almacenamiento de blobs de Windows Azure de la región secundaria. Sin embargo, debe implementar un nuevo servicio y restaurar los datos antes de que se reanude la operación. Aunque totalmente automatizar la transferencia de datos de almacenamiento de copia de seguridad, poner en marcha el nuevo entorno de base de datos consume una gran cantidad de tiempo. Mover los datos desde el almacenamiento en disco de copia de seguridad a la base de datos vacía en la región secundaria es el elemento más caro del proceso de restauración. Debe hacer esto, sin embargo, para poner la nueva base de datos a un estado de funcionamiento debido a que no replicar.

Es el mejor método almacenar los paquetes de servicio de almacenamiento de blobs de la región secundario. Esto elimina la necesidad de cargar el paquete en Azure, que es lo que ocurre cuando se implementa desde un equipo de desarrollo local. Rápidamente puede implementar los paquetes de servicio de almacenamiento de blobs a un nuevo servicio de nube mediante secuencias de comandos de PowerShell.

Esta opción es viable sólo para aplicaciones no críticas que pueden tolerar un RTO elevado. Por ejemplo, esto podría funcionar para una aplicación que puede ser hacia abajo para varias horas, pero debe estar ejecutándose dentro de 24 horas.

![Redistribución a una región de Azure secundaria](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Activo y pasivo

El modelo activo pasivo es la opción favor de muchas empresas. Este patrón proporciona mejoras para el RTO con un pequeño aumento de costo en el patrón de redistribución.
En este escenario, vuelva a hay principal y un área de Azure secundario. Todo el tráfico va a la implementación activada en el área principal. La región secundaria mejor preparada para recuperación porque la base de datos se ejecuta en ambas regiones. Además, un mecanismo de sincronización está en su lugar entre ellas. Este enfoque en espera puede implicar dos variaciones: un enfoque de base de datos o una implementación completa en la región secundaria.

####<a name="database-only"></a>Sólo base de datos

En la primera del patrón activo y pasivo, solo la región principal tiene una aplicación de servicio de nube implementada. Sin embargo, a diferencia de la trama de redistribución, ambas regiones se sincronizan con el contenido de la base de datos. (Para obtener más información, consulte la sección sobre [modelo de datos de transacciones para recuperación](#transactional-data-pattern-for-disaster-recovery)). Cuando se produce un desastre, hay menos requisitos de activación. Inicie la aplicación en la región secundaria, cambie las cadenas de conexión a la nueva base de datos y cambiar las entradas DNS para redirigir el tráfico.

Al igual que el modelo de redistribución, debe ya ha almacenado los paquetes de servicio en el almacenamiento de blobs de Windows Azure en la región secundario para una implementación más rápida. A diferencia de la trama de redistribución evitan la mayor parte de la carga que requiere la operación de restauración de la base de datos. La base de datos está listo y en funcionamiento. Esto ahorra bastante tiempo, haciendo una trama de DR asequible. También es el patrón de DR más popular.

![Activo y pasivo, solo la base de datos](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Réplica completa

En la segunda del patrón activo y pasivo, la región principal y el secundario región tienen una implementación completa. Esta implementación incluye los servicios de nube y una base de datos sincronizado. Sin embargo, sólo el área principal que controla las solicitudes de red de los usuarios. La región secundaria activo solo cuando la región principal experimenta una interrupción del servicio. En ese caso, todas las nuevas solicitudes de red se redirige a la región secundaria. Administrador de tráfico Azure puede administrar esta migración tras error automáticamente.

Migración tras error se produce más rápido que la variación de base de datos porque ya se han implementado los servicios. Este patrón proporciona un RTO muy bajo. La región de failover secundaria debe estar lista para ir inmediatamente después de error de la región principal.

Junto con un tiempo de respuesta más rápido, este patrón tiene la ventaja de asignación previa e implementar servicios de copia de seguridad. No tiene que preocuparse por una región no tiene el espacio que desea asignar las nuevas instancias de un desastre. Esto es importante si su región de Azure secundario está cerca de la capacidad. No hay ninguna garantía (contrato de nivel de servicio) al instante podrán implementar un número de nuevos servicios de nube de cualquier región.

Para el tiempo de respuesta más rápido con este modelo, debe tener escala similar (número de instancias de rol) en las regiones primaria y secundarias. A pesar de las ventajas, pagando por instancias de cálculo sin usar es costosa y no puede ser la opción más prudente financiera. Por este motivo, es más común usar una versión ligeramente reducida de servicios en la nube en la región secundaria. A continuación, puede conmutar rápidamente y escalar la implementación secundaria si es necesario. Debe automatizar el proceso de migración tras error para después de la región principal inaccesible, activar instancias adicionales, dependiendo de la carga. Esto puede implicar el uso de un mecanismo de ajuste automático como [establece la escala de la máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

En el diagrama siguiente muestra el modelo donde las regiones principales y secundarias contienen un servicio de nube totalmente implementado en un modelo de activos y pasivos.

![Réplica completa activo y pasivo](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Activo activo

Ahora está probablemente averiguar la evolución de los patrones: reducir el RTO aumenta los costos y la complejidad. La solución activo activo realmente saltos de esta tendencia con respecto al costo.

En un modelo activo activo, los servicios de nube y la base de datos se implementan totalmente en ambas regiones. A diferencia del modelo activo y pasivo, ambas regiones reciban tráfico de usuario. Esta opción produce el tiempo de recuperación más rápido. Los servicios ya se escalan para manejar una parte de la carga en cada región. DNS ya está habilitado para usar la región secundaria. Hay complejidad adicional para determinar cómo enrutar los usuarios a su región. Programación de turnos posible. Es más probable que algunos usuarios usaría una región específica donde reside la copia principal de sus datos.

En caso de migración tras error, simplemente deshabilitar DNS para el área principal. Esto enruta todo el tráfico a la región secundaria.

Incluso en este modelo, hay algunas variaciones. Por ejemplo, el siguiente diagrama muestra un modelo en el área principal posee la copia maestra de la base de datos. Los servicios de nube de ambas regiones escriben en dicha base de datos principal. La implementación secundaria puede leer de la base de datos replicada o principal. Replicación en este ejemplo se pasa una forma.

![Activo activo](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Hay una desventaja a la arquitectura de activo activo en el diagrama anterior. La segunda región debe tener acceso a la base de datos de la primera región porque la copia maestra resida allí. Rendimiento cae considerablemente cuando tiene acceso a datos desde fuera de una región. En la base de datos entre región llamadas, debe tener en cuenta algún tipo de procesamiento por lotes estrategia para mejorar el rendimiento de estas llamadas. Para obtener más información, vea [cómo usar lotes para mejorar el rendimiento de la aplicación de base de datos de SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Una arquitectura alternativa puede implicar cada región tener acceso directamente a su propia base de datos. En este modelo, es necesario algún tipo de replicación bidireccional para sincronizar las bases de datos de cada región.

En el modelo activo activo, no es posible que tenga tantas instancias del área principal de como lo haría en el modelo activo y pasivo. Si tiene 10 instancias en el área principal en una arquitectura de activo y pasivo, es posible que tenga sólo 5 en cada región en una arquitectura activo activo. Ahora, ambas regiones compartan la carga. Esto podría estar un ahorro de costo sobre el modelo activo pasivo aunque tenga un caliente en espera en la región pasiva 10 instancias esperando migración tras error.

Tenga en cuenta que hasta que se restaura la región principal, el secundario región podría recibir un sobrevoltaje rápida de nuevos usuarios. Si hay 10.000 usuarios en cada servidor cuando la región principal experimenta una interrupción del servicio, la región secundaria tiene repente manejar 20.000 usuarios. Reglas en la región secundaria de supervisión deben detectar este aumento y doble las instancias de la región secundaria. Para obtener más información, vea la sección de [detección de errores](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Solución en la nube y local híbrido

Una estrategia adicional para recuperación es diseñar una aplicación híbrida que se ejecuta en local y en la nube. Dependiendo de la aplicación, la región principal podría ser cualquiera de las ubicaciones. Tenga en cuenta las arquitecturas anteriores y imagine la región primaria o secundaria como una ubicación local.

Hay algunos de los desafíos en estas arquitecturas híbrido. En primer lugar, la mayor parte de este artículo responde a patrones de arquitectura de PaaS. Aplicaciones típicas de PaaS en Azure se basan en construcciones de Azure específicos como roles, servicios de nube y Administrador de tráfico. Para crear una solución local para este tipo de aplicación PaaS requeriría una arquitectura presenten diferencias significativas. Esto no sea posible desde una perspectiva de costo o administración.

Sin embargo, una solución híbrida de recuperación tiene menos desafíos para arquitecturas tradicionales que simplemente se han movido a la nube. Esto es verdadero de arquitecturas que use IaaS. Aplicaciones de IaaS usan máquinas virtuales en la nube que puede tener equivalentes directa local. También puede usar redes virtuales para conectar máquinas en la nube con recursos de la red local. Esto abre varias posibilidades que no son posibles con aplicaciones sólo PaaS. Por ejemplo, SQL Server puede aprovechar las ventajas de las soluciones de recuperación de desastres como grupos de disponibilidad AlwaysOn y reflejo de base de datos. Para obtener información detallada, vea [alta disponibilidad y recuperación para SQL Server en máquinas virtuales de Windows Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Soluciones de IaaS también ofrecen una ruta de acceso más fácil para las aplicaciones locales usar Azure como la opción de migración tras error. Es posible que tenga una aplicación totalmente funcional en una región local existente. Sin embargo, ¿qué sucede si dispone de los recursos para mantener una región geográfica independiente para la migración tras error? Puede usar máquinas virtuales y redes virtuales para obtener la aplicación que se ejecuta en Azure. En ese caso, defina los procesos que se sincronizan los datos en la nube. La implementación de Azure, a continuación, se convierte en la región secundaria para migración tras error. El área principal permanece la aplicación local. Para obtener más información acerca de IaaS arquitecturas y capacidades, consulte la [documentación de máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Nube alternativo

Hay situaciones donde incluso la potencia de Cloud de Microsoft podría no cumplir las reglas de cumplimiento interno o las directivas de su organización requiere. Incluso el mejor preparación y diseño para implementar sistemas de copia de seguridad durante un desastre fracasan si hay una interrupción del servicio global de un proveedor de servicios de nube.

Desea comparar los requisitos de disponibilidad con el costo y la complejidad de mayor disponibilidad. Realizar un análisis de riesgos y defina la recuperaci para la solución. Si su aplicación no admite los tiempos de inactividad, podría ser pertinente para que considere usar otra solución de nube. A menos que toda Internet deja de funcionar, otra solución de nube aún esté disponible si Azure es globalmente inaccesible.

Al igual que con el escenario híbrido, las implementaciones de migración tras error en las arquitecturas anteriores de recuperación de desastres también pueden existir dentro de otra solución de nube. Sitios de nube alternativo DR se deben utilizar solo para soluciones cuyo RTO permite muy poco, si la hay, tiempo de inactividad. Tenga en cuenta que una solución que utiliza un sitio de DR fuera de Azure requerirá más trabajo configurar, desarrollar, implementar y mantener. También es más difícil de implementar los procedimientos recomendados en una arquitectura de nube de cruz. Aunque plataformas de nube tienen los conceptos de alto nivel similar, las API y arquitecturas son diferentes.

Si decide dividir el DR entre distintas plataformas, tendría sentido diseñar capas de abstracción en el diseño de la solución. Si hace esto, no necesita desarrollar y mantener dos versiones diferentes de la misma aplicación para las plataformas de nube diferente en caso de desastre. Con el escenario híbrido, el uso de máquinas virtuales de Azure o servicio de contenedor de Azure podría ser más fácil en estos casos que el uso de diseños de PaaS específica de la nube.

##<a name="automation"></a>Automatización

Algunos de los patrones que hemos analizado requieren activación rápida de implementaciones sin conexión, así como la restauración de partes específicas de un sistema. Automatización o scripting, es compatible con la capacidad de activar recursos a petición e implementar soluciones rápidamente. En este artículo, se iguala automatización de DR con [PowerShell de Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx), pero la [API de REST de administración de servicio](https://msdn.microsoft.com/library/azure/ee460799.aspx) también es una opción.

Desarrollo de secuencias de comandos ayuda a administrar los elementos de DR que Azure no controla transparente. Esto tiene la ventaja de producir resultados coherentes cada vez, lo que minimiza la posibilidad de errores humanos. Tener predefinidos de secuencias de comandos de DR reducen el tiempo para volver a generar un sistema y sus componentes en medio de un desastre. No desea intentar manualmente averiguar cómo restaurar su sitio, aunque es hacia abajo y perder dinero cada minuto.

Después de crear las secuencias de comandos, probarlas repetidamente de principio a fin. Después de comprobar su funcionalidad básica, asegúrese de que las pruebas de [simulación de desastres](#disaster-simulation). Esto le ayuda a descubrir errores en las secuencias de comandos o procesos.

Un procedimiento recomendado con la automatización es crear un repositorio de secuencias de comandos de PowerShell o línea de comandos de interfaz de recuperación de Azure. Claramente marcar y categorizar ellos para la búsqueda. Designar una persona para administrar el repositorio y control de versiones de las secuencias de comandos. Documento de que ellos bien con explicaciones de ejemplos de uso de la secuencia de comandos y parámetros. Asegúrese de mantener esta documentación sincronizados con las instalaciones de Azure. Esto destaca la finalidad de tener una persona responsable de todos los elementos del repositorio.

##<a name="failure-detection"></a>Detección de errores

Para controlar correctamente los problemas de disponibilidad y recuperación, debe poder detectar y diagnosticar errores. Debe hacer avanzada y supervisión de la implementación para rápidamente puede saber cuándo un sistema o sus partes están repente hacia abajo. Herramientas de supervisión que mirar el estado general del servicio de nube y sus dependencias pueden realizar parte de este trabajo. Una herramienta de Microsoft es [sistema Centro 2016](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/). Herramientas de otros fabricantes también pueden proporcionar las capacidades de supervisión. Realizar un seguimiento de la mayoría de soluciones de supervisión contadores clave de rendimiento y la disponibilidad de servicio.

Aunque estas herramientas son importantes, no reemplazar la necesidad de plan para la detección de errores e informe dentro de un servicio de nube. Debe planear utilizar correctamente los diagnósticos de Azure. Entradas de registro de eventos o contadores personalizados también pueden ser parte de la estrategia. Esto proporciona más datos durante los errores de diagnosticar el problema y restaurar todas las capacidades de rápidamente. También proporciona métricas adicionales que pueden usar las herramientas de supervisión para determinar el estado de la aplicación. Para obtener más información, vea [Habilitar diagnósticos de Azure en servicios de nube de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para obtener información sobre cómo planear general "modelo de estado", consulte [a prueba de errores: pautas para arquitecturas de nube flexibles](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Simulación de desastres

Prueba de simulación implica creando pequeñas situaciones reales en la planta para observar cómo reaccionan los miembros del equipo. Simulaciones también mostrar la eficacia de las soluciones están en el plan de recuperación. Llevar a cabo simulaciones de manera que los escenarios creados no afecten a empresa real mientras sigue cansado como situaciones reales.

Considere la posibilidad de diseñar un tipo de "panel de control" en la aplicación para simular manualmente los problemas de disponibilidad. Por ejemplo, mediante un modificador suave, desencadenar excepciones de acceso de base de datos para un módulo de pedidos por lo que no funcione correctamente. Puede tomar enfoques ligeros similares para otros módulos en el nivel de interfaz de red.

La simulación resalta los problemas que se han dirigido mal. Los escenarios simulados deben ser completamente puede controlables. Esto significa que, incluso si el plan de recuperación parece falla, puede restaurar la situación a normal sin causar daños importantes. También es importante que informe de administración de alto nivel sobre cuándo y cómo se ejecutará ejercicios de simulación. Este plan debe incluir información sobre el tiempo o recursos que podrían ser improductivos mientras se ejecuta la prueba de simulación. Cuando está sometan su plan de recuperación a una prueba, también es importante definir cómo el éxito se mide.

Existen varias otras técnicas que puede usar para probar los planes de recuperación de desastres. Sin embargo, la mayoría de ellos es simplemente modificadas versiones de estas técnicas básicas. El motivo principal esta prueba es evaluar cómo factible y cómo aplicables el plan de recuperación. Recuperación pruebas se centra en los detalles para detectar taladros en el plan de recuperación básica.

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie de artículos centrados en la [recuperación y alta disponibilidad de las aplicaciones integradas en Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). El artículo anterior de esta serie es [alta disponibilidad de las aplicaciones integradas en Microsoft Azure](./resiliency-high-availability-azure-applications.md).
