<properties 
    pageTitle="Realizar una previsión - ETS + STL | Microsoft Azure" 
    description="Realizar una previsión - ETS + STL" 
    services="machine-learning" 
    documentationCenter="" 
    authors="xueshanz" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="yijichen"/> 

#<a name="forecasting---ets--stl"></a>Realizar una previsión - ETS + STL  

Este [servicio web]( https://datamarket.azure.com/dataset/aml_labs/demand_forecast) implementa modelos estacional tendencia descomposición (STL) y suavizado exponencial (HTA) para generar predicciones basados en los datos históricos proporcionados por el usuario. ¿La demanda de un producto específico aumentará este año? ¿Predecir las ventas de productos para la combinación de Navidad, por lo que ¿planear de manera eficaz mi inventario? Modelos de previsión son apt tratar interrogantes. Dados los últimos datos, examinan estos modelos tendencias ocultas y estacionalidad predecir tendencias futuras. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
 
>Este servicio web podría haberse consumido por usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.  
 
##<a name="consumption-of-web-service"></a>Consumo de servicio web 

Este servicio acepta 4 argumentos y calcula las previsiones.
Los argumentos de entrada son:

* Frecuencia: indica la frecuencia de los datos sin formato (diaria o semanal o mensual o trimestral o anual).
* Horizonte - futuro previsión de período de tiempo.
* Fecha: agregar en los nuevos datos de la serie de tiempo por vez.
* Valor: agregar los nuevos valores de datos de serie de tiempo.

El resultado del servicio es los valores calculados de previsión.
 
Entrada de ejemplo podría ser: 

* Frecuencia - 12
* Horizonte - 12
* Fecha - 1/15/2012; 2/15/2012 15/3/2012; 4/15/2012; 5/15/2012; 6/15/2012; 7/15/2012; 8 / 15/2012 9/15/2012 15/10/2012; 15/11/2012; 12/15/2012; 1/15/2013; 2/15/2013 15/3/2013; 4/15/2013; 5/15/2013; 6/15/2013; 15/7/2013; 8 / 15/2013 9/15/2013 15/10/2013; 15/11/2013; 12/15/2013; 1/15/2014 2/15/2014; 3/15/2014; 4/15/2014; 5/15/2014; 15/6/2014; 15/7/2014; 8 / 15/2014; 9/15/2014
* Valor - 3.479; 3,68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; 3.65; 3.709; 3.682; 3.511; 3.429 3.51; 3.523; 3.525; 3.626; 3.695; 3.711; 3.711; 3.693; 3.571; 3.509

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (es una aplicación de ejemplo [a continuación](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };         var json = JsonConvert.SerializeObject(input);
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

Desde dentro de aprendizaje del equipo de Azure, se creó un ensayo en blanco nuevo. Se ha cargado los datos de entrada de muestra con un esquema de datos predefinidos. Vinculado al esquema de datos es un [Script de R ejecutar] [ execute-r-script] módulo, que genera STL y ETS modelos de previsión utilizando 'stl', 'ets' y 'previsión' funciones de R. 

###<a name="experiment-flow"></a>Flujo de ensayo:

![Flujo de ensayo][2]

####<a name="module-1"></a>Módulo 1:
 
    # Add in the CSV file with the data in the format shown below 
![Datos de ejemplo][3]   

####<a name="module-2"></a>Módulo 2:

    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # Fit a time series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- stl(train_ts,  s.window="periodic")
    train_model <- forecast(fit1, h = data$horizon, method = 'ets')
    plot(train_model)
    
    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # Data output
    maml.mapOutputPort("data.forecast");

##<a name="limitations"></a>Limitaciones 

Este es un ejemplo sencillo para ETS + STL previsión. Como puede verse en el código de ejemplo anterior, no se implementa detección de error y el servicio se supone que todas las variables son valores continua/positivo y la frecuencia debe ser un entero mayor que 1. La longitud de los vectores de fecha y el valor debe ser el mismo y la longitud de la serie temporal debe ser mayor que 2 * frecuencia. La variable de fecha debe cumplir el formato ' mm/dd/aaaa'.

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
