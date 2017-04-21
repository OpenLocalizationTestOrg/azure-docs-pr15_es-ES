<properties
    pageTitle="Cómo identificar los escenarios y planear avanzado procesamiento del análisis de datos | Microsoft Azure"
    description="Plan para análisis avanzado partiendo de una serie de preguntas clave."
    services="machine-learning"
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


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Cómo identificar los escenarios y planear de procesamiento de datos de análisis avanzadas

¿Debe los recursos que desea incluir al configurar un entorno para realizar análisis avanzado procesamiento en un conjunto de datos? En este artículo se sugiere una serie de preguntas que le ayudarán a identificar las tareas y recursos relevantes del escenario. El orden de los pasos para análisis predictivo tiene un contorno de [¿Qué es el proceso de ciencias de datos de equipo (TDSP)?](data-science-process-overview.md). Cada uno de estos pasos requieren recursos específicos para las tareas relacionadas con la situación particular. Logística de datos, las características, la calidad de los conjuntos de datos y las herramientas y los idiomas que desee hacer el análisis refieren a las preguntas clave para identificar su situación.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Preguntas logísticas: movimiento y las ubicaciones de datos
Las preguntas logísticas refieren a la ubicación de **origen de datos**, el **destino de destino** en Azure y los requisitos para mover los datos, incluidos la programación, importe y recursos relacionados. Los datos que necesite mover varias veces durante el proceso de análisis. Un escenario común es mover datos locales a algún tipo de almacenamiento en Azure y, a continuación, en equipo aprendizaje Studio.

1. **¿Qué es el origen de datos?** ¿Es local o en la nube? Por ejemplo:
    - Los datos están en una dirección HTTP esté disponibles públicamente.
    - Los datos residen en una ubicación de archivo o red local.
    - Los datos están en una base de datos de SQL Server.
    - Los datos se almacenan en un contenedor de almacenamiento de Azure

2. **¿Qué es el destino de Azure?** ¿Cuando sea necesario procesar o modelado? Por ejemplo:
    - Almacenamiento de blobs de Windows Azure
    - Bases de datos de SQL Azure
    - SQL Server en máquina virtual de Azure
    - HDInsight (Hadoop en Azure) o las tablas de la sección
    - Aprendizaje del equipo de Azure
    - Montaje en Azure virtual disco duro.

3. **¿Cómo va a mover los datos?** En los temas siguientes se describen los procedimientos y recursos disponibles para recopilar o cargar datos en una gran variedad de almacenamiento diferente y entornos de proceso.

    -  [Cargar datos en entornos de almacenamiento para análisis](machine-learning-data-science-ingest-data.md)
    -  [Importar datos de formación en Azure máquina aprendizaje Studio desde varios orígenes de datos](machine-learning-data-science-import-data.md).

4. **¿Los datos deben ser movido de forma regular o modificadas durante la migración?** Considere la posibilidad de usar el generador de datos de Azure (ADF) al datos deben migrarse continuamente, especialmente si un escenario híbrido que tiene acceso a ambos local y participa recursos de la nube, o cuando los datos se lleva a cabo o deben modificarse o tiene lógica de negocios agregada durante el que se migran. Para obtener más información, vea [mover datos desde un servidor SQL de local a SQL Azure con el generador de datos de Azure](machine-learning-data-science-move-sql-azure-adf.md)

5. **¿La cantidad de datos es se muevan a Azure?** Conjuntos de datos que son muy grandes pueden superar la capacidad de almacenamiento de ciertos entornos. Para obtener un ejemplo, consulte la descripción de los límites de tamaño de máquina aprendizaje Studio en la siguiente sección. En estos casos, puede utilizarse una muestra de los datos durante el análisis. Para obtener información detallada de abajo muestra cómo un conjunto de datos en distintos entornos de Azure, vea [datos de ejemplo en el proceso de ciencias de datos de grupo](machine-learning-data-science-sample-data.md).


## <a name="data-characteristics-questions-type-format-and-size"></a>Preguntas de las características de datos: tipo, formato y tamaño
Estas preguntas son clave para el almacenamiento de planificación y procesamiento entornos, cada una de las cuales son las más apropiadas para distintos tipos de datos y cada una de las cuales tiene algunas restricciones.

1. **¿Cuáles son los tipos de datos?** Por ejemplo:
    - Numérico
    - Por categorías
    - Cadenas
    - Binario

2. **¿Cómo se aplica el formato de los datos?** Por ejemplo:
    - Valores separados por comas (CSV) o delimitado por tabulaciones (TSV) los archivos sin formato
    - Comprimido o sin comprimir
    - Azure BLOB
    - Tablas de la sección de Hadoop
    - Tablas de SQL Server

2. **¿Cómo grandes están sus datos?**
    - Pequeño: menos de 2GB
    - Medio: Superiores a 2GB y menos de 10GB
    - Grande: Mayores de 10GB

Tomar el entorno de Azure máquina aprendizaje Studio por ejemplo:

- Para obtener una lista de los formatos de datos y tipos admitidos por Azure máquina aprendizaje Studio, consulte la sección [tipos de datos y formatos de datos compatibles](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) .
- Para obtener información sobre las limitaciones de datos de Azure máquina aprendizaje Studio, consulte la **el tamaño del conjunto de datos puede para mi módulos?** sección de [Importar y exportar datos de aprendizaje](machine-learning-faq.md#machine-learning-studio-questions)

Para obtener información sobre las limitaciones de otros servicios de Azure utilizados en el proceso de análisis, consulte [suscripción de Azure y los límites del servicio, cuotas y las restricciones](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Preguntas de calidad de datos: exploración y preprocesamiento

1. **¿Lo que saber sobre los datos?** Explorar datos cuando se debe tener un comprender sus características básicas. ¿Qué patrones o tendencias presentaciones, ¿qué es valores atípicos tienen o faltan cuántos valores. Este paso es importante para determinar el grado de preprocesamiento que sea necesario para formular hipótesis que podrían sugerir las características más adecuadas o tipo de análisis y para elaborar el plan de recopilación de datos adicionales. Calcular la estadística descriptiva y trazar visualizaciones son técnicas útiles para inspección de datos. Para obtener más información sobre cómo explorar un conjunto de datos en distintos entornos de Azure, vea [explorar datos en el proceso de ciencias de datos de grupo](machine-learning-data-science-explore-data.md).

2. **¿Los datos requiere preprocesamiento o limpieza?**
Preprocesamiento y limpieza de datos son tareas importantes que normalmente deben llevarse a cabo antes de conjunto de datos se puede utilizar eficazmente para el aprendizaje. Datos sin formato suele ruido y poco confiables y le falte valores. Usar este tipo de datos para un modelado, puede producir resultados confusos. Para obtener una descripción, vea [tareas para preparar datos de equipo mejorada de aprendizaje](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Preguntas de idiomas y herramientas
Hay muchas opciones aquí dependiendo de qué idiomas y entornos de desarrollo o herramientas necesita o están usando más conformable.

1. **¿Qué idiomas que desee usar para el análisis**  
    - R
    - Python
    - SQL

2. **¿Qué herramientas debe utilizar para el análisis de datos?**
    - [Microsoft Azure Powershell](powershell-install-configure.md) - un lenguaje de secuencia de comandos utilizado para administrar los recursos de Azure en un lenguaje de secuencias de comandos.
    - [Máquina Azure aprendizaje Studio](machine-learning-what-is-ml-studio/)
    - [Análisis de revolución](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Python Tools para Visual Studio](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Blocs de notas Jupyter](http://jupyter.org/)
    - [Microsoft Power BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>Identificar el escenario de análisis avanzadas
Una vez que haya respondido a las preguntas en la sección anterior, ya está listo para determinar qué escenario que mejor se adapte a su caso. Escenarios de ejemplo se clasifican en [escenarios para análisis avanzado en Azure el aprendizaje](machine-learning-data-science-plan-sample-scenarios.md).
