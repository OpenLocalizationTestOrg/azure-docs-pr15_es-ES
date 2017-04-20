<properties
    pageTitle="Aplicación Node.js API de servicio de aplicaciones de Azure | Microsoft Azure"
    description="Aprenda a crear API de REST Node.js e implementar una aplicación API de servicio de aplicaciones de Azure."
    services="app-service\api"
    documentationCenter="node"
    authors="bradygaster"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="rachelap"/>

# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Crear una API de REST de Node.js e implementar en una aplicación de API de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial muestra cómo crear un simple [Node.js](http://nodejs.org) API e implementar en una [aplicación de la API](app-service-api-apps-why-best-platform.md) de [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md) con [Git](http://git-scm.com). Puede usar cualquier sistema operativo que se pueden ejecutar Node.js y deberá realizar todas las tareas con herramientas de línea de comandos como cmd.exe o bash.

## <a name="prerequisites"></a>Requisitos previos

1. Cuenta de Microsoft Azure ([Abrir una cuenta gratuita aquí](https://azure.microsoft.com/pricing/free-trial/))
1. [Node.js](http://nodejs.org) instalado (en este ejemplo se supone que tiene Node.js versión 4.2.2)
2. [Git](https://git-scm.com/) instalado
1. Cuenta de [GitHub](https://github.com/)

Mientras el servicio de aplicación admite muchas formas para implementar el código de una aplicación de API, este tutorial muestra el método Git y supone que tiene un conocimiento básico de cómo trabajar con Git. Para obtener información acerca de otros métodos de implementación, vea [implementar su aplicación de servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Obtener el código de ejemplo

1. Abra una interfaz de línea de comandos que se pueden ejecutar comandos Node.js y Git.

1. Navegue hasta una carpeta que puede usar para un repositorio Git local y clonar el [repositorio de GitHub que contiene el código de ejemplo](https://github.com/Azure-Samples/app-service-api-node-contact-list).

        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

    La API de ejemplo proporciona dos extremos: una solicitud Get a `/contacts` devuelve una lista de nombres y direcciones de correo electrónico en formato JSON, mientras `/contacts/{id}` devuelve solo el contacto seleccionado.

## <a name="scaffold-auto-generate-nodejs-code-based-on-swagger-metadata"></a>Scaffold (generar automáticamente) Node.js código basándose en los metadatos de Swagger

[Swagger](http://swagger.io/) es un formato de archivo de metadatos que describa la API de REST. Servicio de aplicaciones de Azure tiene [compatibilidad integrada para metadatos Swagger](app-service-api-metadata.md). Esta sección del tutorial modelos un flujo de trabajo de desarrollo API en el que crear metadatos Swagger primero y usarlo para scaffold (generar automáticamente) el código de servidor para la API. 

>[AZURE.NOTE] Puede omitir esta sección si no desea obtener información sobre cómo scaffold Node.js código desde un archivo de metadatos de Swagger. Si desea simplemente implementar código de ejemplo en una nueva aplicación API, vaya directamente a la sección [crear una aplicación de la API en Azure](#createapiapp) .

### <a name="install-and-execute-swaggerize"></a>Instalar y ejecutar Swaggerize

1. Ejecute los comandos siguientes para instalar los módulos NPM **yo** y **swaggerize generador** global.

        npm install -g yo
        npm install -g generator-swaggerize

    Swaggerize es una herramienta que genera el código del servidor para una API descrita por un archivo de metadatos de Swagger. El archivo Swagger que usará con el nombre *api.json* y se encuentra en la carpeta de *Inicio* del repositorio que clona.

2. Vaya a la carpeta *Inicio* y, a continuación, ejecute el `yo swaggerize` comando. Swaggerize le pregunte una serie de preguntas.  Por **lo que llame a este proyecto**, escriba "ContactList" ruta de acceso **swagger documento**, escriba "api.json" y **Express, feliz, o Restify**, escriba "rápida".

        yo swaggerize

    ![Swaggerize la línea de comandos](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
    **Nota**: si se produce un error en este paso, el siguiente paso explica cómo repararlo.

    Swaggerize crea una carpeta de la aplicación, scaffolds controladores y los archivos de configuración y genera un archivo de **package.json** . El motor de vista express se usa para generar la página de Ayuda de Swagger.  

3. Si el `swaggerize` comando produce un error de "secuencia de escape no válido" o "token inesperado", corrija la causa del error al editar el archivo generado *package.json* . En la `regenerate` línea en `scripts`, cambiar la barra diagonal inversa que precede a *api.json* a una barra diagonal, de modo que la línea tiene un aspecto similar al ejemplo siguiente:

        "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Vaya a la carpeta que contiene el código scaffolded (en este caso, la subcarpeta */start/ContactList* ).

1. Ejecutar `npm install`.
    
        npm install
        
2. Instale el módulo NPM **jsonpath** . 

        npm install --save jsonpath
        
    ![Instalación de Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Instale el módulo NPM **swaggerize de interfaz de usuario** . 

        npm install --save swaggerize-ui
        
    ![Swaggerize instalación de interfaz de usuario](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Personalizar el código scaffolded

1. Copie la carpeta de **biblioteca** de la carpeta de **Inicio** en la carpeta de **ContactList** creada por la scaffolder. 

1. Reemplace el código en el archivo **handlers/contacts.js** con el código siguiente. 

    Este código utiliza los datos JSON almacenados en el archivo **lib/contacts.json** servida por **lib/contactRepository.js**. El nuevo código de contacts.js responde a las solicitudes HTTP para obtener todos los contactos y volver a ellas como una carga JSON. 

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Reemplace el código en el archivo **handlers/contacts/{id}.js** con el código de fofllowing. 

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Reemplace el código de **server.js** con el código siguiente. 

    Los cambios realizados en el archivo server.js están resaltados con comentarios para que pueda ver los cambios realizados. 

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Pruebe con la API ejecuta localmente

1. Activar el servidor con el archivo ejecutable de línea de comandos de Node.js. 

        node server.js

1. Cuando examine **http://localhost:8000 o contactos**, verá el resultado JSON de la lista de contactos (o se le pide que descargue, dependiendo de su explorador). 

    ![Llamada de la Api de contactos](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Cuando busque **contactos/http://localhost:8000/2**, verá el contacto representado por ese valor de id.

    ![Llamada a la Api de contacto específico](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Los datos JSON Swagger sirve a través del extremo **/ swagger** :

    ![Swagger Json de contactos](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. La interfaz de usuario Swagger sirve a través del extremo de **/docs** . En la interfaz de usuario Swagger, puede usar las características de cliente enriquecidas HTML para probar la API.

    ![Interfaz de usuario de swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a>Crear un nuevo App API

En esta sección se utiliza el portal de Azure para crear un nuevo App API en Azure. Esta aplicación API representa los recursos de cálculo que le proporcionará Azure para ejecutar el código. En secciones posteriores deberá implementar su código en la nueva API de aplicación.

1. Desplácese hasta el [Portal de Azure](https://portal.azure.com/). 

1. Haga clic en **Nuevo > Web + Mobile > aplicación API**. 

    ![Nueva aplicación API de portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Escriba un **nombre de aplicación** que sea único en el dominio *azurewebsites.net* , como NodejsAPIApp más de un número para hacerlo único. 

    Por ejemplo, si el nombre es `NodejsAPIApp`, la dirección URL será `nodejsapiapp.azurewebsites.net`.

    Si escribe un nombre de otra persona ya ha utilizado, verá un signo de exclamación rojo a la derecha.

6. En la lista desplegable **Grupo de recursos** , haga clic en **nuevo**y, a continuación, en el **nombre del nuevo grupo de recursos** escriba "NodejsAPIAppGroup" u otro nombre si lo prefiere. 

    Un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) es una colección de Azure recursos como API de aplicaciones, bases de datos y máquinas virtuales. En este tutorial, es mejor crear un nuevo grupo de recursos, ya hace que sea fácil eliminar en un paso de todos los recursos de Azure que se crean para el tutorial.

4. Haga clic en **Ubicación o plan de servicio de aplicaciones**y, a continuación, haga clic en **Crear nuevo**.

    ![Crear plan de servicios de aplicación](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

    En los pasos siguientes, se crea un plan de servicio de aplicación para el nuevo grupo de recursos. Un plan de servicio de aplicación especifica los recursos de cálculo que se ejecuta la aplicación de la API en. Por ejemplo, si elige el nivel gratuito, la aplicación de la API se ejecuta en VM compartidas, mientras que para algunos niveles de pagadas se ejecuta en máquinas virtuales dedicadas. Para obtener información acerca de los planes de servicio de aplicaciones, vea [información general de los planes de aplicación de servicio](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En el módulo de **Plan de servicio de aplicación** , escriba "NodejsAPIAppPlan" u otro nombre si lo prefiere.

5. En la lista desplegable de **ubicación** , elija la ubicación más cercana a usted.

    Esta opción especifica qué centro de datos de Azure se ejecuta la aplicación en. En este tutorial, puede seleccionar cualquier región y que no será una gran diferencia. Pero para una aplicación de producción, que desea que el servidor sea lo más cerca posible a los clientes que tienen acceso a él para minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Haga clic en **nivel de precio > Ver todo > F1 gratuita**.

    En este tutorial, el nivel de precio gratuito le proporcionará un rendimiento suficiente.

    ![Seleccione liberar precios nivel](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. En el módulo de **Plan de servicio de aplicación** , haga clic en **Aceptar**.

7. En el módulo de **Aplicación de la API** , haga clic en **crear**.

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Configurar la nueva aplicación de la API de implementación de Git

Deberá implementar el código en la aplicación de la API presionando confirmaciones a un repositorio Git en servicio de la aplicación de Azure. En esta sección del tutorial, cree las credenciales y repositorio Git en Azure que usará para su implementación.  

1. Después de crear la aplicación de la API, haga clic en **Servicios de aplicación > {su aplicación API}** desde la página principal del portal. 

    El portal muestra los módulos de **Aplicación de la API** y **configuración** .

    ![Aplicación del portal de API y el módulo de configuración](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. En el módulo de **configuración** , desplácese hacia abajo hasta la sección de **publicación** y, a continuación, haga clic en **credenciales de implementación**.
 
3. En el módulo de **establecer las credenciales de la implementación** , escriba un nombre de usuario y contraseña y, a continuación, haga clic en **Guardar**.

    Deberá usar estas credenciales para publicar el código Node.js en la aplicación de la API. 

    ![Credenciales de implementación](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. En el módulo de **configuración** , haga clic en **origen de implementación > Elegir origen > repositorio Git Local**, a continuación, haga clic en **Aceptar**.

    ![Crear Git Repo](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Una vez se ha creado su repositorio Git los cambios del módulo para mostrar las implementaciones activas. Dado que el repositorio es nuevo, no tiene implementaciones activas en la lista. 

    ![Sin implementaciones activas](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Copie la dirección URL de repositorio de Git. Para ello, navegar hasta el módulo de su App API nueva y observe la sección **Essentials** del módulo. Observe que la **dirección URL de clonar Git** en la sección **Essentials** . Cuando desplace el puntero sobre esta dirección URL, verá un icono de la derecha en la que va a copiar la dirección URL al Portapapeles. Haga clic en este icono para copiar la dirección URL.

    ![Obtener la dirección Url de Git desde el Portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Nota**: debe duplicado Git dirección URL de la siguiente sección así que asegúrese de guardarla en algún lugar por el momento.

Ahora que ya tiene un App API con un repositorio Git una copia de seguridad, puede insertar código en el repositorio para implementar el código de la aplicación de la API. 

## <a name="deploy-your-api-code-to-azure"></a>Implementar el código de la API en Azure

En esta sección se crea un repositorio Git local que contiene el código del servidor de la API y, a continuación, insertar el código de repositorio al repositorio en Azure que creó anteriormente.

1. Copiar la `ContactList` carpeta a una ubicación que puede usar para un nuevo repositorio Git local. Si ya realizó la primera parte del tutorial, copie `ContactList` desde el `start` carpeta; en caso contrario, copiar `ContactList` desde el `end` carpeta.

1. En la herramienta de línea de comandos, vaya a la nueva carpeta y luego ejecute el comando siguiente para crear un nuevo repositorio Git local. 

        git init

     ![Nuevo Git Repo Local](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Ejecute el comando siguiente para agregar un Git remoto para repositorio de la API la aplicación. 

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Nota**: reemplace la cadena "YOUR_GIT_CLONE_URL_HERE" por su propia URL clonar Git que copió anteriormente. 

1. Ejecute los comandos siguientes para crear una confirmación de que contiene todo el código. 

        git add .
        git commit -m "initial revision"

    ![Confirmación de GIT salida](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Ejecute el comando para insertar el código en Azure. Cuando se le solicite una contraseña, escriba el elemento que creó anteriormente en el portal de Azure.

        git push azure master

    Esto activa una implementación de la aplicación de la API.  

1. En el explorador, vaya al módulo de **implementaciones** para la aplicación de la API y verá que se produce la implementación. 

    ![¿Implementación sucediendo?](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Simultáneamente, la interfaz de línea de comandos refleja el estado de la implementación mientras está ocurriendo. 

    ![Nodo Js implementación ocurra](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

    Una vez completada la implementación, el módulo de **implementaciones** refleja la implementación correcta de los cambios de código para su App API. 

## <a name="test-with-the-api-running-in-azure"></a>Pruebe con la API que se ejecuta en Azure
 
3. Copie la **dirección URL** en la sección de **aspectos básicos** de su módulo API App. 

    ![Implementación completada](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Mediante un cliente de la API de REST como Postman o Fiddler (o el explorador web), proporcione la dirección URL de sus contactos llamada API, que es el `/contacts` punto final de la aplicación de la API. La dirección URL será`https://{your API app name}.azurewebsites.net/contacts`

    Si tiene problemas con una solicitud GET a este extremo, obtendrá el resultado JSON de la aplicación de la API.

    ![Postman alcance Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. En un explorador, vaya a la `/docs` extremo para probar la interfaz de usuario Swagger mientras se ejecuta en Azure.

Ahora que ya tiene entrega continua por cable hacia arriba, puede realizar cambios de código e implementar en Azure presionando confirmaciones al repositorio de Azure Git.

## <a name="next-steps"></a>Pasos siguientes

En este momento ha creado un App API correctamente e implementado código Node.js API. El siguiente tutorial se muestra cómo [utilizar las aplicaciones de la API de JavaScript los clientes mediante CORS](app-service-api-cors-consume-javascript.md).
