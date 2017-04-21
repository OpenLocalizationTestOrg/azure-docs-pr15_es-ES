<properties
    pageTitle="Usar los conjuntos de datos de ejemplo en máquina aprendizaje Studio | Microsoft Azure"
    description="Descripciones de los conjuntos de datos que se usan en los modelos de ejemplo incluidos en m Studio. Puede usar estos conjuntos de datos de ejemplo para su experimentación."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Usar los conjuntos de datos de ejemplo en Azure máquina aprendizaje Studio

[top]: #machine-learning-sample-datasets

Cuando se crea un área de trabajo en el aprendizaje Azure, un número de conjuntos de datos de ejemplo y experimentación se incluye de forma predeterminada. Muchos de estos conjuntos de datos de ejemplo se usan en los modelos de ejemplo en la [Galería de inteligencia de Azure Cortana](http://gallery.cortanaintelligence.com/)y otros se incluyen como ejemplos de distintos tipos de datos que se usan normalmente en el aprendizaje.

Algunos de estos conjuntos de datos están disponibles en el almacenamiento de blobs de Windows Azure. Estos conjuntos de datos de la tabla siguiente proporciona un vínculo directo. Puede usar estos conjuntos de datos en su experimentación con los [Datos de importación] [ import-data] módulo.

El resto de los conjuntos de datos de ejemplo se muestran en **Los conjuntos de datos guardado** en la paleta de módulo a la izquierda del lienzo de ensayo al abrir o crear un nuevo ensayo en m Studio.
Puede utilizar cualquiera de estos conjuntos de datos en su propia prueba arrastrándola a su lienzo de ensayo.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nombre del conjunto de datos</th>
  <th align=left>Descripción del conjunto de datos</th>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación de censo ingresos binario para adultos</td>
  <td valign=top>
Un subconjunto de la base de datos del censo de 1994 con trabajo adultos sobre la antigüedad de 16 con un índice de ingresos ajustado de > 100.<p> </p><b>Uso:</b> clasificar personas mediante segmentos predecir si una persona obtiene mayor de 50 KB año.<p> </p><b>Investigación relacionada:</b> Kohavi, R., Becker, B., (1996). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Conjunto de datos de códigos de aeropuerto</td>
  <td valign=top>
Códigos de aeropuerto de Estados Unidos.<p> </p>Este conjunto de datos contiene una fila por cada aeropuerto de Estados Unidos, que proporciona el número de identificación del aeropuerto y el nombre junto con la ubicación ciudad y estado.
  </td>
</tr>

<tr>
  <td valign=top>Datos de precio automóviles (materia)</td>
  <td valign=top>
Información sobre automóviles por hacer y modelo, incluido el precio características como el número de botellas y MPG, así como una calificación de riesgo seguros.<p> </p>La puntuación de riesgo asociado inicialmente precio automático y, a continuación, se ajusta para riesgo real en un proceso conocido a actuarios como symboling. Un valor de + 3 indica que el auto es arriesgado, y un valor de -3 que probablemente bastante seguros.<p> </p><b>Uso:</b> predecir la puntuación de riesgo por características, mediante la clasificación de regresión o multivariate. <p> </p><b>Investigación relacionada:</b> Schlimmer, J.C. (1987). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Conjunto de datos de bicicleta UCI alquiler</td>
  <td valign=top>
Alquiler de bicicleta UCI conjunto de datos que se basa en datos reales de empresa de Capital Bikeshare que mantiene una red de alquiler bicicletas en Washington DC.<p> </p>El conjunto de datos tiene una fila por cada hora del día en 2011 y 2012, para un total de 17,379 filas. El rango de cada hora alquileres de bicicletas es de 1 a 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Imagen de Bill Gates RGB</td>
  <td valign=top>
Archivo de imagen disponible públicamente se convierte en datos CSV.<p> </p>El código para convertir la imagen está disponible en la página de detalles de modelo <strong>cuantificación usar clústeres K medio del Color</strong> .
  </td>
</tr>

<tr>
  <td valign=top>Datos de donación de sangre</td>
  <td valign=top>
Un subconjunto de datos de la base de datos de donante de sangre de transfusiones servicio Centro de Hsin Chu ciudad, Taiwán.<p> </p>Datos de donante incluyen los meses desde el último donación) y frecuencia, o el número total de donativos, tiempo desde la última donación y cantidad de sangre donada.<p> </p><b>Uso:</b> el objetivo es predecir a través de la clasificación si donante donar sangre en marzo de 2007, donde 1 indica un donante durante el período de destino y 0 un usuario que no sean donante. <p> </p><b>Investigación relacionada:</b> Yeh, I.C., (2008). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática <p> </p>Yeh, puedo-Cheng, Yang, King-Jang y Ting, pulse teléfono-Ming "detección de conocimientos en modelo de RFM con secuencia de Bernoulli," sistemas de experto con aplicaciones, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Reseñas de libros de Amazon</td>
  <td valign=top>
Revisiones de libros de Amazon, tomadas desde el sitio Web de amazon.com por investigadores de la Universidad de Pensilvania (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">opinión</a>). Vea el artículo de investigación "biografías, Bollywood,-radiocasetes y batidoras: adaptación de dominio para la clasificación de opinión" John Blitzer, Mark Dredze y Fernando Pereira; Asociación de cálculo lingüístico (ACL) 2007.<p> </p>El conjunto de datos original tiene K 975 revisiones con clasificaciones de 1, 2, 3, 4 o 5. Las revisiones se escribieron en inglés y son del período de tiempo 1997-2007. Este conjunto de datos ha sido inferior para revisiones de 10 KB.
  </td>
</tr>

<tr>
  <td valign=top>Datos de cáncer de seno</td>
  <td valign=top>
Uno de los tres relacionadas con el cáncer los conjuntos de datos proporcionados por el Instituto oncológicos que aparece con frecuencia en la documentación de aprendizaje del equipo. Combina la información de diagnóstico con las características de análisis de laboratorio de 300 ejemplos de seda.<p> </p><b>Uso:</b> clasificar el tipo de cáncer, en función de 9 atributos, algunos de los cuales son lineales y otros por categorías. <p> </p><b>Investigación relacionada:</b> Wohlberg, W.H., código postal, W.N. y Mangasarian, S.C. (1995). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Características de cáncer de seno <td valign=top>
El conjunto de datos contiene información 102K sospechoso regiones (candidatos) de imágenes de rayos x, cada uno describe características 117. Las características son propias y su significado no se muestra por los creadores de conjunto de datos (Siemens salud). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Información de cáncer de seno</td>
  <td valign=top>
El conjunto de datos contiene información adicional para cada región sospechoso de la imagen de rayos x. Cada ejemplo proporciona información (por ejemplo, etiqueta, paciente ID, las coordenadas de revisión en relación con toda la imagen) sobre el número de fila correspondiente en el conjunto de datos de las características de cáncer de seno. Cada paciencia tiene un número de ejemplos. Para pacientes que tienen un cáncer, algunos ejemplos son positivos y algunos son negativa. Para pacientes que no tienen un cáncer, todos los ejemplos son negativos. El conjunto de datos tiene algunos ejemplos de 102K. El conjunto de datos está orientado, 0,6% de los puntos es positivo, el resto son negativos. El conjunto de datos se realizó disponible Siemens salud.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Etiquetas de Appetency CRM compartidas</td>
  <td valign=top>
Etiquetas de KDD taza 2009 desafío del cliente relación predicción (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Etiquetas de renovación CRM compartidas</td>
  <td valign=top>
Etiquetas de KDD taza 2009 desafío del cliente relación predicción (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Conjunto de datos CRM compartido</td>
  <td valign=top>
Estos datos proceden de 2009 taza de KDD desafío del cliente relación predicción (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>El conjunto de datos contiene a 50 KB clientes de la compañía de telecomunicaciones francés naranja. Cada cliente tiene 230 características anonymized, 190 de las cuales son numéricos y 40 son por categorías. Las características están muy dispersas.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Etiquetas de las ventas adicionales CRM compartidas</td>
  <td valign=top>
Etiquetas de KDD taza 2009 desafío del cliente relación predicción (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Datos de regresión eficiencia energética</td>
  <td valign=top>
Una colección de perfiles de energía simulada, en función de 12 formas diferentes de creación. Los edificios varían con respecto a diferencian por 8 características, como cristal área, la distribución de área de cristal y la orientación.<p> </p><b>Uso:</b> usar regresión o clasificación predecir la eficiencia energética de clasificación según como uno de dos respuestas con valores reales. Clasificación de clase múltiples, es redondear la variable de respuesta al entero más próximo. <p> </p><b>Investigación relacionada:</b> Xifara, respuestas y Tsanas, r. (2012). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Datos de retrasos de vuelo</td>
  <td valign=top>
Datos de rendimiento en tiempo de pasaje vuelo tomados de la recopilación de datos de TranStats del departamento de transporte de Estados Unidos (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">En tiempo</a>).<p> </p>El conjunto de datos abarca el período de tiempo de abril de octubre de 2013. Antes de cargar a Azure m Studio, el conjunto de datos se procesa como sigue:<ul><li>El conjunto de datos se filtra para cubrir solo los aeropuertos más ocupados 70 en los EE</li><li>Vuelos cancelados se han marcado como retrasa más de 15 minutos</li><li>Se filtran los vuelos desviados</li><li>Se seleccionan las columnas siguientes: año, mes, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, cancelada</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Rendimiento en vez de vuelo (materia)</td>
  <td valign=top>
Registros de llegadas de vuelos de avión y salidas dentro de Estados Unidos de octubre de 2011.<p> </p><b>Uso:</b> predecir las demoras de vuelo. <p> </p><b>Investigación relacionada:</b> de Estados Unidos departamento de transporte <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Bosque aplica a datos</td>
  <td valign=top>
Contiene los datos del tiempo, como los índices de temperatura y humedad y viento en un área del Nordeste Portugal, junto con los registros de se activa del bosque.<p> </p><b>Uso:</b> es una tarea difícil de regresión, donde el objetivo es predecir el área de se activa bosque grabado. <p> </p><b>Investigación relacionada:</b> Cortez, P. & Morais, r. (2008). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática <p> </p>[Cortez y Morais, 2007] P. Cortez y r. Morais. Un enfoque de minería de datos para se activa bosque predecir con datos meteorológica. En J. Neves, M. F. Alberto y J. Machado EDS, nuevas tendencias en Inteligencia Artificial, procedimientos de 13 EPIA 2007 - portugués conferencia sobre inteligencia Artificial, diciembre, Guimarães, Portugal, pp 512-523, 2007. APPIA, 13 ISBN 978-989-95618-0-9. Disponible en: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Conjunto de datos de alemán UCI de tarjeta de crédito</td>
  <td valign=top>
Dataset de UCI Statlog (tarjeta de crédito alemán) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Datos + Statlog + alemán + crédito</a>), con el archivo german.data.<p> </p>El conjunto de datos clasifica personas, descrito por un conjunto de atributos, como los riesgos de crédito alta o baja. Cada ejemplo representa a una persona. Hay características de 20 numéricas y por categorías y una etiqueta binaria (el valor de riesgo de crédito). Entradas de crédito alto riesgo tienen etiqueta = 2, entradas de riesgo de crédito baja tienen etiqueta = 1. El coste de clasifique mal un ejemplo bajo riesgo como alta es 1, mientras que el coste de clasifique mal un ejemplo de alto riesgo como baja es 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de películas IMDB</td>
  <td valign=top>
El conjunto de datos contiene información acerca de películas que se han clasificado en Twitter tweets: película IMDB ID, nombre de la película y género, año de producción. Hay 17K películas en el conjunto de datos. El conjunto de datos se introdujo en el documento "S. Dooms, T. De Pessemier y L. Martens. MovieTweetings: una película valoración Dataset recopilado Twitter. Talleres de Crowdsourcing y cálculo humana para sistemas de recomendación, CrowdRec a 2013 RecSys."
  </td>
</tr>

<tr>
  <td valign=top>Datos de la clase iris dos</td>
  <td valign=top>
Quizás esta es la base de datos más conocido se encuentra en la documentación de reconocimiento de trama. El conjunto de datos es relativamente pequeño, que contiene 50 ejemplos de medidas pétalo de tres variedades iris.<p> </p><b>Uso:</b> predecir el tipo de iris de las medidas.  <p> </p><b>Investigación relacionada:</b> Fisher, organismos notificadores (1988). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de película</td>
  <td valign=top>
El conjunto de datos es una versión ampliada de la película Tweetings del conjunto de datos. El conjunto de datos tiene K 170 clasificaciones de películas, extraídos de tweets bien estructurados en Twitter. Cada instancia representa un tweet y es una tupla: identificador de usuario, ID de película IMDB, clasificación, marca de hora, número de favoritos para este tweet y número de retweets de este tweet. El conjunto de datos se realizó disponible a dice, S. Dooms, B. Loni y D. Tikk de sistemas de recomendación desafío 2014.
  </td>
</tr>

<tr>
  <td valign=top>Datos MPG para diversos automóviles</td>
  <td valign=top>
Este conjunto de datos es una versión ligeramente modificada del conjunto de datos proporcionado por la biblioteca de StatLib de Carnegie Mellon University. El conjunto de datos se usó en la exposición de asociación de estadísticas American 1983.<p> </p>Las listas de datos consumo de combustible para diversos automóviles en kilómetros por litro, junto con información como el número de botellas, desplazamiento de motor, caballo, peso total y aceleración.<p> </p><b>Uso:</b> predecir consumo de combustible basado en atributos discretas multivalor 3 y 5 continuo. <p> </p><b>Investigación relacionada:</b> StatLib, Universidad Carnegie Mellon, (1993). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de Pima indios Diabetes binario clasificación</td>
  <td valign=top>
Un subconjunto de datos del Instituto nacional de Diabetes y digestivo y enfermedad de riñón de base de datos. El conjunto de datos se filtra para centrarse en pacientes hembras del patrimonio de indios Pima. Los datos incluyen datos médicos como glucosa y niveles de insulina, así como estilo de vida.<p> </p><b>Uso:</b> predecir si el asunto tiene diabetes (clasificación binario). <p> </p><b>Investigación relacionada:</b> Sigillito, V. (1990). Máquina UCI repositorio de aprendizaje <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml "</a>. Irvine, California: Universidad de California, escuela de información e informática </td>
</tr>

<tr>
  <td valign=top>Datos de clientes de restaurante</td>
  <td valign=top>
Un conjunto de metadatos acerca de los clientes, incluidos los segmentos y preferencias.<p> </p><b>Uso:</b> usar este conjunto de datos, en combinación con el dos restaurante conjuntos de datos, entrenar y probar un sistema de recomendación. <p> </p><b>Investigación relacionada:</b> Bache, K. y Lichman, M. (2013). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, CA: Universidad de California, escuela de información e informática.
  </td>
</tr>

<tr>
  <td valign=top>Datos de la característica de restaurante</td>
  <td valign=top>
Un conjunto de metadatos acerca de restaurantes y sus características, como el tipo de comida, la cena estilo y la ubicación.<p> </p><b>Uso:</b> usar este conjunto de datos, en combinación con el dos restaurante conjuntos de datos, entrenar y probar un sistema de recomendación. <p> </p><b>Investigación relacionada:</b> Bache, K. y Lichman, M. (2013). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, CA: Universidad de California, escuela de información e informática.
  </td>
</tr>

<tr>
  <td valign=top>Clasificaciones de restaurante</td>
  <td valign=top>
Contiene clasificaciones proporcionadas por usuarios para restaurantes en una escala de 0 a 2.<p> </p><b>Uso:</b> usar este conjunto de datos, en combinación con el dos restaurante conjuntos de datos, entrenar y probar un sistema de recomendación. <p> </p><b>Investigación relacionada:</b> Bache, K. y Lichman, M. (2013). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, CA: Universidad de California, escuela de información e informática.
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de acero Annealing clase múltiples</td>
  <td valign=top>
Este conjunto de datos contiene una serie de registros de acero recocido ensayos con los atributos físicos (ancho, grosor, tipos de tipo (bobina, hoja, etc.) del resultante acero.<p> </p><b>Uso:</b> predecir cualquiera de los dos atributos de clase numérico; dureza o intensidad. También puede analizar correlación entre atributos.<p> </p>Clases de acero siguen un conjunto estándar definida por SAE y otras organizaciones. Busca un determinado 'calificaciones' (la variable de clase) y desea conocer los valores necesarios. <p> </p><b>Investigación relacionada:</b> esterlina D. & Buntine, w. (NA). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información e informática <p> </p>Una guía útil para clases de acero pueden encontrarse aquí: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Telescopio datos</td>
  <td valign=top>
Registros de alta energía gamma partículas ráfagas junto con el ruido de fondo, ambos simulan mediante un proceso de Montecarlo.<p> </p>El objetivo de la simulación era mejorar la precisión de tierra la Cherenkov gamma telescopios, usando métodos estadísticos para distinguir entre la señal deseado (Cherenkov radiación duchas) y el ruido de fondo (hadronic duchas iniciados por rayos cósmicos en el ambiente superior).<p> </p>Se ha procesado previamente los datos para crear un clúster alargado con long eje está orientada hacia el centro de la cámara. Las características de esta elipse, (a menudo denominadas parámetros Hillas) están entre los parámetros de la imagen que se pueden usar para discriminación.<p> </p><b>Uso:</b> predecir si la imagen de una fiesta representa el ruido de fondo o de señal.<p> </p><b>Notas:</b> clasificación Simple precisión no es significativa para estos datos, desde la clasificación de un evento de fondo como señal es peor que la clasificación de un evento de señal como fondo. Comparación de las diferentes clasificadores debe utilizarse el gráfico ROC. La probabilidad de aceptación de un evento de fondo como señal debe ser inferior de uno de los siguientes umbrales: 0,01, 0,02, 0,05, 0,1 o 0,2.<p> </p>Observe también que se subestima el número de eventos de fondo (h para duchas hadronic), mientras que en medidas reales, la clase h o ruido representa la mayoría de los eventos. <p> </p><b>Investigación relacionada:</b> bloquear, R.K. (1995). Equipo UCI repositorio <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizaje. Irvine, California: Universidad de California, escuela de información </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de datos de tiempo</td>
  <td valign=top>
Observaciones meteorología terrestre por hora de NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">datos combinados de 201304 a 201310</a>).<p> </p>Los datos del tiempo cubren observaciones realizadas desde el aeropuerto meteorología estaciones, cubierta por el período de tiempo de abril de octubre de 2013. Antes de cargar a Azure m Studio, el conjunto de datos se procesa como sigue:<ul><li>Identificadores de estación meteorológica se asignaron a aeropuerto correspondiente Id.</li><li>Se han filtrado estaciones meteorología no está asociadas a los aeropuertos más ocupados 70</li><li>La columna fecha se divide en columnas independientes de año, mes y día</li><li>Se seleccionan las columnas siguientes: AirportID, año, mes, día, hora, zona horaria, SkyCondition, visibilidad, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, velocidad del viento, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, tipo de registro, HourlyPrecip, altímetro</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 conjunto de datos</td>
  <td valign=top>
Datos se derivan de Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) basado en los artículos de cada empresa S & P 500, almacenado como datos XML.<p> </p>Antes de cargar a Azure m Studio, el conjunto de datos se procesa como sigue:<ul><li>Extraer contenido de texto para cada empresa concreta</li><li>Quitar el formato de wiki</li><li>Quite los caracteres no alfanuméricos</li><li>Convertir todo el texto en minúsculas.</li><li>Categorías de empresa conocidos agregados</li></ul><p> </p>Tenga en cuenta que algunas empresas un artículo no se encontró para que el número de registros es menor que 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
El conjunto de datos contiene los datos del cliente y las indicaciones sobre su respuesta a una campaña de correo directo. Cada fila representa a un cliente. El conjunto de datos contiene 9 características sobre segmentos de usuario y más allá de comportamiento y 3 columnas de etiqueta (visite, conversión y dedica).  Visita es una columna binaria que indica que un cliente visitado después de la campaña de marketing, conversión indica un cliente compró algo y dedica es la cantidad que se ha invertido.  El conjunto de datos se realizó disponible por Kevin Hillstrom para MineThatData correo electrónico análisis y minería desafío de los datos.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Características de los ejemplos de prueba en el conjunto de datos de noticias Reuters RCV1 V2. El conjunto de datos tiene artículos de noticias K 781 junto con sus identificadores (primera columna del conjunto de datos). Cada artículo es un token, stopworded y base coincidirá. El conjunto de datos se realizó disponible David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Características de ejemplos de formación en el conjunto de datos de noticias Reuters RCV1 V2. El conjunto de datos tiene artículos de noticias de 23K junto con sus identificadores (primera columna del conjunto de datos). Cada artículo es un token, stopworded y base coincidirá. El conjunto de datos se realizó disponible David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Conjunto de datos de la detección de Knowledge taza 1999 KDD y competencia (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>) de herramientas de minería de datos.<p> </p>El conjunto de datos se ha descargado y almacenados en el almacenamiento de blobs de Windows Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e incluye formación y pruebas de conjuntos de datos. El conjunto de datos de aprendizaje tiene aproximadamente 126K filas y 43 columnas, incluidas las etiquetas; 3 columnas forman parte de la información de la etiqueta y 40 columnas, que consta de características numéricos y de cadena o por categorías, están disponibles para el modelo de aprendizaje. Los datos de prueba tienen aproximadamente 22,5 K probar ejemplos con las mismas 43 columnas como en los datos de entrenamiento.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Asignaciones de tema para artículos de noticias en el conjunto de datos de noticias Reuters RCV1 V2. Puede asignar un artículo de noticias a varios temas. El formato de cada fila es "<topic name>  <document id> 1". El conjunto de datos contiene las asignaciones de tema M 2.6. El conjunto de datos se realizó disponible David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Estos datos proceden del desafío de evaluación de rendimiento KDD taza 2010 estudiantes (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">evaluación del rendimiento de estudiante</a>). Los datos utilizados están el conjunto de formación de Algebra_2008_2009 (sello, J., Niculescu-Mizil, r., Ritter, S., Gordon, G.J. y Koedinger, j (2010). Álgebra puedo 2008-2009. Desafío conjunto de datos de KDD taza 2010 educativos minería desafío de los datos. Encontrará en <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> o <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>El conjunto de datos se ha descargado y almacenados en el almacenamiento de blobs de Windows Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) y contiene los archivos de registro de un alumno clases particulares de sistema. Las características suministradas incluyen Id. del problema y su breve descripción, ID alumno, marca de tiempo y el número de intentos los estudiantes que realizó antes para solucionar el problema en la forma correcta. El conjunto de datos original tiene registros de 8,9 M, este conjunto de datos ha sido inferior para las primeras filas de 100 KB. El conjunto de datos tiene 23 columnas delimitado por tabulaciones de distintos tipos: numérico, por categorías y marca de hora.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
