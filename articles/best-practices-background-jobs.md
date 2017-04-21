<properties
   pageTitle="Guía de trabajos en segundo plano | Microsoft Azure"
   description="Consejos sobre el fondo de las tareas que se ejecutan en forma independiente de la interfaz de usuario."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Guía de trabajos de fondo

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Información general

Muchos tipos de aplicaciones requieren tareas en segundo plano que se ejecutan independientemente de la interfaz de usuario (UI). Por ejemplo, trabajos por lotes, tareas de procesamiento intensivo y procesos de ejecución larga como flujos de trabajo. Pueden ejecutar trabajos en segundo plano sin intervención del usuario, la aplicación puede iniciar el trabajo y continúe con el proceso interactivas solicitudes de usuarios. Esto puede ayudar a reducir la carga en la aplicación de la interfaz de usuario, que puede mejorar la disponibilidad y reducir los tiempos de respuesta interactiva.

Por ejemplo, si se requiere una aplicación para generar las miniaturas de imágenes que cargan por los usuarios, puede hacerlo como un trabajo en segundo plano y guarde la miniatura en almacenamiento cuando haya finalizado, sin el usuario tenga que esperar para el proceso de completarse. En la misma manera, un usuario de un pedido puede iniciar un flujo de trabajo de fondo que procesa el pedido, mientras que la interfaz de usuario permite al usuario continuar con la exploración de la aplicación web. Cuando haya finalizado el trabajo en segundo plano, puede actualizar los datos de pedidos almacenado y enviar un correo electrónico al usuario que confirma el pedido.

Cuando considere si implementar una tarea como un trabajo en segundo plano, los criterios principales es si la tarea se puede ejecutar sin intervención del usuario y sin la interfaz de usuario deba esperar a llevar a cabo el trabajo. Tareas que requieren el usuario o la interfaz de usuario que esperar hasta que se hayan completado no sea adecuadas como trabajos en segundo plano.

## <a name="types-of-background-jobs"></a>Tipos de trabajos en segundo plano

Trabajos en segundo plano suelen incluyen uno o varios de los siguientes tipos de tareas:

- Trabajos de CPU intensiva, como cálculos matemáticos o análisis de modelo estructural.
- Trabajos O-intensiva, como ejecutar una serie de transacciones de almacenamiento o archivos de indización.
- Tareas por lotes, como las actualizaciones de datos por la noche o procesamiento programado.
- Flujos de trabajo de ejecución larga, como pedidos o servicios y sistemas de aprovisionamiento.
- Procesamiento de datos confidenciales en la tarea se entrega a una ubicación para el procesamiento de más segura. Por ejemplo, podría no desea procesar datos confidenciales dentro de una aplicación web. En su lugar, puede usar un modelo como [equipo selector](http://msdn.microsoft.com/library/dn589793.aspx) para transferir los datos a un proceso de fondo aislado que tiene acceso al almacenamiento protegido.

## <a name="triggers"></a>Desencadenadores

Trabajos en segundo plano se pueden iniciar de varias formas diferentes. Se dividen en una de las siguientes categorías:

- [**Desencadenadores de evento**](#event-driven-triggers). La tarea se inicia en respuesta a un evento, normalmente acción realizada por un usuario o un paso de un flujo de trabajo.
- [**Desencadenadores de programación**](#schedule-driven-triggers). La tarea se invoca en una programación basándose en un temporizador. Puede ser una programación periódica o una llamada de un solo uso que se especifica para un momento posterior.

### <a name="event-driven-triggers"></a>Desencadenadores de evento

Invocación condicionados por eventos usa un desencadenador para iniciar la tarea en segundo plano. Ejemplos de usando desencadenadores de evento:

- La interfaz de usuario o de otro trabajo coloca un mensaje de una cola. El mensaje contiene datos sobre una acción que se ha producido, por ejemplo, el usuario de un pedido. La tarea en segundo plano escucha en esta cola y detecta la llegada de un mensaje nuevo. Lee el mensaje y utiliza los datos en él como entrada para el trabajo en segundo plano.
- La interfaz de usuario o de otro trabajo guarda o actualiza un valor de almacenamiento. La tarea en segundo plano supervisa el almacenamiento y detecta los cambios. Lee los datos y se usa como entrada para el trabajo en segundo plano.
- La interfaz de usuario o de otro trabajo realiza una solicitud a un extremo, como un URI HTTPS o una API que se expone como un servicio web. Pasa los datos que se requiere para completar la tarea en segundo plano como parte de la solicitud. El punto final o servicio web invoca la tarea en segundo plano, que usa los datos como entrada.

Típico tareas que se ajusten a condicionados por eventos invocación ejemplos de procesamiento de imagen, flujos de trabajo, enviar información a los servicios remota, enviar mensajes de correo electrónico y el aprovisionamiento de nuevos usuarios en aplicaciones multiempresa.

### <a name="schedule-driven-triggers"></a>Desencadenadores de programación

Programación condicionada por el invocación utiliza un temporizador para iniciar la tarea en segundo plano. Ejemplos de usando desencadenadores de programación:

- Un temporizador que se está ejecutando localmente dentro de la aplicación o como parte del sistema operativo de la aplicación invoca una tarea en segundo plano de forma regular.
- Un temporizador que se ejecuta en una aplicación diferente o un servicio de temporizador como programador de Azure, envía una solicitud a un servicio web o la API de forma regular. El servicio web o API invoca la tarea en segundo plano.
- Un proceso separado o una aplicación inicia un temporizador que hace que la tarea de fondo invocar una vez después de un tiempo de retraso especificado o en un momento determinado.

Ejemplos típicos de las tareas que se ajusten a la programación condicionada por el invocación incluyen rutinas de proceso por lotes (por ejemplo, la actualización de listas de productos relacionados con los usuarios en función de su comportamiento reciente), tareas de procesamiento de datos rutinaria (como actualizar índices o generar resultados acumulados), análisis de datos de informes diarios, limpieza de retención de datos y comprobaciones de coherencia de datos.

Si usa una tarea de programación condicionada por el que se debe ejecutar como una sola instancia, tenga en cuenta de las siguientes acciones:

- Si se cambia la escala de la instancia de cálculo que se está ejecutando al programador (por ejemplo, una máquina virtual con las tareas programadas de Windows), tendrá varias instancias del programador ejecutando. Estos pueden iniciar varias instancias de la tarea.
- Si las tareas se ejecutan más el período entre los eventos del programador, el programador puede iniciar otra instancia de la tarea mientras está ejecutando anterior.

## <a name="returning-results"></a>Devolver resultados

Trabajos en segundo plano ejecutan de forma asincrónica en un proceso separado, o incluso en una ubicación independiente, de la interfaz de usuario o el proceso que llama a la tarea en segundo plano. Lo ideal es que, tareas en segundo plano son operaciones de "desencadenar y omitir", y el progreso de la ejecución no tiene ningún efecto en la interfaz de usuario o el proceso de llamada. Esto significa que el proceso de llamada no espera para la finalización de las tareas. Por lo tanto, que no puede detectar automáticamente cuando finaliza la tarea.

Si establece una tarea en segundo plano para comunicarse con la tarea llamada para indicar el progreso o finalización, debe implementar un mecanismo de este. Algunos ejemplos son:

- Escribir un valor de indicador de estado en el almacenamiento de información que se puede acceder a la tarea de interfaz de usuario o llamador, que puede supervisar o Active este valor cuando sea necesario. Otros datos de la tarea en segundo plano debe volver a la persona que llama se pueden colocar en el mismo almacenamiento.
- Establecer una cola de respuesta que la interfaz de usuario o llamador escucha. La tarea en segundo plano puede enviar mensajes a la cola que indican el estado y finalización. Datos de la tarea en segundo plano debe volver a la persona que llama se pueden colocar en los mensajes. Si está utilizando Bus de servicio de Azure, puede usar las propiedades de **origen** y **CorrelationId** para implementar esta función. Para obtener más información, vea [correlación en mensajería negociada de Bus de servicio](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Exponer una API o el extremo de la tarea de fondo que puede tener acceso la interfaz de usuario o una llamada para obtener información de estado. Datos de la tarea en segundo plano debe volver a la persona que llama se pueden incluir en la respuesta.
- Tiene la tarea en segundo plano de la llamada a la interfaz de usuario o el autor de la llamada a través de una API para indicar el estado en los puntos predefinidos o de finalización. Es posible eventos generados localmente o a través de un mecanismo de publicación y suscripción. Datos de la tarea en segundo plano debe volver a la persona que llama se pueden incluir en la carga de solicitud o el evento.

## <a name="hosting-environment"></a>Entorno de hospedaje

Puede hospedar tareas en segundo plano mediante una gama de servicios de plataforma de Windows Azure diferentes:

- [**WebJobs y aplicaciones Web de azure**](#azure-web-apps-and-webjobs). Puede usar WebJobs para ejecutar trabajos personalizados basados en un rango de distintos tipos de secuencias de comandos o programas ejecutables en el contexto de una aplicación web.
- [**Roles de web y trabajador de azure servicios en la nube**](#azure-cloud-services-web-and-worker-roles). Puede escribir código dentro de una función que se ejecuta como una tarea en segundo plano.
- [**Máquinas virtuales de Windows azure**](#azure-virtual-machines). Si tiene un servicio de Windows o desea usar al programador de tareas de Windows, es común para las tareas de fondo en una máquina virtual dedicada de host.
- [**Lote de azure**](./batch/batch-technical-overview.md). Es un servicio de plataforma que programa el trabajo de descarga intensiva para que se ejecute en una colección de máquinas virtuales administrada y puede automáticamente escala calcular recursos para satisfacer las necesidades de los trabajos.

Las secciones siguientes describen cada una de estas opciones con más detalle e incluyen consideraciones para ayudarle a elegir la opción adecuada.

## <a name="azure-web-apps-and-webjobs"></a>WebJobs y aplicaciones Web de azure

Puede utilizar WebJobs de Azure para ejecutar trabajos personalizados como tareas en segundo plano dentro de una aplicación Web de Azure. WebJobs se ejecutan en el contexto de la aplicación web como un proceso continuo. WebJobs también se ejecutan en respuesta a un evento de desencadenador de Azure programador o factores externos, como los cambios de blobs de almacenamiento y colas de mensajes. Trabajos se pueden iniciar y detenidos a petición y cierra correctamente. Si se produce un error en un WebJob ejecución continuamente, se reinicia automáticamente. Acciones de reintento y error son configurables.

Cuando configura un WebJob:

- Si desea que el trabajo para responder a un desencadenador de evento, debe configurarse como **Ejecutar continuamente**. La secuencia de comandos o el programa se almacena en la carpeta denominada sitio/wwwroot/app_data o trabajos/continuo.
- Si desea que el trabajo para responder a un desencadenador de programación, debe configurarse como **ejecutar en una programación**. La secuencia de comandos o el programa se almacena en la carpeta denominada sitio/wwwroot/app_data o trabajos/activa.
- Si elige la opción **Ejecutar a petición** al configurar una tarea, se ejecutan el mismo código que la opción **ejecutar en una programación** al iniciarlo.

Azure WebJobs ejecutar de recinto de seguridad de la aplicación web. Esto significa que se puede tener acceso a las variables de entorno y compartir información, como las cadenas de conexión con la aplicación web. El trabajo tiene acceso al identificador único del equipo que se está ejecutando el trabajo. La cadena de conexión denominada **AzureWebJobsStorage** proporciona acceso a colas de almacenamiento de Azure, BLOB y las tablas de datos de aplicación y acceso a Bus de servicios de mensajería y comunicaciones. La cadena de conexión denominada **AzureWebJobsDashboard** proporciona acceso a los archivos de registro de acción de trabajo.

WebJobs Azure tiene las siguientes características:

- **Seguridad**: WebJobs están protegidos por las credenciales de implementación de la aplicación web.
- **Tipos de archivo compatibles**: puede definir WebJobs mediante las secuencias de comandos (cmd), archivos por lotes (bat), secuencias de comandos de PowerShell (. ps1), bash secuencias de comandos de shell (.sh), secuencias de comandos PHP (.php), secuencias de comandos de Python (.py), el código de JavaScript (js) y programas ejecutables (.exe, .jar y más).
- **Implementación**: puede implementar secuencias de comandos y ejecutables mediante el portal de Azure, mediante el complemento de [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) para Visual Studio o [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (puede crear e implementar con esta opción), mediante el [SDK de Azure WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)o copiando directamente en las siguientes ubicaciones:
  - Para desencadenar la ejecución: sitio/wwwroot/app_data o trabajos/activa / {trabajo nombre}
  - Para la ejecución continua: sitio/wwwroot/app_data o trabajos/continua / {trabajo nombre}
- **Registro**: Console.Out se considera (marcado) información. Console.Error se trata como ERROR. Puede tener acceso a información de diagnóstico y supervisión mediante el portal de Azure. Puede descargar los archivos de registro directamente desde el sitio. Se guardan en las siguientes ubicaciones:
  - Para desencadenar la ejecución: Vfs/datos o trabajos/activa/nombreDeTrabajo
  - Para la ejecución continua: Vfs/datos o trabajos/continua/nombreDeTrabajo
- **Configuración**: puede configurar WebJobs utilizando el portal de la API de REST y PowerShell. Puede usar un archivo de configuración denominado settings.job en el mismo directorio raíz como la secuencia de comandos de trabajo para proporcionar información de configuración para una tarea. Por ejemplo:
  - {"stopping_wait_time": 60}
  - {"is_singleton": true}

### <a name="considerations"></a>Consideraciones

- De forma predeterminada, la escala de WebJobs con la aplicación web. Sin embargo, puede configurar trabajos para que se ejecute en instancia única estableciendo la propiedad de configuración de **is_singleton** en **true**. Única instancia WebJobs son útiles para tareas que no desea escalar o ejecutar como simultánea varias instancias, como la indización, análisis de datos y tareas similares.
- Para reducir el impacto de los trabajos en el rendimiento de la aplicación web, considere la posibilidad de crear una instancia de la aplicación Web de Azure vacía en un nuevo plan de servicio de aplicación al host WebJobs que pueden estar larga ejecución o recurso intensiva.

### <a name="more-information"></a>Más información

- [Azure WebJobs recomienda recursos](./app-service-web/websites-webjobs-resources.md) enumera los muchos recursos útiles, descargas y ejemplos para WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Funciones de web y trabajador de Azure servicios en la nube

Puede ejecutar tareas en segundo plano dentro de una función web o en una función de trabajo independiente. Cuando decida si desea usar una función de trabajo, considere la posibilidad de escalabilidad y requisitos de elasticidad, duración de tarea, suelte cadencia, seguridad, tolerancia a errores, conflicto, complejidad y la arquitectura lógica. Para obtener más información, vea [Calcular trama de consolidación de recursos](http://msdn.microsoft.com/library/dn589778.aspx).

Hay varias formas de implementar tareas en segundo plano dentro de una función de servicios de nube:

- Crear una implementación de la clase **RoleEntryPoint** de la función y utilizar sus métodos para ejecutar tareas en segundo plano. Las tareas se ejecutan en el contexto de WaIISHost.exe. Puede usar el método **GetSetting** de la clase **CloudConfigurationManager** cargar configuración. Para obtener más información, vea [ciclo de vida (servicios de nube)](#lifecycle-cloud-services).
- Usar tareas de inicio para ejecutar tareas en segundo plano cuando se inicia la aplicación. Para forzar las tareas a seguir ejecutando en segundo plano, establezca la propiedad de **taskType** como **fondo** (si no lo hace, el proceso de inicio de la aplicación detendrá y espere a que finalice la tarea). Para obtener más información, vea [ejecutar las tareas de inicio en Azure](./cloud-services/cloud-services-startup-tasks.md).
- Use el SDK WebJobs para implementar tareas de fondo como WebJobs que se inician como una tarea de inicio. Para obtener más información, vea [Introducción a Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Use una tarea de inicio para instalar un servicio de Windows que se ejecuta una o varias tareas en segundo plano. Debe establecer la propiedad **taskType** a **fondo** para que el servicio se ejecuta en segundo plano. Para obtener más información, vea [ejecutar las tareas de inicio en Azure](./cloud-services/cloud-services-startup-tasks.md).

### <a name="running-background-tasks-in-the-web-role"></a>Ejecutar tareas en segundo plano de la función de web

La ventaja principal de la ejecución de tareas en segundo plano de la función web es el ahorro en costos de hospedaje porque no es necesario para implementar funciones adicionales.

### <a name="running-background-tasks-in-a-worker-role"></a>Ejecución de tareas de fondo en una función de trabajo

Ejecuta tareas en segundo plano en una función de trabajador tiene varias ventajas:

- Permite administrar escala por separado para cada tipo de función. Por ejemplo, es posible que tenga varias instancias de una función de web para admitir la carga actual, pero menos instancias de la función de trabajo que se ejecuta tareas en segundo plano. Cambiando el tamaño de instancias de cálculo de la tarea de fondo por separado de las funciones de la interfaz de usuario, puede reducir los costos de hospedaje, manteniendo el rendimiento aceptable.
- Se descarga la sobrecarga de procesamiento de tareas en segundo plano de la función de web. La función de web que proporciona la interfaz de usuario puede seguir respondiendo y puede significar que menos instancias son necesarios para admitir un determinado volumen de solicitudes de los usuarios.
- Permite implementar separación de asuntos. Cada tipo de rol puede implementar un conjunto específico de tareas claramente definidas y relacionados. Esto permite diseñar y mantener el código sea más fácil porque hay menos interdependencia de código y funcionalidad entre cada rol.
- Puede ayudar a aislar los datos y procesos confidenciales. Por ejemplo, los roles de web que implementan la interfaz de usuario no es necesario tener acceso a los datos que se administra y controlados por una función de trabajo. Esto puede ser útil para mejorar la seguridad, especialmente cuando utilice un modelo como el [Modelo de equipo selector](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Consideraciones

Tenga en cuenta los siguientes puntos al elegir cómo y dónde implementar tareas en segundo plano cuando se usa las funciones de web y trabajador de servicios de nube:

- Hospedaje tareas en segundo plano en una función web existente, puede guardar el costo de la ejecución de una función de trabajo independiente únicamente para estas tareas. Sin embargo, es probable que afectar al rendimiento y la disponibilidad de la aplicación si hay conflicto de procesamiento y de otros recursos. Con un rol de trabajo independiente protege el rol web desde el impacto de tareas en segundo plano de ejecución larga o recursos.
- Si hospeda tareas en segundo plano mediante la clase **RoleEntryPoint** , puede mover fácilmente esto a otro rol. Por ejemplo, si crea la clase en una función web y más tarde decide que necesita para ejecutar las tareas en una función de trabajo, puede mover la implementación de la clase **RoleEntryPoint** a la función de trabajo.
- Tareas de inicio están diseñadas para ejecutar un programa o una secuencia de comandos. Implementar un trabajo en segundo plano como un programa ejecutable podría ser más difícil, especialmente si también se requiere la implementación de ensamblados dependientes. Es posible que sea más fácil de implementar y usar una secuencia de comandos para definir un trabajo en segundo plano cuando se usa las tareas de inicio.
- Excepciones que producen una tarea en segundo plano errores tienen un impacto diferente, dependiendo de la forma que se hospedan:
  - Si utiliza el enfoque de clase **RoleEntryPoint** , una tarea hará que el rol de reiniciar para que la tarea se reinicia automáticamente. Esto puede afectar a la disponibilidad de la aplicación. Para evitar esto, asegúrese de que incluye sólidos excepciones dentro de la clase **RoleEntryPoint** y todas las tareas de fondo. Utilice el código para reiniciar las tareas que no se realice correctamente cuando sea adecuado y excepción para reiniciar la función solo si se pueden recuperar correctamente el error en el código.
  - Si usa las tareas de inicio, usted es responsable de administrar la ejecución de la tarea y comprobar si se produce un error.
- Administración y supervisión de tareas de inicio son más difícil que utiliza el enfoque de la clase **RoleEntryPoint** . Sin embargo, el SDK de WebJobs de Azure incluye un panel para que sea más fácil administrar WebJobs que inicie a través de las tareas de inicio.

### <a name="more-information"></a>Más información

- [Calcular el modelo de consolidación de recurso](http://msdn.microsoft.com/library/dn589778.aspx)
- [Empezar a trabajar con el SDK de WebJobs de Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Máquinas virtuales de Windows Azure

Tareas en segundo plano se podrían implementar de manera que impide que se implementa en aplicaciones Web de Azure o los servicios de nube o estas opciones no sea adecuadas. Ejemplos típicos son servicios de Windows y utilidades de terceros y programas ejecutables. Otro ejemplo podría ser programas escritos para un entorno de ejecución es diferente que hospeda la aplicación. Por ejemplo, podría ser un programa de Unix o Linux que desea ejecutar desde una aplicación de Windows o .NET. Puede elegir entre una variedad de sistemas operativos para una máquina virtual Azure y ejecutar el servicio o ejecutable en esa máquina virtual.

Para ayudarle a elegir cuándo usar máquinas virtuales de Windows, vea [Servicios de aplicación de Azure, servicios en la nube y comparación de máquinas virtuales de Windows](./app-service-web/choose-web-site-cloud-service-vm.md). Para obtener información sobre las opciones para máquinas virtuales de Windows, vea [tamaños de máquina Virtual y servicio de nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para obtener más información acerca de los sistemas operativos y las imágenes prediseñadas que están disponibles para máquinas virtuales de Windows, vea [Máquinas virtuales de Azure Marketplace](https://azure.microsoft.com/gallery/virtual-machines/).

Para iniciar la tarea de fondo en una máquina virtual independiente, tiene varias opciones:

- Puede ejecutar la tarea a petición directamente desde la aplicación para enviar una solicitud a un extremo que expone la tarea. Esto pasa en todos los datos que requiere la tarea. Este extremo llama a la tarea.
- Puede configurar la tarea para que se ejecute en una programación mediante un programador o temporizador que está disponible en el sistema operativo elegido. Por ejemplo, en Windows puede usar al programador de tareas de Windows para ejecutar las secuencias de comandos y las tareas. O bien, si tiene instalado en la máquina virtual de SQL Server, puede usar al agente de SQL Server para ejecutar las secuencias de comandos y las tareas.
- Planificador de Azure sirve para iniciar la tarea mediante la adición de un mensaje a una cola de escucha de la tarea en o para enviar una solicitud a una API que expone la tarea.

Vea la sección anterior [desencadenadores](#triggers) para obtener más información acerca de cómo puede iniciar tareas en segundo plano.  

### <a name="considerations"></a>Consideraciones

Cuando decida si va a implementar tareas en segundo plano en una máquina virtual Azure, tenga en cuenta los siguientes puntos:

- Tareas en segundo plano en una máquina virtual Azure independiente de hospedaje ofrece mayor flexibilidad y permite un control preciso de inicio, la ejecución, la programación y la asignación de recursos. Sin embargo, aumentará el costo de tiempo de ejecución si se debe implementar una máquina virtual solo para ejecutar tareas en segundo plano.
- No hay ninguna funcionalidad para supervisar las tareas en el portal de Azure y no tiene capacidad de reinicio automatizado para las tareas con errores: aunque puede supervisar el estado de la máquina virtual básico y administrar mediante el uso de los [Cmdlets de administración del servicio de Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). Sin embargo, no hay ninguna instalación para controlar los procesos y subprocesos de nodos de cálculo. Normalmente, usar una máquina virtual requieren esfuerzo adicional para implementar un mecanismo que recopila los datos de instrumentación en la tarea y del sistema operativo en la máquina virtual. Una solución que podría ser adecuada es usar el [Paquete de administración de System Center para Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Considere la posibilidad de crear sondeos supervisión que se exponen a través de los extremos HTTP. El código para estos sondeos podría ejecutar comprobaciones de estado, recopilar información operativa y estadísticas o intercalar información de error y volver a una aplicación de administración. Para obtener más información, vea [Trama de supervisión de estado del extremo](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Más información

- [Máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/) de Azure
- [Preguntas más frecuentes de máquinas virtuales de Windows Azure](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Consideraciones de diseño

Hay varios factores fundamentales para tener en cuenta al diseñar tareas en segundo plano. Las secciones siguientes describen partición, conflictos y junto.

## <a name="partitioning"></a>Partición

Si decide incluir tareas en segundo plano dentro de una instancia de cálculo existente (por ejemplo, una aplicación web, función web, existente rol de trabajo o máquina virtual), debe considerar cómo esto afectará a los atributos de calidad de la instancia de cálculo y la propia tarea de fondo. Estos factores le ayudará a decidir si desea situar las tareas con la instancia de cálculo existente o separar en una instancia de cálculo independiente:

- **Disponibilidad**: no es necesario tener el mismo nivel de disponibilidad de los otros elementos de la aplicación, en particular la interfaz de usuario y otros elementos que están directamente implicados en la interacción con el usuario tareas en segundo plano. Tareas en segundo plano podrían ser más tolerancia a errores de latencia de errores de conexión a intentar, y otros factores que afectan a su disponibilidad, ya que pueden poner en cola las operaciones. Sin embargo, debe haber capacidad suficiente para evitar la copia de seguridad de solicitudes que podría bloquear colas y afectan a la aplicación como un todo.
- **Escalabilidad**: tareas en segundo plano están probable que tenga un requisito escalabilidad diferente de la interfaz de usuario y las partes interactivas de la aplicación. Ajuste de escala de la interfaz de usuario podría ser necesario para cumplir con puntos máximos, mientras tareas en segundo plano pendiente podrían completadas las horas menos ocupado por un menor número de instancias de cálculo.
- **Resistencia**: error de una instancia de proceso que hospeda simplemente tareas en segundo plano podría no abren afectar a la aplicación como un todo si las solicitudes de estas tareas pueden en cola o aplazadas hasta que la tarea está disponible de nuevo. Si pueden reiniciar la instancia de proceso o tareas dentro de un intervalo adecuado, pueden que los usuarios de la aplicación no verse afectados.
- **Seguridad**: tareas en segundo plano pueden tener distintos requisitos de seguridad o las restricciones de la interfaz de usuario o a otros elementos de la aplicación. Mediante una instancia de cálculo independiente, puede especificar un entorno de seguridad diferentes para las tareas. También puede usar patrones como equipo selector para aislar las instancias de cálculo de fondo de la interfaz de usuario para maximizar la seguridad y separación.
- **Rendimiento**: puede elegir el tipo de instancia de cálculo para las tareas de fondo a específicamente coincidir los requisitos de rendimiento de las tareas. Esto podría significar utilizando una opción de cálculo menos costosa si las tareas no requiere las mismas capacidades de procesamiento que la interfaz de usuario o una instancia de mayor si requieren capacidad adicional y los recursos.
- **Facilidad de uso**: tareas en segundo plano pueden tener un ritmo diferentes de desarrollo e implementación de código de la aplicación principal o la interfaz de usuario. Puede simplificar la implementación de a una instancia de cálculo independiente actualizaciones y control de versiones.
- **Costo**: agregar calcular instancias ejecutar fondo tareas aumenta los costos de hospedaje. Debe considerar el equilibrio entre capacidad adicional y estos costes adicionales.

Para obtener más información, vea [Guía elecciones patrón](http://msdn.microsoft.com/library/dn568104.aspx) y [una competición de los consumidores](http://msdn.microsoft.com/library/dn568101.aspx).

## <a name="conflicts"></a>Conflictos

Si tiene varias instancias de un trabajo en segundo plano, es posible que le competir para obtener acceso a servicios, como bases de datos y el almacenamiento y recursos. Este acceso simultánea puede provocar conflictos de recursos, lo que podrían provocar conflictos en la disponibilidad de los servicios y en la integridad de datos de almacenamiento. Puede resolver conflictos de recursos mediante un enfoque de bloqueo pesimista. Esto impide que una competición de instancias de una tarea de obtener acceso a un servicio o dañen datos simultáneamente.

Otro método de resolución de conflictos es definir tareas en segundo plano como singleton, para que hay solo una instancia en ejecución. Sin embargo, esto elimina los beneficios de la confiabilidad y el rendimiento que puedan proporcionar una configuración de varias instancias. Esto es cierto si la interfaz de usuario puede proporcionar trabajo suficiente para mantener ocupado más de una tarea en segundo plano.

Es fundamental para asegurarse de que la tarea en segundo plano puede reiniciar automáticamente y que tiene capacidad suficiente para afrontar los picos de demanda. Para ello, puede asignar una instancia de cálculo con recursos suficientes, implementar un mecanismo de cola que puede almacenar las solicitudes de ejecución posterior cuando disminuye la demanda, o mediante una combinación de estas técnicas.

## <a name="coordination"></a>Junto

Las tareas en segundo plano podrían ser complejas y que podría requerir varias tareas individuales para ejecutar para generar un resultado o cumplir todos los requisitos. Es común en estos escenarios para dividir la tarea en pequeños pasos discretas o las subtareas que se pueden ejecutar varios consumidores. Trabajos de varios pasos pueden ser más flexible y más eficaz porque individuales pasos podrían ser reutilizables en varios trabajos. También es fácil agregar, quitar o modificar el orden de los pasos.

Coordinar varias tareas y pasos puede suponer un reto, pero hay tres modelos comunes que puede usar para guiar la implementación de una solución:

- **Desglosar una tarea en varios pasos reutilizables**. Una aplicación tenga que realizar una gran variedad de tareas de diversa complejidad de la información que procesa. Un enfoque sencillo pero inflexible para implementar esta aplicación posible realizar este procesamiento como un módulo monolítico. Sin embargo, este enfoque es probable que reducir las oportunidades de refactorización del código, y lo optimiza o volver a utilizar de si partes de la misma procesamiento son necesarios en otro lugar dentro de la aplicación. Para obtener más información, vea [canalizaciones y trama de filtros](http://msdn.microsoft.com/library/dn568100.aspx).
- **Administrar la ejecución de los pasos de una tarea**. Una aplicación puede realizar las tareas que forman parte de un número de pasos (algunas de las cuales pueden invocar los servicios remotos o tener acceso a recursos remotos). Los pasos individuales pueden ser independientes entre sí, pero organizados por la lógica de aplicación que implementa la tarea. Para obtener más información, consulte [Programador agente Supervisor trama](http://msdn.microsoft.com/library/dn589780.aspx).
- **Administrar la recuperación de los pasos de la tarea que no se realice correctamente**. Una aplicación podría necesitar deshacer el trabajo que realiza una serie de pasos (que definen una operación finalmente coherente) si uno o varios de los pasos no se realice correctamente. Para obtener más información, vea [Modelo de transacción de compensación](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Ciclo de vida (servicios de nube)

 Si decide implementar trabajos en segundo plano para aplicaciones de servicios de nube que utilizan funciones web y trabajo mediante la clase **RoleEntryPoint** , es importante entender el ciclo de vida de esta clase para utilizarlo correctamente.

Roles de Web y trabajador vaya a través de un conjunto de fases como inicio, ejecutar y detener. La clase **RoleEntryPoint** expone una serie de eventos que indican cuándo se producen estas fases. Utilice estos iniciar, ejecutar y dejar las tareas de fondo personalizado. El ciclo completo es:

- Azure carga el ensamblado de la función y busca para una clase que se deriva de **RoleEntryPoint**.
- Si encuentra esta clase, llamada **RoleEntryPoint.OnStart()**. Reemplazar este método para inicializar sus tareas en segundo plano.
- Cuando haya finalizado el método **OnStart** , llamadas Azure **Application_Start()** en el archivo Global de la aplicación si este es presentan (por ejemplo, Global.asax en una función web ejecuta ASP.NET).
- Azure llamadas **RoleEntryPoint.Run()** en un nuevo subproceso de primer plano que se ejecuta en paralelo con **OnStart()**. Reemplazar este método para iniciar las tareas de fondo.
- Cuando finaliza el método Run, Azure llamadas primero **Application_End()** en el archivo Global de la aplicación si esta está presente y, a continuación, llama **RoleEntryPoint.OnStop()**. Reemplazar el método **OnStop** para dejar de sus tareas en segundo plano, limpiar los recursos, eliminar los objetos y cerrar conexiones que pueden haberle asignado las tareas.
- El proceso de host de rol de Azure trabajo se detiene. En este momento, la función será recicla y se reinicie.

Para obtener información detallada y un ejemplo de uso de los métodos de la clase **RoleEntryPoint** , vea [Calcular trama de consolidación de recursos](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Consideraciones

Si planea cómo se ejecutará tareas en segundo plano en una función web o trabajo, tenga en cuenta los siguientes puntos:

- La implementación del método de **Ejecutar** de forma predeterminada en la clase **RoleEntryPoint** contiene una llamada a **Timeout.Infinite** que mantiene la función activos indefinidamente. Si se reemplaza el método **Run** (que es normalmente necesario para ejecutar tareas en segundo plano), no debe permitir el código salir del método a menos que desee a la instancia del rol de reciclaje.
- Una implementación típica del método **Run** incluye código para iniciar cada una de las tareas de fondo y una construcción de bucle que comprueba periódicamente el estado de todas las tareas de fondo. Puede reiniciar cualquiera que no se realice correctamente o supervisar para tokens de cancelación que indican que se han realizado trabajos.
- Si una tarea en segundo plano inicia una excepción no controlada, dicha tarea debe ser reciclado permitiendo otras tareas de fondo de la función siga funcionando. Sin embargo, si la excepción se debe a que se dañen los objetos fuera de la tarea, como almacenamiento compartido, se debe controlar la excepción por la clase **RoleEntryPoint** , se deben cancelar todas las tareas y el método **Run** debe poder finalizar. Azure reinicie el rol.
- Use el método **OnStop** pausar o eliminar tareas en segundo plano y limpiar los recursos. Esto puede implicar detener tareas de ejecución larga o varios pasos. Es importante tener en cuenta cómo se puede hacer para evitar incoherencias de los datos. Si una instancia de la función se detiene por algún motivo que no sea un apagado iniciado por el usuario, el código se ejecuta en el método **OnStop** debe llevar a cabo cinco minutos antes de que finalice fuerza. Asegúrese de que el código se puede completar en ese momento o puede tolerar no se ejecuta hasta el final.  
- El equilibrador de carga de Azure inicia dirigir el tráfico a la instancia de rol cuando el método **RoleEntryPoint.OnStart** devuelve el valor **true**. Por lo tanto, considere la posibilidad de colocación de todo el código de inicialización en el método **OnStart** para que todo el tráfico no recibirá las instancias de función no iniciar correctamente.
- Puede usar las tareas de inicio además de los métodos de la clase **RoleEntryPoint** . Iniciar la configuración que debe cambiar en el equilibrador de carga de Azure porque estas tareas se ejecutarán antes de la función recibe solicitudes debe usar tareas de inicio. Para obtener más información, vea [ejecutar las tareas de inicio en Azure](./cloud-services/cloud-services-startup-tasks.md).
- Si hay un error en una tarea de inicio, se puede forzar el rol continuamente reiniciar. Esto puede impedir realizar un cambio de dirección IP (VIP) virtual a una versión previamente preconfigurada porque el intercambio requiere acceso exclusivo a la función. No puede obtenerse mientras se reinicia el rol. Para resolver este problema:
    -  Agregue el código siguiente al principio de los métodos **OnStart** y **Ejecutar** en su rol:

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Agregar la definición de la configuración de **Inmovilizar** como un valor Boolean al ServiceDefinition.csdef y ServiceConfiguration. *.cscfg archivos para la función y establezca en* *Falso* *. Si la función entra en un modo de reiniciar repetidas, puede cambiar la configuración para * *Verdadero** para inmovilizar la ejecución de la función y permitir que se intercambie con una versión anterior.

## <a name="resiliency-considerations"></a>Consideraciones sobre resistencia

Tareas en segundo plano deben ser flexibles para ofrecer servicios confiables a la aplicación. Cuando se planear y diseñar tareas en segundo plano, tenga en cuenta los siguientes puntos:

- Tareas en segundo plano deben poder controlar correctamente rol o servicio se reinicia sin dañar los datos o introducir incoherencias en la aplicación. Para las tareas de ejecución larga o varios pasos, considere el uso de _comprobar que señala_ guardando el estado de los trabajos en almacenamiento persistente o como mensajes de una cola si esto es adecuado. Por ejemplo, puede conservar la información de estado en un mensaje de una cola y actualizar incrementalmente esta información de estado con el progreso de la tarea para que la tarea se puede procesar desde el último conocido buen punto de control, en lugar de reiniciar desde el principio. Al usar colas de Bus de servicio de Azure, puede usar sesiones de mensajería para habilitar los mismos escenarios. Sesiones permiten guardar y recuperar el estado de procesamiento de la aplicación mediante los métodos [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) y [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Para obtener más información sobre cómo diseñar flujos de trabajo y procesos confiables de varios pasos, consulte [Programador agente Supervisor trama](http://msdn.microsoft.com/library/dn589780.aspx).
- Cuando usa las funciones web o de trabajo para hospedar varias tareas en segundo plano, diseñe el reemplazo del método **Ejecutar** supervisión de error o detenidas tareas y reinicie. Si esto no es práctico y usa una función de trabajador, forzar la función trabajo reiniciar saliendo desde el método **Run** .
- Cuándo usar colas para comunicarse con tareas en segundo plano, las colas pueden actuar como un búfer para almacenar las solicitudes que se envían a las tareas mientras la aplicación está en mayor que carga habitual. Esto permite que las tareas necesarias para ponerse al día con la interfaz de usuario menos ocupado períodos. También significa que la función de reciclaje no se bloqueará la interfaz de usuario. Para obtener más información, vea [modelo de redistribución de carga basado en cola](http://msdn.microsoft.com/library/dn589783.aspx). Si algunas tareas están más importantes que otros usuarios, considere la posibilidad de implementar el [Patrón de cola de prioridad](http://msdn.microsoft.com/library/dn589794.aspx) para garantizar que estas tareas se ejecutan antes que los menos importantes.
- Tareas en segundo plano iniciadas por mensajes o procesos deben diseñarse manejar incoherencias, como los mensajes que llegan en el orden correcto, los mensajes que producen un error (a menudo esto se conoce como _mensajes dudosos_) de varias veces y mensajes que se entregarán más de una vez. Tenga en cuenta lo siguiente:
  - Mensajes que deben procesarse en un orden específico, como los que cambiar los datos según el valor de datos existente (por ejemplo, agregar un valor a un valor existente), en el orden original en el que se han enviado no llegue. Como alternativa, puede controlar diferentes instancias de una tarea de fondo en un orden diferente debido a cargas variables en cada instancia. Los mensajes que deben procesarse en un orden específico deben incluir un número de secuencia, clave o algún otro indicador de tareas en segundo plano pueden utilizar para asegurarse de que se procesan en el orden correcto. Si está utilizando Bus de servicio de Azure, puede usar sesiones de mensajería para garantizar el orden de entrega. Sin embargo, normalmente es más eficaz, cuando sea posible, el proceso de diseño de modo que no es importante el orden de los mensajes.
  - Normalmente, una tarea en segundo plano se leer mensajes en la cola, lo que les oculta temporalmente desde otros consumidores de mensaje. A continuación, elimina los mensajes después de que se ha procesado correctamente. Si se produce un error en la tarea en segundo plano al procesar un mensaje, dicho mensaje aparecerá en la cola después de que expire el tiempo de espera de la información. Se procesará otra instancia de la tarea o durante el siguiente ciclo de procesamiento de esta instancia. Si el mensaje de forma coherente produce un error en el consumidor, bloquee la tarea, la cola y finalmente en la aplicación en Sí cuando se llene la cola. Por lo tanto, es fundamental para detectar y eliminar mensajes dudosos de la cola. Si está utilizando Azure Bus de servicio, pueden mover los mensajes que producen un error automática o manualmente a una cola de asociados problemas de entrega.
  - Colas se garantiza que al _menos una vez_ mecanismos de entrega, pero puede ofrecen el mismo mensaje más de una vez. Además, si se produce un error en la tarea en segundo plano después de procesamiento de un mensaje, pero antes de eliminarlo de la cola, el mensaje estará disponible para el procesamiento de nuevo. Tareas en segundo plano debería alterar y lo que significa que el mismo mensaje de procesamiento de más de una vez no provoca un error o incoherencia en los datos de la aplicación. Algunas operaciones naturalmente pueden alterar y como establecer un valor almacenado en un valor específico de nuevo. Sin embargo, las operaciones, como agregar un valor a un valor almacenado existente sin comprobar que el valor almacenado sigue siendo el mismo que originalmente envió el mensaje provocará incoherencias. Azure colas de Bus de servicio pueden estar configuradas para quitar automáticamente los mensajes duplicados.
  - Algunos sistemas de mensajería, como colas de almacenamiento de Azure y Azure Bus de servicio, una propiedad de recuento de de-queue que indica el número de veces que se ha leído un mensaje desde la cola de soporte técnico. Esto puede ser útil en tratamiento de mensajes repetidos y dudosos. Para obtener más información, vea [Información básica sobre mensajería asincrónica](http://msdn.microsoft.com/library/dn589781.aspx) y [Patrones de idempotencia](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## <a name="scaling-and-performance-considerations"></a>Consideraciones de ajuste de escala y rendimiento

Tareas en segundo plano deben ofrecer un rendimiento suficiente para asegurarse de que no bloquea la aplicación ni provocar incoherencias debido a la operación de retraso cuando el sistema está bajo carga. Normalmente, se mejora el rendimiento cambiando el tamaño de las instancias de cálculo que hospeda las tareas en segundo plano. Cuando se planear y diseñar tareas en segundo plano, tenga en cuenta los siguientes puntos alrededor de rendimiento y escalabilidad:

- Ajuste automático de Azure es compatible con (escalado y ajuste de escala en) basados en demanda actual y la carga, o en una programación predefinida, aplicaciones Web, servicios de nube web y roles de trabajo y máquinas virtuales hospedadas implementaciones. Use esta función para asegurarse de que la aplicación completo tiene suficientes capacidades de rendimiento minimizando los costos de tiempo de ejecución.
- En tareas en segundo plano tienen una capacidad de rendimiento diferentes de los otros elementos de una aplicación de servicios de nube (por ejemplo, la interfaz de usuario o componentes, como la capa de acceso a datos), las tareas de fondo juntos en una función de trabajo independiente de hospedaje permite la interfaz de usuario y el fondo tarea funciones escalar por separado para administrar la carga. Si varias tareas en segundo plano tienen capacidades de rendimiento significativamente diferentes entre sí, considere la posibilidad de dividirlos en funciones de trabajo independiente y escala cada tipo de rol de forma independiente. Sin embargo, tenga en cuenta que esto puede aumentar los costos de tiempo de ejecución en comparación con la combinación de todas las tareas en menos funciones.
- Simplemente escala los roles puede no ser suficiente para evitar la pérdida de rendimiento bajo carga. Es posible que también necesite escalar colas de almacenamiento y otros recursos para evitar que un único punto de generales cadena se convierta en una botella de procesamiento. Además, tenga en cuenta otras limitaciones, como el rendimiento máximo de almacenamiento y otros servicios que dependen de la aplicación y las tareas en segundo plano.
- Tareas en segundo plano deben diseñarse de escala. Por ejemplo, debe ser capaz de detectar dinámicamente el número de colas de almacenamiento en uso para escuchar o enviar mensajes a la cola adecuada.
- De forma predeterminada, WebJobs escala con su instancia de aplicaciones Web de Azure asociada. Sin embargo, si desea un WebJob a ejecutar como una sola instancia, puede crear un archivo Settings.job que contiene los datos JSON **{"is_singleton": true}**. Esto obliga a Azure que solo se ejecuta una instancia de la WebJob, incluso si hay varias instancias de la aplicación web asociado. Puede ser una técnica útil para las tareas programadas que deben ejecutarse como una sola instancia.

## <a name="related-patterns"></a>Patrones relacionados

- [Manual de mensajería asincrónica](http://msdn.microsoft.com/library/dn589781.aspx)
- [Guía de ajuste automático](http://msdn.microsoft.com/library/dn589774.aspx)
- [Trama de transacción de compensación](http://msdn.microsoft.com/library/dn589804.aspx)
- [Trama de los consumidores de competencia](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calcular particiones orientación](http://msdn.microsoft.com/library/dn589773.aspx)
- [Calcular el modelo de consolidación de recurso](http://msdn.microsoft.com/library/dn589778.aspx)
- [Trama de selector](http://msdn.microsoft.com/library/dn589793.aspx)
- [Patrón de elección de relleno](http://msdn.microsoft.com/library/dn568104.aspx)
- [Patrón de filtros y canalizaciones](http://msdn.microsoft.com/library/dn568100.aspx)
- [Trama de cola de prioridad](http://msdn.microsoft.com/library/dn589794.aspx)
- [Trama de redistribución de carga basado en cola](http://msdn.microsoft.com/library/dn589783.aspx)
- [Programador agente Supervisor patrón](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Más información

- [Aplicaciones de Azure escaladas con funciones de trabajo](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Ejecutar tareas en segundo plano](http://msdn.microsoft.com/library/ff803365.aspx)
- [Ciclo de vida de inicio de rol de Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (entrada de blog)
- [Ciclo de vida de rol de servicios de nube de Azure](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vídeo)
- [Introducción a Azure SDK WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure colas y servicio Bus - comparar y contrastar](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Cómo habilitar diagnósticos en un servicio de nube](./cloud-services/cloud-services-dotnet-diagnostics.md)
