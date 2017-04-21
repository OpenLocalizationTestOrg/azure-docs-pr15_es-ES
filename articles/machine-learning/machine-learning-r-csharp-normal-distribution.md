<properties 
    pageTitle="Conjunto de aplicaciones de servicio de distribución normal Web | Microsoft Azure" 
    description="Conjunto de aplicaciones de servicio de Web de distribución normal" 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 

#<a name="normal-distribution-suite"></a>Conjunto de distribución normal



El conjunto de la distribución Normal es un conjunto de los servicios de web muestra ([generador]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Quantile calculadora]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculadora de probabilidad]( https://datamarket.azure.com/dataset/aml_labs/ndp5)) que ayudan a generar y administrar las distribuciones de normal. Los servicios permiten generar una secuencia de distribución normal de cualquier longitud, calcular quantiles de una probabilidad dada y calcular la probabilidad de un determinado quantile. Cada uno de los servicios emite resultados diferentes según el servicio seleccionado (consulte la siguiente descripción). El conjunto de la distribución Normal se basa en la R funciones qnorm, rnorm y pnorm, que se incluyen en el paquete de estadísticas de R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Este servicio web podría haberse consumido por usuarios – potencialmente a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web puede publicado en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo con ninguna configuración de infraestructura por el autor del servicio web.  
 

##<a name="consumption-of-web-service"></a>Consumo de servicio web
El conjunto de la distribución Normal incluye los siguientes 3 servicios.

###<a name="normal-distribution-quantile-calculator"></a>Calculadora de Quantile de distribución normal
Este servicio acepta 4 argumentos de una distribución normal y calcula el quantile asociado.

Los argumentos de entrada son:

* p - una sola probabilidad de un evento con la distribución normal. 
* Media: la media de la distribución normal.
* SD - la desviación estándar de la distribución normal. 
* Lado - L para la parte inferior de la distribución y U para la parte superior de la distribución.

El resultado del servicio es la quantile calculado que esté asociada a la probabilidad dada.

###<a name="normal-distribution-probability-calculator"></a>Calculadora de probabilidad de distribución normal
Este servicio acepta 4 argumentos de una distribución normal y calcula la probabilidad asociada.

Los argumentos de entrada son:

* preguntas - una sola quantile de un evento con la distribución normal. 
* Media: la media de la distribución normal.
* SD - la desviación estándar de la distribución normal. 
* Lado - L para la parte inferior de la distribución y U para la parte superior de la distribución.

El resultado del servicio es la probabilidad calculada que está asociada con el quantile determinado.

###<a name="normal-distribution-generator"></a>Generador de distribución normal
Este servicio acepta 3 argumentos de una distribución normal y genera una secuencia aleatoria de números que se distribuyen normalmente. Los siguientes argumentos deben proporcionarse a él dentro de la solicitud:

* n - el número de observaciones. 
* Media: la media de la distribución normal.
* SD - la desviación estándar de la distribución normal. 

El resultado del servicio es una secuencia de longitud n con una distribución normal en función de los argumentos media y sd.

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (aplicaciones de ejemplo están aquí: [generador](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Calculadora de probabilidad](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Calculadora Quantile](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

###<a name="normal-distribution-quantile-calculator"></a>Calculadora de Quantile de distribución normal
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>Calculadora de probabilidad de distribución normal
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>Generador de distribución normal
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Este servicio web se creó con el aprendizaje Azure. Para una prueba gratuita, así como vídeos de introducción sobre la creación de experimentación y [servicios web de publicación](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). 

A continuación, encontrará una captura de pantalla de la ensayo que creó el código de ejemplo y el servicio web para cada uno de los módulos dentro del ensayo.

###<a name="normal-distribution-quantile-calculator"></a>Calculadora de Quantile de distribución normal

Flujo de ensayo:

![Flujo de ensayo][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>Calculadora de probabilidad de distribución normal
Flujo de ensayo:

![Flujo de ensayo][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>Generador de distribución normal
Flujo de ensayo:

![Flujo de ensayo][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitaciones 

Estos son ejemplos muy simples que rodea la distribución normal. Como puede verse en el código de ejemplo anterior, se implementa la detección de error pequeño.

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 
