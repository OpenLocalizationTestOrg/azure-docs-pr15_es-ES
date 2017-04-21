<properties 
    pageTitle="Ciencia de datos en la máquina Virtual Linux datos ciencias | Microsoft Azure" 
    description="Cómo realizar varias tareas comunes de ciencias datos con Linux datos ciencias VM." 
    services="machine-learning"
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev;paulsh" />


# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Ciencia de datos en la máquina Virtual Linux datos ciencia

En este tutorial se muestra cómo realizar varias tareas comunes de ciencias datos con Linux datos ciencias VM. La máquina Virtual de ciencias de datos de Linux (DSVM) es una imagen de máquina virtual disponible en Azure que viene preinstalado con un conjunto de herramientas que se usan normalmente para el análisis de datos y aprendizaje de equipo. En el tema de [aprovisionamiento de la máquina Virtual de Linux datos ciencias](machine-learning-data-science-linux-dsvm-intro.md) son detallados de los componentes de software clave. La imagen de VM facilita la introducción de manera ciencias de datos en minutos, sin tener que instalar y configurar las herramientas de forma individual. Fácilmente puede escalar la máquina virtual, si es necesario y detenerlo cuando no esté en uso. Así que este recurso es elástico y rentable. 

Las tareas de ciencias de datos muestran en este tutorial, siga los pasos indicados en el [Proceso de ciencias de datos de equipo](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Este proceso proporciona un enfoque sistemático para ciencias de datos que permite a los equipos de científicos de datos para colaborar eficazmente el ciclo de vida de creación de aplicaciones inteligentes. El proceso de ciencias de datos también proporciona un marco iterativo para ciencias de datos que se pueden seguir de forma individual.

Analizar el conjunto de datos [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) en este tutorial. Se trata de un conjunto de mensajes de correo electrónico que se marcan como correo no deseado o ham (lo que significa que no son correo no deseado), y también contiene algunas estadísticas del contenido de los correos electrónicos. En la siguiente pero una sección, se describen las estadísticas incluidas. 


## <a name="prerequisites"></a>Requisitos previos

Antes de usar una máquina Virtual de Linux datos ciencias, debe tener el siguiente:

- Una **suscripción de Azure**. Si ya tiene una, vea [crear su cuenta gratuita de Azure hoy](https://azure.microsoft.com/free/).
- Una [**ciencia de datos de Linux VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para obtener información sobre esta VM de aprovisionamiento, vea [aprovisionar la máquina Virtual de Linux datos ciencias](machine-learning-data-science-linux-dsvm-intro.md). 
- [X2Go](http://wiki.x2go.org/doku.php) instalado en su equipo y abra una sesión XFCE. Para obtener información sobre la instalación y configuración de un **cliente de X2Go**, vea [instalar y configurar el cliente de X2Go](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client). 
- Una **cuenta de AzureML**. Si todavía no tiene una, iniciar sesión en una nueva en la [página principal de AzureML](https://studio.azureml.net/). Hay un nivel de uso gratuito para ayudarle a empezar.


## <a name="download-the-spambase-dataset"></a>Descargar el conjunto de datos spambase

El conjunto de datos [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) es un conjunto de datos que contiene solo 4601 ejemplos relativamente pequeño. Se trata de un tamaño de forma conveniente usar al mostrar algunas de las características clave de la máquina virtual de ciencias datos tal como se conserva los requisitos de recursos reducido.

>[AZURE.NOTE] En este tutorial se creó en una D2 tamaño v2 datos ciencias Máquina Virtual Linux. Este tamaño DSVM es capaz de administración de los procedimientos de este tutorial.

Si necesita más espacio de almacenamiento, puede crear discos adicionales y adjúntelos a su máquina virtual. Estos discos use persistente almacenamiento de Azure, para que sus datos se conservan incluso cuando el servidor reaprovisionarla debido a que el cambio de tamaño o se cierra. Para agregar un disco y adjuntar la VM, siga las instrucciones de [Agregar un disco a una VM Linux](../virtual-machines/virtual-machines-linux-add-disk.md). Estos pasos se utiliza la interfaz de línea de comandos de Azure (Azure CLI), que ya está instalado en el DSVM. Por lo tanto, es posible estos procedimientos por completo de la máquina virtual propiamente dicho. Otra opción para aumentar el almacenamiento es usar [archivos de Azure](../storage/storage-how-to-use-files-linux.md).

Para descargar los datos, abra una ventana de terminal y ejecutar este comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

El archivo descargado no tiene una fila de encabezado, así que vamos a crear otro archivo que tiene un encabezado. Ejecute este comando para crear un archivo con los encabezados correspondientes:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

A continuación, concatenar los dos archivos junto con el comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

El conjunto de datos tiene varios tipos de estadísticas de cada correo electrónico: 

- Columnas como ***word\_frec\_WORD*** indica el porcentaje de palabras en el correo electrónico que coinciden con *WORD*. Por ejemplo, si *word\_frec\_realizar* es 1, 1% de todas las palabras en el correo electrónico estaba *realizar*. 
- Columnas como ***char\_frec\_CHAR*** indica el porcentaje de todos los caracteres en el correo electrónico que hubiera *un carácter*. 
- ***mayúscula\_ejecutar\_longitud\_más larga*** es la longitud más larga de una secuencia de letras en mayúscula. 
- ***mayúscula\_ejecutar\_longitud\_Media*** es el promedio de todas las secuencias de letras en mayúscula. 
- ***mayúscula\_ejecutar\_longitud\_total*** es la longitud total de todas las secuencias de letras en mayúscula. 
- ***correo no deseado*** indica si el correo electrónico se considera correo no deseado o no (1 = correo no deseado, 0 = no correo no deseado).


## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorar el conjunto de datos con Microsoft R abierto

Vamos a examinar los datos y hacer algunas máquina básica de aprendizaje con R. La máquina virtual de ciencias de datos se suministra con [Microsoft R abierto](https://mran.revolutionanalytics.com/open/) previamente instalado. Las bibliotecas de cálculos multiproceso en esta versión de R ofrecen mejor rendimiento que varias versiones de un único subproceso. Microsoft R abrir también proporciona reproducción utilizando una instantánea del repositorio de paquete CRAN.

Para obtener copias de los ejemplos de código utilizados en este tutorial, clonar el repositorio de **Azure máquina aprendizaje datos ciencias** usando git, que está preinstalado en la máquina virtual. Desde la línea de comandos git, ejecute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra una ventana de terminal y comience una nueva sesión de R con la consola interactiva R.

>[AZURE.NOTE] También puede usar RStudio para los siguientes procedimientos. Para instalar RStudio, ejecute este comando en un terminal:`./Desktop/DSVM\ tools/installRStudio.sh`

Para importar los datos y configurar el entorno, ejecute:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver las estadísticas de resumen de cada columna:

    summary(data)

Para obtener una vista distinta de los datos:

    str(data)

Esto indica el tipo de cada variable y algunos valores de la primera en el conjunto de datos. 

La columna de *correo no deseado* se ha leído como un entero, pero es realmente una por categorías variable (o factor). Para establecer su tipo:

    data$spam <- as.factor(data$spam)

Para realizar un análisis de exploración, use el paquete de [ggplot2](http://ggplot2.org/) , una biblioteca de gráficos popular para R que ya está instalado en la máquina virtual. Nota de los datos de resumen se muestra una versión anterior, tenemos estadísticas de resumen en la frecuencia del carácter de signo de exclamación. Vamos a trazar esas frecuencias aquí con los siguientes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Puesto que la barra de cero inclinar el trazado, vamos a eliminarlo:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Hay una densidad no trivial por encima de 1 que tiene un aspecto interesante. Echemos un vistazo a que solo los datos:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

A continuación, dividir por jamón vs de correo no deseado:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Estos ejemplos deben habilitar hacer trazados similares de las demás columnas para explorar los datos contenidos en ellos.


## <a name="train-and-test-an-ml-model"></a>Entrenar y probar un modelo de m

Ahora vamos a formar a un par de modelos de aprendizaje de máquina para clasificar los correos electrónicos en el conjunto de datos como que contiene un intervalo o ham. Estamos entrenar a un modelo de árbol de decisión y un modelo de bosque aleatorio en esta sección y probar su exactitud de sus predicciones. 

>[AZURE.NOTE] Ya está instalado el paquete de rpart (partición recursiva y árboles de regresión) utilizado en el código siguiente en la máquina virtual de ciencias de datos.


En primer lugar, vamos a dividir el conjunto de datos en conjuntos de formación y prueba:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Y, a continuación, crear un árbol de decisión para clasificar los correos electrónicos.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Este es el resultado:

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

Para determinar cómo realiza en el conjunto de entrenamiento, use el siguiente código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar cómo realiza en el conjunto de prueba:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Probemos ahora también un modelo de bosque aleatorio. Aleatorios bosques entrenar una multitud de árboles de decisión y una clase que es el modo de las clasificaciones de todos los árboles de decisión individual de salida. Proporcionan un equipo más eficaz enfoque de aprendizaje como que corrijan la tendencia de un modelo de árbol de decisión a overfit un conjunto de datos de aprendizaje. 

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Implementar un modelo en Azure m

[Máquina Azure Studio de aprendizaje](https://studio.azureml.net/) (AzureML) es un servicio de nube que hace que sea fácil compilar e implementar modelos de análisis predictiva. Una de las características de AzureML buena es su capacidad para publicar cualquier función R como un servicio web. El paquete AzureML R facilita la implementación hacer directamente desde nuestra sesión R en la DSVM. 

Para implementar el código de árbol de decisión de la sección anterior, debe iniciar sesión en Azure máquina aprendizaje Studio. Necesita el identificador de área de trabajo y un token de autorización para suspire en. Para buscar estos valores e inicializar las variables AzureML con ellos:

Seleccione **configuración** en el menú de la izquierda. Nota el **identificador del área de trabajo**. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Seleccione **Autorización Tokens** desde el menú general y anote el **Token de autorización principal**. ![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Cargar el paquete de **AzureML** y a continuación, establezca los valores de las variables con su ID de token y área de trabajo en la sesión de R en el DSVM:


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Vamos a simplificar el modelo para facilitar la implementar esta demostración. Seleccione las tres variables en el árbol de decisión más cercano a la raíz y crear un nuevo árbol con las tres variables:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Se necesita una función de predicción que las características se toma como entrada y devuelve los valores de predicción:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publicar la función predictSpam en AzureML usando la función **publishWebService** : 

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Esta función toma la función **predictSpam** , crea un servicio web denominado **spamWebService** con entradas definidos y salidas y devuelve información acerca del extremo nuevo.

Ver detalles del servicio web publicado, incluido su punto final de la API y las teclas con el comando de acceso:

    spamWebService[[2]]

Para probar el primer establecen 10 filas de la prueba:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Utilizar otras herramientas disponibles

Las secciones restantes muestran cómo utilizar algunas de las herramientas instaladas en la máquina virtual ciencias de datos de Linux. Aquí encontrará la lista de herramientas descritas:

- XGBoost
- Python
- Jupyterhub
- Sonajero
- PostgreSQL & ardilla SQL
- Almacenamiento de datos SQL Server


## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) es una herramienta que proporciona una implementación de árbol aumentada rápida y precisa.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

También puede llamar XGBoost de python o una línea de comandos.

## <a name="python"></a>Python

Para el desarrollo con Python, las distribuciones de Python Anaconda 2.7 y 3.5 se han instalado en el DSVM. 

>[AZURE.NOTE] La distribución de Anaconda incluye [Condas](http://conda.pydata.org/docs/index.html), que se puede utilizar para crear entornos personalizados para Python que tienen diferentes versiones o paquetes instalados en ellos.

Vamos a leer en la parte del conjunto de datos spambase y clasificar los correos electrónicos con equipos de vector de soporte técnico en scikit-obtener:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para realizar predicciones:

    clf.predict(X.ix[0:20, :])

Para mostrar cómo publicar un extremo AzureML, vamos a hacer un modelo más sencillo las tres variables igual que cuando se publica el modelo de R previamente. 

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar el modelo en AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] Esto solo está disponible para python 2.7 y aún no se admite en 3.5. Ejecutar con **/anaconda/bin/python2.7**.


## <a name="jupyterhub"></a>Jupyterhub

La distribución de Anaconda en la DSVM incluye un bloc de notas de Jupyter, un entorno de varias plataformas para compartir su análisis y código Python, R o Julia. El Bloc de notas de Jupyter se tiene acceso a través de JupyterHub. Iniciar sesión con su nombre de usuario de Linux local y la contraseña en ***https://\<nombre VM DNS o la dirección IP\>: 8000 /***. Todos los archivos de configuración para JupyterHub se encuentran en el directorio **/etc/jupyterhub**.

Varios blocs de notas de ejemplo ya están instalados en la máquina virtual:

- Consulte la [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) para un bloc de notas de ejemplo Python.
- Consulte [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) para un bloc de notas de ejemplo **R** .
- Consulte la [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) para el Bloc de notas de otro ejemplo **Python** .

>[AZURE.NOTE] El idioma de Julia también está disponible desde la línea de comandos en la máquina virtual ciencias de datos de Linux.


## <a name="rattle"></a>Sonajero

[Cascabel](https://cran.r-project.org/web/packages/rattle/index.html) (el R analítica herramienta para obtener fácilmente) es una herramienta de R gráfica de minería de datos. Tiene una interfaz intuitiva que facilita la carga, explorar y transformar datos y crear y evaluar modelos.  El artículo [cascabel: gráfico de minería de datos A para R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) proporciona un tutorial que muestra sus características.

Instale e inicie sonajero con los siguientes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] No es necesario instalar en el DSVM. Pero sonajero puede pedirle que instalar paquetes adicionales cuando se carga.

Sonajero utiliza una interfaz basada en la pestaña. La mayoría de las pestañas corresponde a los pasos del [Proceso de ciencias de datos](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), como cargar datos o explorarlo. El proceso de ciencias datos fluye de izquierda a derecha a través de las pestañas. Pero la última ficha contiene un registro de los comandos de R ejecutar sonajero. 


Para cargar y configurar el conjunto de datos:

- Para cargar el archivo, seleccione la ficha **datos** , a continuación 
- Elija el selector situado junto al **nombre de archivo** y seleccione **spambaseHeaders.data**. 
- Para cargar el archivo. Seleccione **Ejecutar** en la fila superior de botones. Debería ver un resumen de cada columna, incluidos el tipo de datos identificados, ya sea una entrada, un destino u otro tipo de variable y el número de valores únicos.
- Sonajero ha identificado correctamente la columna de **correo no deseado** como destino. Seleccione la columna de correo no deseado y luego establezca el **Tipo de datos de destino** en **Categoric**.

Para explorar los datos: 

- Seleccione la ficha **Explorar** . 
- Haga clic en **Resumen**, a continuación, **Ejecutar**, para ver información sobre los tipos de variables y algunas estadísticas de resumen. 
- Para ver otros tipos de estadísticas acerca de cada variable, seleccione otras opciones como **describir** o **conceptos básicos**.

La ficha **Explorar** también permite generar varios trazados precisos. Para trazar un histograma de los datos:


- Seleccione **distribuciones**.
- Consulte **histograma** para **word_freq_remove** y **word_freq_you**.
- Seleccione **Ejecutar**. Debería ver dos trazados densidad en una sola ventana de gráficos, donde está claro que la palabra "usted" aparece con mayor frecuencia en los correos electrónicos de "quitar".

Los gráficos de correlación también son interesantes. Para crear uno:


- Elija **correlación** como **tipo**, a continuación 
- Seleccione **Ejecutar**. 
- Sonajero advierte que recomienda un máximo de 40 variables. Seleccione **Sí** para ver el trazado. 

Hay algunos correlación interesantes que se plantean: "tecnologías" está convenientemente asociado con "CV" y "prácticas", por ejemplo. Está también convenientemente asociado a "650", porque el código de área de los proveedores de fondos de conjunto de datos es 650.

Los valores numéricos de las correlación entre palabras están disponibles en la ventana Explorar. Es interesante nota, por ejemplo, que "tecnología" negativo está asociada con "la" y "dinero".

Sonajero puede transformar el conjunto de datos para tratar algunos problemas habituales. Por ejemplo, le permite cambiar la escala de características, separar valores que faltan, controlar a valores atípicos y quitar las variables u observaciones con datos que faltan. Sonajero también puede identificar las reglas de asociación entre observaciones o variables. Estas fichas están fuera del ámbito de este tutorial introductorio.

Sonajero también puede realizar análisis de clúster. Vamos a excluir algunas características para facilitar la lectura de los resultados. En la pestaña **datos** , elija **Ignorar** junto a cada una de las variables excepto estos diez elementos:

- word_freq_hp
- word_freq_technology
- word_freq_george
- word_freq_remove
- word_freq_your
- word_freq_dollar
- word_freq_money
- capital_run_length_longest
- word_freq_business
- correo no deseado

A continuación, vaya a la pestaña **Diseño** , elija **KMeans**y establezca el *número de clústeres* en 4. A continuación, en **Ejecutar**. Los resultados se muestran en la ventana de resultados. Un clúster tiene alta frecuencia de "george" y "CV" y probablemente es un correo electrónico de empresa legítimos.

Para crear un modelo de aprendizaje del equipo de árbol de decisión simple: 

- Seleccione la ficha de **modelo** 
- Elija **árbol** como **tipo**. 
- Seleccione **Ejecutar** para mostrar el árbol en forma de texto en la ventana de resultados. 
- Seleccione el botón **dibujar** para ver una versión gráfica. Esto es muy similar al árbol que se ha obtenido mediante una versión anterior de *rpart*.

Una de las características de sonajero buena es su capacidad para ejecutar varios métodos de aprendizaje de equipo y evaluarlos rápidamente. Aquí tenemos el procedimiento:

- Seleccione **todo** para el **tipo**. 
- Seleccione **Ejecutar**. 
- Después de que finalice puede haga clic en cualquier **tipo**, como **SVM**y ver los resultados. 
- También puede comparar el rendimiento de los modelos de la validación mediante la ficha **Evaluate** . Por ejemplo, la selección de la **Matriz de Error** le muestra la matriz de confusiones, error general y error de clase promedio para cada modelo en el conjunto de validación. 
- También puede trazar curvas ROC, realizar análisis de sensibilidad y otros tipos de evaluaciones de modelo.

Una vez que haya terminado de crear modelos, seleccione la ficha de **registro** para ver el código de R ejecutar sonajero durante la sesión. Puede seleccionar el botón **Exportar** para guardarlo. 

>[AZURE.NOTE] Hay un error en la versión actual de sonajero. Para modificar la secuencia de comandos o utilizar repetir los pasos más adelante, debe insertar un carácter # delante *exportar este registro...* en el texto del registro. 


## <a name="postgresql--squirrel-sql"></a>PostgreSQL & ardilla SQL

La DSVM incluye PostgreSQL instalado. PostgreSQL es una base de datos relacional sofisticada, Abrir origen. Esta sección muestra cómo cargar el conjunto de datos de correo no deseado en PostgreSQL y, a continuación, la consulta.

Antes de poder cargar los datos, debe permitir una autenticación de contraseña en el host local. En el símbolo del sistema:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

En la parte inferior del archivo de configuración son varias líneas que detallan las conexiones permitidas:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Cambie la línea "IPv4 local conexiones" para usar md5 en lugar de identificación, de modo que nos podemos inicie sesión con un nombre de usuario y contraseña:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Y reinicie el servicio de postgres:

    sudo systemctl restart postgresql

Para iniciar psql, un terminal interactivo para PostgreSQL, como el usuario postgres integrados, ejecute el siguiente comando desde un símbolo del sistema:

    sudo -u postgres psql

Crear una nueva cuenta de usuario, con el mismo nombre de usuario como cuenta de Linux que haya iniciado sesión como y asignarle una contraseña:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

A continuación, inicie sesión en psql el usuario:

    psql

E importar los datos en una nueva base de datos:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Ahora, vamos a explorar los datos y ejecutar algunas consultas con **SQL ardilla**, una herramienta gráfica que le permite interactúan con bases de datos a través de un controlador JDBC.

Para empezar, inicie SQL ardilla desde el menú aplicaciones. Para configurar el controlador:

- Seleccione **Windows**, a continuación, **los controladores de vista**. 
- Haga clic en **PostgreSQL** y seleccione **Modificar controlador**. 
- Seleccione **ruta adicional de la clase**y luego en **Agregar**. 
- Escriba ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** para el **nombre de archivo** y 
- Seleccione **Abrir**.
- Elija controladores de la lista, seleccione **org.postgresql.Driver** en **Nombre de la clase**y haga clic en **Aceptar**.

Para configurar la conexión al servidor local:
 
- Seleccione **Windows**, a continuación, **ver alias.** 
- Elija la **+** botón para realizar un nuevo alias. 
- Nombre de *base de datos de correo no deseado*, elija **PostgreSQL** en la lista desplegable **controlador** .
- Establecer la dirección URL para *jdbc:postgresql://localhost/spam*. 
- Escriba su *nombre de usuario* y *contraseña*. 
- Haga clic en **Aceptar**. 
- Para abrir la ventana de **conexión** , haga doble clic en el alias de la ***base de datos de correo no deseado*** . 
- Seleccione **Conectar**.

Para ejecutar algunas consultas:

- Seleccione la pestaña **SQL** .
- Escriba una consulta simple como `SELECT * from data;` en el cuadro de texto de la consulta en la parte superior de la pestaña SQL. 
- Presione **Ctrl-Entrar** para ejecutarlo. De forma predeterminada ardilla SQL devuelve las primeros 100 filas de la consulta. 

Hay muchos más consultas que se podría ejecutar para explorar los datos. ¿Por ejemplo, la frecuencia de la palabra, *Asegúrese de* diferencias entre correo no deseado y jamón?

    SELECT avg(word_freq_make), spam from data group by spam;

O bien, ¿cuáles son las características de correo electrónico que contienen con frecuencia *3d*?

    SELECT * from data order by word_freq_3d desc;

La mayoría de correos electrónicos que tienen una alto aparición de *3d* se aparentemente de correo no deseado, por lo que podría ser una característica útil para crear un modelo de predicción para clasificar los correos electrónicos.

Si desea realizar el aprendizaje con datos almacenados en una base de datos PostgreSQL, considere la posibilidad de usar [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Almacenamiento de datos SQL Server

Almacén de datos de SQL Azure es una basada en nube escalado base de datos capaz de procesar grandes volúmenes de datos relacionales y no relacionales. Para obtener más información, vea [¿Qué es el almacén de datos de SQL Azure?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para conectarse al almacén de datos y crear la tabla, ejecute el siguiente comando desde un símbolo del sistema:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

A continuación, en el símbolo de sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copiar datos con bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] Fin de línea en el archivo descargado es el estilo de Windows, pero bcp espera estilo UNIX, por lo que necesitamos saber bcp con el indicador - r.

Y consulta con sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

También puede consultar con ardilla SQL. Siga los mismos pasos para PostgreSQL, con el controlador de JDBC de Microsoft MSSQL Server, que se encuentra en ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general de temas que le guíe durante las tareas que forman el proceso de ciencias datos en Azure, consulte [El proceso de ciencias de datos de grupo](http://aka.ms/datascienceprocess).

Para obtener una descripción de los otros tutoriales de llevar a cabo que muestran los pasos del proceso de ciencias de datos de grupo para escenarios específicos, vea [tutoriales de proceso de ciencias de datos de equipo](data-science-process-walkthroughs.md). Los tutoriales también muestran cómo combinar servicios y herramientas de la nube y locales en un flujo de trabajo o la canalización de crear una aplicación inteligente.

