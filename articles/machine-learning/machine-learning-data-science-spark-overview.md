<properties
    pageTitle="Información general sobre la ciencia de datos con el motor en HDInsight de Azure | Microsoft Azure"
    description="El Kit de herramientas de MLlib motor lleva mucho el aprendizaje modelado capacidades para el entorno de HDInsight distribuido."
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
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Información general sobre la ciencia de datos con el motor en HDInsight de Azure

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

En este conjunto de temas se muestra cómo usar HDInsight Spark para completar tareas comunes de ciencias de datos como recopilación de datos, ingeniería de la característica, modelado y evaluación del modelo. Los datos que se utiliza están un ejemplo de la ciudad de Nueva York 2013 taxi dataset de ida y vuelta y tarifa. Los modelos integrados incluyen regresión lineal y logística, aleatorios bosques y árboles aumentados degradados. Los temas también muestra cómo almacenar estos modelos en el almacenamiento de blobs de Windows Azure (WASB) y cómo puntuación y evaluar su rendimiento predictiva. Temas más avanzados cubren cómo pueden ser modelos capacitado mediante validación cruzada y hyper parámetro barrido. En este tema de Introducción también se describe cómo configurar el clúster de motor que necesita para completar los pasos de los tres tutoriales proporcionados. 

[Motor](http://spark.apache.org/) es un paralelo Abrir origen procesamiento framework que admita el procesamiento en memoria para mejorar el rendimiento de aplicaciones de análisis de datos grande. Motor de procesamiento de motor integrado para velocidad, facilidad de uso y análisis sofisticados. Capacidades de cálculo distribuidas en memoria del motor que sea una buena opción para algoritmos iterativos en cálculos de aprendizaje y gráfico de equipo. [MLlib](http://spark.apache.org/mllib/) es biblioteca de aprendizaje de máquina scalable del motor que ofrece capacidades de modelado para este entorno distribuido. 

[Motor de HDInsight](../hdinsight/hdinsight-apache-spark-overview.md) es la oferta hospedada Azure de motor de código abierto. También incluye compatibilidad con **los blocs de notas de Jupyter PySpark** en el clúster de motor que se puede ejecutar consultas interactivas de SQL de motor para transformar, filtrar y visualizar datos almacenados en Azure BLOB (WASB). PySpark es la API de Python para motor. Los fragmentos de código que proporcionan las soluciones y mostrar la trazados relevantes para visualizar los datos que aquí se ejecutan en los blocs de notas Jupyter instalados en los clústeres motor. Los pasos de modelado en estos temas contienen código que muestra cómo entrenar, evaluar, guardar y consumir cada tipo de modelo. 

Los pasos de configuración y el código proporcionado en este tutorial es para HDInsight 3.4 motor 1,6. Sin embargo, el código aquí en los blocs de notas es genérico y debería funcionar en cualquier clúster de motor. Si no está utilizando HDInsight Spark, los pasos de configuración y administración de clúster pueden ser ligeramente diferentes de lo que se muestra aquí.

## <a name="prerequisites"></a>Requisitos previos

1.Haga debe tener una suscripción de Azure. Si ya tiene una, vea [Azure obtener la versión de prueba gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2.Haga necesita un clúster de HDInsight 3.4 motor 1,6 para completar este tutorial. Para crear uno, consulte las instrucciones proporcionadas en [Introducción: crear motor Apache en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). En el menú **Seleccionar tipo de clúster** se especifica el tipo de clúster y la versión. 


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [AZURE.NOTE] Para un tema que se muestra cómo usar Scala en lugar de Python para completar tareas de un proceso de ciencias llevar a cabo datos, vea la [ciencia de datos usando Scala con motor en Azure](machine-learning-data-science-process-scala-walkthrough.md).

<!-- -->

>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="the-nyc-2013-taxi-data"></a>Los datos de Nueva York 2013 Taxi

Los datos de Nueva York Taxi viaje están de aproximadamente 20 GB comprimidos valores separados por comas (CSV) de archivos de (~ 48 GB sin comprimir), que incluyen más de 173 millones de viajes individuales y las tarifas de pago para cada viaje. Cada registro de viaje incluye la recoger y ubicación de entrega y hora, piratería anonymized (controlador) licencia número y medallion (identificador exclusivo de taxi). Los datos cubre todos los viajes del año 2013 y está disponible en los dos conjuntos de datos siguientes para cada mes:

1. Los archivos CSV de 'trip_data' contienen detalles de viaje, como el número de personas, recoger y caída puntos, viaje duración y la duración de viaje. Estos son algunos registros de ejemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Los archivos CSV de 'trip_fare' contienen detalles de la tarifa de pago para cada viaje, como el tipo de pago, importe tarifa, suplemento y los impuestos, sugerencias y peajes y la cantidad total pagada. Estos son algunos registros de ejemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


Hemos tomado una muestra de 0,1% de estos archivos y unido del viaje\_datos y viaje\_resultados son archivos CVS en un único conjunto de datos para usarla como el conjunto de datos de entrada para este tutorial. La clave única para unirse a viajes\_datos y viaje\_tarifa se compone de los campos: medallion, prueba\_licencia y recogida\_datetime. Cada registro del conjunto de datos contiene los siguientes atributos que representa un viaje Taxi de Nueva York:


|Campo| Breve descripción
|------|---------------------------------
| Medallion |Medallion anonymized taxi (identificador único taxi)
| hack_license |    Número de licencias de carro Hackney anonymized
| vendor_id |   Id. de proveedor de taxi
| rate_code | Tasa de Nueva York taxi billete de
| store_and_fwd_flag | Almacenar y reenviar marca
| pickup_datetime | Recoger de fecha y hora
| dropoff_datetime | Caída fecha y hora
| pickup_hour | Recoger hora
| pickup_week | Recoger semana del año
| día de la semana | Weekday (rango 1-7)
| passenger_count | Número de personas en un viaje de taxi
| trip_time_in_secs | Tiempo de ida y vuelta en segundos
| trip_distance | Distancia de viaje desplazado en millas
| pickup_longitude | Recoger latitud
| pickup_latitude | Recoger latitud
| dropoff_longitude | Longitud de caída
| dropoff_latitude | Latitud de caída
| direct_distance | Dirigir la distancia entre elija hacia arriba y caída ubicaciones
| payment_type | Tipo de pago (entidades emisoras de certificados, tarjeta de crédito etcetera).
| fare_amount | Importe del billete en
| suplemento | Suplemento
| mta_tax | MTA impuestos
| tip_amount | Cantidad de sugerencia
| tolls_amount | Cantidad de peajes
| importe total | Importe total
| superpuesto | Superpuesto (0 y 1 para no o Sí)
| tip_class | Sugerencia de clase (0: $0, 1: $0-5, 2: $6 a 10, 3: 11-20 $, 4: > $20)


## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Ejecutar el código de un bloc de notas de Jupyter en el clúster de motor 

Puede iniciar el Bloc de notas de Jupyter desde el portal de Azure. Busque el clúster de motor en el panel y haga clic en él para introducir la página de administración para el clúster. Para abrir el Bloc de notas asociado con el clúster de motor, haga clic en **Paneles de clúster** -> **Jupyter Bloc de notas** .

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

También puede ir a ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para tener acceso a los blocs de notas de Jupyter. Reemplazar el elemento NOMBREDECLÚSTER de esta dirección URL con el nombre de su propio clúster. Necesita la contraseña de su cuenta de administrador tener acceso a los blocs de notas.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Seleccione PySpark para ver un directorio que contiene algunos ejemplos de los blocs de notas preconfiguradas que utilizan la API PySpark. Los blocs de notas que contienen los ejemplos de código para este conjunto de tema de motor están disponibles en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)


Puede cargar los blocs de notas directamente desde Github en el servidor de Bloc de notas de Jupyter en el clúster de motor. En la página de inicio de la Jupyter, haga clic en el botón **cargar** en la parte derecha de la pantalla. Se abre el Explorador de archivos. Aquí puede pegar la dirección URL de Github (contenido) del Bloc de notas y haga clic en **Abrir**. Los blocs de notas PySpark están disponibles en las siguientes URL:

1.  [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.  [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.  [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Volver a ver el nombre de archivo en la lista de archivos de Jupyter con el botón **cargar** . Haga clic en este botón **cargar** . Ahora ha importado el Bloc de notas. Repita estos pasos para cargar los otros blocs de notas de este tutorial.

> [AZURE.TIP] Puede haga clic en los vínculos en el explorador y seleccione **Copiar vínculo** para obtener la URL de contenido sin formato github. Puede pegar esta dirección URL en el cuadro de diálogo Jupyter cargar el Explorador de archivos.

Ahora puede:

- Vea el código haciendo clic en el Bloc de notas.
- Ejecutar cada celda, presione **MAYÚS+ENTRAR**.
- Ejecutar el Bloc de notas completo haciendo clic en la **celda** -> **Ejecutar**.
- Utilice la visualización automática de consultas.

> [AZURE.TIP] El núcleo PySpark automáticamente visualiza los resultados de las consultas SQL (HiveQL). Tiene la opción de elegir entre diferentes tipos de visualizaciones (tabla, circulares, líneas, área o barra) con los botones de menú de **tipo** en el Bloc de notas:

![Curva de regresión logística ROC para enfoque genérico](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>¿Qué es el siguiente?

Ahora que se han configurado con un clúster HDInsight Spark y ha cargado los blocs de notas Jupyter, ya está listo para elaborar los temas que corresponden a los blocs de notas PySpark tres. Se muestran cómo explorar los datos y, a continuación, cómo crear y utilizar modelos. El Bloc de notas de exploración y modelado de datos avanzados muestra cómo incluir validación cruzada, hyper parámetro barrido y del modelo de evaluación. 

**Exploración de datos y modelado de motor:** Explorar el conjunto de datos y crear, puntuación y evaluar el equipo modelos de aprendizaje al trabajar con el tema [Crear clasificación binario y los modelos de regresión de datos con el Kit de herramientas de motor MLlib](machine-learning-data-science-spark-data-exploration-modeling.md) .

**Consumo de modelo:** Para aprender a la puntuación de los modelos de clasificación y regresión creados en este tema, consulte [puntuación y evaluar modelos de aprendizaje integrado motor máquina](machine-learning-data-science-spark-model-consumption.md).

**Validación cruzada y hyperparameter barrido**: vea [la exploración de datos y modelado de motor avanzadas](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sobre cómo pueden ser modelos capacitado mediante validación cruzada y hyper parámetro barrido

