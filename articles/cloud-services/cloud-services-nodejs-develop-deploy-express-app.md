<properties 
    pageTitle="Web App con Express (Node.js) | Microsoft Azure" 
    description="Tutorial que se basa en el tutorial de servicio de nube y a continuación, se muestra cómo usar el módulo de Express." 
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






# <a name="build-a-nodejs-web-application-using-express-on-an-azure-cloud-service"></a>Crear una aplicación web de Node.js con Express en un servicio de nube de Azure

Node.js incluye un conjunto mínimo de funcionalidad en el tiempo de ejecución principal.
Los desarrolladores a menudo utilizar 3ª módulos de terceros para proporcionar funcionalidad adicional al desarrollar una aplicación de Node.js. En este tutorial creará una nueva aplicación mediante el módulo [Express][] , que proporciona un marco de MVC para crear aplicaciones web de Node.js.

Captura de pantalla de la aplicación completada está por debajo de:

![Un explorador web que muestra Bienvenido a Express en Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##<a name="create-a-cloud-service-project"></a>Crear un proyecto de servicio de nube

Realice los pasos siguientes para crear un nuevo proyecto de servicio de nube denominado 'expressapp':

1. Desde el **Menú Inicio** o la **Pantalla de inicio**, busque **Windows PowerShell**. Por último, haga clic en **Windows PowerShell** y seleccione **Ejecutar como administrador**.

    ![Icono de PowerShell de Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

    [AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Cambie los directorios a la **c:\\nodo** directorio y, a continuación, escriba los siguientes comandos para crear una nueva solución denominada **expressapp** y una función de web denominada **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

    > [AZURE.NOTE] De forma predeterminada, **Agregar AzureNodeWebRole** usa una versión anterior de Node.js. La instrucción **AzureServiceProjectRole conjunto** anterior indica que Azure usar v0.10.21 de nodo.  Tenga en cuenta que los parámetros distinguen mayúsculas de minúsculas.  Puede comprobar que la versión correcta de Node.js se ha seleccionado la propiedad **motores** en **WebRole1\package.json**.

##<a name="install-express"></a>Instalación Express

1. Instalar el generador de Express mediante el comando siguiente:

        PS C:\node\expressapp> npm install express-generator -g

    El resultado del comando npm debe ser similar al siguiente resultado. 

    ![Comando express de instalación de Windows PowerShell, que muestra el resultado de la npm.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Cambie los directorios al directorio **WebRole1** y use el comando express para generar una nueva aplicación:

        PS C:\node\expressapp\WebRole1> express

    Se le pedirá que sobrescriba la aplicación anterior. Escriba **y** o en **Sí** para continuar. Express generará el archivo app.js y una estructura de carpetas para la creación de la aplicación.

    ![El resultado del comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Para instalar las dependencias adicionales definidas en el archivo package.json, escriba el siguiente comando:

        PS C:\node\expressapp\WebRole1> npm install

    ![El resultado de la npm el comando de instalación](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Use el comando siguiente para copiar el archivo **bin/www** en **server.js**. Esto es para que el servicio de nube puede encontrar el punto de entrada para esta aplicación.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Una vez finalizado este comando, debe tener un archivo de **server.js** en el directorio WebRole1.

7.  Modificar el **server.js** para quitar una de las '.' caracteres de la línea siguiente.

        var app = require('../app');

    Después de realizar esta modificación, la línea debería aparecer como sigue.

        var app = require('./app');

    Este cambio es necesario puesto que se movió el archivo (anteriormente **Papelera/www**,) para el mismo directorio que el archivo de la aplicación que sea necesario. Después de realizar este cambio, guarde el archivo **server.js** .

8.  Use el comando siguiente para ejecutar la aplicación en el emulador Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Una página web que contiene Bienvenido a express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificación de la vista

Ahora modifique la vista para mostrar el mensaje "Bienvenido a Express en Azure".

1.  Escriba el comando siguiente para abrir el archivo index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![El contenido del archivo index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade es el motor de vistas predeterminado usado por aplicaciones Express. Para obtener más información sobre el motor de vista Jade, consulte [http://jade-lang.com][].

2.  Modificar la última línea de texto agregando **en Azure**.

    ![El archivo index.jade, la última línea lee: p Bienvenido a \#{title} en Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Guarde el archivo y salga de Bloc de notas.

4.  Actualice el explorador y verá los cambios.

    ![Una ventana del explorador, la página de bienvenida a Express en Azure contiene](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Después de probar la aplicación, use el cmdlet **Stop AzureEmulator** para detener el emulador.

##<a name="publishing-the-application-to-azure"></a>Publicar la aplicación en Azure

En la ventana de PowerShell de Azure, use el cmdlet **AzureServiceProject publicar** para implementar la aplicación a un servicio de nube

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Cuando se completa la operación de implementación, se abra el explorador y mostrar la página web.

![Un explorador web que muestra la página de Express. La dirección URL indica que ahora se hospeda en Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com

 
