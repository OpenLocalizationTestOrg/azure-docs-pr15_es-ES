<properties 
    pageTitle="Inserción de anuncios del cliente | Microsoft Azure" 
    description="Este tema muestra cómo insertar anuncios del cliente." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>Inserción de anuncios del cliente

Este tema contiene información sobre cómo insertar distintos tipos de anuncios del cliente.

Para obtener información sobre la compatibilidad de ad y subtítulos cerrada en Live secuencias de vídeos, vea [admite subtítulos y estándares de inserción de Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Reproductor Azure actualmente no admite anuncios.

##<a id="insert_ads_into_media"></a>Inserción de anuncios en el medio

Azure Media Services proporciona soporte técnico para la inserción de ad a través de la plataforma de Windows Media: marcos del Reproductor. Marcos de Reproductor con el soporte técnico de ad están disponibles para los dispositivos de Windows 8, Silverlight, Windows Phone 8 y iOS. Cada marco Reproductor contiene código de ejemplo que muestra cómo implementar una aplicación de reproducción. Existen tres tipos diferentes de anuncios que se puede insertar en la lista de medios:.

- **Lineal** : anuncios de marco completo que pausar el vídeo principal.
- **Nonlinear** – anuncios de superposición que se muestran cuando se está reproduciendo el vídeo principal, normalmente un logotipo u otra imagen estática se coloca en el Reproductor.
- **Companion** – anuncios que se muestran fuera del Reproductor.

Anuncios se pueden colocar en cualquier punto de la escala de tiempo del vídeo principal. Debe indicar al Reproductor cuándo reproducir ad y los anuncios para reproducir. Esto se hace con un conjunto de archivos basado en XML estándar: plantilla de servicio de Ad de vídeo (VAST), lista de reproducción múltiples de Ad (VMAP) de Digital vídeo, multimedia abstractos secuencia de plantilla (más) y Ad interfaz definición (VPAID) el Reproductor de vídeo Digital. Archivos grandes especifican los anuncios para mostrar. Archivos VMAP especifican cuándo reproducir varios anuncios y contienen gran XML. Archivos más constituyen otra forma a los anuncios de secuencia que también puede contener gran XML. Archivos VPAID definen una interfaz entre el Reproductor de vídeo y el anuncio o servidor ad.

Cada marco Reproductor funciona de forma diferente y cada una se explicará en su propio tema. En este tema se describe los mecanismos básicos que se usa para insertar anuncios. Aplicaciones de Reproductor de vídeo solicitan anuncios desde un servidor de ad. El servidor ad puede responder de varias maneras:

- Devolver un gran archivo
- Devolver un archivo VMAP (con VAST incrustado)
- Devolver un archivo de máscara (con VAST incrustado)
- Devolver un archivo de gran con anuncios VPAID

 
###<a name="using-a-video-ad-service-template-vast-file"></a>Con un archivo de plantilla (VAST) de servicio de Ad de vídeo

Un archivo de gran especifica qué anuncio o anuncios para mostrar. El siguiente código XML es un ejemplo de un archivo de gran para un anuncio lineal:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
Se describe el anuncio lineal mediante la **<Linear>** elemento. Especifica la duración del anuncio, seguimiento de eventos, haga clic en, haga clic en seguimiento y a continuación, un número de **<MediaFile>** elementos. Seguimiento de eventos se especifican en el **<TrackingEvents>** elemento y permitir que un servidor de ad realizar un seguimiento de los distintos eventos que se producen mientras visualiza el anuncio. En este caso, el inicio, punto medio, completado, es posible expandir y se realiza un seguimiento de eventos. El evento de inicio se produce cuando se muestra el anuncio. El evento de punto medio se produce cuando al menos el 50% de la escala de tiempo de ad ha sido visto. El evento completado se produce cuando el anuncio se ha ejecutado al final. El evento Expand se produce cuando el usuario expande el Reproductor de vídeo a pantalla completa. Clickthroughs se especifican con un **<ClickThrough>** elemento dentro de un **<VideoClicks>** elemento y especifica un URI a un recurso que se muestra cuando el usuario hace clic en el anuncio. ClickTracking especificado en un **<ClickTracking>** elemento, también dentro del **<VideoClicks>** elemento y especifica un recurso de seguimiento para el Reproductor de solicitud cuando el usuario hace clic en el anuncio. La **<MediaFile>** elementos especifican la información sobre una codificación específica de un anuncio. Cuando hay más de un **<MediaFile>** elemento, el Reproductor de vídeo puede elegir la codificación mejor para la plataforma. 

Se pueden mostrar anuncios lineales en un orden especificado. Para ello, agregue adicionales <Ad> elementos a la VAST de archivos y especificar el orden mediante el atributo de secuencia. En el ejemplo siguiente se muestra esto:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Anuncios Nonlinear se especifican en un <Creative> también el elemento. El ejemplo siguiente muestra una <Creative> elemento que describe un anuncio no lineal.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
La **<NonLinearAds>** elemento puede contener uno o varios **<NonLinear>** elementos, cada una de las cuales puede describir un anuncio no lineal. La **<NonLinear>** elemento especifica el recurso para el anuncio nonlinear. El recurso puede ser una **<StaticResouce>**, **<IFrameResource>**, o un **<HTMLResouce>**.**<StaticResource>** Describe un recurso no HTML y define un atributo creativeType que especifica cómo se muestra el recurso:

Imagen/gif, jpeg o imagen, imagen o png: el recurso se muestra en una etiqueta HTML **<img>** etiqueta.

Aplicación/x-javascript: el recurso se muestra en una etiqueta HTML <**script**>.

Aplicación/x-shockwave-flash: el recurso se muestra en un reproductor Flash.

**<IFrameResource>**Describe un recurso HTML que se pueden mostrar en un IFrame. **<HTMLResource>**Describe un fragmento de código HTML que se puede insertar en una página web. **<TrackingEvents>**especificar los eventos de seguimiento y la URI para solicitar cuando se produce el evento. En este ejemplo se controlan los eventos acceptInvitation y contraer. Para obtener más información sobre la **<NonLinearAds>** elemento y sus elementos secundarios, vea IAB.NET/VAST. Tenga en cuenta que el **<TrackingEvents>** elemento se encuentra en la** <NonLinearAds> ** elemento en lugar de **<NonLinear>** elemento.

Anuncios Companion se definen dentro de un <CompanionAds> elemento. La <CompanionAds> elemento puede contener uno o varios <Companion> elementos. Cada <Companion> elemento describe un anuncio companion y puede contener un <StaticResource>, <IFrameResource>, o <HTMLResource> que se especifican en la misma forma que en un anuncio nonlinear. Un archivo gran puede contener varios anuncios companion y la aplicación de reproducción puede elegir el anuncio más apropiado para mostrar. Para obtener más información sobre VAST, consulte [gran 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Usar un vídeo Digital varios archivos de la lista de reproducción (VMAP) de Ad

Un archivo VMAP permite especificar cuando se producen saltos de ad, ¿cuánto tiempo dura de cada salto, se pueden mostrar anuncios cuántos dentro de un salto de y ¿qué tipos de anuncios pueden estar muestra durante un salto. Las siguientes acciones en un archivo VMAP de ejemplo que define un salto de ad solo:
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Un archivo VMAP empieza con un <VMAP> elemento que contiene uno o varios <AdBreak> elementos, cada definición de un salto de ad. Cada salto de ad especifica un tipo de salto, el identificador de salto y el desplazamiento de tiempo. El atributo breakType especifica el tipo de anuncio que se pueden reproducir durante el salto: lineal, nonlinear, o mostrar. Mostrar mapa de anuncios para los anuncios de gran companion. Puede especificar el tipo de ad más de una en una lista separada por comas (sin espacios). El breakID es un identificador opcional de ad. El timeOffset especifica cuándo debe mostrarse el anuncio. Se pueden especificar en una de las siguientes maneras:

1. Tiempo, en formato de hh o hh donde .mmm es milisegundos. El valor de este atributo especifica la hora de comienzo de la escala de tiempo de vídeo hasta el principio del salto de ad.
1. Porcentaje: en formato % n, donde n es el porcentaje de la escala de tiempo de vídeo para reproducir antes de la reproducción de ad
1. Inicio o finalización: Especifica que se debe mostrar un anuncio antes o después de que se ha mostrado el vídeo
1. Posición: especifica el orden de los saltos de ad cuando los intervalos de los saltos de ad es desconocido, como en transmisión directo. El orden de cada salto de ad especificado en el formato #n donde n es un entero de 1 o mayor. 1 indica que se debe reproducir el anuncio a la primera oportunidad, 2 significa ad se debe reproducir en la segunda oportunidad y así sucesivamente.

En el elemento <**AdBreak**> puede ser un elemento de <**AdSource**>. El elemento <**AdSource**> contiene los siguientes atributos:

1. Id: especifica un identificador para el origen de ad
1. allowMultipleAds – un valor Boolean que especifica si se pueden mostrar varios anuncios durante el salto de ad
1. followRedirects: valor booleano opcional que especifica si el Reproductor de vídeo debe respetar redirige dentro de una respuesta de ad

El elemento <**AdSource**> proporciona al Reproductor una respuesta de un anuncio en línea o una referencia a una respuesta de ad. Puede contener uno de los siguientes elementos:

- <VASTAdData>indica que una respuesta de ad gran incrustada en el archivo VMAP
- <AdTagURI>URI que hace referencia a una respuesta de ad desde otro sistema
- <CustomAdData>-un arbitrario de cadena que representa una respuesta no gran

En este ejemplo se especifica una respuesta de un anuncio en línea con un <VASTAdData> elemento que contiene una respuesta de gran ad. Para obtener más información acerca de los demás elementos, consulte [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

El elemento <**AdBreak**> también puede contener un elemento de <**TrackingEvents**>. El elemento <**TrackingEvents**> le permite realizar un seguimiento de comienzo o al final de un salto de ad o si se produjo un error durante el salto de ad. El elemento <**TrackingEvents**> contiene uno o más <**seguimiento**> elementos, cada una de las cuales especifica un evento de seguimiento y un URI de seguimiento. Los eventos de seguimiento posibles son:

1. principio de un salto de ad realiza un seguimiento de breakStart:
1. breakEnd: realizar un seguimiento de la finalización de un salto de ad
1. Error: realiza un seguimiento de un error que ocurrieron durante el salto de ad

En el ejemplo siguiente se muestra un archivo VMAP que especifica el seguimiento de eventos

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Para obtener más información sobre el elemento <**TrackingEvents**> y sus elementos secundarios, consulte http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usar un resumen multimedia de la secuencia de archivo de plantilla (más)

Un archivo de máscara le permite especificar desencadenadores que definen cuando se muestra un anuncio. El siguiente es un archivo de más de ejemplo que contiene desencadenadores para un anuncio de rollo pre, un anuncio rollo intermedio y un anuncio posteriores a la implementación.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Un archivo de máscara empieza con un **<MAST>** elemento que contiene una **<triggers>** elemento. La <triggers> elemento contiene uno o varios **<trigger>** elementos que definen cuándo se debe reproducir un anuncio. 

La **<trigger>** elemento contiene un **<startConditions>** elemento que especifique cuándo debe comenzar un anuncio a reproducirse. La **<startConditions>** elemento contiene uno o varios <condition> elementos. Cuando cada <condition> se evalúa como verdadero un desencadenador se inicia o revoca dependiendo de si la <condition> está dentro de un **< startConditions**> o **<endConditions>** elemento respectivamente. Cuando varios <condition> elementos están presentes, se traten como un OR implícito, cualquier condición evaluar True hará que el desencadenador iniciar. <condition>es posible anidar elementos. Cuando secundarios <condition> elementos están predefinidos, se traten como un AND implícito, todas las condiciones se deben evaluar como verdadero iniciar el desencadenador. La <condition> elemento contiene los siguientes atributos que definen la condición: 

1. **tipo** : especifica el tipo de condición, el evento o la propiedad
1. **nombre** : el nombre de la propiedad o el evento que se utilizarán durante la evaluación
1. **valor** : el valor de una propiedad se evaluará en
1. **operador** : la operación que debe usar durante la evaluación: EC (igual), NEQ (no es igual), GTR (mayor), GEQ (mayor o igual que), LT (menor que), LEQ (menor o igual que), MOD (módulo)

**<endConditions>**también contienen <condition> elementos. Cuando una condición se evalúa como verdadero el desencadenador se restablece. La <trigger> elemento también contiene un <sources> elemento que contiene uno o varios <source> elementos. La <source> elementos definen la URI a la respuesta de ad y el tipo de respuesta de ad. En este ejemplo se proporciona un URI para una gran respuesta. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>Uso de la definición de la interfaz de anuncio de Reproductor de vídeo (VPAID)

VPAID es una API para habilitar las unidades de ad ejecutable para comunicarse con un Reproductor de vídeo. Esto permite experiencias de ad altamente interactivo. El usuario puede interactuar con Active Directory y ad puede responder a las acciones realizadas por el Visor. Por ejemplo, un anuncio puede mostrar los botones que permiten al usuario ver más información o una versión más larga de ad. El Reproductor de vídeo debe ser compatible con la API VPAID y ad ejecutable debe implementar la API. Cuando un Reproductor solicita que un anuncio de un servidor de anuncios el servidor puede responder con una respuesta gran que contiene un anuncio VPAID.

Se crea un anuncio ejecutable en el código que se debe ejecutar en un entorno de tiempo de ejecución como Adobe Flash™ o JavaScript que se puede ejecutar en un explorador web. Cuando un servidor ad devuelve una respuesta gran que contiene un anuncio VPAID, el valor de la apiFramework atributo en la <MediaFile> elemento debe ser "VPAID". Este atributo especifica que el contenido ad es un anuncio ejecutable VPAID. El atributo type debe establecerse en el tipo MIME del archivo ejecutable, como "application/x-shockwave-flash" o "application/x-javascript". El siguiente fragmento de código XML se muestra la <MediaFile> elemento de una gran respuesta que contiene un anuncio ejecutable VPAID. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

Un anuncio ejecutable puede inicializarse utilizando la <AdParameters> elemento dentro de la <Linear> o <NonLinear> elementos en una gran respuesta. Para obtener más información sobre la <AdParameters> elemento, consulte [gran 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Para obtener más información acerca de la API VPAID, consulte [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementar un Windows o el Reproductor de Windows Phone 8 con soporte de Ad

La plataforma de Microsoft Media: Reproductor Framework para Windows 8 y Windows Phone 8 contiene un conjunto de aplicaciones de ejemplo que muestran cómo implementar una aplicación de Reproductor de vídeo con el marco de trabajo. Puede descargar el marco del Reproductor y los ejemplos de [marco de Reproductor de Windows 8 y Windows Phone 8](https://playerframework.codeplex.com).

Cuando se abre la solución Microsoft.PlayerFramework.Xaml.Samples verá un número de carpetas en el proyecto. La carpeta de publicidad contiene el código de ejemplo relacionado con la creación de un Reproductor de vídeo con el soporte de ad. Dentro de la publicidad carpeta es un número de archivos XAML/cs cada uno de las cuales se muestra cómo insertar anuncios de forma diferente. En la lista siguiente describe cada uno:

- AdPodPage.xaml muestra cómo mostrar una caja de ad.
- AdSchedulingPage.xaml muestra cómo programar anuncios.
- FreeWheelPage.xaml muestra cómo usar el complemento FreeWheel para programar los anuncios.
- MastPage.xaml muestra cómo programar anuncios con un archivo de máscara.
- ProgrammaticAdPage.xaml muestra cómo establecer mediante programación los anuncios en un vídeo.
- ScheduleClipPage.xaml muestra cómo programar un anuncio sin un archivo de gran.
- VastLinearCompanionPage.xaml muestra cómo insertar un lineal y ad companion.
- VastNonLinearPage.xaml muestra cómo insertar un anuncio no lineal.
- VmapPage.xaml muestra cómo especificar anuncios con un archivo VMAP.

Cada uno de estos ejemplos usa la clase MediaPlayer definida por el marco del Reproductor. La mayoría de las muestras use complementos que agregan la compatibilidad con varios formatos de respuesta de ad. El ejemplo ProgrammaticAdPage mediante programación interactúa con una instancia de MediaPlayer.

###<a name="adpodpage-sample"></a>Ejemplo de AdPodPage

Este ejemplo usa la AdSchedulerPlugin para definir cuándo se muestra un anuncio. En este ejemplo un anuncio rollo intermedio está programado para que se reproduzca después de 5 segundos. La caja de ad (un grupo de anuncios para mostrarlos en orden) especificado en un archivo gran devuelto desde un servidor de ad. La URI del archivo gran especificado en el <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Para obtener más información sobre la AdSchedulerPlugin, vea [publicidad en el marco del Reproductor de Windows 8 y Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

Este ejemplo también utiliza la AdSchedulerPlugin. Programar tres anuncios, un anuncio de preparación, un anuncio rollo intermedio y un anuncio posteriores a la implementación. La URI a la VAST para cada anuncio especificado en un <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

Este ejemplo usa la FreeWheelPlugin que especifica un atributo de origen que especifica un URI que señala a un archivo SmartXML que especifica el contenido de ad, así como la información de programación de ad.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

Este ejemplo usa la MastSchedulerPlugin que le permite utilizar un archivo de máscara. El atributo de origen Especifica la ubicación del archivo más.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

En este ejemplo mediante programación interactúa con MediaPlayer. El archivo ProgrammaticAdPage.xaml crea una instancia de MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

El archivo ProgrammaticAdPage.xaml.cs crea una AdHandlerPlugin agrega un TimelineMarker especificar cuando un anuncio se debe mostrar y, a continuación, agrega un controlador para el evento MarkerReached que carga un RemoteAdSource que especifica un URI a un archivo gran y, a continuación, se reproduce el anuncio.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

Este ejemplo usa la AdSchedulerPlugin para programar un anuncio rollo intermedio especificando un archivo .wmv que contiene el anuncio.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

Este ejemplo muestra cómo usar el AdSchedulerPlugin para programar un anuncio lineal rollo intermedio con un anuncio companion. La <RemoteAdSource> elemento especifica la ubicación del archivo gran.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

Este ejemplo usa la AdSchedulerPlugin para programar una lineal y un anuncio no lineal. Se especifica la ubicación del archivo gran con la <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

En este ejemplo usa la VmapSchedulerPlugin para programar anuncios con un archivo VMAP. La URI del archivo VMAP especificado en el atributo de origen de la <VmapSchedulerPlugin> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Implementar un Reproductor de vídeo con el soporte técnico de Ad iOS


La plataforma de Microsoft Media: Reproductor Framework para iOS contiene un conjunto de aplicaciones de ejemplo que muestran cómo implementar una aplicación de Reproductor de vídeo con el marco de trabajo. Puede descargar el marco del Reproductor y los ejemplos de [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). La página github tiene un vínculo a un Wiki que contiene información adicional sobre el marco del Reproductor y una introducción sobre el Reproductor de ejemplo: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Programación de anuncios con VMAP

En el ejemplo siguiente se muestra cómo programar anuncios con un archivo VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Programación de anuncios con VAST

En el ejemplo siguiente se muestra cómo programar un anuncio gran enlace retrasadas.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   En el ejemplo siguiente se muestra cómo programar un anuncio gran enlace anticipado.
Programación de ejemplo: 4 un anticipado enlace ad gran //Download la VAST archivo si (! [ framework.adResolver downloadManifest: & manifiestos withURL: [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[logFrameworkError self];} else {adLinearTime.startTime = 7; adLinearTime.duration = 0;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

En el ejemplo siguiente muestra cómo insertar un anuncio con la edición de cortar aproximada (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

En el ejemplo siguiente se muestra cómo programar una caja de ad.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

En el ejemplo siguiente se muestra cómo programar un anuncio rollo intermedio no rápidas. Un anuncio no rápidas sólo se reproduce una vez independientemente de cualquier búsqueda realiza el Visor.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

En el ejemplo siguiente se muestra cómo programar un anuncio rollo intermedio rápido. Cada vez que se llega al punto especificado en la escala de tiempo de vídeo se mostrará un anuncio rápido.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


En el ejemplo siguiente se muestra cómo programar un anuncio posteriores a la implementación.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

En el ejemplo siguiente se muestra cómo programar un anuncio de preparación.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

En el ejemplo siguiente se muestra cómo programar un anuncio rollo intermedio superposición.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Vea también

[Desarrollar aplicaciones de Reproductor de vídeo](media-services-develop-video-players.md)
