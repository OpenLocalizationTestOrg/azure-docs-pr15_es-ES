<properties 
    pageTitle="Aplicación de aprendizaje de máquina: servicio de detección de anomalías | Microsoft Azure" 
    description="API de detección de anomalías es un ejemplo creado con Microsoft Azure el aprendizaje que detecta anomalías en los datos de la serie de tiempo con valores numéricos que tengan un espaciado uniforme en el tiempo." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Servicio de detección de anomalías de aprendizaje de equipo#

##<a name="overview"></a>Información general

[API de detección de anomalías](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) es un ejemplo generado con el aprendizaje Azure que detecta anomalías en los datos de la serie de tiempo con valores numéricos que tengan un espaciado uniforme en el tiempo. 

Esta API puede detectar los siguientes tipos de patrones irregulares en los datos de la serie:

* **Positivos y negativos tendencias**: por ejemplo, al supervisar el uso de memoria en informática una tendencia al alza puede ser de interés como puede ser indicativo de una pérdida de memoria

* **Cambios en el rango de valores dinámico**: por ejemplo, al supervisar las excepciones iniciadas por un servicio de nube, los cambios en el rango de valores dinámico podrían indicar inestabilidad en el estado del servicio, y

* **Picos e interrupciones**: por ejemplo, al supervisar el número de errores de inicio de sesión en un servicio o número de desprotecciones en un sitio de comercio electrónico, picos o DIP podría indicar comportamiento anómalo.

Estos detectores de aprendizaje del equipo realizar un seguimiento de estos cambios en valores sobre cambios en curso informe y la hora en sus valores como la puntuación anomalías. No requiere el ajuste del umbral de ad hoc y sus calificaciones pueden usarse para controlar los falsos positivos. La detección de anomalías API es útil en varios escenarios como servicio supervisión mediante el seguimiento de KPI con el tiempo, uso supervisión a través de métricas como el número de búsquedas, números de clics, supervisión de rendimiento a través de contadores como memoria, CPU, lee el archivo, etc. lo largo del tiempo.

La oferta de detección de anomalías incluye herramientas útiles para ayudarle a comenzar. 

* La [aplicación web](http://anomalydetection-aml.azurewebsites.net/) le ayuda a evaluar y visualizar los resultados de la API de detección de anomalías en los datos. 

* El [código de ejemplo](http://adresultparser.codeplex.com/) se muestra cómo tener acceso a la API y analizar los resultados en C# mediante programación.

>[AZURE.NOTE]
>Pruebe la **solución de TI anomalías perspectivas** ofrecido por [esta API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>Para obtener esta solución de llevar a cabo implementada a su suscripción de Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **empiece aquí >**</a>


##<a name="api-definition"></a>Definición de la API

El servicio proporciona una API de REST sobre HTTPS que pueden usar de varias formas como un web o una aplicación móvil, R, Python, Excel, etcetera.  Enviar los datos de la serie de tiempo a este servicio a través de una llamada de la API de REST y se ejecuta una combinación de los tipos de tres anomalías descritos anteriormente. El servicio se ejecuta en la plataforma de aprendizaje del equipo de Azure, que se ajusta a sus necesidades empresariales sin problemas y proporciona SLA.

###<a name="headers"></a>Encabezados
Asegúrese de que incluye los encabezados correctos en la convocatoria, que debe ser como sigue:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Puede encontrar la clave de cuenta desde su cuenta en el [Mercado de datos de Azure](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>API de puntuación

La API de puntuación se usa para ejecutar la detección de anomalías en los datos de la serie de tiempo no estacional. La API ejecuta un número de detectores de anomalías en los datos y devuelve sus calificaciones anomalías. La siguiente ilustración muestra un ejemplo de anomalías que puede detectar la API de puntuación. Esta serie de tiempo tiene cambios distinct 2 y 3 picos. Los puntos de color rojo muestran la hora a la que se detecta el cambio de nivel, mientras que los puntos negros muestran los picos detectados.

![API de puntuación][1]
    
**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Cuerpo de la solicitud de ejemplo**

En la convocatoria a continuación, enviaremos una serie de tiempo (como se muestra truncada) con puntos de datos de 1/3/2016 a través de 3/10/2016 y los parámetros de detectores especial a la API para detectar si cualquiera de estos puntos de datos son irregulares. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

Será la respuesta a esta: 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

La API ScoreWithSeasonality se usa para ejecutar la detección de anomalías en serie de tiempo que tienen patrones de temporada. Esta API es útil para detectar desviaciones en modelos de temporada.  

La siguiente ilustración muestra un ejemplo de anomalías detectado en una serie de tiempo de temporada. La serie temporal tiene una punta (el punto negro 1 º), dos DIP (el 2 º punto negro y uno al final) y un cambio de nivel (punto rojo). Tenga en cuenta que tanto el dip en el medio de la serie de tiempo y el cambio de nivel solo fáciles de comprender después de temporada componentes se quitan de la serie.

![Estacionalidad API][2]

**DIRECCIÓN URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Cuerpo de la solicitud de ejemplo**

En la convocatoria a continuación, enviaremos una serie de tiempo (como se muestra truncada) con puntos de datos de 1/3/2016 a través de 3/10/2016 y parámetros a la API para detectar si cualquiera de estos puntos de datos son irregulares. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

Será la respuesta a esta: 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Detectores

La detección de anomalías API admite detectores en 3 categorías. Obtener más información sobre los parámetros de entrada específicos y los resultados de cada detector se puede encontrar en la tabla siguiente.

|Categoría de detector|Detector|Descripción|Parámetros de entrada|Resultados
|---|---|---|---|---|
|Punta detectores|Detector de TSpike|Detectar picos DIP basándose en ese momento los valores que proceden de los cuartiles primeros y terceros|*tspikedetector.sensitivity:* toma el valor entero en el intervalo predeterminado de 1-10: 3; Los valores más altos capturará más valores extremos realizar menos confidencial|TSpike: valores binarios: '1' si se detecta una punta y lastre, '0' en caso contrario|
||Detector de ZSpike|Detectar picos y DIP en función de cuánto la datapoints son de su Media|*zspikedetector.sensitivity:* tomar valor entero en el intervalo predeterminado de 1-10: 3; Los valores más altos capturará más valores extremos realizar menos confidencial|ZSpike: valores binarios: '1' si se detecta una punta y lastre, '0' en caso contrario|
|Detector de tendencia lenta|Detector de tendencia lenta|Detectar lenta tendencia positiva según la sensibilidad del conjunto|*trenddetector.sensitivity:* umbral de puntuación detector (predeterminado: 3,25, 3,25 – 5 es un rango razonable para seleccionar desde; Cuanto mayor sea el menos sensible)|TScore: flotante número que representa la puntuación de anomalías en la línea de tendencia|
|Cambiar nivel detectores|Cambiar nivel unidireccional Detector|Detectar nivel arriba cambiar según la sensibilidad del conjunto|*upleveldetector.sensitivity:* umbral de puntuación detector (predeterminado: 3,25, 3,25 – 5 es un rango razonable para seleccionar desde; Cuanto mayor sea el menos sensible)|PScore: número que representa el resultado de anomalías en hacia arriba de flotante cambio de nivel|
||Detector de cambio de nivel bidireccional|Detectar cambio nivel hacia arriba y hacia abajo según la sensibilidad del conjunto|*bileveldetector.sensitivity:* umbral de puntuación detector (predeterminado: 3,25, 3,25 – 5 es un rango razonable para seleccionar desde; Cuanto mayor sea el menos sensible)|RPScore: flotante número anomalías que representen puntuación hacia arriba y hacia abajo nivel cambiar

###<a name="parameters"></a>Parámetros

Más información detallada sobre estos parámetros de entrada se muestra en la tabla siguiente:

|Parámetros de entrada|Descripción|Valor predeterminado|Tipo|Rango válido|Rango sugerido|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Serie de tiempo de intervalo de agregación en segundos para la agregación de entrada|0 (no se realiza ninguna agregación)|entero|0: omitir agregación, > 0 en caso contrario,|5 minutos para 1 día, depende de la serie de tiempo
|preprocess.aggregationFunc|Función utilizada para agregar datos a la AggregationInterval especificado|Media.|enumerado|Media, suma, longitud|N/A.|
|preprocess.replaceMissing|Valores que se utiliza para separar los datos que faltan|lkv (último valor conocido)|enumerado|cero, lkv, Media.|N/A.|
|detectors.historyWindow|Historial (en número de puntos de datos) utilizado para el cálculo de la puntuación de anomalías|500|entero|10-2000|Depende de la serie de tiempo|
|upleveldetector.Sensitivity|Sensibilidad nivel arriba de cambiar detector. |3,25|doble|Ninguno|3,25 5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Sensibilidad de nivel bidireccional cambiar detector. |3,25|doble|Ninguno|3,25 5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Sensibilidad para detector de tendencia positiva. |3,25|doble|Ninguno|3,25 5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Sensibilidad para Detector de TSpike|3|entero|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Sensibilidad para Detector de ZSpike|3|entero|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.Enable|Si es el análisis de estacionalidad llevarse a cabo|Verdadero|valor booleano|Verdadero, Falso|Depende de la serie de tiempo|
|seasonality.numSeasonality |Número máximo de ciclos periódicos detectarse|1|entero|1, 2|1-2|
|seasonality.Transform |Si estacional componentes de tendencia se eliminarán antes de aplicar la detección de anomalías (y)|deseason|enumerado|Ninguno, deseason, deseasontrend|N/A.|
|postprocess.tailRows |Número de los puntos de datos más recientes a mantener en los resultados de salida|0|entero|0 (mantener todos los puntos de datos), o especifique el número de puntos a tener en los resultados|N/A.|

###<a name="output"></a>Salida
La API ejecuta todos los detectores en los datos de la serie de tiempo y devuelve puntuaciones anomalías e indicadores de pico binario para cada punto de tiempo. La siguiente tabla enumera los resultados de la API. 

|Resultados|Descripción|
|---|---|
|Hora|Las marcas de tiempo de datos sin formato o datos (o) agregados imputados si (o agregación) falta se aplica imputación de datos|
|OriginalData|Si los valores de datos sin formato o datos (o) agregados imputados (o agregación) falta se aplica imputación de datos|
|ProcessedData|Cualquiera de estos procedimientos: <ul><li>Ajustar forma estacional serie temporal si estacionalidad significativa se ha detectado y deseason opción seleccionada;</li><li>forma estacional se ajusta y se oscilador serie de tiempo si se ha detectado estacionalidad significativa y seleccionada la opción de deseasontrend</li><li>en caso contrario, es el mismo que OriginalData</li>|
|TSpike|Indicador binario para indicar si se ha detectado un pico por Detector de TSpike|
|ZSpike|Indicador binario para indicar si se ha detectado un pico por Detector de ZSpike|
|Pscore|Número flotante que representa la puntuación anomalías nivel arriba de cambiar|
|Palert|valor de 0 o 1 que indica que hay un nivel arriba cambiar anomalías en función de la sensibilidad de entrada|
|RPScore|Un flotante anomalías que representen número puntuación en Cambiar nivel de bidireccional|
|RPAlert|valor de 0 o 1 que indica que hay un nivel de bidireccional cambiar anomalías en función de la sensibilidad de entrada|
|TScore|Un flotante anomalías que representen número puntuación en tendencia positiva|
|TAlert|valor de 0 o 1 que indica que hay es una anomalía tendencia positiva en función de la sensibilidad de entrada|


Este resultado puede analizar mediante un [Analizador simple](https://adresultparser.codeplex.com/) - tiene código de ejemplo que muestra cómo conectarse a la API y analizar los resultados. Las anomalías detectadas pueden visualizarse en un panel y pasan a expertos humanos para medidas correctivas o integradas en los sistemas de vales.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 
