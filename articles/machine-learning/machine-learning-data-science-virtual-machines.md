<properties
    pageTitle="Máquinas virtuales de ciencias de datos de Azure | Microsoft Azure"
    description="Establecer el una máquina Virtual de datos de ciencias"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Máquinas virtuales de ciencias de datos de Azure

A continuación se proporcionan instrucciones que describen cómo configurar una máquina virtual de Azure y una máquina virtual de Azure con el servicio SQL como servidores de Bloc de notas de IPython. La máquina virtual de Windows está configurada con herramientas como el Bloc de notas de IPython, Explorador de almacenamiento de Azure y AzCopy, así como otras utilidades que son útiles para proyectos de ciencias de datos de soporte. Explorador de almacenamiento de Azure y AzCopy, por ejemplo, proporcionan cómodas formas para cargar datos en el almacenamiento de Azure desde el equipo local o descargar en el equipo local de almacenamiento. 

Vínculos de este menú a temas que describen cómo configurar los diversos entornos de ciencias de datos usados por el [Proceso de ciencias de datos de equipo (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Varios tipos de máquinas virtuales de Windows Azure pueden aprovisionar y configurar para usarse como parte de un entorno de ciencias datos basados en la nube. La decisión sobre qué tipo de máquina virtual de utilizar depende del tipo y la cantidad de datos modelar con el aprendizaje y el destino para los datos en la nube. 

* Para obtener información sobre las cuestiones a tener en cuenta al tomar esta decisión, vea [Planear su equipo aprendizaje datos ciencias entorno de Azure](machine-learning-data-science-plan-your-environment.md). 
* Para un catálogo de algunas de las situaciones que pueden surgir al realizar análisis avanzado, consulte [escenarios para el proceso de análisis avanzadas y tecnología de aprendizaje de máquina de Azure](machine-learning-data-science-plan-sample-scenarios.md)

Se proporcionan dos conjuntos de instrucciones:

* [Configurar una máquina virtual Azure como un servidor de Bloc de notas de IPython para análisis avanzado](machine-learning-data-science-setup-virtual-machine.md) muestra cómo aprovisionar una máquina virtual Azure con Bloc de notas de IPython y otras herramientas utilizadas para hacer ciencias de datos para los casos en los que puede usarse un formulario de almacenamiento de Azure que no sea de SQL para almacenar los datos.

* [Configurar una máquina virtual de Azure SQL Server como un servidor de Bloc de notas de IPython para análisis avanzado](machine-learning-data-science-setup-sql-server-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de servidor de SQL Azure con Bloc de notas de IPython y otras herramientas utilizadas para hacer ciencias de datos para los casos en que se puede utilizar una base de datos SQL para almacenar los datos.

Una vez configurados y configurado, estas máquinas virtuales están listos para usarse como servidores de Bloc de notas de IPython para la exploración y tratamiento de datos y para otras tareas necesarias junto con el aprendizaje Azure y el proceso de ciencias de datos de equipo (TDSP). Los siguientes pasos en el proceso de ciencias de datos se asignan en la [ruta de aprendizaje de TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y pueden incluir pasos que mover los datos de SQL Server o HDInsight, procesarán y escuche ahí en preparación para aprender de los datos con el aprendizaje del equipo de Azure.


> [AZURE.NOTE] El precio de máquinas virtuales de Windows Azure como **pagar solo por lo que usa**. Para asegurarse de que no se facturación cuando no se utiliza la máquina virtual, tiene que estar en el estado de **detenido (Deallocated)** desde el [Portal de Azure clásica](http://manage.windowsazure.com/). Para que obtener instrucciones paso a paso o cómo desasignar máquina virtual, consulte [apagado y desasignar máquina virtual cuando no esté en uso](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
