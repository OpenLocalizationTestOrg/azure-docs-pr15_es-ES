<properties 
    pageTitle="Modelo de clúster | Microsoft Azure" 
    description="Modelo de clúster" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Modelo de clúster    

¿Cómo podemos predecir grupos de comportamientos de crédito titular de la tarjeta con el fin de reducir el riesgo de cargo pérdidas de empresas emisoras de tarjetas de crédito? ¿Cómo podemos definir grupos de características de personalidad de empleados con el fin de mejorar el rendimiento en el trabajo? ¿Cómo pueden médicos clasificar pacientes en grupos en función de las características de sus enfermedades? En principio, todas estas preguntas pueden responder a través de análisis de clúster.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Análisis de clúster clasifican un conjunto de observaciones en dos o más grupos desconocidos mutuamente basados en combinaciones de variables. El propósito de análisis de clúster es descubrir un sistema de organizar observaciones, normalmente personas o sus características, en grupos, donde los miembros de los grupos de compartan propiedades en común. Este [servicio](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utiliza la metodología de medios K, una técnica de agrupación utiliza con frecuencia, a datos arbitrario de clúster en grupos. Este servicio web toma los datos y el número de k clústeres como entrada y genera predicciones de cuál de los grupos de k al que pertenece cada observaciones. 

>Este servicio web podría haberse consumido por usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.  

##<a name="consumption-of-web-service"></a>Consumo de servicio web   
Este servicio web agrupa los datos en un conjunto de grupos de k y envía la asignación a un grupo para cada fila. El servicio web espera la entrada de datos como una cadena donde filas están separadas por comas (,) y las columnas están separadas por punto y coma (;) del usuario final. El servicio web espera 1 fila a la vez. Un conjunto de datos de ejemplo podría tener este aspecto:

![Datos de ejemplo][1]

Supongamos que el usuario desea separar estos datos en 3 grupos se excluyen mutuamente. Los datos de entrada para el conjunto de datos anterior sería lo siguiente: valor = "10; 5; 2,18; 1; 6,7; 5; 5,22; 3; 4,12; 2; 1,10; 3; 4"; k = "3". El resultado es la pertenencia a previsto para cada una de las filas.

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (es una aplicación de ejemplo [a continuación](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




##<a name="creation-of-web-service"></a>Creación de servicio web  
>Este servicio web se creó con el aprendizaje Azure. Para una prueba gratuita, así como vídeos de introducción sobre la creación de experimentación y [servicios web de publicación](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). A continuación, encontrará una captura de pantalla de la ensayo que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del ensayo.

Desde dentro de aprendizaje del equipo de Azure, un ensayo en blanco nuevo se creó y dos [Ejecutar Script de R] [ execute-r-script] módulos colocados en el área de trabajo. El esquema de datos se creó con una simple [Secuencia de comandos de ejecutar R][execute-r-script]. A continuación, el esquema de datos se ha vinculado a la sección de modelo de clúster nuevo creada con una [Secuencia de comandos de ejecutar R][execute-r-script]. En la [Secuencia de comandos de ejecutar R] [ execute-r-script] usado para el modelo de clúster, el servicio web utiliza, a continuación, la función "k significa", que es preintegrada en la [Secuencia de comandos de ejecutar R] [ execute-r-script] de aprendizaje del equipo de Azure.    
   

     
![Flujo de ensayo][3]

####<a name="module-1"></a>Módulo 1: 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Módulo 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Limitaciones
Este es un ejemplo de un servicio web clústeres muy sencillo. Como puede verse en el código de ejemplo anterior, no se implementa la detección de error y el servicio supone todo lo que es una variable continua (sin categorías características permitidas), como servicio solo entradas valores numéricos en el momento de la creación de este servicio web. Además, el servicio actualmente controladores de tamaño de datos limitado, debido a la naturaleza de convocatoria y respuesta de la llamada de servicio web y el hecho de que el modelo es que se ajustan cada vez que se llama al servicio web. 

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
