<properties
    pageTitle="Módulo de PowerShell para el aprendizaje | Microsoft Azure"
    description="El módulo de PowerShell para el aprendizaje Azure está disponible en modo de vista previa público. Usar PowerShell para crear y administrar áreas de trabajo, experimentación, servicios web y mucho más."
    keywords="experimentar regresión lineal, algoritmos de aprendizaje de equipo, tutorial de aprendizaje de equipo, técnicas de modelado de predicción, ensayo de ciencias de datos"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Módulo de PowerShell de aprendizaje de Microsoft Azure máquina

El módulo de PowerShell para el aprendizaje Azure es una herramienta eficaz que le permite usar Windows PowerShell para administrar áreas de trabajo, experimentación, conjuntos de datos, web (biofeedback) y.

Puede ver la documentación y descargue el módulo, junto con el código fuente completo, en [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>¿Qué es el módulo de PowerShell de aprendizaje de equipo?

El módulo de PowerShell de aprendizaje del equipo es una. Módulo DLL basada en red que le permite administrar completamente el aprendizaje Azure áreas de trabajo, experimentación, conjuntos de datos, servicios web y extremos de servicios web de Windows PowerShell. Junto con el módulo, puede descargar el código fuente completo que incluye una más limpia separados [capa API de C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Esto significa que puede hacer referencia a este archivo DLL desde su propio proyecto .NET y administrar el aprendizaje Azure a través del código de .NET. Además, la DLL depende de la API de REST subyacentes que usted puede aprovechar directamente desde el cliente de favorito.

## <a name="what-can-i-do-with-the-powershell-module"></a>¿Qué puedo hacer con el módulo de PowerShell?

Estas son algunas de las tareas que puede realizar con este módulo de PowerShell. Consulte la [documentación completa](https://aka.ms/amlps) para éstas y muchas más funciones.

- Aprovisionar a un área de trabajo con un certificado de administración ([Nuevo AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exportar e importar un archivo JSON que representa un gráfico de ensayo ([Exportar AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) y [AmlExperimentGraph de importación](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Ejecutar un ensayo ([AmlExperiment de inicio](https://github.com/hning86/azuremlps#start-amlexperiment))
- Crear un servicio web fuera de un ensayo predictivo ([Nuevo AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Crear un nuevo punto final en un servicio web publicado ([Agregar AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Invocar un extremo de servicio de web RR o BES ([Invocar AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invocar AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Este es un ejemplo rápido de usar PowerShell para ejecutar un ensayo existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para un caso de uso más en profundidad, vea el artículo sobre el uso del módulo de PowerShell para automatizar una tarea muy más solicitados: [crear muchos modelos de aprendizaje y web extremos de servicio de un ensayo con PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>¿Cómo empezar?

Para empezar con Machine aprendizaje PowerShell, descargue el [paquete de lanzamiento](https://github.com/hning86/azuremlps/releases) de GitHub y siga las [instrucciones de instalación](https://github.com/hning86/azuremlps/blob/master/README.md). Debe desbloquear la DLL descargado de descomprimir y, a continuación, importarla a su entorno de PowerShell. La mayoría de los cmdlets de requiere que proporcione el identificador de área de trabajo, el token de autorización de área de trabajo y la región de Azure que en el área de trabajo. La manera más sencilla de proporcionar estos es a través de un archivo de config.json de forma predeterminada, que se trata en detalle en las instrucciones de instalación. Por supuesto, también puede clonar el árbol git y modificar y compilar el código localmente mediante Visual Studio.

## <a name="next-steps"></a>Pasos siguientes

El módulo de PowerShell seguirá mejorado y expandido durante este período de vista previa. Vigile la [inteligencia de Cortana y el Blog de aprendizaje del equipo](https://blogs.technet.microsoft.com/machinelearning/) para obtener más información y noticias.
