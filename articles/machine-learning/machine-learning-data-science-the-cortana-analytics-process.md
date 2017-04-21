<properties 
    pageTitle="¿Qué es el proceso de ciencias de datos de grupo?  | Microsoft Azure" 
    description="El proceso de ciencias de datos de grupo es un método sistemático para generar aplicaciones inteligentes que aprovechan funciones analíticas avanzadas." 
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


# <a name="what-is-the-team-data-science-process-tdsp"></a>¿Qué es el proceso de ciencias de datos de equipo (TDSP)?

El [Proceso de ciencias de datos de equipo (TDSP)](data-science-process-overview.md) proporciona un enfoque sistemático a la creación de aplicaciones inteligentes que permite a los equipos de científicos datos a colaborar de manera efectiva sobre el ciclo de vida de actividades necesarias para activar estas aplicaciones en los productos. La TDSP describe una secuencia de pasos que se proporcionan **instrucciones** sobre cómo definir el problema, configure las herramientas y el entorno necesita analizar datos pertinentes, crear evaluar modelos de predicción y, a continuación, implementar esos modelos en aplicaciones empresariales. 

Estos son los pasos en **Proceso de ciencias de datos de grupo**:  

![Flujo de trabajo de capital](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

El proceso es **iterativo**: la descripción de los nuevos y existentes o mejoras en el modelo de evolucionar y necesita repasar previamente completadas en la secuencia de pasos. Desarrollo de la organización existente y procesos de planificación de proyecto son **con facilidad** para que funcione con la secuencia definida por el TDSP de pasos. 

Los pasos del proceso se ha creado un diagrama y vinculados en la [ruta de aprendizaje de TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y se describe a continuación.  

## <a name="preparation-steps"></a>Pasos de preparación 

## <a name="p1-plan-the-analytics-project"></a>P1. Planear el proyecto de análisis 

Iniciar un proyecto de análisis al definir los objetivos empresariales y problemas. Se especifican en términos de **requisitos empresariales**. Un objetivo central de este paso es identificar las variables de la clave de empresa (previsión de ventas o la probabilidad de una orden ser fraudulenta, por ejemplo) que el análisis debe predecir para satisfacer estos requisitos. Planificación adicional es normalmente esencial para desarrollar una comprensión de los **orígenes de datos** necesarios para tratar los objetivos del proyecto desde la perspectiva de analítica. No es raro, por ejemplo, para encontrar que necesitan sistemas existentes recopilar y registrar los tipos adicionales de datos para solucionar el problema y lograr los objetivos del proyecto. Para obtener instrucciones, vea [Planear el entorno para el proceso de ciencias de datos de equipo](machine-learning-data-science-plan-your-environment.md) y [escenarios para análisis avanzado en Azure el aprendizaje](machine-learning-data-science-plan-sample-scenarios.md).  

## <a name="p2-setup-analytics-environment"></a>P2. Configurar el entorno de análisis 

Un entorno de análisis para el proceso de ciencias de datos de grupo incluye varios componentes: 

- **áreas de trabajo de datos** donde se traslada los datos para análisis y modelado de 
- una **infraestructura de procesamiento** de preprocesamiento, explorar y modelado de los datos
- **infraestructura de tiempo de ejecución** controle la modelos analíticos y ejecutar al cliente inteligente aplicaciones que consumen los modelos.  

La infraestructura de análisis que es necesario configurar con qué frecuencia es parte de un entorno diferente de los sistemas operativos principales. Pero generalmente aprovecha datos de varios sistemas dentro de la empresa, así como de orígenes externos a la empresa. La infraestructura de análisis puede ser puramente en la nube, o una instalación local o un híbrido de los dos. Para las opciones, vea [configurar entornos de ciencias de datos para su uso en el proceso de ciencias de datos de grupo](machine-learning-data-science-environment-setup.md).

## <a name="analytics-steps"></a>Pasos de análisis:  

## <a name="1-ingest-data-into-the-analytical-environment"></a>1. la recopilación de datos en el entorno analítico 

El primer paso es pasar los datos relevantes de diversos orígenes, ya sea desde dentro o desde fuera de la empresa a un analítico entornos donde pueden procesar los datos. El **formato** de los datos de origen puede diferir de formato que requiere el destino. Así que algunos transformación de datos tenga también deben realizarse por las herramientas de recopilación. Para opciones, vea [cargar datos en entornos de almacenamiento para análisis](machine-learning-data-science-ingest-data.md)

Además de la recopilación inicial de los datos, muchas aplicaciones inteligentes se requieren para actualizar los datos con regularidad como parte de un proceso de aprendizaje en curso. Esto puede hacerse mediante la configuración de un flujo de trabajo o la **canalización de datos** . Esto forma parte de la parte del proceso que incluye reconstruir y volver a evaluar los modelos analítica usados por la aplicación inteligente implementar la solución iterativa. Por ejemplo, vea [mover datos desde un servidor SQL de local a SQL Azure con el generador de datos de Azure](machine-learning-data-science-move-sql-azure-adf.md).


## <a name="2-explore-and-pre-process-data"></a>2. explorar y procesar previamente los datos 

El siguiente paso es obtener un conocimiento más profundo de los datos de sus **estadísticas de resumen** , las relaciones, la investigación y mediante técnicas de dicha **visualización**. Esta es también donde se administran los problemas de integridad, como los valores que faltan, errores de coincidencia de tipo de datos y las relaciones de datos incoherentes y **calidad de los datos** . Preprocesamiento transformaciones se usan para limpiar los datos sin formato antes aún más el análisis y modelado tenga lugar. Para obtener una descripción, vea [tareas para preparar datos de equipo mejorada de aprendizaje](machine-learning-data-science-prepare-data.md).


## <a name="3-develop-features"></a>3. características de desarrollo de 

Científicos de datos, en colaboración con expertos de dominio, deben identificar las características que capturar las propiedades del conjunto de datos importantes y que mejor se puede utilizar para predecir las variables clave del negocio identificadas durante la planeación. Estas nuevas características pueden derivarse de los datos existentes o pueden requerir datos adicionales que se van a recopilar. Este proceso se conoce como **ingeniería de la característica** y es uno de los pasos clave en la creación de un sistema de análisis predictiva eficaces. Este paso requiere una combinación creativo de experiencia de dominio y la información que se obtienen desde el paso de exploración de datos. Para obtener instrucciones, vea [características de ingeniería en el proceso de ciencias de datos de grupo](machine-learning-data-science-create-features.md).


## <a name="4-create-predictive-models"></a>4. crear modelos de predicción 

Científicos datos crear modelos analíticos para predecir las variables claves identificadas con los requisitos de negocio definidos en la planificación paso utilizando los datos que se eliminó y featurized. Sistemas de aprendizaje de máquina admiten varios **algoritmos de modelado** que se aplican a una amplia variedad de casos. Para obtener instrucciones, consulte [cómo elegir algoritmos para equipo Azure aprendizaje](machine-learning-algorithm-choice.md).

Científicos datos deben elegir el modelo más adecuado para su tarea de predicción y no es extraño que deben combinarse para obtener los mejores resultados resultados de varios modelos. Normalmente, los datos de entrada para un modelado se dividen aleatoriamente en tres partes:

- un conjunto de datos de aprendizaje 
- un conjunto de datos de validación 
- un conjunto de datos de prueba 

Los modelos se crean utilizando el **conjunto de datos de aprendizaje**. La combinación óptima de modelos (con parámetros ajustados) está activada, ejecute los modelos y los errores de predicción del **conjunto de datos de validación**de medición. Por último se utiliza el **conjunto de datos de prueba** para evaluar el rendimiento del modelo de datos independientes que no se ha usado para formar o validar el modelo elegido.  Para obtener información, consulte [cómo evaluar el rendimiento del modelo de aprendizaje del equipo de Azure](machine-learning-evaluate-model-performance.md).


## <a name="5-deploy-and-consume-models"></a>5. implementar y consumir modelos 

Una vez que tenemos un conjunto de modelos que lleve a cabo correctamente, se pueden **operationalized** consumir otras aplicaciones. Según sus necesidades, predicciones se realizan en **tiempo real** o de una forma de **proceso por lotes** . Para ser operationalized, los modelos tienen exponer con una **interfaz API de abrir** fácilmente consumida desde distintas aplicaciones como sitio Web en línea, hojas de cálculo, paneles o línea de aplicaciones empresariales y back-end. Vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="summary-and-next-steps"></a>Resumen y pasos siguientes

Modelado el [Proceso de ciencias de datos de equipo](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) como una secuencia de pasos recorridos en iteración que **proporcionan orientación** sobre las tareas necesarias para usar funciones analíticas avanzadas para crear un aplicaciones inteligente. Cada paso también proporciona detalles sobre cómo usar diversas tecnologías de Microsoft para completar las tareas descritas. 

Mientras TDSP no prevea determinados tipos de **documentación** artefactos, es recomendable documentar los resultados de la evaluación, la exploración de datos y modelado y guardar el código pertinente para que el análisis puede itera cuando sea necesario. Esto también permite reutilizar del trabajo análisis cuando trabaje en otras aplicaciones relacionadas con datos similares y tareas de predicción.

También se incluyen tutoriales de end-to-end que muestran todos los pasos del proceso para **escenarios específicos** . Ver, por ejemplo:

- [El proceso de ciencias de datos de equipo en acción: uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [El proceso de ciencias de datos de equipo en acción: usar clústeres HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).
- [Ciencia de datos con el motor en HD.mdnsight de Azure](machine-learning-data-science-spark-overview.md)
- [Scalable ciencias de datos de Azure datos lago: un tutorial to-end](machine-learning-data-science-process-data-lake-walkthrough.md)

 
