<properties 
    pageTitle="Diccionario en función de análisis de opinión | Microsoft Azure" 
    description="Diccionario en función de análisis de opinión" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Diccionario en función de análisis de opinión 

¿Cómo puede medir actitudes marcas o temas de redes sociales en línea y opiniones de los usuarios, como Facebook publica, tweets, revisiones, etcetera.? Análisis de opinión proporciona un método para analizar dichas preguntas.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Por lo general, hay dos métodos para el análisis de opinión. Uno usa un algoritmo de aprendizaje supervisados y el otro puede tratará como aprendizaje sin supervisión. Un algoritmo de aprendizaje controlados generalmente crea un modelo de clasificación de un gran cuerpo anotado. Su precisión principalmente se basa en la calidad de las anotaciones y el proceso de entrenamiento tardan mucho tiempo. Además, cuando se aplica el algoritmo a otro dominio, el resultado normalmente no es bueno. En comparación con la supervisadas usos de aprendizaje sin supervisión de aprendizaje basado en el diccionario, un diccionario de opinión, que no requiere almacenar un cuerpo de datos de gran tamaño y formación: lo que todo el proceso con mayor rapidez. 

Nuestro [servicio](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) se basa en la política de subjetividad MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), que es una de las políticas de subjetividad utilizadas con más frecuencia. Hay 5097 negativas y 2533 positivas palabras en MPQA. Y todas estas palabras anotados como polaridad altas o bajas. El cuerpo todo está bajo licencia pública General GNU. El servicio web se puede aplicar a cualquier oración breve, como tweets y entradas de Facebook. 

>Por ejemplo usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local podría consumir este servicio web. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.

##<a name="consumption-of-web-service"></a>Consumo de servicio web

Los datos de entrada pueden ser cualquier texto, pero el servicio web funciona mejor con frases breves. El resultado es un valor numérico entre -1 y 1. Cualquier valor de 0 indica que la opinión del texto es negativa; positivo si superior a 0. El valor absoluto del resultado indica la intensidad de la opinión asociada. 

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (es una aplicación de ejemplo [a continuación](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



La entrada es "Hoy es un buen día". El resultado es "1", que indica una opinión positivo asociado con la entrada de la oración. 

##<a name="creation-of-web-service"></a>Creación de servicio web
>Este servicio web se creó con el aprendizaje Azure. Para una prueba gratuita, así como vídeos de introducción sobre la creación de experimentación y [servicios web de publicación](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). A continuación, encontrará una captura de pantalla de la ensayo que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del ensayo.


Desde dentro de aprendizaje del equipo de Azure, se creó un ensayo en blanco nuevo. La siguiente ilustración muestra el flujo de ensayo de análisis de opinión basada en biblioteca. El archivo "sent_dict.csv" es la política de subjetividad MPQA y se establece como una de las entradas de [Secuencia de comandos de ejecutar R][execute-r-script]. Otra entrada es una revisión muestra del conjunto de datos de revisión de Amazon para prueba, donde se realiza la selección, modificación de nombre de columna y las operaciones de separación. Usamos un paquete de hash para almacenar la política de subjetividad en la memoria y acelerar el proceso de cálculo de puntuación. Todo el texto se crean los tokens paquete "tm" y en comparación con la palabra en el diccionario de opinión. Por último, se calculará una puntuación agregando el grosor de cada palabra subjetiva en el texto. 

###<a name="experiment-flow"></a>Flujo de ensayo:

![experimentar flujo][2]


####<a name="module-1"></a>Módulo 1:
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Instalar el paquete de hash
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Limitaciones

Desde la perspectiva del algoritmo, análisis de opinión basada en biblioteca es una herramienta de análisis de la idea general, que no puede funcionar mejor que el método de clasificación para campos específicos. El problema de negación no es atenderse bien. Nos codificar negación varias palabras en el programa, pero una manera mejor está usando un diccionario de negación y generar algunas reglas. El servicio web funciona mejor en oraciones cortos y simples, como tweets y entradas de Facebook, que en oraciones largos y complejos como revisiones de Amazon. 

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 
