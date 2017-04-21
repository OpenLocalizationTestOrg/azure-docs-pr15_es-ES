<properties 
    pageTitle="Máquina recomendaciones de aprendizaje: Integración de JavaScript | Microsoft Azure" 
    description="Documentación de Azure recomendaciones de aprendizaje de máquina - integración de JavaScript:" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Máquina Azure aprendizaje recomendaciones - integración de JavaScript

>[AZURE.NOTE]Debe comenzar a usar el servicio cognitivas API de recomendaciones en lugar de esta versión. El servicio de recomendaciones cognitivas se van a reemplazar este servicio y las nuevas características se desarrollará allí. Tiene nuevas capacidades como lotes de soporte técnico, una mejor API Explorer, una experiencia de suscripción y facturación de superficie, más coherente API más limpio, etcetera.
> Más información acerca de la [migración al nuevo servicio cognitivas](http://aka.ms/recomigrate)


Este documento se describe cómo integrar su sitio con JavaScript. JavaScript permite enviar eventos de adquisición de datos y consumir recomendaciones una vez que cree un modelo de recomendación. Todas las operaciones realizadas mediante JS pueden hacerse también del lado del servidor.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. información general
Integración de su sitio con Azure m recomendaciones constar en 2 fases:

1.  Enviar eventos en Azure m recomendaciones. Esto le permitirá crear un modelo de recomendación.
2.  Utilizar las recomendaciones. Una vez creado el modelo puede consumir las recomendaciones. (Este documento no explica cómo crear un modelo, lea la Guía de inicio rápido para obtener más información acerca de cómo).


<ins>Fase I</ins>

En la primera fase insertar en sus páginas html una pequeña biblioteca de JavaScript que permite a la página Enviar eventos, tal como aparecen en la página html en servidores de Azure m recomendaciones (a través de Data Market):

![Dibujo1][1]

<ins>Fase II</ins>

En la segunda fase cuando desee mostrar las recomendaciones en la página Seleccionar una de las siguientes opciones:

1 el servidor (en la fase de representación de la página) llamadas Azure m recomendaciones Server (a través de Data Market) para obtener recomendaciones. Los resultados incluyen una lista de elementos id. Su servidor se necesita mejorar los resultados con los elementos de metadatos (por ejemplo, imágenes, descripción) y enviar la página creada en el explorador.

![Drawing2][2]

2.haga la otra opción es usar el archivo de JavaScript pequeño de la fase uno para obtener una lista de elementos recomendados. Los datos recibidos aquí están más simple que la primera opción.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. requisitos previos de

1. Crear un modelo nuevo con la API. Acerca de cómo hacerlo, consulte a la Guía de inicio rápido.
2. Codificar su &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; con base 64. (Esto se utilizará para la autenticación básica para activar el código JS llamar a la API).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. enviar eventos de adquisición de datos mediante JavaScript
Los siguientes pasos facilitan el envío eventos:

1.  Incluir JQuery biblioteca en el código. Puede descargarla de nuget en la siguiente URL.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Incluir la biblioteca de secuencia de comandos de recomendaciones Java desde la siguiente URL: http://aka.ms/RecoJSLib1

3.  Iniciar la biblioteca de Azure m recomendaciones con los parámetros adecuados.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Enviar el evento adecuado. Vea detallada sección debajo de todo tipo de eventos (ejemplo de evento click)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Limitaciones y compatibilidad del explorador
Se trata de una implementación de referencia y se le asigna como está. Debe admitir todos los exploradores principales.

###<a name="32-type-of-events"></a>3.2. Tipo de eventos
Existen 5 tipos de eventos que admite la biblioteca: haga clic en, haga clic en la recomendación, agregar al carro de la tienda, quitar de carro de la tienda y comprar. Hay un evento adicional que se usa para establecer el contexto de usuario denominado inicio de sesión.

####<a name="321-click-event"></a>3.2.1. Haga clic en evento
Este evento debe usarse siempre que un usuario hace clic en un elemento. Normalmente cuando el usuario hace clic en un elemento se abre una página nueva con los detalles del elemento; en esta página debe activarse este evento.

Parámetros:
- evento (cadena, obligatoria) - "haga clic en"
- elemento (cadena, obligatoria) - identificador único del elemento
- itemName (cadena, opcional) - el nombre del elemento
- Descripción de producto (cadena, opcional) - la descripción del elemento
- itemCategory (cadena, opcional) - la categoría del elemento
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

O con datos opcionales:

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Recomendación, haga clic en evento
Este evento debe usarse siempre que un usuario hace clic en un elemento que se ha recibido de Azure m recomendaciones como un elemento recomendado. Normalmente cuando el usuario hace clic en un elemento se abre una página nueva con los detalles del elemento; en esta página debe activarse este evento.

Parámetros:
- evento (cadena, obligatoria) - "recommendationclick"
- elemento (cadena, obligatoria) - identificador único del elemento
- itemName (cadena, opcional) - el nombre del elemento
- Descripción de producto (cadena, opcional) - la descripción del elemento
- itemCategory (cadena, opcional) - la categoría del elemento
- inicia (cadena matriz, opcional): las semillas que genera la consulta de recomendación.
- recoList (cadena matriz opcional) - el resultado de la solicitud de recomendación que generó el elemento que se ha hecho clic.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

O con datos opcionales:

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Agregar evento de carro de la compra
Este evento se debe usar cuando el usuario agrega un elemento a la cesta.
Parámetros:
* evento (cadena, obligatoria) - "addshopcart"
* elemento (cadena, obligatoria) - identificador único del elemento
* itemName (cadena, opcional) - el nombre del elemento
* Descripción de producto (cadena, opcional) - la descripción del elemento
* itemCategory (cadena, opcional) - la categoría del elemento
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Quitar evento carro de la compra
Este evento se debe utilizar cuando el usuario elimina un elemento a la cesta.

Parámetros:
* evento (cadena, obligatoria) - "removeshopcart"
* elemento (cadena, obligatoria) - identificador único del elemento
* itemName (cadena, opcional) - el nombre del elemento
* Descripción de producto (cadena, opcional) - la descripción del elemento
* itemCategory (cadena, opcional) - la categoría del elemento
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Evento de compra
Este evento se debe utilizar cuando el usuario comprado su carro de la compra.

Parámetros:
* evento (cadena) - "compra"
* comprados ([]) - matriz contiene una entrada para cada producto comprado.<br><br>
Formato comprado:
    * elemento (cadena) - identificador único del elemento.
    * Contar (int o string) - número de elementos que se hayan comprado.
    * precio (flotante o cadena) - campo opcional - el precio del elemento.

El ejemplo siguiente muestra la compra de 3 elementos (33, 34, 35), dos con todos los campos (elemento, cantidad, precio) y uno (elemento 34) sin un precio.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Evento de inicio de sesión de usuario
Biblioteca de Azure m recomendaciones evento crea y utilizar una cookie para identificar los eventos que se ha suministrado desde el mismo explorador. Para mejorar los resultados del modelo Azure m recomendaciones permite establecer una identificación único usuario que reemplace el uso de cookies.

Este evento se debe utilizar después el inicio de sesión de usuario a su sitio.

Parámetros:
* evento (cadena) - "userlogin"
* usuario (cadena) - identificación único del usuario.
        <script>
  Si (typeof AzureMLRecommendationsEvent == "no definido") {AzureMLRecommendationsEvent = [];} AzureMLRecommendationsEvent.push ({evento: "userlogin" usuario: "ABCD10AA"});      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. consumir recomendaciones a través de JavaScript
El código que consume la recomendación es desencadenado por algún evento de JavaScript por página Web del cliente. La respuesta de recomendación incluye los identificadores de elementos recomendado, sus nombres y sus clasificaciones. Es mejor usar esta opción solo para una visualización de la lista de los elementos recomendadas, debe realizar un control más complejo (por ejemplo, para agregar los metadatos del elemento) sobre la integración del lado servidor.

###<a name="41-consume-recommendations"></a>4.1 consumir recomendaciones
Para consumir recomendaciones que debe incluyen las bibliotecas de JavaScript necesarias en la página y para llamar a AzureMLRecommendationsStart. Consulte la sección 2.

Consumir recomendaciones para uno o varios elementos que necesita llamar a un método denominado: AzureMLRecommendationsGetI2IRecommendation.

Parámetros:
* elementos (matriz de cadenas) - uno o más elementos para obtener recomendaciones para. Si utiliza una versión de Fbt, a continuación, puede establecer aquí solo un elemento.
* numberOfResults (int) - número de resultados necesarios.
* includeMetadata (boolean, opcional) - si establecido en 'true' indica que el campo de metadatos debe rellenarse en el resultado.
* Devuelve la función - una función que controlará las recomendaciones de procesamiento. Los datos se devuelven como una matriz de:
    * Identificador exclusivo de elemento: elemento
    * nombre: nombre del elemento (si existe en el catálogo)
    * tipo de voto - clasificación de recomendación
    * metadatos, una cadena que representa los metadatos del elemento

Ejemplo: 8 recomendaciones para 64f6eb0d-947a-4c18-a16c-888da9e228ba"elemento" solicita el siguiente código (y no especificando includeMetadata - implícitamente dice que no se requiere ninguna metadatos), a continuación, concatenar los resultados en un búfer.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
