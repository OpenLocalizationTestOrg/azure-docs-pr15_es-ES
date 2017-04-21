<properties 
    pageTitle="Puente de vista Web Android con nativa Mobile compromiso Android SDK" 
    description="Describe cómo crear un puente entre la vista Web ejecuten Javascript y el compromiso de Mobile nativa Android SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Puente de vista Web Android con nativa Mobile compromiso Android SDK

> [AZURE.SELECTOR]
- [Puente de Android](mobile-engagement-bridge-webview-native-android.md)
- [Puente de iOS](mobile-engagement-bridge-webview-native-ios.md)

Algunas aplicaciones móviles están diseñados como una aplicación híbrida donde la aplicación se desarrolla mediante programación Android nativo pero algunos o incluso todas las pantallas se representan dentro de una vista Web Android. Aún puede consumir Mobile compromiso Android SDK dentro de dichas aplicaciones y este tutorial describe cómo hacerlo. El código de ejemplo siguiente se basa en la documentación Android [aquí](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Describe cómo se podría utilizar este enfoque documentado para implementar el mismo para métodos de Mobile compromiso Android SDK usados para que una vista Web desde una aplicación híbrida también puede iniciar las solicitudes de eventos de control, tareas, errores, información de la aplicación mientras tuberías ellos a través de nuestro SDK Android. 

1. En primer lugar, debe asegurarse de que han pasado nuestro [tutorial de introducción](mobile-engagement-android-get-started.md) para integrar el SDK Android compromiso de Mobile en su aplicación híbrido. Una vez que lo haga, su `OnCreate` método tendrá un aspecto parecido al siguiente.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Ahora, asegúrese de que su aplicación híbrida tiene una pantalla con una vista Web en él. El código para el mismo será similar a la siguiente donde vamos a cargar una HTML local archivo **Sample.html** en la vista Web en el `onCreate` método de la pantalla. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. Ahora cree un archivo de puente denominado **WebAppInterface** que crea un contenedor sobre algunos métodos Mobile compromiso Android SDK usados con la `@JavascriptInterface` procedimiento descrito en la [documentación de Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Una vez que hemos creado el archivo de puente anterior, es necesario para asegurarse de que está asociada con la vista Web. Para que ello, debe modificar su `SetWebview` método de modo que TI tiene un aspecto similar al siguiente:

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. En el fragmento de código anterior, se denomina `addJavascriptInterface` para asociar la clase puente a nuestra vista Web y también crea un controlador de **EngagementJs** para llamar a los métodos desde el archivo de puente. 

6. Ahora cree el siguiente archivo denominado **Sample.html** en el proyecto en una carpeta denominada **activos** que se cargan en la vista Web y donde se llamará a los métodos desde el archivo de puente.

        <!doctype html>
        <html>
            <head>
                <style type='text/css'>
                    html { font-family:Helvetica; color:#222; }
                    h1 { color:steelblue; font-size:22px; margin-top:16px; }
                    h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
                </style>
        
                <script type="text/javascript">
        
                    window.onerror = function(err)
                    {
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Tenga en cuenta los siguientes puntos sobre el archivo HTML anterior:

    -   Contiene un conjunto de cuadros de entrada donde puede proporcionar los datos que se usará como nombres para el evento, tarea, Error, AppInfo. Al hacer clic en el botón junto a él, se realiza una llamada a Javascript que finalmente llama a los métodos desde el archivo puente para pasar esta llamada en el SDK Android compromiso de móvil. 
    -   Nos estamos etiquetado en algunos estático información adicional a los eventos, tareas e incluso errores para demostrar cómo puede hacerlo. Esta información adicional se envía como un JSON de cadena que, si busca en el `WebAppInterface` de archivo, que se analiza y se coloca en un dispositivo Android `Bundle` y se pasa junto con el envío de eventos, tareas, errores. 
    -   Un trabajo de contratación Mobile comienza con el nombre que especifique en el cuadro de entrada, ejecute durante 10 segundos y cerrar. 
    -   Se pasa una etiqueta o compromiso de Mobile appinfo con 'Nombre_cliente' como clave estática y el valor que ha escrito en la entrada como el valor de la etiqueta. 
 
9. Ejecute la aplicación y verá lo siguiente. Ahora proporcionan algún nombre de un evento de prueba como la siguiente y haga clic en **Enviar** debajo de ella. 

    ![][1]

10. Ahora cuando vaya a la pestaña **Monitor** de la aplicación y busque en **eventos -> detalles**, verá este evento mostrarse junto con la información de aplicación estática que le estamos enviando. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
