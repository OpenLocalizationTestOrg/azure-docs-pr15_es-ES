<properties 
    pageTitle="Análisis de supervivencia con el aprendizaje Azure | Microsoft Azure" 
    description="Probabilidad de repetición de eventos de análisis de supervivencia" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>Análisis de supervivencia 

En muchos casos, el resultado principal en evaluación es el momento de un evento de interés. En otras palabras, la pregunta "cuando se produzca este evento?" se le pregunta. Como ejemplo, considere la posibilidad de situaciones donde los datos describen el tiempo transcurrido (días, años, kilometraje, etc.) hasta que el evento de interés (relapse enfermedad, grado de doctorado recibido, error de teclado de freno) se produce. Cada instancia de los datos representa un objeto específico (una paciencia, un estudiante, un coche, etcetera.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) responde a la pregunta "¿Cuál es la probabilidad de que se produzca el evento de interés por n de tiempo para el objeto x?" Al proporcionar un modelo de análisis de supervivencia, este servicio web permite a los usuarios proporcionar datos para entrenar el modelo y la prueba. El tema principal de la ensayo es modelar la longitud del tiempo transcurrido hasta que se produzca el evento de interés. 

>Este servicio web podría haberse consumido por usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.  

##<a name="consumption-of-web-service"></a>Consumo de servicio web

El esquema de datos de entrada del servicio web se muestra en la tabla siguiente. Seis elementos de información se necesitan como entrada: los datos de entrenamiento, comprobar los datos, la dimensión tiempo de interés, el índice de "hora", el índice de dimensión "evento" y los tipos de variables (continua o factor). Los datos de entrenamiento se representan con una cadena, donde las filas están separadas por comas y las columnas están separadas por punto y coma. El número de características de los datos es flexible. Todos los elementos de la cadena de entrada deben ser numéricos. En los datos de entrenamiento, la dimensión "hora" indica que el número de unidades de tiempo (días, años, kilometraje, etc.) transcurridos desde el punto de partida del estudio (un paciencia recibir programas de tratamiento de fármacos, un práctico doctorado inicial de estudiantes, un coche comiencen a estar condicionada por el, etc.) hasta que el evento de interés (la paciencia regreso a uso fármaco, los estudiantes obtener el grado de doctorado, el automóvil con error de teclado de freno etcetera.) se produce. La dimensión "evento" indica si el evento de interés se produce al final del estudio. Un valor de "evento = 1" significa que se produce el evento de interés en el momento indicado por la dimensión "hora"; "evento = 0" significa que no se ha producido el evento de interés por el momento indicado por la dimensión "hora".

- trainingdata - una cadena de caracteres. Filas separadas por comas y columnas separadas por punto y coma. Cada fila incluye dimensión "hora", "evento" dimensión y variables de predicción.
- testingdata - una fila de datos que contiene las variables de predicción de un objeto determinado.
- time_of_interest - el tiempo transcurrido de n de interés.
- index_time - el índice de columna de la dimensión "hora" (a partir de 1).
- index_event - el índice de columna de la dimensión "evento" (a partir de 1).
- variable_types - una cadena de caracteres con puntos y comas como separadores en él. 0 representa variables continuas y 1 representa las variables de factores.


El resultado es la probabilidad de un evento que se producen en un momento específico. 

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (es una aplicación de ejemplo [a continuación](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




La interpretación de esta prueba es la siguiente. Suponiendo que el objetivo de los datos es modelar el tiempo transcurrido hasta el retorno para uso fármaco para los pacientes que ha recibido uno de los programas de tratamiento de dos. El resultado de las lecturas de servicio web: para pacientes ser 35 años, teniendo anterior fármaco tratamiento 2 veces teniendo el programa tratamiento residencial cuánto tiempo, y con el uso de heroína y cocaína, la probabilidad de que devuelve el uso de drogas es 95.64% por día 500.

##<a name="creation-of-web-service"></a>Creación de servicio web

>Este servicio web se creó con el aprendizaje Azure. Para una prueba gratuita, así como vídeos de introducción sobre la creación de experimentación y [servicios web de publicación](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). A continuación, encontrará una captura de pantalla de la ensayo que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del ensayo.

Desde dentro de aprendizaje del equipo de Azure, un ensayo en blanco nuevo se creó y dos [Ejecutar Script de R] [ execute-r-script] módulos extraídos en el área de trabajo. El esquema de datos se creó con una simple [Secuencia de comandos de ejecutar R][execute-r-script], que define el esquema de datos de entrada para el servicio web. Este módulo, a continuación, está vinculado a la segunda [Ejecutar Script de R] [ execute-r-script] módulo que importantes de trabajo. Este módulo realiza preprocesamiento de datos, la creación de modelo y predicciones. En el paso de preprocesamiento de datos, los datos de entrada representados por una cadena larga se transforma y convertidos en un marco de datos. En el paso de creación del modelo, un paquete de R externo "survival_2.37 7.zip" se instala en primer lugar para realizar análisis de supervivencia. A continuación se ejecuta la función "coxph" después de una tarea de procesamiento de datos de serie. Pueden leer los detalles de la función "coxph" para el análisis de supervivencia de la documentación de R. En el paso de predicción, se proporciona una instancia de prueba en el modelo capacitado con la función "surfit" y la curva de supervivencia de esta instancia de prueba se produce como variable "curva". Por último, se obtiene la probabilidad de que el tiempo de interés. 

###<a name="experiment-flow"></a>Flujo de ensayo:

![experimentar flujo][1]

####<a name="module-1"></a>Módulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>Módulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>Limitaciones

Este servicio web puede tomar solo los valores numéricos como variables de la característica (columnas). La columna "evento" puede tomar solo valor 0 o 1. La columna "tiempo" debe ser un entero positivo.

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
