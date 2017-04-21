<properties 
    pageTitle="Cargar datos en entornos de almacenamiento para análisis | Microsoft Azure" 
    description="Mover datos a y desde el almacenamiento de blobs de Windows Azure" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="bradsev" />

# <a name="load-data-into-storage-environments-for-analytics"></a>Cargar datos en entornos de almacenamiento para análisis

El proceso de ciencias de datos de equipo requiere ingestión datos o cargados en una variedad de entornos de almacenamiento diferente que se procesan o analizados en la forma más adecuada en cada fase del proceso. Destinos de datos usados para el procesamiento de incluyen almacenamiento de blobs de Azure, bases de datos de SQL Azure, SQL Server en máquina virtual de Azure, HDInsight (Hadoop) y aprendizaje del equipo de Azure. 

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Vínculos de este **menú** a temas que describen cómo recopilar datos en estos entornos de destino donde se almacena y se procesan los datos.

Técnica y necesidades empresariales, así como la ubicación inicial, formato y tamaño de los datos determinará los entornos de destino en la que los datos se necesitan recopilar para lograr los objetivos de su análisis. No es raro para un escenario que requieren datos se mueven entre varios entornos para lograr la variedad de tareas necesarias para construir un modelo de predicción. Por ejemplo, esta secuencia de tareas puede incluir la exploración de datos, preprocesamiento, limpieza, muestreo inferior y formación del modelo.
