<properties 
    pageTitle="Diferencia de prueba de proporciones | Microsoft Azure" 
    description="Diferencia de prueba de proporciones" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Diferencia de prueba de proporciones


¿Son dos proporciones estadística diferente? Supongamos que un usuario desea comparar dos películas para determinar si una película tiene una parte mayor del 'le gusta' cuándo con respecto a la otra. Con un tamaño de las muestras, podría ser una diferencia entre las proporciones 0,50 y 0,51 estadística significativa. Con un pequeño ejemplo, no puede haber suficientes datos para determinar si estas proporciones son realmente diferentes. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) se realiza una prueba de hipótesis de la diferencia de dos proporciones basada en la entrada de usuario del número de éxitos y el número total de pruebas para los grupos de 2 comparación. En un escenario posible, este servicio web se puede llamar desde dentro de una aplicación de comparación de película, que indica que el usuario si una de las películas es 'gustó' más a menudo que otros, según su clasificación de película.

>Este servicio web podría haberse consumido por usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.


##<a name="consumption-of-web-service"></a>Consumo de servicio web

Este servicio acepta 4 argumentos y una hipótesis de prueba de proporciones.

Los argumentos de entrada son:

* Successes1 - número de eventos de éxito de ejemplo 1.
* Successes2 - número de eventos de éxito de ejemplo 2.
* Total1 - tamaño del ejemplo 1.
* Total2 - tamaño de muestra 2.

El resultado del servicio es el resultado de la hipótesis probar junto con el valor de p estadística, df, chi-square y proporción en límites de 1/2 y el intervalo de confianza de ejemplo.

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (es una aplicación de ejemplo [a continuación](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

Desde dentro de aprendizaje del equipo de Azure, un ensayo en blanco nuevo se creó con dos [Ejecutar Script de R] [ execute-r-script] módulos. En el primer módulo que se define el esquema de datos, mientras que el segundo módulo utiliza el comando prop.test R para realizar la prueba de hipótesis de 2 proporciones. 


###<a name="experiment-flow"></a>Flujo de ensayo:

![Flujo de ensayo][2]


####<a name="module-1"></a>Módulo 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Módulo 2:

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Limitaciones 

Este es un ejemplo sencillo para una prueba de diferencia en 2 proporciones. Como puede verse en el código de ejemplo anterior, no se implementa la detección de error y el servicio se supone que todas las variables están continuas.

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
