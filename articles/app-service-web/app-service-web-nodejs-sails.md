<properties
    pageTitle="Implementar una aplicación web de Sails.js al servicio de la aplicación de Azure"
    description="Obtenga información sobre cómo implementar una aplicación de Node.js servicio de aplicación de Azure. En este tutorial se muestra cómo implementar una aplicación web de Sails.js."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>

# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Implementar una aplicación web de Sails.js al servicio de la aplicación de Azure

En este tutorial se muestra cómo implementar una aplicación de Sails.js al servicio de la aplicación de Azure. En el proceso, puede recopilar conocimiento general sobre cómo configurar su aplicación Node.js para que se ejecute en la aplicación de servicio. 

Debe tener conocimientos de Sails.js. Este tutorial no está pensado para ayudarle con problemas relacionados con la ejecución Sail.js en general.


## <a name="prerequisites"></a>Requisitos previos

- [Node.js](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [GIT](http://www.git-scm.com/downloads)
- [CLI de Azure](../xplat-cli-install.md)
- Una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [registrarse para una prueba gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Para ver la aplicación de servicio de Azure en acción antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicación](http://go.microsoft.com/fwlink/?LinkId=523751). Allí, puede crear inmediatamente una aplicación de corta duración starter en la aplicación de servicio: necesaria ninguna tarjeta de crédito, sin compromisos.

## <a name="step-1-create-a-sailsjs-app-locally"></a>Paso 1: Crear una aplicación de Sails.js localmente

En primer lugar, crear rápidamente una aplicación de Sails.js predeterminada en su entorno de desarrollo siguiendo estos pasos:

1. Abrir terminal de línea de comandos de su elección y `CD` al directorio de trabajo.

2. Crear una aplicación de Sails.js y ejecútelo:

        sails new <appname>
        cd <appname>
        sails lift

    Asegúrese de que se puede navegar a la página de inicio de forma predeterminada en http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Paso 2: Crear el recurso de la aplicación de Azure

A continuación, cree el recurso de servicio de aplicación en Azure. Va a implementar la aplicación Sails.js a él más adelante.

1. Inicie sesión en Azure similares así:
1. En el mismo terminal, cambie al modo de ASM e inicie sesión en Azure:

        azure config mode asm
        azure login

    Siga el símbolo del sistema para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tiene la suscripción de Azure.

2. Asegúrese de que sigue en el directorio raíz de su proyecto Sails.js. Crear el recurso de la aplicación de servicio de aplicación en Azure con un nombre de aplicación única con el siguiente comando. Dirección URL de la aplicación web es http://&lt;nombre de aplicación >. azurewebsites.net.

        azure site create --git <appname>

    Siga el indicador para seleccionar un área de Azure para implementar. Si nunca ha configurado las credenciales de implementación de Git/FTP para su suscripción de Azure, también se pedirá de crearlas.

    Una vez creado el recurso de la aplicación de servicio de aplicaciones:

    - Aplicación de Sails.js está inicializado Git,
    - Su repositorio inicializado Git local está conectado a la nueva aplicación de servicio de aplicación como un Git remoto, como su propio nombre "azure", y
    - Y iisnode.yml archivo se crea en el directorio raíz. Puede utilizar este archivo para configurar [iisnode](https://github.com/tjanczuk/iisnode), que usa el servicio de aplicación para ejecutar aplicaciones de Node.js.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Paso 3: Configurar e implementar la aplicación Sails.js

 Trabajar con una aplicación de Sails.js en la aplicación de servicio consta de tres pasos principales:

 - Configurar la aplicación para se ejecute en la aplicación de servicio
 - Implementar aplicación de servicio
 - Registros de stderr y stdout lecturas para solucionar problemas de implementación

Siga estos pasos:

1. Abrir el nuevo archivo de iisnode.yml en el directorio raíz y agregue las dos líneas siguientes:

        loggingEnabled: true
        logDirectory: iisnode

    Registro ahora está habilitado para iisnode. Para obtener más información sobre cómo funciona esto, vea  [obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Abra config/env/production.js para configurar su entorno de producción y establecer `port` y `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Puede encontrar documentación para estas opciones de configuración en la  [Documentación de Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    A continuación, debe asegurarse de que [normalmente](https://www.npmjs.com/package/grunt) es compatible con las unidades de red de Azure. Simplifique versiones menor que 1.0.0 utiliza un paquete de obsoleta [glob](https://www.npmjs.com/package/glob) (menos de 5.0.14), que no es compatible con las unidades de red. 

3. Abra package.json y cambie el `grunt` versión `1.0.0` y quitar todo `grunt-*` paquetes. Su `dependencies` propiedad debe tener un aspecto similar a este:

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. En package.json, agregue la siguiente `engines` propiedad para establecer la versión de Node.js que queremos.

        "engines": {
            "node": "6.6.0"
        },

6. Guardar los cambios y probar sus cambios para asegurarse de que la aplicación se ejecuta localmente. Para ello, elimine la `node_modules` y, a continuación, ejecute el archivo:

        npm install
        sails lift

4. Ahora puede utilizar git para implementar la aplicación de Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Por último, simplemente inicie la aplicación de Azure directo en el explorador:

        azure site browse

    Ahora debe ver la misma página de inicio de Sails.js.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Solucionar problemas de la implementación

Si se produce un error en la aplicación Sails.js por algún motivo en la aplicación de servicio, busque los registros stderr para ayudar a solucionar el problema.
Para obtener más información, vea [obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Si se inicia correctamente, el registro de stdout debe mostrar el mensaje habituales:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Puede controlar el detalle de los registros de stdout en el archivo [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Conectarse a una base de datos de Azure

Para conectarse a una base de datos de Azure, crear la base de datos de su elección en Azure, como base de datos de SQL Azure, MySQL, MongoDB, caché de Azure (Redis), etc. y use el correspondiente [adaptador de almacén de datos](https://github.com/balderdashy/sails#compatibility) para conectarse a él. Los pasos de esta sección muestran cómo conectarse a una base de datos MySQL en Azure.

1. Siga el tutorial [aquí](../store-php-create-mysql-database.md) para crear una base de datos MySQL en Azure.

2. Desde la línea de comandos terminal, instale al adaptador MySQL:

        npm install sails-mysql --save

3. Abra config/connections.js y agregue el siguiente objeto de conexión a la lista: 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Para cada variable de entorno (`process.env.*`), es necesario configurar en la aplicación de servicio. Para ello, ejecute los comandos siguientes desde el dispositivo terminal. Toda la información de conexión que necesita está en el portal de Azure (vea [conectarse a la base de datos MySQL](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    Colocación de la configuración en configuración de la aplicación de Azure mantiene datos confidenciales fuera de su control de origen (Git). A continuación, va a configurar su entorno de desarrollo para usar la misma información de conexión.

4. Abra config/local.js y agregue el siguiente objeto de conexiones:

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Esta configuración reemplaza la configuración en el archivo config/connections.js para el entorno local. Este archivo es excluido por .gitignore de forma predeterminada en su proyecto, por lo que no se almacenarán en Git. Ahora, es posible conectarse a la base de datos MySQL desde la aplicación web de Azure y del entorno de desarrollo local.

4. Abra config/env/production.js para configurar su entorno de producción y agregue las siguientes `models` objeto:

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Abra config/env/development.js para configurar el entorno de desarrollo y agregue las siguientes `models` objeto:

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'`le permite usar características de migración de base de datos para crear y actualizar las tablas de base de datos en su MySQL fácilmente. Sin embargo, `migrate: 'safe'` se utiliza para su entorno de Azure (producción) porque Sails.js no permite usar `migrate: 'alter'` en un entorno de producción (consulte la  [Documentación de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. Desde el terminal [Generar](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) una Sails.js [Guía API](http://sailsjs.org/documentation/concepts/blueprints) como normalmente haría, a continuación, ejecutar `sails lift` para crear la base de datos con la migración de la base de datos de Sails.js. Por ejemplo:

         sails generate api mywidget
         sails lift

    La `mywidget` modelo generado por este comando está vacío, pero se puede utilizar para mostrar que tenemos conectividad de base de datos.
    Cuando se ejecuta `sails lift`, crea las tablas que faltan para los modelos de la aplicación usa.

6. Obtener acceso a la guía API que acaba de crear en el explorador. Por ejemplo:

        http://localhost:1337/mywidget/create
    
    La API debe devolver la entrada creada con usted en la ventana del explorador, lo que significa que la base de datos se creó correctamente.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. Ahora, aplicar los cambios a Azure y busque la aplicación para asegurarse de que sigue funcionando.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Obtener acceso a la Guía de la API de la aplicación web de Azure. Por ejemplo:

        http://<appname>.azurewebsites.net/mywidget/create

    Si la API devuelve otra entrada nuevo, la aplicación web de Azure está hablando con la base de datos MySQL.

## <a name="more-resources"></a>Más recursos

- [Introducción a aplicaciones web de Node.js en la aplicación de servicio de Azure](app-service-web-nodejs-get-started.md)
- [Utilizar módulos de Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)
