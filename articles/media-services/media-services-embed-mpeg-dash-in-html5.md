<properties 
    pageTitle="Incrustar un vídeo de transmisión adaptación MPEG-guión en una aplicación de HTML5 con DASH.js | Microsoft Azure" 
    description="En este tema se muestra cómo incrustar un vídeo MPEG-guión adaptación transmisión en una aplicación de HTML5 con DASH.js." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incrustar un vídeo de transmisión adaptación MPEG-guión en una aplicación de HTML5 con DASH.js

##<a name="overview"></a>Información general

MPEG-guión es un estándar ISO para la transmisión adaptación de contenido de vídeo, que ofrece ventajas muy significativas para aquellos que deseen ofrecer resultados de transmisión de vídeo de alta calidad, adaptación. Con MPEG-guión, se quitará automáticamente la secuencia de vídeo a una definición inferior cuando la red esté ocupada. Esto reduce la probabilidad de que el Visor ve un vídeo "en pausa" mientras el Reproductor descarga los segundos para reproducir (también conocido como búfer). Mientras reduce la congestión de la red, por separado devolverá el Reproductor de vídeo en una secuencia de calidad superior. La capacidad de adaptar el ancho de banda necesario también el resultado de una hora de inicio rápida para el vídeo. Esto significa que se pueden reproducir en un segmento de calidad inferior fast para descargar los primeros segundos y, a continuación, paso hasta una mayor calidad suficiente una vez el contenido está en el búfer.

Dash.js es un Reproductor de vídeo MPEG-guión de código abierto escrito en JavaScript. Su objetivo es proporcionar un reproductor sólido entre plataformas que puedan reutilizarse libremente en aplicaciones que requieren reproducción de vídeo. Proporciona reproducción MPEG-guión en cualquier explorador compatible con las extensiones W3C de origen multimedia (MSE), hoy Chrome, Microsoft Edge y IE11 (otros exploradores han indicado su intención para admitir MSE). Para obtener más información sobre DASH.js, js vea del repositorio de dash.js GitHub.


##<a name="creating-a-browser-based-streaming-video-player"></a>Crear un Reproductor de vídeo streaming basada en explorador

Para crear una página web sencilla que muestra un Reproductor de vídeo con el se esperaba controles como reproducir, pausa, rebobinar etc., necesitará:

1. Crear una página HTML
1. Agregue la etiqueta de vídeo
1. Agregar el Reproductor dash.js
1. Inicializar el Reproductor
1. Agregar algún estilo CSS
1. Ver los resultados en un explorador que implementa MSE

Puede completar la inicialización del Reproductor en unos pocos de líneas de código de JavaScript. Usa dash.js, realmente es sencillo incrustar vídeo MPEG-guión en las aplicaciones basadas en explorador.

##<a name="creating-the-html-page"></a>Creación de la página HTML

El primer paso es crear una página HTML estándar que contiene el elemento de **vídeo** , guardar este archivo como basicPlayer.html, como se muestra en el ejemplo siguiente:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Agregar el Reproductor DASH.js

Para agregar la implementación de referencia de dash.js a la aplicación, debe conseguir el archivo dash.all.js desde la 1.0 versión de proyecto dash.js. Esto se debe guardar en la carpeta de JavaScript de la aplicación. Este es un archivo de comodidad que reúne todo el código necesario dash.js en un solo archivo. Si tiene un aspecto alrededor del repositorio de dash.js, observará los archivos individuales, probar código y mucho más, pero si todo lo que desee hacer es usar dash.js, el archivo dash.all.js es lo que necesita.

Para agregar el Reproductor dash.js a sus aplicaciones, agregue una etiqueta de secuencia de comandos a la sección de encabezado de basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


A continuación, cree una función para iniciar el Reproductor cargue la página. Agregue la siguiente secuencia de comandos después de la línea en la que se carga dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

En primer lugar, esta función crea un DashContext. Se utiliza para configurar la aplicación para un entorno de tiempo de ejecución específica. Desde el punto de vista técnico define las clases que debe usar el marco de trabajo de inyección de dependencia al construir la aplicación. En la mayoría de los casos, usará Dash.di.DashContext.

A continuación, crear una instancia de la clase principal de dash.js framework, MediaPlayer. Esta clase contiene el núcleo métodos necesarios como reproducirán y pausar, administra la relación con el elemento de vídeo y también administra la interpretación del archivo multimedia presentación descripción (MPD) que describa el vídeo que se reproduzca.

La función startup() de la clase MediaPlayer se denomina para asegurarse de que el Reproductor está listo para reproducir el vídeo. Entre otras cosas, esta función garantiza que todas las clases necesarias (como defina el contexto) se han cargado. Una vez el Reproductor esté listo, puede adjuntar el elemento vídeo mediante la función attachView(). Esto permite MediaPlayer insertar la secuencia de vídeo en el elemento y también controlar reproducción según sea necesario.

Pasar la dirección URL del archivo MPD a MediaPlayer para que sepa sobre el vídeo se espera para reproducir. La función setupVideo() que acaba de crear será necesario ejecutar una vez que la página se haya cargado completamente. Hacer esto mediante el evento onload del elemento de cuerpo. Cambiar el <body> elemento:

    <body onload="setupVideo()">

Por último, establezca el tamaño del elemento de vídeo con CSS. En un entorno de streaming adaptación, es especialmente importante dado que puede cambiar el tamaño del vídeo que se reproduce como reproducción se adapta a condiciones de red cambiantes. En esta demostración simple simplemente forzar el elemento vídeo sea 80% de la ventana del explorador disponible agregando la CSS siguiente a la sección de encabezado de la página:
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Reproducir un vídeo

Para reproducir un vídeo, elija el explorador en el archivo basicPlayback.html y haga clic en Reproducir en el Reproductor de vídeo que se muestra.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vea también

[Desarrollar aplicaciones de Reproductor de vídeo](media-services-develop-video-players.md)

[Repositorio de dash.js GitHub](https://github.com/Dash-Industry-Forum/dash.js) 
