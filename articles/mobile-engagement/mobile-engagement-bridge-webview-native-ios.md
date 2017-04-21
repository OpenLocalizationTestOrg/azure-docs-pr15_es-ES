<properties 
    pageTitle="Puente iOS vista Web con iOS nativo de contratación Mobile SDK" 
    description="Describe cómo crear un puente entre la vista Web ejecuten Javascript y nativo compromiso móvil iOS SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-ios" 
    ms.devlang="objective-c" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Puente iOS vista Web con iOS nativo de contratación Mobile SDK

> [AZURE.SELECTOR]
- [Puente de Android](mobile-engagement-bridge-webview-native-android.md)
- [Puente de iOS](mobile-engagement-bridge-webview-native-ios.md)

Algunas aplicaciones móviles están diseñados como una aplicación híbrida donde la aplicación se desarrolla mediante el desarrollo de iOS nativa objetivo C, pero algunas o todas las pantallas incluso se representan dentro de una vista Web de iOS. Aún puede consumir compromiso móvil iOS SDK dentro de dichas aplicaciones y este tutorial describe cómo hacerlo. 

Existen dos métodos para conseguirlo aunque ambas son sin documentar:

- En primer lugar una se describe en este [vínculo](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) que implica registrar un `UIWebViewDelegate` en la vista web y capturas-y-inmediatamente-cancelar un cambio de ubicación hecho en Javascript. 
- Segundo uno está basado en esta [sesión de 2013 WWDC](https://developer.apple.com/videos/play/wwdc2013/615), un método que es más limpio que la primera y que seguiremos para esta guía. Tenga en cuenta que este método solo funciona en iOS7 o superior. 

Siga los pasos siguientes para el iOS puente de ejemplo:

1. En primer lugar, debe asegurarse de que han pasado nuestro [tutorial de introducción](mobile-engagement-ios-get-started.md) para integrar iOS compromiso Mobile SDK en su aplicación híbrido. Si lo desea, también puede habilitar prueba siguiente registro para que puedan ver los métodos SDK mientras se activan desde la vista Web. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Ahora, asegúrese de que su aplicación híbrida tiene una pantalla con una vista Web en él. Puede agregarlo a la `Main.storyboard` de la aplicación. 

3. Asociar esta vista Web con su **ViewController** haciendo clic y arrastrando la vista Web de la escena de controlador de vista a la `ViewController.h` editar pantalla, puesta justo debajo del `@interface` línea. 

4. Una vez que se hace esto, un cuadro de diálogo aparecerá solicitan un nombre. Proporcione el nombre como **vista Web**. Su `ViewController.h` archivo debe tener un aspecto parecido al siguiente:

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Actualizaremos la `ViewController.m` de archivo más adelante, pero primero se creará el archivo de puente que crea un contenedor sobre algunos iOS Mobile compromiso utilizados con frecuencia métodos SDK. Crear un nuevo archivo de encabezado denominado **EngagementJsExports.h** que usa el `JSExport` mecanismo descrito en el mencionado [sesión](https://developer.apple.com/videos/play/wwdc2013/615) para exponer los métodos de iOS nativa. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. A continuación se crea la segunda parte del archivo puente. Crear un archivo llamado **EngagementJsExports.m** que contendrá la implementación crear los contenedores reales al llamar a los métodos de contratación móvil iOS SDK. Observe también que nos vamos a analizar la `extras` pasen desde la vista Web de javascript y los que se va a colocar una `NSMutableDictionary` objeto que se pasarán con el método de contratación SDK llama.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. Ahora se vuelve a la **ViewController.m** y lo actualiza con el siguiente código: 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Tenga en cuenta los siguientes puntos acerca del archivo **ViewController.m** :

    - En la `loadWebView` método, vamos a cargar un archivo HTML local denominado **LocalPage.html** cuyo código revisará siguiente. 
    - En la `webViewDidFinishLoad` método, nos estamos captura la `JsContext` y asociar a nuestra clase contenedora. Esto le permite llamar a nuestro contenedor métodos SDK con el controlador de **EngagementJs** desde la vista Web. 

7. Crear un archivo llamado **LocalPage.html** con el siguiente código:

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
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Tenga en cuenta los siguientes puntos sobre el archivo HTML anterior:

    -   Contiene un conjunto de cuadros de entrada donde puede proporcionar los datos que se usará como nombres para el evento, tarea, Error, AppInfo. Al hacer clic en el botón junto a él, se realiza una llamada a Javascript que finalmente llama a los métodos desde el archivo puente para pasar esta llamada a Mobile compromiso iOS SDK. 
    -   Nos estamos etiquetado en algunos estático información adicional a los eventos, tareas e incluso errores para demostrar cómo puede hacerlo. Esta información adicional se envía como un JSON de cadena que, si busca en el `EngagementJsExports.m` de archivo, que se analiza y se pasa con enviar eventos, tareas, errores. 
    -   Un trabajo de contratación Mobile comienza con el nombre que especifique en el cuadro de entrada, ejecute durante 10 segundos y cerrar. 
    -   Se pasa una etiqueta o compromiso de Mobile appinfo con 'Nombre_cliente' como clave estática y el valor que ha escrito en la entrada como el valor de la etiqueta. 
 
9. Ejecute la aplicación y verá lo siguiente. Ahora proporcionan algún nombre de un evento de prueba como la siguiente y haga clic en **Enviar** a su lado. 

    ![][1]

10. Ahora cuando vaya a la pestaña **Monitor** de la aplicación y busque en **eventos -> detalles**, verá este evento mostrarse junto con la información de aplicación estática que le estamos enviando. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
