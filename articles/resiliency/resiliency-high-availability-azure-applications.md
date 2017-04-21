<properties
   pageTitle="Alta disponibilidad para las aplicaciones de Azure | Microsoft Azure"
   description="Información general técnica e información detallada sobre cómo diseñar y crear aplicaciones de alta disponibilidad en Microsoft Azure."
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

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Alta disponibilidad para las aplicaciones integradas en Microsoft Azure

Una aplicación altamente disponible absorbe fluctuación de disponibilidad, carga y errores temporales en el hardware y los servicios dependientes. La aplicación continúa funcionando a un usuario aceptable y un nivel de respuesta sistemático, según los requisitos empresariales o contratos de aplicación nivel de servicio (SLA).

##<a name="azure-high-availability-features"></a>Características de alta disponibilidad de Azure

Azure tiene muchas características integradas de plataforma que admiten aplicaciones altamente disponibles. Esta sección describen algunas de las características clave. Para un análisis más exhaustivo de la plataforma, vea [Guía técnica de la resistencia de Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Controlador de tela

El controlador de Azure tela aprovisiona y supervisa el estado de las instancias de cálculo Azure. El controlador de tela comprueba el estado del hardware y software de las instancias del equipo host y de invitado. Cuando se detecta un error, exige SLA cambiando automáticamente las instancias de máquina virtual. El concepto de más dominios errores y actualización es compatible con el SLA de cálculo.

Cuando se implementan varias instancias de la función de servicio de nube, Azure implementa estas instancias en dominios de error diferente. Un límite de dominio de errores básicamente es un bastidor hardware diferente en la misma región. Dominios de error, reducen la probabilidad de que un error de hardware localizados interrumpe el servicio de una aplicación. No puede administrar el número de dominios de error que están asignados a su trabajo o funciones web. El controlador de tela utiliza recursos dedicados independientes de aplicaciones hospedadas en Azure. Tiene tiempo de actividad de 100 por cien porque sirve como el núcleo del sistema de Azure. Supervisa y administra las instancias de función a través de dominios de error.

El siguiente diagrama muestra los recursos compartidos Azure que el controlador de tela implementa y administra a través de dominios de error diferente.

![Vista simplificada de aislamiento de dominio de errores](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Actualización dominios son similares a los dominios de error en la función, pero que admiten actualizaciones en lugar de errores. Un dominio de actualización es una unidad lógica de separación de instancia que determina qué instancias en un servicio en particular se actualizarán en un punto en el tiempo. De forma predeterminada, para la implementación de servicio hospedado, se definen cinco dominios de actualización. Sin embargo, puede cambiar ese valor en el archivo de definición de servicio. Por ejemplo, suponga que tiene ocho instancias de la función de web. Habrá dos instancias en tres dominios de actualización y dos en un dominio de actualización. Azure define la secuencia de actualización, pero se basa en el número de dominios de actualización. Para obtener más información sobre los dominios de actualización, vea [actualizar un servicio de nube](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Características de otros servicios

Además de las funciones de plataforma que admiten cálculo alta disponibilidad, Azure incrusta funcionalidades de alta disponibilidad en sus otros servicios. Por ejemplo, el almacenamiento de Azure mantiene tres réplicas de todos los blob, tabla y los datos de cola. También permite la opción de replicación geo para almacenar copias de seguridad de BLOB y tablas en una región secundaria. La red de entrega de contenido de Azure permite BLOB en caché todo el mundo para redundancia y escalabilidad. Base de datos de SQL Azure mantiene varias réplicas también.

Además de la serie de [orientación técnica de la resistencia](https://aka.ms/bctechguide) de artículos, consulte el documento de [Prácticas recomendadas para el diseño de los servicios a gran escala en servicios de nube de Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) . Estos documentos proporcionan profundizar en las características de disponibilidad de la plataforma Windows Azure.

Aunque Azure proporciona varias características que son compatibles con alta disponibilidad, es importante conocer sus limitaciones:

- Para calcular, Azure garantiza que los roles están disponibles y en funcionamiento, pero no sabe si la aplicación se está ejecutando o sobrecarga.
- Para la base de datos de SQL Azure, los datos se replican sincrónicamente dentro de la región. Puede elegir active geo replicación, lo que permite hasta cuatro copias adicionales de la base de datos en la misma región (o regiones diferentes). Estas réplicas de base de datos no son las copias de seguridad en un momento. Bases de datos SQL proporcionar las capacidades de copia de seguridad en un momento. Para obtener más información sobre las capacidades de base de datos de SQL en el momento, lea el [Punto de base de datos de SQL Azure en tiempo restaurar](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Para el almacenamiento de Azure, datos de tabla y blob es replicar de forma predeterminada en una región alternativa. Sin embargo, no puede acceder a las réplicas hasta que elige Microsoft conmutar al sitio alternativo. Se produce un error de región sólo en el caso de una interrupción del servicio de toda la región prolongado y no hay ningún SLA por vez geo-migración tras error. También es importante que tenga en cuenta que cualquier daños en los datos se propagan rápidamente a las réplicas.

Para ello, debe completar las características de disponibilidad de la plataforma con las características de disponibilidad específicos de la aplicación. Características de disponibilidad específicos de la aplicación incluyen la función de instantánea blob para crear copias de seguridad de tiempo de punto de datos blob.

###<a name="availability-sets-for-azure-virtual-machines"></a>Disponibilidad conjuntos de Azure máquinas virtuales de Windows

La mayoría de este artículo se centra en los servicios de nube, que usan una plataforma como un modelo de servicio (PaaS). Sin embargo, también hay características de disponibilidad específicos para Azure máquinas virtuales, que utiliza una infraestructura como un modelo de servicio (IaaS). Para lograr alta disponibilidad con máquinas virtuales de Windows, debe usar conjuntos de disponibilidad. Un conjunto de disponibilidad sirve una función similar a errores y actualización de dominios. Dentro de un conjunto de disponibilidad, Azure sitúa las máquinas virtuales de manera que evita errores de hardware localizados y actividades de mantenimiento bloquee todos los equipos de ese grupo. Conjuntos de disponibilidad necesarios para lograr SLA para la disponibilidad de máquinas virtuales de Windows Azure.

En el diagrama siguiente proporciona una representación de dos conjuntos de disponibilidad que web de grupo y máquinas virtuales de SQL Server, respectivamente.

![Disponibilidad conjuntos de Azure máquinas virtuales de Windows](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] En el diagrama anterior, SQL Server está instalado y se ejecuta en máquinas virtuales. Esto es diferente de la discusión anterior de datos de SQL Azure, que proporciona una base de datos como un servicio administrado.

##<a name="application-strategies-for-high-availability"></a>Estrategias de aplicación de alta disponibilidad

La mayoría aplicación estrategias de alta disponibilidad implican redundancia o la eliminación de disco duras dependencias entre los componentes de la aplicación. Diseño de la aplicación debería admitir tolerancia a errores durante el tiempo de inactividad se de Azure o servicios de terceros. Las siguientes secciones describen los modelos de aplicación para aumentar la disponibilidad de los servicios de nube.

###<a name="asynchronous-communication-and-durable-queues"></a>Comunicación asincrónica y colas resistentes

Considere la posibilidad de comunicación asincrónica entre servicios de acoplamiento flexible para aumentar la disponibilidad de aplicaciones de Azure. En este modelo, escribir mensajes en colas de almacenamiento o colas de Bus de servicio de Azure para el procesamiento de más adelante. Escribir el mensaje en la cola, control devuelve inmediatamente al remitente del mensaje. Otro nivel de la aplicación controla el mensaje de procesamiento, normalmente se implementa como una función de trabajo. Si la función de trabajo deja de funcionar, los mensajes se acumulan en la cola hasta que se restaura el servicio de procesamiento. Siempre y cuando la cola está disponible, no hay ninguna dependencia directa entre el remitente front-end y el procesador de mensajes. Esto elimina el requisito de llamadas de servicio sincrónico que puede ser una botella de rendimiento en aplicaciones distribuidas.

Una variación de esta usa almacenamiento de Azure (blobs, tablas, colas) o colas de Bus de servicios como una ubicación de la migración tras error para las llamadas de base de datos dañada. Por ejemplo, una llamada sincrónica dentro de una aplicación a otro servicio (como base de datos de SQL Azure) falla varias veces. Es posible que pueda serializar datos en almacenamiento resistente. En algún momento posterior cuando el servicio o la base de datos es volver a conectarse, la aplicación puede volver a enviarla de almacenamiento. La diferencia en este modelo es que la ubicación intermedia no es una constante parte del flujo de trabajo de aplicación. Se utiliza solo en escenarios de error.

En ambos casos, comunicación asincrónica y almacenamiento intermedio impiden que un servicio de back-end inactivo interrumpir toda la aplicación. Colas servir como un intermediario lógico. Para obtener más información sobre cómo elegir el servicio de cola correcto, vea [colas Azure y Bus de servicio de Azure--comparan y frente](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Lógica de detección y vuelva a intentarlo de errores

Un punto clave en el diseño de la aplicación altamente disponible es utilizar la lógica de reintento en código tratar correctamente un servicio que funciona de forma temporal. El [Bloque de aplicación de control de errores transitorias](https://msdn.microsoft.com/library/hh680934.aspx), desarrollado por el equipo de Microsoft Patterns y prácticas, ayuda a los desarrolladores de aplicaciones en este proceso. La palabra "transitorio" significa una condición temporal que dura sólo durante un tiempo relativamente corto. En el contexto de este artículo, controlar errores transitorias es parte del desarrollo de una aplicación altamente disponible. Ejemplos de condiciones transitorias, errores de red intermitentes y conexiones de base de datos se pierden.

El bloque de aplicación de control de errores transitorias es una manera simplificada para controlar los errores en el código de forma gradual. Puede usarlo para mejorar la disponibilidad de las aplicaciones agregando sólida transitoria lógica de control de errores. En la mayoría de los casos, lógica de reintento controla la interrupción breve y vuelve a conectar el remitente y el receptor después de uno o varios intentos. Un reintento correcta normalmente trata problemas a los usuarios de la aplicación.

Los desarrolladores tienen tres opciones para administrar su lógica de reintento: intervalo incremental, fijo y exponencial. Espera incremental ya antes cada reintento en un formato lineal creciente (por ejemplo, 1, 2, 3 y 4 segundos). Intervalo fijo espera la misma cantidad de tiempo entre cada reintento (por ejemplo, 2 segundos). Para una opción de más aleatoria el exponencial interrupción espera ya entre reintentos. Sin embargo, usa el comportamiento exponencial (por ejemplo, 2, 4, 8 y 16 segundos).

La estrategia de alto nivel en el código es:

1. Definir su estrategia de reintento y la directiva.
1. Pruebe la operación que puede producir un error transitorio.
1. Si se produce un error transitorio, invocar la directiva de reintento.
1. Si se produce un error en todos los reintentos, detectar una excepción final.

Probar la lógica de reintento en errores simulados para asegurarse de que reintentos en operaciones sucesivas no como resultado un retardo largo imprevisto. Haga lo siguiente antes de decidir un error de la tarea.

###<a name="reference-data-pattern-for-high-availability"></a>Modelo de datos de referencia de alta disponibilidad

Datos de referencia son los datos de sólo lectura de una aplicación. Estos datos proporcionan el contexto de empresa en el que la aplicación genera datos de transacciones durante el curso de una operación de la empresa. Datos de transacciones están una función de punto en el tiempo de los datos de referencia. Por lo tanto, su integridad depende de la instantánea de los datos de referencia en el momento de la transacción. Esta es una definición algo separada, pero debe ser suficiente para nuestro objetivo.

Datos de referencia en el contexto de una aplicación están necesarios para el funcionamiento de la aplicación. Las aplicaciones correspondientes, crear y mantienen datos de referencia; sistemas de administración (MDM) de datos maestros suelen realizan esta función. Estos sistemas son responsables de ciclo de vida de los datos de referencia. Catálogo de productos, patrón de empleado, elementos patrón y patrón de equipos son ejemplos de datos de referencia. Datos de referencia también pueden proceden de fuera de la organización, por ejemplo, códigos postales o tasas de impuestos. Estrategias para aumentar la disponibilidad de los datos de referencia son normalmente menos difíciles que las de los datos de transacciones. Datos de referencia tienen la ventaja de principalmente inmutable.

Puede hacer que Azure roles de web y trabajador que utilizan los datos de referencia autónomos en tiempo de ejecución mediante la implementación de los datos de referencia junto con la aplicación. Si el tamaño de almacenamiento local permite una implementación, esto es un estado ideal. Con la autonomía de unidades de la escala de cálculo Azure le ayudará incrustado bases de datos (SQL, NoSQL) o archivos XML desplegados en un sistema de archivos local. Sin embargo, debe tener un mecanismo para actualizar los datos de cada rol sin necesidad de redistribución. Para ello, coloque las actualizaciones a los datos de referencia a un extremo de almacenamiento de la nube (por ejemplo, el almacenamiento de blobs de Windows Azure o base de datos SQL). Agregar código para cada rol que descarga las actualizaciones de datos en los nodos de cálculo en el inicio de la función. También puede agregar código que permite a un administrador para realizar una descarga forzada en las instancias de la función.

Para aumentar la disponibilidad, los roles también deben contener un conjunto de datos de referencia en caso de almacenamiento hacia abajo. Esto permite a los roles empezar con un conjunto de datos de referencia básico hasta que esté disponible para las actualizaciones de los recursos de almacenamiento.

![Alta disponibilidad de la aplicación a través de nodos de cálculo autónomos](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Una consideración este patrón es la implementación y la velocidad de inicio para sus funciones. Si va a implementar o descargar grandes cantidades de datos de referencia en el inicio, esto puede aumentar la cantidad de tiempo que se tarda en poner en marcha nuevas implementaciones o instancias de la función. Esto podría ser un equilibrio aceptable para la autonomía de tener los datos de referencia inmediatamente disponibles en cada rol en lugar de según en servicios de almacenamiento externo.

###<a name="transactional-data-pattern-for-high-availability"></a>Modelo de datos de transacciones de alta disponibilidad

Datos de transacciones son los datos que la aplicación se genera en un contexto de negocios. Datos de transacciones están una combinación del conjunto de procesos de negocio que implementa la aplicación y los datos de referencia que es compatible con estos procesos. Ejemplos de datos de transacciones pueden incluir pedidos, notificaciones de envío avanzadas, facturas y oportunidades de administración (CRM) de relación de cliente. Los datos de transacciones generados, por tanto, se enviarse a sistemas externos para mantener un registro o a la transformación.

Tenga en cuenta que hacen referencia a datos pueden cambiar dentro de los sistemas que son responsables de estos datos. Por este motivo, datos de transacciones deben guardar el contexto de datos de referencia de punto en el tiempo para que tenga dependencias externas mínima para su coherencia semántico. Por ejemplo, considere la eliminación de un producto del catálogo unos meses después de que se ha entregado un pedido. El procedimiento recomendado es incrustar tanto contexto de datos de referencia como sea posible en la transacción. Esto conserva la semántica asociada a la transacción, incluso si cambian los datos de referencia después de que se genera la transacción.

Como se mencionó anteriormente, arquitecturas que use acoplamiento y comunicación asincrónica adecuadas para mayores niveles de disponibilidad. Esto es válido para los datos de transacciones, pero la implementación es más compleja. Nociones transacciones tradicionales normalmente se basan en la base de datos para garantizar la transacción. Cuando se introducen capas intermedias, el código de la aplicación debe controlar correctamente los datos en varias capas para garantizar la coherencia y duración suficientes.

La siguiente secuencia describe un flujo de trabajo que separa la captura de datos de transacciones de su procesamiento:

1. Nodo de cálculo Web: presentar hacen referencia a datos.
1. Almacenamiento externo: guardar datos de transacciones intermedios.
1. Nodo de cálculo Web: finalizar la transacción de usuario final.
1. Nodo de cálculo Web: enviar los datos de transacciones completados, junto con el contexto de datos de referencia al almacenamiento resistente temporal que garantiza que proporcione una respuesta predecible.
1. Nodo de cálculo Web: señalar la finalización de usuario final de la transacción.
1. Nodo de cálculo de fondo: extraer datos de transacciones, proceso posterior, si es necesario y enviar a su ubicación de almacenamiento final en el sistema actual.

El siguiente diagrama muestra una posible implementación de este diseño en un servicio de nube hospedado de Azure.

![Alta disponibilidad a través de acoplamiento flexible](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Las flechas de guiones en el diagrama anterior indican procesamiento asincrónico. La función web front-end no tiene constancia de este procesamiento asincrónico. Esto supone el almacenamiento de la transacción a su destino final en relación con el sistema actual. Debido a la latencia que presenta este modelo asincrónica, los datos de transacciones no están inmediatamente disponibles para la consulta. Por lo tanto, cada unidad de datos de transacciones debe guardarse en la memoria caché o necesita una sesión de usuario para cumplir con la interfaz de usuario inmediato.

La función web es autónoma del resto de la infraestructura. Su perfil de disponibilidad es una combinación de la función web y la cola de Azure y no toda la infraestructura. Además de alta disponibilidad, este enfoque permite la función web escalar horizontalmente, independientemente del almacenamiento back-end. En este modelo de alta disponibilidad puede tener un impacto en el ahorro de operaciones. Componentes adicionales como Azure colas y roles de trabajo pueden afectar a los costos mensuales de uso.

Tenga en cuenta que el diagrama anterior muestra una implementación de este enfoque desacoplada a datos de transacciones. Hay muchas otras implementaciones posibles. La siguiente lista proporciona algunas alternativas:

 * Una función de trabajador puede colocarse entre el rol de web y la cola de almacenamiento.
 * Una cola de Bus de servicio se puede utilizar en lugar de una cola de almacenamiento de Azure.
 * Almacenamiento de Azure o un proveedor de base de datos diferente, podría ser el destino final.
 * Caché de Azure puede usarse en la capa web para proporcionar los requisitos de almacenamiento en caché inmediatamente después de la transacción.

###<a name="scalability-patterns"></a>Patrones de escalabilidad

Es importante que tenga en cuenta que la escalabilidad del servicio de nube directamente afecta a la disponibilidad. Si carga mayor hace que el servicio no responde, la impresión de usuario es la aplicación hacia abajo. Siga los procedimientos recomendados para la escalabilidad en función de la carga de la aplicación esperados y expectativas futuras. La escala máxima implica muchas consideraciones, como el uso de único frente a varias cuentas de almacenamiento, compartir a través de varias bases de datos y almacenamiento en caché estrategias. Para detenimiento estos patrones, vea [Procedimientos recomendados para el diseño de los servicios a gran escala en servicios de nube de Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie de artículos centrados en la [recuperación y alta disponibilidad de las aplicaciones integradas en Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). El siguiente artículo de esta serie es [recuperación para las aplicaciones integradas en Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).
