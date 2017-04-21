<properties 
    pageTitle="Complemento de transmisión por secuencias suave para abrir origen multimedia Framework" 
    description="Obtenga información sobre cómo usar el complemento de Azure Media Services Smooth Streaming para el marco de trabajo de medios de origen abierto de Adobe." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Cómo usar la suave Microsoft transmisión complemento para el marco de trabajo de Adobe Abrir origen multimedia

##<a name="overview"></a>Información general


El complemento de Microsoft Smooth Streaming para abrir origen Media Framework 2.0 (SS para OSMF) amplía las capacidades predeterminadas de OSMF y agrega la reproducción de contenido de Microsoft Smooth Streaming a reproductores OSMF nuevos y existentes. El complemento también agrega capacidades de reproducción Smooth Streaming a reproducción de medios de señal (SMP).

SS para OSMF incluye dos versiones del complemento:

- Complemento de Smooth Streaming estático para OSMF (.swc)

- Complemento de Smooth Streaming dinámico para OSMF (.swf)

Este documento se supone que el lector tiene un conocimiento general de OSMF y OSMF de trabajo complementos. Para obtener más información sobre OSMF, consulte la documentación en el [sitio OSMF oficial](http://osmf.org/).

###<a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming complemento para de OSMF 2.0

Es compatible con el complemento de carga y la reproducción de contenido de Smooth Streaming a petición con las características siguientes:

- Reproducción Smooth Streaming a petición (reproducir, pausa, Seek, detener)
- Smooth Streaming directo reproducción (reproducir)
- Funciones DVR directo (pausa, Seek, reproducción de DVR, poner en marcha)
- Compatibilidad con códecs de vídeo - h.264.
- Compatibilidad con códecs de Audio - AAC
- Varios idiomas audio cambiar con la API integradas de OSMF
- Selección de calidad de reproducción de Max con API integradas de OSMF
- Sidecar subtítulos con el complemento de títulos OSMF
- Adobe&reg; Flash&reg; Reproductor 11,4 o posterior.
- Esta versión sólo admite OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Problemas conocidos y características compatibles

Para obtener una lista completa de características compatibles, las características no compatibles y problemas conocidos, consulte [este documento](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).


## <a name="loading-the-plugin"></a>Cargar el complemento
Se pueden cargar OSMF complementos estática (en tiempo de compilación) o dinámicamente (en tiempo de ejecución). El complemento Smooth Streaming descargar OSMF incluye versiones estáticas y dinámicas.

- Carga estática: para cargar estática, es necesario un archivo de biblioteca estática (SWC). Complementos estáticos se agregarán como una referencia a los proyectos y combinar dentro del archivo de salida final en el tiempo de compilación.

- Carga dinámica: para cargar dinámicamente, se requiere un archivo (SWF) precompilado. Complementos dinámicos están cargados en el tiempo de ejecución y no se incluyen en los resultados del proyecto. (Resultado compilado) Pueden cargar complementos dinámicos mediante los protocolos HTTP y archivo.

Para obtener más información sobre la carga estático y dinámico, consulte la [página de complemento OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)de oficial.

###<a name="ss-for-osmf-static-loading"></a>SS para OSMF la carga estática
El fragmento de código siguiente muestra cómo cargar el complemento SS para OSMF estática y reproducir un vídeo básico mediante OSMF MediaFactory clase. Antes de incluir el SS para código OSMF, asegúrese de que la referencia del proyecto incluye el complemento estático "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS para OSMF de carga dinámica

El fragmento de código siguiente muestra cómo cargar el complemento de SS para OSMF dinámicamente y reproducir una basic vídeo mediante la clase OSMF MediaFactory. Antes de incluir el SS para código OSMF, copie el complemento dinámico "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" a la carpeta de proyecto si desea cargar mediante el protocolo de archivo o copiar en un servidor web para la carga HTTP. No es necesario para incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" en las referencias de proyecto.

 
{paquete
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Reproducción de medios de señal con el complemento de SS ODMF dinámica

Smooth Streaming para OSMF dinámico complemento es compatible con la [Reproducción de medios de señal (SMP)](http://osmf.org/strobe_mediaplayback.html). Puede usar la SS para OSMF complemento agregar Smooth Streaming reproducción de contenido a SMP. Para ello, copie "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" en un servidor web para la carga HTTP con los pasos siguientes:

1.  Busque la [página de configuración de señal de la reproducción multimedia](http://osmf.org/dev/2.0gm/setup.html). 
2.  Configure los un origen Smooth Streaming (por ejemplo, http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  Realice los cambios que desee y haga clic en vista previa y la actualización.
 
    **Nota** El servidor web contenido necesita un crossdomain.xml válido. 
4.  Copie y pegue el código en una página HTML utilizando el editor de texto, como en el ejemplo siguiente:



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. Agregar complemento de OSMF de transmisión por secuencias suave al código para insertar y guardar.

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  Guardar la página HTML y publicar en un servidor web. Vaya a la página web publicada mediante su favorito Flash&reg; Reproductor habilitado para el Explorador de Internet (Internet Explorer, Chrome, Firefox, etc.).
7.  Disfrutar de Smooth Streaming contenido dentro de Adobe&reg; Flash&reg; Reproductor.

Para obtener más información sobre el desarrollo de OSMF general, consulte la [página de desarrollo de OSMF](http://osmf.org/resources.html)de oficial.

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Vea también

[Microsoft adaptación transmisión complemento para actualizar OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
