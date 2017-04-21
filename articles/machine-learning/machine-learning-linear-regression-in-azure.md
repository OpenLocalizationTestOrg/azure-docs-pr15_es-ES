<properties 
    pageTitle="Uso de regresión lineal en el aprendizaje | Microsoft Azure" 
    description="Una comparación de los modelos de regresión lineal en Excel y en Azure máquina aprendizaje Studio" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>Uso de regresión lineal en aprendizaje del equipo de Azure

> *Kate Baroni* y *Miguel Boatman* son diseño de solución de empresa en el centro de excelencia de Microsoft datos perspectivas. En este artículo, se describe su experiencia migrar un conjunto de análisis de regresión existente a una solución basada en nube mediante el aprendizaje Azure.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Objetivo

Nuestro proyecto en marcha con dos objetivos:  

1. Usar el análisis de predictivo para mejorar la precisión de proyecciones de ingresos mensuales de nuestra organización  
2. Usar Azure m para confirmar, optimizar, aumentar la velocidad y la escala de los resultados.  

Como muchas empresas, nuestra organización pasa por un proceso de previsión de ingresos mensuales. Nuestro equipo pequeña de analistas de negocio responsable usando el aprendizaje para admitir el proceso y mejorar la precisión de previsión.  El grupo trabajó varios meses de recopilación de datos de varios orígenes y con los atributos de datos a través de análisis estadísticos identifican los atributos de clave relacionados con la previsión de ventas de servicios.  Pasos siguientes era comenzar modelos de estadísticas de regresión de creación de prototipos de los datos en Excel.  En las próximas semanas, tenemos un modelo de regresión de Excel que se mejores el campo actual y la previsión procesos de finanzas. Esto se convirtió en el resultado de la predicción de línea base.  


A continuación, utilizamos el siguiente paso para mover nuestro análisis predictivo sobre a Azure m para saber cómo puede mejorar Azure m rendimiento predictiva.


## <a name="achieving-predictive-performance-parity"></a>Lograr paridad performance predictiva

Nuestra primera prioridad era lograr paridad entre los modelos de regresión m de Azure y Excel.  Dado exactamente los mismos datos y la misma división de formación y prueba datos queremos lograr rendimiento predictiva paridad entre Excel y m de Azure.   Inicialmente error. El modelo de Excel superó el modelo m de Azure.   El error era debido a una falta de conocimiento de la configuración de la herramienta de base en Azure m. Después de una sincronización con el equipo de producto de Azure m, había adquirida una mejor comprensión de la base de configuración necesarios para nuestros conjuntos de datos y lograr paridad entre los dos modelos.  

### <a name="create-regression-model-in-excel"></a>Crear el modelo de regresión en Excel
Nuestra regresión de Excel se utiliza el modelo de regresión lineal estándar que se encuentra en las herramientas para análisis de Excel. 

Se calcula la *intención % absoluta Error* y había utilizado como la medida de rendimiento para el modelo.  Tardó 3 meses para llegar a un modelo de trabajar con Excel.  Se ha insertado gran parte de aprendizaje en el ensayo de Azure m que finalmente es útil en la descripción de los requisitos.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Crear ensayo comparable en aprendizaje de máquina de Azure  
Seguimos estos pasos para crear nuestra ensayo en Azure m:  

1.  Cargado el conjunto de datos como un archivo csv a Azure m (archivo muy pequeño)
2.  Crea un nuevo ensayo y utiliza la [Seleccionar columnas de conjunto de datos] [ select-columns] módulo para seleccionar las mismas características de datos usadas en Excel   
3.  Utilizar los [Datos divididos] [ split] módulo (con el modo de *Expresión relativa* ) para dividir los datos en conjuntos de tren mismo exacta como hizo en Excel  
4.  Experimentó con la [Regresión lineal] [ linear-regression] módulo (solo opciones predeterminadas), documentado y comparar los resultados a nuestro modelo de regresión de Excel

### <a name="review-initial-results"></a>Revisar los resultados iniciales
En primer lugar, el modelo de Excel claramente superó el modelo de Azure m:  

|   |Excel|Azure m|
|---|:---:|:---:|
|Rendimiento|   |  |
|<ul style="list-style-type: none;"><li>R cuadrado ajustado</li></ul>| 0.96 |N/A.|
|<ul style="list-style-type: none;"><li>Coeficiente de <br />Determinación</li></ul>|N/A.|   0.78<br />(precisión baja)|
|La intención de Error absoluta |  $9. 5M|  $ 19.4 M|
|La intención de Error absoluta (%)|   % de 6.03|  12.2%

Cuando nos hemos encontrado nuestro proceso y los resultados por los programadores y científicos de datos en el equipo de Azure m, rápidamente proporcionan algunas sugerencias útiles.  

* Cuando se utiliza la [Regresión lineal] [ linear-regression] módulo Azure m, se proporcionan dos métodos:
    *  Descenso de degradado en línea: Puede ser más adecuado para los problemas de gran escala
    *  Normal mínimos cuadrados: Este es el método que pensar mayoría de las personas cuando escucharán regresión lineal. Para pequeños conjuntos de datos normal de mínimos cuadrados puede ser una opción más óptima.
*  Considere la posibilidad de ajustar el parámetro de nivel 2 Regularization peso para mejorar el rendimiento. Se establece en 0,001 predeterminada y para nuestro pequeño conjunto de datos, se establezca 0,005 para mejorar el rendimiento.    

### <a name="mystery-solved"></a>¡Mystery resuelto!
Cuando se aplican las recomendaciones, hemos logrado el mismo rendimiento de línea base en Azure m como con Excel:   

|| Excel|Azure m (inicial)|M Azure con los mínimos cuadrados|
|---|:---:|:---:|:---:|
|Valor con etiqueta  |Datos reales (numérico)|mismo|mismo|
|Estudiante  |Excel -> datos Análisis -> regresión.|Regresión lineal.|Regresión lineal.|
|Opciones de aprendizaje|N/A.|Valores predeterminados|ordinarios mínimos cuadrados<br />NIVEL 2 = 0,005|
|Conjunto de datos.|1 etiqueta, 3 características, 26 filas.   Todos los numérico.|mismo|mismo|
|Dividir: tren|Formación de Excel en las 18 en primer lugar filas, probadas en la última 8 filas.|mismo|mismo|
|Dividir: prueba|Fórmula de regresión aplicado a la última 8 filas en Excel|mismo|mismo|
|**Rendimiento**||||
|R cuadrado ajustado|0.96|N/A.||
|Coeficiente de determinación|N/A.|0.78|0.952049|
|La intención de Error absoluta |$9. 5M|$ 19.4 M|$9. 5M|
|La intención de Error absoluta (%)|<span style="background-color: 00FF00;">% de 6.03</span>|12.2%|<span style="background-color: 00FF00;">% de 6.03</span>|

Además, los coeficientes de Excel en comparación con bien grosores de función en el modelo capacitado Azure:

||Coeficientes de Excel|Característica de Azure grosores|
|---|:---:|:---:|
|Intersección/compensación|19470209.88|19328500|
|Característica A|0.832653063|0.834156|
|Característica B|11071967.08|11007300|
|Característica C|25383318.09|25140800|

## <a name="next-steps"></a>Pasos siguientes

Deseamos consumir el servicio web de Azure m dentro de Excel.  Nuestros analistas de empresa se basan en Excel y se necesita una manera de llamar al servicio web de Azure m con una fila de datos de Excel y que devuelve el valor previsto para Excel.   

También queremos optimizar nuestro modelo, con las opciones y algoritmos disponibles en Azure m.

### <a name="integration-with-excel"></a>Integración con Excel
Nuestra solución fue que controle nuestro modelo de regresión Azure m mediante la creación de un servicio web desde el modelo capacitado.  En unos minutos, se creó el servicio web y podríamos llamarlo directamente desde Excel para devolver un valor de los ingresos previstos.    

La sección del *Panel de servicios Web* incluye un libro de Excel descargable.  El libro se suministra con formato previo con la información esquemas y API de servicio web incrustada.   Al hacer clic en *Descargar el libro de Excel*, se abre y puede guardarlo en su equipo local.    

![][1]
 
Con el libro abierto, copie los parámetros predefinidos en la sección de parámetro azul tal como se muestra a continuación.  Una vez que se introducen los parámetros, Excel resalta al servicio web AzureML y la predicción de puntuación etiquetas se mostrarán en la sección valores de predicción verde.  El libro seguirá crear predicciones para los parámetros basados en un modelo de formación para todos los elementos de la fila especificada en parámetros.   Para obtener más información sobre cómo usar esta característica, consulte [uso de un servicio Web de Azure máquina aprendizaje de Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Optimización y aún más experimentación
Ahora que tenemos una línea base con nuestro modelo de Excel, se mueve hacia adelante optimizar nuestra modelo de regresión lineal de Azure m.  Hemos usado el módulo de [selección de característica basada en el filtro] [ filter-based-feature-selection] mejorar nuestra selección de datos iniciales elementos y contribuido lograr una mejora del rendimiento de 4,6% significa Error absolutas.   Para proyectos futuros usamos esta característica que nos puede guardar semanas en recorrer los atributos de datos para buscar el conjunto de características para modelado adecuado.  

A continuación tenemos previsto incluir los algoritmos adicionales como [bayesiana] [ bayesian-linear-regression] o [Aumentar árboles de decisión] [ boosted-decision-tree-regression] en nuestra prueba para comparar el rendimiento.    

Si desea experimentar con regresión, un buen conjunto de datos puede probar es el conjunto de datos de ejemplo de regresión de eficiencia energética, que tiene una gran cantidad de atributos numéricos. El conjunto de datos se proporciona como parte de los conjuntos de datos de ejemplo en m Studio.  Puede utilizar una variedad de módulos de aprendizaje para predecir la carga de calefacción o refrigeración carga.  La tabla siguiente es que una comparación de rendimiento de diferentes de regresión aprende contra la predicción de conjunto de datos de la eficiencia energética para la variable de destino carga refrigeración: 

|Modelo|La intención de Error absoluta|Media root cuadrado Error|Error absoluta relativa|Proporcional al cuadrado Error|Coeficiente de determinación
|---|---|---|---|---|---
|Árbol de decisión aumentada|0.930113|1.4239|0.106647|0.021662|0.978338
|Regresión lineal (degradado descenso)|2.035693|2.98006|0.233414|0.094881|0.905119
|Red neuronal regresión|1.548195|2.114617|0.177517|0.047774|0.952226
|Regresión lineal (normal de mínimos cuadrados)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Puntos clave 

Hemos aprendido mucho por de regresión ejecución de Excel y aprendizaje del equipo de Azure experimentación en paralelo. Crear el modelo de línea base en Excel y comparar modelos con Azure m [Regresión lineal] [ linear-regression] ayudado nos obtener m de Azure y hemos descubierto oportunidades para mejorar el rendimiento de selección y el modelo de datos.         

También hemos encontrado que es aconsejable usar [selección de característica basada en el filtro] [ filter-based-feature-selection] para acelerar los proyectos futuros de predicción.  Aplicando selección de características a los datos, puede crear un modelo mejorado en Azure m con un mejor rendimiento general. 

La capacidad para transferir la predictiva analítico previsión de Azure m a Excel sistémica permite un aumento significativo en la capacidad de proporcionar correctamente resultados para una audiencia de usuario de negocio generales.     


## <a name="resources"></a>Recursos
Se muestran algunos recursos para ayudarle a que trabajar con regresión:  

* Regresión en Excel.  Si nunca ha probado regresión en Excel, este tutorial facilita: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regresión vs de previsión.  Figura de ajedrez Tyler escribió un artículo de blog que se explica cómo tiempo serie previsión en Excel, que contiene la descripción del buena principiantes de regresión lineal. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-Forecasting-Concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Ordinarias mínimos cuadrados regresión lineal: Errores, problemas y riesgos.  Para una introducción y el análisis de regresión: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
