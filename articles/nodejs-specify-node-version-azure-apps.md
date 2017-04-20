<properties
    pageTitle="Especificar una versión Node.js"
    description="Obtenga información sobre cómo especificar la versión de Node.js utilizada servicios de nube y sitios Web de Azure"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Especificar una versión de Node.js en una aplicación de Azure

Al hospedar una aplicación Node.js, desea asegurarse de que la aplicación utilice una versión específica de Node.js. Existen varias formas de hacerlo para aplicaciones de Azure.

##<a name="default-versions"></a>Versiones de forma predeterminada

Las versiones de Node.js proporcionadas por Azure se actualizan constantemente. A menos que se indique lo contrario, la versión predeterminada que se especifica en la `WEBSITE_NODE_DEFAULT_VERSION` se utilizará la variable de entorno. Para reemplazar este valor predeterminado, siga los pasos descritos en las siguientes secciones de este artículo

> [AZURE.NOTE] Si va a hospedar la aplicación en un servicio de nube de Azure (función web o de trabajo), y es la primera vez que se ha implementado la aplicación, Azure intentará utilizar la misma versión de Node.js tal y como se ha instalado en su entorno de desarrollo si coincida con una de las versiones predeterminadas disponibles en Azure.

##<a name="versioning-with-packagejson"></a>Control de versiones con package.json

Puede especificar la versión de Node.js para usarse agregando lo siguiente al archivo **package.json** :

    "engines":{"node":version}

Donde *versión* es el número de versión específica que desee utilizar. Se pueden que puede especificar condiciones más complejas de versión, como:

    "engines":{"node": "0.6.22 || 0.8.x"}

Puesto que 0.6.22 no es una de las versiones disponibles en el entorno de hospedaje, la última versión de la serie que está disponible será de 0,8 utiliza - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Sitios Web de control de versiones con la configuración de la aplicación
Si va a hospedar la aplicación en un sitio Web, puede establecer la variable de entorno **WEBSITE_NODE_DEFAULT_VERSION** a la versión deseada. 

##<a name="versioning-cloud-services-with-powershell"></a>Servicios de nube de control de versiones con PowerShell

Si va a hospedar la aplicación en un servicio de nube y está implementando la aplicación con PowerShell de Azure, puede reemplazar la versión de Node.js predeterminada mediante el cmdlet de PowerShell **Conjunto AzureServiceProjectRole** . Por ejemplo:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Tenga en cuenta que los parámetros en la instrucción anterior distinguen mayúsculas de minúsculas.  Puede comprobar que la versión correcta de Node.js se ha seleccionado la propiedad **motores** en **package.json de la función**.

También puede usar el **AzureServiceProjectRoleRuntime Get** para recuperar una lista de versiones de Node.js disponibles para las aplicaciones que aloja como un servicio de nube.  Comprobar siempre la versión de su proyecto depende de Node.js está en esta lista.

##<a name="using-a-custom-version-with-azure-websites"></a>Usar una versión personalizada con sitios Web de Azure

Mientras Azure proporciona varias versiones de forma predeterminada de Node.js, desea usar una versión que no se proporciona de forma predeterminada. Si la aplicación se hospeda como un sitio Web de Azure, puede hacerlo mediante el archivo **iisnode.yml** . Los siguientes pasos guían durante el proceso de usar una versión personalizada de Node.Js con un sitio Web de Azure:

1. Crear un nuevo directorio y, a continuación, cree un archivo **server.js** dentro del directorio. El archivo **server.js** debe contener lo siguiente:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Esta opción mostrará la versión de Node.js que se usa cuando navega por el sitio Web.

2. Crear un nuevo sitio Web y anote el nombre del sitio. Por ejemplo, el siguiente usa las [Herramientas de línea de comandos de Azure] para crear un nuevo sitio Web de Azure denominado **MiSitioWeb**y, a continuación, habilitar un repositorio Git para el sitio Web.

        azure site create mywebsite --git

3. Crear un nuevo directorio denominado **bin** como secundario del directorio que contiene el archivo **server.js** .

4. Descargue la versión específica de **node.exe** (la versión de Windows) que desea usar con la aplicación. Por ejemplo, el siguiente usa **doblez** Descargar versión 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Guarde el archivo **node.exe** en la carpeta **bin** creada anteriormente.

5. Crear un archivo de **iisnode.yml** en el mismo directorio que el archivo **server.js** y, a continuación, agregue el contenido siguiente al archivo **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Esta ruta es donde el archivo **node.exe** dentro del proyecto se encuentran una vez que haya publicado su aplicación para el sitio Web de Azure.

6. Publicar la aplicación. Por ejemplo, puesto que he creado un nuevo sitio Web con el parámetro--git anteriormente, los siguientes comandos agregar los archivos de aplicación a Mi repositorio Git local y después insertarlos en el repositorio de sitio Web:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Después de la aplicación se ha publicado, abra el sitio Web en un explorador. Verá un mensaje que indica "Hola a todos de Azure ejecución de la versión de nodo: v0.8.1".

##<a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo especificar la versión de Node.js usado por la aplicación, obtenga información sobre cómo [trabajar con los módulos], [Compile e implemente un sitio Web de Node.js]y [cómo usar las herramientas de línea de comandos de Azure para Mac y Linux].

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[Cómo usar las herramientas de línea de comandos de Azure para Mac y Linux]: xplat-cli-install.md
[Herramientas de línea de comandos de Azure]: xplat-cli-install.md
[trabajar con los módulos]: nodejs-use-node-modules-azure-apps.md
[Compile e implemente un sitio Web de Node.js]: web-sites-nodejs-develop-deploy-mac.md
