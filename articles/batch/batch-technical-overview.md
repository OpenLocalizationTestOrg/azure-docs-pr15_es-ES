<properties
    pageTitle="Conceptos básicos de servicio de lote Azure | Microsoft Azure"
    description="Aprenda a usar el servicio de Azure lote por paralelo a gran escala y las cargas de trabajo HPC"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Conceptos básicos del lote de Azure

Lote de Azure le permite ejecutar aplicaciones a gran escala paralelo y de alto rendimiento informática (HPC) de forma eficiente en la nube. Es un servicio de plataforma que programa el trabajo de descarga intensiva para que se ejecute en una colección de máquinas virtuales administrada y puede automáticamente escala calcular recursos para satisfacer las necesidades de los trabajos.

El servicio por lotes, se definen los recursos de cálculo Azure para ejecutar las aplicaciones en paralelo y, en escala. Puede ejecutar a petición o programada trabajos y que no necesita crear manualmente, configurar y administrar un clúster HPC, máquinas virtuales individuales, redes virtuales o un trabajo complejo e infraestructura de programación de tareas.

## <a name="use-cases-for-batch"></a>Casos de uso para por lotes

Proceso por lotes es un servicio de Azure administrado que se usa para el *proceso por lotes* o *lote informática*ejecutando un gran volumen de tareas similares para obtener algunos resultado deseado. Informática lote es más utilizada por las organizaciones que procesarán, transforman y analizar grandes volúmenes de datos con regularidad.

Lote funciona bien con cargas de trabajo y esencia paralelas (también conocido como "una") aplicaciones paralelas. Cargas de trabajo paralelas esencia fácilmente se dividen en varias tareas que realizan el trabajo de forma simultánea en varios equipos.

![Tareas paralelas][1]<br/>

Algunos ejemplos de cargas de trabajo que se procesan suelen usar esta técnica son:

* Modelado de riesgos financieros
* Clima e hidrología análisis de datos
* Procesamiento, análisis y representación de imagen
* Codificación de medios y transcodificación
* Análisis de secuencia genética
* Análisis de carga de ingeniería
* Pruebas de software

Lote también puede realizar cálculos paralelo con un paso de reducir al final y ejecutar cargas de trabajo HPC más complejas, como las aplicaciones de la [Interfaz de paso de mensajes (MPI)](batch-mpi.md) .

Para ver una comparación entre el lote y otras opciones de solución HPC en Azure, vea [soluciones lote y la informática](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Desarrollo de proceso por lotes

Procesamiento de cargas de trabajo paralelas con lote suele hacerse mediante programación utilizando una de las [API de lote](#batch-development-apis). Con la API por lotes, crear y administrar grupos de nodos de cálculo (máquinas virtuales) y programar trabajos y las tareas para que se ejecute en los nodos. Una aplicación de cliente o servicio que crear utiliza las API de lote para comunicarse con el servicio de lote.

Eficaz puede procesar las cargas de trabajo a gran escala para su organización o proporcionar un front-end de servicio a sus clientes para que pueden ejecutar tareas--a petición o en una programación--en uno, cientos o incluso miles de nodos. También puede usar el proceso por lotes como parte de un flujo de trabajo más grande, administrada con herramientas como [Generador de datos de Azure](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Cuando esté listo para profundizar a la API de lote para obtener una descripción de las características que proporciona información más detallada, consulte la [información general de la característica de lote para desarrolladores](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Cuentas de Azure que necesitará

Al desarrollar soluciones de proceso por lotes, deberá usar las cuentas siguientes en Microsoft Azure.

- **Cuenta de azure y de suscripción** : si todavía no tiene una suscripción de Azure, puede activar los [suscriptores MSDN aprovecharse][msdn_benefits], o suscribirse a una [cuenta gratuita de Azure][free_account]. Cuando se crea una cuenta, se creará una suscripción de forma predeterminada.

- **Cuenta de proceso por lotes** , cuando las aplicaciones interactúan con el servicio de proceso por lotes, el nombre de cuenta, la dirección URL de la cuenta y una tecla de acceso se usan como credenciales. Todos los recursos del lote como grupos de nodos de cálculo, trabajos, y las tareas están asociadas a una cuenta de proceso por lotes. Puede [Crear lote cuenta](batch-account-create-portal.md) en el portal de Azure.

- **Cuenta de almacenamiento** - lote incluye compatibilidad integrada para trabajar con archivos de [Almacenamiento de Azure][azure_storage]. Casi todas las situaciones lote usan almacenamiento de Azure--para los programas que se ejecutan las tareas y los datos de procesamiento de ensayo y para el almacenamiento de datos de salida que generan. Para crear una cuenta de almacenamiento, consulte [acerca de Azure cuentas de almacenamiento](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>Desarrollo de lote API

Las aplicaciones y servicios pueden emitir llamadas API de REST directas, utilice uno o más de las siguientes bibliotecas de cliente o una combinación de ambos para administrar calcular recursos y ejecutar las cargas de trabajo paralelas a escala con el servicio de lote.

| API    | Referencia de la API | Descargar | Ejemplos de código |
| ----------------- | ------------- | -------- | ------------ |
| **Lote REST** | [MSDN][batch_rest] | N/A. | [MSDN][batch_rest] |
| **Lote .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Python por lotes**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Node.js por lotes** | [github.IO][api_nodejs] | [NPM][api_nodejs_npm] | - |
| **Lote Java** (vista previa) | [github.IO][api_java] | [Experto][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Administración de recursos por lotes

Además de la API de cliente, también puede usar las siguientes acciones para administrar los recursos dentro de su cuenta de lote.

- [Cmdlets de PowerShell de lotes][batch_ps]: cmdlets el lote de Azure en el módulo de [PowerShell de Azure](../powershell-install-configure.md) le permiten administrar recursos por lotes con PowerShell.

- [Azure CLI](../xplat-cli-install.md): el Azure de línea de comandos interfaz Azure es un conjunto de herramientas de varias plataformas que proporciona comandos de shell para interactuar con muchos de los servicios de Azure, incluidos el lote.

- Biblioteca de cliente [.NET de administración de lote](batch-management-dotnet.md) : también está disponible mediante [NuGet][api_net_mgmt_nuget], puede usar la biblioteca de cliente .NET de administración de proceso para administrar mediante programación cuentas por lotes, cuotas y paquetes de aplicaciones. Referencia de la biblioteca de administración es en [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Herramientas de proceso por lotes

Aunque no es necesario para crear soluciones con lote, estas son algunas herramientas valiosas para usar al generar y depurar los servicios y las aplicaciones de proceso por lotes.

 - [Portal de Azure][portal]: puede crear, supervisar y eliminar grupos por lotes, tareas y tareas de módulos del lote del portal de Azure. Puede ver la información de estado para estos y otros recursos mientras ejecuta los trabajos e incluso descargar archivos desde los nodos de cálculo de los grupos (descargar una tarea ha fallado `stderr.txt` durante la solución de problemas, por ejemplo). También puede descargar archivos de escritorio remoto (RDP) que puede usar para iniciar sesión en nodos de cálculo.

 - [Explorador de Azure lote][batch_explorer]: lote Explorer proporciona lote recursos administración funciones similares como el portal de Azure, pero en una aplicación de cliente de Windows Presentation Foundation (WPF) independiente. Una de las aplicaciones de ejemplo .NET lote disponibles en [GitHub][github_samples], puede generar con Visual Studio de 2015 o posterior y usarlo para explorar y administrar los recursos en su cuenta por lotes mientras desarrollar y depurar las soluciones de lote. Trabajo de vista, grupo y detalles de la tarea, descargar archivos de nodos de cálculo y conectan a nodos de forma remota mediante archivos de escritorio remoto (RDP) puede descargar con el Explorador de lote.

 - [Explorador de Microsoft Azure almacenamiento][storage_explorer]: mientras no estrictamente una herramienta de Azure lote, el Explorador de almacenamiento es otra herramienta valiosa tener mientras desarrolla y sus soluciones lote de depuración.

## <a name="scenario-scale-out-a-parallel-workload"></a>Escenario: Escalado una carga de trabajo en paralelo

Una solución común que utiliza las API de lote para interactuar con el servicio de lote implica escalado trabajo esencia paralelo, como la representación de imágenes para escenas 3D--en un grupo de nodos de cálculo. Este grupo de nodos de cálculo puede ser la "granja de representación" que proporciona decenas, cientos o incluso miles de núcleos de su trabajo representación, por ejemplo.

El siguiente diagrama muestra un flujo de trabajo por lotes comunes, con una aplicación de cliente o un servicio hospedado utilizando lote para ejecutar una carga de trabajo en paralelo.

![Flujo de trabajo de solución de proceso por lotes][2]

En este escenario común, la aplicación o servicio procesa una carga de trabajo de cálculo en el lote de Azure realizando los siguientes pasos:

1. Cargue los **archivos de entrada** y la **aplicación** que procesa esos archivos a su cuenta de almacenamiento de Azure. Los archivos de entrada pueden ser cualquier dato que procesa la aplicación, como los datos de modelado financiero o archivos de vídeo pueden transformar. Los archivos de la aplicación pueden ser cualquier aplicación que se usa para el procesamiento de los datos, como una aplicación de procesamiento de 3D o Transcodificador de medios.

2. Crear un lote del **grupo** de nodos de cálculo en su cuenta por lotes--estos nodos son los equipos virtuales que se ejecutarán las tareas. Especificar propiedades como el [tamaño de nodo](./../cloud-services/cloud-services-sizes-specs.md), su sistema operativo y la ubicación en el almacenamiento de Azure de la aplicación para instalar cuando los nodos unirse a la agrupación (la aplicación que ha cargado en el paso 1 #). También puede configurar el grupo [cambiar automáticamente la escala](batch-automatic-scaling.md)--dinámicamente ajustar el número de nodos de cálculo en el grupo--en respuesta a la carga de trabajo que generan las tareas.

3. Crear un lote de **trabajo** para ejecutar la carga de trabajo en el grupo de nodos de cálculo. Cuando se crea una tarea, asociarlo con un grupo de lotes.

4. Agregar **tareas** a la tarea. Al agregar tareas a una tarea, el servicio de lote programa automáticamente las tareas de ejecución en los nodos de cálculo en el grupo. Cada tarea utiliza la aplicación que ha cargado para procesar los archivos de entrada.

    - 4. Antes de que se ejecuta una tarea, puede descargar los datos (los archivos de entrada) que resulta al proceso para que se le haya asignado el nodo de cálculo. Si la aplicación no ya se instaló en el nodo (vea el paso 2 #), se puede descargar aquí en su lugar. Cuando haya completado la descarga, las tareas se ejecutan en los nodos asignados.

5. Mientras ejecutan las tareas, puede crear una consulta por lotes para supervisar el progreso de la tarea y sus tareas. La aplicación cliente o servicio se comunica con el servicio de lote sobre HTTPS y porque puede supervisar miles de tareas que se ejecutan en miles de nodos de cálculo, asegúrese de [consultar el servicio de lote de forma eficaz](batch-efficient-list-queries.md).

6. Como las tareas completadas, pueden cargar sus datos de resultado en el almacenamiento de Azure. También puede recuperar archivos directamente desde los nodos de cálculo.

7. Cuando la supervisión detecta que se han completado las tareas de su trabajo, la aplicación cliente o servicio puede descargar los datos de salida de transformación o de evaluación.

Tenga en cuenta esto es solo una forma de usar el lote y, a continuación, este escenario describe sólo algunos de sus características disponibles. Por ejemplo, puede ejecutar [varias tareas en paralelo](batch-parallel-node-tasks.md) en cada nodo de cálculo, y puede usar [las tareas de preparación y finalización de tarea](batch-job-prep-release.md) para preparar los nodos de los trabajos y luego limpiar después.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene una descripción general del servicio de proceso por lotes, es el momento para profundizar más información sobre cómo utilizar para procesar las cargas de trabajo paralelas descarga intensiva.

- Lea la [información general de la característica de lote para desarrolladores](batch-api-basics.md), información esencial para cualquier persona prepararse para utilizar el lote. El artículo contiene información más detallada sobre los recursos de servicio de lote como grupos, nodos, trabajos y las tareas y la muchas características de la API que puede usar durante la compilación de la aplicación del lote.

- [Introducción a la biblioteca de Azure lote para .NET](batch-dotnet-get-started.md) para aprender a utilizar C# y la biblioteca de .NET lote para ejecutar una carga de trabajo simple con un flujo de trabajo de lote común. En este artículo debe ser uno de su primera puntos mientras aprender a usar el servicio de lote. También hay una [versión de Python](batch-python-tutorial.md) del curso.

- Descargar los [ejemplos de código en GitHub] [ github_samples] para ver cómo C# y Python puede interfaz con lote a cargas de trabajo de ejemplo de programación y proceso.

- Consulte la [Ruta de aprendizaje de lote] [ learning_path] para hacerse una idea de los recursos disponibles para usted, aprenda a trabajar con el lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
