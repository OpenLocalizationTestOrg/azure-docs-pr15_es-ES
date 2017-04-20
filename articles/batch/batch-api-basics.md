<properties
    pageTitle="Introducción a las características lote Azure para desarrolladores | Microsoft Azure"
    description="Obtenga información sobre las características del servicio lote y sus API desde la perspectiva de desarrollo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Información general de la característica de lote para desarrolladores

En esta información general de los componentes principales del servicio de Azure lote, trataremos las características de servicio principal y recursos de programadores de lote pueden utilizar para generar paralelo a gran escala calculan soluciones.

Si desarrolla una aplicación de cálculo distribuida o servicio que emite directa [API de REST] [ batch_rest_api] llamadas o si está usando uno de los [SDK por lotes](batch-technical-overview.md#batch-development-apis), deberá usar muchos de los recursos y las características descritas en este artículo.

> [AZURE.TIP] Para obtener una introducción de nivel superior al servicio por lotes, consulte [Conceptos básicos de Azure por lotes](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Flujo de trabajo de servicio de proceso por lotes

El flujo de trabajo de alto nivel siguiente es habitual en casi todas las aplicaciones y servicios que utilizan el servicio por lotes para el procesamiento de cargas de trabajo paralelas:

1. Cargue los **archivos de datos** que se desean procesar en un [Almacenamiento de Azure] [ azure_storage] cuenta. Lote incluye compatibilidad integrada para acceso al almacenamiento de blobs de Windows Azure, y las tareas pueden descargar estos archivos para [calcular los nodos](#compute-node) cuando se inician las tareas.

2. Cargue los **archivos de la aplicación** que se ejecutarán las tareas. Estos archivos pueden ser archivos binarios o secuencias de comandos y sus dependencias y ejecutarán las tareas de los trabajos. Las tareas pueden descargar estos archivos desde su cuenta de almacenamiento, o puede usar la característica de [paquetes de aplicaciones](#application-packages) del lote de implementación y administración de aplicaciones.

3. Crear un [grupo](#pool) de nodos de cálculo. Al crear un grupo, especifique el número de nodos de cálculo para la agrupación, su tamaño y el sistema operativo. Cuando se ejecuta cada tarea en su trabajo, asignó ejecutar en uno de los nodos en el grupo.

4. Crear una [tarea](#job). Un trabajo administra una colección de tareas. Asociar cada tarea a un grupo específico donde se ejecutarán las tareas de la tarea.

5. Agregar [tareas](#task) a la tarea. Cada tarea ejecuta la aplicación o script que ha cargado para procesar los archivos de datos que se descarga desde su cuenta de almacenamiento. Como cada tarea finaliza, puede cargar su resultado en el almacenamiento de Azure.

6. Supervisar el progreso de trabajo y recuperar los resultados de la tarea de almacenamiento de Azure.

Las secciones siguientes describen estos y otros recursos del lote que habilitar el escenario de cálculo distribuido.

> [AZURE.NOTE] Se necesita una [cuenta de proceso por lotes](batch-account-create-portal.md) para utilizar el servicio de lote. Además, casi todas las soluciones de usan un [Almacenamiento de Azure] [ azure_storage] para el almacenamiento de archivos y la recuperación de cuenta. Lote actualmente es compatible con solo el **Propósito General** almacenamiento tipo de cuenta, como se describe en el paso 5 de [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Recursos de servicio de proceso por lotes

Algunos de los siguientes recursos: cuentas, calculan todas las soluciones que utilizan el servicio por lotes requiere nodos, grupos, tareas y tareas--. Otras, como las programaciones de trabajo y paquetes de aplicaciones, son características útiles, pero es opcionales.

- [Cuenta](#account)
- [Nodo de cálculo](#compute-node)
- [Grupo de servidores](#pool)
- [Trabajo](#job)

  - [Programaciones de trabajo](#scheduled-jobs)

- [Tarea](#task)

  - [Iniciar tarea](#start-task)
  - [Administrador de tarea](#job-manager-task)
  - [Tareas de preparación y la versión de trabajo](#job-preparation-and-release-tasks)
  - [Tarea de instancias múltiples (MPI)](#multi-instance-tasks)
  - [Dependencias entre tareas](#task-dependencies)

- [Paquetes de aplicaciones](#application-packages)

## <a name="account"></a>Cuenta

Una cuenta de lote es una entidad de identificación exclusiva en el servicio de lote. Todo el procesamiento está asociado con una cuenta de proceso por lotes. Al realizar operaciones con el servicio de proceso por lotes, necesita el nombre de cuenta y en uno de sus claves de cuenta. Puede [crear una cuenta de Azure lote con el portal de Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Nodo de cálculo

Un nodo de cálculo es una máquina virtual Azure (VM) que está dedicada al procesamiento de una parte de la carga de trabajo de la aplicación. El tamaño de un nodo determina el número de núcleos de CPU, capacidad de memoria y tamaño del sistema de archivos local asignado al nodo. Puede crear grupos de nodos de Windows o Linux mediante servicios de nube de Azure o máquinas virtuales Marketplace imágenes. Vea la siguiente sección de [grupo](#pool) para obtener más información sobre estas opciones.

Nodos pueden ejecutar cualquier archivo ejecutable o script que es compatible con el entorno de sistema operativo del nodo. Esto incluye \*.exe, \*cmd, \*bat y secuencias de comandos de PowerShell para Windows--y binarios, revestimientos y Python secuencias de comandos para Linux.

Todo calcular los nodos en lote también incluyen:

- Un estándar de [estructura de carpetas](#files-and-directories) y asociados [variables de entorno](#environment-settings-for-tasks) que están disponibles para referencia las tareas.
- Configuración de **Firewall** que se haya configurado para controlar el acceso.
- [Acceso remoto](#connecting-to-compute-nodes) a Windows (Protocolo de escritorio remoto (RDP)) y nodos Linux (Shell seguro (SSH)).

## <a name="pool"></a>Grupo de servidores

Un grupo es una colección de nodos que se inicia la aplicación. El grupo puede crearse manualmente por el usuario o automáticamente por el servicio de lote al especificar que se realice el trabajo. Puede crear y administrar un grupo que cumpla los requisitos de recursos de la aplicación. Puede usar un grupo solo con la cuenta del lote en que se creó. Una cuenta del lote puede tener más de un grupo.

Grupos de Azure lote generación encima de la plataforma de Azure cálculo principal. Proporcionan asignación a gran escala, instalación de aplicaciones, distribución de datos, supervisión de estado y flexible ajuste del número de nodos de cálculo dentro de un grupo ([escala](#scaling-compute-resources)).

Todos los nodos que se agregan a un grupo se asignaron un nombre único y la dirección IP. Cuando se quita un nodo de un grupo, se pierden los cambios realizados en el sistema operativo o los archivos y su nombre y la dirección IP se publican para usos futuros. Cuando un nodo abandona un grupo, su duración sea sobre.

Al crear un grupo, puede especificar los siguientes atributos:

- Calcular el nodo **sistema operativo** y **versión**

    Tiene dos opciones cuando se selecciona un sistema operativo de los nodos en el grupo de servidores: **Configuración de la máquina Virtual** y **Configuración de servicios de nube**.

    **Configuración de la máquina virtual** proporciona imágenes de Windows y Linux para calculan nodos de [Máquinas virtuales de Azure Marketplace][vm_marketplace].
    Al crear un grupo que contiene los nodos de configuración de la máquina Virtual, debe especificar no solo el tamaño de los nodos, pero también la **referencia de la imagen de máquina virtual** y el lote **agente de nodo SKU** esté instalado en los nodos. Para obtener más información acerca de cómo especificar las propiedades del grupo, vea [nodos en grupos de Azure lote de cálculo de aprovisionamiento Linux](batch-linux-nodes.md).

    **Configuración de servicios de nube** proporciona que Windows calculan nodos *solo*. Sistemas operativos disponibles para los grupos de configuración de servicios de nube aparecen en la [matriz de compatibilidad SDK y versiones del sistema operativo invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Al crear un grupo que contiene los nodos de servicios de nube, debe especificar el tamaño de nodo y su *Familia OS*. Al crear nodos de cálculo de grupos de Windows, suele usar servicios en la nube.

    - La *Familia OS* también determina qué versiones de .NET se instalan con el sistema operativo.
    - Como con las funciones de trabajo dentro de los servicios de nube, puede especificar una *Versión del SO* (para obtener más información acerca de las funciones de trabajo, consulte la sección [acerca de los servicios de nube](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) en la [información general sobre servicios de nube](../cloud-services/cloud-services-choose-me.md)).
    - Como con las funciones de trabajo, le recomendamos que especifique `*` para la *Versión del SO* para que los nodos se actualizan automáticamente y no hay ningún trabajo necesario para satisfacer recién versiones de lanzamiento. Es el caso de uso principal para seleccionar una versión de sistema operativo específica garantizar la compatibilidad de aplicaciones, que permite la compatibilidad con versiones anteriores pruebas para realizarse antes de permitir que la versión que se actualicen. Después de la validación, se puede actualizar la *Versión del SO* para el grupo y la nueva imagen de sistema operativo puede ser instalado las tareas en ejecución se interrumpen y cola.

- **Tamaño de los nodos**

    Tamaños de nodo de cálculo de **Configuración de servicios de nube** se enumeran en [tamaños de servicios en la nube](../cloud-services/cloud-services-sizes-specs.md). Lote es compatible con todos los tamaños de los servicios de nube excepto `ExtraSmall`.

    **Configuración de la máquina virtual** calcular nodo tamaños se muestran en [tamaños para máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) y [tamaños para máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Lote es compatible con todos los tamaños de Azure VM excepto `STANDARD_A0` y aquellos con almacenamiento premium (`STANDARD_GS`, `STANDARD_DS`, y `STANDARD_DSV2` serie).

    Al seleccionar un tamaño de nodo de cálculo, considere la posibilidad de las características y los requisitos de las aplicaciones que deberá ejecutar en los nodos. Aspectos como si la aplicación es multiproceso y cuánta memoria consume puede ayudar a determinan el tamaño de nodo más adecuada y rentable. Es típico para seleccionar un tamaño de nodo suponiendo que una tarea se ejecutará en un nodo a la vez. Sin embargo, es posible tener varias tareas (y, por tanto, varias instancias de la aplicación) [se ejecutan en paralelo](batch-parallel-node-tasks.md) en nodos de cálculo durante la ejecución del trabajo. En este caso, es común para elegir un tamaño de nodo más grande para acomodar el aumento de la demanda de ejecución de la tarea paralelo. Para obtener más información, vea la [Directiva de programación de tareas](#task-scheduling-policy) .

    Todos los nodos en un grupo tienen el mismo tamaño. Si desea ejecutar aplicaciones con diferentes requisitos del sistema y cargar niveles, le recomendamos que use grupos distintos.

- **Número de nodos de destino**

    Este es el número de nodos de cálculo que desea implementar en el grupo. Esto se conoce como *destino* porque, en algunos casos, el grupo podría no alcanza el número deseado de nodos. Un grupo no puede alcanza el número deseado de nodos si alcanza la [cuota de núcleo](batch-quota-limit.md#batch-account-quotas) para su cuenta por lotes, o si hay una fórmula de escala automático que ha aplicado al grupo que limita el número máximo de nodos (consulte la sección siguiente "Directiva de escalado").

- **Directiva de escala**

    Además de especificar un número de nodos estático, en su lugar puede escribir y aplicar una [fórmula de escala automático](#scaling-compute-resources) a un grupo. El servicio de lote periódicamente evalúa la fórmula y ajusta el número de nodos dentro del grupo basado en diversos grupo de trabajo y parámetros de la tarea que puede especificar.

- **Directiva de programación de tareas**

    La opción de configuración [max tareas por nodo](batch-parallel-node-tasks.md) determina el número máximo de tareas que se pueden ejecutar en paralelo en cada nodo de cálculo en el grupo.

    La configuración predeterminada es que una tarea a la vez que se ejecuta en un nodo, pero hay situaciones donde es útil tener más de una tarea que se ejecuta en un nodo simultáneamente. Consulte el [escenario de ejemplo](batch-parallel-node-tasks.md#example-scenario) en el artículo de [tareas de nodo simultáneas](batch-parallel-node-tasks.md) para ver cómo pueden beneficiarse de varias tareas por nodo.

    También puede especificar un *tipo de relleno* que determina si lote reparte las tareas uniformemente entre todos los nodos en un grupo o paquetes de cada nodo con el número máximo de tareas antes de asignar tareas a otro nodo.

- **Estado de las comunicaciones** de nodos de cálculo

    En la mayoría de los escenarios, las tareas funcionan de forma independiente y no es necesario comunicarse con ellos. Sin embargo, existen algunas aplicaciones en el que deben comunicarse las tareas, como [escenarios MPI](batch-mpi.md).

    Puede configurar un grupo para permitir la comunicación entre los nodos de TI--**la comunicación**. Cuando se habilita la comunicación, nodos en grupos de configuración de servicios de nube pueden comunicarse con ellos en puertos mayores que 1100 y grupos de configuración de máquina Virtual no restringen el tráfico en cualquier puerto.

    Tenga en cuenta que también habilitar la comunicación afecta a la posición de los nodos de clústeres y podría limitar el número máximo de nodos en un grupo debido a las restricciones de implementación. Si su aplicación no requiere la comunicación entre nodos, el servicio de lote puede asignar un gran número de nodos a la agrupación de muchas diferentes clústeres y centros de datos para habilitar la mayor potencia de procesamiento en paralelo.

- **Iniciar tarea** de nodos de cálculo

    Opcional *Iniciar tarea* se ejecuta en cada nodo ese nodo une a la agrupación, y cada vez que un nodo se reinicia o renovado. La tarea de inicio es especialmente útil para preparar los nodos de cálculo para la ejecución de tareas, como instalar las aplicaciones de las tareas que se ejecutan en los nodos de cálculo.

- **Paquetes de aplicaciones**

    Puede especificar los [paquetes de aplicaciones](#application-packages) para implementar los nodos de cálculo en el grupo. Paquetes de aplicaciones proporcionan implementación simplificada y control de versiones de las aplicaciones que se ejecutan las tareas. Paquetes de aplicación que se especifican para un grupo de servidores están instalados en todos los nodos que se unan a ese grupo y cada vez que un nodo se reinicia o renovado. Paquetes de aplicaciones actualmente no son compatibles en los nodos de cálculo Linux.

- **Configuración de red**

    Puede especificar el identificador de una Azure [red virtual (VNet)](../virtual-network/virtual-networks-overview.md) en el que el grupo los nodos de cálculo que debe crearse. Requisitos para especificar un VNet para su grupo puede encontrarse en [Agregar un grupo a una cuenta de] [ vnet] en la referencia de la API de REST de lote.

> [AZURE.IMPORTANT] Todas las cuentas de lote tienen predeterminado **cuota** que limita el número de **núcleos** (y por tanto, nodos de cálculo) en una cuenta de proceso por lotes. Puede encontrar las cuotas predeterminadas e instrucciones sobre cómo [aumentar una cuota](batch-quota-limit.md#increase-a-quota) (por ejemplo, el número máximo de núcleos en su cuenta por lotes) en [las cuotas y límites para el servicio del lote de Azure](batch-quota-limit.md). Si siente que le pregunta "¿por qué no mi grupo de llegar a más de X nodos?" Esta cuota de núcleo puede ser la causa.

## <a name="job"></a>Trabajo

Un trabajo es una colección de tareas. Administra cómo realiza sus tareas en los nodos de cálculo en un grupo de cálculo.

- El trabajo especifica el **grupo** en el que es ejecutarse el trabajo. Puede crear un nuevo grupo para cada tarea o utilizar un grupo para muchos trabajos. Puede crear un grupo para cada tarea que está asociado a una tarea programada o para todas las tareas que están asociadas a una tarea programada.

- Puede especificar un opcional **prioridad del trabajo**. Cuando se envía un trabajo con mayor prioridad que las tareas que están actualmente en curso, las tareas de trabajo de mayor prioridad se insertan en la cola por delante de las tareas de los trabajos de menor prioridad. No se preferencia tareas de trabajos de menor prioridad que ya se estén ejecutando.

- Puede usar **las restricciones** de trabajo para especificar ciertos límites de los trabajos:

    Puede establecer un **tiempo máximo wallclock**, por lo que si una tarea se ejecute durante más tiempo máximo wallclock especificado, se terminan el trabajo y todas sus tareas.

    Lote puede detectar y, a continuación, vuelva a intentar las tareas con errores. Puede especificar el **número máximo de reintentos de tareas** como una restricción, incluso si una tarea es *siempre* o *nunca* vuelve a intentar. Volver a intentar una tarea significa que la tarea se tenía para volver a ejecutar.

- La aplicación cliente puede agregar tareas a una tarea o puede especificar un [Administrador de la tarea](#job-manager-task). Una administrador de tarea contiene la información necesaria crear las tareas necesarias para una tarea, con el Administrador de tarea que se ejecute en uno de los nodos de cálculo en el grupo. La tarea del Administrador de trabajo se administra específicamente por lotes--está en la cola en cuanto se crea el trabajo y reinicia si se produce un error. Una tarea de la tarea de administrador es *necesaria* para las tareas que se crean en una [programación de trabajo](#scheduled-jobs) porque es la única manera de definir las tareas antes de que se crea una instancia de la tarea.

- De forma predeterminada, trabajos permanecen en el estado activo cuando se hayan completado todas las tareas dentro del trabajo. Puede cambiar este comportamiento para que la tarea finalice automáticamente cuando se hayan completado todas las tareas de la tarea. Establezca la propiedad del trabajo **onAllTasksComplete** ([OnAllTasksComplete] [ net_onalltaskscomplete] en .NET lote) a *terminatejob* para terminar automáticamente el trabajo cuando todas sus tareas en el estado completado.

    Observe que el servicio de lote considera un trabajo *sin* tareas tener todas sus tareas completadas. Por lo tanto, esta opción se usa normalmente con un [Administrador de la tarea](#job-manager-task). Si desea usar la finalización automática de trabajo sin un administrador de trabajos, debe inicialmente establezca la propiedad de **onAllTasksComplete** de la tarea nueva en *noaction*luego establezca *terminatejob* solo después de que haya terminado de agregar tareas a la tarea.

### <a name="job-priority"></a>Prioridad del trabajo

Puede asignar una prioridad a las tareas que cree en lote. El servicio de lote usa el valor de prioridad de la tarea para determinar el orden de programación de la tarea dentro de una cuenta (Esto no es se deben confundir con un [trabajo programado](#scheduled-jobs)). La prioridad valores comprendidos entre -1000 a 1000-1000 la prioridad más baja y 1000 la más alta. Puede actualizar la prioridad de un trabajo mediante el [actualizar las propiedades de un trabajo] [ rest_update_job] operación (lote REST) o modificando la [CloudJob.Priority] [ net_cloudjob_priority] propiedad (lote. NET).

Dentro de la misma cuenta, los trabajos de mayor prioridad tienen programación prioridad sobre trabajos de menor prioridad. Un trabajo con un valor de prioridad de una cuenta no tiene la prioridad de programación sobre otro trabajo con un valor de prioridad inferior en una cuenta diferente.

Programación de grupos de trabajo es independiente. Entre los distintos grupos, no se garantiza que se programa un trabajo de mayor prioridad primero si su grupo asociada está quedando sin nodos inactivos. En el mismo grupo, trabajos con el mismo nivel de prioridad tienen una oportunidad de igual de que se ha programado.

### <a name="scheduled-jobs"></a>Tareas programadas

[Las programaciones de trabajo] [ rest_job_schedules] le permite crear trabajos periódicos dentro del servicio de lote. Una programación de trabajo especifica cuándo se debe ejecutar los trabajos e incluye las especificaciones de los trabajos de ejecución. Puede especificar la duración de la programación, ¿cuánto tiempo y cuando la programación está activada y con qué frecuencia durante ese período de tiempo que se deben crear trabajos.

## <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociada con un proyecto. Se ejecuta en un nodo. Las tareas están asignadas a un nodo de ejecución o se ponen en cola hasta que quede libre un nodo. Poner simplemente, una tarea inicia uno o varios programas o secuencias de comandos de un nodo de cálculo para realizar el trabajo que necesita.

Cuando se crea una tarea, puede especificar:

- La **línea de comandos** de la tarea. Esta es la línea de comandos que se ejecute la aplicación o el script en el nodo de cálculo.

    Es importante que tenga en cuenta que la línea de comandos no se ejecutan realmente en un shell. Por lo tanto, no se forma nativa aprovechar características de shell como expansión de [variable de entorno](#environment-settings-for-tasks) (Esto incluye el `PATH`). Para aprovechar las ventajas de estas características, debe invocar el shell de la línea de comandos, por ejemplo, iniciando `cmd.exe` en nodos de Windows o `/bin/sh` en Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Si las tareas se necesitan para ejecutar una aplicación o una secuencia de comandos que no está en el nodo `PATH` o hacer referencia a variables de entorno, invocar el shell explícitamente en la línea de comandos de la tarea.

- **Archivos de recursos** que contienen los datos que deben procesarse. Estos archivos se copian automáticamente al nodo de almacenamiento de blobs de una cuenta de Azure almacenamiento de **Propósito General** antes de que se ejecuta la línea de comandos de la tarea. Para obtener más información, vea las secciones [Iniciar tarea](#start-task) y [directorios y archivos](#files-and-directories).

- Las **variables de entorno** que requiere la aplicación. Para obtener más información, consulte la sección [Configuración del entorno para las tareas](#environment-settings-for-tasks) .

- Las **restricciones** en las que se debe ejecutar la tarea. Por ejemplo, el tiempo máximo que la tarea se puede ejecutar, el número máximo de veces que se debe volver a intentar una tarea ha fallado, y el número máximo de tiempo que los archivos en el directorio de trabajo de la tarea se conservan.

- **Paquetes de aplicaciones** para implementar el nodo de cálculo en la que la tarea se programará para ejecutar. [Paquetes de aplicaciones](#application-packages) proporcionan implementación simplificada y control de versiones de las aplicaciones que se ejecutan las tareas. Paquetes de aplicaciones de nivel de tarea son especialmente útiles en entornos de grupo compartido, donde se inician las distintas tareas en un grupo y el grupo no se elimina cuando se complete una tarea. Si el trabajo tiene menos tareas de nodos en el grupo, paquetes de aplicaciones de la tarea pueden minimizar la transferencia de datos desde la aplicación se implementa solo en los nodos que ejecutan las tareas.

Además de las tareas que defina para realizar cálculos en un nodo, también se proporcionan las siguientes tareas especiales por el servicio de lote:

- [Iniciar tarea](#start-task)
- [Administrador de tarea](#job-manager-task)
- [Tareas de preparación y la versión de trabajo](#job-preparation-and-release-tasks)
- [Tareas de instancias múltiples (MPI)](#multi-instance-tasks)
- [Dependencias entre tareas](#task-dependencies)

### <a name="start-task"></a>Iniciar tarea

Mediante la asociación de una **tarea de inicio** con un grupo, puede preparar el entorno operativo de sus nodos. Por ejemplo, puede realizar acciones como instalar las aplicaciones que se ejecutan las tareas e iniciar procesos en segundo plano. La tarea de inicio se ejecuta cada vez que se inicia un nodo para como permanece en el grupo, incluso cuando el nodo es la primero agregados a la agrupación y cuándo se reinicia o renovado.

Una de las ventajas de la tarea de inicio es que puede contener toda la información necesaria configurar un nodo de cálculo e instalar las aplicaciones que son necesarias para la ejecución de la tarea. Por lo tanto, aumenta el número de nodos en un grupo es tan sencillo como especificar el nuevo número de nodos de destino: lote ya tiene la información que necesita para configurar los nuevos nodos y conseguirlos listo para aceptar las tareas.

Como con cualquier tarea por lotes de Azure, puede especificar una lista de **archivos de recursos** en [El almacenamiento de Azure][azure_storage], además de una **línea de comandos** que se ejecutará. Lote copia primero los archivos de recursos para el nodo de almacenamiento de Azure y, a continuación, ejecuta la línea de comandos. Para una tarea de inicio del grupo, la lista de archivos normalmente contiene la aplicación de tareas y sus dependencias.

Sin embargo, también puede incluir datos de referencia para usarlo con todas las tareas que se ejecutan en el nodo de cálculo. Por ejemplo, puede realizar la línea de comandos de la tarea de iniciar un `robocopy` operación Copiar archivos de la aplicación (que se han especificado como archivos de recursos y descarga en el nodo) de [directorio de trabajo de la tarea de inicio](#files-and-directories) a la [carpeta compartida](#files-and-directories)y, a continuación, ejecutar un MSI o `setup.exe`.

> [AZURE.IMPORTANT] Lote actualmente *solo* es compatible con el tipo de cuenta de almacenamiento de **Propósito General** , como se describe en el paso 5 de [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Las tareas por lotes (incluidos tareas estándar, las tareas de inicio, tareas de preparación y lanzamiento de tareas) deben especificar los archivos de recursos que residen *sólo* en cuentas de almacenamiento de **Propósito General** .

Conviene normalmente al servicio de lote esperar a que la tarea de inicio completar antes de considerar el nodo listo para asignar tareas, pero puede configurarlo.

Si se produce un error en una tarea de inicio en un nodo de cálculo, a continuación, se actualiza el estado del nodo para reflejar el error y el nodo no está asignado a las tareas. Una tarea de inicio puede fallar si hay un problema copiando sus archivos de recursos de almacenamiento, o si el proceso ejecutado por su línea de comandos devuelve un código de salida distinto de cero.

Si agrega o actualizar la tarea de inicio de un grupo *existente* , debe reiniciar sus nodos de cálculo para la tarea de inicio aplicarlos a los nodos.

### <a name="job-manager-task"></a>Administrador de tarea

Normalmente utilizar una **tarea de administrador** para controlar y supervisar la ejecución del trabajo, por ejemplo, para crear y enviar las tareas para una tarea, determinar tareas adicionales para ejecutar y determinar cuándo finaliza trabajo. Sin embargo, una tarea de administrador de trabajos no está restringida a estas actividades. Es una tarea completa que puede realizar las acciones que se requieren para el trabajo. Por ejemplo, una tarea de la tarea de administrador puede descargar un archivo que se especifica como un parámetro, analizar el contenido del archivo y enviar tareas adicionales según dicho contenido.

Se inicia una tarea de la tarea de administrador antes de todas las demás tareas. Proporciona las siguientes características:

- Se envía automáticamente como una tarea por el servicio de lote cuando se crea el trabajo.

- Está programada para ejecutar antes de las otras tareas de un trabajo.

- Su nodo asociado es el último quitarse de un grupo al que se está reducido el grupo.

- Su terminación puede estar vinculado a la finalización de todas las tareas de la tarea.

- Una tarea de la tarea de administrador se expresa la máxima prioridad cuando sea necesario reiniciar. Si el nodo inactivo no está disponible, el servicio del lote puede finalizar una de las otras tareas en ejecución en el grupo para liberar espacio para el Administrador de tarea Ejecutar.

- Una administrador de tarea en una tarea no tiene prioridad sobre las tareas de otros trabajos. A través de trabajos, se observan solo las prioridades de nivel de trabajo.

### <a name="job-preparation-and-release-tasks"></a>Tareas de preparación y la versión de trabajo

Lote proporciona tareas de preparación para la configuración de ejecución de la tarea anterior. Lanzamiento de tareas son de mantenimiento posteriores a la tarea o Liberador de espacio.

- **Tarea de preparación**: una tarea de preparación de trabajo se ejecuta en todos los nodos de cálculo que están programados para ejecutar las tareas, antes de cualquiera de las tareas se ejecutan. Puede utilizar una tarea de la tarea de preparación para copiar los datos que se comparten por todas las tareas, pero es único para el trabajo, por ejemplo.
- **Tarea de lanzamiento**: cuando haya finalizado una tarea, una tarea de lanzamiento de trabajo se ejecuta en cada nodo en el grupo que ejecuta al menos una tarea. Puede utilizar una tarea de la tarea de lanzamiento para eliminar los datos que se copian por la tarea de preparación del trabajo, o para comprimir y cargar los datos en el registro de diagnóstico, por ejemplo.

Ambas preparación del trabajo y tareas de lanzamiento le permiten especificar una línea de comandos para que se ejecute cuando se invoca la tarea. Ofrecen características como descarga de archivos, ejecución elevada, variables de entorno personalizado, duración de la ejecución máximo, número de reintentos y tiempo de retención de archivo.

Para obtener más información sobre las tareas de preparación y lanzamiento de trabajo, vea [Ejecutar preparación y finalización tareas en lotes de Azure nodos de cálculo](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarea de instancias múltiples

Una [tarea de instancias múltiples](batch-mpi.md) es una tarea que está configurada para ejecutar más de un nodo de cálculo al mismo tiempo. Con las tareas de instancias múltiples, puede habilitar escenarios informáticos de alto rendimiento que requieren un grupo de nodos de cálculo se asignan juntos para procesar una sola carga de trabajo (como mensaje de interfaz de paso (MPI)).

Para obtener información detallada sobre la ejecución de trabajos MPI lote mediante el uso de la biblioteca de .NET por lotes, consulte [tareas de instancias múltiples de uso para ejecutar aplicaciones de la interfaz de paso de mensajes (MPI) en el lote de Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Dependencias entre tareas

[Las dependencias entre tareas](batch-task-dependencies.md), como el nombre implica, le permiten especificar que una tarea depende de la realización de otras tareas antes de su ejecución. Esta función proporciona compatibilidad para las situaciones en que una tarea "descendente" consume el resultado de la tarea "ascendente", o cuando una tarea precede realiza alguna inicialización requerida por una tarea de nivel inferior. Para usar esta característica, primero debe habilitar las dependencias entre tareas en el proceso. A continuación, para cada tarea que depende de otro (o muchas otras), especifique las tareas que depende de dicha tarea.

Con las dependencias entre tareas, puede configurar escenarios como la siguiente:

* *taskB* depende de *taskA* (*taskB* no comenzará la ejecución hasta que haya completado *taskA* ).
* *taskC* depende de *taskA* y *taskB*.
* *taskD* depende de un rango de tareas, como tareas *1* al *10*, antes de ejecutar.

Consulte [dependencias de tareas en el lote de Azure](batch-task-dependencies.md) y la [TaskDependencies] [ github_sample_taskdeps] código de ejemplo en los [ejemplos del lote de azure] [ github_samples] repositorio de GitHub para obtener más información detallada acerca de esta característica.

## <a name="environment-settings-for-tasks"></a>Configuración del entorno de tareas

Cada tarea ejecuta el servicio de lote tiene acceso a las variables de entorno que establece en los nodos de cálculo. Esto incluye variables de entorno definidas por el servicio por lotes ([definidos por el servicio][msdn_env_vars]) y variables de entorno personalizado que puede definir para las tareas. Las aplicaciones y secuencias de comandos que se ejecutan las tareas tienen acceso a estas variables de entorno durante la ejecución.

Puede establecer las variables de entorno personalizado en el nivel de tarea o trabajo rellenando la propiedad de *configuración de entorno* para estas entidades. Por ejemplo, vea [Agregar una tarea a un trabajo] [ rest_add_task] operación (API de REST de lote) o la [CloudTask.EnvironmentSettings] [ net_cloudtask_env] y [CloudJob.CommonEnvironmentSettings] [ net_job_env] propiedades en .NET lote.

La aplicación cliente o servicio puede obtener variables de entorno de una tarea, definidos por el servicio y personalizadas, con [obtener información acerca de una tarea] [ rest_get_task_info] operación (lote REST) o mediante el acceso a la [CloudTask.EnvironmentSettings] [ net_cloudtask_env] propiedad (lote. NET). Procesos que se ejecutan en un nodo de cálculo pueden tener acceso a estas y otras variables de entorno en el nodo, por ejemplo, mediante la familiar `%VARIABLE_NAME%` (Windows) o `$VARIABLE_NAME` sintaxis (Linux).

Puede encontrar una lista completa de todas las variables de entorno definidas por el servicio en [calcular variables de entorno de nodo][msdn_env_vars].

## <a name="files-and-directories"></a>Directorios y archivos

Cada tarea tiene un *directorio de trabajo* que crea cero o más archivos y directorios. Este directorio de trabajo puede utilizarse para almacenar el programa que se ejecuta la tarea, los datos que procesa y el resultado del procesamiento realiza. Todos los archivos y directorios de una tarea son propiedad por el usuario de la tarea.

El servicio de lote expone una parte del sistema de archivos en un nodo como el *directorio raíz*. Las tareas pueden acceder al directorio de raíz mediante una referencia a la `AZ_BATCH_NODE_ROOT_DIR` variable de entorno. Para obtener más información sobre el uso de variables de entorno, consulte [configuración de entorno para las tareas](#environment-settings-for-tasks).

El directorio raíz contiene la estructura de directorio siguiente:

![Calcular la estructura de directorios de nodo][1]

- **compartido**: este directorio proporciona acceso de lectura y escritura a *todas las* tareas que se ejecutan en un nodo. Cualquier tarea que se ejecuta en el nodo puede crear, leer, actualizar y eliminar archivos en este directorio. Tareas pueden acceder a este directorio mediante una referencia a la `AZ_BATCH_NODE_SHARED_DIR` variable de entorno.

- **Inicio**: este directorio se usa una tarea de inicio como su directorio de trabajo. Aquí se almacenan todos los archivos que se descargan al nodo por la tarea de inicio. La tarea de inicio puede crear, leer, actualizar y eliminar archivos en este directorio. Tareas pueden acceder a este directorio mediante una referencia a la `AZ_BATCH_NODE_STARTUP_DIR` variable de entorno.

- **Tareas**: se crea un directorio para cada tarea que se ejecuta en el nodo. Obtener acceso a ellas mediante una referencia a la `AZ_BATCH_TASK_DIR` variable de entorno.

    Dentro de cada directorio de la tarea, el servicio de lote crea un directorio de trabajo (`wd`) cuya ruta de acceso único especificado por el `AZ_BATCH_TASK_WORKING_DIR` variable de entorno. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio. Este directorio se conserva en función de la restricción *RetentionTime* especificada para la tarea.

    `stdout.txt`y `stderr.txt`: estos archivos se escriben en la carpeta de tareas durante la ejecución de la tarea.

>[AZURE.IMPORTANT] Cuando se quita un nodo del grupo, se quitan *todos* los archivos que se almacenan en el nodo.

## <a name="application-packages"></a>Paquetes de aplicaciones

La característica de [paquetes de aplicaciones](batch-application-packages.md) proporciona fácil administración e implementación de aplicaciones para los nodos de cálculo de los grupos. Puede cargar y administrar varias versiones de las aplicaciones que se ejecutará mediante las tareas, incluidas sus archivos binarios y los archivos de soporte técnico. A continuación, automáticamente puede implementar una o varias de estas aplicaciones a los nodos de cálculo en el grupo.

Puede especificar los paquetes de aplicaciones en el nivel de grupo y tareas. Al especificar que los paquetes de aplicaciones de grupo, la aplicación se implementa en todos los nodos en el grupo. Al especificar que los paquetes de aplicaciones de tarea, la aplicación se implementa solo a los nodos que están programados para que se ejecute al menos una de las tareas del proyecto, justo antes de que se ejecuta la línea de comandos de la tarea.

Lote controla los detalles del trabajo con el almacenamiento de Azure para almacenar los paquetes de aplicaciones e implementar para nodos de cálculo, por lo que se puede simplificar la sobrecarga de administración y el código.

Para obtener más información sobre la característica de paquete de aplicación, consulte [implementación de la aplicación con los paquetes de aplicaciones de Azure lote](batch-application-packages.md).

>[AZURE.NOTE] Si agrega paquetes de aplicaciones de grupo a un grupo *existente* , debe reiniciar sus nodos de cálculo para los paquetes de aplicaciones para su implementación en los nodos.

## <a name="pool-and-compute-node-lifetime"></a>Duración de nodo de cálculo y de grupo

Cuando diseñe su solución lote de Azure, debe tomar una decisión de diseño acerca de cómo y cuando se crean grupos y calculan cuánto tiempo se mantienen a disposición los nodos dentro de esos grupos.

En un extremo del espectro, puede crear un grupo para cada tarea que envíe y elimine el grupo tan pronto como la ejecución de fin de sus tareas. Esto aumenta al máximo la utilización porque sólo se asignan los nodos cuando es necesario y a continuación, cierre tan pronto como se encuentra inactivos. Aunque esto significa que el trabajo debe esperar a que los nodos que se debe asignar, es importante que tenga en cuenta que las tareas se programan para ejecución tan pronto como nodos están disponibles de forma individual, asignados, y la tarea de inicio se ha completado. Lote hace *esperar hasta que todos los nodos dentro de un grupo están disponibles antes de asignar tareas a los nodos* . Así se garantiza que máxima utilización de todos los nodos disponibles.

En el otro extremo del espectro, si tiene trabajos iniciar inmediatamente es la prioridad más alta, puede crear un grupo de antelación y realizar sus nodos disponibles antes de envían los trabajos. En este escenario, tareas pueden iniciar inmediatamente, pero nodos podrían permanecen inactivos mientras se espera asignarlos.

Un enfoque combinado normalmente se usa para controlar una carga variable, pero en curso. Puede hacer que un grupo que se envían a varias tareas, pero pueden escalar el número de nodos hacia arriba o hacia abajo según el trabajo carga (consulte [escala calcular recursos](#scaling-compute-resources) en la sección siguiente). Puede hacerlo de manera reactiva, basados en carga actual, o de manera proactiva, si puede predecir la carga.

## <a name="scaling-compute-resources"></a>Escala de los recursos de cálculo

Con [la escala automática](batch-automatic-scaling.md), puede tener el servicio de lote ajustar dinámicamente el número de nodos de cálculo en un grupo según el uso actual de carga de trabajo y recursos de su situación de cálculo. Esto le permite reducir el costo total de la ejecución de la aplicación con sólo los recursos que necesita y soltarlas que no necesite.

Habilitar el ajuste de escala automático escribiendo una [fórmula de escala automático](batch-automatic-scaling.md#automatic-scaling-formulas) y asociar dicha fórmula con un grupo. El servicio de lote utiliza la fórmula para determinar el número de nodos en el grupo de destino para el siguiente intervalo de escala (un intervalo en el que puede configurar). Puede especificar la configuración de escala automática para un grupo cuando se crea o activar la escala en un grupo más adelante. También puede actualizar la configuración de escalado de un grupo habilitado para el ajuste de escala.

Como ejemplo, tal vez un trabajo requiere que envíe a un gran número de tareas que deben ejecutarse. Puede asignar una fórmula escala al grupo que se ajusta el número de nodos en del grupo basado en el número de tareas en cola actual y la tasa de finalización de las tareas en el trabajo. El servicio de lote periódicamente evalúa la fórmula y cambia el tamaño de la agrupación, en función de la carga de trabajo (nodos de muchas de las tareas en cola de agregar y quitar nodos de ninguna tarea en cola o se está ejecutando) y la configuración de fórmula.

Una fórmula de escalado se puede basar en las siguientes métricas:

- **Métrica de tiempo** se basan en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métrica de recursos** se basan en el uso de CPU, uso de ancho de banda, uso de la memoria y número de nodos.

- **Métrica de tareas** se basan en el estado de la tarea, como *activo* (en cola), *Ejecutar*o *completadas*.

Cuando la escala automática disminuye el número de nodos de cálculo en un grupo, debe tener en cuenta cómo tratar las tareas que se están ejecutando en el momento de la operación de disminución. Para ello, lote proporciona una *opción de cancelación de asignación de nodo* que puede incluir en las fórmulas. Por ejemplo, puede especificar que ejecutar las tareas se detiene inmediatamente, detenerse inmediatamente y, a continuación, tenía para la ejecución en otro nodo o permiso para finalizar antes de que se quita el nodo del grupo.

Para obtener más información sobre la escala automáticamente una aplicación, vea [automáticamente escala nodos de cálculo en un grupo por lotes de Azure](batch-automatic-scaling.md).

> [AZURE.TIP] Para maximizar la utilización de recursos de cálculo, establezca el número de destino de nodos cero al final de una tarea, pero permitir la ejecución de tareas para finalizar.

## <a name="security-with-certificates"></a>Seguridad con los certificados

Normalmente debe usar certificados al cifrar o descifrar información confidencial de tareas, como la clave para una [cuenta de almacenamiento de Azure][azure_storage]. Para ello, puede instalar certificados en los nodos. Información confidencial cifrada se pasan a las tareas a través de los parámetros o incrustados en uno de los recursos de la tarea, y los certificados instalados pueden usarse para descifrar los archivos.

Usar el [complemento certificado] [ rest_add_cert] operación (lote REST) o [CertificateOperations.CreateCertificate] [ net_create_cert] método (lote .NET) para agregar un certificado a una cuenta de lote. A continuación, puede asociar el certificado a un grupo nuevo o existente. Cuando un certificado está asociado a un grupo, el servicio de lote instala el certificado en cada nodo del grupo. El servicio de lote instala los certificados apropiados cuando se inicia el nodo, antes de iniciar las tareas (incluidas las tareas de inicio y trabajo manager).

Si agrega certificados a un grupo *existente* , debe reiniciar sus nodos de cálculo para los certificados que se aplique a los nodos.

## <a name="error-handling"></a>Control de errores

Es posible necesarias para controlar los errores de tarea y aplicación dentro de su solución de lote.

### <a name="task-failure-handling"></a>Control de errores de tareas
Errores de tareas se dividen en estas categorías:

- **Errores de programación**

    Si la transferencia de archivos que se especifican para una tarea falla por cualquier motivo, se establece un "error de programación" de la tarea.

    Errores de programación puede ocurrir si se han movido los archivos de recursos de la tarea, la cuenta de almacenamiento ya no está disponible o se encontró otro problema que impide la copia correcta de archivos en el nodo.

- **Errores de aplicación**

    También puede fallar el proceso especificado por línea de comandos de la tarea. El proceso se considera que ha fallado cuando se devuelve un código de salida distinto de cero por el proceso que se ejecuta la tarea (vea *códigos de salida de tareas* en la sección siguiente).

    Errores de aplicación, puede configurar lote para reintentar automáticamente la tarea hasta un número determinado de veces.

- **Errores de restricción**

    Puede establecer una delimitación que especifica la duración de ejecución máximo para una tarea o una tarea, la *maxWallClockTime*. Esto puede ser útil para finalizar tareas "colgado".

    Cuando se ha superado la cantidad máxima de tiempo, la tarea se marca como *completado*, pero el código de salida se establece en `0xC000013A` y el campo *schedulingError* está marcado como `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Depuración de errores de aplicación

- `stderr`y`stdout`

    Durante la ejecución, una aplicación podría producir resultados de diagnóstico que puede utilizar para solucionar problemas. Como se mencionó en la sección anterior, [archivos y directorios](#files-and-directories), el servicio de lote escribe salida estándar y salida de error estándar para `stdout.txt` y `stderr.txt` archivos en el directorio de la tarea en el nodo de cálculo. Puede usar el portal de Azure o uno de los SDK lote para descargar estos archivos. Por ejemplo, puede recuperar estos y otros archivos para solucionar problemas utilizando [ComputeNode.GetNodeFile] [ net_getfile_node] y [CloudTask.GetNodeFile] [ net_getfile_task] en la biblioteca de .NET lote.

- **Códigos de salida de la tarea**

    Como se mencionó anteriormente, una tarea está marcada como error por el servicio de lote si el proceso que se ejecuta la tarea devuelve un código de salida distinto de cero. Cuando una tarea ejecuta un proceso, lote rellena la propiedad de código de salida de la tarea con el *código del proceso de retorno*. Es importante que tenga en cuenta que el código de salida de la tarea es **no** determinado por el servicio de lote--viene determinada por el proceso o el sistema operativo en el que se ejecuta el proceso.

### <a name="accounting-for-task-failures-or-interruptions"></a>Cuentas para los errores de tareas o interrupciones

Tareas en ocasiones pueden producir un error o ser interrumpidas. Puede producir un error en la aplicación de la tarea, puede reiniciar el nodo en el que se ejecuta la tarea o el nodo puede que se quite del grupo durante una operación de cambio de tamaño si se establece la directiva de cancelación de asignación del grupo para quitar los nodos inmediatamente sin tener que esperar tareas Finalizar. En todos los casos, la tarea puede ser automáticamente tenía para la ejecución en otro nodo por lotes.

También es posible para hacer que una tarea deje de responder o requiere demasiado tiempo para ejecutar un problema intermitente. Puede establecer el tiempo máximo de ejecución de una tarea. Si se supera, lote interrumpe la aplicación de la tarea.

### <a name="connecting-to-compute-nodes"></a>Conectar nodos de cálculo

Puede realizar adicionales de depuración y solución de problemas de inicio de sesión en un nodo de cálculo de forma remota. Puede usar el portal de Azure para descargar un archivo de protocolo de escritorio remoto (RDP) para los nodos de Windows y obtener información de conexión de Shell seguro (SSH) para los nodos de Linux. También puede hacer esto mediante las API de lote: por ejemplo, con el [Lote .NET] [ net_rdpfile] o [Lote Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Para conectarse a un nodo a través de RDP o SSH, primero debe crear un usuario en el nodo. Para ello, puede usar Azure portal, [Agregar una cuenta de usuario a un nodo] [ rest_create_user] mediante el uso de la API de REST de proceso por lotes, llame a la [ComputeNode.CreateComputeNodeUser] [ net_create_user] método .NET lote o llamada la [add_user] [ py_add_user] método en el módulo de lote Python.

### <a name="troubleshooting-bad-compute-nodes"></a>Solución de problemas "mala" nodos de cálculo

En situaciones donde algunas de las tareas se producen errores, la aplicación cliente de proceso por lotes o servicio puede examinar los metadatos de las tareas para identificar un nodo que ha fallado. Cada nodo de un grupo se expresa un identificador único y, a continuación, el nodo en el que se ejecuta una tarea se incluye en los metadatos de la tarea. Cuando haya identificado un nodo del problema, puede realizar varias acciones con él:

- **Reinicie el nodo** ([REST][rest_reboot] | [.NET][net_reboot])

    Reiniciar el nodo a veces puede solucionar problemas de latencia como procesos atascados o se ha bloqueado. Tenga en cuenta que si el grupo utiliza una tarea de inicio o el trabajo de una tarea de la tarea de preparación, se ejecutan cuando se reinicia el nodo.

- **Crear una nueva imagen el nodo** ([REST][rest_reimage] | [.NET][net_reimage])

    Esto se vuelve a instalar el sistema operativo en el nodo. Al igual que con reiniciar un nodo, iniciar las tareas y tareas de preparación de trabajo se vuelva a ejecutar después de que se ha renovado el nodo.

- **Quitar el nodo del grupo** ([REST][rest_remove] | [.NET][net_remove])

    A veces es necesario quitar completamente el nodo del grupo.

- **Deshabilitar el nodo de programación de tareas** ([REST][rest_offline] | [.NET][net_offline])

    Este eficaz toma el nodo "sin conexión" para que no hay más tareas están asignados a ella, pero permite que el nodo permanezca ejecución y en el grupo. Esto le permite llevar a cabo una investigación más de la causa de los errores sin perder los datos de la tarea ha fallado--y sin el nodo causando errores de tareas adicionales. Por ejemplo, puede deshabilitar la programación en el nodo, a continuación, [inicie sesión en forma remota](#connecting-to-compute-nodes) para examinar los registros de eventos del nodo o solucionar otros problemas de las tareas. Cuando termine la investigación, a continuación, puede reunir en línea el nodo habilitando la programación de tareas ([resto][rest_online] | [.NET][net_online]), o realice una de las otras acciones que se indicó anteriormente.

> [AZURE.IMPORTANT] Con cada acción que se describe en esta sección: reiniciar, crear una nueva imagen, quitar y deshabilitar la programación de tareas, es posible especificar cómo se administran las tareas que se están ejecutando actualmente en el nodo al realizar la acción. Por ejemplo, cuando se deshabilita la programación de tareas en un nodo mediante el uso de la biblioteca de cliente .NET por lotes, puede especificar un [DisableComputeNodeSchedulingOption] [ net_offline_option] valor de enumeración para especificar si para **Finalizar** tareas en ejecución, **poner** para programación en otros nodos o permitir la ejecución de tareas para completar antes de realizar la acción (**TaskCompletion**).

## <a name="next-steps"></a>Pasos siguientes

- Recorra una aplicación de proceso por lotes de ejemplo paso a paso de [Introducción a la biblioteca por lotes de Azure para .NET](batch-dotnet-get-started.md). También hay una [versión de Python](batch-python-tutorial.md) del tutorial que se ejecuta una carga de trabajo en los nodos de cálculo Linux.

- Descargar y generar el [Lote Explorer] [ github_batchexplorer] proyecto de ejemplo para su uso mientras desarrolla soluciones lote. Mediante el explorador por lotes, puede realizar el siguiente y mucho más:
  - Supervisar y manipular grupos, tareas y tareas en su cuenta por lotes
  - Descargar `stdout.txt`, `stderr.txt`y otros archivos de nodos
  - Crear usuarios en nodos y descargar archivos RDP para inicio de sesión remota

- Obtenga información sobre cómo [crear grupos de nodos de cálculo de Linux](batch-linux-nodes.md).

- Visite el [foro de Azure lote] [ batch_forum] en MSDN. El foro es un buen lugar para hacer preguntas, si acaba de aprendizaje o es un experto en el uso de lote.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
