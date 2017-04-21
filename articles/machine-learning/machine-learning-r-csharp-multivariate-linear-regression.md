<properties 
    pageTitle="Regresión lineal multivariate | Microsoft Azure" 
    description="Multivariate regresión lineal." 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>Multivariate regresión lineal.   
 

 
Suponga que tiene un conjunto de datos y le gustaría predecir rápidamente una variable dependiente (y) para cada persona (i), en función de las variables independientes. Regresión lineal es una técnica estadística popular utilizada para dicha predicciones. Aquí y de variable dependiente se supone que es un valor continuo.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Podría ser un sencillo escenario donde el investigador intenta predecir el grosor de un individuo (y) en función de su alto (x). Un escenario más avanzado podría ser donde el investigador tiene información adicional para la persona (como peso, género, carrera) e intenta predecir el grosor de la persona. Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) se ajusta el modelo de regresión lineal a los datos y envía el valor previsto (y) para cada una de las observaciones de los datos.

>Este servicio web podría haberse consumido por usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.  

##<a name="consumption-of-web-service"></a>Consumo de servicio web  
Este servicio web proporciona los valores de predicción de la variable dependiente en función de las variables independientes para todas las observaciones. El servicio web espera la entrada de datos como una cadena donde filas están separadas por comas (,) y las columnas están separadas por punto y coma (;) del usuario final. El servicio web espera 1 fila a la vez y espera la primera columna sea la variable dependiente. Un conjunto de datos de ejemplo podría tener este aspecto:

![Datos de ejemplo][1]

Observaciones sin una variable dependiente deben información como "NA" para y. Los datos de entrada para el conjunto de datos anterior sería la cadena siguiente: "10; 5; 2,18; 1; 6,6; 5.3; 2.1,7; 5; 5,22; 3; 4,12; 2; 1, NOD; 3; 4". El resultado es el valor previsto para cada una de las filas en función de las variables independientes. 

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (es una aplicación de ejemplo [a continuación](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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


Desde dentro de aprendizaje del equipo de Azure, un ensayo en blanco nuevo se creó y dos [Ejecutar Script de R] [ execute-r-script] módulos extraídos en el área de trabajo. Este servicio web ejecuta un ensayo Azure el aprendizaje con una secuencia de comandos de R subyacente. Existen 2 partes en este ensayo: definición de esquema y el modelo de aprendizaje + puntuación. El primer módulo define la estructura esperada de entrada conjunto de datos, donde la primera variable es la variable dependiente y el resto de variables es independiente. El segundo módulo se ajusta a un modelo de regresión lineal genérico para los datos de entrada.  
  
![Flujo de ensayo][3]

####<a name="module-1"></a>Módulo 1:
 
####<a name="schema-definition"></a>Definición del esquema  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Módulo 2:
####<a name="lm-modeling"></a>Modelado de LM   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Limitaciones
Este es un ejemplo de un servicio web de regresión lineal varios muy sencillo. Como puede verse en el código de ejemplo anterior, no se implementa la detección de error y el servicio supone todo lo que es una variable continua (sin categorías características permitidas), como servicio solo entradas valores numéricos en el momento de la creación de este servicio web. Además, el servicio actualmente controladores de tamaño de datos limitado, debido a la naturaleza de convocatoria y respuesta de la llamada de servicio web y el hecho de que el modelo es que se ajustan cada vez que se llama al servicio web. 

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
