<properties 
    pageTitle="Distribución binomial Suite | Microsoft Azure" 
    description="Conjunto de distribución binomial." 
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


#<a name="binomial-distribution-suite"></a>Conjunto de distribución binomial.




El conjunto de la distribución Binomial es un conjunto de los servicios de web muestra ([Generador Binomial](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Calculadora de probabilidad]( https://datamarket.azure.com/dataset/aml_labs/bdp4), [Quantile calculadora]( https://datamarket.azure.com/dataset/aml_labs/bdq5)) que ayudan a generar y cómo tratar las distribuciones binomial. Permiten a los servicios de generar una secuencia de distribución binomial de cualquier longitud, calcular quantiles fuera de dado probabilidad y cálculo probabilidad de un determinado quantile. Cada uno de los servicios emite resultados diferentes según el servicio seleccionado (consulte la siguiente descripción). El conjunto de la distribución Binomial se basa en la R funciones qbinom, rbinom y pbinom, que se incluyen en el paquete de estadísticas R. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Estos servicios web podrían ser consumidos por los usuarios: potencialmente directamente en el catálogo de soluciones a través de una aplicación móvil, a través de un sitio Web o incluso en un equipo local, por ejemplo. Pero también es la finalidad del servicio web para que actúe como un ejemplo de cómo puede utilizarse el aprendizaje Azure crear servicios web en la parte superior código R. Con unas pocas líneas de código R y hace clic de botón en Azure máquina aprendizaje Studio, apropiados se pueden creados con código de R y publicar como un servicio web. El servicio web se publican en Azure Marketplace y consumido por los usuarios y dispositivos en todo el mundo: se requiere ninguna configuración de infraestructura por el autor del servicio web.

##<a name="consumption-of-web-service"></a>Consumo de servicio web
El conjunto de la distribución Binomial incluye los siguientes 3 servicios.

###<a name="binomial-distribution-quantile-calculator"></a>Calculadora de Quantile de distribución binomial.
Este servicio acepta 4 argumentos de una distribución normal y calcula el quantile asociado.
Los argumentos de entrada son:

- p - un único agregado probabilidad de varias versiones de prueba.  
- tamaño: el número de ensayos.
- probabilidad - la probabilidad de éxito en una versión de prueba.
- Lado - L para la parte inferior de la distribución, U para la parte superior de la distribución. 

El resultado del servicio es la quantile calculado que esté asociada a la probabilidad dada.

###<a name="binomial-distribution-probability-calculator"></a>Calculadora de probabilidad de distribución binomial.
Este servicio acepta 4 argumentos de una distribución binomial y calcula la probabilidad asociada.
Los argumentos de entrada son:

- preguntas - una sola quantile de un evento con una distribución binomial. 
- tamaño: el número de ensayos.
- probabilidad - la probabilidad de éxito en una versión de prueba.
- lado - L para la parte inferior de la distribución, U para la parte superior de la distribución o E que es igual a un único número de éxitos.

El resultado del servicio es la probabilidad calculada que está asociada con el quantile determinado.

###<a name="binomial-distribution-generator"></a>Generador de distribución binomial.
Este servicio acepta 3 argumentos de una distribución binomial y genera una secuencia aleatoria de números que se distribuyen binomially. Los siguientes argumentos deben proporcionarse a él dentro de la solicitud:

- n - número de observaciones. 
- tamaño - número de ensayos.
- probabilidad: probabilidad de éxito.

El resultado del servicio es una secuencia de longitud n con una distribución binomial basada en los argumentos de tamaño y probabilidad.

>Este servicio, como hospedado en Azure Marketplace, es un servicio de OData; estos se puede llamar a través de métodos de entrada o GET. 

Hay varias formas de utilizar el servicio de forma automática (aplicaciones de ejemplo están aquí: [generador](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Calculadora de probabilidad](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Calculadora Quantile](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Código de C# para el consumo de servicio web de inicio:

###<a name="binomial-distribution-quantile-calculator"></a>Calculadora de Quantile de distribución binomial.
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="binomial-distribution-probability-calculator"></a>Calculadora de probabilidad de distribución binomial.
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="binomial-distribution-generator"></a>Generador de distribución binomial.
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

###<a name="binomial-distribution-quantile-calculator"></a>Calculadora de Quantile de distribución binomial.

![Crear área de trabajo][4]

####<a name="module-1"></a>Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####<a name="module-2"></a>Módulo 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###<a name="binomial-distribution-probability-calculator"></a>Calculadora de probabilidad de distribución binomial.

![Crear área de trabajo][5]

####<a name="module-1"></a>Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####<a name="module-2"></a>Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###<a name="binomial-distribution-generator"></a>Generador de distribución binomial.

![Crear área de trabajo][6]

####<a name="module-1"></a>Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####<a name="module-2"></a>Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitaciones 
Estos son ejemplos muy simples que rodea la distribución binomial. Como puede verse en el código de ejemplo anterior, se implementa la detección de error pequeño.

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicación de Azure Marketplace, consulte [aquí](machine-learning-marketplace-faq.md).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 
