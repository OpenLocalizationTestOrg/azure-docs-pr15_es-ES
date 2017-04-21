<properties
    pageTitle="Analizar la renovación de cliente con el aprendizaje | Microsoft Azure"
    description="Caso práctico de desarrollo de un modelo integrado para analizar y puntuación renovación de cliente"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analizar la renovación de cliente mediante el aprendizaje Azure

##<a name="overview"></a>Información general
Este tema presenta una implementación de referencia de un proyecto de análisis de renovación de cliente que está integrado con Azure máquina aprendizaje Studio. Describe modelos genéricos asociados para holísticamente resolver el problema de renovación de cliente industrial. También hemos medir la precisión de los modelos creados mediante el aprendizaje y evaluar la instrucciones para el desarrollo.  

### <a name="acknowledgements"></a>Confirmaciones

Este ensayo se ha desarrollado y probado por Serge Berger, científico de datos Principal de Microsoft y Roger Barga, anteriormente Administrador de producto para el aprendizaje de Microsoft Azure máquina. El equipo de documentación Azure expresar reconoce su experiencia y gracias a ellas para compartir este documento.

>[AZURE.NOTE] Los datos utilizados para este ensayo no están disponibles públicamente. Para obtener un ejemplo de cómo crear un modelo de aprendizaje de equipo para el análisis de renovación, vea: [plantilla de modelo de renovación de telecomunicaciones](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) en [Galería de inteligencia de Cortana](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>El problema de renovación de cliente
Las empresas en el mercado de consumidor y en todos los sectores de la empresa tienen que tratar con renovación. A veces es excesivo renovación mientras que afecta a las decisiones de directiva. La solución tradicional es predecir la tendencia de alta churners y sus necesidades a través de un servicio de soporte, las campañas de marketing o aplicando dispensas especiales de direcciones. Estos métodos pueden variar del sector a sector e incluso desde un clúster de consumidor particular a otro dentro de un sector (por ejemplo, telecomunicaciones).

El factor común es que las empresas necesitan minimizar estos esfuerzos de retención especiales para los clientes. Por lo tanto, sería una metodología natural para todos los clientes con la probabilidad de renovación de la puntuación y abordar la N superior unos. Los clientes principales pueden ser los más rentables; Por ejemplo, en escenarios más complejos, una función de beneficio se emplea durante la selección de candidatos para exención especial. Sin embargo, estas consideraciones son únicamente una parte de la estrategia de holística para abordar la renovación. Las empresas también tendrán en riesgos de cuenta (y tolerancia de riesgos asociados), el nivel y el costo de la intervención y la segmentación de clientes plausible.  

##<a name="industry-outlook-and-approaches"></a>Enfoques y outlook de la industria
Tratamiento sofisticado de renovación de es un signo de un sector para adultos. El ejemplo clásico es el sector de telecomunicaciones donde los suscriptores suelen cambiar con frecuencia de un proveedor a otro. Esta renovación voluntaria es un problema principal. Además, proveedores acumulados conocimientos importantes sobre *los controladores de renovación*, que son los factores que clientes de unidad para cambiar.

Por ejemplo, opción auriculares o dispositivo es un controlador conocido de renovación en la empresa de teléfono móvil. Como resultado, una directiva popular es subvencionar el precio de unos auriculares para nuevos suscriptores y carga un precio total a los clientes existentes para una actualización. Tradicionalmente, esta directiva se ha llevado a clientes salto de un proveedor a otro para obtener un nuevo descuento, que a su vez, se pide proveedores perfeccionar sus estrategias.

Alta volatilidad en las ofertas de auriculares es un factor que invalida muy rápidamente modelos de renovación de basados en modelos de auricular actual. Además, teléfonos móviles no son solo los dispositivos de telecomunicaciones; también son instrucciones moda (tenga en cuenta el iPhone) y estos indicadores sociales están fuera del ámbito de conjuntos de datos de telecomunicaciones normal.

El resultado final para un modelado es que no puede establecer una directiva de sonido eliminando motivos conocidos para la renovación. De hecho, una estrategia de modelado continuo, incluidos los modelos clásicos que cuantificar variables por categorías (como árboles de decisión), es **obligatorio**.

Organizaciones con grandes conjuntos de datos en sus clientes, están realizando análisis de datos grande (en particular, detección de renovación basada en datos grandes) como un enfoque eficaz para el problema. Puede encontrar más información sobre el enfoque de datos grande para el problema de renovación de las recomendaciones de la sección ETL.  

##<a name="methodology-to-model-customer-churn"></a>Metodología de renovación de cliente de modelo
Un proceso de solución de problemas comunes para solucionar renovación de cliente se muestra en las figuras 1-3:  

1.  Un modelo de riesgos le permite tener en cuenta cómo afectan a acciones probabilidad y los riesgos.
2.  Un modelo de intervención para tener en cuenta cómo el nivel de intervención podría afectar la probabilidad de renovación y la cantidad de cliente permite el valor de duración (CLV).
3.  Este análisis permite a un análisis cualitativo que se remite a una campaña de marketing proactiva destinada a segmentos de clientes para ofrecer la oferta óptima.  

![][1]

Este enfoque reenviar electrónicos es la mejor manera de tratar de renovación, pero se suministra con complejidad: tenemos que desarrollar un Arquetipo varios modelo y seguimiento de las dependencias entre los modelos. La interacción entre los modelos se puede encapsulada tal como se muestra en el diagrama siguiente:  

![][2]

*Figura 4: Unificada Arquetipo varios modelo*  

Interacción entre los modelos es clave si estamos para ofrecer un enfoque integral a la retención de clientes. Cada modelo necesariamente disminuye con el tiempo; por lo tanto, la arquitectura es un bucle implícito (similar a la Arquetipo establecida por el estándar de minería de datos y claro DM, [***3***]).  

El ciclo general de marketing de decisión de riesgo segmentación/descomposición sigue siendo una estructura generalizada, que es aplicable a muchos problemas empresariales. Análisis de renovación es simplemente un fuerte representante de este grupo de problemas porque tiene todas las características de un problema de negocio complejo que no permite una solución predictiva simplificada. Las características sociales del enfoque moderno renovación no se resaltan especialmente en el enfoque, pero los aspectos sociales encapsulados en Arquetipo de modelado, como aparecerían en cualquier modelo.  

Una adición interesante aquí es el análisis de datos grande. Telecomunicaciones actual y empresas minorista recopilan datos exhaustivas sobre sus clientes y fácilmente nos podemos prever que la necesidad de conectividad varios modelo se convertirá en una línea de tendencia comunes dado tendencias emergentes como la Internet de cosas y dispositivos presentes, que permiten que empresa emplear soluciones inteligentes en varias capas.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementar Arquetipo de modelado en máquina aprendizaje Studio
Dado que el problema descrito, ¿cuál es la mejor manera de implementar un modelo integrado y puntuación enfoque? En esta sección, mostramos cómo se lleva a cabo esta mediante Studio de aprendizaje del equipo de Azure.  

El enfoque de varios modelo es obligatorio cuando diseñe un Arquetipo global para la renovación. Incluso la puntuación (predictiva) parte del enfoque debe ser varios modelo.  

El siguiente diagrama muestra el prototipo que se creó, que emplea cuatro algoritmos puntuación en máquina aprendizaje Studio predecir renovación. La razón para usar un enfoque varios modelo no es solo crear un conjunto de clasificador para aumentar la precisión, pero también para protegerse de conexión exceso y para mejorar la selección de características normativas.  

![][3]

*Figura 5: Prototipo de una enfoque de modelado de renovación de*  

Las siguientes secciones proporcionan más detalles sobre el modelo que hemos implementado con Machine aprendizaje Studio de puntuación de prototipo.  

###<a name="data-selection-and-preparation"></a>Preparación y selección de datos
Los datos se utilizan para generar los modelos y clientes de puntuación obtenida de una solución CRM vertical, con los datos protegidos para proteger la privacidad del cliente. Los datos contienen información sobre 8.000 suscripciones en los Estados Unidos y combina tres orígenes: hacer el aprovisionamiento de datos (metadatos de suscripción), datos de actividad (uso del sistema) y datos de soporte al cliente. Los datos no incluye cualquier negocio información relacionada acerca de los clientes; Por ejemplo, no incluye la puntuación de crédito o de metadatos de fidelidad.  

Para simplificar, ETL y procesos de limpieza datos fuera del ámbito porque se supone que ya tiene preparación de datos ha terminado en otro sitio.   

Selección de características para un modelado se basa en cifra_significativa preliminar puntuación del conjunto de indicadores, incluidos en el proceso que utiliza el módulo de bosque aleatorio. Para la implementación de la máquina aprendizaje Studio, se calculan la media, mediana y rangos para características representantes. Por ejemplo, hemos agregado agregados para los datos de calidad, como los valores mínimos y máximos de actividad de usuario.    

También se genera información temporal durante los últimos seis meses. Se analizan los datos de un año y se estableció que incluso si hubiera tendencias estadística significativas, el efecto de renovación disminuye considerablemente después de seis meses.  

El punto más importante es que todo el proceso, incluyendo ETL, selección de características y modelado se ha implementado en máquina aprendizaje Studio, con orígenes de datos de Microsoft Azure.   

Los diagramas siguientes muestran los datos que se ha usado.  

![][4]

*Figura 6: Fragmento del origen de datos (protegido)*  

![][5]


*Figura 7: Características extraídas de origen de datos*
 
> Tenga en cuenta que estos datos están privados y, por tanto, no se pueden compartir los datos y modelo.
> Sin embargo, para un modelo de similar con datos disponibles públicamente, vea este ejemplo experimentar en la [Galería de inteligencia Cortana](http://gallery.cortanaintelligence.com/): [Renovación de cliente de telecomunicaciones](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para obtener más información acerca de cómo puede implementar un modelo de análisis de renovación con el conjunto de aplicaciones de inteligencia de Cortana, se recomienda que [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) , director de programas oeste Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algoritmos utilizados en el prototipo

Los siguientes algoritmos de aprendizaje de máquina cuatro hemos usado para generar el prototipo (sin personalización):  

1.  Regresión logística (LR)
2.  Árbol de decisión aumentada (BT)
3.  Perceptron promedio (PA)
4.  Equipo de soporte técnico vector (SVM)  


El siguiente diagrama muestra una parte de la superficie de diseño de ensayo que indica el orden en que se crearon los modelos:  

![][6]  


*Figura 8: Crear modelos en máquina aprendizaje Studio*  

###<a name="scoring-methods"></a>Métodos de puntuación
Se cuentan los cuatro modelos usando un conjunto de datos de aprendizaje etiquetada.  

También se envía el conjunto de datos de calificación a un modelo de comparable creado mediante la edición de escritorio de SA Enterprise minero 12. Se puede medir la precisión del modelo SA y los cuatro modelos Studio de aprendizaje del equipo.  

##<a name="results"></a>Resultados
En esta sección, presentamos nuestras conclusiones sobre la precisión de los modelos, según la puntuación del conjunto de datos.  

###<a name="accuracy-and-precision-of-scoring"></a>Precisión y la precisión de puntuación
En general, la implementación de Azure el aprendizaje es detrás SA en precisión por unos 10-15% (área en curva o AUC).  

¿Sin embargo, la métrica más importante en la renovación de es la tasa de misclassification: es decir, de la churners top N como previsto por el clasificador, cuáles de ellas hacía **no** renovación y ha recibido un tratamiento especial? En el diagrama siguiente se compara esta tasa misclassification para todos los modelos:  

![][7]


*Figura 9: Passau prototipo en el área curva*

###<a name="using-auc-to-compare-results"></a>Usar AUC para comparar los resultados
Área en curva (AUC) es una métrica que representa una medida global de la *cláusula de salvedad* entre las distribuciones de puntuaciones de población positivos y negativos. Es similar a tradicional graph receptor operador característica (rc), pero una diferencia importante es que la métrica AUC no es necesario que elija un valor de umbral. En su lugar, se resumen los resultados sobre **todas las** opciones posibles. En cambio, el gráfico ROC tradicional muestra la tasa positiva en el eje vertical y la tasa de falsa positiva en el eje horizontal y el umbral de clasificación varía.   

AUC generalmente se usa como una medida de patrimonio algoritmos diferentes (o distintos sistemas) porque permite modelos que se comparan mediante los valores AUC. Se trata de un enfoque popular de industrias como meteorología y biosciences. Por lo tanto, AUC representa una herramienta popular para evaluar el rendimiento de clasificador.  

###<a name="comparing-misclassification-rates"></a>Comparación de velocidades misclassification
Se comparan las tasas de misclassification en el conjunto de datos en cuestión mediante los datos CRM de suscripciones aproximadamente 8.000.  

-   La tasa de misclassification SA era 10-15%.
-   La velocidad de misclassification de máquina aprendizaje Studio era 15-20% para el churners principio 200-300.  

En el sector de telecomunicaciones, es importante sólo aquellos clientes que tengan mayor riesgo para la renovación mediante la oferta de un servicio de soporte u otro tratamiento especial de direcciones. En ese sentido, la implementación de máquina aprendizaje Studio logra resultados equivalente del modelo de SA.  

Por la misma razón, precisión es más importante que la precisión porque nos interesa principalmente de clasificación correctamente churners posibles.  

El siguiente diagrama de Wikipedia muestra la relación de un gráfico interesantes y fácil de entender:  

![][8]

*Figura 10: Equilibrio entre precisión*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de la precisión de modelo de árbol de decisión aumentada  

El siguiente gráfico muestra los resultados sin formato de puntuación usando el prototipo de aprendizaje para el modelo de árbol de decisión aumentada, que es más precisa entre los modelos de cuatro:  

![][9]

*Figura 11: Características del modelo de árbol de decisión aumentada*

##<a name="performance-comparison"></a>Comparación de rendimiento
Se compara la velocidad a la que se ha obtenido datos con los modelos de máquina aprendizaje Studio y un modelo comparable creados mediante la edición de escritorio de SA Enterprise minero 12.1.  

La siguiente tabla resume el rendimiento de los algoritmos:  

*Tabla 1. Rendimiento general (precisión) de los algoritmos*

| LR|BT|PA|SVM|
|---|---|---|---|
|Modelo medio|El modelo de recomendaciones|Su rendimiento bajo|Modelo medio|

Los modelos de hospedado en máquina aprendizaje Studio fue superó SA 15-25% de la velocidad de ejecución, pero la precisión en gran medida de par.  

##<a name="discussion-and-recommendations"></a>Discusión y recomendaciones
En el sector de telecomunicaciones, han surgido varios recomendados para analizar la renovación, incluidos:  

-   Derivan métricas para cuatro categorías fundamentales:
    -   **Entidad (por ejemplo, una suscripción)**. Proporcionar información básica sobre la suscripción o un cliente que es el asunto de la renovación.
    -   **Actividad**. Obtenga toda la información de uso posibles está relacionado con la entidad, por ejemplo, el número de inicios de sesión.
    -   **La asistencia al cliente**. Recopilar información de registros de soporte al cliente para indicar si la suscripción tenía problemas o interacciones con soporte al cliente.
    -   **Competitivos y datos empresariales**. Obtener cualquier información posible acerca del cliente (por ejemplo, puede ser difícil realizar un seguimiento o no disponible).
-   Use la prioridad a la selección de la característica de unidad. Esto implica que el modelo de árbol de decisión aumentada siempre es un enfoque compromiso de entrega.  

El uso de estos cuatro categorías crea la ilusión de que un enfoque simple *determinista* , basado en índices formados de factores razonable por categoría, debe ser suficiente para identificar a los clientes en el riesgo para la renovación. Desgraciadamente, aunque este concepto parece plausible, es una descripción false. El motivo es que renovación es un efecto temporal y los factores para renovación suelen en estados transitorios. Lo lleva un cliente que deben contemplarse salir hoy podría ser diferente mañana y, por supuesto será diferente seis meses desde ahora. Por lo tanto, una *probabilidad* modelo es necesaria.  

A menudo se pasa por alto esta observación importante de la empresa, que generalmente se prefiere un enfoque orientado a la inteligencia de negocio análisis, principalmente porque es más fácil vender y admite automatización sencillo.  

Sin embargo, la promesa de análisis de autoservicio con Machine aprendizaje Studio es que las cuatro categorías de información, clasificados por división o departamento, se convierten en una valiosa fuente para el aprendizaje sobre la renovación.  

Otra capacidad interesante procedentes de aprendizaje del equipo de Azure es la capacidad para agregar un módulo personalizado en el repositorio de módulos predefinidos que ya están disponibles. Esta función, esencialmente, crea una oportunidad para seleccionar las bibliotecas y crear plantillas para mercados verticales. Es un diferenciador importante de aprendizaje del equipo de Azure en el mercado.  

Esperamos seguir este tema en el futuro, especialmente relacionada con el análisis de datos grande.
  
##<a name="conclusion"></a>Conclusión
Este artículo describe un enfoque razonable para abordar el problema común de renovación de cliente utilizando un marco genérico. Se considera un prototipo para modelos de puntuación y había implementado usando el aprendizaje Azure. Por último, se evalúan la precisión y el rendimiento de la solución de prototipo con respecto a algoritmos comparables en SA.  

**Para obtener más información:**  

¿Este artículo de ayuda? Por favor, envíenos sus comentarios. Comuníquese con nosotros en una escala del 1 (deficiente) al 5 (excelente), ¿qué puntuación este artículo y ¿por qué se le asignado esta clasificación? Por ejemplo:  

-   ¿Se valoración alto debido a tener buenos ejemplos, capturas de pantalla excelente, desactive escribiendo o por otro motivo?
-   ¿Se valoración baja debido a una mala ejemplos, capturas de pantalla aproximada o es confusa escritura?  

Este comentario ayudar a mejorar la calidad de notas de que lanzamiento del producto.   

[Enviar comentarios](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Referencias
Análisis predictivo [1] : más allá de la administración de información de predicciones, McKnight occidental, julio/agosto de 2011, p.18-20.  

Artículo de Wikipedia [2] : [precisión](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [y claro-DM 1.0: Guía de minería de datos paso a paso](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing big Data: contratar a los clientes de manera más eficaz y unidad valor](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [plantilla modelo de renovación de telecomunicaciones](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) en [Galería de inteligencia de Cortana](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Apéndice

![][10]

*Figura 12: Instantánea de una presentación en la renovación de prototipo*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
