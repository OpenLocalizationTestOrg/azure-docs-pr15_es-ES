<properties
    pageTitle="Crear una aplicación de chat de Node.js con Socket.IO en el servicio de aplicación de Azure"
    description="Tutorial que muestra cómo utilizar socket.io en una aplicación web de node.js hospedada en Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Crear una aplicación de chat de Node.js con Socket.IO en el servicio de aplicación de Azure

Socket.IO proporciona comunicación en tiempo real entre el servidor de node.js y los clientes con WebSockets. También es compatible con reserva a otros transportes (por ejemplo, el sondeo long) que funcionan con exploradores más antiguos. Este tutorial le guiaremos a través de una aplicación de chat Socket.IO según como una aplicación web de Azure de hospedaje y mostrar cómo ajustar el tamaño de la aplicación con [Azure Redis caché]. Para obtener más información sobre Socket.IO, consulte <http://socket.io/>.

> [AZURE.NOTE] Los procedimientos descritos en esta tarea se aplican a la [Aplicación de servicio Web Apps]; para servicios de nube, consulte [crear una aplicación de Chat de Node.js con Socket.IO en un servicio de nube de Azure].

## <a name="download-the-chat-example"></a>Descargar el ejemplo de chat

Para este proyecto, usaremos el ejemplo de chat del [repositorio de Socket.IO GitHub]. Realice los pasos siguientes para descargar el ejemplo y agregar al proyecto que creó anteriormente.

1.  Descargar un [ZIP o GZ archivados lanzamiento] del proyecto Socket.IO (versión 1.3.5 se utilizó para este documento)

1.  Extraer el archivo y copie la **ejemplos\\chat** directorio a una nueva ubicación. Por ejemplo, ** \\nodo\\chat**.

## <a name="modify-appjs-and-install-modules"></a>Modificar app.js e instalar los módulos

1.  Cambie el nombre del archivo **index.js** **app.js**. Esto permite Azure detectar si se trata de una aplicación de Node.js.

1.  Abra el archivo **app.js** en un editor de texto. Cambiar la línea que contiene `var io = require('../..')(server);` tal como se muestra a continuación:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Abra el archivo **package.json** y agregue una referencia a socket.io en `dependencies`, tal como se muestra a continuación:

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. En la línea de comandos, cambie a la ** \\nodo\\chat** npm directorio y usar para instalar los módulos necesarios para esta aplicación:

        npm install

    Se instalará los módulos en una subcarpeta denominada **node_modules**.

## <a name="create-an-azure-web-app"></a>Crear una aplicación Web de Azure

Siga estos pasos para crear una aplicación web de Azure, habilitar Git publicación y, a continuación, habilitar la compatibilidad de WebSocket para la aplicación web.

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Prueba gratuita de Azure</a>.

1. Instalar la interfaz de línea de comandos de Azure (Azure CLI) y conectarse a su suscripción de Azure. Vea [instalar y configurar la CLI Azure](../xplat-cli-install.md).

1. Si esta es la primera vez configurar un repositorio de Azure, debe crear las credenciales de inicio de sesión. Desde la CLI Azure, escriba el siguiente comando:

        azure site deployment user set [username] [password]

1. Cambiar a la ** \\node\chat** directorio y use el comando siguiente para crear un nuevo Azure web app y un repositorio Git local. Este comando también crea un Git remoto con nombre 'azure'.

        azure site create mysitename --git

    'Mysitename' debe reemplazarse con un nombre único para la aplicación web.

1. Confirmar los archivos existentes en el repositorio local mediante los comandos siguientes:

        git add .
        git commit -m "Initial commit"

1. Insertar los archivos en el repositorio de aplicaciones Web de Azure con el siguiente comando:

        git push azure master

    Cuando se le solicite, escriba sus credenciales en el paso 2. Recibirá mensajes de estado módulos se importan en el servidor. Una vez finalizado este proceso, la aplicación se alojarán en la aplicación web de Azure.

    > [AZURE.NOTE] Durante la instalación del módulo, puede observar errores que '... el proyecto importado no se encontró '. Estos pueden pasar por alto.

1. Socket.IO utiliza WebSockets, que no están habilitadas de forma predeterminada en Azure. Para habilitar sockets web, utilice el siguiente comando:

        azure site set -w

    Si se le solicita, escriba el nombre de la aplicación web.

    >[AZURE.NOTE]
    >'Sitio azure set -w' comando usará sólo funcionan con la versión 0.7.4 o superior de la interfaz de línea de comandos de Azure. También puede habilitar la compatibilidad de WebSocket con el [Portal de Azure](https://portal.azure.com).
    >
    >Para habilitar la WebSockets con el Portal de Azure, haga clic en la aplicación web desde el módulo de aplicaciones Web, haga clic en **configuración de todos los** > **configuración de la aplicación**. En la **Web Sockets**, haga clic **en**. A continuación, haga clic en **Guardar**.

1. Para ver la aplicación web de Azure, use el comando siguiente para iniciar el explorador web y vaya a la aplicación web hospedado:

        azure site browse

La aplicación ahora se ejecuta en Azure y puede transmitir mensajes de chat entre diferentes clientes con Socket.IO.

## <a name="scale-out"></a>Escalar

Aplicaciones de Socket.IO pueden escalar mediante un __adaptador__ para distribuir mensajes y eventos entre varias instancias de aplicación. Si bien hay varios adaptadores, el adaptador [redis socket.io] puede utilizarse fácilmente con la característica de caché de Azure Redis.

> [AZURE.NOTE] Un requisito adicional para escalar una solución Socket.IO es compatible con las sesiones. Sesiones permanentes están habilitadas de forma predeterminada para las aplicaciones Web de Azure mediante solicitar enrutamiento Azure. Para obtener más información, vea [Instancia afinidad en los sitios Web de Azure].

### <a name="create-a-redis-cache"></a>Crear una caché Redis

Realice los pasos de [crear una caché en Azure Redis caché] para crear una nueva memoria caché.

> [AZURE.NOTE] Guarde la __clave principal__ y el __nombre de Host__ de la memoria caché, estos se necesarios en los siguientes pasos.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Agregar la redis y módulos redis socket.io

1. Desde una línea de comandos, cambie a la __ \\nodo\\chat__ directorio y use el comando siguiente.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] Las versiones especificadas en este comando son las versiones utilizadas para realizar pruebas de este artículo.

1. Modificar el archivo __app.js__ para agregar las siguientes líneas inmediatamente después de`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Reemplazar __redishostname__ y __rediskey__ con el nombre de host y la clave de la memoria caché Redis.

    Se crea una publicación y suscribirse a cliente en la caché Redis creada previamente. Los clientes se utilizan con el adaptador para configurar Socket.IO para utilizar la caché Redis para pasar mensajes y eventos entre instancias de la aplicación

    > [AZURE.NOTE] Mientras el adaptador __redis socket.io__ puede comunicarse directamente a Redis, la versión actual no admite la autenticación requerida por caché Redis Azure. Así se crea la conexión inicial uso del módulo __redis__ , a continuación, el cliente se pasa al adaptador __redis socket.io__ .
    >
    > Mientras Azure Redis caché es compatible con conexiones seguras utilizando el puerto 6380, los módulos utilizados en este ejemplo no admiten conexiones seguras a partir de 14/7/2014. El código anterior usa el valor predeterminado, el puerto no seguro de 6379.

1. Guardar la modificación __app.js__

### <a name="commit-changes-and-redeploy"></a>Confirmar los cambios y volver a

Desde la línea de comandos en el __ \\nodo\\chat__ directorio, use los comandos siguientes para confirmar los cambios y volver a la aplicación.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Una vez que los cambios se han insertado en el servidor, puede escalar el sitio en varias instancias mediante el comando siguiente.

    azure site scale instances --instances #

Donde __#__ es el número de instancias de crear.

Puede conectarse a su aplicación web desde varios exploradores o equipos para comprobar que los mensajes se envían correctamente a todos los clientes.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="connection-limits"></a>Límites de conexión

Aplicaciones Web de Azure está disponible en varias SKU, que determinan los recursos disponibles para su sitio. Esto incluye el número de conexiones de WebSocket permitidos. Para obtener más información, consulte la [página de precios de aplicaciones Web].

### <a name="messages-arent-being-sent-using-websockets"></a>No se envían los mensajes con WebSockets

Si los exploradores cliente mantienen retroceder cuánto tiempo sondeo en lugar de usar WebSockets, puede ser debido a uno de estos procedimientos.

* **Pruebe a limitar el transporte solo WebSockets**

    Para Socket.IO usar WebSockets como el transporte de mensajería, el servidor y el cliente deben admitir WebSockets. Si uno de ellos no, Socket.IO se negocie otro transporte, como sondeo larga. La lista predeterminada de transportes usado por Socket.IO es ` websocket, htmlfile, xhr-polling, jsonp-polling`. Puede exigir que use únicamente WebSockets agregando el código siguiente después de la línea que contiene el archivo **app.js** , `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Tenga en cuenta que los exploradores más antiguos que no admiten WebSockets no podrá conectarse al sitio mientras está activo, el código anterior que restringe la comunicación a WebSockets solo.

* **Usar SSL**

    WebSockets se basa en algunos encabezados HTTP menor usados, como el encabezado **de actualización** . Algunos dispositivos de red intermedios, como servidores proxy de web, pueden quitar estos encabezados. Para evitar este problema, puede establecer la conexión de WebSocket sobre SSL.

    Una forma sencilla de hacerlo es configurar Socket.IO a `match origin protocol`. Esto indica que Socket.IO a WebSockets de comunicación segura idéntica a la solicitud HTTP/HTTPS original de la página web. Si utiliza una dirección URL HTTPS el explorador para visitar el sitio Web, se protegerse posteriores comunicaciones WebSocket a través de Socket.IO sobre SSL.

    Para modificar este ejemplo para habilitar esta configuración, agregue el código siguiente al archivo **app.js** después de la línea que contiene `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Comprobar la configuración de web.config**

    Aplicaciones web de Azure que alojan aplicaciones Node.js utilizan el archivo **web.config** para enrutar las solicitudes entrantes a la aplicación Node.js. Para WebSockets funcione correctamente con aplicaciones de Node.js, **web.config** debe contener la entrada siguiente.

        <webSocket enabled="false"/>

    Esto deshabilita el módulo WebSockets de IIS, que incluye su propia implementación de conflictos con Node.js módulos específicos de WebSocket como Socket.IO y WebSockets. Si esta línea no está presente o se establece en `true`, esto puede ser el motivo por el que el transporte WebSocket no funciona para su aplicación.

    Normalmente, Node.js aplicaciones no incluye un archivo **web.config** , para que sitios Web de Azure generará automáticamente uno para aplicaciones de Node.js cuando se implementan. Dado que este archivo se genera automáticamente en el servidor, debe utilizar el FTP o FTPS dirección URL para el sitio Web para ver este archivo. Puede encontrar el FTP y FTPS URL para el sitio en el portal de clásico seleccionando la aplicación web y, a continuación, en el vínculo del **escritorio** . Las direcciones URL se muestran en la sección **vistazo rápido** .

    > [AZURE.NOTE] El archivo **web.config** sólo se genera por los sitios Web de Azure si su aplicación no proporciona uno. Si proporciona un archivo **web.config** en la raíz de su proyecto de la aplicación, se utilizará por aplicaciones Web de Azure.

    Si la entrada no está presente o se establece en un valor de `true`, debe crear un **web.config** en la raíz de la aplicación Node.js y especifique un valor de `false`.  Para referencia, el a continuación, encontrará una predeterminado **web.config** para una aplicación que usa **app.js** como punto de entrada.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Si su aplicación utiliza un punto de entrada que no sea **app.js**, debe reemplazar todas las apariciones de **app.js** por el punto de entrada correcta. Por ejemplo, reemplazando **app.js** con **server.js**.

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones], donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo crear una aplicación de chat alojada en una aplicación web de Azure. También puede hospedar esta aplicación como un servicio de nube de Azure. Para conocer los pasos para llevar a cabo esto, vea [crear una aplicación de Chat de Node.js con Socket.IO en un servicio de nube de Azure].

Para obtener más información, vea también el [Centro para desarrolladores de Node.js].

## <a name="whats-changed"></a>¿Qué ha cambiado

* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes].

<!-- URL List -->

[Caché de Azure Redis]: /documentation/services/redis-cache/
[Aplicaciones de servicio de aplicación Web]: http://go.microsoft.com/fwlink/?LinkId=529714
[Página de precios de aplicaciones Web]: http://go.microsoft.com/fwlink/?LinkId=511643
[Crear una aplicación de Chat de Node.js con Socket.IO en un servicio de nube de Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Azure de aplicación de servicio y su impacto en los servicios de Azure existentes]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro para desarrolladores de Node.js]: /develop/nodejs/
[Pruebe la aplicación de servicio]: http://go.microsoft.com/fwlink/?LinkId=523751
[Instancia afinidad en sitios Web de Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Crear una caché en caché Redis de Azure]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[redis Socket.IO]: https://github.com/socketio/socket.io-redis
[Repositorio de Socket.IO GitHub]: https://github.com/socketio/socket.io
[ZIP o GZ archivados lanzamiento]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
