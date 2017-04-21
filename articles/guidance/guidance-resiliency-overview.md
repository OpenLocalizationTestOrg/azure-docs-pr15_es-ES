<properties
   pageTitle="Diseño de aplicaciones y son resistentes | Microsoft Azure"
   description="Cómo crear aplicaciones flexibles en Azure, para alta disponibilidad y recuperación de desastres."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="mwasson"/>
   
# <a name="designing-resilient-applications-for-azure"></a>Diseño de aplicaciones flexibles para Azure

En un sistema distribuido, se producen errores. Puede producir un error de hardware. La red puede tener errores transitorias. Casi nunca, todo el servicio o la región puede experimentar una interrupción, pero incluso los deben planificados para. 

Crear una aplicación confiable en la nube es diferente de la creación de una aplicación confiable en una configuración de la empresa.  Mientras tradicionalmente es posible que haya adquirido hardware superiores escalar, en un entorno de nube se debe escalar en lugar de arriba. Los costos de entornos de nube se mantienen bajos mediante el uso de hardware de productos básicos. En lugar de centrarse en evitar errores y optimización "tiempo medio entre errores", en este nuevo entorno el foco para "media hora restaurar". El objetivo es minimizar el impacto de un error.

Este artículo ofrece información general sobre cómo crear aplicaciones flexibles en Microsoft Azure. Se inicia con una definición de la *resistencia* de términos y conceptos relacionados. A continuación, se describe un proceso para lograr resistencia, con un enfoque estructurado sobre la vida de una aplicación, desde el diseño y la implementación, implementación y operaciones.

## <a name="what-is-resiliency"></a>¿Qué es la resistencia?

**La resistencia** es la capacidad de recuperación de errores y siga funcionando. No es acerca de *evitar* errores, pero *responder* a errores de manera que evita la pérdida de datos o el tiempo de inactividad. Es el objetivo de la resistencia devolver la aplicación a un estado totalmente funcional tras un error.

Dos aspectos importantes de la resistencia son alta disponibilidad y recuperación.

- **Alta disponibilidad** (HA) es la capacidad de la aplicación se siga ejecutando en buen estado, sin tiempo de inactividad considerable. Por "buen estado", nos referimos es responde la aplicación, los usuarios pueden conectar con la aplicación e interactuar con él.  

- **Recuperación** (DR) es la capacidad para recuperar de incidencias poco frecuentes pero principales: errores no transitorios y gran escala, como interrupción del servicio que afecta a toda una región. Recuperación incluye copia de seguridad de datos y archivado y puede incluir intervenciones manuales, como la restauración de una base de datos de copia de seguridad. 

Una forma de pensar HA frente a DR es que DR comienza cuando el impacto de un error supera la posibilidad de que el diseño de alta disponibilidad para controlar. Por ejemplo, la colocación de varias máquinas virtuales detrás de un equilibrador de carga le proporcionará disponibilidad si se produce un error en una VM, pero no si todos ellos se producirá un error al mismo tiempo. 

Cuando diseñe una aplicación para estar flexibles, debe comprender los requisitos de disponibilidad. ¿Cuánto tiempo de inactividad es aceptable? Parcialmente es una función de costo. ¿Cuánto costará inactividad a su empresa? ¿Cuánto debe invertir en hacer que la aplicación altamente disponible? También debe definir qué significa para que la aplicación esté disponible. ¿Por ejemplo, es la aplicación "abajo" si un cliente puede enviar un pedido, pero el sistema no puede procesar en el intervalo de tiempo normal?

Otro término común es **continuidad del negocio** (BC), que es la capacidad de realizar funciones empresariales esenciales durante y después de un desastre. BC cubra todo el funcionamiento de la empresa, incluidas las instalaciones físicas, personas, comunicaciones, transporte, y TI. En este artículo, nos solo estamos centrado en aplicaciones de nube, pero resistencia planificación debe realizarse en el contexto de los requisitos de BC global. 

## <a name="process-to-achieve-resiliency"></a>Proceso para lograr resistencia

Resistencia no es un complemento. Debe ser diseñada en el sistema y poner en práctica operativa. Aquí es un modelo general para seguir:

1.  **Definir** los requisitos de disponibilidad, según las necesidades empresariales

2.  **Diseño** de la aplicación de la resistencia. Empezar con una arquitectura que sigue prácticas y, a continuación, identifique los puntos de posibles errores de esa arquitectura.

3.  Estrategias de **implementación** para detectar y recuperarse de errores. 

4.  **Prueba** la implementación, simular errores y desencadenante forzado migraciones tras error. 

5.  **Implementar** la aplicación en producción mediante un proceso reproducible confiable. 

6.  **Monitor** de la aplicación para detectar errores. Al supervisar el sistema, puede evaluar el estado de la aplicación y responder a las incidencias si es necesario. 

7.  **Responder** si hay incidencias que requieran intervenciones manuales.

En el resto de este artículo, se describe cada uno de estos pasos con más detalle.

## <a name="defining-your-resiliency-requirements"></a>Definir los requisitos de la resistencia

Planeación de la resistencia empieza con los requisitos empresariales. Estos son algunos métodos para pensar resistencia en estos términos.

### <a name="decompose-by-workload"></a>Descomponer por carga de trabajo

Muchas soluciones de nube constan de múltiples cargas de trabajo. El término "" carga de trabajo en este contexto significa una capacidad discreta o tarea informática, que puede estar separado lógicamente de otras tareas, en función de los requisitos de almacenamiento de datos y lógica empresarial. Por ejemplo, una aplicación de comercio electrónico puede incluir las cargas de trabajo siguientes:

- Examinar y buscar un catálogo de productos.

- Crear y realizar el seguimiento de pedidos.

- Recomendaciones para la vista.

Estas cargas de trabajo pueden tener diferentes requisitos de disponibilidad, escalabilidad, coherencia de los datos, recuperación y así sucesivamente. De nuevo, estos son decisiones empresariales.

Piense también patrones de uso. ¿Hay ciertos períodos críticos cuando el sistema debe estar disponible? Por ejemplo, un servicio de archivado de impuestos no se puede ir hacia abajo hacia la derecha dentro del plazo de presentación; un servicio de transmisión por secuencias de vídeo debe estar arriba durante un evento deportivo grande; y así sucesivamente. Durante los períodos críticos, es posible que tenga implementaciones redundantes a través de diferentes regiones, por lo que podría conmutar la aplicación si el error de una región. Sin embargo, una implementación de múltiples región es más cara, por lo que durante las horas menos críticas, puede ejecutar la aplicación en una sola región.  

### <a name="rto-and-rpo"></a>Recuperaci

Dos medidas importantes a tener en cuenta son el objetivo de tiempo de recuperación y el objetivo de punto de recuperación:

- **Objetivo de tiempo de recuperación** (RTO) es el tiempo máximo aceptable que una aplicación puede no estar disponible después de un incidente. Si su RTO es 90 minutos, debe poder restaurar la aplicación a un estado de ejecución en 90 minutos desde el inicio de un desastre. Si tiene un RTO muy bajo, puede mantener una segunda implementación continuamente ejecutando en espera, para proteger ante un corte regional.

- **Objetivo de punto de recuperación** (RPO) es la duración máxima de pérdida de datos es aceptable durante un desastre. Por ejemplo, si almacenar datos en una base de datos, sin replicación para otras bases de datos y realizar copias de seguridad por hora, podría perder hasta una hora de datos. 

Recuperaci son los requisitos empresariales. Otra medida común es **recuperar el tiempo medio** (tiempo medio de reparación), que es el tiempo medio que se tarda en restaurar la aplicación después de un error. Tiempo medio de reparación es un hecho empírico acerca de un sistema. Si el tiempo medio de reparación supera el RTO, un error en el sistema provocará una interrupción del negocio aceptable, porque no será posible restaurar el sistema en el RTO definido. 

### <a name="slas"></a>SLA

En Azure, el [Contrato de nivel de servicio] [ sla] (SLA) describe los compromisos de Microsoft y conectividad. Si el SLA para un servicio en particular es 99,9%, significa que debe esperar a que el servicio esté disponible 99,9% del tiempo.

> [AZURE.NOTE] El SLA de Azure también incluye disposiciones para obtener un crédito por servicio si no se cumple el SLA, junto con las definiciones específicas de "disponibilidad" para cada servicio. Ese aspecto del SLA actúa como una directiva de aplicación. 

Debe definir su propios SLA de destino para cada carga de trabajo en la solución. Un SLA hace posible razón sobre la arquitectura, y si la arquitectura cumple los requisitos empresariales. Por ejemplo, si una carga de trabajo requiere 99,99% de tiempo activo, pero depende de un servicio con un SLA 99,9%, dicho servicio no puede ser un único punto de error en el sistema. Una solución es tener una ruta de reserva en caso de que se produce un error en el servicio o tomar otras medidas para recuperarse de un error en dicho servicio. 

La siguiente tabla muestra el tiempo de inactividad acumulado posible para distintos niveles de SLA. 


| SLA     | Tiempo de inactividad por semana | Tiempo de inactividad por mes. | Tiempo de inactividad por año |
|---------|-------------------|--------------------|-------------------|
| 99%     | horas 1,68        | 7.2 horas          | 3.65 días         |
| 99,9%   | 10,1 minutos      | 43,2 minutos       | 8,76 horas        |
| 99,95%  | 5 minutos         | 21,6 minutos       | 4,38 horas        |
| 99,99%  | minutos 1.01      | 4,32 minutos       | 52.56 minutos     |
| 99,999% | 6 segundos         | 25,9 segundos       | 5,26 minutos      |


Por supuesto, mayor disponibilidad es mejor, todo igualdad. Pero como favor más 9s, aumenta el costo y la complejidad para lograr ese nivel de disponibilidad. Un tiempo de actividad del 99,99% se traduce a aproximadamente 5 minutos de inactividad total por mes. ¿Es la pena la complejidad adicional y el coste para alcanzar cinco 9s? La respuesta depende de los requisitos empresariales. 

Estas son algunas otras consideraciones al definir un SLA:

- Para lograr cuatro 9 (99,99%), probablemente no confiar en intervención manual para recuperar de errores. La aplicación se debe propia diagnosticar y recuperación automática. 

- Más allá de cuatro 9, es difícil detectar interrupciones lo suficientemente rápido para cumplir el SLA.

- Piense en la ventana de tiempo que se mide el SLA contra. Cuanto menor sea la ventana, la mayor la tolerancia. Probablemente no tiene sentido para definir el SLA en términos de actividad de horas o por día. 

### <a name="composite-slas"></a>SLA compuestos

Considere la posibilidad de una aplicación web de servicio de la aplicación que escribe en la base de datos de SQL Azure. En el momento de escribir este artículo, estos servicios Azure tienen los siguientes SLA:

- Servicio de aplicación Web Apps = 99,95%

- Base de datos SQL = 99,99%

![SLA compuesto](media/resiliency/resiliency-sla1.png)

¿Qué es el tiempo de inactividad máximo que esperaría de esta aplicación? Si se produce un error en alguno de los servicios, se produce un error en toda la aplicación. En general, la probabilidad de cada error del servicio es independiente, por lo que el SLA compuesto de esta aplicación es 99,95% x 99,99% = 99,94%. Que es menor que el SLA individuales, que no es sorprendentes, porque una aplicación que se basa en varios servicios tiene más posibles puntos de error. 

Por otro lado, puede mejorar el SLA compuesto mediante la creación de rutas de acceso de reserva independientes. Por ejemplo, si la base de datos de SQL no está disponible, poner transacciones en cola, que deben procesarse más adelante.

![SLA compuesto](media/resiliency/resiliency-sla2.png)

Con este diseño, la aplicación sigue estando disponible incluso si no puede conectarse a la base de datos. Sin embargo, se produce un error si la base de datos y la cola de un error al mismo tiempo. El porcentaje de tiempo para un error simultáneo esperado es 0,0001 × 0,001, por lo que es el SLA compuesto para esta ruta de acceso combinada  

- Cola de la base de datos o = 1.0 &minus; (0.0001 &times; 0.001) = 99,99999%

El SLA de composición total es:

- Web app y (base de datos de cola o) = 99,95% &times; % 99,99999 = ~99.95%

Pero hay compensaciones a este enfoque. La lógica de la aplicación es más compleja, paga por la cola y puede haber problemas de coherencia de datos para tener en cuenta.

**SLA para implementaciones de múltiples región**. Otra técnica HA es implementar la aplicación en más de una región y a continuación, usar el Administrador de tráfico de Azure conmutar si se produce un error en la aplicación en una región específica. Una implementación de dos región, el SLA compuesto se calcula como sigue. 

Sea *N* el SLA compuesto para la aplicación implementada en una región específica. Es la posibilidad de esperados que la aplicación se producirá un error en ambas regiones al mismo tiempo (1 &minus; N) &times; (1 &minus; N). Por lo tanto,

- Combinar SLA de ambas regiones = 1 &minus; (1 &minus; N) (1 &minus; N) = N + (1 &minus; N) N

Por último, debe considerar en el [SLA para el Administrador de tráfico][tm-sla]. A partir de cuándo se escribió este artículo, el SLA para el Administrador de tráfico SLA es 99,99%.

- SLA compuesto = 99,99% &times; (combinado SLA de ambas regiones)

Un mayor detalle es que falta sobre no es instantánea, lo que puede provocar en el tiempo de inactividad durante un error. Consulte [Administrador de tráfico extremo supervisión y migración tras error][tm-failover].

El número de SLA calculado es una línea de base útil, pero no indica la información sobre la disponibilidad. A menudo, una aplicación puede degradar correctamente cuando se produce un error en una ruta de acceso no crítica. Considere una aplicación que muestra un catálogo de libros. Si la aplicación no puede recuperar la imagen en miniatura de la portada, puede que aparezca una imagen de marcador de posición. En ese caso, no puede obtener la imagen no reducir el tiempo de actividad de la aplicación, aunque afecta a la experiencia del usuario.  

## <a name="designing-for-resiliency"></a>Diseño de la resistencia de

Durante la fase de diseño, debe realizar un análisis de modo de error (FMA). El objetivo de un FMA es identificar los posibles puntos de error y definir cómo debe responder la aplicación a estos errores.

- ¿Cómo detecta la aplicación de este tipo de error?

- ¿Cómo se responde la aplicación a este tipo de error?

- ¿Cómo se inicie y supervisar este tipo de error? 

Para obtener más información sobre el proceso FMA, con recomendaciones específicas para Azure, consulte [Guía de la resistencia Azure: análisis de modo de errores][fma].


### <a name="example-of-identifying-failure-modes-and-detection-strategy"></a>Ejemplo de identificación de la estrategia de detección y modos de error

**Punto de error:** Llamar a un servicio web externo o API.

| Modo de error | Estrategia de detección |
|--------------|--------------------|
| Servicio no está disponible | 5xx HTTP |
| Límite | HTTP 429 (demasiadas solicitudes) | 
| Autenticación | HTTP 401 (no autorizado) | 
| Respuesta lenta | Solicitud agota el tiempo |


## <a name="resiliency-strategies"></a>Estrategias de resistencia

Esta sección proporciona una encuesta de algunas estrategias de la resistencia comunes. La mayoría de las siguientes opciones no está limitada a una tecnología determinada. Las descripciones de esta sección están diseñadas para resumir la idea general de cada una de ellas, con vínculos a más información.

### <a name="retry-transient-failures"></a>Vuelva a intentar transitorias errores

Errores transitorias pueden deberse a instantáneo pérdida de conectividad de red, una conexión de base de datos colocados o un tiempo de espera cuando un servicio está ocupado. A menudo, un error temporal pueden resolverlas simplemente volver a intentar la solicitud. Para muchos de los servicios de Azure, el cliente de SDK implementa reintentos automáticos, de manera transparente para el llamador; vea [Reintentar instrucciones específicas de servicios][retry-service-specific guidance].

Cada reintento se agrega a la latencia total. Además, hay demasiados errores en las peticiones pueden provocar una botella, como las solicitudes pendientes se acumulan en la cola. Estas solicitudes bloqueadas pueden contener recursos críticos del sistema como memoria, subprocesos, conexiones de base de datos y así sucesivamente, que puede provocar errores en cascada. Para evitar esto, aumentar el tiempo de retraso entre cada reintento y limitar el número total de solicitudes de errores.

![SLA compuesto](media/resiliency/resiliency-retry.png)

Para obtener más información, vea [Reintentar trama][retry-pattern].

### <a name="load-balance-across-instances"></a>Equilibrio de carga en varias instancias

Escalabilidad, una aplicación de nube debería poder escalar agregando más instancias. Este enfoque también mejora la resistencia, porque es posible instancias mal Estados fuera de la rotación.  

Por ejemplo:

- Coloque dos o más VM detrás de un equilibrador de carga. El equilibrador de carga distribuye el tráfico a todas las máquinas virtuales. Ver [ejecutando varios VM en Azure para escalabilidad y disponibilidad][ra-multi-vm].

- Escalar una aplicación de servicio de la aplicación de Azure a varias instancias. Servicio de aplicación cargar automáticamente saldos en varias instancias. Ver la [aplicación web básica][ra-basic-web].

- Usar el [Administrador de tráfico de Azure] [ tm] para distribuir el tráfico a través de un conjunto de puntos finales.

### <a name="replicate-data"></a>Duplicar datos

Replicación de datos es una estrategia general de administración no transitorios errores en un almacén de datos. Muchas tecnologías de almacenamiento proporcionan replicación integrada, incluyendo bases de datos de SQL Azure, DocumentDB y Casandra Apache.  

Es importante tener en cuenta la lectura y escritura rutas de acceso. Dependiendo de la tecnología de almacenamiento, es posible que tenga varias réplicas de escritura, o una única réplica de escritura y múltiples réplicas de sólo lectura. 

Para obtener una mayor disponibilidad, réplicas se pueden colocar en varias áreas. Sin embargo, se incrementa la latencia para replicar los datos. Normalmente, replicar en todas las regiones se realiza de forma asincrónica, que indica un modelo de la coherencia y posibles pérdidas de datos si se produce un error en una réplica. 

### <a name="degrade-gracefully"></a>Apartar

Si se produce un error en un servicio y no hay ninguna ruta de migración tras error, es posible que la aplicación pueda apartar, de modo que aún se ofrece una experiencia de usuario aceptable. Por ejemplo:

- Coloque un elemento de trabajo de una cola, que se ejecutará más adelante. 

- Devolver un valor estimado 

- Utilizar datos almacenados en caché local. 

- Mostrar al usuario en un mensaje de error. (Esta opción es mejor que la aplicación deje de responder a solicitudes).

### <a name="throttle-high-volume-users"></a>Limitar a los usuarios de gran volumen

En ocasiones, un pequeño número de usuarios crea carga excesiva. Que puede tener un impacto en otros usuarios, se reduce la disponibilidad general de la aplicación.

Cuando un cliente realiza un número excesivo de solicitudes, la aplicación podría limitar al cliente para un período de tiempo determinado. Durante el período límite, la aplicación rechaza algunas o todas las solicitudes de cliente (dependiendo de la estrategia de limitación exacta). El umbral de limitación podría dependen de nivel de servicio del cliente. 

Limitación no implica que el cliente necesariamente se insertan malintencionada. Simplemente significa que el cliente ha superado la cuota de servicio.  En algunos casos, un consumidor podría superar constantemente la cuota o en caso contrario, se comportan de modo incorrecto. En ese caso, puede ir más allá y bloquear el usuario. Normalmente, esto se hace mediante el bloqueo de una clave de API o un intervalo de direcciones IP.

Para obtener más información, vea [Limitación trama][throttling-pattern].

### <a name="use-a-circuit-breaker"></a>Usar un disyuntor  

El modelo de disyuntor puede impedir que una aplicación varias veces intentando una operación que es probable que no se realice correctamente. La analogía es un físico disyuntor, un conmutador que interrumpe el flujo de actual cuando se sobrecarga un circuito.

El disyuntor ajusta llamadas a un servicio. Tiene tres estados:

- **Cerrado**. Este es el estado normal. El disyuntor envía solicitudes del servicio y un contador registra el número de fracasos recientes. Si el recuento de errores supera un umbral dentro de un período de tiempo determinado, el disyuntor cambia al estado abierto. 

- **Abrir**. En este estado, el disyuntor falla inmediatamente todas las solicitudes, sin llamar al servicio. La aplicación debe utilizar una ruta de acceso de mitigación, como leer datos desde una réplica o simplemente devolver un error al usuario. Cuando el disyuntor cambia a abrir, inicia un temporizador. Cuando expira, el disyuntor cambia al estado parcialmente abierto.

- **Parcialmente abierto**. En este estado, el disyuntor le permite un número limitado de solicitudes de pasar por el servicio. Si realiza correctamente, el servicio se supone que para pueden recuperar y el disyuntor cambia al estado cerrado. En caso contrario, vuelve a estado abierto. El estado parcialmente abierto impide que un servicio de recuperación de repente mucha solicitudes.

Para obtener más información, vea [Disyuntor trama][circuit-breaker-pattern].

### <a name="use-load-leveling-to-smooth-out-spikes-in-traffic"></a>Utiliza la redistribución de carga para mejorar el tráfico intenso

Las aplicaciones pueden experimentar rápidos picos de tráfico, que puede sobrecargan servicios en el servidor. Si un servicio de back-end no puede responder a solicitudes lo suficientemente rápido, puede hacer que las solicitudes en cola (copia de seguridad) o hacer que el servicio limitar la aplicación.

Para evitar esto, puede usar una cola como búfer. Cuando hay un nuevo elemento de trabajo, en lugar de llamar al servicio de back-end inmediatamente, la aplicación en cola un elemento de trabajo para ejecutarse de forma asincrónica. La cola actúa como un búfer que atenúa los picos en la carga. 

Para obtener más información, vea [modelo de redistribución de carga basado en cola][load-leveling-pattern].

### <a name="isolate-critical-resources"></a>Aislar recursos críticos 

Errores en un subsistema pueden a veces en cascada, lo que provoca errores en otras partes de la aplicación. Esto puede deberse a un error provoca algunos recursos, como subprocesos o sockets, no a obtener libera de forma oportuna, lo que lleva a agotamiento de los recursos. 

Para evitar esto, puede dividir un sistema en grupos aislados, para que un error en una partición no desconecte todo el sistema. Esta técnica se denomina el modelo de cierre.

Ejemplos:

- Dividir una base de datos, por ejemplo, al inquilino--y asigne un grupo independiente de instancias de servidor web para cada partición.  
- Usar grupos de subprocesos independiente para aislar llamadas a diferentes servicios. Esto ayuda a evitar errores en cascada si se produce un error en alguno de los servicios. Para obtener un ejemplo, vea Netflix [Hystrix biblioteca][hystrix].

- Utilizar los [contenedores] [ containers] para limitar los recursos disponibles para un subsistema determinado. 

![SLA compuesto](media/resiliency/resiliency-bulkhead.png)

### <a name="apply-compensating-transactions"></a>Aplicar transacciones de compensación 

Una transacción de compensación es una que deshace los efectos de otra transacción completada.

En un sistema distribuido, puede ser muy difícil de lograr coherencia de transacciones segura. Transacciones de compensación son una manera de conseguir coherencia mediante una serie de transacciones individuales más pequeñas, que se pueden deshacer en cada paso.

Por ejemplo, para un viaje de libro, un cliente podría reservar un coche, un hotel y un vuelo. Si se produce un error en alguno de estos pasos, se produce un error en toda la operación. En lugar de usar una sola transacción distribuida para toda la operación, puede definir una transacción compensación de cada paso. Por ejemplo, para deshacer una reserva coche, cancelar la reserva. Para completar toda la operación, un coordinador ejecuta cada paso. Si se produce un error en alguno de los pasos, el coordinador aplica transacciones de compensación para deshacer los pasos que se han completado. 

Para obtener más información, consulte [Compensación transacción trama][compensating-transaction-pattern]. 

## <a name="testing-for-resiliency"></a>Comprobación de la resistencia

En general, no puede comprobar la resistencia de la misma manera que pruebe la funcionalidad de la aplicación (por la ejecución de pruebas unitarias y así sucesivamente). En su lugar, debe probar el rendimiento de la carga de trabajo de llevar a cabo en condiciones de error, que por definición no ocurrir todo el tiempo.

Probar forma parte de un proceso iterativo. Probar la aplicación, medir el resultado, analizar y corregir los errores que se producen y a continuación, repita el proceso.

**Pruebas de inserción de errores**. Probar la resistencia del sistema errores, desencadenante reales errores o simulación de ellos. Estas son algunas situaciones de error comunes para probar:

- Cierre VM instancias.

- Bloqueo de procesos.

- Certificados de punto de expirar.

- Cambiar las teclas de acceso.

- Cierre el servicio DNS en controladores de dominio.

- Limitar los recursos del sistema disponibles, como RAM o el número de subprocesos.

- Desmontar discos.

- Implementar una máquina virtual.

Medir los tiempos de recuperación y compruebe que coincidan con los requisitos empresariales. Probar combinaciones de modos de error. Asegúrese de que no cascada errores y se controlan en una forma aislada.

Esta es otra razón por qué es importante analizar puntos posibles errores durante la fase de diseño. Los resultados del análisis debería entradas en su plan de pruebas.

**Pruebas de carga**. Prueba de carga de la aplicación con una herramienta como [Visual Studio Team Services] [ vsts] o [Apache JMeter] [ jmeter] pruebas de carga son fundamental para identificar los errores que se producen sólo en carga, como la base de datos back-end está abrumado o limitación de servicio. Comprobar la carga de recursos asignadas, usando datos de producción o datos síntesis que está cerca de los datos de producción como sea posible. El objetivo es ver cómo se comporta la aplicación en condiciones del mundo real.   

## <a name="resilient-deployment"></a>Implementación flexible

Una vez que se implementa una aplicación de producción, actualizaciones son un origen posible de errores. En el peor caso, una actualización errónea puede causar el tiempo de inactividad. Para evitar esto, el proceso de implementación debe ser predecible y predecible. Implementación incluye el aprovisionamiento de recursos de Azure, implementar código de la aplicación y aplicar opciones de configuración. Una actualización puede implicar tres o un subconjunto. 

El punto fundamental es que implementaciones manuales susceptible a errores. Por lo tanto, es recomendable para tener un proceso idempotente automatizado, que se puede ejecutar a petición y vuelva a ejecutar si se produce un error. 

- Use plantillas de administrador de recursos para automatizar el aprovisionamiento de recursos de Azure.

- Use [La configuración de estado deseado de Azure automatización] [ dsc] (DSC) para configurar máquinas virtuales.

- Utilizar un proceso de distribución automática de código de la aplicación.

Implementación relacionados con flexibles de dos conceptos son *infraestructura como código* e *inmutable*.

- **Infraestructura como código** consiste en usar código para crear y configurar la infraestructura. Infraestructura como código puede utilizar un enfoque descriptiva o un enfoque fundamental (o una combinación de ambos). Plantillas de administrador de recursos son un ejemplo de un enfoque descriptiva. Las secuencias de comandos de PowerShell son un ejemplo de un enfoque fundamental.

- **Infraestructura inmutable** es el principio que no debería modificar infraestructura después de que se implemente en producción. En caso contrario, puede ir a un estado donde se han aplicado cambios ad hoc, por lo que es difícil saber exactamente lo que ha cambiado y motivo acerca del sistema. 

Otra pregunta es cómo distribuir una actualización de la aplicación. Se recomienda técnicas como verde azulado implementación o canary versiones, que inserción actualizaciones en altamente controlan forma para minimizar los posibles efectos de una implementación incorrecta.

- [Verde azulado implementación] [ blue-green] es una técnica donde se implementa una actualización en un entorno de producción distinta de la aplicación activa. Después de validar la implementación, cambiar el enrutamiento de tráfico a la versión actualizada. Por ejemplo, Azure aplicación de servicio Web Apps permite esto con [ranuras de ensayo][staging-slots]. 

- [Versiones Canary] [ canary-release] son similares a verde azulado implementación. En lugar de cambiar todo el tráfico a la versión actualizada, gire la actualización a un pequeño porcentaje de usuarios, por una parte del tráfico de enrutamiento a la implementación nueva. Si hay un problema, retroceder y volver a la implementación anterior. En caso contrario, enrutar más a la nueva versión, hasta que obtiene el 100% de tráfico.

Cualquier opción que adopte, asegúrese de que puede volver a la implementación de buena conocida último, en caso de que la nueva versión no funciona. Además, si se producen errores, debe ser posible determinarlo con los registros de aplicaciones versión que provocó el error. 

## <a name="monitoring-and-diagnostics"></a>Supervisión y diagnóstico

Supervisión y diagnóstico es fundamental de la resistencia. Si se produce un error, debe saber que se produjo un error y necesita investigar la causa del error. 

Supervisión de un sistema distribuido a gran escala supone un reto significativo. Piense en una aplicación que se ejecuta en máquinas virtuales de docenas unas: no es práctico iniciar sesión en cada máquina virtual, de uno en uno y revise los archivos de registro, intenta solucionar un problema. Además, el número de instancias VM probablemente no es estático. Máquinas virtuales obtengan agregadas y quitadas como las escalas de aplicación y alejar y ocasionalmente una instancia puede producir errores y necesita ser reaprovisionarla. Además, una aplicación de nube típico podría utilizar varios almacena de datos (almacenamiento de Azure, base de datos de SQL, DocumentDB, Redis caché) y una acción del usuario solo puede abarcar varios subsistemas. 

Puede considerar que el proceso de supervisión y diagnóstico como una canalización con varias etapas distintas:

![SLA compuesto](media/resiliency/resiliency-monitoring.png)

- **Instrumentación**. Los datos sin formato para la supervisión y diagnósticos proceden de una gran variedad de orígenes, incluidos los registros de la aplicación, los registros de servidor web, contadores de rendimiento OS, registros de base de datos y diagnósticos integrados en la plataforma Windows Azure. Servicios más Azure tienen una característica de diagnóstico que puede usar para averiguar la causa de problemas.

- **Recopilación y almacenamiento**. Los datos sin formato instrumentación pueden mantenerse en una gran variedad de ubicaciones y con diferentes formatos (los registros de seguimiento de aplicación, contadores de rendimiento, registros de IIS). Estos diferentes orígenes recopilados, consolidados y poner en almacenamiento confiable.

- **Análisis y diagnóstico**. Después de que los datos están consolidados, se puede analizar, para solucionar problemas y proporcionar una visión general del estado de la aplicación.

- **Visualización y alertas**. En esta fase, los datos de telemetría se presentan de manera que un operador rápidamente puede detectar problemas o tendencias. En el ejemplo se incluyen paneles o alertas de correo electrónico.  

Supervisión es diferente de la detección de errores. Por ejemplo, la aplicación puede detectar un transitorio error e inténtelo de nuevo, por lo que ningún tiempo de inactividad. Pero también debe iniciar la operación de reintento, para que pueda supervisar la tasa de error con el fin de obtener una visión general del estado de aplicación. 

Los registros de aplicación son una importante fuente de datos de diagnóstico. Estas son algunas prácticas recomendadas para el registro de aplicación:

- Inicie sesión en producción. En caso contrario, perderá una perspectiva momentos muy cuando lo necesite más.

- Eventos de registro en los límites del servicio. Incluir un ID de correlación que fluye por los límites del servicio. Si la transacción X fluye a través de varios servicios y falla una de ellas, el identificador de correlación le ayudará a pinpoint por qué ha fallado la transacción.

- Usar semántico registro, también denominado estructurado de registro. Registros no estructurados dificultar automatizar el consumo y análisis de los datos del registro, que es necesario a escala de la nube.

- Usar el registro asincrónico. En caso contrario, el propio sistema de registro puede provocar errores, haciendo que las solicitudes de copia de seguridad, tal y como se bloquean en espera para escribir un evento de registro de la aplicación.

- Registro de aplicación no es idéntica a la auditoría. Puede realizar la auditoría por motivos normativos o de cumplimiento. Por lo tanto, deben completar los registros de auditoría y no está acceptible para quitar cualquiera durante el procesamiento de transacciones. Si una aplicación requiere la auditoría, debe guardarse independiente de registro de diagnóstico. 

Para obtener más información sobre la supervisión y diagnósticos, vea [Guía de supervisión y diagnóstico][monitoring-guidance].

## <a name="manual-failure-responses"></a>Respuestas error manual

Las secciones anteriores se han centrado en estrategias de recuperación automática, que son críticas para alta disponibilidad. No obstante, intervención manual a veces es necesario.

- **Alertas**. Supervisar la aplicación señales de advertencia que puede requerir intervención proactiva. Por ejemplo, si ve que la base de datos de SQL o DocumentDB constantemente limita la aplicación, necesitará aumentar la capacidad de la base de datos u optimizar las consultas. En este ejemplo, aunque la aplicación puede controlar los errores de límites transparente, la telemetría debe generar una alerta, aún para que puede realizar el seguimiento.  

- **Failover manual**. Algunos sistemas no pueden conmutar automáticamente y requiere un error manual. 

- **Pruebas de preparación operativa**. Si su aplicación realiza a una región secundaria, debe realizar una prueba de preparación operativa antes de que vuelva a la región principal. La prueba debe comprobar que el área principal es correcto y está listo para recibir el tráfico.

- **Comprobar coherencia de los datos**. Si se produce un error en un almacén de datos, puede haber incoherencias de los datos cuando el almacén vuelve a estar disponible, especialmente si los datos se hayan duplicado. 

- **Restauración de copia de seguridad**. Por ejemplo, si un corte regional experiencias de base de datos de SQL, puede geo-restaurar la base de datos de la copia de seguridad más reciente.

Documentar y probar su plan de recuperación. Incluir procedimientos escritos para los pasos manuales, como la migración tras error manual, restaurar los datos desde las copias de seguridad y así sucesivamente. 

## <a name="summary"></a>Resumen

En este artículo miró resistencia desde una perspectiva holística, enfatizar algunos de los desafíos únicos de la nube. Se incluyen la naturaleza distribuida de la informática en nube, el uso de hardware de productos básicos y la presencia de errores en la red transience.

Estos son los puntos principales de este artículo:

- Resistencia lleva a una mayor disponibilidad y reducir el tiempo medio para recuperar de errores. 

- Lograr resistencia en la nube requiere un conjunto diferente de técnicas de soluciones locales tradicionales. 

- Resistencia no ocurrir por accidente. Debe diseñado e integrada desde el principio.

- Resistencia toca cada parte del ciclo de vida de aplicación, desde la planificación y las operaciones de codificación.

- ¡Prueba y monitor!

## <a name="next-steps"></a>Pasos siguientes

- [Lista de comprobación de la resistencia] [ resiliency-checklist] contiene un recomendaciones que le ayudará a planear una gran variedad de modos de error podría ocurrir.

- [Análisis de modo de errores] [ fma] (FMA) es un proceso para crear la resistencia en un sistema, identificando puntos posibles errores. Como punto de partida para el proceso FMA, este artículo contiene un catálogo de posibles modos de error y sus soluciones. 

- Puede buscar recursos adicionales aquí: [orientación técnica de la resistencia de Azure](../resiliency/resiliency-technical-guidance.md) 

<!-- links -->

[blue-green]: http://martinfowler.com/bliki/BlueGreenDeployment.html
[canary-release]: http://martinfowler.com/bliki/CanaryRelease.html
[circuit-breaker-pattern]: https://msdn.microsoft.com/library/dn589784.aspx
[compensating-transaction-pattern]: https://msdn.microsoft.com/library/dn589804.aspx
[containers]: https://en.wikipedia.org/wiki/Operating-system-level_virtualization
[dsc]: https://azure.microsoft.com/documentation/articles/automation-dsc-overview/
[fma]: guidance-resiliency-failure-mode-analysis.md
[hystrix]: http://techblog.netflix.com/2012/11/hystrix.html
[jmeter]: http://jmeter.apache.org/
[load-leveling-pattern]: https://msdn.microsoft.com/library/dn589783.aspx
[monitoring-guidance]: https://azure.microsoft.com/documentation/articles/best-practices-monitoring/
[ra-basic-web]: https://azure.microsoft.com/documentation/articles/guidance-web-apps-basic/
[ra-multi-vm]: https://azure.microsoft.com/documentation/articles/guidance-compute-multi-vm/
[resiliency-checklist]: guidance-resiliency-checklist.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-specific guidance]: https://azure.microsoft.com/documentation/articles/best-practices-retry-service-specific/
[sla]: https://azure.microsoft.com/support/legal/sla/
[staging-slots]: https://azure.microsoft.com/documentation/articles/guidance-web-apps-basic/
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[tm-failover]: https://azure.microsoft.com/documentation/articles/traffic-manager-monitoring/
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager/v1_0/
[vsts]: https://www.visualstudio.com/features/vso-cloud-load-testing-vs.aspx
