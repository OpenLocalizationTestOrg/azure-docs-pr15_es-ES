<properties 
    pageTitle="Aplicación de Node.js con Socket.io | Microsoft Azure" 
    description="Aprenda a utilizar socket.io en una aplicación de node.js hospedada en Azure." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Crear una aplicación de Chat de Node.js con Socket.IO en un servicio de nube de Azure

Socket.IO proporciona comunicación en tiempo real entre entre el servidor de node.js y los clientes. Este tutorial le guiará a través de un socket de hospedaje. IO según la aplicación de chat en Azure. Para obtener más información sobre Socket.IO, consulte <http://socket.io/>.

Captura de pantalla de la aplicación completada está por debajo de:

![Una ventana del explorador que muestra el servicio hospedado en Azure][completed-app]  

## <a name="prerequisites"></a>Requisitos previos

Garantizar que los siguientes productos y versiones se instalan para completar correctamente el ejemplo en este artículo:

* Instalar [2013 de Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar [Node.js](https://nodejs.org/download/)
* Instalar [Python versión 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Crear un proyecto de servicio de nube

Los siguientes pasos crean el proyecto de servicio de nube que hospedará la aplicación Socket.IO.

1. Desde el **Menú Inicio** o la **Pantalla de inicio**, busque **Windows PowerShell**. Por último, haga clic en **Windows PowerShell** y seleccione **Ejecutar como administrador**.

    ![Icono de PowerShell de Azure][powershell-menu]

2. Cree un directorio denominado **c:\\nodo**. 
 
        PS C:\> md node

3. Cambie los directorios a la **c:\\nodo** directorio
 
        PS C:\> cd node

4. Escriba los siguientes comandos para crear una nueva solución denominada **chatapp** y una función de trabajador denominado **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Verá la respuesta siguiente:

    ![El resultado de la nueva azureservice y azurenodeworkerrolecmdlets agregar](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Descargar el ejemplo de Chat

Para este proyecto, usaremos el ejemplo de chat del [repositorio de Socket.IO GitHub]. Realice los pasos siguientes para descargar el ejemplo y agregar al proyecto que creó anteriormente.

1.  Crear una copia local del repositorio mediante el botón **Duplicar** . También puede usar el botón **ZIP** para descargar el proyecto.

    ![Una ventana del explorador viendo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, con el icono de descarga ZIP resaltado][chat-example-view]

3.  Desplazarse por la estructura de directorio del repositorio local hasta que llegue a la **ejemplos\\chat** directorio. Copiar el contenido de este directorio a la **C:\\nodo\\chatapp\\WorkerRole1** directorio que creó anteriormente.

    ![Explorador, que muestra el contenido de los ejemplos\\extraído desde el archivo de directorio de chat][chat-contents]

    Los elementos resaltados en la captura de pantalla anterior son los archivos que se copian desde el **ejemplos\\chat** directorio

4.  En la **C:\\nodo\\chatapp\\WorkerRole1** directorio, elimine el archivo **server.js** y, a continuación, cambie el nombre del archivo **app.js** **server.js**. Esto elimina el archivo de **server.js** predeterminado creado previamente por el cmdlet de **Agregar AzureNodeWorkerRole** y lo sustituye por el archivo de la aplicación de ejemplo de chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar Server.js e instalar los módulos

Antes de probar la aplicación en el emulador Azure, debemos hacer algunas modificaciones menores. Realice los pasos siguientes en el archivo server.js:

1.  Abra el archivo **server.js** en Visual Studio o cualquier editor de texto.

2.  Busque la sección de **las dependencias de módulo** al principio del server.js y cambie la línea que contiene **sio = require('.. //.. lib//Socket.IO')** a **sio = require('socket.io')** tal como se muestra a continuación:

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Para asegurarse de que la aplicación de escucha en el puerto correcto, abra server.js en el Bloc de notas o su editor favorito y, a continuación, cambie la línea siguiente reemplazando **3000** con **process.env.port** tal como se muestra a continuación:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Después de guardar los cambios a **server.js**, realice los pasos siguientes para instalar módulos necesarios y, a continuación, pruebe la aplicación en el emulador Azure:

1.  Con **PowerShell de Azure**, cambie los directorios a la **C:\\nodo\\chatapp\\WorkerRole1** directorio y use el comando siguiente para instalar los módulos necesarios para esta aplicación:

        PS C:\node\chatapp\WorkerRole1> npm install

    Esto instalará los módulos enumerados en el archivo package.json. Cuando finalice el comando, verá un resultado similar al siguiente:

    ![El resultado de la npm el comando de instalación][The-output-of-the-npm-install-command]

4.  Dado que este ejemplo originalmente era un elemento del repositorio de Socket.IO GitHub y hace referencia directamente a la biblioteca de Socket.IO ruta de acceso relativa, Socket.IO no se hace referencia en el archivo package.json, por lo que debe instalarlo mediante el comando siguiente:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Probar e implementar

1.  Inicie el emulador mediante el comando siguiente:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Abra un explorador y vaya a **http://127.0.0.1**.

3.  Cuando se abre la ventana del explorador, escriba un alias y, a continuación, presione ENTRAR.
    Esto le puede publicar mensajes como un sobrenombre específico. Para probar la funcionalidad de varios usuario, abra ventanas del explorador adicionales con la misma dirección URL y escriba alias diferentes.

    ![Mostrar los mensajes de chat de User1 y User2 dos ventanas del explorador](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Después de probar la aplicación, detenga el emulador mediante el comando siguiente:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Para implementar la aplicación en Azure, use el cmdlet **AzureServiceProject publicar** . Por ejemplo:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] Asegúrese de usar un nombre único, lo contrario, que se producirá un error en el proceso de publicación. Cuando haya finalizado la implementación, el explorador se abra y navegue hasta el servicio implementado.
    > 
    > Si recibe un error que indica que el nombre de suscripción proporcionado no existe en el perfil de publicación importados, debe descargar e importar el perfil de publicación para la suscripción antes de implementar para Azure. Vea la sección **implementar la aplicación en Azure** de [Compile e implemente una aplicación de Node.js a un servicio de nube de Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

    ![Una ventana del explorador que muestra el servicio hospedado en Azure][completed-app]

    > [AZURE.NOTE] Si recibe un error que indica que el nombre de suscripción proporcionado no existe en el perfil de publicación importados, debe descargar e importar el perfil de publicación para la suscripción antes de implementar para Azure. Vea la sección **implementar la aplicación en Azure** de [Compile e implemente una aplicación de Node.js a un servicio de nube de Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

La aplicación ahora se ejecuta en Azure y puede transmitir mensajes de chat entre diferentes clientes con Socket.IO.

> [AZURE.NOTE] Para simplificar, en este ejemplo se limita a charlar entre los usuarios conectados a la misma instancia. Esto significa que si el servicio de nube crea dos instancias de la función de trabajo, los usuarios solo podrá conversar con otros usuarios conectados a la misma instancia del rol de trabajo. Para cambiar la escala de la aplicación para trabajar con varias instancias de la función, podría usar una tecnología como Bus de servicio para compartir el estado de la tienda de Socket.IO en varias instancias. Para obtener ejemplos, vea los ejemplos de uso de colas de Bus de servicio y temas en el [SDK de Azure para Node.js GitHub repositorio](https://github.com/WindowsAzure/azure-sdk-for-node).

##<a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo crear una aplicación de chat básicas alojada en un servicio de nube de Azure. Para obtener información sobre cómo hospedar esta aplicación en un sitio Web de Azure, vea [crear una aplicación de Chat de Node.js con Socket.IO en un sitio Web de Azure][chatwebsite].

Para obtener más información, vea también el [Centro para desarrolladores de Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Repositorio de Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 
